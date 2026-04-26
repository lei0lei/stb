# stretchy_buffer.h 代码解说

源文件：`deprecated/stretchy_buffer.h`

本文按源文件中的自然代码段切分，每段先给出原始代码，再用中文解释这段代码在做什么以及它为什么存在。

## 第 1 段（第 1-80 行）

```c
// stretchy_buffer.h - v1.04 - public domain - nothings.org/stb
// a vector<>-like dynamic array for C
//
// version history:
//      1.04 -  fix warning
//      1.03 -  compile as C++ maybe
//      1.02 -  tweaks to syntax for no good reason
//      1.01 -  added a "common uses" documentation section
//      1.0  -  fixed bug in the version I posted prematurely
//      0.9  -  rewrite to try to avoid strict-aliasing optimization
//              issues, but won't compile as C++
//
// Will probably not work correctly with strict-aliasing optimizations.
//
// The idea:
//
//    This implements an approximation to C++ vector<> for C, in that it
//    provides a generic definition for dynamic arrays which you can
//    still access in a typesafe way using arr[i] or *(arr+i). However,
//    it is simply a convenience wrapper around the common idiom of
//    of keeping a set of variables (in a struct or globals) which store
//        - pointer to array
//        - the length of the "in-use" part of the array
//        - the current size of the allocated array
//
//    I find it to be the single most useful non-built-in-structure when
//    programming in C (hash tables a close second), but to be clear
//    it lacks many of the capabilities of C++ vector<>: there is no
//    range checking, the object address isn't stable (see next section
//    for details), the set of methods available is small (although
//    the file stb.h has another implementation of stretchy buffers
//    called 'stb_arr' which provides more methods, e.g. for insertion
//    and deletion).
//
// How to use:
//
//    Unlike other stb header file libraries, there is no need to
//    define an _IMPLEMENTATION symbol. Every #include creates as
//    much implementation is needed.
//
//    stretchy_buffer.h does not define any types, so you do not
//    need to #include it to before defining data types that are
//    stretchy buffers, only in files that *manipulate* stretchy
//    buffers.
//
//    If you want a stretchy buffer aka dynamic array containing
//    objects of TYPE, declare such an array as:
//
//       TYPE *myarray = NULL;
//
//    (There is no typesafe way to distinguish between stretchy
//    buffers and regular arrays/pointers; this is necessary to
//    make ordinary array indexing work on these objects.)
//
//    Unlike C++ vector<>, the stretchy_buffer has the same
//    semantics as an object that you manually malloc and realloc.
//    The pointer may relocate every time you add a new object
//    to it, so you:
//
//         1. can't take long-term pointers to elements of the array
//         2. have to return the pointer from functions which might expand it
//            (either as a return value or by storing it to a ptr-to-ptr)
//
//    Now you can do the following things with this array:
//
//         sb_free(TYPE *a)           free the array
//         sb_count(TYPE *a)          the number of elements in the array
//         sb_push(TYPE *a, TYPE v)   adds v on the end of the array, a la push_back
//         sb_add(TYPE *a, int n)     adds n uninitialized elements at end of array & returns pointer to first added
//         sb_last(TYPE *a)           returns an lvalue of the last item in the array
//         a[n]                       access the nth (counting from 0) element of the array
//
//     #define STRETCHY_BUFFER_NO_SHORT_NAMES to only export
//     names of the form 'stb_sb_' if you have a name that would
//     otherwise collide.
//
//     Note that these are all macros and many of them evaluate
//     their arguments more than once, so the arguments should
//     be side-effect-free.
//
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 2 段（第 81-160 行）

```c
//     Note that 'TYPE *a' in sb_push and sb_add must be lvalues
//     so that the library can overwrite the existing pointer if
//     the object has to be reallocated.
//
//     In an out-of-memory condition, the code will try to
//     set up a null-pointer or otherwise-invalid-pointer
//     exception to happen later. It's possible optimizing
//     compilers could detect this write-to-null statically
//     and optimize away some of the code, but it should only
//     be along the failure path. Nevertheless, for more security
//     in the face of such compilers, #define STRETCHY_BUFFER_OUT_OF_MEMORY
//     to a statement such as assert(0) or exit(1) or something
//     to force a failure when out-of-memory occurs.
//
// Common use:
//
//    The main application for this is when building a list of
//    things with an unknown quantity, either due to loading from
//    a file or through a process which produces an unpredictable
//    number.
//
//    My most common idiom is something like:
//
//       SomeStruct *arr = NULL;
//       while (something)
//       {
//          SomeStruct new_one;
//          new_one.whatever = whatever;
//          new_one.whatup   = whatup;
//          new_one.foobar   = barfoo;
//          sb_push(arr, new_one);
//       }
//
//    and various closely-related factorings of that. For example,
//    you might have several functions to create/init new SomeStructs,
//    and if you use the above idiom, you might prefer to make them
//    return structs rather than take non-const-pointers-to-structs,
//    so you can do things like:
//
//       SomeStruct *arr = NULL;
//       while (something)
//       {
//          if (case_A) {
//             sb_push(arr, some_func1());
//          } else if (case_B) {
//             sb_push(arr, some_func2());
//          } else {
//             sb_push(arr, some_func3());
//          }
//       }
//
//    Note that the above relies on the fact that sb_push doesn't
//    evaluate its second argument more than once. The macros do
//    evaluate the *array* argument multiple times, and numeric
//    arguments may be evaluated multiple times, but you can rely
//    on the second argument of sb_push being evaluated only once.
//
//    Of course, you don't have to store bare objects in the array;
//    if you need the objects to have stable pointers, store an array
//    of pointers instead:
//
//       SomeStruct **arr = NULL;
//       while (something)
//       {
//          SomeStruct *new_one = malloc(sizeof(*new_one));
//          new_one->whatever = whatever;
//          new_one->whatup   = whatup;
//          new_one->foobar   = barfoo;
//          sb_push(arr, new_one);
//       }
//
// How it works:
//
//    A long-standing tradition in things like malloc implementations
//    is to store extra data before the beginning of the block returned
//    to the user. The stretchy buffer implementation here uses the
//    same trick; the current-count and current-allocation-size are
//    stored before the beginning of the array returned to the user.
//    (This means you can't directly free() the pointer, because the
//    allocated pointer is different from the type-safe pointer provided
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 3 段（第 161-174 行）

