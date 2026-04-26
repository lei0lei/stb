# glext_list.h 代码解说

源文件：`tests/caveview/glext_list.h`

本文按源文件中的自然代码段切分，每段先给出原始代码，再用中文解释这段代码在做什么以及它为什么存在。

## 第 1 段（第 1-8 行）

```c
GLARB(ActiveTexture,ACTIVETEXTURE)
GLARB(ClientActiveTexture,CLIENTACTIVETEXTURE)
GLARB(MultiTexCoord2f,MULTITEXCOORD2F)
GLEXT(TexImage3D,TEXIMAGE3D)
GLEXT(TexSubImage3D,TEXSUBIMAGE3D)
GLEXT(GenerateMipmap,GENERATEMIPMAP)
GLARB(DebugMessageCallback,DEBUGMESSAGECALLBACK)
```

解说：这一段实现或声明函数 `GLARB`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 2 段（第 9-22 行）

```c
GLCORE(VertexAttribIPointer,VERTEXATTRIBIPOINTER)

GLEXT(BindFramebuffer,BINDFRAMEBUFFER)
GLEXT(DeleteFramebuffers,DELETEFRAMEBUFFERS)
GLEXT(GenFramebuffers,GENFRAMEBUFFERS)
GLEXT(CheckFramebufferStatus,CHECKFRAMEBUFFERSTATUS)
GLEXT(FramebufferTexture2D,FRAMEBUFFERTEXTURE2D)
GLEXT(BindRenderBuffer,BINDRENDERBUFFER)
GLEXT(RenderbufferStorage,RENDERBUFFERSTORAGE)
GLEXT(GenRenderbuffers,GENRENDERBUFFERS)
GLEXT(BindRenderbuffer,BINDRENDERBUFFER)
GLEXT(FramebufferRenderbuffer,FRAMEBUFFERRENDERBUFFER)
GLEXT(GenerateMipmap,GENERATEMIPMAP)
```

解说：这一段实现或声明函数 `GLEXT`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 3 段（第 23-31 行）

```c
GLARB(BindBuffer   ,BINDBUFFER,)
GLARB(GenBuffers   ,GENBUFFERS   )
GLARB(DeleteBuffers,DELETEBUFFERS)
GLARB(BufferData   ,BUFFERDATA   )
GLARB(BufferSubData,BUFFERSUBDATA)
GLARB(MapBuffer    ,MAPBUFFER    )
GLARB(UnmapBuffer  ,UNMAPBUFFER  )
GLARB(TexBuffer    ,TEXBUFFER    )
```

解说：这一段实现或声明函数 `GLARB`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 4 段（第 32-34 行）

```c
GLEXT(NamedBufferStorage,NAMEDBUFFERSTORAGE)
GLE(BufferStorage,BUFFERSTORAGE)
GLE(GetStringi,GETSTRINGI)
```

解说：这一段实现或声明函数 `GLE`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。
