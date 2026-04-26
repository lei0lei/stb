# dotimings.c 代码解说

源文件：`stb_image_resize_test/dotimings.c`

本文按源文件中的自然代码段切分，每段先给出原始代码，再用中文解释这段代码在做什么以及它为什么存在。

## 第 1 段（第 1-4 行）

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
```

解说：这一段引入编译所需的头文件或本项目内的依赖，使后面的类型、宏和函数声明能够被编译器识别。

## 第 2 段（第 5-6 行）

```c
#ifdef _MSC_VER
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 3 段（第 7-11 行）

```c
#define stop() __debugbreak()
#include <windows.h>
#define int64 __int64
#pragma warning(disable:4127)
```

解说：这一段定义宏（如 `stop, int64`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 4 段（第 12-13 行）

```c
#define get_milliseconds GetTickCount
```

解说：这一段定义宏（如 `get_milliseconds`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 5 段（第 14-15 行）

```c
#else
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 6 段（第 16-18 行）

```c
#define stop() __builtin_trap()
#define int64 long long
```

解说：这一段定义宏（如 `stop, int64`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 7 段（第 19-21 行）

```c
typedef unsigned int U32;
typedef unsigned long long U64;
```

解说：这一段通过 `typedef` 给类型取别名，减少平台差异或复杂类型签名对后续代码的干扰。

## 第 8 段（第 22-29 行）

```c
#include <time.h>
static int get_milliseconds()
{
  struct timespec ts;
  clock_gettime( CLOCK_MONOTONIC, &ts );
  return (U32) ( ( ((U64)(U32)ts.tv_sec) * 1000LL ) + (U64)(((U32)ts.tv_nsec+500000)/1000000) );
}
```

解说：这一段实现或声明函数 `get_milliseconds`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 9 段（第 30-31 行）

```c
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 10 段（第 32-39 行）

```c
#if defined(TIME_SIMD)
  // default for most platforms
#elif defined(TIME_SCALAR)
  #define STBIR_NO_SIMD
#else
  #error You must define TIME_SIMD or TIME_SCALAR when compiling this file.
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 11 段（第 40-44 行）

```c
#define STBIR_PROFILE
#define STB_IMAGE_RESIZE_IMPLEMENTATION
#define STBIR__V_FIRST_INFO_BUFFER v_info
#include "stb_image_resize2.h"  // new one!
```

解说：这一段定义宏（如 `STBIR_PROFILE, STB_IMAGE_RESIZE_IMPLEMENTATION, STBIR__V_FIRST_INFO_BUFFER`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 12 段（第 45-48 行）

```c
#if defined(TIME_SIMD)  && !defined(STBIR_SIMD)
#error Timing SIMD, but scalar was ON!
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 13 段（第 49-52 行）

```c
#if defined(TIME_SCALAR)  && defined(STBIR_SIMD)
#error Timing scalar, but SIMD was ON!
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 14 段（第 53-55 行）

```c
#define HEADER 32
```

解说：这一段定义宏（如 `HEADER`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 15 段（第 56-64 行）

```c
static int file_write( const char *filename, void * buffer, size_t size ) 
{
  FILE * f = fopen( filename, "wb" );
  if ( f == 0 ) return 0;
  if ( fwrite( buffer, 1, size, f) != size ) return 0;
  fclose(f);
  return 1;
}
```

解说：这一段实现或声明函数 `file_write`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 16 段（第 65-97 行）

```c
int64 nresize( void * o, int ox, int oy, int op, void * i, int ix, int iy, int ip, int buf, int type, int edg, int flt )
{
  STBIR_RESIZE resize;
  int t;
  int64 b;

  stbir_resize_init( &resize, i, ix, iy, ip, o, ox, oy, op, buf, type );
  stbir_set_edgemodes( &resize, edg, edg );
  stbir_set_filters( &resize, flt, flt );
  
  stbir_build_samplers_with_splits( &resize, 1 );

  b = 0x7fffffffffffffffULL;
  for( t = 0 ; t < 16 ; t++ )
  {
    STBIR_PROFILE_INFO profile;
    int64 v;
    if(!stbir_resize_extended( &resize ) )
      stop();
    stbir_resize_extended_profile_info( &profile, &resize );
    v = profile.clocks[1]+profile.clocks[2];
    if ( v < b )
    {
      b = v;
      t = 0;
    }
  }

  stbir_free_samplers( &resize );

  return b;
}
```

解说：这一段实现或声明函数 `nresize`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 17 段（第 99-102 行）

```c
#define INSIZES 5
#define TYPESCOUNT 5
#define NUM 64
```

解说：这一段定义宏（如 `INSIZES, TYPESCOUNT, NUM`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 18 段（第 103-106 行）

```c
static const int sizes[INSIZES]={63,126,252,520,772};
static const int types[TYPESCOUNT]={STBIR_1CHANNEL,STBIR_2CHANNEL,STBIR_RGB,STBIR_4CHANNEL,STBIR_RGBA};
static const int effective[TYPESCOUNT]={1,2,3,4,7};
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 19 段（第 107-224 行）

