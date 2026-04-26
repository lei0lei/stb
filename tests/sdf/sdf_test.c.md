# sdf_test.c 代码解说

源文件：`tests/sdf/sdf_test.c`

本文按源文件中的自然代码段切分，每段先给出原始代码，再用中文解释这段代码在做什么以及它为什么存在。

## 第 1 段（第 1-3 行）

```c
#define STB_DEFINE
#include "stb.h"
```

解说：这一段定义宏（如 `STB_DEFINE`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 2 段（第 4-6 行）

```c
#define STB_TRUETYPE_IMPLEMENTATION
#include "stb_truetype.h"
```

解说：这一段定义宏（如 `STB_TRUETYPE_IMPLEMENTATION`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 3 段（第 7-15 行）

```c
#define STB_IMAGE_WRITE_IMPLEMENTATION
#include "stb_image_write.h"

// used both to compute SDF and in 'shader'
float sdf_size = 32.0;          // the larger this is, the better large font sizes look
float pixel_dist_scale = 64.0;  // trades off precision w/ ability to handle *smaller* sizes
int onedge_value = 128;
int padding = 3; // not used in shader
```

解说：这一段进行变量初始化或状态更新，为后续计算准备输入数据，或保存当前步骤得到的中间结果。

## 第 4 段（第 16-24 行）

```c
typedef struct
{
   float advance;
   signed char xoff;
   signed char yoff;
   unsigned char w,h;
   unsigned char *data;
} fontchar;
```

解说：这一段声明结构体 `fontchar`，把相关字段组织成一个数据对象，供后续算法在函数之间传递和维护状态。

## 第 5 段（第 25-26 行）

