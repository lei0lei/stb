# test_truetype.c 代码解说

源文件：`tests/test_truetype.c`

本文按源文件中的自然代码段切分，每段先给出原始代码，再用中文解释这段代码在做什么以及它为什么存在。

## 第 1 段（第 1-5 行）

```c
#ifndef _CRT_SECURE_NO_WARNINGS
// Fixes Compile Errors for Visual Studio 2005 or newer
  #define _CRT_SECURE_NO_WARNINGS
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 2 段（第 6-13 行）

```c
#include <stdlib.h>

// this isn't meant to compile standalone; link with test_c_compilation.c as well
#include "stb_rect_pack.h"
#define STB_TRUETYPE_IMPLEMENTATION
#include "stb_truetype.h"
#include "stb_image_write.h"
```

解说：这一段定义宏（如 `STB_TRUETYPE_IMPLEMENTATION`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 3 段（第 14-15 行）

```c
#ifdef TT_TEST
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 4 段（第 16-17 行）

```c
#include <stdio.h>
```

解说：这一段引入编译所需的头文件或本项目内的依赖，使后面的类型、宏和函数声明能够被编译器识别。

## 第 5 段（第 18-20 行）

```c
unsigned char ttf_buffer[1 << 25];
unsigned char output[512*100];
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 6 段（第 21-29 行）

```c
void debug(void)
{
   stbtt_fontinfo font;
   fread(ttf_buffer, 1, 1<<25, fopen("c:/x/lm/LiberationMono-Regular.ttf", "rb"));
   stbtt_InitFont(&font, ttf_buffer, 0);

   stbtt_MakeGlyphBitmap(&font, output, 6, 9, 512, 5.172414E-03f, 5.172414E-03f, 54);
}
```

解说：这一段实现或声明函数 `debug`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 7 段（第 30-35 行）

```c
#define BITMAP_W  256
#define BITMAP_H  512
unsigned char temp_bitmap[BITMAP_H][BITMAP_W];
stbtt_bakedchar cdata[256*2]; // ASCII 32..126 is 95 glyphs
stbtt_packedchar pdata[256*2];
```

解说：这一段定义宏（如 `BITMAP_W, BITMAP_H`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 8 段（第 36-153 行）

```c
int main(int argc, char **argv)
{
   stbtt_fontinfo font;
   unsigned char *bitmap;
   int w,h,i,j,c = (argc > 1 ? atoi(argv[1]) : '@'), s = (argc > 2 ? atoi(argv[2]) : 32);

   //debug();

   // @TODO: why is minglui.ttc failing? 
   //fread(ttf_buffer, 1, 1<<25, fopen(argc > 3 ? argv[3] : "c:/windows/fonts/mingliu.ttc", "rb"));

   fread(ttf_buffer, 1, 1<<25, fopen(argc > 3 ? argv[3] : "c:/windows/fonts/DejaVuSans.ttf", "rb"));

   stbtt_InitFont(&font, ttf_buffer, stbtt_GetFontOffsetForIndex(ttf_buffer,0));

#if 0
   {
      stbtt__bitmap b;
      stbtt__point p[2];
      int wcount[2] = { 2,0 };
      p[0].x = 0.2f;
      p[0].y = 0.3f;
      p[1].x = 3.8f;
      p[1].y = 0.8f;
      b.w = 16;
      b.h = 2;
      b.stride = 16;
      b.pixels = malloc(b.w*b.h);
      stbtt__rasterize(&b, p, wcount, 1, 1, 1, 0, 0, 0, 0, 0, NULL);
      for (i=0; i < 8; ++i)
         printf("%f\n", b.pixels[i]/255.0);
   }
#endif

#if 1
   {
      static stbtt_pack_context pc;
      static stbtt_packedchar cd[256];
      static unsigned char atlas[1024*1024];

      stbtt_PackBegin(&pc, atlas, 1024,1024,1024,1,NULL);
      stbtt_PackFontRange(&pc, ttf_buffer, 0, 32.0, 0, 256, cd);
      stbtt_PackEnd(&pc);
   }
#endif


#if 1
   {
      static stbtt_pack_context pc;
      static stbtt_packedchar cd[256];
      static unsigned char atlas[1024*1024];
      unsigned char *data;

      data = stbtt_GetCodepointSDF(&font, stbtt_ScaleForPixelHeight(&font,32.0), 'u', 4, 128, 128/4, &w,&h,&i,&j);
      for (j=0; j < h; ++j) {
         for (i=0; i < w; ++i) {
            putchar(" .:ioVM@"[data[j*w+i]>>5]);
         }
         putchar('\n');
      }
   }
#endif

#if 0
   stbtt_BakeFontBitmap(ttf_buffer,stbtt_GetFontOffsetForIndex(ttf_buffer,0), 40.0, temp_bitmap[0],BITMAP_W,BITMAP_H, 32,96, cdata); // no guarantee this fits!
   stbi_write_png("fonttest1.png", BITMAP_W, BITMAP_H, 1, temp_bitmap, 0);

   {
      stbtt_pack_context pc;
      stbtt_PackBegin(&pc, temp_bitmap[0], BITMAP_W, BITMAP_H, 0, 1, NULL);
      stbtt_PackFontRange(&pc, ttf_buffer, 0, 20.0, 32, 95, pdata);
      stbtt_PackFontRange(&pc, ttf_buffer, 0, 20.0, 0xa0, 0x100-0xa0, pdata);
      stbtt_PackEnd(&pc);
      stbi_write_png("fonttest2.png", BITMAP_W, BITMAP_H, 1, temp_bitmap, 0);
   }

   {
      stbtt_pack_context pc;
      stbtt_pack_range pr[2];
      stbtt_PackBegin(&pc, temp_bitmap[0], BITMAP_W, BITMAP_H, 0, 1, NULL);

      pr[0].chardata_for_range = pdata;
      pr[0].array_of_unicode_codepoints = NULL;
      pr[0].first_unicode_codepoint_in_range = 32;
      pr[0].num_chars = 95;
      pr[0].font_size = 20.0f;
      pr[1].chardata_for_range = pdata+256;
      pr[1].array_of_unicode_codepoints = NULL;
      pr[1].first_unicode_codepoint_in_range = 0xa0;
      pr[1].num_chars = 0x100 - 0xa0;
      pr[1].font_size = 20.0f;

      stbtt_PackSetOversampling(&pc, 2, 2);
      stbtt_PackFontRanges(&pc, ttf_buffer, 0, pr, 2);
      stbtt_PackEnd(&pc);
      stbi_write_png("fonttest3.png", BITMAP_W, BITMAP_H, 1, temp_bitmap, 0);
   }
   return 0;
#endif

   (void)stbtt_GetCodepointBitmapSubpixel(&font,
					 0.4972374737262726f,
					 0.4986416995525360f,
					 0.2391788959503174f,
					 0.1752119064331055f,
					 'd',
					 &w, &h,
					 0,0);

   bitmap = stbtt_GetCodepointBitmap(&font, 0,stbtt_ScaleForPixelHeight(&font, (float)s), c, &w, &h, 0,0);
   for (j=0; j < h; ++j) {
      for (i=0; i < w; ++i)
         putchar(" .:ioVM@"[bitmap[j*w+i]>>5]);
      putchar('\n');
   }
   return 0;
}
```

解说：这一段是程序入口，负责准备测试或工具运行所需的参数、调用核心逻辑，并把执行结果转换成进程退出状态。

## 第 9 段（第 154-154 行）

```c
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。
