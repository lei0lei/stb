# truetype_test_win32.c 代码解说

源文件：`tests/truetype_test_win32.c`

本文按源文件中的自然代码段切分，每段先给出原始代码，再用中文解释这段代码在做什么以及它为什么存在。

## 第 1 段（第 1-5 行）

```c
// tested in VC6 (1998) and VS 2019
#define _CRT_SECURE_NO_WARNINGS
#define WIN32_MEAN_AND_LEAN
#include <windows.h>
```

解说：这一段定义宏（如 `_CRT_SECURE_NO_WARNINGS, WIN32_MEAN_AND_LEAN`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 2 段（第 6-8 行）

```c
#include <stdio.h>
#include <tchar.h>
```

解说：这一段引入编译所需的头文件或本项目内的依赖，使后面的类型、宏和函数声明能够被编译器识别。

## 第 3 段（第 9-11 行）

```c
#define STB_TRUETYPE_IMPLEMENTATION
#include "stb_truetype.h"
```

解说：这一段定义宏（如 `STB_TRUETYPE_IMPLEMENTATION`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 4 段（第 12-14 行）

```c
#include <gl/gl.h>
#include <gl/glu.h>
```

解说：这一段引入编译所需的头文件或本项目内的依赖，使后面的类型、宏和函数声明能够被编译器识别。

## 第 5 段（第 15-17 行）

```c
int screen_x=1024, screen_y=768;
GLuint tex;
```

解说：这一段进行变量初始化或状态更新，为后续计算准备输入数据，或保存当前步骤得到的中间结果。

## 第 6 段（第 18-21 行）

```c
unsigned char ttf_buffer[1<<20];
unsigned char temp_bitmap[1024*1024];
stbtt_bakedchar cdata[96]; // ASCII 32..126 is 95 glyphs
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 7 段（第 22-31 行）

```c
void init(void)
{
   fread(ttf_buffer, 1, 1<<20, fopen("c:/windows/fonts/times.ttf", "rb"));
   stbtt_BakeFontBitmap(ttf_buffer,0, 64.0, temp_bitmap,1024,1024, 32,96, cdata);
   glGenTextures(1, &tex);
   glBindTexture(GL_TEXTURE_2D, tex);
   glTexImage2D(GL_TEXTURE_2D, 0, GL_ALPHA, 1024,1024,0, GL_ALPHA, GL_UNSIGNED_BYTE, temp_bitmap);
   glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
}
```

解说：这一段实现或声明函数 `init`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 8 段（第 32-50 行）

```c
void print(float x, float y, char *text)
{
   // assume orthographic projection with units = screen pixels, origin at top left
   glBindTexture(GL_TEXTURE_2D, tex);
   glBegin(GL_QUADS);
   while (*text) {
      if (*text >= 32 && *text < 128) {
         stbtt_aligned_quad q;
         stbtt_GetBakedQuad(cdata, 1024,1024, *text-32, &x,&y,&q,1);//1=opengl & d3d10+,0=d3d9
         glTexCoord2f(q.s0,q.t0); glVertex2f(q.x0,q.y0);
         glTexCoord2f(q.s1,q.t0); glVertex2f(q.x1,q.y0);
         glTexCoord2f(q.s1,q.t1); glVertex2f(q.x1,q.y1);
         glTexCoord2f(q.s0,q.t1); glVertex2f(q.x0,q.y1);
      }
      ++text;
   }
   glEnd();
}
```

解说：这一段实现或声明函数 `print`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 9 段（第 51-81 行）

```c
void draw(void)
{
   glViewport(0,0,screen_x,screen_y);
   glClearColor(0.45f,0.45f,0.75f,0);
   glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
   glDisable(GL_CULL_FACE);
   glDisable(GL_DEPTH_TEST);
   glDisable(GL_BLEND);

   glMatrixMode(GL_PROJECTION);
   glLoadIdentity();
   glOrtho(0,screen_x,screen_y,0,-1,1);
   glMatrixMode(GL_MODELVIEW);
   glLoadIdentity();

   glEnable(GL_TEXTURE_2D);
   glEnable(GL_BLEND);
   glBlendFunc(GL_SRC_ALPHA, GL_ONE_MINUS_SRC_ALPHA);
   glColor3f(1,1,1);

   print(100,150, "This is a simple test!");

   // show font bitmap
   glBegin(GL_QUADS);
      glTexCoord2f(0,0); glVertex2f(256,200+0);
      glTexCoord2f(1,0); glVertex2f(768,200+0);
      glTexCoord2f(1,1); glVertex2f(768,200+512);
      glTexCoord2f(0,1); glVertex2f(256,200+512);
   glEnd();
}
```

解说：这一段实现或声明函数 `draw`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 10 段（第 82-88 行）

```c
///////////////////////////////////////////////////////////////////////
///
///
///  Windows OpenGL setup
///
///
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 11 段（第 89-93 行）

