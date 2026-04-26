# stb_glprog.h 代码解说

源文件：`tests/caveview/stb_glprog.h`

本文按源文件中的自然代码段切分，每段先给出原始代码，再用中文解释这段代码在做什么以及它为什么存在。

## 第 1 段（第 1-45 行）

```c
// stb_glprog v0.02 public domain         functions to reduce GLSL boilerplate
// http://nothings.org/stb/stb_glprog.h   especially with GL1 + ARB extensions
//
// Following defines *before* including have following effects:
//
//     STB_GLPROG_IMPLEMENTATION
//           creates the implementation
//
//     STB_GLPROG_STATIC
//           forces the implementation to be static (private to file that creates it)
//
//     STB_GLPROG_ARB
//           uses ARB extension names for GLSL functions and enumerants instead of core names
//
//     STB_GLPROG_ARB_DEFINE_EXTENSIONS
//           instantiates function pointers needed, static to implementing file
//           to avoid collisions (but will collide if implementing file also
//           defines any; best to isolate this to its own file in this case).
//           This will try to automatically #include glext.h, but if it's not
//           in the default include directories you'll need to include it
//           yourself and define the next macro.
//
//     STB_GLPROG_SUPPRESS_GLEXT_INCLUDE
//           disables the automatic #include of glext.h which is normally
//           forced by STB_GLPROG_ARB_DEFINE_EXTENSIONS
//
// So, e.g., sample usage on an old Windows compiler:
//
//     #define STB_GLPROG_IMPLEMENTATION
//     #define STB_GLPROG_ARB_DEFINE_EXTENSIONS
//     #include <windows.h>
//     #include "gl/gl.h"
//     #include "stb_glprog.h"
//
// Note though that the header-file version of this (when you don't define
// STB_GLPROG_IMPLEMENTATION) still uses GLint and such, so you basically
// can only include it in places where you're already including GL, especially
// on Windows where including "gl.h" requires (some of) "windows.h".
//
// See following comment blocks for function documentation.
//
// Version history:
//    2013-12-08   v0.02   slightly simplified API and reduced GL resource usage (@rygorous)
//    2013-12-08   v0.01   initial release
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 2 段（第 46-50 行）

```c

// header file section starts here
#if !defined(INCLUDE_STB_GLPROG_H)
#define INCLUDE_STB_GLPROG_H
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 3 段（第 51-152 行）