```c
fontchar fdata[128];
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 6 段（第 27-30 行）

```c
#define BITMAP_W  1200
#define BITMAP_H  800
unsigned char bitmap[BITMAP_H][BITMAP_W][3];
```

解说：这一段定义宏（如 `BITMAP_W, BITMAP_H`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 7 段（第 31-33 行）

```c
char *sample = "This is goofy text, size %d!";
char *small_sample = "This is goofy text, size %d! Really needs in-shader supersampling to look good.";
```

解说：这一段进行变量初始化或状态更新，为后续计算准备输入数据，或保存当前步骤得到的中间结果。

## 第 8 段（第 34-40 行）

```c
void blend_pixel(int x, int y, int color, float alpha)
{
   int i;
   for (i=0; i < 3; ++i)
      bitmap[y][x][i] = (unsigned char) (stb_lerp(alpha, bitmap[y][x][i], color)+0.5); // round
}
```

解说：这一段实现或声明函数 `blend_pixel`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 9 段（第 41-106 行）

```c
void draw_char(float px, float py, char c, float relative_scale)
{
   int x,y;
   fontchar *fc = &fdata[c];
   float fx0 = px + fc->xoff*relative_scale;
   float fy0 = py + fc->yoff*relative_scale;
   float fx1 = fx0 + fc->w*relative_scale;
   float fy1 = fy0 + fc->h*relative_scale;
   int ix0 = (int) floor(fx0);
   int iy0 = (int) floor(fy0);
   int ix1 = (int) ceil(fx1);
   int iy1 = (int) ceil(fy1);
   // clamp to viewport
   if (ix0 < 0) ix0 = 0;
   if (iy0 < 0) iy0 = 0;
   if (ix1 > BITMAP_W) ix1 = BITMAP_W;
   if (iy1 > BITMAP_H) iy1 = BITMAP_H;

   for (y=iy0; y < iy1; ++y) {
      for (x=ix0; x < ix1; ++x) {
         float sdf_dist, pix_dist;
         float bmx = stb_linear_remap(x, fx0, fx1, 0, fc->w);
         float bmy = stb_linear_remap(y, fy0, fy1, 0, fc->h);
         int v00,v01,v10,v11;
         float v0,v1,v;
         int sx0 = (int) bmx;
         int sx1 = sx0+1;
         int sy0 = (int) bmy;
         int sy1 = sy0+1;
         // compute lerp weights
         bmx = bmx - sx0;
         bmy = bmy - sy0;
         // clamp to edge
         sx0 = stb_clamp(sx0, 0, fc->w-1);
         sx1 = stb_clamp(sx1, 0, fc->w-1);
         sy0 = stb_clamp(sy0, 0, fc->h-1);
         sy1 = stb_clamp(sy1, 0, fc->h-1);
         // bilinear texture sample
         v00 = fc->data[sy0*fc->w+sx0];
         v01 = fc->data[sy0*fc->w+sx1];
         v10 = fc->data[sy1*fc->w+sx0];
         v11 = fc->data[sy1*fc->w+sx1];
         v0 = stb_lerp(bmx,v00,v01);
         v1 = stb_lerp(bmx,v10,v11);
         v  = stb_lerp(bmy,v0 ,v1 );
         #if 0
         // non-anti-aliased
         if (v > onedge_value)
            blend_pixel(x,y,0,1.0);
         #else
         // Following math can be greatly simplified

         // convert distance in SDF value to distance in SDF bitmap
         sdf_dist = stb_linear_remap(v, onedge_value, onedge_value+pixel_dist_scale, 0, 1);
         // convert distance in SDF bitmap to distance in output bitmap
         pix_dist = sdf_dist * relative_scale;
         // anti-alias by mapping 1/2 pixel around contour from 0..1 alpha
         v = stb_linear_remap(pix_dist, -0.5f, 0.5f, 0, 1);
         if (v > 1) v = 1;
         if (v > 0)
            blend_pixel(x,y,0,v);
         #endif
      }
   }
}
```

解说：这一段实现或声明函数 `draw_char`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 10 段（第 108-117 行）

```c
void print_text(float x, float y, char *text, float scale)
{
   int i;
   for (i=0; text[i]; ++i) {
      if (fdata[text[i]].data)
         draw_char(x,y,text[i],scale);
      x += fdata[text[i]].advance * scale;
   }
}
```

解说：这一段实现或声明函数 `print_text`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 11 段（第 118-152 行）

```c
int main(int argc, char **argv)
{
   int ch;
   float scale, ypos;
   stbtt_fontinfo font;
   void *data = stb_file("c:/windows/fonts/times.ttf", NULL);
   stbtt_InitFont(&font, data, 0);

   scale = stbtt_ScaleForPixelHeight(&font, sdf_size);

   for (ch=32; ch < 127; ++ch) {
      fontchar fc;
      int xoff,yoff,w,h, advance;
      fc.data = stbtt_GetCodepointSDF(&font, scale, ch, padding, onedge_value, pixel_dist_scale, &w, &h, &xoff, &yoff);
      fc.xoff = xoff;
      fc.yoff = yoff;
      fc.w = w;
      fc.h = h;
      stbtt_GetCodepointHMetrics(&font, ch, &advance, NULL);
      fc.advance = advance * scale;
      fdata[ch] = fc;
   }

   ypos = 60;
   memset(bitmap, 255, sizeof(bitmap));
   print_text(400, ypos+30, stb_sprintf("sdf bitmap height %d", (int) sdf_size), 30/sdf_size);
   ypos += 80;
   for (scale = 8.0; scale < 120.0; scale *= 1.33f) {
      print_text(80, ypos+scale, stb_sprintf(scale == 8.0 ? small_sample : sample, (int) scale), scale / sdf_size);
      ypos += scale*1.05f + 20;
   }

   stbi_write_png("sdf_test.png", BITMAP_W, BITMAP_H, 3, bitmap, 0);
   return 0;
}
```

解说：这一段是程序入口，负责准备测试或工具运行所需的参数、调用核心逻辑，并把执行结果转换成进程退出状态。
