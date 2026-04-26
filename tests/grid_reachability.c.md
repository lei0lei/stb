# grid_reachability.c 代码解说

源文件：`tests/grid_reachability.c`

本文按源文件中的自然代码段切分，每段先给出原始代码，再用中文解释这段代码在做什么以及它为什么存在。

## 第 1 段（第 1-5 行）

```c
#define STB_CONNECTED_COMPONENTS_IMPLEMENTATION
#define STBCC_GRID_COUNT_X_LOG2  10
#define STBCC_GRID_COUNT_Y_LOG2  10
#include "stb_connected_components.h"
```

解说：这一段定义宏（如 `STB_CONNECTED_COMPONENTS_IMPLEMENTATION, STBCC_GRID_COUNT_X_LOG2, STBCC_GRID_COUNT_Y_LOG2`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 2 段（第 6-7 行）

```c
#ifdef GRID_TEST
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 3 段（第 8-11 行）

```c
#include <windows.h>
#include <stdio.h>
#include <direct.h>
```

解说：这一段引入编译所需的头文件或本项目内的依赖，使后面的类型、宏和函数声明能够被编译器识别。

## 第 4 段（第 12-17 行）

```c
//#define STB_DEFINE
#include "stb.h"

//#define STB_IMAGE_IMPLEMENTATION
#include "stb_image.h"
```

解说：这一段是预处理指令，负责在真正编译前组织符号、平台差异和条件代码。

## 第 5 段（第 18-20 行）

```c
//#define STB_IMAGE_WRITE_IMPLEMENTATION
#include "stb_image_write.h"
```

解说：这一段是预处理指令，负责在真正编译前组织符号、平台差异和条件代码。

## 第 6 段（第 21-25 行）

```c
typedef struct
{
   uint16 x,y;
} point;
```

解说：这一段声明结构体 `point`，把相关字段组织成一个数据对象，供后续算法在函数之间传递和维护状态。

## 第 7 段（第 26-39 行）

```c
point leader[1024][1024];
uint32 color[1024][1024];

point find(int x, int y)
{
   point p,q;
   p = leader[y][x];
   if (p.x == x && p.y == y)
      return p;
   q = find(p.x, p.y);
   leader[y][x] = q;
   return q;
}
```

解说：这一段实现或声明函数 `find`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 8 段（第 40-50 行）

```c
void onion(int x1, int y1, int x2, int y2)
{
   point p = find(x1,y1);
   point q = find(x2,y2);

   if (p.x == q.x && p.y == q.y)
      return;

   leader[p.y][p.x] = q;
}
```

解说：这一段实现或声明函数 `onion`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 9 段（第 51-91 行）

```c
void reference(uint8 *map, int w, int h)
{
   int i,j;

   for (j=0; j < h; ++j) {
      for (i=0; i < w; ++i) {
         leader[j][i].x = i;
         leader[j][i].y = j;
      }
   }
         
   for (j=1; j < h-1; ++j) {
      for (i=1; i < w-1; ++i) {
         if (map[j*w+i] == 255) {
            if (map[(j+1)*w+i] == 255) onion(i,j, i,j+1);
            if (map[(j)*w+i+1] == 255) onion(i,j, i+1,j);
         }
      }
   }

   for (j=0; j < h; ++j) {
      for (i=0; i < w; ++i) {
         uint32 c = 0xff000000;
         if (leader[j][i].x == i && leader[j][i].y == j) {
            if (map[j*w+i] == 255)
               c = stb_randLCG() | 0xff404040;
         }
         color[j][i] = c;
      }
   }

   for (j=0; j < h; ++j) {
      for (i=0; i < w; ++i) {
         if (leader[j][i].x != i || leader[j][i].y != j) {
            point p = find(i,j);
            color[j][i] = color[p.y][p.x];
         }
      }
   }
}
```

解说：这一段实现或声明函数 `reference`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 10 段（第 92-118 行）

```c
void write_map(stbcc_grid *g, int w, int h, char *filename)
{
   int i,j;
   for (j=0; j < h; ++j) {
      for (i=0; i < w; ++i) {
         unsigned int c;
         c = stbcc_get_unique_id(g,i,j);
         c = stb_rehash_improved(c)&0xffffff;
         if (c == STBCC_NULL_UNIQUE_ID)
            c = 0xff000000;
         else
            c = (~c)^0x555555;
         if (i % 32 == 0 || j %32 == 0) {
            int r = (c >> 16) & 255;
            int g = (c >> 8) & 255;
            int b = c & 255;
            r = (r+130)/2;
            g = (g+130)/2;
            b = (b+130)/2;
            c = 0xff000000 + (r<<16) + (g<<8) + b;
         }
         color[j][i] = c;
      }
   }
   stbi_write_png(filename, w, h, 4, color, 4*w);
}
```

解说：这一段实现或声明函数 `write_map`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 11 段（第 119-124 行）

```c
void test_connected(stbcc_grid *g)
{
   int n = stbcc_query_grid_node_connection(g, 512, 90, 512, 871);
   //printf("%d ", n);
}
```

解说：这一段实现测试函数 `test_connected`，通过构造输入、调用目标接口并检查结果，验证相关功能是否按预期工作。

## 第 12 段（第 125-127 行）

```c
static char *message;
LARGE_INTEGER start;
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 13 段（第 128-133 行）

