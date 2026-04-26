# stb_static.c 代码解说

源文件：`tests/stb_static.c`

本文按源文件中的自然代码段切分，每段先给出原始代码，再用中文解释这段代码在做什么以及它为什么存在。

## 第 1 段（第 1-5 行）

```c
#define STBI_WINDOWS_UTF8
#define STB_IMAGE_STATIC
#define STB_IMAGE_IMPLEMENTATION
#include "stb_image.h"
```

解说：这一段定义宏（如 `STBI_WINDOWS_UTF8, STB_IMAGE_STATIC, STB_IMAGE_IMPLEMENTATION`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 2 段（第 6-9 行）

```c
#define STB_IMAGE_WRITE_STATIC
#define STB_IMAGE_WRITE_IMPLEMENTATION
//#include "stb_image_write.h"
```

解说：这一段定义宏（如 `STB_IMAGE_WRITE_STATIC, STB_IMAGE_WRITE_IMPLEMENTATION`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 3 段（第 10-12 行）

```c
#define STBTT_STATIC
#define STB_TRUETYPE_IMPLEMENTATION
#include "stb_truetype.h"
```

解说：这一段定义宏（如 `STBTT_STATIC, STB_TRUETYPE_IMPLEMENTATION`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。