```c
#ifndef STB_GLPROG_STATIC
#ifdef __cplusplus
extern "C" {
#endif

//////////////////////////////////////////////////////////////////////////////

/////////////    SHADER CREATION


/// EASY API

extern GLuint stbgl_create_program(char const **vertex_source, char const **frag_source, char const **binds, char *error, int error_buflen);
// This function returns a compiled program or 0 if there's an error.
// To free the created program, call stbgl_delete_program.
//
//     stbgl_create_program(
//             char **vertex_source,   // NULL or one or more strings with the vertex shader source, with a final NULL
//             char **frag_source,     // NULL or one or more strings with the fragment shader source, with a final NULL
//             char **binds,           // NULL or zero or more strings with attribute bind names, with a final NULL
//             char *error,            // output location where compile error message is placed
//             int error_buflen)       // length of error output buffer
//
// Returns a GLuint with the GL program object handle.
//
// If an individual bind string is "", no name is bound to that slot (this
// allows you to create binds that aren't continuous integers starting at 0).
//
// If the vertex shader is NULL, then fixed-function vertex pipeline
// is used, if that's legal in your version of GL.
//
// If the fragment shader is NULL, then fixed-function fragment pipeline
// is used, if that's legal in your version of GL.

extern void stgbl_delete_program(GLuint program);
// deletes a program created by stbgl_create_program or stbgl_link_program


/// FLEXIBLE API

extern GLuint stbgl_compile_shader(GLenum type, char const **sources, int num_sources, char *error, int error_buflen);
// compiles a shader. returns the shader on success or 0 on failure.
//
//    type          either:  GL_VERTEX_SHADER or GL_FRAGMENT_SHADER
//                     or    GL_VERTEX_SHADER_ARB or GL_FRAGMENT_SHADER_ARB
//                     or    STBGL_VERTEX_SHADER or STBGL_FRAGMENT_SHADER
//    sources       array of strings containing the shader source
//    num_sources   number of string in sources, or -1 meaning sources is NULL-terminated
//    error         string to output compiler error to
//    error_buflen  length of error buffer in chars

extern GLuint stbgl_link_program(GLuint vertex_shader, GLuint fragment_shader, char const **binds, int num_binds, char *error, int error_buflen);
// links a shader. returns the linked program on success or 0 on failure.
//
//    vertex_shader    a compiled vertex shader from stbgl_compile_shader, or 0 for fixed-function (if legal)
//    fragment_shader  a compiled fragment shader from stbgl_compile_shader, or 0 for fixed-function (if legal)
//

extern void stbgl_delete_shader(GLuint shader);
// deletes a shader created by stbgl_compile_shader


/////////////    RENDERING WITH SHADERS

extern GLint stbgl_find_uniform(GLuint prog, char *uniform);

extern void stbgl_find_uniforms(GLuint prog, GLint *locations, char const **uniforms, int num_uniforms);
// Given the locations array that is num_uniforms long, fills out
// the locations of each of those uniforms for the specified program.
// If num_uniforms is -1, then uniforms[] must be NULL-terminated

// the following functions just wrap the difference in naming between GL2+ and ARB,
// so you don't need them unless you're using both ARB and GL2+ in the same codebase,
// or you're relying on this lib to provide the extensions
extern void stbglUseProgram(GLuint program);
extern void stbglVertexAttribPointer(GLuint index,  GLint size,  GLenum type,  GLboolean normalized,  GLsizei stride,  const GLvoid * pointer);
extern void stbglEnableVertexAttribArray(GLuint index);
extern void stbglDisableVertexAttribArray(GLuint index);
extern void stbglUniform1fv(GLint loc, GLsizei count, const GLfloat *v);
extern void stbglUniform2fv(GLint loc, GLsizei count, const GLfloat *v);
extern void stbglUniform3fv(GLint loc, GLsizei count, const GLfloat *v);
extern void stbglUniform4fv(GLint loc, GLsizei count, const GLfloat *v);
extern void stbglUniform1iv(GLint loc, GLsizei count, const GLint *v);
extern void stbglUniform2iv(GLint loc, GLsizei count, const GLint *v);
extern void stbglUniform3iv(GLint loc, GLsizei count, const GLint *v);
extern void stbglUniform4iv(GLint loc, GLsizei count, const GLint *v);
extern void stbglUniform1f(GLint loc, float v0);
extern void stbglUniform2f(GLint loc, float v0, float v1);
extern void stbglUniform3f(GLint loc, float v0, float v1, float v2);
extern void stbglUniform4f(GLint loc, float v0, float v1, float v2, float v3);
extern void stbglUniform1i(GLint loc, GLint v0);
extern void stbglUniform2i(GLint loc, GLint v0, GLint v1);
extern void stbglUniform3i(GLint loc, GLint v0, GLint v1, GLint v2);
extern void stbglUniform4i(GLint loc, GLint v0, GLint v1, GLint v2, GLint v3);


//////////////     END OF FUNCTIONS

//////////////////////////////////////////////////////////////////////////////

#ifdef __cplusplus
}
```

解说：这一段涉及动态内存管理，负责申请、调整或释放运行时数据结构需要的存储空间。

## 第 4 段（第 153-155 行）

```c
#endif
#endif // STB_GLPROG_STATIC
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 5 段（第 156-163 行）

```c
#ifdef STB_GLPROG_ARB
#define STBGL_VERTEX_SHADER    GL_VERTEX_SHADER_ARB
#define STBGL_FRAGMENT_SHADER  GL_FRAGMENT_SHADER_ARB
#else
#define STBGL_VERTEX_SHADER    GL_VERTEX_SHADER
#define STBGL_FRAGMENT_SHADER  GL_FRAGMENT_SHADER
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 6 段（第 164-168 行）

```c
#endif // INCLUDE_STB_GLPROG_H


/////////    header file section ends here
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 7 段（第 170-172 行）

```c
#ifdef STB_GLPROG_IMPLEMENTATION
#include <string.h> // strncpy
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 8 段（第 173-178 行）

