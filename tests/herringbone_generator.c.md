# herringbone_generator.c 代码解说

源文件：`tests/herringbone_generator.c`

本文按源文件中的自然代码段切分，每段先给出原始代码，再用中文解释这段代码在做什么以及它为什么存在。

## 第 1 段（第 1-3 行）

```c
#define STB_HERRINGBONE_WANG_TILE_IMPLEMENTATION
#include "stb_herringbone_wang_tile.h"
```

解说：这一段定义宏（如 `STB_HERRINGBONE_WANG_TILE_IMPLEMENTATION`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 2 段（第 4-8 行）

```c
#define STB_IMAGE_WRITE_IMPLEMENTATION
#include "stb_image_write.h"

//  e 12 1 1 1 1 1 1 4 4
```

解说：这一段定义宏（如 `STB_IMAGE_WRITE_IMPLEMENTATION`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 3 段（第 9-87 行）

```c
int main(int argc, char **argv)
{
   stbhw_config c = { 0 };
   int w,h, num_colors,i;
   unsigned char *data;

   if (argc == 1)  goto usage;
   if (argc  < 3)  goto error;

   switch (argv[2][0]) {
      case 'c':
         if (argc <  8 || argc > 10)
            goto error;
         num_colors = 4;
         c.is_corner = 1;
         break;

      case 'e':
         if (argc < 10 || argc > 12)
            goto error;
         num_colors = 6;
         c.is_corner = 0;
         break;

      default:
         goto error;
   }

   c.short_side_len = atoi(argv[3]);
   for (i=0; i < num_colors; ++i)
      c.num_color[i] = atoi(argv[4+i]);

   c.num_vary_x = 1;
   c.num_vary_y = 1;

   if (argc > 4+i)
      c.num_vary_x = atoi(argv[4+i]);
   if (argc > 5+i)
      c.num_vary_y = atoi(argv[5+i]);

   stbhw_get_template_size(&c, &w, &h);

   data = (unsigned char *) malloc(w*h*3);

   if (stbhw_make_template(&c, data, w, h, w*3))
      stbi_write_png(argv[1], w, h, 3, data, w*3);
   else
      fprintf(stderr, "Error: %s\n", stbhw_get_last_error());
   return 0;

 error:
   fputs("Invalid command-line arguments\n\n", stderr);
 usage:
   fputs("Usage (see source for corner & edge type definitions):\n\n", stderr);
   fputs("herringbone_generator {outfile} c {sidelen} {c0} {c1} {c2} {c3} [{vx} {vy}]\n"
         "     {outfile}  -- filename that template will be written to as PNG\n"
         "     {sidelen}  -- length of short side of rectangle in pixels\n"
         "     {c0}       -- number of colors for corner type 0\n"
         "     {c1}       -- number of colors for corner type 1\n"
         "     {c2}       -- number of colors for corner type 2\n"
         "     {c3}       -- number of colors for corner type 3\n"
         "     {vx}       -- number of color-duplicating variations horizontally in template\n"
         "     {vy}       -- number of color-duplicating variations vertically in template\n"
         "\n"
         , stderr);
   fputs("herringbone_generator {outfile} e {sidelen} {e0} {e1} {e2} {e3} {e4} {e5} [{vx} {vy}]\n"
         "     {outfile}  -- filename that template will be written to as PNG\n"
         "     {sidelen}  -- length of short side of rectangle in pixels\n"
         "     {e0}       -- number of colors for edge type 0\n"
         "     {e1}       -- number of colors for edge type 1\n"
         "     {e2}       -- number of colors for edge type 2\n"
         "     {e3}       -- number of colors for edge type 3\n"
         "     {e4}       -- number of colors for edge type 4\n"
         "     {e5}       -- number of colors for edge type 5\n"
         "     {vx}       -- number of color-duplicating variations horizontally in template\n"
         "     {vy}       -- number of color-duplicating variations vertically in template\n"
         , stderr);
   return 1;
}
```

解说：这一段是程序入口，负责准备测试或工具运行所需的参数、调用核心逻辑，并把执行结果转换成进程退出状态。