```c
int main( int argc, char ** argv )
{
  unsigned char * input;
  unsigned char * output;
  int dimensionx, dimensiony;
  int scalex, scaley;
  int totalms;
  int timing_count;
  int ir;
  int * file;
  int * ts;
  int64 totalcycles;

  if ( argc != 6 )
  {
    printf("command: dotimings x_samps y_samps x_scale y_scale outfilename\n");
    exit(1);
  }

  input = malloc( 4*1200*1200 );
  memset( input, 0x80, 4*1200*1200 );
  output = malloc( 4*10000*10000ULL );
  
  dimensionx = atoi( argv[1] );
  dimensiony = atoi( argv[2] );
  scalex = atoi( argv[3] );
  scaley = atoi( argv[4] );

  timing_count = dimensionx * dimensiony * INSIZES * TYPESCOUNT;

  file = malloc( sizeof(int) * ( 2 * timing_count + HEADER ) );
  ts = file + HEADER;

  totalms = get_milliseconds();  
  totalcycles = STBIR_PROFILE_FUNC();
  for( ir = 0 ; ir < INSIZES ; ir++ )
  {
    int ix, iy, ty;
    ix = iy = sizes[ir];

    for( ty = 0 ; ty < TYPESCOUNT ; ty++ )
    {
      int h, hh;

      h = 1;
      for( hh = 0 ; hh < dimensiony; hh++ )
      {
        int ww, w = 1;
        for( ww = 0 ; ww < dimensionx; ww++ )
        {
          int64 VF, HF;
          int good;
        
          v_info.control_v_first = 2; // vertical first
          VF = nresize( output, w, h, (w*4*1)&~3, input, ix, iy, ix*4*1, types[ty], STBIR_TYPE_UINT8, STBIR_EDGE_CLAMP, STBIR_FILTER_MITCHELL );
          v_info.control_v_first = 1; // horizonal first
          HF = nresize( output, w, h, (w*4*1)&~3, input, ix, iy, ix*4*1, types[ty], STBIR_TYPE_UINT8, STBIR_EDGE_CLAMP, STBIR_FILTER_MITCHELL );

          good = ( ((HF<=VF) && (!v_info.v_first)) || ((VF<=HF) && (v_info.v_first)));

//          printf("\r%d,%d, %d,%d, %d, %I64d,%I64d, // Good: %c(%c-%d)  CompEst: %.1f %.1f\n", ix, iy, w, h, ty, VF, HF, good?'y':'n', v_info.v_first?'v':'h', v_info.v_resize_classification, v_info.v_cost,v_info.h_cost );
          ts[0] = (int)VF;
          ts[1] = (int)HF;

          ts += 2;

          w += scalex;
        }
        printf(".");
        h += scaley;  
      }
    }
  }
  totalms = get_milliseconds() - totalms;  
  totalcycles = STBIR_PROFILE_FUNC() - totalcycles;

  printf("\n");

  file[0] = 'VFT1';

  #if defined(_x86_64) || defined( __x86_64__ ) || defined( _M_X64 ) || defined(__x86_64) || defined(__SSE2__) || defined( _M_IX86_FP ) || defined(__i386) || defined( __i386__ ) || defined( _M_IX86 ) || defined( _X86_ )
  file[1] = 1;  // x64
  #elif defined( _M_AMD64 ) || defined( __aarch64__ ) || defined( __arm64__ ) || defined(__ARM_NEON__) || defined(__ARM_NEON) || defined(__arm__) || defined( _M_ARM )
  file[1] = 2;  // arm
  #else
  file[1] = 99;  // who knows???
  #endif
  
  #ifdef STBIR_SIMD8
    file[2] = 2;  // simd-8
  #elif defined( STBIR_SIMD )
    file[2] = 1;  // simd-4
  #else
    file[2] = 0;  // nosimd
  #endif
  
  file[3] = dimensionx; // dimx
  file[4] = dimensiony; // dimy
  file[5] = TYPESCOUNT;  // channel types
  file[ 6] = types[0]; file[7] = types[1]; file[8] = types[2]; file[9] = types[3]; file[10] = types[4];  // buffer_type 
  file[11] = effective[0]; file[12] = effective[1]; file[13] = effective[2]; file[14] = effective[3]; file[15] = effective[4];  // effective channels 
  file[16] = INSIZES;  // resizes
  file[17] = sizes[0]; file[18] = sizes[0]; // input sizes (w x h)
  file[19] = sizes[1]; file[20] = sizes[1];
  file[21] = sizes[2]; file[22] = sizes[2];
  file[23] = sizes[3]; file[24] = sizes[3];
  file[25] = sizes[4]; file[26] = sizes[4];
  file[27] = scalex;   file[28] = scaley;  // scale the dimx and dimy amount ( for(i=0;i<dimx) outputx = 1 + i*scalex; )
  file[29] = totalms;
  ((int64*)(file+30))[0] = totalcycles;

  if ( !file_write( argv[5], file, sizeof(int) * ( 2 * timing_count + HEADER ) ) )
    printf( "Error writing file: %s\n", argv[5] );
  else
    printf( "Successfully wrote timing file: %s\n", argv[5] );

  return 0;
}
```

解说：这一段是程序入口，负责准备测试或工具运行所需的参数、调用核心逻辑，并把执行结果转换成进程退出状态。