```c
#ifdef STB_GLPROG_STATIC
#define STB_GLPROG_DECLARE static
#else
#define STB_GLPROG_DECLARE extern
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 9 段（第 179-182 行）

```c
// check if user wants this file to define the GL extensions itself
#ifdef STB_GLPROG_ARB_DEFINE_EXTENSIONS
#define STB_GLPROG_ARB  // make sure later code uses the extensions
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 10 段（第 183-186 行）

```c
#ifndef STB_GLPROG_SUPPRESS_GLEXT_INCLUDE
#include "glext.h"
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 11 段（第 187-221 行）

```c
#define STB_GLPROG_EXTENSIONS                                 \
   STB_GLPROG_FUNC(ATTACHOBJECT        , AttachObject        ) \
   STB_GLPROG_FUNC(BINDATTRIBLOCATION  , BindAttribLocation  ) \
   STB_GLPROG_FUNC(COMPILESHADER       , CompileShader       ) \
   STB_GLPROG_FUNC(CREATEPROGRAMOBJECT , CreateProgramObject ) \
   STB_GLPROG_FUNC(CREATESHADEROBJECT  , CreateShaderObject  ) \
   STB_GLPROG_FUNC(DELETEOBJECT        , DeleteObject        ) \
   STB_GLPROG_FUNC(DETACHOBJECT        , DetachObject        ) \
   STB_GLPROG_FUNC(DISABLEVERTEXATTRIBARRAY, DisableVertexAttribArray) \
   STB_GLPROG_FUNC(ENABLEVERTEXATTRIBARRAY,  EnableVertexAttribArray ) \
   STB_GLPROG_FUNC(GETATTACHEDOBJECTS  , GetAttachedObjects  ) \
   STB_GLPROG_FUNC(GETOBJECTPARAMETERIV, GetObjectParameteriv) \
   STB_GLPROG_FUNC(GETINFOLOG          , GetInfoLog          ) \
   STB_GLPROG_FUNC(GETUNIFORMLOCATION  , GetUniformLocation  ) \
   STB_GLPROG_FUNC(LINKPROGRAM         , LinkProgram         ) \
   STB_GLPROG_FUNC(SHADERSOURCE        , ShaderSource        ) \
   STB_GLPROG_FUNC(UNIFORM1F           , Uniform1f           ) \
   STB_GLPROG_FUNC(UNIFORM2F           , Uniform2f           ) \
   STB_GLPROG_FUNC(UNIFORM3F           , Uniform3f           ) \
   STB_GLPROG_FUNC(UNIFORM4F           , Uniform4f           ) \
   STB_GLPROG_FUNC(UNIFORM1I           , Uniform1i           ) \
   STB_GLPROG_FUNC(UNIFORM2I           , Uniform2i           ) \
   STB_GLPROG_FUNC(UNIFORM3I           , Uniform3i           ) \
   STB_GLPROG_FUNC(UNIFORM4I           , Uniform4i           ) \
   STB_GLPROG_FUNC(UNIFORM1FV          , Uniform1fv          ) \
   STB_GLPROG_FUNC(UNIFORM2FV          , Uniform2fv          ) \
   STB_GLPROG_FUNC(UNIFORM3FV          , Uniform3fv          ) \
   STB_GLPROG_FUNC(UNIFORM4FV          , Uniform4fv          ) \
   STB_GLPROG_FUNC(UNIFORM1IV          , Uniform1iv          ) \
   STB_GLPROG_FUNC(UNIFORM2IV          , Uniform2iv          ) \
   STB_GLPROG_FUNC(UNIFORM3IV          , Uniform3iv          ) \
   STB_GLPROG_FUNC(UNIFORM4IV          , Uniform4iv          ) \
   STB_GLPROG_FUNC(USEPROGRAMOBJECT    , UseProgramObject    ) \
   STB_GLPROG_FUNC(VERTEXATTRIBPOINTER , VertexAttribPointer )
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 12 段（第 222-223 行）

```c
// define the static function pointers
```

