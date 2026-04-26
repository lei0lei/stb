# stb_divide.h 代码解说

源文件：`stb_divide.h`

本文按源文件中的自然代码段切分，每段先给出原始代码，再用中文解释这段代码在做什么以及它为什么存在。

## 第 1 段（第 1-80 行）

```c
// stb_divide.h - v0.94 - public domain - Sean Barrett, Feb 2010
// Three kinds of divide/modulus of signed integers.
//
// HISTORY
//
//   v0.94              Fix integer overflow issues
//   v0.93  2020-02-02  Write useful exit() value from main()
//   v0.92  2019-02-25  Fix warning
//   v0.91  2010-02-27  Fix euclidean division by INT_MIN for non-truncating C
//                      Check result with 64-bit math to catch such cases
//   v0.90  2010-02-24  First public release
//
// USAGE
//
// In *ONE* source file, put:
//
//    #define STB_DIVIDE_IMPLEMENTATION
//    // #define C_INTEGER_DIVISION_TRUNCATES  // see Note 1
//    // #define C_INTEGER_DIVISION_FLOORS     // see Note 2
//    #include "stb_divide.h"
//
// Other source files should just include stb_divide.h
//
// Note 1: On platforms/compilers that you know signed C division
// truncates, you can #define C_INTEGER_DIVISION_TRUNCATES.
//
// Note 2: On platforms/compilers that you know signed C division
// floors (rounds to negative infinity), you can #define
// C_INTEGER_DIVISION_FLOORS.
//
// You can #define STB_DIVIDE_TEST in which case the implementation
// will generate a main() and compiling the result will create a
// program that tests the implementation. Run it with no arguments
// and any output indicates an error; run it with any argument and
// it will also print the test results. Define STB_DIVIDE_TEST_64
// to a 64-bit integer type to avoid overflows in the result-checking
// which give false negatives.
//
// ABOUT
//
// This file provides three different consistent divide/mod pairs
// implemented on top of arbitrary C/C++ division, including correct
// handling of overflow of intermediate calculations:
//
//     trunc:   a/b truncates to 0,           a%b has same sign as a
//     floor:   a/b truncates to -inf,        a%b has same sign as b
//     eucl:    a/b truncates to sign(b)*inf, a%b is non-negative
//
// Not necessarily optimal; I tried to keep it generally efficient,
// but there may be better ways.
//
// Briefly, for those who are not familiar with the problem, we note
// the reason these divides exist and are interesting:
//
//     'trunc' is easy to implement in hardware (strip the signs,
//          compute, reapply the signs), thus is commonly defined
//          by many languages (including C99)
//
//     'floor' is simple to define and better behaved than trunc;
//          for example it divides integers into fixed-size buckets
//          without an extra-wide bucket at 0, and for a fixed
//          divisor N there are only |N| possible moduli.
//
//     'eucl' guarantees fixed-sized buckets *and* a non-negative
//          modulus and defines division to be whatever is needed
//          to achieve that result.
//
// See "The Euclidean definition of the functions div and mod"
// by Raymond Boute (1992), or "Division and Modulus for Computer
// Scientists" by Daan Leijen (2001)
//
// We assume of the built-in C division:
//     (a) modulus is the remainder for the corresponding division
//     (b) a/b truncates if a and b are the same sign
//
// Property (a) requires (a/b)*b + (a%b)==a, and is required by C.
// Property (b) seems to be true of all hardware but is *not* satisfied
// by the euclidean division operator we define, so it's possibly not
// always true. If any such platform turns up, we can add more cases.
// (Possibly only stb_div_trunc currently relies on property (b).)
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 2 段（第 81-85 行）

```c
//
// LICENSE
//
//   See end of file for license information.
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 3 段（第 87-89 行）

```c
#ifndef INCLUDE_STB_DIVIDE_H
#define INCLUDE_STB_DIVIDE_H
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 4 段（第 90-104 行）

```c
#ifdef __cplusplus
extern "C" {
#endif

extern int stb_div_trunc(int value_to_be_divided, int value_to_divide_by);
extern int stb_div_floor(int value_to_be_divided, int value_to_divide_by);
extern int stb_div_eucl (int value_to_be_divided, int value_to_divide_by);
extern int stb_mod_trunc(int value_to_be_divided, int value_to_divide_by);
extern int stb_mod_floor(int value_to_be_divided, int value_to_divide_by);
extern int stb_mod_eucl (int value_to_be_divided, int value_to_divide_by);

#ifdef __cplusplus
}
#endif
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 5 段（第 105-106 行）

