# stb_lib.h 代码解说

源文件：`tests/prerelease/stb_lib.h`

本文按源文件中的自然代码段切分，每段先给出原始代码，再用中文解释这段代码在做什么以及它为什么存在。

## 第 1 段（第 1-6 行）

```c
/* stb_lib.h - v1.00 - http://nothings.org/stb
   no warranty is offered or implied; use this code at your own risk

 ============================================================================
   You MUST
```

解说：这一段进行变量初始化或状态更新，为后续计算准备输入数据，或保存当前步骤得到的中间结果。

## 第 2 段（第 7-11 行）

```c
      #define STB_LIB_IMPLEMENTATION
                                                                             
   in EXACTLY _one_ C or C++ file that includes this header, BEFORE the
   include, like this:
```

解说：这一段定义宏（如 `STB_LIB_IMPLEMENTATION`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 3 段（第 12-17 行）

```c
      #define STB_LIB_IMPLEMENTATION
      #include "stblib_files.h"
      
   All other files should just #include "stblib_files.h" without the #define.
 ============================================================================
```

解说：这一段定义宏（如 `STB_LIB_IMPLEMENTATION`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 4 段（第 18-21 行）

```c
LICENSE

 See end of file for license information.
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 5 段（第 22-25 行）

```c
CREDITS

 Written by Sean Barrett.
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 6 段（第 26-34 行）

```c
 Fixes:
  Philipp Wiesemann    Robert Nix
  r-lyeh               blackpawn
  github:Mojofreem     Ryan Whitworth
  Vincent Isambart     Mike Sartain
  Eugene Opalev        Tim Sjostrand
  github:infatum       Dave Butler
*/
```

解说：这一段涉及动态内存管理，负责申请、调整或释放运行时数据结构需要的存储空间。

## 第 7 段（第 35-36 行）

```c
#ifndef STB_INCLUDE_STB_LIB_H
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 8 段（第 37-38 行）

```c
#include <stdarg.h>
```

解说：这一段引入编译所需的头文件或本项目内的依赖，使后面的类型、宏和函数声明能够被编译器识别。

## 第 9 段（第 39-53 行）

```c
#if defined(_WIN32) && !defined(__MINGW32__)
   #ifndef _CRT_SECURE_NO_WARNINGS
   #define _CRT_SECURE_NO_WARNINGS
   #endif
   #ifndef _CRT_NONSTDC_NO_DEPRECATE
   #define _CRT_NONSTDC_NO_DEPRECATE
   #endif
   #ifndef _CRT_NON_CONFORMING_SWPRINTFS
   #define _CRT_NON_CONFORMING_SWPRINTFS
   #endif
   #if !defined(_MSC_VER) || _MSC_VER > 1700
   #include <intrin.h> // _BitScanReverse
   #endif
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 10 段（第 54-58 行）

```c
#include <stdlib.h>     // stdlib could have min/max
#include <stdio.h>      // need FILE
#include <string.h>     // stb_define_hash needs memcpy/memset
#include <time.h>       // stb_dirtree
```

解说：这一段引入编译所需的头文件或本项目内的依赖，使后面的类型、宏和函数声明能够被编译器识别。

## 第 11 段（第 59-74 行）

```c
typedef unsigned char stb_uchar;
typedef unsigned char stb_uint8;
typedef unsigned int  stb_uint;
typedef unsigned short stb_uint16;
typedef          short stb_int16;
typedef   signed char  stb_int8;
#if defined(STB_USE_LONG_FOR_32_BIT_INT) || defined(STB_LONG32)
  typedef unsigned long  stb_uint32;
  typedef          long  stb_int32;
#else
  typedef unsigned int   stb_uint32;
  typedef          int   stb_int32;
#endif
typedef char stb__testsize2_16[sizeof(stb_uint16)==2 ? 1 : -1];
typedef char stb__testsize2_32[sizeof(stb_uint32)==4 ? 1 : -1];
```

解说：这一段通过 `typedef` 给类型取别名，减少平台差异或复杂类型签名对后续代码的干扰。

## 第 12 段（第 75-86 行）

```c
#ifdef _MSC_VER
  typedef unsigned __int64 stb_uint64;
  typedef          __int64 stb_int64;
  #define STB_IMM_UINT64(literalui64) (literalui64##ui64)
#else
  // ??
  typedef unsigned long long stb_uint64;
  typedef          long long stb_int64;
  #define STB_IMM_UINT64(literalui64) (literalui64##ULL)
#endif
typedef char stb__testsize2_64[sizeof(stb_uint64)==8 ? 1 : -1];
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 13 段（第 87-92 行）

```c
#ifdef __cplusplus
   #define STB_EXTERN   extern "C"
#else
   #define STB_EXTERN   extern
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 14 段（第 93-99 行）

```c
// check for well-known debug defines
#if defined(DEBUG) || defined(_DEBUG) || defined(DBG)
   #ifndef NDEBUG
      #define STB_DEBUG
   #endif
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 15 段（第 100-104 行）

```c
#ifdef STB_DEBUG
   #include <assert.h>
#endif
#endif // STB_INCLUDE_STB_LIB_H
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 16 段（第 105-120 行）

```c
#ifdef STB_LIB_IMPLEMENTATION
   #include <assert.h>
   #include <stdarg.h>
   #include <stddef.h>
   #include <ctype.h>
   #include <math.h>
   #ifndef _WIN32
   #include <unistd.h>
   #else
   #include <io.h>      // _mktemp
   #include <direct.h>  // _rmdir
   #endif
   #include <sys/types.h> // stat()/_stat()
   #include <sys/stat.h>  // stat()/_stat()
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 17 段（第 121-125 行）

```c
//////////////////////////////////////////////////////////////////////////////
//
//                         Miscellany
//
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 18 段（第 126-133 行）

```c
#ifdef _WIN32
   #define stb_stricmp(a,b)    stricmp(a,b)
   #define stb_strnicmp(a,b,n) strnicmp(a,b,n)
#else
   #define stb_stricmp(a,b)    strcasecmp(a,b)
   #define stb_strnicmp(a,b,n) strncasecmp(a,b,n)
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 19 段（第 134-139 行）

```c
#ifndef STB_INCLUDE_STB_LIB_H
STB_EXTERN void stb_fatal(char *fmt, ...);
STB_EXTERN void stb_swap(void *p, void *q, size_t sz);
STB_EXTERN double stb_linear_remap(double x, double x_min, double x_max,
                                  double out_min, double out_max);
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 20 段（第 140-143 行）

```c
#define stb_arrcount(x)   (sizeof(x)/sizeof((x)[0]))
#define stb_lerp(t,a,b)               ( (a) + (t) * (float) ((b)-(a)) )
#define stb_unlerp(t,a,b)             ( ((t) - (a)) / (float) ((b) - (a)) )
```

解说：这一段定义宏（如 `stb_arrcount, stb_lerp, stb_unlerp`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 21 段（第 144-146 行）

```c
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 22 段（第 147-169 行）

```c
#ifdef STB_LIB_IMPLEMENTATION
void stb_fatal(char *s, ...)
{
   va_list a;
   va_start(a,s);
   fputs("Fatal error: ", stderr);
   vfprintf(stderr, s, a);
   va_end(a);
   fputs("\n", stderr);
   #ifdef STB_DEBUG
   #ifdef _MSC_VER
   #ifndef _WIN64
   __asm int 3;   // trap to debugger!
   #else
   __debugbreak();
   #endif
   #else
   __builtin_trap();
   #endif
   #endif
   exit(1);
}
```

解说：这一段实现或声明库接口 `stb_fatal`，把“fatal”相关能力封装成可被外部或内部复用的函数。

## 第 23 段（第 170-201 行）

```c
typedef struct { char d[4]; } stb__4;
typedef struct { char d[8]; } stb__8;

// optimize the small cases, though you shouldn't be calling this for those!
void stb_swap(void *p, void *q, size_t sz)
{
   char buffer[256];
   if (p == q) return;
   if (sz == 4) {
      stb__4 temp    = * ( stb__4 *) p;
      * (stb__4 *) p = * ( stb__4 *) q;
      * (stb__4 *) q = temp;
      return;
   } else if (sz == 8) {
      stb__8 temp    = * ( stb__8 *) p;
      * (stb__8 *) p = * ( stb__8 *) q;
      * (stb__8 *) q = temp;
      return;
   }

   while (sz > sizeof(buffer)) {
      stb_swap(p, q, sizeof(buffer));
      p = (char *) p + sizeof(buffer);
      q = (char *) q + sizeof(buffer);
      sz -= sizeof(buffer);
   }

   memcpy(buffer, p     , sz);
   memcpy(p     , q     , sz);
   memcpy(q     , buffer, sz);
}
```

解说：这一段声明结构体 `return`，把相关字段组织成一个数据对象，供后续算法在函数之间传递和维护状态。

## 第 24 段（第 202-205 行）

```c
#ifdef stb_linear_remap
#undef stb_linear_remap
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 25 段（第 206-211 行）

```c
double stb_linear_remap(double x, double x_min, double x_max,
                                  double out_min, double out_max)
{
   return stb_lerp(stb_unlerp(x,x_min,x_max),out_min,out_max);
}
```

解说：这一段实现或声明库接口 `stb_linear_remap`，把“linear remap”相关能力封装成可被外部或内部复用的函数。

## 第 26 段（第 212-214 行）

```c
#define stb_linear_remap(t,a,b,c,d)   stb_lerp(stb_unlerp(t,a,b),c,d)
#endif // STB_LIB_IMPLEMENTATION
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 27 段（第 215-221 行）

```c
#ifndef STB_INCLUDE_STB_LIB_H
// avoid unnecessary function call, but define function so its address can be taken
#ifndef stb_linear_remap
#define stb_linear_remap(t,a,b,c,d)   stb_lerp(stb_unlerp(t,a,b),c,d)
#endif
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 28 段（第 222-227 行）

```c
//////////////////////////////////////////////////////////////////////////////
//
//     cross-platform snprintf because they keep changing that,
//     and with old compilers without vararg macros we can't write
//     a macro wrapper to fix it up
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 29 段（第 228-233 行）

```c
#ifndef STB_INCLUDE_STB_LIB_H
STB_EXTERN int  stb_snprintf(char *s, size_t n, const char *fmt, ...);
STB_EXTERN int  stb_vsnprintf(char *s, size_t n, const char *fmt, va_list v);
STB_EXTERN char *stb_sprintf(const char *fmt, ...);
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 30 段（第 234-235 行）

```c
#ifdef STB_LIB_IMPLEMENTATION
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 31 段（第 236-249 行）

```c
int stb_vsnprintf(char *s, size_t n, const char *fmt, va_list v)
{
   int res;
   #ifdef _WIN32
   // Could use "_vsnprintf_s(s, n, _TRUNCATE, fmt, v)" ?
   res = _vsnprintf(s,n,fmt,v);
   #else
   res = vsnprintf(s,n,fmt,v);
   #endif
   if (n) s[n-1] = 0;
   // Unix returns length output would require, Windows returns negative when truncated.
   return (res >= (int) n || res < 0) ? -1 : res;
}
```

解说：这一段实现或声明库接口 `stb_vsnprintf`，把“vsnprintf”相关能力封装成可被外部或内部复用的函数。

## 第 32 段（第 250-259 行）

```c
int stb_snprintf(char *s, size_t n, const char *fmt, ...)
{
   int res;
   va_list v;
   va_start(v,fmt);
   res = stb_vsnprintf(s, n, fmt, v);
   va_end(v);
   return res;
}
```

解说：这一段实现或声明库接口 `stb_snprintf`，把“snprintf”相关能力封装成可被外部或内部复用的函数。

## 第 33 段（第 260-270 行）

```c
char *stb_sprintf(const char *fmt, ...)
{
   static char buffer[1024];
   va_list v;
   va_start(v,fmt);
   stb_vsnprintf(buffer,1024,fmt,v);
   va_end(v);
   return buffer;
}
#endif
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 34 段（第 271-279 行）

```c
//////////////////////////////////////////////////////////////////////////////
//
//                         Windows UTF8 filename handling
//
// Windows stupidly treats 8-bit filenames as some dopey code page,
// rather than utf-8. If we want to use utf8 filenames, we have to
// convert them to WCHAR explicitly and call WCHAR versions of the
// file functions. So, ok, we do.
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 35 段（第 281-289 行）

```c
#ifndef STB_INCLUDE_STB_LIB_H
#ifdef _WIN32
   #define stb__fopen(x,y)    _wfopen((const wchar_t *)stb__from_utf8(x), (const wchar_t *)stb__from_utf8_alt(y))
   #define stb__windows(x,y)  x
#else
   #define stb__fopen(x,y)    fopen(x,y)
   #define stb__windows(x,y)  y
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 36 段（第 291-292 行）

```c
typedef unsigned short stb__wchar;
```

解说：这一段通过 `typedef` 给类型取别名，减少平台差异或复杂类型签名对后续代码的干扰。

## 第 37 段（第 293-295 行）

```c
STB_EXTERN stb__wchar * stb_from_utf8(stb__wchar *buffer, char *str, int n);
STB_EXTERN char       * stb_to_utf8  (char *buffer, stb__wchar *str, int n);
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 38 段（第 296-300 行）

```c
STB_EXTERN stb__wchar *stb__from_utf8(char *str);
STB_EXTERN stb__wchar *stb__from_utf8_alt(char *str);
STB_EXTERN char *stb__to_utf8(stb__wchar *str);
#endif
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 39 段（第 301-351 行）

```c
#ifdef STB_LIB_IMPLEMENTATION
stb__wchar * stb_from_utf8(stb__wchar *buffer, char *ostr, int n)
{
   unsigned char *str = (unsigned char *) ostr;
   stb_uint32 c;
   int i=0;
   --n;
   while (*str) {
      if (i >= n)
         return NULL;
      if (!(*str & 0x80))
         buffer[i++] = *str++;
      else if ((*str & 0xe0) == 0xc0) {
         if (*str < 0xc2) return NULL;
         c = (*str++ & 0x1f) << 6;
         if ((*str & 0xc0) != 0x80) return NULL;
         buffer[i++] = c + (*str++ & 0x3f);
      } else if ((*str & 0xf0) == 0xe0) {
         if (*str == 0xe0 && (str[1] < 0xa0 || str[1] > 0xbf)) return NULL;
         if (*str == 0xed && str[1] > 0x9f) return NULL; // str[1] < 0x80 is checked below
         c = (*str++ & 0x0f) << 12;
         if ((*str & 0xc0) != 0x80) return NULL;
         c += (*str++ & 0x3f) << 6;
         if ((*str & 0xc0) != 0x80) return NULL;
         buffer[i++] = c + (*str++ & 0x3f);
      } else if ((*str & 0xf8) == 0xf0) {
         if (*str > 0xf4) return NULL;
         if (*str == 0xf0 && (str[1] < 0x90 || str[1] > 0xbf)) return NULL;
         if (*str == 0xf4 && str[1] > 0x8f) return NULL; // str[1] < 0x80 is checked below
         c = (*str++ & 0x07) << 18;
         if ((*str & 0xc0) != 0x80) return NULL;
         c += (*str++ & 0x3f) << 12;
         if ((*str & 0xc0) != 0x80) return NULL;
         c += (*str++ & 0x3f) << 6;
         if ((*str & 0xc0) != 0x80) return NULL;
         c += (*str++ & 0x3f);
         // utf-8 encodings of values used in surrogate pairs are invalid
         if ((c & 0xFFFFF800) == 0xD800) return NULL;
         if (c >= 0x10000) {
            c -= 0x10000;
            if (i + 2 > n) return NULL;
            buffer[i++] = 0xD800 | (0x3ff & (c >> 10));
            buffer[i++] = 0xDC00 | (0x3ff & (c      ));
         }
      } else
         return NULL;
   }
   buffer[i] = 0;
   return buffer;
}
```

解说：这一段实现或声明库接口 `stb_from_utf8`，把“from utf8”相关能力封装成可被外部或内部复用的函数。

## 第 40 段（第 352-387 行）

```c
char * stb_to_utf8(char *buffer, stb__wchar *str, int n)
{
   int i=0;
   --n;
   while (*str) {
      if (*str < 0x80) {
         if (i+1 > n) return NULL;
         buffer[i++] = (char) *str++;
      } else if (*str < 0x800) {
         if (i+2 > n) return NULL;
         buffer[i++] = 0xc0 + (*str >> 6);
         buffer[i++] = 0x80 + (*str & 0x3f);
         str += 1;
      } else if (*str >= 0xd800 && *str < 0xdc00) {
         stb_uint32 c;
         if (i+4 > n) return NULL;
         c = ((str[0] - 0xd800) << 10) + ((str[1]) - 0xdc00) + 0x10000;
         buffer[i++] = 0xf0 + (c >> 18);
         buffer[i++] = 0x80 + ((c >> 12) & 0x3f);
         buffer[i++] = 0x80 + ((c >>  6) & 0x3f);
         buffer[i++] = 0x80 + ((c      ) & 0x3f);
         str += 2;
      } else if (*str >= 0xdc00 && *str < 0xe000) {
         return NULL;
      } else {
         if (i+3 > n) return NULL;
         buffer[i++] = 0xe0 + (*str >> 12);
         buffer[i++] = 0x80 + ((*str >> 6) & 0x3f);
         buffer[i++] = 0x80 + ((*str     ) & 0x3f);
         str += 1;
      }
   }
   buffer[i] = 0;
   return buffer;
}
```

解说：这一段实现或声明库接口 `stb_to_utf8`，把“to utf8”相关能力封装成可被外部或内部复用的函数。