解说：这一段是说明性注释，记录附近代码的用途、限制或维护提示，方便读者理解后续实现。

## 第 13 段（第 224-227 行）

```c
#define STB_GLPROG_FUNC(x,y)  static PFNGL##x##ARBPROC gl##y##ARB;
STB_GLPROG_EXTENSIONS
#undef STB_GLPROG_FUNC
```

解说：这一段定义宏（如 `STB_GLPROG_FUNC`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 14 段（第 228-229 行）

```c
// define the GetProcAddress
```

解说：这一段是说明性注释，记录附近代码的用途、限制或维护提示，方便读者理解后续实现。

## 第 15 段（第 230-241 行）

```c
#ifdef _WIN32
#ifndef WINGDIAPI
#ifndef STB__HAS_WGLPROC
typedef int (__stdcall *stbgl__voidfunc)(void);
static __declspec(dllimport) stbgl__voidfunc wglGetProcAddress(char *);
#endif
#endif
#define STBGL__GET_FUNC(x)   wglGetProcAddress(x)
#else
#error "need to define how this platform gets extensions"
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 16 段（第 242-243 行）

```c
// create a function that fills out the function pointers
```

解说：这一段是说明性注释，记录附近代码的用途、限制或维护提示，方便读者理解后续实现。

## 第 17 段（第 244-253 行）

```c
static void stb_glprog_init(void)
{
   static int initialized = 0; // not thread safe!
   if (initialized) return;
   #define STB_GLPROG_FUNC(x,y) gl##y##ARB = (PFNGL##x##ARBPROC) STBGL__GET_FUNC("gl" #y "ARB");
   STB_GLPROG_EXTENSIONS
   #undef STB_GLPROG_FUNC
}
#undef STB_GLPROG_EXTENSIONS
```

解说：这一段实现或声明库接口 `stb_glprog_init`，把“glprog init”相关能力封装成可被外部或内部复用的函数。

## 第 18 段（第 254-259 行）

```c
#else
static void stb_glprog_init(void)
{
}
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 19 段（第 260-300 行）

```c

// define generic names for many of the gl functions or extensions for later use;
// note that in some cases there are two functions in core and one function in ARB
#ifdef STB_GLPROG_ARB
#define stbglCreateShader          glCreateShaderObjectARB
#define stbglDeleteShader          glDeleteObjectARB
#define stbglAttachShader          glAttachObjectARB
#define stbglDetachShader          glDetachObjectARB
#define stbglShaderSource          glShaderSourceARB
#define stbglCompileShader         glCompileShaderARB
#define stbglGetShaderStatus(a,b)  glGetObjectParameterivARB(a, GL_OBJECT_COMPILE_STATUS_ARB, b)
#define stbglGetShaderInfoLog      glGetInfoLogARB
#define stbglCreateProgram         glCreateProgramObjectARB
#define stbglDeleteProgram         glDeleteObjectARB
#define stbglLinkProgram           glLinkProgramARB
#define stbglGetProgramStatus(a,b) glGetObjectParameterivARB(a, GL_OBJECT_LINK_STATUS_ARB, b)
#define stbglGetProgramInfoLog     glGetInfoLogARB
#define stbglGetAttachedShaders    glGetAttachedObjectsARB
#define stbglBindAttribLocation    glBindAttribLocationARB
#define stbglGetUniformLocation    glGetUniformLocationARB
#define stbgl_UseProgram           glUseProgramObjectARB
#else
#define stbglCreateShader          glCreateShader
#define stbglDeleteShader          glDeleteShader
#define stbglAttachShader          glAttachShader
#define stbglDetachShader          glDetachShader
#define stbglShaderSource          glShaderSource
#define stbglCompileShader         glCompileShader
#define stbglGetShaderStatus(a,b)  glGetShaderiv(a, GL_COMPILE_STATUS, b)
#define stbglGetShaderInfoLog      glGetShaderInfoLog
#define stbglCreateProgram         glCreateProgram
#define stbglDeleteProgram         glDeleteProgram
#define stbglLinkProgram           glLinkProgram
#define stbglGetProgramStatus(a,b) glGetProgramiv(a, GL_LINK_STATUS, b)
#define stbglGetProgramInfoLog     glGetProgramInfoLog
#define stbglGetAttachedShaders    glGetAttachedShaders
#define stbglBindAttribLocation    glBindAttribLocation
#define stbglGetUniformLocation    glGetUniformLocation
#define stbgl_UseProgram           glUseProgram
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 20 段（第 301-317 行）

```c

