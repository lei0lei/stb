# stbi_read_fuzzer.c 代码解说

源文件：`tests/stbi_read_fuzzer.c`

本文按源文件中的自然代码段切分，每段先给出原始代码，再用中文解释这段代码在做什么以及它为什么存在。

## 第 1 段（第 1-28 行）

```c
#ifdef __cplusplus
extern "C" {
#endif

#define STB_IMAGE_IMPLEMENTATION

#include "../stb_image.h"


int LLVMFuzzerTestOneInput(const uint8_t* data, size_t size)
{
    int x, y, channels;

    if(!stbi_info_from_memory(data, size, &x, &y, &channels)) return 0;

    /* exit if the image is larger than ~80MB */
    if(y && x > (80000000 / 4) / y) return 0;

    unsigned char *img = stbi_load_from_memory(data, size, &x, &y, &channels, 4);

    free(img);

    return 0;
}

#ifdef __cplusplus
}
#endif
```

解说：这一段实现或声明函数 `LLVMFuzzerTestOneInput`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。
