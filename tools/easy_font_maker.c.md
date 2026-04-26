# easy_font_maker.c 代码解说

源文件：`tools/easy_font_maker.c`

本文按源文件中的自然代码段切分，每段先给出原始代码，再用中文解释这段代码在做什么以及它为什么存在。

## 第 1 段（第 1-2 行）

```c
// This program was used to encode the data for stb_simple_font.h
```

解说：这一段是说明性注释，记录附近代码的用途、限制或维护提示，方便读者理解后续实现。

## 第 2 段（第 3-7 行）

```c
#define STB_DEFINE
#include "stb.h"
#define STB_IMAGE_IMPLEMENTATION
#include "stb_image.h"
```

解说：这一段定义宏（如 `STB_DEFINE, STB_IMAGE_IMPLEMENTATION`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 3 段（第 8-10 行）

```c
int w,h;
uint8 *data;
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 4 段（第 11-19 行）

```c
int last_x[2], last_y[2];
int num_seg[2], non_empty;
#if 0
typedef struct
{
   unsigned short first_segment;
   unsigned char advance;
} chardata;
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 5 段（第 20-27 行）

```c
typedef struct
{
   unsigned char x:4;
   unsigned char y:4;
   unsigned char len:3;
   unsigned char dir:1;
} segment;
```

解说：这一段声明结构体 `segment`，把相关字段组织成一个数据对象，供后续算法在函数之间传递和维护状态。

## 第 6 段（第 28-29 行）

```c
segment *segments;
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 7 段（第 30-50 行）

```c
void add_seg(int x, int y, int len, int horizontal)
{
   segment s;
   s.x = x;
   s.y = y;
   s.len = len;
   s.dir = horizontal;
   assert(s.x == x);
   assert(s.y == y);
   assert(s.len == len);
   stb_arr_push(segments, s);
}
#else
typedef struct
{
   unsigned char first_segment:8;
   unsigned char first_v_segment:8;
   unsigned char advance:5;
   unsigned char voff:1;
} chardata;
```

解说：这一段实现或声明函数 `add_seg`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 8 段（第 51-53 行）

```c
#define X_LIMIT 1
#define LEN_LIMIT 7
```

解说：这一段定义宏（如 `X_LIMIT, LEN_LIMIT`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 9 段（第 54-60 行）

```c
typedef struct
{
   unsigned char dx:1;
   unsigned char y:4;
   unsigned char len:3;
} segment;
```

解说：这一段声明结构体 `segment`，把相关字段组织成一个数据对象，供后续算法在函数之间传递和维护状态。

## 第 10 段（第 61-63 行）

```c
segment *segments;
segment *vsegments;
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 11 段（第 64-92 行）

```c
void add_seg(int x, int y, int len, int horizontal)
{
   segment s;

   while (x - last_x[horizontal] > X_LIMIT) {
      add_seg(last_x[horizontal] + X_LIMIT, 0, 0, horizontal);
   }
   while (len > LEN_LIMIT) {
      add_seg(x, y, LEN_LIMIT, horizontal);
      len -= LEN_LIMIT;
      x += LEN_LIMIT*horizontal;
      y += LEN_LIMIT*!horizontal;
   }

   s.dx = x - last_x[horizontal];
   s.y = y;
   s.len = len;
   non_empty += len != 0;
   //assert(s.x == x);
   assert(s.y == y);
   assert(s.len == len);
   ++num_seg[horizontal];
   if (horizontal)
      stb_arr_push(segments, s);
   else
      stb_arr_push(vsegments, s);
   last_x[horizontal] = x;
}
```

解说：这一段实现或声明函数 `add_seg`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 12 段（第 93-109 行）

```c
void print_segments(segment *s)
{
   int i, hpos;
   printf("   ");
   hpos = 4;
   for (i=0; i < stb_arr_len(s); ++i) {
      // repack for portability
      unsigned char seg = s[i].len + s[i].dx*8 + s[i].y*16;
      hpos += printf("%d,", seg);
      if (hpos > 72 && i+1 < stb_arr_len(s)) {
         hpos = 4;
         printf("\n    ");
      }
   }
   printf("\n");
}
```