// perform a safe strcat of 3 strings, given that we can't rely on portable snprintf
// if you need to break on error, this is the best place to place a breakpoint
static void stb_glprog_error(char *error, int error_buflen, char *str1, char *str2, char *str3)
{
   int n = strlen(str1);
   strncpy(error, str1, error_buflen);
   if (n < error_buflen && str2) {
      strncpy(error+n, str2, error_buflen - n);
      n += strlen(str2);
      if (n < error_buflen && str3) {
         strncpy(error+n, str3, error_buflen - n);
      }
   }
   error[error_buflen-1] = 0;
}
```

解说：这一段实现或声明库接口 `stb_glprog_error`，把“glprog error”相关能力封装成可被外部或内部复用的函数。

## 第 21 段（第 318-358 行）

```c
STB_GLPROG_DECLARE GLuint stbgl_compile_shader(GLenum type, char const **sources, int num_sources, char *error, int error_buflen)
{
   char *typename = (type == STBGL_VERTEX_SHADER ? "vertex" : "fragment");
   int len;
   GLint result;
   GLuint shader;

   // initialize the extensions if we haven't already
   stb_glprog_init();

   // allocate

   shader = stbglCreateShader(type);
   if (!shader) {
      stb_glprog_error(error, error_buflen, "Couldn't allocate shader object in stbgl_compile_shader for ", typename, NULL);
      return 0;
   }

   // compile

   // if num_sources is negative, assume source is NULL-terminated and count the non-NULL ones
   if (num_sources < 0)
      for (num_sources = 0; sources[num_sources] != NULL; ++num_sources)
         ;
   stbglShaderSource(shader, num_sources, sources, NULL);
   stbglCompileShader(shader);
   stbglGetShaderStatus(shader, &result);
   if (result)
      return shader;

   // errors

   stb_glprog_error(error, error_buflen, "Compile error for ", typename, " shader: ");
   len = strlen(error);
   if (len < error_buflen)
      stbglGetShaderInfoLog(shader, error_buflen-len, NULL, error+len);

   stbglDeleteShader(shader);
   return 0;
}
```

解说：这一段实现或声明库接口 `stbgl_compile_shader`，把“gl compile shader”相关能力封装成可被外部或内部复用的函数。

## 第 22 段（第 359-417 行）

```c
STB_GLPROG_DECLARE GLuint stbgl_link_program(GLuint vertex_shader, GLuint fragment_shader, char const **binds, int num_binds, char *error, int error_buflen)
{
   int len;
   GLint result;

   // allocate

   GLuint prog = stbglCreateProgram();
   if (!prog) {
      stb_glprog_error(error, error_buflen, "Couldn't allocate program object in stbgl_link_program", NULL, NULL);
      return 0;
   }

   // attach

   if (vertex_shader)
      stbglAttachShader(prog, vertex_shader);
   if (fragment_shader)
      stbglAttachShader(prog, fragment_shader);

   // attribute binds

   if (binds) {
      int i;
      // if num_binds is negative, then it is NULL terminated
      if (num_binds < 0)
         for (num_binds=0; binds[num_binds]; ++num_binds)
            ;
      for (i=0; i < num_binds; ++i)
         if (binds[i] && binds[i][0]) // empty binds can be NULL or ""
            stbglBindAttribLocation(prog, i, binds[i]);
   }

   // link

   stbglLinkProgram(prog);

   // detach

   if (vertex_shader)
      stbglDetachShader(prog, vertex_shader);
   if (fragment_shader)
      stbglDetachShader(prog, fragment_shader);

   // errors

   stbglGetProgramStatus(prog, &result);
   if (result)
      return prog;

   stb_glprog_error(error, error_buflen, "Link error: ", NULL, NULL);
   len = strlen(error);
   if (len < error_buflen)
      stbglGetProgramInfoLog(prog, error_buflen-len, NULL, error+len);

   stbglDeleteProgram(prog);
   return 0;   
}
```

解说：这一段实现或声明库接口 `stbgl_link_program`，把“gl link program”相关能力封装成可被外部或内部复用的函数。

## 第 23 段（第 418-432 行）

```c
STB_GLPROG_DECLARE GLuint stbgl_create_program(char const **vertex_source, char const **frag_source, char const **binds, char *error, int error_buflen)
{
   GLuint vertex, fragment, prog=0;
   vertex = stbgl_compile_shader(STBGL_VERTEX_SHADER, vertex_source, -1, error, error_buflen);
   if (vertex) {
      fragment = stbgl_compile_shader(STBGL_FRAGMENT_SHADER, frag_source, -1, error, error_buflen);
      if (fragment)
         prog = stbgl_link_program(vertex, fragment, binds, -1, error, error_buflen);
      if (fragment)
         stbglDeleteShader(fragment);
      stbglDeleteShader(vertex);
   }
   return prog;
}
```

解说：这一段实现或声明库接口 `stbgl_create_program`，把“gl create program”相关能力封装成可被外部或内部复用的函数。

## 第 24 段（第 433-437 行）

```c
STB_GLPROG_DECLARE void stbgl_delete_shader(GLuint shader)
{
   stbglDeleteShader(shader);
}
```

解说：这一段实现或声明库接口 `stbgl_delete_shader`，把“gl delete shader”相关能力封装成可被外部或内部复用的函数。

## 第 25 段（第 438-442 行）

```c
STB_GLPROG_DECLARE void stgbl_delete_program(GLuint program)
{
   stbglDeleteProgram(program);
}
```

解说：这一段实现或声明函数 `stgbl_delete_program`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 26 段（第 443-447 行）

```c
GLint stbgl_find_uniform(GLuint prog, char *uniform)
{
   return stbglGetUniformLocation(prog, uniform);
}
```

解说：这一段实现或声明库接口 `stbgl_find_uniform`，把“gl find uniform”相关能力封装成可被外部或内部复用的函数。

## 第 27 段（第 448-456 行）

```c
STB_GLPROG_DECLARE void stbgl_find_uniforms(GLuint prog, GLint *locations, char const **uniforms, int num_uniforms)
{
   int i;
   if (num_uniforms < 0)
      num_uniforms = 999999;
   for (i=0; i < num_uniforms && uniforms[i]; ++i)
      locations[i] = stbglGetUniformLocation(prog, uniforms[i]);
}
```

解说：这一段实现或声明库接口 `stbgl_find_uniforms`，把“gl find uniforms”相关能力封装成可被外部或内部复用的函数。

## 第 28 段（第 457-461 行）

```c
STB_GLPROG_DECLARE void stbglUseProgram(GLuint program)
{
   stbgl_UseProgram(program);
}
```

解说：这一段实现或声明库接口 `stbglUseProgram`，把“glUseProgram”相关能力封装成可被外部或内部复用的函数。

## 第 29 段（第 462-467 行）

```c
#ifdef STB_GLPROG_ARB
#define STBGL_ARBIFY(name)   name##ARB
#else
#define STBGL_ARBIFY(name)   name
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 30 段（第 468-472 行）

