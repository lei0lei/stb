# test_siphash.c 代码解说

源文件：`tests/test_siphash.c`

本文按源文件中的自然代码段切分，每段先给出原始代码，再用中文解释这段代码在做什么以及它为什么存在。

## 第 1 段（第 1-2 行）

```c
#include <stdio.h>
```

解说：这一段引入编译所需的头文件或本项目内的依赖，使后面的类型、宏和函数声明能够被编译器识别。

## 第 2 段（第 3-7 行）

```c
#define STB_DS_IMPLEMENTATION
#define STBDS_SIPHASH_2_4
#define STBDS_TEST_SIPHASH_2_4
#include "../stb_ds.h"
```

解说：这一段定义宏（如 `STB_DS_IMPLEMENTATION, STBDS_SIPHASH_2_4, STBDS_TEST_SIPHASH_2_4`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 3 段（第 8-21 行）

```c
int main(int argc, char **argv)
{
  unsigned char mem[64];
  int i,j;
  for (i=0; i < 64; ++i) mem[i] = i;
  for (i=0; i < 64; ++i) {
    size_t hash = stbds_hash_bytes(mem, i, 0);
    printf("  { ");
    for (j=0; j < 8; ++j)
      printf("0x%02x, ", (unsigned char) ((hash >> (j*8)) & 255));
    printf(" },\n");
  }
  return 0;
}
```

解说：这一段是程序入口，负责准备测试或工具运行所需的参数、调用核心逻辑，并把执行结果转换成进程退出状态。