解说：这一段实现或声明函数 `print_segments`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 13 段（第 110-113 行）

```c
#endif

chardata charinfo[128];
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 14 段（第 114-171 行）

```c
int parse_char(int x, chardata *c, int offset)
{
   int start_x = x, end_x, top_y = 0, y;

   c->first_segment = stb_arr_len(segments);
   c->first_v_segment = stb_arr_len(vsegments) - offset;
   assert(c->first_segment == stb_arr_len(segments));
   assert(c->first_v_segment + offset == stb_arr_len(vsegments));

   // find advance distance
   end_x = x+1;
   while (data[end_x*3] == 255)
      ++end_x;
   c->advance = end_x - start_x + 1;

   last_x[0] = last_x[1] = 0;
   last_y[0] = last_y[1] = 0;

   for (y=2; y < h; ++y) {
      for (x=start_x; x < end_x; ++x) {
         if (data[y*3*w+x*3+1] < 255) {
            top_y = y;
            break;
         }
      }
      if (top_y)
         break;
   }
   c->voff = top_y > 2;
   if (top_y > 2) 
      top_y = 3;

   for (x=start_x; x < end_x; ++x) {
      int y;
      for (y=2; y < h; ++y) {
         if (data[y*3*w+x*3+1] < 255) {
            if (data[y*3*w+x*3+0] == 255) { // red
               int len=0;
               while (y+len < h && data[(y+len)*3*w+x*3+0] == 255 && data[(y+len)*3*w+x*3+1] == 0) {
                  data[(y+len)*3*w+x*3+0] = 0;
                  ++len;
               }
               add_seg(x-start_x,y-top_y,len,0);
            }
            if (data[y*3*w+x*3+2] == 255) { // blue
               int len=0;
               while (x+len < end_x && data[y*3*w+(x+len)*3+2] == 255 && data[y*3*w+(x+len)*3+1] == 0) {
                  data[y*3*w+(x+len)*3+2] = 0;
                  ++len;
               }
               add_seg(x-start_x,y-top_y,len,1);
            }
         }
      }
   }
   return end_x;
}
```

解说：这一段实现或声明函数 `parse_char`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 15 段（第 173-211 行）

```c
int main(int argc, char **argv)
{
   int c, x=0;
   data = stbi_load("easy_font_raw.png", &w, &h, 0, 3);
   for (c=32; c < 127; ++c) {
      x = parse_char(x, &charinfo[c], 0);
      printf("%3d -- %3d %3d\n", c, charinfo[c].first_segment, charinfo[c].first_v_segment);
   }
   printf("===\n");
   printf("%d %d %d\n", num_seg[0], num_seg[1], non_empty);
   printf("%d\n", sizeof(segments[0]) * stb_arr_len(segments));
   printf("%d\n", sizeof(segments[0]) * stb_arr_len(segments) + sizeof(segments[0]) * stb_arr_len(vsegments) + sizeof(charinfo[32])*95);

   printf("struct {\n"
          "    unsigned char advance;\n"
          "    unsigned char h_seg;\n"
          "    unsigned char v_seg;\n"
          "} stb_easy_font_charinfo[96] = {\n");
   charinfo[c].first_segment = stb_arr_len(segments);
   charinfo[c].first_v_segment = stb_arr_len(vsegments);
   for (c=32; c < 128; ++c) {
      if ((c & 3) == 0) printf("    ");
      printf("{ %2d,%3d,%3d },",
         charinfo[c].advance + 16*charinfo[c].voff,
         charinfo[c].first_segment,
         charinfo[c].first_v_segment);
      if ((c & 3) == 3) printf("\n"); else printf("  ");
   }
   printf("};\n\n");

   printf("unsigned char stb_easy_font_hseg[%d] = {\n", stb_arr_len(segments));
      print_segments(segments);
   printf("};\n\n");

   printf("unsigned char stb_easy_font_vseg[%d] = {\n", stb_arr_len(vsegments));
      print_segments(vsegments);
   printf("};\n");
   return 0;
}
```

解说：这一段是程序入口，负责准备测试或工具运行所需的参数、调用核心逻辑，并把执行结果转换成进程退出状态。
