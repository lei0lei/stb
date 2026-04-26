# stb_cpp.cpp 代码解说

源文件：`tests/stb_cpp.cpp`

本文按源文件中的自然代码段切分，每段先给出原始代码，再用中文解释这段代码在做什么以及它为什么存在。

## 第 1 段（第 1-12 行）

```cpp
#define WIN32_MEAN_AND_LEAN
#define WIN32_LEAN_AND_MEAN
//#include <windows.h>
#include <conio.h>
#define STB_DEFINE
#ifndef _M_AMD64
#define STB_NPTR
#endif
#define STB_ONLY
#include "stb.h"
//#include "stb_file.h"
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 2 段（第 13-21 行）

```cpp
int count;
void c(int truth, const char *error)
{
   if (!truth) {
      fprintf(stderr, "Test failed: %s\n", error);
      ++count;
   }
}
```

解说：这一段实现或声明函数 `c`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 3 段（第 22-29 行）

```cpp
char *expects(stb_matcher *m, char *s, int result, int len, const char *str)
{
   int res2,len2=0;
   res2 = stb_lex(m, s, &len2);
   c(result == res2 && len == len2, str);
   return s + len;
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 4 段（第 30-52 行）

```cpp
void test_lex(void)
{
   stb_matcher *m = stb_lex_matcher();
   //         tok_en5 .3 20.1 20. .20 .1
   char *s = (char*) "tok_en5.3 20.1 20. .20.1";

   stb_lex_item(m, "[a-zA-Z_][a-zA-Z0-9_]*", 1   );
   stb_lex_item(m, "[0-9]*\\.?[0-9]*"      , 2   );
   stb_lex_item(m, "[\r\n\t ]+"            , 3   );
   stb_lex_item(m, "."                     , -99 );
   s=expects(m,s,1,7, "stb_lex 1");
   s=expects(m,s,2,2, "stb_lex 2");
   s=expects(m,s,3,1, "stb_lex 3");
   s=expects(m,s,2,4, "stb_lex 4");
   s=expects(m,s,3,1, "stb_lex 5");
   s=expects(m,s,2,3, "stb_lex 6");
   s=expects(m,s,3,1, "stb_lex 7");
   s=expects(m,s,2,3, "stb_lex 8");
   s=expects(m,s,2,2, "stb_lex 9");
   s=expects(m,s,0,0, "stb_lex 10");
   stb_matcher_free(m);
}
```

解说：这一段实现测试函数 `test_lex`，通过构造输入、调用目标接口并检查结果，验证相关功能是否按预期工作。

## 第 5 段（第 53-85 行）

```cpp
int main(int argc, char **argv)
{
#if 0
   char *p;
   p = (char*) "abcdefghijklmnopqrstuvwxyz";
   c(stb_ischar('c', p), "stb_ischar 1");
   c(stb_ischar('x', p), "stb_ischar 2");
   c(!stb_ischar('#', p), "stb_ischar 3");
   c(!stb_ischar('X', p), "stb_ischar 4");
   p = (char*) "0123456789";
   c(!stb_ischar('c', p), "stb_ischar 5");
   c(!stb_ischar('x', p), "stb_ischar 6");
   c(!stb_ischar('#', p), "stb_ischar 7");
   c(!stb_ischar('X', p), "stb_ischar 8");
   p = (char*) "#####";
   c(!stb_ischar('c', p), "stb_ischar a");
   c(!stb_ischar('x', p), "stb_ischar b");
   c(stb_ischar('#', p), "stb_ischar c");
   c(!stb_ischar('X', p), "stb_ischar d");
   p = (char*) "xXyY";
   c(!stb_ischar('c', p), "stb_ischar e");
   c(stb_ischar('x', p), "stb_ischar f");
   c(!stb_ischar('#', p), "stb_ischar g");
   c(stb_ischar('X', p), "stb_ischar h");
#endif

   test_lex();

   if (count) {
      _getch();
   }
   return 0;
}
```

解说：这一段是程序入口，负责准备测试或工具运行所需的参数、调用核心逻辑，并把执行结果转换成进程退出状态。
