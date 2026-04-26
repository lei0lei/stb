# vorbseek.c 代码解说

源文件：`tests/vorbseek/vorbseek.c`

本文按源文件中的自然代码段切分，每段先给出原始代码，再用中文解释这段代码在做什么以及它为什么存在。

## 第 1 段（第 1-5 行）

```c
#include <assert.h>
#include <string.h>
#include <stdlib.h>
#include <stdio.h>
```

解说：这一段引入编译所需的头文件或本项目内的依赖，使后面的类型、宏和函数声明能够被编译器识别。

## 第 2 段（第 6-8 行）

```c
#define STB_VORBIS_HEADER_ONLY
#include "stb_vorbis.c"
```

解说：这一段定义宏（如 `STB_VORBIS_HEADER_ONLY`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 3 段（第 9-10 行）

```c
#define SAMPLES_TO_TEST 3000
```

解说：这一段定义宏（如 `SAMPLES_TO_TEST`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 4 段（第 11-13 行）

```c
int test_count  [5] = { 5000, 3000, 2000, 50000, 50000 };
int test_spacing[5] = {    1,  111, 3337,  7779, 72717 };
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 5 段（第 14-54 行）

```c
int try_seeking(stb_vorbis *v, unsigned int pos, short *output, unsigned int num_samples)
{
   int count;
   short samples[SAMPLES_TO_TEST*2];
   assert(pos <= num_samples);

   if (!stb_vorbis_seek(v, pos)) {
      fprintf(stderr, "Seek to %u returned error from stb_vorbis\n", pos);
      return 0;
   }

   count = stb_vorbis_get_samples_short_interleaved(v, 2, samples, SAMPLES_TO_TEST*2);

   if (count > (int) (num_samples - pos)) {
      fprintf(stderr, "Seek to %u allowed decoding %d samples when only %d should have been valid.\n",
            pos, count, (int) (num_samples - pos));
      return 0;
   }

   if (count < SAMPLES_TO_TEST && count < (int) (num_samples - pos)) {
      fprintf(stderr, "Seek to %u only decoded %d samples of %d attempted when at least %d should have been valid.\n",
         pos, count, SAMPLES_TO_TEST, num_samples - pos);
      return 0;                      
   }

   if (0 != memcmp(samples, output + pos*2, count*2)) {
      int k;
      for (k=0; k < SAMPLES_TO_TEST*2; ++k) {
         if (samples[k] != output[k]) {
            fprintf(stderr, "Seek to %u produced incorrect samples starting at sample %u (short #%d in buffer).\n",
                    pos, pos + (k/2), k);
            break;
         }
      }
      assert(k != SAMPLES_TO_TEST*2);
      return 0;
   }

   return 1;
}
```

解说：这一段实现或声明函数 `try_seeking`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 6 段（第 55-125 行）

```c
int main(int argc, char **argv)
{
   int num_chan, samprate;
   int i, j, test, phase;
   short *output;

   if (argc == 1) {
      fprintf(stderr, "Usage: vorbseek {vorbisfile} [{vorbisfile]*]\n");
      fprintf(stderr, "Tests various seek offsets to make sure they're sample exact.\n");
      return 0;
   }

   #if 0
   {
      // check that outofmem occurs correctly
      stb_vorbis_alloc va;
      va.alloc_buffer = malloc(1024*1024);
      for (i=0; i < 1024*1024; i += 10) {
         int error=0;
         stb_vorbis *v;
         va.alloc_buffer_length_in_bytes = i;
         v = stb_vorbis_open_filename(argv[1], &error, &va);
         if (v != NULL)
            break;
         printf("Error %d at %d\n", error, i);
      }
   }
   #endif

   for (j=1; j < argc; ++j) {
      unsigned int successes=0, attempts = 0;
      unsigned int num_samples = stb_vorbis_decode_filename(argv[j], &num_chan, &samprate, &output);

      break;

      if (num_samples == 0xffffffff) {
         fprintf(stderr, "Error: couldn't open file or not vorbis file: %s\n", argv[j]);
         goto fail;
      }

      if (num_chan != 2) {
         fprintf(stderr, "vorbseek testing only works with files with 2 channels, %s has %d\n", argv[j], num_chan);
         goto fail;
      }

      for (test=0; test < 5; ++test) {
         int error;
         stb_vorbis *v = stb_vorbis_open_filename(argv[j], &error, NULL);
         if (v == NULL) {
            fprintf(stderr, "Couldn't re-open %s for test #%d\n", argv[j], test);
            goto fail;
         }
         for (phase=0; phase < 3; ++phase) {
            unsigned int base = phase == 0 ? 0 : phase == 1 ? num_samples - test_count[test]*test_spacing[test] : num_samples/3;
            for (i=0; i < test_count[test]; ++i) {
               unsigned int pos = base + i*test_spacing[test];
               if (pos > num_samples) // this also catches underflows
                  continue;
               successes += try_seeking(v, pos, output, num_samples);
               attempts += 1;
            }
         }
         stb_vorbis_close(v);
      }
      printf("%d of %d seeks failed in %s (%d samples)\n", attempts-successes, attempts, argv[j], num_samples);
      free(output);
   }
   return 0;
  fail:
   return 1;
}
```

解说：这一段是程序入口，负责准备测试或工具运行所需的参数、调用核心逻辑，并把执行结果转换成进程退出状态。