```c
#ifdef STB_DIVIDE_IMPLEMENTATION
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 6 段（第 107-112 行）

```c
#if defined(__STDC_VERSION) && __STDC_VERSION__ >= 19901
   #ifndef C_INTEGER_DIVISION_TRUNCATES
      #define C_INTEGER_DIVISION_TRUNCATES
   #endif
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 7 段（第 113-116 行）

```c
#ifndef INT_MIN
#include <limits.h> // if you have no limits.h, #define INT_MIN yourself
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 8 段（第 117-136 行）

```c
// the following macros are designed to allow testing
// other platforms by simulating them
#ifndef STB_DIVIDE_TEST_FLOOR
   #define stb__div(a,b)  ((a)/(b))
   #define stb__mod(a,b)  ((a)%(b))
#else
   // implement floor-style divide on trunc platform
   #ifndef C_INTEGER_DIVISION_TRUNCATES
   #error "floor test requires truncating division"
   #endif
   #undef C_INTEGER_DIVISION_TRUNCATES
   int stb__div(int v1, int v2)
   {
      int q = v1/v2, r = v1%v2;
      if ((r > 0 && v2 < 0) || (r < 0 && v2 > 0))
         return q-1;
      else
         return q;
   }
```

解说：这一段实现或声明库接口 `stb__div`，把“div”相关能力封装成可被外部或内部复用的函数。

## 第 9 段（第 137-146 行）

```c
   int stb__mod(int v1, int v2)
   {
      int r = v1%v2;
      if ((r > 0 && v2 < 0) || (r < 0 && v2 > 0))
         return r+v2;
      else
         return r;
   }