```c
HINSTANCE app;
HWND  window;
HGLRC rc;
HDC   dc;
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 12 段（第 94-97 行）

```c
#pragma comment(lib, "opengl32.lib")
#pragma comment(lib, "glu32.lib")
#pragma comment(lib, "winmm.lib")
```

解说：这一段是预处理指令，负责在真正编译前组织符号、平台差异和条件代码。

## 第 13 段（第 98-114 行）

```c
int mySetPixelFormat(HWND win)
{
   PIXELFORMATDESCRIPTOR pfd = { sizeof(pfd), 1, PFD_SUPPORT_OPENGL | PFD_DRAW_TO_WINDOW | PFD_DOUBLEBUFFER, PFD_TYPE_RGBA };
   int                   pixel_format;
   pfd.dwLayerMask  = PFD_MAIN_PLANE;
   pfd.cColorBits   = 24;
   pfd.cAlphaBits   = 8;
   pfd.cDepthBits   = 24;
   pfd.cStencilBits = 8;
   pixel_format = ChoosePixelFormat(dc, &pfd);
   if (!pixel_format) return FALSE;
   if (!DescribePixelFormat(dc, pixel_format, sizeof(PIXELFORMATDESCRIPTOR), &pfd))
      return FALSE;
   SetPixelFormat(dc, pixel_format, &pfd);
   return TRUE;
}
```

解说：这一段实现或声明函数 `mySetPixelFormat`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 14 段（第 115-143 行）

```c
static int WINAPI WinProc(HWND wnd, UINT msg, WPARAM wparam, LPARAM lparam)
{
   switch (msg) {
      case WM_CREATE: {
         LPCREATESTRUCT lpcs = (LPCREATESTRUCT) lparam;
         dc = GetDC(wnd);
         if (mySetPixelFormat(wnd)) {
            rc = wglCreateContext(dc);
            if (rc) {
               wglMakeCurrent(dc, rc);
               return 0;
            }
         }
         return -1;
      }

      case WM_DESTROY:
         wglMakeCurrent(NULL, NULL); 
         if (rc) wglDeleteContext(rc);
         PostQuitMessage (0);
         return 0;

      default:
         return DefWindowProc (wnd, msg, wparam, lparam);
   }

   return DefWindowProc (wnd, msg, wparam, lparam);
}
```

解说：这一段实现或声明函数 `WinProc`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 15 段（第 144-184 行）

```c
int WINAPI WinMain(HINSTANCE hInstance, HINSTANCE hPrevInstance, LPSTR lpCmdLine, int nCmdShow)
{
   DWORD dwstyle = WS_OVERLAPPED | WS_CAPTION | WS_SYSMENU | WS_MINIMIZEBOX;
   WNDCLASSEX  wndclass;
   wndclass.cbSize        = sizeof(wndclass);
   wndclass.style         = CS_OWNDC;
   wndclass.lpfnWndProc   = (WNDPROC) WinProc;
   wndclass.cbClsExtra    = 0;
   wndclass.cbWndExtra    = 0;
   wndclass.hInstance     = hInstance;
   wndclass.hIcon         = LoadIcon(hInstance, _T("appicon"));
   wndclass.hCursor       = LoadCursor(NULL,IDC_ARROW);
   wndclass.hbrBackground = GetStockObject(NULL_BRUSH);
   wndclass.lpszMenuName  = _T("truetype-test");
   wndclass.lpszClassName = _T("truetype-test");
   wndclass.hIconSm       = NULL;
   app = hInstance;

   if (!RegisterClassEx(&wndclass))
      return 0;

   window = CreateWindow(_T("truetype-test"), _T("truetype test"), dwstyle,
                      CW_USEDEFAULT,0, screen_x, screen_y,
                      NULL, NULL, app,  NULL);
   ShowWindow(window, SW_SHOWNORMAL);
   init();

   for(;;) {
      MSG msg;
      if (GetMessage(&msg, NULL, 0, 0)) {
         TranslateMessage(&msg);
         DispatchMessage(&msg);
      } else {
         return 1;   // WM_QUIT
      }
      wglMakeCurrent(dc, rc);
      draw();
      SwapBuffers(dc);
   }
   return 0;
}
```

解说：这一段实现或声明函数 `WinMain`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。