```c
STB_GLPROG_DECLARE void stbglVertexAttribPointer(GLuint index,  GLint size,  GLenum type,  GLboolean normalized,  GLsizei stride,  const GLvoid * pointer)
{
   STBGL_ARBIFY(glVertexAttribPointer)(index, size, type, normalized, stride, pointer);
}
```

解说：这一段实现或声明库接口 `stbglVertexAttribPointer`，把“glVertexAttribPointer”相关能力封装成可被外部或内部复用的函数。

## 第 31 段（第 473-475 行）

```c
STB_GLPROG_DECLARE void stbglEnableVertexAttribArray (GLuint index) { STBGL_ARBIFY(glEnableVertexAttribArray )(index); }
STB_GLPROG_DECLARE void stbglDisableVertexAttribArray(GLuint index) { STBGL_ARBIFY(glDisableVertexAttribArray)(index); }
```

解说：这一段实现或声明库接口 `stbglEnableVertexAttribArray`，把“glEnableVertexAttribArray”相关能力封装成可被外部或内部复用的函数。

## 第 32 段（第 476-480 行）

```c
STB_GLPROG_DECLARE void stbglUniform1fv(GLint loc, GLsizei count, const GLfloat *v) { STBGL_ARBIFY(glUniform1fv)(loc,count,v); }
STB_GLPROG_DECLARE void stbglUniform2fv(GLint loc, GLsizei count, const GLfloat *v) { STBGL_ARBIFY(glUniform2fv)(loc,count,v); }
STB_GLPROG_DECLARE void stbglUniform3fv(GLint loc, GLsizei count, const GLfloat *v) { STBGL_ARBIFY(glUniform3fv)(loc,count,v); }
STB_GLPROG_DECLARE void stbglUniform4fv(GLint loc, GLsizei count, const GLfloat *v) { STBGL_ARBIFY(glUniform4fv)(loc,count,v); }
```

