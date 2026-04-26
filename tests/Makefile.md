# Makefile 代码解说

源文件：`tests/Makefile`

本文按源文件中的自然代码段切分，每段先给出原始代码，再用中文解释这段代码在做什么以及它为什么存在。

## 第 1 段（第 1-4 行）

```makefile
INCLUDES = -I..
CFLAGS = -Wno-pointer-to-int-cast -Wno-int-to-pointer-cast -DSTB_DIVIDE_TEST
CPPFLAGS = -Wno-write-strings -DSTB_DIVIDE_TEST
```

解说：这一段进行变量初始化或状态更新，为后续计算准备输入数据，或保存当前步骤得到的中间结果。

## 第 2 段（第 5-12 行）

```makefile
# Uncomment this line for reproducing OSS-Fuzz bugs with image_fuzzer
#CFLAGS += -O -fsanitize=address 

all:
	$(CC) $(INCLUDES) $(CFLAGS) ../stb_vorbis.c test_c_compilation.c test_c_lexer.c test_dxt.c test_easyfont.c test_image.c test_image_write.c test_perlin.c test_sprintf.c test_truetype.c test_voxel.c -lm
	$(CC) $(INCLUDES) $(CPPFLAGS) -std=c++0x test_cpp_compilation.cpp -lm -lstdc++
	$(CC) $(INCLUDES) $(CFLAGS) -DIWT_TEST image_write_test.c -lm -o image_write_test
	$(CC) $(INCLUDES) $(CFLAGS) fuzz_main.c stbi_read_fuzzer.c -lm -o image_fuzzer
```

解说：这一段进行变量初始化或状态更新，为后续计算准备输入数据，或保存当前步骤得到的中间结果。