#endif
```

解说：这一段实现或声明库接口 `stb__mod`，把“mod”相关能力封装成可被外部或内部复用的函数。

## 第 10 段（第 147-163 行）

```c
int stb_div_trunc(int v1, int v2)
{
   #ifdef C_INTEGER_DIVISION_TRUNCATES
   return v1/v2;
   #else
   if (v1 >= 0 && v2 <= 0)
      return -stb__div(-v1,v2);  // both negative to avoid overflow
   if (v1 <= 0 && v2 >= 0)
      if (v1 != INT_MIN)
         return -stb__div(v1,-v2);    // both negative to avoid overflow
      else
         return -stb__div(v1+v2,-v2)-1; // push v1 away from wrap point
   else
      return v1/v2;            // same sign, so expect truncation
   #endif
}
```

解说：这一段实现或声明库接口 `stb_div_trunc`，把“div trunc”相关能力封装成可被外部或内部复用的函数。

## 第 11 段（第 164-187 行）

```c
int stb_div_floor(int v1, int v2)
{
   #ifdef C_INTEGER_DIVISION_FLOORS
   return v1/v2;
   #else
   if (v1 >= 0 && v2 < 0) {
      if (v2 + 1 >= INT_MIN + v1) // check if increasing v1's magnitude overflows
         return -stb__div((v2+1)-v1,v2); // nope, so just compute it
      else
         return -stb__div(-v1,v2) + ((-v1)%v2 ? -1 : 0);
   }
   if (v1 < 0 && v2 >= 0) {
      if (v1 != INT_MIN) {
         if (v1 + 1 >= INT_MIN + v2) // check if increasing v1's magnitude overflows
            return -stb__div((v1+1)-v2,-v2); // nope, so just compute it
         else
            return -stb__div(-v1,v2) + (stb__mod(v1,-v2) ? -1 : 0);
      } else // it must be possible to compute -(v1+v2) without overflowing
         return -stb__div(-(v1+v2),v2) + (stb__mod(-(v1+v2),v2) ? -2 : -1);
   } else
      return v1/v2;           // same sign, so expect truncation
   #endif
}
```

解说：这一段实现或声明库接口 `stb_div_floor`，把“div floor”相关能力封装成可被外部或内部复用的函数。

## 第 12 段（第 188-223 行）

```c
int stb_div_eucl(int v1, int v2)
{
   int q,r;
   #ifdef C_INTEGER_DIVISION_TRUNCATES
   q = v1/v2;
   r = v1%v2;
   #else
   // handle every quadrant separately, since we can't rely on q and r flor
   if (v1 >= 0)
      if (v2 >= 0)
         return stb__div(v1,v2);
      else if (v2 != INT_MIN)
         q = -stb__div(v1,-v2), r = stb__mod(v1,-v2);
      else
         q = 0, r = v1;
   else if (v1 != INT_MIN)
      if (v2 >= 0)
         q = -stb__div(-v1,v2), r = -stb__mod(-v1,v2);
      else if (v2 != INT_MIN)
         q = stb__div(-v1,-v2), r = -stb__mod(-v1,-v2);
      else // if v2 is INT_MIN, then we can't use -v2, but we can't divide by v2
         q = 1, r = v1-q*v2;
   else // if v1 is INT_MIN, we have to move away from overflow place
      if (v2 >= 0)
         q = -stb__div(-(v1+v2),v2)-1, r = -stb__mod(-(v1+v2),v2);
      else if (v2 != INT_MIN)
         q = stb__div(-(v1-v2),-v2)+1, r = -stb__mod(-(v1-v2),-v2);
      else // for INT_MIN / INT_MIN, we need to be extra-careful to avoid overflow
         q = 1, r = 0;
   #endif
   if (r >= 0)
      return q;
   else
      return q + (v2 > 0 ? -1 : 1);
}
```

解说：这一段实现或声明库接口 `stb_div_eucl`，把“div eucl”相关能力封装成可被外部或内部复用的函数。

## 第 13 段（第 224-244 行）

```c
int stb_mod_trunc(int v1, int v2)
{
   #ifdef C_INTEGER_DIVISION_TRUNCATES
   return v1%v2;
   #else
   if (v1 >= 0) { // modulus result should always be positive
      int r = stb__mod(v1,v2);
      if (r >= 0)
         return r;
      else
         return r - (v2 < 0 ? v2 : -v2);
   } else {    // modulus result should always be negative
      int r = stb__mod(v1,v2);
      if (r <= 0)
         return r;
      else
         return r + (v2 < 0 ? v2 : -v2);
   }
   #endif
}
```

解说：这一段实现或声明库接口 `stb_mod_trunc`，把“mod trunc”相关能力封装成可被外部或内部复用的函数。

## 第 14 段（第 245-265 行）

```c
int stb_mod_floor(int v1, int v2)
{
   #ifdef C_INTEGER_DIVISION_FLOORS
   return v1%v2;
   #else
   if (v2 >= 0) { // result should always be positive
      int r = stb__mod(v1,v2);
      if (r >= 0)
         return r;
      else
         return r + v2;
   } else { // result should always be negative
      int r = stb__mod(v1,v2);
      if (r <= 0)
         return r;
      else
         return r + v2;
   }
   #endif
}
```

解说：这一段实现或声明库接口 `stb_mod_floor`，把“mod floor”相关能力封装成可被外部或内部复用的函数。

## 第 15 段（第 266-275 行）

```c
int stb_mod_eucl(int v1, int v2)
{
   int r = stb__mod(v1,v2);

   if (r >= 0)
      return r;
   else
      return r - (v2 < 0 ? v2 : -v2); // negative abs() [to avoid overflow]
}
```

解说：这一段实现或声明库接口 `stb_mod_eucl`，把“mod eucl”相关能力封装成可被外部或内部复用的函数。

## 第 16 段（第 276-280 行）

```c
#ifdef STB_DIVIDE_TEST
#include <stdio.h>
#include <math.h>
#include <limits.h>
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 17 段（第 281-283 行）

```c
int show=0;
int err=0;
```

解说：这一段进行变量初始化或状态更新，为后续计算准备输入数据，或保存当前步骤得到的中间结果。

## 第 18 段（第 284-310 行）

```c
void stbdiv_check(int q, int r, int a, int b, char *type, int dir)
{
   if ((dir > 0 && r < 0) || (dir < 0 && r > 0)) {
      fprintf(stderr, "FAILED: %s(%d,%d) remainder %d in wrong direction\n", type,a,b,r);
      err++;
   } else
      if (b != INT_MIN) // can't compute abs(), but if b==INT_MIN all remainders are valid
         if (r <= -abs(b) || r >= abs(b)) {
            fprintf(stderr, "FAILED: %s(%d,%d) remainder %d out of range\n", type,a,b,r);
            err++;
         }
   #ifdef STB_DIVIDE_TEST_64
   {
      STB_DIVIDE_TEST_64 q64 = q, r64=r, a64=a, b64=b;
      if (q64*b64+r64 != a64) {
         fprintf(stderr, "FAILED: %s(%d,%d) remainder %d doesn't match quotient %d\n", type,a,b,r,q);
         err++;
      }
   }
   #else
   if (q*b+r != a) {
      fprintf(stderr, "FAILED: %s(%d,%d) remainder %d doesn't match quotient %d\n", type,a,b,r,q);
      err++;
   }
   #endif
}
```

