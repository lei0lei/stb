# stb_leakcheck.h 代码解说

源文件：`stb_leakcheck.h`

本文按源文件中的自然代码段切分，每段先给出原始代码，再用中文解释这段代码在做什么以及它为什么存在。

## 第 1 段（第 1-5 行）

```c
// stb_leakcheck.h - v0.6 - quick & dirty malloc leak-checking - public domain
// LICENSE
//
//   See end of file.
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 2 段（第 6-15 行）

```c
#ifdef STB_LEAKCHECK_IMPLEMENTATION
#undef STB_LEAKCHECK_IMPLEMENTATION // don't implement more than once

// if we've already included leakcheck before, undefine the macros
#ifdef malloc
#undef malloc
#undef free
#undef realloc
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 3 段（第 16-19 行）

```c
#ifndef STB_LEAKCHECK_OUTPUT_PIPE
#define STB_LEAKCHECK_OUTPUT_PIPE stdout
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 4 段（第 20-26 行）

```c
#include <assert.h>
#include <string.h>
#include <stdlib.h>
#include <stdio.h>
#include <stddef.h>
typedef struct malloc_info stb_leakcheck_malloc_info;
```

解说：这一段是预处理指令，负责在真正编译前组织符号、平台差异和条件代码。

## 第 5 段（第 27-34 行）

```c
struct malloc_info
{
   const char *file;
   int line;
   size_t size;
   stb_leakcheck_malloc_info *next,*prev;
};
```

解说：这一段声明结构体 `prev`，把相关字段组织成一个数据对象，供后续算法在函数之间传递和维护状态。

## 第 6 段（第 35-36 行）

```c
static stb_leakcheck_malloc_info *mi_head;
```

解说：这一段涉及动态内存管理，负责申请、调整或释放运行时数据结构需要的存储空间。

## 第 7 段（第 37-51 行）

```c
void *stb_leakcheck_malloc(size_t sz, const char *file, int line)
{
   stb_leakcheck_malloc_info *mi = (stb_leakcheck_malloc_info *) malloc(sz + sizeof(*mi));
   if (mi == NULL) return mi;
   mi->file = file;
   mi->line = line;
   mi->next = mi_head;
   if (mi_head)
      mi->next->prev = mi;
   mi->prev = NULL;
   mi->size = (int) sz;
   mi_head = mi;
   return mi+1;
}
```

解说：这一段涉及动态内存管理，负责申请、调整或释放运行时数据结构需要的存储空间。

## 第 8 段（第 52-69 行）

```c
void stb_leakcheck_free(void *ptr)
{
   if (ptr != NULL) {
      stb_leakcheck_malloc_info *mi = (stb_leakcheck_malloc_info *) ptr - 1;
      mi->size = ~mi->size;
      #ifndef STB_LEAKCHECK_SHOWALL
      if (mi->prev == NULL) {
         assert(mi_head == mi);
         mi_head = mi->next;
      } else
         mi->prev->next = mi->next;
      if (mi->next)
         mi->next->prev = mi->prev;
      free(mi);
      #endif
   }
}
```

解说：这一段实现或声明库接口 `stb_leakcheck_free`，把“leakcheck free”相关能力封装成可被外部或内部复用的函数。

## 第 9 段（第 70-95 行）

```c
void *stb_leakcheck_realloc(void *ptr, size_t sz, const char *file, int line)
{
   if (ptr == NULL) {
      return stb_leakcheck_malloc(sz, file, line);
   } else if (sz == 0) {
      stb_leakcheck_free(ptr);
      return NULL;
   } else {
      stb_leakcheck_malloc_info *mi = (stb_leakcheck_malloc_info *) ptr - 1;
      if (sz <= mi->size)
         return ptr;
      else {
         #ifdef STB_LEAKCHECK_REALLOC_PRESERVE_MALLOC_FILELINE
         void *q = stb_leakcheck_malloc(sz, mi->file, mi->line);
         #else
         void *q = stb_leakcheck_malloc(sz, file, line);
         #endif
         if (q) {
            memcpy(q, ptr, mi->size);
            stb_leakcheck_free(ptr);
         }
         return q;
      }
   }
}
```

解说：这一段涉及动态内存管理，负责申请、调整或释放运行时数据结构需要的存储空间。

## 第 10 段（第 96-117 行）

```c
static void stblkck_internal_print(const char *reason, stb_leakcheck_malloc_info *mi)
{
#if defined(_MSC_VER) && _MSC_VER < 1900 // 1900=VS 2015
   // Compilers that use the old MS C runtime library don't have %zd
   // and the older ones don't even have %lld either... however, the old compilers
   // without "long long" don't support 64-bit targets either, so here's the
   // compromise:
   #if _MSC_VER < 1400 // before VS 2005
      fprintf(STB_LEAKCHECK_OUTPUT_PIPE, "%s: %s (%4d): %8d bytes at %p\n", reason, mi->file, mi->line, (int)mi->size, (void*)(mi+1));
   #else
      fprintf(STB_LEAKCHECK_OUTPUT_PIPE, "%s: %s (%4d): %16lld bytes at %p\n", reason, mi->file, mi->line, (long long)mi->size, (void*)(mi+1));
   #endif
#else
   // Assume we have %zd on other targets.
   #ifdef __MINGW32__
      __mingw_fprintf(STB_LEAKCHECK_OUTPUT_PIPE, "%s: %s (%4d): %zd bytes at %p\n", reason, mi->file, mi->line, mi->size, (void*)(mi+1));
   #else
      fprintf(STB_LEAKCHECK_OUTPUT_PIPE, "%s: %s (%4d): %zd bytes at %p\n", reason, mi->file, mi->line, mi->size, (void*)(mi+1));
   #endif
#endif
}
```

解说：这一段实现或声明库接口 `stblkck_internal_print`，把“lkck internal print”相关能力封装成可被外部或内部复用的函数。

## 第 11 段（第 118-136 行）

```c
void stb_leakcheck_dumpmem(void)
{
   stb_leakcheck_malloc_info *mi = mi_head;
   while (mi) {
      if ((ptrdiff_t) mi->size >= 0)
         stblkck_internal_print("LEAKED", mi);
      mi = mi->next;
   }
   #ifdef STB_LEAKCHECK_SHOWALL
   mi = mi_head;
   while (mi) {
      if ((ptrdiff_t) mi->size < 0)
         stblkck_internal_print("FREED ", mi);
      mi = mi->next;
   }
   #endif
}
#endif // STB_LEAKCHECK_IMPLEMENTATION
```

解说：这一段实现或声明库接口 `stb_leakcheck_dumpmem`，把“leakcheck dumpmem”相关能力封装成可被外部或内部复用的函数。

## 第 12 段（第 137-139 行）

```c
#if !defined(INCLUDE_STB_LEAKCHECK_H) || !defined(malloc)
#define INCLUDE_STB_LEAKCHECK_H
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 13 段（第 140-141 行）

```c
#include <stdlib.h> // we want to define the macros *after* stdlib to avoid a slew of errors
```

解说：这一段引入编译所需的头文件或本项目内的依赖，使后面的类型、宏和函数声明能够被编译器识别。

## 第 14 段（第 142-145 行）

```c
#define malloc(sz)    stb_leakcheck_malloc(sz, __FILE__, __LINE__)
#define free(p)       stb_leakcheck_free(p)
#define realloc(p,sz) stb_leakcheck_realloc(p,sz, __FILE__, __LINE__)
```

解说：这一段定义宏（如 `malloc, free, realloc`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 15 段（第 146-150 行）

```c
extern void * stb_leakcheck_malloc(size_t sz, const char *file, int line);
extern void * stb_leakcheck_realloc(void *ptr, size_t sz, const char *file, int line);
extern void   stb_leakcheck_free(void *ptr);
extern void   stb_leakcheck_dumpmem(void);
```

解说：这一段涉及动态内存管理，负责申请、调整或释放运行时数据结构需要的存储空间。

## 第 16 段（第 151-194 行）

```c
#endif // INCLUDE_STB_LEAKCHECK_H


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