解说：这一段实现或声明库接口 `stbglUniform1fv`，把“glUniform1fv”相关能力封装成可被外部或内部复用的函数。

## 第 33 段（第 481-485 行）

```c
STB_GLPROG_DECLARE void stbglUniform1iv(GLint loc, GLsizei count, const GLint   *v) { STBGL_ARBIFY(glUniform1iv)(loc,count,v); }
STB_GLPROG_DECLARE void stbglUniform2iv(GLint loc, GLsizei count, const GLint   *v) { STBGL_ARBIFY(glUniform2iv)(loc,count,v); }
STB_GLPROG_DECLARE void stbglUniform3iv(GLint loc, GLsizei count, const GLint   *v) { STBGL_ARBIFY(glUniform3iv)(loc,count,v); }
STB_GLPROG_DECLARE void stbglUniform4iv(GLint loc, GLsizei count, const GLint   *v) { STBGL_ARBIFY(glUniform4iv)(loc,count,v); }
```

解说：这一段实现或声明库接口 `stbglUniform1iv`，把“glUniform1iv”相关能力封装成可被外部或内部复用的函数。

## 第 34 段（第 486-494 行）

```c
STB_GLPROG_DECLARE void stbglUniform1f(GLint loc, float v0)
    { STBGL_ARBIFY(glUniform1f)(loc,v0); }
STB_GLPROG_DECLARE void stbglUniform2f(GLint loc, float v0, float v1)
    { STBGL_ARBIFY(glUniform2f)(loc,v0,v1); }
STB_GLPROG_DECLARE void stbglUniform3f(GLint loc, float v0, float v1, float v2)
    { STBGL_ARBIFY(glUniform3f)(loc,v0,v1,v2); }
STB_GLPROG_DECLARE void stbglUniform4f(GLint loc, float v0, float v1, float v2, float v3)
    { STBGL_ARBIFY(glUniform4f)(loc,v0,v1,v2,v3); }
```

解说：这一段实现或声明库接口 `stbglUniform1f`，把“glUniform1f”相关能力封装成可被外部或内部复用的函数。

## 第 35 段（第 495-503 行）

```c
STB_GLPROG_DECLARE void stbglUniform1i(GLint loc, GLint v0)
    { STBGL_ARBIFY(glUniform1i)(loc,v0); }
STB_GLPROG_DECLARE void stbglUniform2i(GLint loc, GLint v0, GLint v1)
    { STBGL_ARBIFY(glUniform2i)(loc,v0,v1); }
STB_GLPROG_DECLARE void stbglUniform3i(GLint loc, GLint v0, GLint v1, GLint v2)
    { STBGL_ARBIFY(glUniform3i)(loc,v0,v1,v2); }
STB_GLPROG_DECLARE void stbglUniform4i(GLint loc, GLint v0, GLint v1, GLint v2, GLint v3)
    { STBGL_ARBIFY(glUniform4i)(loc,v0,v1,v2,v3); }
```

解说：这一段实现或声明库接口 `stbglUniform1i`，把“glUniform1i”相关能力封装成可被外部或内部复用的函数。

## 第 36 段（第 504-504 行）

```c
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。