```c
//    to the user.)
//
//    The details are trivial and implementation is straightforward;
//    the main trick is in realizing in the first place that it's
//    possible to do this in a generic, type-safe way in C.
//
// Contributors:
//
// Timothy Wright (github:ZenToad)
//
// LICENSE
//
//   See end of file for license information.
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 4 段（第 175-177 行）

```c
#ifndef STB_STRETCHY_BUFFER_H_INCLUDED
#define STB_STRETCHY_BUFFER_H_INCLUDED
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 5 段（第 178-185 行）

```c
#ifndef NO_STRETCHY_BUFFER_SHORT_NAMES
#define sb_free   stb_sb_free
#define sb_push   stb_sb_push
#define sb_count  stb_sb_count
#define sb_add    stb_sb_add
#define sb_last   stb_sb_last
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 6 段（第 186-191 行）

```c
#define stb_sb_free(a)         ((a) ? free(stb__sbraw(a)),0 : 0)
#define stb_sb_push(a,v)       (stb__sbmaybegrow(a,1), (a)[stb__sbn(a)++] = (v))
#define stb_sb_count(a)        ((a) ? stb__sbn(a) : 0)
#define stb_sb_add(a,n)        (stb__sbmaybegrow(a,n), stb__sbn(a)+=(n), &(a)[stb__sbn(a)-(n)])
#define stb_sb_last(a)         ((a)[stb__sbn(a)-1])
```

解说：这一段定义宏（如 `stb_sb_free, stb_sb_push, stb_sb_count, stb_sb_add, stb_sb_last`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 7 段（第 192-195 行）

```c
#define stb__sbraw(a) ((int *) (void *) (a) - 2)
#define stb__sbm(a)   stb__sbraw(a)[0]
#define stb__sbn(a)   stb__sbraw(a)[1]
```

解说：这一段定义宏（如 `stb__sbraw, stb__sbm, stb__sbn`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 8 段（第 196-199 行）

```c
#define stb__sbneedgrow(a,n)  ((a)==0 || stb__sbn(a)+(n) >= stb__sbm(a))
#define stb__sbmaybegrow(a,n) (stb__sbneedgrow(a,(n)) ? stb__sbgrow(a,n) : 0)
#define stb__sbgrow(a,n)      (*((void **)&(a)) = stb__sbgrowf((a), (n), sizeof(*(a))))
```

解说：这一段定义宏（如 `stb__sbneedgrow, stb__sbmaybegrow, stb__sbgrow`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 9 段（第 200-201 行）

```c
#include <stdlib.h>
```

解说：这一段引入编译所需的头文件或本项目内的依赖，使后面的类型、宏和函数声明能够被编译器识别。

## 第 10 段（第 202-221 行）

```c
static void * stb__sbgrowf(void *arr, int increment, int itemsize)
{
   int dbl_cur = arr ? 2*stb__sbm(arr) : 0;
   int min_needed = stb_sb_count(arr) + increment;
   int m = dbl_cur > min_needed ? dbl_cur : min_needed;
   int *p = (int *) realloc(arr ? stb__sbraw(arr) : 0, itemsize * m + sizeof(int)*2);
   if (p) {
      if (!arr)
         p[1] = 0;
      p[0] = m;
      return p+2;
   } else {
      #ifdef STRETCHY_BUFFER_OUT_OF_MEMORY
      STRETCHY_BUFFER_OUT_OF_MEMORY ;
      #endif
      return (void *) (2*sizeof(int)); // try to force a NULL pointer exception later
   }
}
#endif // STB_STRETCHY_BUFFER_H_INCLUDED
```

解说：这一段实现或声明库接口 `stb__sbgrowf`，把“sbgrowf”相关能力封装成可被外部或内部复用的函数。

## 第 11 段（第 222-263 行）

```c

/*
------------------------------------------------------------------------------
This software is available under 2 licenses -- choose whichever you prefer.
------------------------------------------------------------------------------
ALTERNATIVE A - MIT License
Copyright (c) 2017 Sean Barrett
Permission is hereby granted, free of charge, to any person obtaining a copy of
this software and associated documentation files (the "Software"), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies
of the Software, and to permit persons to whom the Software is furnished to do
so, subject to the following conditions:
The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.
THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
------------------------------------------------------------------------------
ALTERNATIVE B - Public Domain (www.unlicense.org)
This is free and unencumbered software released into the public domain.
Anyone is free to copy, modify, publish, use, compile, sell, or distribute this
software, either in source code form or as a compiled binary, for any purpose,
commercial or non-commercial, and by any means.
In jurisdictions that recognize copyright laws, the author or authors of this
software dedicate any and all copyright interest in the software to the public
domain. We make this dedication for the benefit of the public at large and to
the detriment of our heirs and successors. We intend this dedication to be an
overt act of relinquishment in perpetuity of all present and future rights to
this software under copyright law.
THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN
ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION
WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
------------------------------------------------------------------------------
*/
```

解说：这一段实现或声明函数 `files`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。