解说：这一段实现或声明库接口 `stbdiv_check`，把“div check”相关能力封装成可被外部或内部复用的函数。

## 第 19 段（第 311-322 行）

```c
void test(int a, int b)
{
   int q,r;
   if (show) printf("(%+11d,%+d) |  ", a,b);
   q = stb_div_trunc(a,b), r = stb_mod_trunc(a,b);
   if (show) printf("(%+11d,%+2d)  ", q,r); stbdiv_check(q,r,a,b, "trunc",a);
   q = stb_div_floor(a,b), r = stb_mod_floor(a,b);
   if (show) printf("(%+11d,%+2d)  ", q,r); stbdiv_check(q,r,a,b, "floor",b);
   q = stb_div_eucl (a,b), r = stb_mod_eucl (a,b);
   if (show) printf("(%+11d,%+2d)\n", q,r); stbdiv_check(q,r,a,b, "euclidean",1);
}
```

解说：这一段实现测试函数 `test`，通过构造输入、调用目标接口并检查结果，验证相关功能是否按预期工作。

## 第 20 段（第 323-334 行）

```c
void testh(int a, int b)
{
   int q,r;
   if (show) printf("(%08x,%08x) |\n", a,b);
   q = stb_div_trunc(a,b), r = stb_mod_trunc(a,b); stbdiv_check(q,r,a,b, "trunc",a);
   if (show) printf("             (%08x,%08x)", q,r);
   q = stb_div_floor(a,b), r = stb_mod_floor(a,b); stbdiv_check(q,r,a,b, "floor",b);
   if (show) printf("   (%08x,%08x)", q,r);
   q = stb_div_eucl (a,b), r = stb_mod_eucl (a,b); stbdiv_check(q,r,a,b, "euclidean",1);
   if (show) printf("   (%08x,%08x)\n ", q,r);
}
```

解说：这一段实现测试函数 `testh`，通过构造输入、调用目标接口并检查结果，验证相关功能是否按预期工作。

## 第 21 段（第 335-392 行）

```c
int main(int argc, char **argv)
{
   if (argc > 1) show=1;

   test(8,3);
   test(8,-3);
   test(-8,3);
   test(-8,-3);
   test(1,2);
   test(1,-2);
   test(-1,2);
   test(-1,-2);
   test(8,4);
   test(8,-4);
   test(-8,4);
   test(-8,-4);

   test(INT_MAX,1);
   test(INT_MIN,1);
   test(INT_MIN+1,1);
   test(INT_MAX,-1);
   //test(INT_MIN,-1); // this traps in MSVC, so we leave it untested
   test(INT_MIN+1,-1);
   test(INT_MIN,-2);
   test(INT_MIN+1,2);
   test(INT_MIN+1,-2);
   test(INT_MAX,2);
   test(INT_MAX,-2);
   test(INT_MIN+1,2);
   test(INT_MIN+1,-2);
   test(INT_MIN,2);
   test(INT_MIN,-2);
   test(INT_MIN,7);
   test(INT_MIN,-7);
   test(INT_MIN+1,4);
   test(INT_MIN+1,-4);

   testh(-7, INT_MIN);
   testh(-1, INT_MIN);
   testh(1, INT_MIN);
   testh(7, INT_MIN);

   testh(INT_MAX-1, INT_MIN);
   testh(INT_MAX,   INT_MIN);
   testh(INT_MIN,   INT_MIN);
   testh(INT_MIN+1, INT_MIN);

   testh(INT_MAX-1, INT_MAX);
   testh(INT_MAX  , INT_MAX);
   testh(INT_MIN  , INT_MAX);
   testh(INT_MIN+1, INT_MAX);

   return err > 0 ? 1 : 0;
}
#endif // STB_DIVIDE_TEST
#endif // STB_DIVIDE_IMPLEMENTATION
#endif // INCLUDE_STB_DIVIDE_H
```

解说：这一段是程序入口，负责准备测试或工具运行所需的参数、调用核心逻辑，并把执行结果转换成进程退出状态。

## 第 22 段（第 393-433 行）

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
