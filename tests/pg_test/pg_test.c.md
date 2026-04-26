# pg_test.c 代码解说

源文件：`tests/pg_test/pg_test.c`

本文按源文件中的自然代码段切分，每段先给出原始代码，再用中文解释这段代码在做什么以及它为什么存在。

## 第 1 段（第 1-9 行）

```c
#define STB_DEFINE
#include "stb.h"
#define STB_PG_IMPLEMENTATION
#include "stb_pg.h"
#define STB_IMAGE_IMPLEMENTATION
#include "stb_image.h"
#define STB_IMAGE_WRITE_IMPLEMENTATION
#include "stb_image_write.h"
```

解说：这一段定义宏（如 `STB_DEFINE, STB_PG_IMPLEMENTATION, STB_IMAGE_IMPLEMENTATION, STB_IMAGE_WRITE_IMPLEMENTATION`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 2 段（第 10-13 行）

```c
static float *hf;
static int hf_width = 10001;
static int hf_height = 10001;
```

解说：这一段进行变量初始化或状态更新，为后续计算准备输入数据，或保存当前步骤得到的中间结果。

## 第 3 段（第 14-32 行）

```c
static float get_height(float x, float y)
{
   float h00,h01,h10,h11,h0,h1;
   int ix,iy;
   if (x < 0) x = 0;
   if (x > hf_width-1) x = (float) hf_width-1;
   if (y < 0) y = 0;
   if (y > hf_height-1) y = (float) hf_height-1;
   ix = (int) x; x -= ix;
   iy = (int) y; y -= iy;
   h00 = hf[(iy+0)*hf_height+(ix+0)];
   h10 = hf[(iy+0)*hf_height+(ix+1)];
   h01 = hf[(iy+1)*hf_height+(ix+0)];
   h11 = hf[(iy+1)*hf_height+(ix+1)];
   h0 = stb_lerp(y, h00, h01);
   h1 = stb_lerp(y, h10, h11);
   return stb_lerp(x, h0, h1);
}
```

解说：这一段实现或声明函数 `get_height`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 4 段（第 33-100 行）

```c
void stbpg_tick(float dt)
{
   int i=0,j=0;
   int step = 1;

   glUseProgram(0);

   glClearColor(0.6f,0.7f,1.0f,1.0f);
   glClearDepth(1.0f);
   glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);

   glDepthFunc(GL_LESS);
   glEnable(GL_DEPTH_TEST);
#if 1
   glEnable(GL_CULL_FACE);

   glMatrixMode(GL_PROJECTION);
   glLoadIdentity();
   gluPerspective(60.0, 1920/1080.0f, 0.02f, 8000.0f);
   //glOrtho(-8,8,-6,6, -100, 100);

   glMatrixMode(GL_MODELVIEW);
   glLoadIdentity();
   glRotatef(-90, 1,0,0); // z-up

   {
      float x,y;
      stbpg_get_mouselook(&x,&y);
      glRotatef(-y, 1,0,0);
      glRotatef(-x, 0,0,1);
   }

   {
      static float cam_x = 1000;
      static float cam_y = 1000;
      static float cam_z = 700;
      float x=0,y=0;
      stbpg_get_keymove(&x,&y);
      cam_x += x*dt*5.0f;
      cam_y += y*dt*5.0f;
      glTranslatef(-cam_x, -cam_y, -cam_z);
      if (cam_x >= 0 && cam_x < hf_width && cam_y >= 0 && cam_y < hf_height)
         cam_z = get_height(cam_x, cam_y) + 1.65f; // average eye height in meters
   }

   for (j=501; j+1 < 1500+0*hf_height; j += step) {
      glBegin(GL_QUAD_STRIP);
      for (i=501; i < 1500+0*hf_width; i += step) {
         static int flip=0;
         if (flip)
            glColor3f(0.5,0.5,0.5);
         else
            glColor3f(0.4f,0.4f,0.4f);
         flip = !flip;
         glVertex3f((float) i, (float) j+step,hf[(j+step)*hf_width+i]);
         glVertex3f((float) i, (float) j   ,hf[  j    *hf_width+i]);
      }
      glEnd();
   }

   glBegin(GL_LINES);
      glColor3f(1,0,0); glVertex3f(10,0,0); glVertex3f(0,0,0);
      glColor3f(0,1,0); glVertex3f(0,10,0); glVertex3f(0,0,0);
      glColor3f(0,0,1); glVertex3f(0,0,10); glVertex3f(0,0,0);
   glEnd();
#endif
}
```

解说：这一段实现或声明库接口 `stbpg_tick`，把“pg tick”相关能力封装成可被外部或内部复用的函数。

## 第 5 段（第 101-124 行）

```c
void stbpg_main(int argc, char **argv)
{
   int i,j;

   #if 0
   int w,h,c;
   unsigned short *data = stbi_load_16("c:/x/ned_1m/test2.png", &w, &h, &c, 1);
   stb_filewrite("c:/x/ned_1m/x73_y428_10012_10012.bin", data, w*h*2);
   #else
   unsigned short *data = stb_file("c:/x/ned_1m/x73_y428_10012_10012.bin", NULL);
   int w=10012, h = 10012;
   #endif

   hf = malloc(hf_width * hf_height * 4);
   for (j=0; j < hf_height; ++j)
      for (i=0; i < hf_width; ++i)
         hf[j*hf_width+i] = data[j*w+i] / 32.0f;

   stbpg_gl_compat_version(1,1);   
   stbpg_windowed("terrain_edit", 1920, 1080);
   stbpg_run();

   return;
}
```

解说：这一段实现或声明库接口 `stbpg_main`，把“pg main”相关能力封装成可被外部或内部复用的函数。
