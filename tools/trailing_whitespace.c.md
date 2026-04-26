# trailing_whitespace.c 代码解说

源文件：`tools/trailing_whitespace.c`

本文按源文件中的自然代码段切分，每段先给出原始代码，再用中文解释这段代码在做什么以及它为什么存在。

## 第 1 段（第 1-3 行）

```c
#define STB_DEFINE
#include "stb.h"
```

解说：这一段定义宏（如 `STB_DEFINE`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 2 段（第 4-32 行）

```c
int main(int argc, char **argv)
{
   int i;
   for (i=1; i < argc; ++i) {
      int len;
      FILE *f;
      char *s = stb_file(argv[i], &len);
      char *end, *src, *dest;
      if (s == NULL) {
         printf("Couldn't read file '%s'.\n", argv[i]);
         continue; 
      }
      end = s + len;
      src = dest = s;
      while (src < end) {
         char *start=0;
         while (src < end && *src != '\n' && *src != '\r')
            *dest++ = *src++;
         while (dest-1 > s && (dest[-1] == ' ' || dest[-1] == '\t'))
            --dest;
         while (src < end && (*src == '\n' || *src == '\r'))
            *dest++ = *src++;
      }
      f = fopen(argv[i], "wb");
      fwrite(s, 1, dest-s, f);
      fclose(f);
   }
    return 0;
}
```

解说：这一段是程序入口，负责准备测试或工具运行所需的参数、调用核心逻辑，并把执行结果转换成进程退出状态。