```c
void start_timer(char *s)
{
   message = s;
   QueryPerformanceCounter(&start);
}
```

解说：这一段实现或声明函数 `start_timer`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 14 段（第 134-145 行）

```c
void end_timer(void)
{
   LARGE_INTEGER end, freq;
   double tm;

   QueryPerformanceCounter(&end);
   QueryPerformanceFrequency(&freq);

   tm = (end.QuadPart - start.QuadPart) / (double) freq.QuadPart;
   printf("%6.4lf ms: %s\n", tm * 1000, message);
}
```

解说：这一段实现或声明函数 `end_timer`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 15 段（第 146-147 行）

```c
extern void quicktest(void);
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 16 段（第 148-362 行）

```c
int loc[5000][2];
int main(int argc, char **argv)
{
   stbcc_grid *g;

   int w,h, i,j,k=0, count=0, r;
   uint8 *map = stbi_load("data/map_03.png", &w, &h, 0, 1);

   assert(map);
   quicktest();

   for (j=0; j < h; ++j)
      for (i=0; i < w; ++i)
         map[j*w+i] = ~map[j*w+i];

   for (i=0; i < w; ++i)
      for (j=0; j < h; ++j)
         //map[j*w+i] = (((i+1) ^ (j+1)) >> 1) & 1 ? 255 : 0;
         map[j*w+i] = stb_max(abs(i-w/2),abs(j-h/2)) & 1 ? 255 : 0;
         //map[j*w+i] = (((i ^ j) >> 5) ^ (i ^ j)) & 1 ? 255 : 0;
         //map[j*w+i] = stb_rand() & 1 ? 255 : 0;

   #if 1
   for (i=0; i < 100000; ++i)
      map[(stb_rand()%h)*w + stb_rand()%w] ^= 255;
   #endif
            
   _mkdir("tests/output/stbcc");

   stbi_write_png("tests/output/stbcc/reference.png", w, h, 1, map, 0);

   //reference(map, w, h);

   g = malloc(stbcc_grid_sizeof());
   printf("Size: %d\n", stbcc_grid_sizeof());

#if 0
   memset(map, 0, w*h);
   stbcc_init_grid(g, map, w, h);
   {
      int n;
      char **s = stb_stringfile("c:/x/clockwork_update.txt", &n);
      write_map(g, w, h, "tests/output/stbcc/base.png");
      for (i=1; i < n; i += 1) {
         int x,y,t;
         sscanf(s[i], "%d %d %d", &x, &y, &t);
         if (i == 571678)
            write_map(g, w, h, stb_sprintf("tests/output/stbcc/clockwork_good.png", i));
         stbcc_update_grid(g, x, y, t);
         if (i == 571678)
            write_map(g, w, h, stb_sprintf("tests/output/stbcc/clockwork_bad.png", i));
         //if (i > 571648 && i <= 571712)
            //write_map(g, w, h, stb_sprintf("tests/output/stbcc/clockwork_%06d.png", i));
      }
      write_map(g, w, h, stb_sprintf("tests/output/stbcc/clockwork_%06d.png", i-1));
   }
   return 0;
#endif


   start_timer("init");
   stbcc_init_grid(g, map, w, h);
   end_timer();
   //g = stb_file("c:/x/clockwork_path.bin", 0);
   write_map(g, w, h, "tests/output/stbcc/base.png");

   for (i=0; i < 5000;) {
      loc[i][0] = stb_rand() % w;
      loc[i][1] = stb_rand() % h;
      if (stbcc_query_grid_open(g, loc[i][0], loc[i][1]))
         ++i;
   }

   r = 0;
   start_timer("reachable");
   for (i=0; i < 2000; ++i) {
      for (j=0; j < 2000; ++j) {
         int x1 = loc[i][0], y1 = loc[i][1];
         int x2 = loc[2000+j][0], y2 = loc[2000+j][1];
         r += stbcc_query_grid_node_connection(g, x1,y1, x2,y2);
      }
   }
   end_timer();
   printf("%d reachable\n", r);

   printf("Cluster size: %d,%d\n", STBCC__CLUSTER_SIZE_X, STBCC__CLUSTER_SIZE_Y);

   #if 1
   for (j=0; j < 10; ++j) {
      for (i=0; i < 5000; ++i) {
         loc[i][0] = stb_rand() % w;
         loc[i][1] = stb_rand() % h;
      }
      start_timer("updating 2500");
      for (i=0; i < 2500; ++i) {
         if (stbcc_query_grid_open(g, loc[i][0], loc[i][1]))
            stbcc_update_grid(g, loc[i][0], loc[i][1], 1);
         else
            stbcc_update_grid(g, loc[i][0], loc[i][1], 0);
      }
      end_timer();
      write_map(g, w, h, stb_sprintf("tests/output/stbcc/update_random_%d.png", j*i));
   }
   #endif

   #if 0
   start_timer("removing");
   count = 0;
   for (i=0; i < 1800; ++i) {
      int x,y,a,b;
      x = stb_rand() % (w-32);
      y = stb_rand() % (h-32);
      
      if (i & 1) {
         for (a=0; a < 32; ++a)
            for (b=0; b < 1; ++b)
               if (stbcc_query_grid_open(g, x+a, y+b)) {
                  stbcc_update_grid(g, x+a, y+b, 1);
                  ++count;
               }
      } else {
         for (a=0; a < 1; ++a)
            for (b=0; b < 32; ++b)
               if (stbcc_query_grid_open(g, x+a, y+b)) {
                  stbcc_update_grid(g, x+a, y+b, 1);
                  ++count;
               }
      }

      //if (i % 100 == 0) write_map(g, w, h, stb_sprintf("tests/output/stbcc/open_random_%d.png", i+1));
   }
   end_timer();
   printf("Removed %d grid spaces\n", count);
   write_map(g, w, h, stb_sprintf("tests/output/stbcc/open_random_%d.png", i));


   r = 0;
   start_timer("reachable");
   for (i=0; i < 1000; ++i) {
      for (j=0; j < 1000; ++j) {
         int x1 = loc[i][0], y1 = loc[i][1];
         int x2 = loc[j][0], y2 = loc[j][1];
         r += stbcc_query_grid_node_connection(g, x1,y1, x2,y2);
      }
   }
   end_timer();
   printf("%d reachable\n", r);

   start_timer("adding");
   count = 0;
   for (i=0; i < 1800; ++i) {
      int x,y,a,b;
      x = stb_rand() % (w-32);
      y = stb_rand() % (h-32);

      if (i & 1) {
         for (a=0; a < 32; ++a)
            for (b=0; b < 1; ++b)
               if (!stbcc_query_grid_open(g, x+a, y+b)) {
                  stbcc_update_grid(g, x+a, y+b, 0);
                  ++count;
               }
      } else {
         for (a=0; a < 1; ++a)
            for (b=0; b < 32; ++b)
               if (!stbcc_query_grid_open(g, x+a, y+b)) {
                  stbcc_update_grid(g, x+a, y+b, 0);
                  ++count;
               }
      }

      //if (i % 100 == 0) write_map(g, w, h, stb_sprintf("tests/output/stbcc/close_random_%d.png", i+1));
   }
   end_timer();
   write_map(g, w, h, stb_sprintf("tests/output/stbcc/close_random_%d.png", i));
   printf("Added %d grid spaces\n", count);
   #endif


   #if 0  // for map_02.png
   start_timer("process");
   for (k=0; k < 20; ++k) {
      for (j=0; j < h; ++j) {
         int any=0;
         for (i=0; i < w; ++i) {
            if (map[j*w+i] > 10 && map[j*w+i] < 250) {
               //start_timer(stb_sprintf("open %d,%d", i,j));
               stbcc_update_grid(g, i, j, 0);
               test_connected(g);
               //end_timer();
               any = 1;
            }
         }
         if (any) write_map(g, w, h, stb_sprintf("tests/output/stbcc/open_row_%04d.png", j));
      }

      for (j=0; j < h; ++j) {
         int any=0;
         for (i=0; i < w; ++i) {
            if (map[j*w+i] > 10 && map[j*w+i] < 250) {
               //start_timer(stb_sprintf("close %d,%d", i,j));
               stbcc_update_grid(g, i, j, 1);
               test_connected(g);
               //end_timer();
               any = 1;
            }
         }
         if (any) write_map(g, w, h, stb_sprintf("tests/output/stbcc/close_row_%04d.png", j));
      }
   }
   end_timer();
   #endif

   return 0;
}
```

解说：这一段是程序入口，负责准备测试或工具运行所需的参数、调用核心逻辑，并把执行结果转换成进程退出状态。

## 第 17 段（第 363-363 行）

```c
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。
