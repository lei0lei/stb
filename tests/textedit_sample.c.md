# textedit_sample.c 代码解说

源文件：`tests/textedit_sample.c`

本文按源文件中的自然代码段切分，每段先给出原始代码，再用中文解释这段代码在做什么以及它为什么存在。

## 第 1 段（第 1-2 行）

```c
// I haven't actually tested this yet, this is just to make sure it compiles
```

解说：这一段是说明性注释，记录附近代码的用途、限制或维护提示，方便读者理解后续实现。

## 第 2 段（第 3-6 行）

```c
#include <stdlib.h>
#include <string.h> // memmove
#include <ctype.h>  // isspace
```

解说：这一段引入编译所需的头文件或本项目内的依赖，使后面的类型、宏和函数声明能够被编译器识别。

## 第 3 段（第 7-12 行）

```c
#define STB_TEXTEDIT_CHARTYPE   char
#define STB_TEXTEDIT_STRING     text_control

// get the base type
#include "stb_textedit.h"
```

解说：这一段定义宏（如 `STB_TEXTEDIT_CHARTYPE, STB_TEXTEDIT_STRING`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 4 段（第 13-20 行）

```c
// define our editor structure
typedef struct
{
   char *string;
   int stringlen;
   STB_TexteditState state;
} text_control;
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 5 段（第 21-32 行）

```c
// define the functions we need
void layout_func(StbTexteditRow *row, STB_TEXTEDIT_STRING *str, int start_i)
{
   int remaining_chars = str->stringlen - start_i;
   row->num_chars = remaining_chars > 20 ? 20 : remaining_chars; // should do real word wrap here
   row->x0 = 0;
   row->x1 = 20; // need to account for actual size of characters
   row->baseline_y_delta = 1.25;
   row->ymin = -1;
   row->ymax =  0;
}
```

解说：这一段实现或声明函数 `layout_func`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 6 段（第 33-39 行）

```c
int delete_chars(STB_TEXTEDIT_STRING *str, int pos, int num)
{
   memmove(&str->string[pos], &str->string[pos+num], str->stringlen - (pos+num));
   str->stringlen -= num;
   return 1; // always succeeds
}
```

解说：这一段实现或声明函数 `delete_chars`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 7 段（第 40-48 行）

```c
int insert_chars(STB_TEXTEDIT_STRING *str, int pos, STB_TEXTEDIT_CHARTYPE *newtext, int num)
{
   str->string = realloc(str->string, str->stringlen + num);
   memmove(&str->string[pos+num], &str->string[pos], str->stringlen - pos);
   memcpy(&str->string[pos], newtext, num);
   str->stringlen += num;
   return 1; // always succeeds
}
```

解说：这一段实现或声明函数 `insert_chars`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 8 段（第 49-50 行）

```c
// define all the #defines needed
```

解说：这一段是说明性注释，记录附近代码的用途、限制或维护提示，方便读者理解后续实现。

## 第 9 段（第 51-52 行）

```c
#define KEYDOWN_BIT                    0x80000000
```

解说：这一段定义宏（如 `KEYDOWN_BIT`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 10 段（第 53-62 行）

```c
#define STB_TEXTEDIT_STRINGLEN(tc)     ((tc)->stringlen)
#define STB_TEXTEDIT_LAYOUTROW         layout_func
#define STB_TEXTEDIT_GETWIDTH(tc,n,i)  (1) // quick hack for monospaced
#define STB_TEXTEDIT_KEYTOTEXT(key)    (((key) & KEYDOWN_BIT) ? 0 : (key))
#define STB_TEXTEDIT_GETCHAR(tc,i)     ((tc)->string[i])
#define STB_TEXTEDIT_NEWLINE           '\n'
#define STB_TEXTEDIT_IS_SPACE(ch)      isspace(ch)
#define STB_TEXTEDIT_DELETECHARS       delete_chars
#define STB_TEXTEDIT_INSERTCHARS       insert_chars
```

解说：这一段定义宏（如 `STB_TEXTEDIT_STRINGLEN, STB_TEXTEDIT_LAYOUTROW, STB_TEXTEDIT_GETWIDTH, STB_TEXTEDIT_KEYTOTEXT, STB_TEXTEDIT_GETCHAR`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 11 段（第 63-82 行）

```c
#define STB_TEXTEDIT_K_SHIFT           0x40000000
#define STB_TEXTEDIT_K_CONTROL         0x20000000
#define STB_TEXTEDIT_K_LEFT            (KEYDOWN_BIT | 1) // actually use VK_LEFT, SDLK_LEFT, etc
#define STB_TEXTEDIT_K_RIGHT           (KEYDOWN_BIT | 2) // VK_RIGHT
#define STB_TEXTEDIT_K_UP              (KEYDOWN_BIT | 3) // VK_UP
#define STB_TEXTEDIT_K_DOWN            (KEYDOWN_BIT | 4) // VK_DOWN
#define STB_TEXTEDIT_K_LINESTART       (KEYDOWN_BIT | 5) // VK_HOME
#define STB_TEXTEDIT_K_LINEEND         (KEYDOWN_BIT | 6) // VK_END
#define STB_TEXTEDIT_K_TEXTSTART       (STB_TEXTEDIT_K_LINESTART | STB_TEXTEDIT_K_CONTROL)
#define STB_TEXTEDIT_K_TEXTEND         (STB_TEXTEDIT_K_LINEEND   | STB_TEXTEDIT_K_CONTROL)
#define STB_TEXTEDIT_K_DELETE          (KEYDOWN_BIT | 7) // VK_DELETE
#define STB_TEXTEDIT_K_BACKSPACE       (KEYDOWN_BIT | 8) // VK_BACKSPACE
#define STB_TEXTEDIT_K_UNDO            (KEYDOWN_BIT | STB_TEXTEDIT_K_CONTROL | 'z')
#define STB_TEXTEDIT_K_REDO            (KEYDOWN_BIT | STB_TEXTEDIT_K_CONTROL | 'y')
#define STB_TEXTEDIT_K_INSERT          (KEYDOWN_BIT | 9) // VK_INSERT
#define STB_TEXTEDIT_K_WORDLEFT        (STB_TEXTEDIT_K_LEFT  | STB_TEXTEDIT_K_CONTROL)
#define STB_TEXTEDIT_K_WORDRIGHT       (STB_TEXTEDIT_K_RIGHT | STB_TEXTEDIT_K_CONTROL)
#define STB_TEXTEDIT_K_PGUP            (KEYDOWN_BIT | 10) // VK_PGUP -- not implemented
#define STB_TEXTEDIT_K_PGDOWN          (KEYDOWN_BIT | 11) // VK_PGDOWN -- not implemented
```

解说：这一段定义宏（如 `STB_TEXTEDIT_K_SHIFT, STB_TEXTEDIT_K_CONTROL, STB_TEXTEDIT_K_LEFT, STB_TEXTEDIT_K_RIGHT, STB_TEXTEDIT_K_UP`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 12 段（第 83-85 行）

```c
#define STB_TEXTEDIT_IMPLEMENTATION
#include "stb_textedit.h"
```

解说：这一段定义宏（如 `STB_TEXTEDIT_IMPLEMENTATION`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 13 段（第 86-94 行）

```c
void dummy3(void)
{
  stb_textedit_click(0,0,0,0);
  stb_textedit_drag(0,0,0,0);
  stb_textedit_cut(0,0);
  stb_textedit_key(0,0,0);
  stb_textedit_initialize_state(0,0);
  stb_textedit_paste(0,0,0,0);
}
```

解说：这一段实现或声明函数 `dummy3`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。
