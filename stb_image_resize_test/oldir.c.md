# oldir.c 代码解说

源文件：`stb_image_resize_test/oldir.c`

本文按源文件中的自然代码段切分，每段先给出原始代码，再用中文解释这段代码在做什么以及它为什么存在。

## 第 1 段（第 1-3 行）

```c
#include <stdio.h>
#include <stdlib.h>
```

解说：这一段引入编译所需的头文件或本项目内的依赖，使后面的类型、宏和函数声明能够被编译器识别。

## 第 2 段（第 4-9 行）

```c
#ifdef _MSC_VER
#define stop() __debugbreak()
#else
#define stop() __builtin_trap()
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 3 段（第 10-12 行）

```c
//#define HEAVYTM
#include "tm.h"
```

解说：这一段是预处理指令，负责在真正编译前组织符号、平台差异和条件代码。

## 第 4 段（第 13-17 行）

```c
#define STBIR_SATURATE_INT
#define STB_IMAGE_RESIZE_STATIC
#define STB_IMAGE_RESIZE_IMPLEMENTATION
#include "old_image_resize.h"
```

解说：这一段定义宏（如 `STBIR_SATURATE_INT, STB_IMAGE_RESIZE_STATIC, STB_IMAGE_RESIZE_IMPLEMENTATION`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 5 段（第 19-24 行）

```c
static int types[4] =    { STBIR_TYPE_UINT8, STBIR_TYPE_UINT8, STBIR_TYPE_UINT16, STBIR_TYPE_FLOAT };
static int edges[4] =    { STBIR_EDGE_CLAMP, STBIR_EDGE_REFLECT, STBIR_EDGE_ZERO, STBIR_EDGE_WRAP };
static int flts[5] =     { STBIR_FILTER_BOX, STBIR_FILTER_TRIANGLE, STBIR_FILTER_CUBICBSPLINE, STBIR_FILTER_CATMULLROM, STBIR_FILTER_MITCHELL };
static int channels[20] = { 1, 2, 3, 4,      4,4,  2,2,  4,4, 2,2,  4,4, 2,2,  4,4, 2,2 }; 
static int alphapos[20] = { -1, -1, -1, -1,  3,0,  1,0,   3,0,  1,0,   3,0,  1,0,   3,0,  1,0 };
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 6 段（第 26-56 行）

```c
void oresize( void * o, int ox, int oy, int op, void * i, int ix, int iy, int ip, int buf, int type, int edg, int flt )
{
  int t = types[type];
  int ic = channels[buf];
  int alpha = alphapos[buf];
  int e = edges[edg];
  int f = flts[flt];
  int space = ( type == 1 ) ? STBIR_COLORSPACE_SRGB : 0;
  int flags = ( buf >= 16 ) ? STBIR_FLAG_ALPHA_PREMULTIPLIED : ( ( buf >= 12 ) ? STBIR_FLAG_ALPHA_OUT_PREMULTIPLIED : ( ( buf >= 8 ) ? (STBIR_FLAG_ALPHA_PREMULTIPLIED|STBIR_FLAG_ALPHA_OUT_PREMULTIPLIED) : 0 ) );
  stbir_uint64 start;

  ENTER( "Resize (old)" );
  start = tmGetAccumulationStart( tm_mask );

  if(!stbir_resize( i, ix, iy, ip, o, ox, oy, op, t, ic, alpha, flags, e, e, f, f, space, 0 ) )
    stop();

  #ifdef STBIR_PROFILE
  tmEmitAccumulationZone( 0, 0, (tm_uint64 *)&start, 0, oldprofile.named.setup, "Setup (old)" );
  tmEmitAccumulationZone( 0, 0, (tm_uint64 *)&start, 0, oldprofile.named.filters, "Filters (old)" );
  tmEmitAccumulationZone( 0, 0, (tm_uint64 *)&start, 0, oldprofile.named.looping, "Looping (old)" );
  tmEmitAccumulationZone( 0, 0, (tm_uint64 *)&start, 0, oldprofile.named.vertical, "Vertical (old)" );
  tmEmitAccumulationZone( 0, 0, (tm_uint64 *)&start, 0, oldprofile.named.horizontal, "Horizontal (old)" );
  tmEmitAccumulationZone( 0, 0, (tm_uint64 *)&start, 0, oldprofile.named.decode, "Scanline input (old)" );
  tmEmitAccumulationZone( 0, 0, (tm_uint64 *)&start, 0, oldprofile.named.encode, "Scanline output (old)" );
  tmEmitAccumulationZone( 0, 0, (tm_uint64 *)&start, 0, oldprofile.named.alpha, "Alpha weighting (old)" );
  tmEmitAccumulationZone( 0, 0, (tm_uint64 *)&start, 0, oldprofile.named.unalpha, "Alpha unweighting (old)" );
  #endif

  LEAVE();
}
```

解说：这一段实现或声明函数 `oresize`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。