## 第 41 段（第 388-393 行）

```c
stb__wchar *stb__from_utf8(char *str)
{
   static stb__wchar buffer[4096];
   return stb_from_utf8(buffer, str, 4096);
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 42 段（第 394-399 行）

```c
stb__wchar *stb__from_utf8_alt(char *str)
{
   static stb__wchar buffer[4096];
   return stb_from_utf8(buffer, str, 4096);
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 43 段（第 400-406 行）

```c
char *stb__to_utf8(stb__wchar *str)
{
   static char buffer[4096];
   return stb_to_utf8(buffer, str, 4096);
}
#endif
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 44 段（第 407-411 行）

```c
//////////////////////////////////////////////////////////////////////////////
//
//                            qsort Compare Routines
//                              NOT THREAD SAFE
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 45 段（第 412-421 行）

```c
#ifndef STB_INCLUDE_STB_LIB_H
STB_EXTERN int (*stb_intcmp(int offset))(const void *a, const void *b);
STB_EXTERN int (*stb_qsort_strcmp(int offset))(const void *a, const void *b);
STB_EXTERN int (*stb_qsort_stricmp(int offset))(const void *a, const void *b);
STB_EXTERN int (*stb_floatcmp(int offset))(const void *a, const void *b);
STB_EXTERN int (*stb_doublecmp(int offset))(const void *a, const void *b);
STB_EXTERN int (*stb_ucharcmp(int offset))(const void *a, const void *b);
STB_EXTERN int (*stb_charcmp(int offset))(const void *a, const void *b);
#endif
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 46 段（第 422-425 行）

```c
#ifdef STB_LIB_IMPLEMENTATION
static int stb__intcmpoffset, stb__ucharcmpoffset, stb__strcmpoffset;
static int stb__floatcmpoffset, stb__doublecmpoffset, stb__charcmpoffset;
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 47 段（第 426-432 行）

```c
int stb__intcmp(const void *a, const void *b)
{
   const int p = *(const int *) ((const char *) a + stb__intcmpoffset);
   const int q = *(const int *) ((const char *) b + stb__intcmpoffset);
   return p < q ? -1 : p > q;
}
```

解说：这一段实现或声明库接口 `stb__intcmp`，把“intcmp”相关能力封装成可被外部或内部复用的函数。

## 第 48 段（第 433-439 行）

```c
int stb__ucharcmp(const void *a, const void *b)
{
   const int p = *(const unsigned char *) ((const char *) a + stb__ucharcmpoffset);
   const int q = *(const unsigned char *) ((const char *) b + stb__ucharcmpoffset);
   return p < q ? -1 : p > q;
}
```

解说：这一段实现或声明库接口 `stb__ucharcmp`，把“ucharcmp”相关能力封装成可被外部或内部复用的函数。

## 第 49 段（第 440-446 行）

```c
int stb__charcmp(const void *a, const void *b)
{
   const int p = *(const char *) ((const char *) a + stb__ucharcmpoffset);
   const int q = *(const char *) ((const char *) b + stb__ucharcmpoffset);
   return p < q ? -1 : p > q;
}
```

解说：这一段实现或声明库接口 `stb__charcmp`，把“charcmp”相关能力封装成可被外部或内部复用的函数。

## 第 50 段（第 447-453 行）

```c
int stb__floatcmp(const void *a, const void *b)
{
   const float p = *(const float *) ((const char *) a + stb__floatcmpoffset);
   const float q = *(const float *) ((const char *) b + stb__floatcmpoffset);
   return p < q ? -1 : p > q;
}
```

解说：这一段实现或声明库接口 `stb__floatcmp`，把“floatcmp”相关能力封装成可被外部或内部复用的函数。

## 第 51 段（第 454-460 行）

```c
int stb__doublecmp(const void *a, const void *b)
{
   const double p = *(const double *) ((const char *) a + stb__doublecmpoffset);
   const double q = *(const double *) ((const char *) b + stb__doublecmpoffset);
   return p < q ? -1 : p > q;
}
```

解说：这一段实现或声明库接口 `stb__doublecmp`，把“doublecmp”相关能力封装成可被外部或内部复用的函数。

## 第 52 段（第 461-467 行）

```c
int stb__qsort_strcmp(const void *a, const void *b)
{
   const char *p = *(const char **) ((const char *) a + stb__strcmpoffset);
   const char *q = *(const char **) ((const char *) b + stb__strcmpoffset);
   return strcmp(p,q);
}
```

解说：这一段实现或声明库接口 `stb__qsort_strcmp`，把“qsort strcmp”相关能力封装成可被外部或内部复用的函数。

## 第 53 段（第 468-474 行）

```c
int stb__qsort_stricmp(const void *a, const void *b)
{
   const char *p = *(const char **) ((const char *) a + stb__strcmpoffset);
   const char *q = *(const char **) ((const char *) b + stb__strcmpoffset);
   return stb_stricmp(p,q);
}
```

解说：这一段实现或声明库接口 `stb__qsort_stricmp`，把“qsort stricmp”相关能力封装成可被外部或内部复用的函数。

## 第 54 段（第 475-480 行）

```c
int (*stb_intcmp(int offset))(const void *, const void *)
{
   stb__intcmpoffset = offset;
   return &stb__intcmp;
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 55 段（第 481-486 行）

```c
int (*stb_ucharcmp(int offset))(const void *, const void *)
{
   stb__ucharcmpoffset = offset;
   return &stb__ucharcmp;
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 56 段（第 487-492 行）

```c
int (*stb_charcmp(int offset))(const void *, const void *)
{
   stb__charcmpoffset = offset;
   return &stb__ucharcmp;
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 57 段（第 493-498 行）

```c
int (*stb_qsort_strcmp(int offset))(const void *, const void *)
{
   stb__strcmpoffset = offset;
   return &stb__qsort_strcmp;
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 58 段（第 499-504 行）

```c
int (*stb_qsort_stricmp(int offset))(const void *, const void *)
{
   stb__strcmpoffset = offset;
   return &stb__qsort_stricmp;
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 59 段（第 505-510 行）

```c
int (*stb_floatcmp(int offset))(const void *, const void *)
{
   stb__floatcmpoffset = offset;
   return &stb__floatcmp;
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 60 段（第 511-517 行）

```c
int (*stb_doublecmp(int offset))(const void *, const void *)
{
   stb__doublecmpoffset = offset;
   return &stb__doublecmp;
}
#endif
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 61 段（第 518-522 行）

```c
//////////////////////////////////////////////////////////////////////////////
//
//                           String Processing
//
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 62 段（第 523-525 行）

```c
#ifndef STB_INCLUDE_STB_LIB_H
#define stb_prefixi(s,t)  (0==stb_strnicmp((s),(t),strlen(t)))
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 63 段（第 526-535 行）

```c
enum stb_splitpath_flag
{
   STB_PATH = 1,
   STB_FILE = 2,
   STB_EXT  = 4,
   STB_PATH_FILE = STB_PATH + STB_FILE,
   STB_FILE_EXT  = STB_FILE + STB_EXT,
   STB_EXT_NO_PERIOD = 8,
};
```

解说：这一段声明枚举类型，把一组相关常量集中命名，便于后续代码用语义化名字表达状态或选项。

## 第 64 段（第 536-564 行）

```c
STB_EXTERN char * stb_skipwhite(char *s);
STB_EXTERN char * stb_trimwhite(char *s);
STB_EXTERN char * stb_skipnewline(char *s);
STB_EXTERN char * stb_strncpy(char *s, char *t, int n);
STB_EXTERN char * stb_substr(char *t, int n);
STB_EXTERN char * stb_duplower(char *s);
STB_EXTERN void   stb_tolower (char *s);
STB_EXTERN char * stb_strchr2 (char *s, char p1, char p2);
STB_EXTERN char * stb_strrchr2(char *s, char p1, char p2);
STB_EXTERN char * stb_strtok(char *output, char *src, char *delimit);
STB_EXTERN char * stb_strtok_keep(char *output, char *src, char *delimit);
STB_EXTERN char * stb_strtok_invert(char *output, char *src, char *allowed);
STB_EXTERN char * stb_dupreplace(char *s, char *find, char *replace);
STB_EXTERN void   stb_replaceinplace(char *s, char *find, char *replace);
STB_EXTERN char * stb_splitpath(char *output, char *src, int flag);
STB_EXTERN char * stb_splitpathdup(char *src, int flag);
STB_EXTERN char * stb_replacedir(char *output, char *src, char *dir);
STB_EXTERN char * stb_replaceext(char *output, char *src, char *ext);
STB_EXTERN void   stb_fixpath(char *path);
STB_EXTERN char * stb_shorten_path_readable(char *path, int max_len);
STB_EXTERN int    stb_suffix (char *s, char *t);
STB_EXTERN int    stb_suffixi(char *s, char *t);
STB_EXTERN int    stb_prefix (char *s, char *t);
STB_EXTERN char * stb_strichr(char *s, char t);
STB_EXTERN char * stb_stristr(char *s, char *t);
STB_EXTERN int    stb_prefix_count(char *s, char *t);
STB_EXTERN const char * stb_plural(int n);  // "s" or ""
STB_EXTERN size_t stb_strscpy(char *d, const char *s, size_t n);
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 65 段（第 565-576 行）

```c
STB_EXTERN char **stb_tokens(char *src, char *delimit, int *count);
STB_EXTERN char **stb_tokens_nested(char *src, char *delimit, int *count, char *nest_in, char *nest_out);
STB_EXTERN char **stb_tokens_nested_empty(char *src, char *delimit, int *count, char *nest_in, char *nest_out);
STB_EXTERN char **stb_tokens_allowempty(char *src, char *delimit, int *count);
STB_EXTERN char **stb_tokens_stripwhite(char *src, char *delimit, int *count);
STB_EXTERN char **stb_tokens_withdelim(char *src, char *delimit, int *count);
STB_EXTERN char **stb_tokens_quoted(char *src, char *delimit, int *count);
// with 'quoted', allow delimiters to appear inside quotation marks, and don't
// strip whitespace inside them (and we delete the quotation marks unless they
// appear back to back, in which case they're considered escaped)
#endif // STB_INCLUDE_STB_LIB_H
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 66 段（第 577-590 行）

```c
#ifdef STB_LIB_IMPLEMENTATION
#include <ctype.h>

size_t stb_strscpy(char *d, const char *s, size_t n)
{
   size_t len = strlen(s);
   if (len >= n) {
      if (n) d[0] = 0;
      return 0;
   }
   strcpy(d,s);
   return len + 1;
}
```

解说：这一段实现或声明库接口 `stb_strscpy`，把“strscpy”相关能力封装成可被外部或内部复用的函数。

## 第 67 段（第 591-595 行）

```c
const char *stb_plural(int n)
{
   return n == 1 ? "" : "s";
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 68 段（第 596-603 行）

```c
int stb_prefix(char *s, char *t)
{
   while (*t)
      if (*s++ != *t++)
         return 0;
   return 1;
}
```

解说：这一段实现或声明库接口 `stb_prefix`，把“prefix”相关能力封装成可被外部或内部复用的函数。

## 第 69 段（第 604-614 行）

```c
int stb_prefix_count(char *s, char *t)
{
   int c=0;
   while (*t) {
      if (*s++ != *t++)
         break;
      ++c;
   }
   return c;
}
```

解说：这一段实现或声明库接口 `stb_prefix_count`，把“prefix count”相关能力封装成可被外部或内部复用的函数。

## 第 70 段（第 615-624 行）

```c
int stb_suffix(char *s, char *t)
{
   size_t n = strlen(s);
   size_t m = strlen(t);
   if (m <= n)
      return 0 == strcmp(s+n-m, t);
   else
      return 0;
}
```

解说：这一段实现或声明库接口 `stb_suffix`，把“suffix”相关能力封装成可被外部或内部复用的函数。

## 第 71 段（第 625-634 行）

```c
int stb_suffixi(char *s, char *t)
{
   size_t n = strlen(s);
   size_t m = strlen(t);
   if (m <= n)
      return 0 == stb_stricmp(s+n-m, t);
   else
      return 0;
}
```

解说：这一段实现或声明库接口 `stb_suffixi`，把“suffixi”相关能力封装成可被外部或内部复用的函数。

## 第 72 段（第 635-645 行）

```c
// originally I was using this table so that I could create known sentinel
// values--e.g. change whitetable[0] to be true if I was scanning for whitespace,
// and false if I was scanning for nonwhite. I don't appear to be using that
// functionality anymore (I do for tokentable, though), so just replace it
// with isspace()
char *stb_skipwhite(char *s)
{
   while (isspace((unsigned char) *s)) ++s;
   return s;
}
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 73 段（第 646-654 行）

```c
char *stb_skipnewline(char *s)
{
   if (s[0] == '\r' || s[0] == '\n') {
      if (s[0]+s[1] == '\r' + '\n') ++s;
      ++s;
   }
   return s;
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 74 段（第 655-666 行）

```c
char *stb_trimwhite(char *s)
{
   int i,n;
   s = stb_skipwhite(s);
   n = (int) strlen(s);
   for (i=n-1; i >= 0; --i)
      if (!isspace(s[i]))
         break;
   s[i+1] = 0;
   return s;
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 75 段（第 667-673 行）

```c
char *stb_strncpy(char *s, char *t, int n)
{
   strncpy(s,t,n);
   s[n-1] = 0;
   return s;
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 76 段（第 674-684 行）

```c
char *stb_substr(char *t, int n)
{
   char *a;
   int z = (int) strlen(t);
   if (z < n) n = z;
   a = (char *) malloc(n+1);
   strncpy(a,t,n);
   a[n] = 0;
   return a;
}
```

解说：这一段涉及动态内存管理，负责申请、调整或释放运行时数据结构需要的存储空间。

## 第 77 段（第 685-694 行）

```c
char *stb_duplower(char *s)
{
   char *p = strdup(s), *q = p;
   while (*q) {
      *q = tolower(*q);
      ++q;
   }
   return p;
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 78 段（第 695-702 行）

```c
void stb_tolower(char *s)
{
   while (*s) {
      *s = tolower(*s);
      ++s;
   }
}
```

解说：这一段实现或声明库接口 `stb_tolower`，把“tolower”相关能力封装成可被外部或内部复用的函数。

## 第 79 段（第 703-710 行）

```c
char *stb_strchr2(char *s, char x, char y)
{
   for(; *s; ++s)
      if (*s == x || *s == y)
         return s;
   return NULL;
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 80 段（第 711-719 行）

```c
char *stb_strrchr2(char *s, char x, char y)
{
   char *r = NULL;
   for(; *s; ++s)
      if (*s == x || *s == y)
         r = s;
   return r;
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 81 段（第 720-726 行）

```c
char *stb_strichr(char *s, char t)
{
   if (tolower(t) == toupper(t))
      return strchr(s,t);
   return stb_strchr2(s, (char) tolower(t), (char) toupper(t));
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 82 段（第 727-739 行）

```c
char *stb_stristr(char *s, char *t)
{
   size_t n = strlen(t);
   char *z;
   if (n==0) return s;
   while ((z = stb_strichr(s, *t)) != NULL) {
      if (0==stb_strnicmp(z, t, n))
         return z;
      s = z+1;
   }
   return NULL;
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 83 段（第 740-757 行）

```c
static char *stb_strtok_raw(char *output, char *src, char *delimit, int keep, int invert)
{
   if (invert) {
      while (*src && strchr(delimit, *src) != NULL) {
         *output++ = *src++;
      }
   } else {
      while (*src && strchr(delimit, *src) == NULL) {
         *output++ = *src++;
      }
   }
   *output = 0;
   if (keep)
      return src;
   else
      return *src ? src+1 : src;
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 84 段（第 758-762 行）

```c
char *stb_strtok(char *output, char *src, char *delimit)
{
   return stb_strtok_raw(output, src, delimit, 0, 0);
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 85 段（第 763-767 行）

```c
char *stb_strtok_keep(char *output, char *src, char *delimit)
{
   return stb_strtok_raw(output, src, delimit, 1, 0);
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 86 段（第 768-772 行）

```c
char *stb_strtok_invert(char *output, char *src, char *delimit)
{
   return stb_strtok_raw(output, src, delimit, 1,1);
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 87 段（第 773-926 行）

```c
static char **stb_tokens_raw(char *src_, char *delimit, int *count,
                             int stripwhite, int allow_empty, char *start, char *end)
{
   int nested = 0;
   unsigned char *src = (unsigned char *) src_;
   static char stb_tokentable[256]; // rely on static initializion to 0
   static char stable[256],etable[256];
   char *out;
   char **result;
   int num=0;
   unsigned char *s;

   s = (unsigned char *) delimit; while (*s) stb_tokentable[*s++] = 1;
   if (start) {
      s = (unsigned char *) start;         while (*s) stable[*s++] = 1;
      s = (unsigned char *) end;   if (s)  while (*s) stable[*s++] = 1;
      s = (unsigned char *) end;   if (s)  while (*s) etable[*s++] = 1;
   }
   stable[0] = 1;

   // two passes through: the first time, counting how many
   s = (unsigned char *) src;
   while (*s) {
      // state: just found delimiter
      // skip further delimiters
      if (!allow_empty) {
         stb_tokentable[0] = 0;
         while (stb_tokentable[*s])
            ++s;
         if (!*s) break;
      }
      ++num;
      // skip further non-delimiters
      stb_tokentable[0] = 1;
      if (stripwhite == 2) { // quoted strings
         while (!stb_tokentable[*s]) {
            if (*s != '"')
               ++s;
            else {
               ++s;
               if (*s == '"')
                  ++s;   // "" -> ", not start a string
               else {
                  // begin a string
                  while (*s) {
                     if (s[0] == '"') {
                        if (s[1] == '"') s += 2; // "" -> "
                        else { ++s; break; } // terminating "
                     } else
                        ++s;
                  }
               }
            }
         }
      } else 
         while (nested || !stb_tokentable[*s]) {
            if (stable[*s]) {
               if (!*s) break;
               if (end ? etable[*s] : nested)
                  --nested;
               else
                  ++nested;
            }
            ++s;
         }
      if (allow_empty) {
         if (*s) ++s;
      }
   }
   // now num has the actual count... malloc our output structure
   // need space for all the strings: strings won't be any longer than
   // original input, since for every '\0' there's at least one delimiter
   result = (char **) malloc(sizeof(*result) * (num+1) + (s-src+1));
   if (result == NULL) return result;
   out = (char *) (result + (num+1));
   // second pass: copy out the data
   s = (unsigned char *) src;
   num = 0;
   nested = 0;
   while (*s) {
      char *last_nonwhite;
      // state: just found delimiter
      // skip further delimiters
      if (!allow_empty) {
         stb_tokentable[0] = 0;
         if (stripwhite)
            while (stb_tokentable[*s] || isspace(*s))
               ++s;
         else
            while (stb_tokentable[*s])
               ++s;
      } else if (stripwhite) {
         while (isspace(*s)) ++s;
      }
      if (!*s) break;
      // we're past any leading delimiters and whitespace
      result[num] = out;
      ++num;
      // copy non-delimiters
      stb_tokentable[0] = 1;
      last_nonwhite = out-1;
      if (stripwhite == 2) {
         while (!stb_tokentable[*s]) {
            if (*s != '"') {
               if (!isspace(*s)) last_nonwhite = out;
               *out++ = *s++;
            } else {
               ++s;
               if (*s == '"') {
                  if (!isspace(*s)) last_nonwhite = out;
                  *out++ = *s++; // "" -> ", not start string
               } else {
                  // begin a quoted string
                  while (*s) {
                     if (s[0] == '"') {
                        if (s[1] == '"') { *out++ = *s; s += 2; }
                        else { ++s; break; } // terminating "
                     } else
                        *out++ = *s++;
                  }
                  last_nonwhite = out-1; // all in quotes counts as non-white
               }
            }
         }
      } else {
         while (nested || !stb_tokentable[*s]) {
            if (!isspace(*s)) last_nonwhite = out;
            if (stable[*s]) {
               if (!*s) break;
               if (end ? etable[*s] : nested)
                  --nested;
               else
                  ++nested;
            }
            *out++ = *s++;
         }
      }

      if (stripwhite) // rewind to last non-whitespace char
         out = last_nonwhite+1;
      *out++ = '\0';

      if (*s) ++s; // skip delimiter
   }
   s = (unsigned char *) delimit; while (*s) stb_tokentable[*s++] = 0;
   if (start) {
      s = (unsigned char *) start;         while (*s) stable[*s++] = 1;
      s = (unsigned char *) end;   if (s)  while (*s) stable[*s++] = 1;
      s = (unsigned char *) end;   if (s)  while (*s) etable[*s++] = 1;
   }
   if (count != NULL) *count = num;
   result[num] = 0;
   return result;
}
```

解说：这一段实现或声明函数 `while`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 88 段（第 928-932 行）

```c
char **stb_tokens(char *src, char *delimit, int *count)
{
   return stb_tokens_raw(src,delimit,count,0,0,0,0);
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 89 段（第 933-937 行）

```c
char **stb_tokens_nested(char *src, char *delimit, int *count, char *nest_in, char *nest_out)
{
   return stb_tokens_raw(src,delimit,count,0,0,nest_in,nest_out);
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 90 段（第 938-942 行）

```c
char **stb_tokens_nested_empty(char *src, char *delimit, int *count, char *nest_in, char *nest_out)
{
   return stb_tokens_raw(src,delimit,count,0,1,nest_in,nest_out);
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 91 段（第 943-947 行）

```c
char **stb_tokens_allowempty(char *src, char *delimit, int *count)
{
   return stb_tokens_raw(src,delimit,count,0,1,0,0);
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 92 段（第 948-952 行）

```c
char **stb_tokens_stripwhite(char *src, char *delimit, int *count)
{
   return stb_tokens_raw(src,delimit,count,1,1,0,0);
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 93 段（第 953-957 行）

```c
char **stb_tokens_quoted(char *src, char *delimit, int *count)
{
   return stb_tokens_raw(src,delimit,count,2,1,0,0);
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 94 段（第 958-991 行）

```c
char *stb_dupreplace(char *src, char *find, char *replace)
{
   size_t len_find = strlen(find);
   size_t len_replace = strlen(replace);
   int count = 0;

   char *s,*p,*q;

   s = strstr(src, find);
   if (s == NULL) return strdup(src);
   do {
      ++count;
      s = strstr(s + len_find, find);
   } while (s != NULL);

   p = (char *)  malloc(strlen(src) + count * (len_replace - len_find) + 1);
   if (p == NULL) return p;
   q = p;
   s = src;
   for (;;) {
      char *t = strstr(s, find);
      if (t == NULL) {
         strcpy(q,s);
         assert(strlen(p) == strlen(src) + count*(len_replace-len_find));
         return p;
      }
      memcpy(q, s, t-s);
      q += t-s;
      memcpy(q, replace, len_replace);
      q += len_replace;
      s = t + len_find;
   }
}
```

解说：这一段涉及动态内存管理，负责申请、调整或释放运行时数据结构需要的存储空间。

## 第 95 段（第 992-1020 行）

```c
void stb_replaceinplace(char *src, char *find, char *replace)
{
   size_t len_find = strlen(find);
   size_t len_replace = strlen(replace);
   int delta;

   char *s,*p,*q;

   delta = len_replace - len_find;
   assert(delta <= 0);
   if (delta > 0) return;

   p = strstr(src, find);
   if (p == NULL) return;

   s = q = p;
   while (*s) {
      memcpy(q, replace, len_replace);
      p += len_find;
      q += len_replace;
      s = strstr(p, find);
      if (s == NULL) s = p + strlen(p);
      memmove(q, p, s-p);
      q += s-p;
      p = s;
   }
   *q = 0;
}
```

解说：这一段实现或声明库接口 `stb_replaceinplace`，把“replaceinplace”相关能力封装成可被外部或内部复用的函数。

## 第 96 段（第 1021-1027 行）

```c
void stb_fixpath(char *path)
{
   for(; *path; ++path)
      if (*path == '\\')
         *path = '/';
}
```

解说：这一段实现或声明库接口 `stb_fixpath`，把“fixpath”相关能力封装成可被外部或内部复用的函数。

## 第 97 段（第 1028-1039 行）

```c
void stb__add_section(char *buffer, char *data, int curlen, int newlen)
{
   if (newlen < curlen) {
      int z1 = newlen >> 1, z2 = newlen-z1;
      memcpy(buffer, data, z1-1);
      buffer[z1-1] = '.';
      buffer[z1-0] = '.';
      memcpy(buffer+z1+1, data+curlen-z2+1, z2-1);
   } else
      memcpy(buffer, data, curlen);
}
```

解说：这一段实现或声明库接口 `stb__add_section`，把“add section”相关能力封装成可被外部或内部复用的函数。

## 第 98 段（第 1040-1077 行）

```c
char * stb_shorten_path_readable(char *path, int len)
{
   static char buffer[1024];
   int n = strlen(path),n1,n2,r1,r2;
   char *s;
   if (n <= len) return path;
   if (len > 1024) return path;
   s = stb_strrchr2(path, '/', '\\');
   if (s) {
      n1 = s - path + 1;
      n2 = n - n1;
      ++s;
   } else {
      n1 = 0;
      n2 = n;
      s = path;
   }
   // now we need to reduce r1 and r2 so that they fit in len
   if (n1 < len>>1) {
      r1 = n1;
      r2 = len - r1;
   } else if (n2 < len >> 1) {
      r2 = n2;
      r1 = len - r2;
   } else {
      r1 = n1 * len / n;
      r2 = n2 * len / n;
      if (r1 < len>>2) r1 = len>>2, r2 = len-r1;
      if (r2 < len>>2) r2 = len>>2, r1 = len-r2;
   }
   assert(r1 <= n1 && r2 <= n2);
   if (n1)
      stb__add_section(buffer, path, n1, r1);
   stb__add_section(buffer+r1, s, n2, r2);
   buffer[len] = 0;
   return buffer;
}
```

解说：这一段实现或声明库接口 `stb_shorten_path_readable`，把“shorten path readable”相关能力封装成可被外部或内部复用的函数。

## 第 99 段（第 1078-1122 行）

```c
static char *stb__splitpath_raw(char *buffer, char *path, int flag)
{
   int len=0,x,y, n = (int) strlen(path), f1,f2;
   char *s = stb_strrchr2(path, '/', '\\');
   char *t = strrchr(path, '.');
   if (s && t && t < s) t = NULL;
   if (s) ++s;

   if (flag == STB_EXT_NO_PERIOD)
      flag |= STB_EXT;

   if (!(flag & (STB_PATH | STB_FILE | STB_EXT))) return NULL;

   f1 = s == NULL ? 0 : s-path; // start of filename
   f2 = t == NULL ? n : t-path; // just past end of filename

   if (flag & STB_PATH) {
      x = 0; if (f1 == 0 && flag == STB_PATH) len=2;
   } else if (flag & STB_FILE) {
      x = f1;
   } else {
      x = f2;
      if (flag & STB_EXT_NO_PERIOD)
         if (buffer[x] == '.')
            ++x;
   }

   if (flag & STB_EXT)
      y = n;
   else if (flag & STB_FILE)
      y = f2;
   else
      y = f1;

   if (buffer == NULL) {
      buffer = (char *) malloc(y-x + len + 1);
      if (!buffer) return NULL;
   }

   if (len) { strcpy(buffer, "./"); return buffer; }
   strncpy(buffer, path+x, y-x);
   buffer[y-x] = 0;
   return buffer;
}
```

解说：这一段实现或声明函数 `if`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 100 段（第 1123-1127 行）

```c
char *stb_splitpath(char *output, char *src, int flag)
{
   return stb__splitpath_raw(output, src, flag);
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 101 段（第 1128-1132 行）

```c
char *stb_splitpathdup(char *src, int flag)
{
   return stb__splitpath_raw(NULL, src, flag);
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 102 段（第 1133-1143 行）

```c
char *stb_replacedir(char *output, char *src, char *dir)
{
   char buffer[4096];
   stb_splitpath(buffer, src, STB_FILE | STB_EXT);
   if (dir)
      sprintf(output, "%s/%s", dir, buffer);
   else
      strcpy(output, buffer);
   return output;
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 103 段（第 1144-1155 行）

```c
char *stb_replaceext(char *output, char *src, char *ext)
{
   char buffer[4096];
   stb_splitpath(buffer, src, STB_PATH | STB_FILE);
   if (ext)
      sprintf(output, "%s.%s", buffer, ext[0] == '.' ? ext+1 : ext);
   else
      strcpy(output, buffer);
   return output;
}
#endif
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 104 段（第 1156-1191 行）

```c

//////////////////////////////////////////////////////////////////////////////
//
//                                stb_arr
//
//  An stb_arr is directly useable as a pointer (use the actual type in your
//  definition), but when it resizes, it returns a new pointer and you can't
//  use the old one, so you have to be careful to copy-in-out as necessary.
//
//  Use a NULL pointer as a 0-length array.
//
//     float *my_array = NULL, *temp;
//
//     // add elements on the end one at a time
//     stb_arr_push(my_array, 0.0f);
//     stb_arr_push(my_array, 1.0f);
//     stb_arr_push(my_array, 2.0f);
//
//     assert(my_array[1] == 2.0f);
//
//     // add an uninitialized element at the end, then assign it
//     *stb_arr_add(my_array) = 3.0f;
//
//     // add three uninitialized elements at the end
//     temp = stb_arr_addn(my_array,3);
//     temp[0] = 4.0f;
//     temp[1] = 5.0f;
//     temp[2] = 6.0f;
//
//     assert(my_array[5] == 5.0f);
//
//     // remove the last one
//     stb_arr_pop(my_array);
//
//     assert(stb_arr_len(my_array) == 6);
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 105 段（第 1193-1199 行）

```c
#ifndef STB_INCLUDE_STB_LIB_H

// simple functions written on top of other functions
#define stb_arr_empty(a)       (  stb_arr_len(a) == 0 )
#define stb_arr_add(a)         (  stb_arr_addn((a),1) )
#define stb_arr_push(a,v)      ( *stb_arr_add(a)=(v)  )
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 106 段（第 1200-1206 行）

```c
typedef struct
{
   int len, limit;
   unsigned int signature;
   unsigned int padding; // make it a multiple of 16 so preserve alignment mod 16
} stb__arr;
```

解说：这一段声明结构体 `stb__arr`，把相关字段组织成一个数据对象，供后续算法在函数之间传递和维护状态。

## 第 107 段（第 1207-1212 行）

```c
#define stb_arr_signature      0x51bada7b  // ends with 0123 in decimal

// access the header block stored before the data
#define stb_arrhead(a)         /*lint --e(826)*/ (((stb__arr *) (a)) - 1)
#define stb_arrhead2(a)        /*lint --e(826)*/ (((stb__arr *) (a)) - 1)
```

解说：这一段定义宏（如 `stb_arr_signature, stb_arrhead, stb_arrhead2`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 108 段（第 1213-1220 行）

```c
#ifdef STB_DEBUG
#define stb_arr_check(a)       assert(!a || stb_arrhead(a)->signature == stb_arr_signature)
#define stb_arr_check2(a)      assert(!a || stb_arrhead2(a)->signature == stb_arr_signature)
#else
#define stb_arr_check(a)       ((void) 0)
#define stb_arr_check2(a)      ((void) 0)
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 109 段（第 1221-1227 行）

```c
// ARRAY LENGTH

// get the array length; special case if pointer is NULL
#define stb_arr_len(a)         (a ? stb_arrhead(a)->len : 0)
#define stb_arr_len2(a)        ((stb__arr *) (a) ? stb_arrhead2(a)->len : 0)
#define stb_arr_lastn(a)       (stb_arr_len(a)-1)
```

解说：这一段定义宏（如 `stb_arr_len, stb_arr_len2, stb_arr_lastn`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 110 段（第 1228-1235 行）

```c
// check whether a given index is valid -- tests 0 <= i < stb_arr_len(a) 
#define stb_arr_valid(a,i)     (a ? (int) (i) < stb_arrhead(a)->len : 0)

// change the array length so is is exactly N entries long, creating
// uninitialized entries as needed
#define stb_arr_setlen(a,n)  \
            (stb__arr_setlen((void **) &(a), sizeof(a[0]), (n)))
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 111 段（第 1236-1240 行）

```c
// change the array length so that N is a valid index (that is, so
// it is at least N entries long), creating uninitialized entries as needed
#define stb_arr_makevalid(a,n)  \
            (stb_arr_len(a) < (n)+1 ? stb_arr_setlen(a,(n)+1),(a) : (a))
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 112 段（第 1241-1246 行）

```c
// remove the last element of the array, returning it
#define stb_arr_pop(a)         ((stb_arr_check(a), (a))[--stb_arrhead(a)->len])

// access the last element in the array
#define stb_arr_last(a)        ((stb_arr_check(a), (a))[stb_arr_len(a)-1])
```

解说：这一段定义宏（如 `stb_arr_pop, stb_arr_last`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 113 段（第 1247-1252 行）

```c
// is iterator at end of list?
#define stb_arr_end(a,i)       ((i) >= &(a)[stb_arr_len(a)])

// (internal) change the allocated length of the array
#define stb_arr__grow(a,n)     (stb_arr_check(a), stb_arrhead(a)->len += (n))
```

解说：这一段定义宏（如 `stb_arr_end, stb_arr__grow`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 114 段（第 1253-1258 行）

```c
// add N new uninitialized elements to the end of the array
#define stb_arr__addn(a,n)     /*lint --e(826)*/ \
                               ((stb_arr_len(a)+(n) > stb_arrcurmax(a))      \
                                 ? (stb__arr_addlen((void **) &(a),sizeof(*a),(n)),0) \
                                 : ((stb_arr__grow(a,n), 0)))
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 115 段（第 1259-1262 行）

```c
// add N new uninitialized elements to the end of the array, and return
// a pointer to the first new one
#define stb_arr_addn(a,n)      (stb_arr__addn((a),n),(a)+stb_arr_len(a)-(n))
```

解说：这一段定义宏（如 `stb_arr_addn`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 116 段（第 1263-1268 行）

```c
// add N new uninitialized elements starting at index 'i'
#define stb_arr_insertn(a,i,n) (stb__arr_insertn((void **) &(a), sizeof(*a), i, n))

// insert an element at i
#define stb_arr_insert(a,i,v)  (stb__arr_insertn((void **) &(a), sizeof(*a), i, 1), ((a)[i] = v))
```

解说：这一段定义宏（如 `stb_arr_insertn, stb_arr_insert`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 117 段（第 1269-1274 行）

```c
// delete N elements from the middle starting at index 'i'
#define stb_arr_deleten(a,i,n) (stb__arr_deleten((void **) &(a), sizeof(*a), i, n))

// delete the i'th element
#define stb_arr_delete(a,i)   stb_arr_deleten(a,i,1)
```

解说：这一段定义宏（如 `stb_arr_deleten, stb_arr_delete`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 118 段（第 1275-1278 行）

```c
// delete the i'th element, swapping down from the end
#define stb_arr_fastdelete(a,i)  \
   (stb_swap(&a[i], &a[stb_arrhead(a)->len-1], sizeof(*a)), stb_arr_pop(a))
```

解说：这一段定义宏（如 `stb_arr_fastdelete`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 119 段（第 1279-1285 行）

```c

// ARRAY STORAGE

// get the array maximum storage; special case if NULL
#define stb_arrcurmax(a)       (a ? stb_arrhead(a)->limit : 0)
#define stb_arrcurmax2(a)      (a ? stb_arrhead2(a)->limit : 0)
```

解说：这一段定义宏（如 `stb_arrcurmax, stb_arrcurmax2`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 120 段（第 1286-1292 行）

```c
// set the maxlength of the array to n in anticipation of further growth
#define stb_arr_setsize(a,n)   (stb_arr_check(a), stb__arr_setsize((void **) &(a),sizeof((a)[0]),n))

// make sure maxlength is large enough for at least N new allocations
#define stb_arr_atleast(a,n)   (stb_arr_len(a)+(n) > stb_arrcurmax(a)      \
                                 ? stb_arr_setsize((a), (n)) : 0)
```

解说：这一段定义宏（如 `stb_arr_setsize, stb_arr_atleast`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 121 段（第 1293-1298 行）

```c
// make a copy of a given array (copies contents via 'memcpy'!)
#define stb_arr_copy(a)        stb__arr_copy(a, sizeof((a)[0]))

// compute the storage needed to store all the elements of the array
#define stb_arr_storage(a)     (stb_arr_len(a) * sizeof((a)[0]))
```

解说：这一段定义宏（如 `stb_arr_copy, stb_arr_storage`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 122 段（第 1299-1302 行）

```c
#define stb_arr_for(v,arr)     for((v)=(arr); (v) < (arr)+stb_arr_len(arr); ++(v))

// IMPLEMENTATION
```

解说：这一段定义宏（如 `stb_arr_for`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 123 段（第 1303-1310 行）

```c
STB_EXTERN void stb_arr_free_(void **p);
STB_EXTERN void *stb__arr_copy_(void *p, int elem_size);
STB_EXTERN void stb__arr_setsize_(void **p, int size, int limit);
STB_EXTERN void stb__arr_setlen_(void **p, int size, int newlen);
STB_EXTERN void stb__arr_addlen_(void **p, int size, int addlen);
STB_EXTERN void stb__arr_deleten_(void **p, int size, int loc, int n);
STB_EXTERN void stb__arr_insertn_(void **p, int size, int loc, int n);
```

解说：这一段涉及动态内存管理，负责申请、调整或释放运行时数据结构需要的存储空间。

## 第 124 段（第 1311-1312 行）

```c
#define stb_arr_free(p)            stb_arr_free_((void **) &(p))
```

解说：这一段定义宏（如 `stb_arr_free`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 125 段（第 1313-1329 行）

```c
#ifndef STBLIB_MALLOC_WRAPPER // @Todo
  #define stb__arr_setsize         stb__arr_setsize_
  #define stb__arr_setlen          stb__arr_setlen_
  #define stb__arr_addlen          stb__arr_addlen_
  #define stb__arr_deleten         stb__arr_deleten_
  #define stb__arr_insertn         stb__arr_insertn_
  #define stb__arr_copy            stb__arr_copy_
#else
  #define stb__arr_addlen(p,s,n)    stb__arr_addlen_(p,s,n,__FILE__,__LINE__)
  #define stb__arr_setlen(p,s,n)    stb__arr_setlen_(p,s,n,__FILE__,__LINE__)
  #define stb__arr_setsize(p,s,n)   stb__arr_setsize_(p,s,n,__FILE__,__LINE__)
  #define stb__arr_deleten(p,s,i,n) stb__arr_deleten_(p,s,i,n,__FILE__,__LINE__)
  #define stb__arr_insertn(p,s,i,n) stb__arr_insertn_(p,s,i,n,__FILE__,__LINE__)
  #define stb__arr_copy(p,s)        stb__arr_copy_(p,s,__FILE__,__LINE__)
#endif
#endif // STB_INCLUDE_STB_LIB_H
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 126 段（第 1330-1338 行）

```c
#ifdef STB_LIB_IMPLEMENTATION
void stb_arr_malloc(void **target, void *context)
{
   stb__arr *q = (stb__arr *) malloc(sizeof(*q));
   q->len = q->limit = 0;
   q->signature = stb_arr_signature;
   *target = (void *) (q+1);
}
```

解说：这一段实现或声明库接口 `stb_arr_malloc`，把“arr malloc”相关能力封装成可被外部或内部复用的函数。

## 第 127 段（第 1339-1343 行）

```c
static void * stb__arr_malloc(int size)
{
   return malloc(size);
}
```

解说：这一段实现或声明库接口 `stb__arr_malloc`，把“arr malloc”相关能力封装成可被外部或内部复用的函数。

## 第 128 段（第 1344-1353 行）

```c
void * stb__arr_copy_(void *p, int elem_size)
{
   stb__arr *q;
   if (p == NULL) return p;
   q = (stb__arr *) malloc(sizeof(*q) + elem_size * stb_arrhead2(p)->limit);
   stb_arr_check2(p);
   memcpy(q, stb_arrhead2(p), sizeof(*q) + elem_size * stb_arrhead2(p)->len);
   return q+1;
}
```

解说：这一段实现或声明库接口 `stb__arr_copy_`，把“arr copy”相关能力封装成可被外部或内部复用的函数。

## 第 129 段（第 1354-1364 行）

```c
void stb_arr_free_(void **pp)
{
   void *p = *pp;
   stb_arr_check2(p);
   if (p) {
      stb__arr *q = stb_arrhead2(p);
      free(q);
   }
   *pp = NULL;
}
```

解说：这一段实现或声明库接口 `stb_arr_free_`，把“arr free”相关能力封装成可被外部或内部复用的函数。

## 第 130 段（第 1365-1395 行）

```c
static void stb__arrsize_(void **pp, int size, int limit, int len)
{
   void *p = *pp;
   stb__arr *a;
   stb_arr_check2(p);
   if (p == NULL) {
      if (len == 0 && size == 0) return;
      a = (stb__arr *) stb__arr_malloc(sizeof(*a) + size*limit);
      a->limit = limit;
      a->len   = len;
      a->signature = stb_arr_signature;
   } else {
      a = stb_arrhead2(p);
      a->len = len;
      if (a->limit < limit) {
         void *p;
         if (a->limit >= 4 && limit < a->limit * 2)
            limit = a->limit * 2;
         p = realloc(a, sizeof(*a) + limit*size);
         if (p) {
            a = (stb__arr *) p;
            a->limit = limit;
         } else {
            // throw an error!
         }
      }
   }
   a->len = a->len < a->limit ? a->len : a->limit;
   *pp = a+1;
}
```

解说：这一段实现或声明库接口 `stb__arrsize_`，把“arrsize”相关能力封装成可被外部或内部复用的函数。

## 第 131 段（第 1396-1402 行）

```c
void stb__arr_setsize_(void **pp, int size, int limit)
{
   void *p = *pp;
   stb_arr_check2(p);
   stb__arrsize_(pp, size, limit, stb_arr_len2(p));
}
```

解说：这一段实现或声明库接口 `stb__arr_setsize_`，把“arr setsize”相关能力封装成可被外部或内部复用的函数。

## 第 132 段（第 1403-1413 行）

```c
void stb__arr_setlen_(void **pp, int size, int newlen)
{
   void *p = *pp;
   stb_arr_check2(p);
   if (stb_arrcurmax2(p) < newlen || p == NULL) {
      stb__arrsize_(pp, size, newlen, newlen);
   } else {
      stb_arrhead2(p)->len = newlen;
   }
}
```

解说：这一段实现或声明库接口 `stb__arr_setlen_`，把“arr setlen”相关能力封装成可被外部或内部复用的函数。

## 第 133 段（第 1414-1418 行）

```c
void stb__arr_addlen_(void **p, int size, int addlen)
{
   stb__arr_setlen_(p, size, stb_arr_len2(*p) + addlen);
}
```

解说：这一段实现或声明库接口 `stb__arr_addlen_`，把“arr addlen”相关能力封装成可被外部或内部复用的函数。

## 第 134 段（第 1419-1436 行）

```c
void stb__arr_insertn_(void **pp, int size, int i, int n)
{
   void *p = *pp;
   if (n) {
      int z;

      if (p == NULL) {
         stb__arr_addlen_(pp, size, n);
         return;
      }

      z = stb_arr_len2(p);
      stb__arr_addlen_(&p, size, n);
      memmove((char *) p + (i+n)*size, (char *) p + i*size, size * (z-i));
   }
   *pp = p;
}
```

解说：这一段实现或声明库接口 `stb__arr_insertn_`，把“arr insertn”相关能力封装成可被外部或内部复用的函数。

## 第 135 段（第 1437-1447 行）

```c
void stb__arr_deleten_(void **pp, int size, int i, int n)
{
   void *p = *pp;
   if (n) {
      memmove((char *) p + i*size, (char *) p + (i+n)*size, size * (stb_arr_len2(p)-(i+n)));
      stb_arrhead2(p)->len -= n;
   }
   *pp = p;
}
#endif
```

解说：这一段实现或声明库接口 `stb__arr_deleten_`，把“arr deleten”相关能力封装成可被外部或内部复用的函数。

## 第 136 段（第 1448-1470 行）

```c
//////////////////////////////////////////////////////////////////////////////
//
//                               Hashing
//
//      typical use for this is to make a power-of-two hash table.
//
//      let N = size of table (2^n)
//      let H = stb_hash(str)
//      let S = stb_rehash(H) | 1
//
//      then hash probe sequence P(i) for i=0..N-1
//         P(i) = (H + S*i) & (N-1)
//
//      the idea is that H has 32 bits of hash information, but the
//      table has only, say, 2^20 entries so only uses 20 of the bits.
//      then by rehashing the original H we get 2^12 different probe
//      sequences for a given initial probe location. (So it's optimal
//      for 64K tables and its optimality decreases past that.)
//
//      ok, so I've added something that generates _two separate_
//      32-bit hashes simultaneously which should scale better to
//      very large tables.
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 137 段（第 1471-1479 行）

```c
#ifndef STB_INCLUDE_STB_LIB_H
STB_EXTERN unsigned int stb_hash(char *str);
STB_EXTERN unsigned int stb_hashptr(void *p);
STB_EXTERN unsigned int stb_hashlen(char *str, int len);
STB_EXTERN unsigned int stb_rehash_improved(unsigned int v);
STB_EXTERN unsigned int stb_hash_fast(void *p, int len);
STB_EXTERN unsigned int stb_hash2(char *str, unsigned int *hash2_ptr);
STB_EXTERN unsigned int stb_hash_number(unsigned int hash);
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 138 段（第 1480-1482 行）

```c
#define stb_rehash(x)  ((x) + ((x) >> 6) + ((x) >> 19))
#endif // STB_INCLUDE_STB_LIB_H
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 139 段（第 1483-1491 行）

```c
#ifdef STB_LIB_IMPLEMENTATION
unsigned int stb_hash(char *str)
{
   unsigned int hash = 0;
   while (*str)
      hash = (hash << 7) + (hash >> 25) + *str++;
   return hash + (hash >> 16);
}
```

解说：这一段实现或声明库接口 `stb_hash`，把“hash”相关能力封装成可被外部或内部复用的函数。

## 第 140 段（第 1492-1499 行）

```c
unsigned int stb_hashlen(char *str, int len)
{
   unsigned int hash = 0;
   while (len-- > 0 && *str)
      hash = (hash << 7) + (hash >> 25) + *str++;
   return hash + (hash >> 16);
}
```

解说：这一段实现或声明库接口 `stb_hashlen`，把“hashlen”相关能力封装成可被外部或内部复用的函数。

## 第 141 段（第 1500-1516 行）

```c
unsigned int stb_hashptr(void *p)
{
    unsigned int x = (unsigned int)(size_t) p;

   // typically lacking in low bits and high bits
   x = stb_rehash(x);
   x += x << 16;

   // pearson's shuffle
   x ^= x << 3;
   x += x >> 5;
   x ^= x << 2;
   x += x >> 15;
   x ^= x << 10;
   return stb_rehash(x);
}
```

解说：这一段实现或声明库接口 `stb_hashptr`，把“hashptr”相关能力封装成可被外部或内部复用的函数。

## 第 142 段（第 1517-1521 行）

```c
unsigned int stb_rehash_improved(unsigned int v)
{
   return stb_hashptr((void *)(size_t) v);
}
```

解说：这一段实现或声明库接口 `stb_rehash_improved`，把“rehash improved”相关能力封装成可被外部或内部复用的函数。

## 第 143 段（第 1522-1534 行）

```c
unsigned int stb_hash2(char *str, unsigned int *hash2_ptr)
{
   unsigned int hash1 = 0x3141592c;
   unsigned int hash2 = 0x77f044ed;
   while (*str) {
      hash1 = (hash1 << 7) + (hash1 >> 25) + *str;
      hash2 = (hash2 << 11) + (hash2 >> 21) + *str;
      ++str;
   }
   *hash2_ptr = hash2 + (hash1 >> 16);
   return       hash1 + (hash2 >> 16);
}
```

解说：这一段实现或声明库接口 `stb_hash2`，把“hash2”相关能力封装成可被外部或内部复用的函数。

## 第 144 段（第 1535-1542 行）

```c
// Paul Hsieh hash
#define stb__get16_slow(p) ((p)[0] + ((p)[1] << 8))
#if defined(_MSC_VER)
   #define stb__get16(p) (*((unsigned short *) (p)))
#else
   #define stb__get16(p) stb__get16_slow(p)
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 145 段（第 1543-1599 行）

```c
unsigned int stb_hash_fast(void *p, int len)
{
   unsigned char *q = (unsigned char *) p;
   unsigned int hash = len;

   if (len <= 0 || q == NULL) return 0;

   /* Main loop */
    if (((int)(size_t) q & 1) == 0) {
      for (;len > 3; len -= 4) {
         unsigned int val;
         hash +=  stb__get16(q);
         val   = (stb__get16(q+2) << 11);
         hash  = (hash << 16) ^ hash ^ val;
         q    += 4;
         hash += hash >> 11;
      }
   } else {
      for (;len > 3; len -= 4) {
         unsigned int val;
         hash +=  stb__get16_slow(q);
         val   = (stb__get16_slow(q+2) << 11);
         hash  = (hash << 16) ^ hash ^ val;
         q    += 4;
         hash += hash >> 11;
      }
   }

   /* Handle end cases */
   switch (len) {
      case 3: hash += stb__get16_slow(q);
              hash ^= hash << 16;
              hash ^= q[2] << 18;
              hash += hash >> 11;
              break;
      case 2: hash += stb__get16_slow(q);
              hash ^= hash << 11;
              hash += hash >> 17;
              break;
      case 1: hash += q[0];
              hash ^= hash << 10;
              hash += hash >> 1;
              break;
      case 0: break;
   }

   /* Force "avalanching" of final 127 bits */
   hash ^= hash << 3;
   hash += hash >> 5;
   hash ^= hash << 4;
   hash += hash >> 17;
   hash ^= hash << 25;
   hash += hash >> 6;

   return hash;
}
```

解说：这一段实现或声明库接口 `stb_hash_fast`，把“hash fast”相关能力封装成可被外部或内部复用的函数。

## 第 146 段（第 1600-1611 行）

```c
unsigned int stb_hash_number(unsigned int hash)
{
   hash ^= hash << 3;
   hash += hash >> 5;
   hash ^= hash << 4;
   hash += hash >> 17;
   hash ^= hash << 25;
   hash += hash >> 6;
   return hash;
}
#endif
```

解说：这一段实现或声明库接口 `stb_hash_number`，把“hash number”相关能力封装成可被外部或内部复用的函数。

## 第 147 段（第 1612-1634 行）

```c
//////////////////////////////////////////////////////////////////////////////
//
//                     Instantiated data structures
//
// This is an attempt to implement a templated data structure.
//
// Hash table: call stb_define_hash(TYPE,N,KEY,K1,K2,HASH,VALUE)
//     TYPE     -- will define a structure type containing the hash table
//     N        -- the name, will prefix functions named:
//                        N create
//                        N destroy
//                        N get
//                        N set, N add, N update,
//                        N remove
//     KEY      -- the type of the key. 'x == y' must be valid
//       K1,K2  -- keys never used by the app, used as flags in the hashtable
//       HASH   -- a piece of code ending with 'return' that hashes key 'k'
//     VALUE    -- the type of the value. 'x = y' must be valid
//
//  Note that stb_define_hash_base can be used to define more sophisticated
//  hash tables, e.g. those that make copies of the key or use special
//  comparisons (e.g. strcmp).
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 148 段（第 1635-1639 行）

```c
#define STB_(prefix,name)     stb__##prefix##name
#define STB__(prefix,name)    prefix##name
#define STB__use(x)           x
#define STB__skip(x)
```

解说：这一段定义宏（如 `STB_, STB__, STB__use, STB__skip`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 149 段（第 1640-1653 行）

```c
#define stb_declare_hash(PREFIX,TYPE,N,KEY,VALUE) \
   typedef struct stb__st_##TYPE TYPE;\
   PREFIX int STB__(N, init)(TYPE *h, int count);\
   PREFIX int STB__(N, memory_usage)(TYPE *h);\
   PREFIX TYPE * STB__(N, create)(void);\
   PREFIX TYPE * STB__(N, copy)(TYPE *h);\
   PREFIX void STB__(N, destroy)(TYPE *h);\
   PREFIX int STB__(N,get_flag)(TYPE *a, KEY k, VALUE *v);\
   PREFIX VALUE STB__(N,get)(TYPE *a, KEY k);\
   PREFIX int STB__(N, set)(TYPE *a, KEY k, VALUE v);\
   PREFIX int STB__(N, add)(TYPE *a, KEY k, VALUE v);\
   PREFIX int STB__(N, update)(TYPE*a,KEY k,VALUE v);\
   PREFIX int STB__(N, remove)(TYPE *a, KEY k, VALUE *v);
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 150 段（第 1654-1662 行）

```c
#define STB_nocopy(x)        (x)
#define STB_nodelete(x)      0
#define STB_nofields         
#define STB_nonullvalue(x)
#define STB_nullvalue(x)     x
#define STB_safecompare(x)   x
#define STB_nosafe(x)
#define STB_noprefix
```

解说：这一段定义宏（如 `STB_nocopy, STB_nodelete, STB_nofields, STB_nonullvalue, STB_nullvalue`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 151 段（第 1663-1668 行）

```c
#ifdef __GNUC__
#define STB__nogcc(x)
#else
#define STB__nogcc(x)  x
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 152 段（第 1669-1749 行）

```c
#define stb_define_hash_base(PREFIX,TYPE,FIELDS,N,NC,LOAD_FACTOR,             \
                             KEY,EMPTY,DEL,COPY,DISPOSE,SAFE,                 \
                             VCOMPARE,CCOMPARE,HASH,                          \
                             VALUE,HASVNULL,VNULL)                            \
                                                                              \
typedef struct                                                                \
{                                                                             \
   KEY   k;                                                                   \
   VALUE v;                                                                   \
} STB_(N,_hashpair);                                                          \
                                                                              \
STB__nogcc( typedef struct stb__st_##TYPE TYPE;  )                            \
struct stb__st_##TYPE {                                                       \
   FIELDS                                                                     \
   STB_(N,_hashpair) *table;                                                  \
   unsigned int mask;                                                         \
   int count, limit;                                                          \
   int deleted;                                                               \
                                                                              \
   int delete_threshhold;                                                     \
   int grow_threshhold;                                                       \
   int shrink_threshhold;                                                     \
   unsigned char alloced, has_empty, has_del;                                 \
   VALUE ev; VALUE dv;                                                        \
};                                                                            \
                                                                              \
static unsigned int STB_(N, hash)(KEY k)                                      \
{                                                                             \
   HASH                                                                       \
}                                                                             \
                                                                              \
PREFIX int STB__(N, init)(TYPE *h, int count)                                        \
{                                                                             \
   int i;                                                                     \
   if (count < 4) count = 4;                                                  \
   h->limit = count;                                                          \
   h->count = 0;                                                              \
   h->mask  = count-1;                                                        \
   h->deleted = 0;                                                            \
   h->grow_threshhold = (int) (count * LOAD_FACTOR);                          \
   h->has_empty = h->has_del = 0;                                             \
   h->alloced = 0;                                                            \
   if (count <= 64)                                                           \
      h->shrink_threshhold = 0;                                               \
   else                                                                       \
      h->shrink_threshhold = (int) (count * (LOAD_FACTOR/2.25));              \
   h->delete_threshhold = (int) (count * (1-LOAD_FACTOR)/2);                  \
   h->table = (STB_(N,_hashpair)*) malloc(sizeof(h->table[0]) * count);       \
   if (h->table == NULL) return 0;                                            \
   /* ideally this gets turned into a memset32 automatically */               \
   for (i=0; i < count; ++i)                                                  \
      h->table[i].k = EMPTY;                                                  \
   return 1;                                                                  \
}                                                                             \
                                                                              \
PREFIX int STB__(N, memory_usage)(TYPE *h)                                           \
{                                                                             \
   return sizeof(*h) + h->limit * sizeof(h->table[0]);                        \
}                                                                             \
                                                                              \
PREFIX TYPE * STB__(N, create)(void)                                                 \
{                                                                             \
   TYPE *h = (TYPE *) malloc(sizeof(*h));                                     \
   if (h) {                                                                   \
      if (STB__(N, init)(h, 16))                                              \
         h->alloced = 1;                                                      \
      else { free(h); h=NULL; }                                               \
   }                                                                          \
   return h;                                                                  \
}                                                                             \
                                                                              \
PREFIX void STB__(N, destroy)(TYPE *a)                                               \
{                                                                             \
   int i;                                                                     \
   for (i=0; i < a->limit; ++i)                                               \
      if (!CCOMPARE(a->table[i].k,EMPTY) && !CCOMPARE(a->table[i].k, DEL))    \
         DISPOSE(a->table[i].k);                                              \
   free(a->table);                                                            \
   if (a->alloced)                                                            \
      free(a);                                                                \
}                                                                             \
```

解说：这一段涉及动态内存管理，负责申请、调整或释放运行时数据结构需要的存储空间。

## 第 153 段（第 1750-1845 行）

```c
                                                                              \
static void STB_(N, rehash)(TYPE *a, int count);                              \
                                                                              \
PREFIX int STB__(N,get_flag)(TYPE *a, KEY k, VALUE *v)                               \
{                                                                             \
   unsigned int h = STB_(N, hash)(k);                                         \
   unsigned int n = h & a->mask, s;                                           \
   if (CCOMPARE(k,EMPTY)){ if (a->has_empty) *v = a->ev; return a->has_empty;}\
   if (CCOMPARE(k,DEL)) { if (a->has_del  ) *v = a->dv; return a->has_del;   }\
   if (CCOMPARE(a->table[n].k,EMPTY)) return 0;                               \
   SAFE(if (!CCOMPARE(a->table[n].k,DEL)))                                    \
   if (VCOMPARE(a->table[n].k,k)) { *v = a->table[n].v; return 1; }            \
   s = stb_rehash(h) | 1;                                                     \
   for(;;) {                                                                  \
      n = (n + s) & a->mask;                                                  \
      if (CCOMPARE(a->table[n].k,EMPTY)) return 0;                            \
      SAFE(if (CCOMPARE(a->table[n].k,DEL)) continue;)                        \
      if (VCOMPARE(a->table[n].k,k))                                           \
         { *v = a->table[n].v; return 1; }                                    \
   }                                                                          \
}                                                                             \
                                                                              \
HASVNULL(                                                                     \
   PREFIX VALUE STB__(N,get)(TYPE *a, KEY k)                                         \
   {                                                                          \
      VALUE v;                                                                \
      if (STB__(N,get_flag)(a,k,&v)) return v;                                \
      else                           return VNULL;                            \
   }                                                                          \
)                                                                             \
                                                                              \
PREFIX int STB__(N,getkey)(TYPE *a, KEY k, KEY *kout)                                \
{                                                                             \
   unsigned int h = STB_(N, hash)(k);                                         \
   unsigned int n = h & a->mask, s;                                           \
   if (CCOMPARE(k,EMPTY)||CCOMPARE(k,DEL)) return 0;                          \
   if (CCOMPARE(a->table[n].k,EMPTY)) return 0;                               \
   SAFE(if (!CCOMPARE(a->table[n].k,DEL)))                                    \
   if (VCOMPARE(a->table[n].k,k)) { *kout = a->table[n].k; return 1; }         \
   s = stb_rehash(h) | 1;                                                     \
   for(;;) {                                                                  \
      n = (n + s) & a->mask;                                                  \
      if (CCOMPARE(a->table[n].k,EMPTY)) return 0;                            \
      SAFE(if (CCOMPARE(a->table[n].k,DEL)) continue;)                        \
      if (VCOMPARE(a->table[n].k,k))                                          \
         { *kout = a->table[n].k; return 1; }                                 \
   }                                                                          \
}                                                                             \
                                                                              \
static int STB_(N,addset)(TYPE *a, KEY k, VALUE v,                            \
                             int allow_new, int allow_old, int copy)          \
{                                                                             \
   unsigned int h = STB_(N, hash)(k);                                         \
   unsigned int n = h & a->mask;                                              \
   int b = -1;                                                                \
   if (CCOMPARE(k,EMPTY)) {                                                   \
      if (a->has_empty ? allow_old : allow_new) {                             \
          n=a->has_empty; a->ev = v; a->has_empty = 1; return !n;             \
      } else return 0;                                                        \
   }                                                                          \
   if (CCOMPARE(k,DEL)) {                                                     \
      if (a->has_del ? allow_old : allow_new) {                               \
          n=a->has_del; a->dv = v; a->has_del = 1; return !n;                 \
      } else return 0;                                                        \
   }                                                                          \
   if (!CCOMPARE(a->table[n].k, EMPTY)) {                                     \
      unsigned int s;                                                         \
      if (CCOMPARE(a->table[n].k, DEL))                                       \
         b = n;                                                               \
      else if (VCOMPARE(a->table[n].k,k)) {                                   \
         if (allow_old)                                                       \
            a->table[n].v = v;                                                \
         return !allow_new;                                                   \
      }                                                                       \
      s = stb_rehash(h) | 1;                                                  \
      for(;;) {                                                               \
         n = (n + s) & a->mask;                                               \
         if (CCOMPARE(a->table[n].k, EMPTY)) break;                           \
         if (CCOMPARE(a->table[n].k, DEL)) {                                  \
            if (b < 0) b = n;                                                 \
         } else if (VCOMPARE(a->table[n].k,k)) {                              \
            if (allow_old)                                                    \
               a->table[n].v = v;                                             \
            return !allow_new;                                                \
         }                                                                    \
      }                                                                       \
   }                                                                          \
   if (!allow_new) return 0;                                                  \
   if (b < 0) b = n; else --a->deleted;                                       \
   a->table[b].k = copy ? COPY(k) : k;                                        \
   a->table[b].v = v;                                                         \
   ++a->count;                                                                \
   if (a->count > a->grow_threshhold)                                         \
      STB_(N,rehash)(a, a->limit*2);                                          \
   return 1;                                                                  \
}                                                                             \
```

解说：这一段实现或声明函数 `if`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 154 段（第 1846-1916 行）

```c
                                                                              \
PREFIX int STB__(N, set)(TYPE *a, KEY k, VALUE v){return STB_(N,addset)(a,k,v,1,1,1);}\
PREFIX int STB__(N, add)(TYPE *a, KEY k, VALUE v){return STB_(N,addset)(a,k,v,1,0,1);}\
PREFIX int STB__(N, update)(TYPE*a,KEY k,VALUE v){return STB_(N,addset)(a,k,v,0,1,1);}\
                                                                              \
PREFIX int STB__(N, remove)(TYPE *a, KEY k, VALUE *v)                                \
{                                                                             \
   unsigned int h = STB_(N, hash)(k);                                         \
   unsigned int n = h & a->mask, s;                                           \
   if (CCOMPARE(k,EMPTY)) { if (a->has_empty) { if(v)*v = a->ev; a->has_empty=0; return 1; } return 0; } \
   if (CCOMPARE(k,DEL))   { if (a->has_del  ) { if(v)*v = a->dv; a->has_del  =0; return 1; } return 0; } \
   if (CCOMPARE(a->table[n].k,EMPTY)) return 0;                               \
   if (SAFE(CCOMPARE(a->table[n].k,DEL) || ) !VCOMPARE(a->table[n].k,k)) {     \
      s = stb_rehash(h) | 1;                                                  \
      for(;;) {                                                               \
         n = (n + s) & a->mask;                                               \
         if (CCOMPARE(a->table[n].k,EMPTY)) return 0;                         \
         SAFE(if (CCOMPARE(a->table[n].k, DEL)) continue;)                    \
         if (VCOMPARE(a->table[n].k,k)) break;                                 \
      }                                                                       \
   }                                                                          \
   DISPOSE(a->table[n].k);                                                    \
   a->table[n].k = DEL;                                                       \
   --a->count;                                                                \
   ++a->deleted;                                                              \
   if (v != NULL)                                                             \
      *v = a->table[n].v;                                                     \
   if (a->count < a->shrink_threshhold)                                       \
      STB_(N, rehash)(a, a->limit >> 1);                                      \
   else if (a->deleted > a->delete_threshhold)                                \
      STB_(N, rehash)(a, a->limit);                                           \
   return 1;                                                                  \
}                                                                             \
                                                                              \
PREFIX TYPE * STB__(NC, copy)(TYPE *a)                                        \
{                                                                             \
   int i;                                                                     \
   TYPE *h = (TYPE *) malloc(sizeof(*h));                                     \
   if (!h) return NULL;                                                       \
   if (!STB__(N, init)(h, a->limit)) { free(h); return NULL; }                \
   h->count = a->count;                                                       \
   h->deleted = a->deleted;                                                   \
   h->alloced = 1;                                                            \
   h->ev = a->ev; h->dv = a->dv;                                              \
   h->has_empty = a->has_empty; h->has_del = a->has_del;                      \
   memcpy(h->table, a->table, h->limit * sizeof(h->table[0]));                \
   for (i=0; i < a->limit; ++i)                                               \
      if (!CCOMPARE(h->table[i].k,EMPTY) && !CCOMPARE(h->table[i].k,DEL))     \
         h->table[i].k = COPY(h->table[i].k);                                 \
   return h;                                                                  \
}                                                                             \
                                                                              \
static void STB_(N, rehash)(TYPE *a, int count)                               \
{                                                                             \
   int i;                                                                     \
   TYPE b;                                                                    \
   STB__(N, init)(&b, count);                                                 \
   for (i=0; i < a->limit; ++i)                                               \
      if (!CCOMPARE(a->table[i].k,EMPTY) && !CCOMPARE(a->table[i].k,DEL))     \
         STB_(N,addset)(&b, a->table[i].k, a->table[i].v,1,1,0);              \
   free(a->table);                                                            \
   a->table = b.table;                                                        \
   a->mask = b.mask;                                                          \
   a->count = b.count;                                                        \
   a->limit = b.limit;                                                        \
   a->deleted = b.deleted;                                                    \
   a->delete_threshhold = b.delete_threshhold;                                \
   a->grow_threshhold = b.grow_threshhold;                                    \
   a->shrink_threshhold = b.shrink_threshhold;                                \
}
```

解说：这一段实现或声明库接口 `STB__`，把“STB__”相关能力封装成可被外部或内部复用的函数。

## 第 155 段（第 1917-1918 行）

```c
#define STB_equal(a,b)  ((a) == (b))
```

解说：这一段定义宏（如 `STB_equal`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 156 段（第 1919-1924 行）

```c
#define stb_define_hash(TYPE,N,KEY,EMPTY,DEL,HASH,VALUE)                      \
   stb_define_hash_base(STB_noprefix, TYPE,STB_nofields,N,NC,0.85f,              \
              KEY,EMPTY,DEL,STB_nocopy,STB_nodelete,STB_nosafe,               \
              STB_equal,STB_equal,HASH,                                       \
              VALUE,STB_nonullvalue,0)
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 157 段（第 1925-1930 行）

```c
#define stb_define_hash_vnull(TYPE,N,KEY,EMPTY,DEL,HASH,VALUE,VNULL)          \
   stb_define_hash_base(STB_noprefix, TYPE,STB_nofields,N,NC,0.85f,              \
              KEY,EMPTY,DEL,STB_nocopy,STB_nodelete,STB_nosafe,               \
              STB_equal,STB_equal,HASH,                                       \
              VALUE,STB_nullvalue,VNULL)
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 158 段（第 1931-1938 行）

```c
//////////////////////////////////////////////////////////////////////////////
//
//                        stb_ptrmap
//
// An stb_ptrmap data structure is an O(1) hash table between pointers. One
// application is to let you store "extra" data associated with pointers,
// which is why it was originally called stb_extra.
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 159 段（第 1939-1942 行）

```c
#ifndef STB_INCLUDE_STB_LIB_H
stb_declare_hash(STB_EXTERN, stb_ptrmap, stb_ptrmap_, void *, void *)
stb_declare_hash(STB_EXTERN, stb_idict, stb_idict_, stb_int32, stb_int32)
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 160 段（第 1943-1945 行）

```c
STB_EXTERN void        stb_ptrmap_delete(stb_ptrmap *e, void (*free_func)(void *));
STB_EXTERN stb_ptrmap *stb_ptrmap_new(void);
```

解说：这一段涉及动态内存管理，负责申请、调整或释放运行时数据结构需要的存储空间。

## 第 161 段（第 1946-1949 行）

```c
STB_EXTERN stb_idict * stb_idict_new_size(unsigned int size);
STB_EXTERN void        stb_idict_remove_all(stb_idict *e);
#endif // STB_INCLUDE_STB_LIB_H
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 162 段（第 1950-1951 行）

```c
#ifdef STB_LIB_IMPLEMENTATION
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 163 段（第 1952-1954 行）

```c
#define STB_EMPTY ((void *) 2)
#define STB_EDEL  ((void *) 6)
```

解说：这一段定义宏（如 `STB_EMPTY, STB_EDEL`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 164 段（第 1955-1959 行）

```c
stb_define_hash_base(STB_noprefix,stb_ptrmap, STB_nofields, stb_ptrmap_,stb_ptrmap_,0.85f,
              void *,STB_EMPTY,STB_EDEL,STB_nocopy,STB_nodelete,STB_nosafe,
              STB_equal,STB_equal,return stb_hashptr(k);,
              void *,STB_nullvalue,NULL)
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 165 段（第 1960-1964 行）

```c
stb_ptrmap *stb_ptrmap_new(void)
{
   return stb_ptrmap_create();
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 166 段（第 1965-1978 行）

```c
void stb_ptrmap_delete(stb_ptrmap *e, void (*free_func)(void *))
{
   int i;
   if (free_func)
      for (i=0; i < e->limit; ++i)
         if (e->table[i].k != STB_EMPTY && e->table[i].k != STB_EDEL) {
            if (free_func == free)
               free(e->table[i].v); // allow STB_MALLOC_WRAPPER to operate
            else
               free_func(e->table[i].v);
         }
   stb_ptrmap_destroy(e);
}
```

解说：这一段实现或声明库接口 `stb_ptrmap_delete`，把“ptrmap delete”相关能力封装成可被外部或内部复用的函数。

## 第 167 段（第 1979-1986 行）

```c
// extra fields needed for stua_dict
#define STB_IEMPTY  ((int) 1)
#define STB_IDEL    ((int) 3)
stb_define_hash_base(STB_noprefix, stb_idict, STB_nofields, stb_idict_,stb_idict_,0.85f,
              stb_int32,STB_IEMPTY,STB_IDEL,STB_nocopy,STB_nodelete,STB_nosafe,
              STB_equal,STB_equal,
              return stb_rehash_improved(k);,stb_int32,STB_nonullvalue,0)
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 168 段（第 1987-1999 行）

```c
stb_idict * stb_idict_new_size(unsigned int size)
{
   stb_idict *e = (stb_idict *) malloc(sizeof(*e));
   if (e) {
      // round up to power of 2
      while ((size & (size-1)) != 0) // while more than 1 bit is set
         size += (size & ~(size-1)); // add the lowest set bit
      stb_idict_init(e, size);
      e->alloced = 1;
   }
   return e;
}
```

解说：这一段实现或声明库接口 `stb_idict_new_size`，把“idict new size”相关能力封装成可被外部或内部复用的函数。

## 第 169 段（第 2000-2008 行）

```c
void stb_idict_remove_all(stb_idict *e)
{
   int n;
   for (n=0; n < e->limit; ++n)
      e->table[n].k = STB_IEMPTY;
   e->has_empty = e->has_del = 0;
}
#endif
```

解说：这一段实现或声明库接口 `stb_idict_remove_all`，把“idict remove all”相关能力封装成可被外部或内部复用的函数。

## 第 170 段（第 2009-2017 行）

```c
//////////////////////////////////////////////////////////////////////////////
//
//                  SDICT: Hash Table for Strings (symbol table)
//
//           if "use_arena=1", then strings will be copied
//           into blocks and never freed until the sdict is freed;
//           otherwise they're malloc()ed and free()d on the fly. 
//           (specify use_arena=1 if you never stb_sdict_remove)
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 171 段（第 2018-2020 行）

```c
#ifndef STB_INCLUDE_STB_LIB_H
stb_declare_hash(STB_EXTERN, stb_sdict, stb_sdict_, char *, void *)
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 172 段（第 2021-2026 行）

```c
STB_EXTERN stb_sdict * stb_sdict_new(void);
STB_EXTERN stb_sdict * stb_sdict_copy(stb_sdict*); 
STB_EXTERN void        stb_sdict_delete(stb_sdict *);
STB_EXTERN void *      stb_sdict_change(stb_sdict *, char *str, void *p);
STB_EXTERN int         stb_sdict_count(stb_sdict *d);
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 173 段（第 2027-2030 行）

```c
STB_EXTERN int         stb_sdict_internal_limit(stb_sdict *d);
STB_EXTERN char *      stb_sdict_internal_key(stb_sdict *d, int n);
STB_EXTERN void *      stb_sdict_internal_value(stb_sdict *d, int n);
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 174 段（第 2031-2035 行）

```c
#define stb_sdict_for(d,i,q,z)                                          \
   for(i=0; i < stb_sdict_internal_limit(d) ? (q=stb_sdict_internal_key(d,i),z=stb_sdict_internal_value(d,i),1) : 0; ++i)    \
      if (q==NULL||q==(void *) 1);else   // reversed makes macro friendly
#endif // STB_INCLUDE_STB_LIB_H
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 175 段（第 2036-2043 行）

```c
#ifdef STB_LIB_IMPLEMENTATION

// if in same translation unit, for speed, don't call accessors
#undef stb_sdict_for
#define stb_sdict_for(d,i,q,z)                                          \
   for(i=0; i < (d)->limit ? (q=(d)->table[i].k,z=(d)->table[i].v,1) : 0; ++i)    \
      if (q==NULL||q==(void *) 1);else   // reversed makes macro friendly
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 176 段（第 2044-2046 行）

```c
//#define STB_DEL ((void *) 1)
#define STB_SDEL  ((char *) 1)
```

解说：这一段定义宏（如 `STB_SDEL`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 177 段（第 2047-2051 行）

```c
stb_define_hash_base(STB_noprefix, stb_sdict, STB_nofields, stb_sdict_,stb_sdictinternal_, 0.85f,
        char *, NULL, STB_SDEL, strdup, free,
                        STB_safecompare, !strcmp, STB_equal, return stb_hash(k);,
        void *, STB_nullvalue, NULL)
```

解说：这一段涉及动态内存管理，负责申请、调整或释放运行时数据结构需要的存储空间。

## 第 178 段（第 2052-2056 行）

```c
int stb_sdict_count(stb_sdict *a)
{
   return a->count;
}
```

解说：这一段实现或声明库接口 `stb_sdict_count`，把“sdict count”相关能力封装成可被外部或内部复用的函数。

## 第 179 段（第 2057-2069 行）

```c
int stb_sdict_internal_limit(stb_sdict *a)
{
   return a->limit;
}
char* stb_sdict_internal_key(stb_sdict *a, int n)
{
   return a->table[n].k;
}
void* stb_sdict_internal_value(stb_sdict *a, int n)
{
   return a->table[n].v;
}
```

解说：这一段实现或声明库接口 `stb_sdict_internal_limit`，把“sdict internal limit”相关能力封装成可被外部或内部复用的函数。

## 第 180 段（第 2070-2076 行）

```c
stb_sdict * stb_sdict_new(void)
{
   stb_sdict *d = stb_sdict_create();
   if (d == NULL) return NULL;
   return d;
}
```

解说：这一段实现或声明库接口 `stb_sdict_new`，把“sdict new”相关能力封装成可被外部或内部复用的函数。

## 第 181 段（第 2077-2081 行）

```c
stb_sdict* stb_sdict_copy(stb_sdict *old)
{
   return stb_sdictinternal_copy(old);
}
```

解说：这一段实现或声明库接口 `stb_sdict_copy`，把“sdict copy”相关能力封装成可被外部或内部复用的函数。

## 第 182 段（第 2082-2086 行）

```c
void stb_sdict_delete(stb_sdict *d)
{
   stb_sdict_destroy(d);
}
```

解说：这一段实现或声明库接口 `stb_sdict_delete`，把“sdict delete”相关能力封装成可被外部或内部复用的函数。

## 第 183 段（第 2087-2094 行）

```c
void * stb_sdict_change(stb_sdict *d, char *str, void *p)
{
   void *q = stb_sdict_get(d, str);
   stb_sdict_set(d, str, p);
   return q;
}
#endif
```

解说：这一段实现或声明库接口 `stb_sdict_change`，把“sdict change”相关能力封装成可被外部或内部复用的函数。

## 第 184 段（第 2095-2099 行）

```c
//////////////////////////////////////////////////////////////////////////////
//
//                             File Processing
//
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 185 段（第 2100-2108 行）

```c
#ifndef STB_INCLUDE_STB_LIB_H
#ifdef _MSC_VER
  #define stb_rename(x,y)   _wrename((const wchar_t *)stb__from_utf8(x), (const wchar_t *)stb__from_utf8_alt(y))
  #define stb_mktemp   _mktemp
#else
  #define stb_mktemp   mktemp
  #define stb_rename   rename
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 186 段（第 2109-2123 行）

```c
#define stb_filec    (char *) stb_file
#define stb_fileu    (unsigned char *) stb_file
STB_EXTERN void *  stb_file(char *filename, size_t *length);
STB_EXTERN size_t  stb_filelen(FILE *f);
STB_EXTERN int     stb_filewrite(char *filename, void *data, size_t length);
STB_EXTERN int     stb_filewritestr(char *filename, char *data);
STB_EXTERN char ** stb_stringfile(char *filename, int *len);
STB_EXTERN char *  stb_fgets(char *buffer, int buflen, FILE *f);
STB_EXTERN char *  stb_fgets_malloc(FILE *f);
STB_EXTERN int     stb_fexists(char *filename);
STB_EXTERN int     stb_fcmp(char *s1, char *s2);
STB_EXTERN int     stb_feq(char *s1, char *s2);
STB_EXTERN time_t  stb_ftimestamp(char *filename);
STB_EXTERN int     stb_fullpath(char *abs, int abs_size, char *rel);
```

解说：这一段涉及动态内存管理，负责申请、调整或释放运行时数据结构需要的存储空间。

## 第 187 段（第 2124-2128 行）

```c
STB_EXTERN int     stb_copyfile(char *src, char *dest);
STB_EXTERN int     stb_fread(void *data, size_t len, size_t count, void *f);
STB_EXTERN int     stb_fwrite(void *data, size_t len, size_t count, void *f);
#endif // STB_INCLUDE_STB_LIB_H
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 188 段（第 2129-2135 行）

```c
#ifdef STB_LIB_IMPLEMENTATION
#if defined(_MSC_VER) || defined(__MINGW32__)
   #define stb__stat   _stat
#else
   #define stb__stat   stat
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 189 段（第 2136-2144 行）

```c
int stb_fexists(char *filename)
{
   struct stb__stat buf;
   return stb__windows(
             _wstat((const wchar_t *)stb__from_utf8(filename), &buf),
               stat(filename,&buf)
          ) == 0;
}
```

解说：这一段实现或声明库接口 `stb_fexists`，把“fexists”相关能力封装成可被外部或内部复用的函数。

## 第 190 段（第 2145-2158 行）

```c
time_t stb_ftimestamp(char *filename)
{
   struct stb__stat buf;
   if (stb__windows(
             _wstat((const wchar_t *)stb__from_utf8(filename), &buf),
               stat(filename,&buf)
          ) == 0)
   {
      return buf.st_mtime;
   } else {
      return 0;
   }
}
```

解说：这一段实现或声明库接口 `stb_ftimestamp`，把“ftimestamp”相关能力封装成可被外部或内部复用的函数。

## 第 191 段（第 2159-2168 行）

```c
size_t  stb_filelen(FILE *f)
{
   size_t len, pos;
   pos = ftell(f);
   fseek(f, 0, SEEK_END);
   len = ftell(f);
   fseek(f, pos, SEEK_SET);
   return len;
}
```

解说：这一段实现或声明库接口 `stb_filelen`，把“filelen”相关能力封装成可被外部或内部复用的函数。

## 第 192 段（第 2169-2188 行）

```c
void *stb_file(char *filename, size_t *length)
{
   FILE *f = stb__fopen(filename, "rb");
   char *buffer;
   size_t len, len2;
   if (!f) return NULL;
   len = stb_filelen(f);
   buffer = (char *) malloc(len+2); // nul + extra
   len2 = fread(buffer, 1, len, f);
   if (len2 == len) {
      if (length) *length = len;
      buffer[len] = 0;
   } else {
      free(buffer);
      buffer = NULL;
   }
   fclose(f);
   return buffer;
}
```

解说：这一段涉及动态内存管理，负责申请、调整或释放运行时数据结构需要的存储空间。

## 第 193 段（第 2189-2209 行）

```c
int stb_filewrite(char *filename, void *data, size_t length)
{
   FILE *f = stb__fopen(filename, "wb");
   if (f) {
      unsigned char *data_ptr = (unsigned char *) data;
      size_t remaining = length;
      while (remaining > 0) {
         size_t len2 = remaining > 65536 ? 65536 : remaining;
         size_t len3 = fwrite(data_ptr, 1, len2, f);
         if (len2 != len3) {
            fprintf(stderr, "Failed while writing %s\n", filename);
            break;
         }
         remaining -= len2;
         data_ptr += len2;
      }
      fclose(f);
   }
   return f != NULL;
}
```

解说：这一段实现或声明库接口 `stb_filewrite`，把“filewrite”相关能力封装成可被外部或内部复用的函数。

## 第 194 段（第 2210-2214 行）

```c
int stb_filewritestr(char *filename, char *data)
{
   return stb_filewrite(filename, data, strlen(data));
}
```

解说：这一段实现或声明库接口 `stb_filewritestr`，把“filewritestr”相关能力封装成可被外部或内部复用的函数。

## 第 195 段（第 2215-2260 行）

```c
char ** stb_stringfile(char *filename, int *plen)
{
   FILE *f = stb__fopen(filename, "rb");
   char *buffer, **list=NULL, *s;
   size_t len, count, i;

   if (!f) return NULL;
   len = stb_filelen(f);
   buffer = (char *) malloc(len+1);
   len = fread(buffer, 1, len, f);
   buffer[len] = 0;
   fclose(f);

   // two passes through: first time count lines, second time set them
   for (i=0; i < 2; ++i) {
      s = buffer;
      if (i == 1)
         list[0] = s;
      count = 1;
      while (*s) {
         if (*s == '\n' || *s == '\r') {
            // detect if both cr & lf are together
            int crlf = (s[0] + s[1]) == ('\n' + '\r');
            if (i == 1) *s = 0;
            if (crlf) ++s;
            if (s[1]) {  // it's not over yet
               if (i == 1) list[count] = s+1;
               ++count;
            }
         }
         ++s;
      }
      if (i == 0) {
         list = (char **) malloc(sizeof(*list) * (count+1) + len+1);
         if (!list) return NULL;
         list[count] = 0;
         // recopy the file so there's just a single allocation to free
         memcpy(&list[count+1], buffer, len+1);
         free(buffer);
         buffer = (char *) &list[count+1];
         if (plen) *plen = count;
      }
   }
   return list;
}
```

解说：这一段实现或声明库接口 `stb_stringfile`，把“stringfile”相关能力封装成可被外部或内部复用的函数。

## 第 196 段（第 2261-2274 行）

```c
char * stb_fgets(char *buffer, int buflen, FILE *f)
{
   char *p;
   buffer[0] = 0;
   p = fgets(buffer, buflen, f);
   if (p) {
      int n = strlen(p)-1;
      if (n >= 0)
         if (p[n] == '\n')
            p[n] = 0;
   }
   return p;
}
```

解说：这一段实现或声明库接口 `stb_fgets`，把“fgets”相关能力封装成可被外部或内部复用的函数。

## 第 197 段（第 2275-2311 行）

```c
char * stb_fgets_malloc(FILE *f)
{
   // avoid reallocing for small strings
   char quick_buffer[800];
   quick_buffer[sizeof(quick_buffer)-2] = 0;
   if (!fgets(quick_buffer, sizeof(quick_buffer), f))
      return NULL;

   if (quick_buffer[sizeof(quick_buffer)-2] == 0) {
      int n = strlen(quick_buffer);
      if (n > 0 && quick_buffer[n-1] == '\n')
         quick_buffer[n-1] = 0;
      return strdup(quick_buffer);
   } else {
      char *p;
      char *a = strdup(quick_buffer);
      int len = sizeof(quick_buffer)-1;

      while (!feof(f)) {
         if (a[len-1] == '\n') break;
         a = (char *) realloc(a, len*2);
         p = &a[len];
         p[len-2] = 0;
         if (!fgets(p, len, f))
            break;
         if (p[len-2] == 0) {
            len += strlen(p);
            break;
         }
         len = len + (len-1);
      }
      if (a[len-1] == '\n')
         a[len-1] = 0;
      return a;
   }
}
```

解说：这一段实现或声明库接口 `stb_fgets_malloc`，把“fgets malloc”相关能力封装成可被外部或内部复用的函数。

## 第 198 段（第 2312-2336 行）

```c
int stb_fullpath(char *abs, int abs_size, char *rel)
{
   #ifdef _MSC_VER
   return _fullpath(abs, rel, abs_size) != NULL;
   #else
   if (rel[0] == '/' || rel[0] == '~') {
      if ((int) strlen(rel) >= abs_size)
         return 0;
      strcpy(abs,rel);
      return 1;
   } else {
      int n;
      getcwd(abs, abs_size);
      n = strlen(abs);
      if (n+(int) strlen(rel)+2 <= abs_size) {
         abs[n] = '/';
         strcpy(abs+n+1, rel);
         return 1;
      } else {
         return 0;
      }
   }
   #endif
}
```

解说：这一段实现或声明库接口 `stb_fullpath`，把“fullpath”相关能力封装成可被外部或内部复用的函数。

## 第 199 段（第 2337-2360 行）

```c
static int stb_fcmp_core(FILE *f, FILE *g)
{
   char buf1[1024],buf2[1024];
   int n1,n2, res=0;

   while (1) {
      n1 = fread(buf1, 1, sizeof(buf1), f);
      n2 = fread(buf2, 1, sizeof(buf2), g);
      res = memcmp(buf1,buf2,n1 < n2 ? n1 : n2);
      if (res)
         break;
      if (n1 != n2) {
         res = n1 < n2 ? -1 : 1;
         break;
      }
      if (n1 == 0)
         break;
   }

   fclose(f);
   fclose(g);
   return res;
}
```

解说：这一段实现或声明库接口 `stb_fcmp_core`，把“fcmp core”相关能力封装成可被外部或内部复用的函数。

## 第 200 段（第 2361-2377 行）

```c
int stb_fcmp(char *s1, char *s2)
{
   FILE *f = stb__fopen(s1, "rb");
   FILE *g = stb__fopen(s2, "rb");

   if (f == NULL || g == NULL) {
      if (f) fclose(f);
      if (g) {
         fclose(g);
         return 1;
      }
      return f != NULL;
   }

   return stb_fcmp_core(f,g);
}
```

解说：这一段实现或声明库接口 `stb_fcmp`，把“fcmp”相关能力封装成可被外部或内部复用的函数。

## 第 201 段（第 2378-2398 行）

```c
int stb_feq(char *s1, char *s2)
{
   FILE *f = stb__fopen(s1, "rb");
   FILE *g = stb__fopen(s2, "rb");

   if (f == NULL || g == NULL) {
      if (f) fclose(f);
      if (g) fclose(g);
      return f == g;
   }

   // feq is faster because it shortcuts if they're different length
   if (stb_filelen(f) != stb_filelen(g)) {
      fclose(f);
      fclose(g);
      return 0;
   }

   return !stb_fcmp_core(f,g);
}
```

解说：这一段实现或声明库接口 `stb_feq`，把“feq”相关能力封装成可被外部或内部复用的函数。

## 第 202 段（第 2399-2440 行）

```c
int stb_copyfile(char *src, char *dest)
{
   char raw_buffer[1024];
   char *buffer;
   int buf_size = 65536;

   FILE *f, *g;

   // if file already exists at destination, do nothing
   if (stb_feq(src, dest)) return 1;

   // open file
   f = stb__fopen(src, "rb");
   if (f == NULL) return 0;

   // open file for writing
   g = stb__fopen(dest, "wb");
   if (g == NULL) {
      fclose(f);
      return 0;
   }

   buffer = (char *) malloc(buf_size);
   if (buffer == NULL) {
      buffer = raw_buffer;
      buf_size = sizeof(raw_buffer);
   }

   while (!feof(f)) {
      int n = fread(buffer, 1, buf_size, f);
      if (n != 0)
         fwrite(buffer, 1, n, g);
   }

   fclose(f);
   if (buffer != raw_buffer)
      free(buffer);

   fclose(g);
   return 1;
}
```

解说：这一段实现或声明库接口 `stb_copyfile`，把“copyfile”相关能力封装成可被外部或内部复用的函数。

## 第 203 段（第 2441-2442 行）

```c
#define stb_fgetc(f)    ((unsigned char) fgetc(f))
```

解说：这一段定义宏（如 `stb_fgetc`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 204 段（第 2443-2459 行）

```c
#if 0
// strip the trailing '/' or '\\' from a directory so we can refer to it
// as a file for _stat()
char *stb_strip_final_slash(char *t)
{
   if (t[0]) {
      char *z = t + strlen(t) - 1;
      // *z is the last character
      if (*z == '\\' || *z == '/')
         if (z != t+2 || t[1] != ':') // but don't strip it if it's e.g. "c:/"
            *z = 0;
      if (*z == '\\')
         *z = '/'; // canonicalize to make sure it matches db
   }
   return t;
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 205 段（第 2460-2474 行）

```c
char *stb_strip_final_slash_regardless(char *t)
{
   if (t[0]) {
      char *z = t + strlen(t) - 1;
      // *z is the last character
      if (*z == '\\' || *z == '/')
         *z = 0;
      if (*z == '\\')
         *z = '/'; // canonicalize to make sure it matches db
   }
   return t;
}
#endif
#endif
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 206 段（第 2475-2479 行）

```c
//////////////////////////////////////////////////////////////////////////////
//
//                 Portable directory reading
//
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 207 段（第 2480-2488 行）

```c
#ifndef STB_INCLUDE_STB_LIB_H
STB_EXTERN char **stb_readdir_files  (char *dir);
STB_EXTERN char **stb_readdir_files_mask(char *dir, char *wild);
STB_EXTERN char **stb_readdir_subdirs(char *dir);
STB_EXTERN char **stb_readdir_subdirs_mask(char *dir, char *wild);
STB_EXTERN void   stb_readdir_free   (char **files);
STB_EXTERN char **stb_readdir_recursive(char *dir, char *filespec);
STB_EXTERN void stb_delete_directory_recursive(char *dir);
```

解说：这一段涉及动态内存管理，负责申请、调整或释放运行时数据结构需要的存储空间。

## 第 208 段（第 2489-2492 行）

```c
// forward declare for implementation
STB_EXTERN int stb_wildmatchi(char *expr, char *candidate);
#endif // STB_INCLUDE_STB_LIB_H
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 209 段（第 2494-2495 行）

```c
#ifdef STB_LIB_IMPLEMENTATION
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 210 段（第 2496-2502 行）

```c
#ifdef _MSC_VER
#include <io.h>
#else
#include <unistd.h>
#include <dirent.h>
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 211 段（第 2503-2511 行）

```c
void stb_readdir_free(char **files)
{
   char **f2 = files;
   int i;
   for (i=0; i < stb_arr_len(f2); ++i)
      free(f2[i]);
   stb_arr_free(f2);
}
```

解说：这一段实现或声明库接口 `stb_readdir_free`，把“readdir free”相关能力封装成可被外部或内部复用的函数。

## 第 212 段（第 2512-2518 行）

```c
static int isdotdirname(char *name)
{
   if (name[0] == '.')
      return (name[1] == '.') ? !name[2] : !name[1];
   return 0;
}
```

解说：这一段实现或声明函数 `isdotdirname`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 213 段（第 2519-2614 行）

```c
static char **readdir_raw(char *dir, int return_subdirs, char *mask)
{
   char **results = NULL;
   char buffer[4096], with_slash[4096];
   size_t n;

   #ifdef _MSC_VER
      stb__wchar *ws;
      struct _wfinddata_t data;
   #ifdef _WIN64
      const intptr_t none = -1;
      intptr_t z;
   #else
      const long none = -1;
      long z;
   #endif
   #else // !_MSC_VER
      const DIR *none = NULL;
      DIR *z;
   #endif

   n = stb_strscpy(buffer,dir,sizeof(buffer));
   if (!n || n >= sizeof(buffer))
      return NULL;
   stb_fixpath(buffer);
   n--;

   if (n > 0 && (buffer[n-1] != '/')) {
      buffer[n++] = '/';
   }
   buffer[n] = 0;
   if (!stb_strscpy(with_slash,buffer,sizeof(with_slash)))
      return NULL;

   #ifdef _MSC_VER
      if (!stb_strscpy(buffer+n,"*.*",sizeof(buffer)-n))
         return NULL;
      ws = stb__from_utf8(buffer);
      z = _wfindfirst((const wchar_t *)ws, &data);
   #else
      z = opendir(dir);
   #endif

   if (z != none) {
      int nonempty = 1;
      #ifndef _MSC_VER
      struct dirent *data = readdir(z);
      nonempty = (data != NULL);
      #endif

      if (nonempty) {

         do {
            int is_subdir;
            #ifdef _MSC_VER
            char *name = stb__to_utf8((stb__wchar *)data.name);
            if (name == NULL) {
               fprintf(stderr, "%s to convert '%S' to %s!\n", "Unable", data.name, "utf8");
               continue;
            }
            is_subdir = !!(data.attrib & _A_SUBDIR);
            #else
            char *name = data->d_name;
            if (!stb_strscpy(buffer+n,name,sizeof(buffer)-n))
               break;
            // Could follow DT_LNK, but would need to check for recursive links.
            is_subdir = !!(data->d_type & DT_DIR);
            #endif

            if (is_subdir == return_subdirs) {
               if (!is_subdir || !isdotdirname(name)) {
                  if (!mask || stb_wildmatchi(mask, name)) {
                     char buffer[4096],*p=buffer;
                     if ( stb_snprintf(buffer, sizeof(buffer), "%s%s", with_slash, name) < 0 )
                        break;
                     if (buffer[0] == '.' && buffer[1] == '/')
                        p = buffer+2;
                     stb_arr_push(results, strdup(p));
                  }
               }
            }
         }
         #ifdef _MSC_VER
         while (0 == _wfindnext(z, &data));
         #else
         while ((data = readdir(z)) != NULL);
         #endif
      }
      #ifdef _MSC_VER
         _findclose(z);
      #else
         closedir(z);
      #endif
   }
   return results;
}
```

解说：这一段实现或声明库接口 `stb_snprintf`，把“snprintf”相关能力封装成可被外部或内部复用的函数。

## 第 214 段（第 2616-2620 行）

```c
char **stb_readdir_files  (char *dir) { return readdir_raw(dir, 0, NULL); }
char **stb_readdir_subdirs(char *dir) { return readdir_raw(dir, 1, NULL); }
char **stb_readdir_files_mask(char *dir, char *wild) { return readdir_raw(dir, 0, wild); }
char **stb_readdir_subdirs_mask(char *dir, char *wild) { return readdir_raw(dir, 1, wild); }
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 215 段（第 2621-2644 行）

```c
int stb__rec_max=0x7fffffff;
static char **stb_readdir_rec(char **sofar, char *dir, char *filespec)
{
   char **files;
   char ** dirs;
   char **p;

   if (stb_arr_len(sofar) >= stb__rec_max) return sofar;

   files = stb_readdir_files_mask(dir, filespec);
   stb_arr_for(p, files) {
      stb_arr_push(sofar, strdup(*p));
      if (stb_arr_len(sofar) >= stb__rec_max) break;
   }
   stb_readdir_free(files);
   if (stb_arr_len(sofar) >= stb__rec_max) return sofar;

   dirs = stb_readdir_subdirs(dir);
   stb_arr_for(p, dirs)
      sofar = stb_readdir_rec(sofar, *p, filespec);
   stb_readdir_free(dirs);
   return sofar;
}
```

解说：这一段涉及动态内存管理，负责申请、调整或释放运行时数据结构需要的存储空间。

## 第 216 段（第 2645-2649 行）

```c
char **stb_readdir_recursive(char *dir, char *filespec)
{
   return stb_readdir_rec(NULL, dir, filespec);
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 217 段（第 2650-2671 行）

```c
void stb_delete_directory_recursive(char *dir)
{
   char **list = stb_readdir_subdirs(dir);
   int i;
   for (i=0; i < stb_arr_len(list); ++i)
      stb_delete_directory_recursive(list[i]);
   stb_arr_free(list);
   list = stb_readdir_files(dir);
   for (i=0; i < stb_arr_len(list); ++i)
      if (!remove(list[i])) {
         // on windows, try again after making it writeable; don't ALWAYS
         // do this first since that would be slow in the normal case
         #ifdef _MSC_VER
         _chmod(list[i], _S_IWRITE);
         remove(list[i]);
         #endif
      }
   stb_arr_free(list);
   stb__windows(_rmdir,rmdir)(dir);
}
#endif
```

解说：这一段实现或声明库接口 `stb_delete_directory_recursive`，把“delete directory recursive”相关能力封装成可被外部或内部复用的函数。

## 第 218 段（第 2672-2679 行）

```c
//////////////////////////////////////////////////////////////////////////////
//
//                 Checksums: CRC-32, ADLER32, SHA-1
//
//    CRC-32 and ADLER32 allow streaming blocks
//    SHA-1 requires either a complete buffer, max size 2^32 - 73
//          or it can checksum directly from a file, max 2^61
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 219 段（第 2680-2683 行）

```c
#ifndef STB_INCLUDE_STB_LIB_H
#define STB_ADLER32_SEED   1
#define STB_CRC32_SEED     0    // note that we logical NOT this in the code
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 220 段（第 2684-2687 行）

```c
STB_EXTERN stb_uint stb_adler32    (stb_uint adler32,  stb_uchar *buffer, stb_uint buflen);
STB_EXTERN stb_uint stb_crc32_block(stb_uint crc32  ,  stb_uchar *buffer, stb_uint buflen);
STB_EXTERN stb_uint stb_crc32      (                   stb_uchar *buffer, stb_uint buflen);
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 221 段（第 2688-2691 行）

```c
STB_EXTERN void stb_sha1(    unsigned char output[20], stb_uchar *buffer, unsigned int len);
STB_EXTERN int stb_sha1_file(unsigned char output[20], char *file);
#endif //  STB_INCLUDE_STB_LIB_H
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 222 段（第 2692-2709 行）

```c
#ifdef STB_LIB_IMPLEMENTATION
stb_uint stb_crc32_block(stb_uint crc, unsigned char *buffer, stb_uint len)
{
   static stb_uint crc_table[256];
   stb_uint i,j,s;
   crc = ~crc;

   if (crc_table[1] == 0)
      for(i=0; i < 256; i++) {
         for (s=i, j=0; j < 8; ++j)
            s = (s >> 1) ^ (s & 1 ? 0xedb88320 : 0);
         crc_table[i] = s;
      }
   for (i=0; i < len; ++i)
      crc = (crc >> 8) ^ crc_table[buffer[i] ^ (crc & 0xff)];
   return ~crc;
}
```

解说：这一段实现或声明库接口 `stb_crc32_block`，把“crc32 block”相关能力封装成可被外部或内部复用的函数。

## 第 223 段（第 2710-2714 行）

```c
stb_uint stb_crc32(unsigned char *buffer, stb_uint len)
{
   return stb_crc32_block(0, buffer, len);
}
```

解说：这一段实现或声明库接口 `stb_crc32`，把“crc32”相关能力封装成可被外部或内部复用的函数。

## 第 224 段（第 2715-2745 行）

```c
stb_uint stb_adler32(stb_uint adler32, stb_uchar *buffer, stb_uint buflen)
{
   const unsigned long ADLER_MOD = 65521;
   unsigned long s1 = adler32 & 0xffff, s2 = adler32 >> 16;
   unsigned long blocklen, i;

   blocklen = buflen % 5552;
   while (buflen) {
      for (i=0; i + 7 < blocklen; i += 8) {
         s1 += buffer[0], s2 += s1;
         s1 += buffer[1], s2 += s1;
         s1 += buffer[2], s2 += s1;
         s1 += buffer[3], s2 += s1;
         s1 += buffer[4], s2 += s1;
         s1 += buffer[5], s2 += s1;
         s1 += buffer[6], s2 += s1;
         s1 += buffer[7], s2 += s1;

         buffer += 8;
      }

      for (; i < blocklen; ++i)
         s1 += *buffer++, s2 += s1;

      s1 %= ADLER_MOD, s2 %= ADLER_MOD;
      buflen -= blocklen;
      blocklen = 5552;
   }
   return (s2 << 16) + s1;
}
```

解说：这一段实现或声明库接口 `stb_adler32`，把“adler32”相关能力封装成可被外部或内部复用的函数。

## 第 225 段（第 2746-2791 行）

```c
#define stb__big32(c)    (((c)[0]<<24) + (c)[1]*65536 + (c)[2]*256 + (c)[3])
static void stb__sha1(stb_uchar *chunk, stb_uint h[5])
{
   int i;
   stb_uint a,b,c,d,e;
   stb_uint w[80];

   for (i=0; i < 16; ++i)
      w[i] = stb__big32(&chunk[i*4]);
   for (i=16; i < 80; ++i) {
      stb_uint t;
      t = w[i-3] ^ w[i-8] ^ w[i-14] ^ w[i-16];
      w[i] = (t + t) | (t >> 31);
   }

   a = h[0];
   b = h[1];
   c = h[2];
   d = h[3];
   e = h[4];

   #define STB__SHA1(k,f)                                            \
   {                                                                 \
      stb_uint temp = (a << 5) + (a >> 27) + (f) + e + (k) + w[i];  \
      e = d;                                                       \
      d = c;                                                     \
      c = (b << 30) + (b >> 2);                               \
      b = a;                                              \
      a = temp;                                    \
   }

   i=0;
   for (; i < 20; ++i) STB__SHA1(0x5a827999, d ^ (b & (c ^ d))       );
   for (; i < 40; ++i) STB__SHA1(0x6ed9eba1, b ^ c ^ d               );
   for (; i < 60; ++i) STB__SHA1(0x8f1bbcdc, (b & c) + (d & (b ^ c)) );
   for (; i < 80; ++i) STB__SHA1(0xca62c1d6, b ^ c ^ d               );

   #undef STB__SHA1

   h[0] += a;
   h[1] += b;
   h[2] += c;
   h[3] += d;
   h[4] += e;
}
```

解说：这一段实现或声明库接口 `stb__sha1`，把“sha1”相关能力封装成可被外部或内部复用的函数。

## 第 226 段（第 2792-2857 行）

```c
void stb_sha1(stb_uchar output[20], stb_uchar *buffer, stb_uint len)
{
   unsigned char final_block[128];
   stb_uint end_start, final_len, j;
   int i;

   stb_uint h[5];

   h[0] = 0x67452301;
   h[1] = 0xefcdab89;
   h[2] = 0x98badcfe;
   h[3] = 0x10325476;
   h[4] = 0xc3d2e1f0;

   // we need to write padding to the last one or two
   // blocks, so build those first into 'final_block'

   // we have to write one special byte, plus the 8-byte length

   // compute the block where the data runs out
   end_start = len & ~63;

   // compute the earliest we can encode the length
   if (((len+9) & ~63) == end_start) {
      // it all fits in one block, so fill a second-to-last block
      end_start -= 64;
   }

   final_len = end_start + 128;

   // now we need to copy the data in
   assert(end_start + 128 >= len+9);
   assert(end_start < len || len < 64-9);

   j = 0;
   if (end_start > len)
      j = (stb_uint) - (int) end_start;

   for (; end_start + j < len; ++j)
      final_block[j] = buffer[end_start + j];
   final_block[j++] = 0x80;
   while (j < 128-5) // 5 byte length, so write 4 extra padding bytes
      final_block[j++] = 0;
   // big-endian size
   final_block[j++] = len >> 29;
   final_block[j++] = len >> 21;
   final_block[j++] = len >> 13;
   final_block[j++] = len >>  5;
   final_block[j++] = len <<  3;
   assert(j == 128 && end_start + j == final_len);

   for (j=0; j < final_len; j += 64) { // 512-bit chunks
      if (j+64 >= end_start+64)
         stb__sha1(&final_block[j - end_start], h);
      else
         stb__sha1(&buffer[j], h);
   }

   for (i=0; i < 5; ++i) {
      output[i*4 + 0] = h[i] >> 24;
      output[i*4 + 1] = h[i] >> 16;
      output[i*4 + 2] = h[i] >>  8;
      output[i*4 + 3] = h[i] >>  0;
   }
}
```

解说：这一段实现或声明库接口 `stb_sha1`，把“sha1”相关能力封装成可被外部或内部复用的函数。

## 第 227 段（第 2858-2924 行）

```c
int stb_sha1_file(stb_uchar output[20], char *file)
{
   int i;
   stb_uint64 length=0;
   unsigned char buffer[128];

   FILE *f = stb__fopen(file, "rb");
   stb_uint h[5];

   if (f == NULL) return 0; // file not found

   h[0] = 0x67452301;
   h[1] = 0xefcdab89;
   h[2] = 0x98badcfe;
   h[3] = 0x10325476;
   h[4] = 0xc3d2e1f0;

   for(;;) {
      int n = fread(buffer, 1, 64, f);
      if (n == 64) {
         stb__sha1(buffer, h);
         length += n;
      } else {
         int block = 64;

         length += n;

         buffer[n++] = 0x80;

         // if there isn't enough room for the length, double the block
         if (n + 8 > 64) 
            block = 128;

         // pad to end
         memset(buffer+n, 0, block-8-n);

         i = block - 8;
         buffer[i++] = (stb_uchar) (length >> 53);
         buffer[i++] = (stb_uchar) (length >> 45);
         buffer[i++] = (stb_uchar) (length >> 37);
         buffer[i++] = (stb_uchar) (length >> 29);
         buffer[i++] = (stb_uchar) (length >> 21);
         buffer[i++] = (stb_uchar) (length >> 13);
         buffer[i++] = (stb_uchar) (length >>  5);
         buffer[i++] = (stb_uchar) (length <<  3);
         assert(i == block);
         stb__sha1(buffer, h);
         if (block == 128)
            stb__sha1(buffer+64, h);
         else
            assert(block == 64);
         break;
      }
   }
   fclose(f);

   for (i=0; i < 5; ++i) {
      output[i*4 + 0] = h[i] >> 24;
      output[i*4 + 1] = h[i] >> 16;
      output[i*4 + 2] = h[i] >>  8;
      output[i*4 + 3] = h[i] >>  0;
   }

   return 1;
}
#endif // STB_LIB_IMPLEMENTATION
```

解说：这一段实现或声明库接口 `stb_sha1_file`，把“sha1 file”相关能力封装成可被外部或内部复用的函数。

## 第 228 段（第 2925-2929 行）

```c
//////////////////////////////////////////////////////////////////////////////
//
//               Random Numbers via Meresenne Twister or LCG
//
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 229 段（第 2930-2934 行）

```c
#ifndef STB_INCLUDE_STB_LIB_H
STB_EXTERN unsigned long stb_srandLCG(unsigned long seed);
STB_EXTERN unsigned long stb_randLCG(void);
STB_EXTERN double        stb_frandLCG(void);
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 230 段（第 2935-2941 行）

```c
STB_EXTERN void          stb_srand(unsigned long seed);
STB_EXTERN unsigned long stb_rand(void);
STB_EXTERN double        stb_frand(void);
STB_EXTERN void          stb_shuffle(void *p, size_t n, size_t sz,
                                        unsigned long seed);
STB_EXTERN void stb_reverse(void *p, size_t n, size_t sz);
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 231 段（第 2942-2944 行）

```c
STB_EXTERN unsigned long stb_randLCG_explicit(unsigned long seed);
#endif // STB_INCLUDE_STB_LIB_H
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 232 段（第 2945-2950 行）

```c
#ifdef STB_LIB_IMPLEMENTATION
unsigned long stb_randLCG_explicit(unsigned long seed)
{
   return seed * 2147001325 + 715136305;
}
```

解说：这一段实现或声明库接口 `stb_randLCG_explicit`，把“randLCG explicit”相关能力封装成可被外部或内部复用的函数。

## 第 233 段（第 2951-2952 行）

```c
static unsigned long stb__rand_seed=0;
```

解说：这一段进行变量初始化或状态更新，为后续计算准备输入数据，或保存当前步骤得到的中间结果。

## 第 234 段（第 2953-2959 行）

```c
unsigned long stb_srandLCG(unsigned long seed)
{
   unsigned long previous = stb__rand_seed;
   stb__rand_seed = seed;
   return previous;
}
```

解说：这一段实现或声明库接口 `stb_srandLCG`，把“srandLCG”相关能力封装成可被外部或内部复用的函数。

## 第 235 段（第 2960-2966 行）

```c
unsigned long stb_randLCG(void)
{
   stb__rand_seed = stb__rand_seed * 2147001325 + 715136305; // BCPL generator
   // shuffle non-random bits to the middle, and xor to decorrelate with seed
   return 0x31415926 ^ ((stb__rand_seed >> 16) + (stb__rand_seed << 16));
}
```

解说：这一段实现或声明库接口 `stb_randLCG`，把“randLCG”相关能力封装成可被外部或内部复用的函数。

## 第 236 段（第 2967-2971 行）

```c
double stb_frandLCG(void)
{
   return stb_randLCG() / ((double) (1 << 16) * (1 << 16));
}
```

解说：这一段实现或声明库接口 `stb_frandLCG`，把“frandLCG”相关能力封装成可被外部或内部复用的函数。

## 第 237 段（第 2972-2989 行）

```c
void stb_shuffle(void *p, size_t n, size_t sz, unsigned long seed)
{
   char *a;
   unsigned long old_seed;
   int i;
   if (seed)
      old_seed = stb_srandLCG(seed);
   a = (char *) p + (n-1) * sz;

   for (i=n; i > 1; --i) {
      int j = stb_randLCG() % i;
      stb_swap(a, (char *) p + j * sz, sz);
      a -= sz;
   }
   if (seed)
      stb_srandLCG(old_seed);
}
```

解说：这一段实现或声明库接口 `stb_shuffle`，把“shuffle”相关能力封装成可被外部或内部复用的函数。

## 第 238 段（第 2990-2997 行）

```c
void stb_reverse(void *p, size_t n, size_t sz)
{
   int i,j = n-1;
   for (i=0; i < j; ++i,--j) {
      stb_swap((char *) p + i * sz, (char *) p + j * sz, sz);
   }
}
```

解说：这一段实现或声明库接口 `stb_reverse`，把“reverse”相关能力封装成可被外部或内部复用的函数。

## 第 239 段（第 2998-3000 行）

```c
// public domain Mersenne Twister by Michael Brundage
#define STB__MT_LEN       624
```

解说：这一段定义宏（如 `STB__MT_LEN`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 240 段（第 3001-3003 行）

```c
int stb__mt_index = STB__MT_LEN*sizeof(unsigned long)+1;
unsigned long stb__mt_buffer[STB__MT_LEN];
```

解说：这一段进行变量初始化或状态更新，为后续计算准备输入数据，或保存当前步骤得到的中间结果。

## 第 241 段（第 3004-3013 行）

```c
void stb_srand(unsigned long seed)
{
   int i;
   unsigned long old = stb_srandLCG(seed);
   for (i = 0; i < STB__MT_LEN; i++)
      stb__mt_buffer[i] = stb_randLCG();
   stb_srandLCG(old);
   stb__mt_index = STB__MT_LEN*sizeof(unsigned long);
}
```

解说：这一段实现或声明库接口 `stb_srand`，把“srand”相关能力封装成可被外部或内部复用的函数。

## 第 242 段（第 3014-3021 行）

```c
#define STB__MT_IA           397
#define STB__MT_IB           (STB__MT_LEN - STB__MT_IA)
#define STB__UPPER_MASK      0x80000000
#define STB__LOWER_MASK      0x7FFFFFFF
#define STB__MATRIX_A        0x9908B0DF
#define STB__TWIST(b,i,j)    ((b)[i] & STB__UPPER_MASK) | ((b)[j] & STB__LOWER_MASK)
#define STB__MAGIC(s)        (((s)&1)*STB__MATRIX_A)
```

解说：这一段定义宏（如 `STB__MT_IA, STB__MT_IB, STB__UPPER_MASK, STB__LOWER_MASK, STB__MATRIX_A`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 243 段（第 3022-3057 行）

```c
unsigned long stb_rand()
{
   unsigned long * b = stb__mt_buffer;
   int idx = stb__mt_index;
   unsigned long s,r;
   int i;
	
   if (idx >= STB__MT_LEN*sizeof(unsigned long)) {
      if (idx > STB__MT_LEN*sizeof(unsigned long))
         stb_srand(0);
      idx = 0;
      i = 0;
      for (; i < STB__MT_IB; i++) {
         s = STB__TWIST(b, i, i+1);
         b[i] = b[i + STB__MT_IA] ^ (s >> 1) ^ STB__MAGIC(s);
      }
      for (; i < STB__MT_LEN-1; i++) {
         s = STB__TWIST(b, i, i+1);
         b[i] = b[i - STB__MT_IB] ^ (s >> 1) ^ STB__MAGIC(s);
      }
      
      s = STB__TWIST(b, STB__MT_LEN-1, 0);
      b[STB__MT_LEN-1] = b[STB__MT_IA-1] ^ (s >> 1) ^ STB__MAGIC(s);
   }
   stb__mt_index = idx + sizeof(unsigned long);
   
   r = *(unsigned long *)((unsigned char *)b + idx);
   
   r ^= (r >> 11);
   r ^= (r << 7) & 0x9D2C5680;
   r ^= (r << 15) & 0xEFC60000;
   r ^= (r >> 18);
   
   return r;
}
```

解说：这一段实现或声明库接口 `stb_rand`，把“rand”相关能力封装成可被外部或内部复用的函数。

## 第 244 段（第 3058-3063 行）

```c
double stb_frand(void)
{
   return stb_rand() / ((double) (1 << 16) * (1 << 16));
}
#endif
```

解说：这一段实现或声明库接口 `stb_frand`，把“frand”相关能力封装成可被外部或内部复用的函数。

## 第 245 段（第 3064-3068 行）

```c
//////////////////////////////////////////////////////////////////////////////
//
//      wildcards and regexping
//
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 246 段（第 3069-3075 行）

```c
#ifndef STB_INCLUDE_STB_LIB_H
STB_EXTERN int stb_wildmatch (char *expr, char *candidate);
STB_EXTERN int stb_wildmatchi(char *expr, char *candidate);
STB_EXTERN int stb_wildfind  (char *expr, char *candidate);
STB_EXTERN int stb_wildfindi (char *expr, char *candidate);
#endif // STB_INCLUDE_STB_LIB_H
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 247 段（第 3076-3097 行）

```c
#ifdef STB_LIB_IMPLEMENTATION
static int stb__match_qstring(char *candidate, char *qstring, int qlen, int insensitive)
{
   int i;
   if (insensitive) {
      for (i=0; i < qlen; ++i)
         if (qstring[i] == '?') {
            if (!candidate[i]) return 0;
         } else
            if (tolower(qstring[i]) != tolower(candidate[i]))
               return 0;
   } else {
      for (i=0; i < qlen; ++i)
         if (qstring[i] == '?') {
            if (!candidate[i]) return 0;
         } else
            if (qstring[i] != candidate[i])
               return 0;
   }
   return 1;
}
```

解说：这一段实现或声明库接口 `stb__match_qstring`，把“match qstring”相关能力封装成可被外部或内部复用的函数。

## 第 248 段（第 3098-3124 行）

```c
static int stb__find_qstring(char *candidate, char *qstring, int qlen, int insensitive)
{
   char c;

   int offset=0;
   while (*qstring == '?') {
      ++qstring;
      --qlen;
      ++candidate;
      if (qlen == 0) return 0;
      if (*candidate == 0) return -1;
   }

   c = *qstring++;
   --qlen;
   if (insensitive) c = tolower(c);

   while (candidate[offset]) {
      if (c == (insensitive ? tolower(candidate[offset]) : candidate[offset]))
         if (stb__match_qstring(candidate+offset+1, qstring, qlen, insensitive))
            return offset;
      ++offset;
   }

   return -1;
}
```

解说：这一段实现或声明库接口 `stb__find_qstring`，把“find qstring”相关能力封装成可被外部或内部复用的函数。

## 第 249 段（第 3125-3211 行）

```c
int stb__wildmatch_raw2(char *expr, char *candidate, int search, int insensitive)
{
   int where=0;
   int start = -1;
   
   if (!search) {
      // parse to first '*'
      if (*expr != '*')
         start = 0;
      while (*expr != '*') {
         if (!*expr)
            return *candidate == 0 ? 0 : -1;
         if (*expr == '?') {
            if (!*candidate) return -1;
         } else {
            if (insensitive) {
               if (tolower(*candidate) != tolower(*expr))
                  return -1;
            } else 
               if (*candidate != *expr)
                  return -1;
         }
         ++candidate, ++expr, ++where;
      }
   } else {
      // 0-length search string
      if (!*expr)
         return 0;
   }

   assert(search || *expr == '*');
   if (!search)
      ++expr;

   // implicit '*' at this point
      
   while (*expr) {
      int o=0;
      // combine redundant * characters
      while (expr[0] == '*') ++expr;

      // ok, at this point, expr[-1] == '*',
      // and expr[0] != '*'

      if (!expr[0]) return start >= 0 ? start : 0;

      // now find next '*'
      o = 0;
      while (expr[o] != '*') {
         if (expr[o] == 0)
            break;
         ++o;
      }
      // if no '*', scan to end, then match at end
      if (expr[o] == 0 && !search) {
         int z;
         for (z=0; z < o; ++z)
            if (candidate[z] == 0)
               return -1;
         while (candidate[z])
            ++z;
         // ok, now check if they match
         if (stb__match_qstring(candidate+z-o, expr, o, insensitive))
            return start >= 0 ? start : 0;
         return -1; 
      } else {
         // if yes '*', then do stb__find_qmatch on the intervening chars
         int n = stb__find_qstring(candidate, expr, o, insensitive);
         if (n < 0)
            return -1;
         if (start < 0)
            start = where + n;
         expr += o;
         candidate += n+o;
      }

      if (*expr == 0) {
         assert(search);
         return start;
      }

      assert(*expr == '*');
      ++expr;
   }

   return start >= 0 ? start : 0;
}
```

解说：这一段实现或声明库接口 `stb__wildmatch_raw2`，把“wildmatch raw2”相关能力封装成可被外部或内部复用的函数。

## 第 250 段（第 3213-3236 行）

```c
int stb__wildmatch_raw(char *expr, char *candidate, int search, int insensitive)
{
   char buffer[256];
   // handle multiple search strings
   char *s = strchr(expr, ';');
   char *last = expr;
   while (s) {
      int z;
      // need to allow for non-writeable strings... assume they're small
      if (s - last < 256) {
         stb_strncpy(buffer, last, s-last+1);
         z = stb__wildmatch_raw2(buffer, candidate, search, insensitive);
      } else {
         *s = 0;
         z = stb__wildmatch_raw2(last, candidate, search, insensitive);
         *s = ';';
      }
      if (z >= 0) return z;
      last = s+1;
      s = strchr(last, ';');
   }
   return stb__wildmatch_raw2(last, candidate, search, insensitive);
}
```

解说：这一段实现或声明库接口 `stb__wildmatch_raw`，把“wildmatch raw”相关能力封装成可被外部或内部复用的函数。

## 第 251 段（第 3237-3241 行）

```c
int stb_wildmatch(char *expr, char *candidate)
{
   return stb__wildmatch_raw(expr, candidate, 0,0) >= 0;
}
```

解说：这一段实现或声明库接口 `stb_wildmatch`，把“wildmatch”相关能力封装成可被外部或内部复用的函数。

## 第 252 段（第 3242-3246 行）

```c
int stb_wildmatchi(char *expr, char *candidate)
{
   return stb__wildmatch_raw(expr, candidate, 0,1) >= 0;
}
```

解说：这一段实现或声明库接口 `stb_wildmatchi`，把“wildmatchi”相关能力封装成可被外部或内部复用的函数。

## 第 253 段（第 3247-3251 行）

```c
int stb_wildfind(char *expr, char *candidate)
{
   return stb__wildmatch_raw(expr, candidate, 1,0);
}
```

解说：这一段实现或声明库接口 `stb_wildfind`，把“wildfind”相关能力封装成可被外部或内部复用的函数。

## 第 254 段（第 3252-3256 行）

```c
int stb_wildfindi(char *expr, char *candidate)
{
   return stb__wildmatch_raw(expr, candidate, 1,1);
}
```

解说：这一段实现或声明库接口 `stb_wildfindi`，把“wildfindi”相关能力封装成可被外部或内部复用的函数。

## 第 255 段（第 3257-3259 行）

```c
#undef STB_LIB_IMPLEMENTATION
#endif // STB_LIB_IMPLEMENTATION
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 256 段（第 3260-3264 行）

```c
#ifndef STB_INCLUDE_STB_LIB_H
#define STB_INCLUDE_STB_LIB_H
#undef STB_EXTERN
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 257 段（第 3265-3305 行）

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
