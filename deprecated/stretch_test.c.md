# stretch_test.c 代码解说

源文件：`deprecated/stretch_test.c`

本文按源文件中的自然代码段切分，每段先给出原始代码，再用中文解释这段代码在做什么以及它为什么存在。

## 第 1 段（第 1-4 行）

```c
// check that stb_truetype compiles with no stb_rect_pack.h
#define STB_TRUETYPE_IMPLEMENTATION
#include "stb_truetype.h"
```

解说：这一段定义宏（如 `STB_TRUETYPE_IMPLEMENTATION`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 2 段（第 5-8 行）

```c
#define STB_DS_IMPLEMENTATION
#include "stb_ds.h"
#include <assert.h>
```

解说：这一段定义宏（如 `STB_DS_IMPLEMENTATION`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 3 段（第 9-29 行）

```c
int main(int arg, char **argv)
{
   int i;
   int *arr = NULL;

   for (i=0; i < 1000000; ++i)
      arrput(arr, i);

   assert(arrlen(arr) == 1000000);
   for (i=0; i < 1000000; ++i)
      assert(arr[i] == i);

   arrfree(arr);
   arr = NULL;

   for (i=0; i < 1000; ++i)
      arrput(arr, 1000);
   assert(arrlen(arr) == 1000000);

   return 0;
}
```

解说：这一段是程序入口，负责准备测试或工具运行所需的参数、调用核心逻辑，并把执行结果转换成进程退出状态。
