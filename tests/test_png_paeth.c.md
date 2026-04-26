# test_png_paeth.c 代码解说

源文件：`tests/test_png_paeth.c`

本文按源文件中的自然代码段切分，每段先给出原始代码，再用中文解释这段代码在做什么以及它为什么存在。

## 第 1 段（第 1-15 行）

```c
#include <stdio.h>
#include <stdlib.h>

// Reference Paeth filter as per PNG spec
static int ref_paeth(int a, int b, int c)
{
   int p = a + b - c;
   int pa = abs(p-a);
   int pb = abs(p-b);
   int pc = abs(p-c);
   if (pa <= pb && pa <= pc) return a;
   if (pb <= pc) return b;
   return c;
}
```

解说：这一段实现或声明函数 `ref_paeth`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 2 段（第 16-26 行）

```c
// Optimized Paeth filter
static int opt_paeth(int a, int b, int c)
{
   int thresh = c*3 - (a + b);
   int lo = a < b ? a : b;
   int hi = a < b ? b : a;
   int t0 = (hi <= thresh) ? lo : c;
   int t1 = (thresh <= lo) ? hi : t0;
   return t1;
}
```

解说：这一段实现或声明函数 `opt_paeth`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 3 段（第 27-46 行）

```c
int main()
{
   // Exhaustively test the functions match for all byte inputs a, b,c in [0,255]
   for (int i = 0; i < (1 << 24); ++i) {
      int a = i & 0xff;
      int b = (i >> 8) & 0xff;
      int c = (i >> 16) & 0xff;

      int ref = ref_paeth(a, b, c);
      int opt = opt_paeth(a, b, c);
      if (ref != opt) {
         fprintf(stderr, "mismatch at a=%3d b=%3d c=%3d: ref=%3d opt=%3d\n", a, b, c, ref, opt);
         return 1;
      }
   }

   printf("all ok!\n");
   return 0;
}
```

解说：这一段是程序入口，负责准备测试或工具运行所需的参数、调用核心逻辑，并把执行结果转换成进程退出状态。

## 第 4 段（第 47-47 行）

```c
// vim:sw=3:sts=3:et
```

解说：这一段是说明性注释，记录附近代码的用途、限制或维护提示，方便读者理解后续实现。
