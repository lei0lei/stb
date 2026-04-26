# stb_c_lexer_fuzzer.cpp 代码解说

源文件：`tests/stb_c_lexer_fuzzer.cpp`

本文按源文件中的自然代码段切分，每段先给出原始代码，再用中文解释这段代码在做什么以及它为什么存在。

## 第 1 段（第 1-20 行）

```cpp
#define STB_C_LEX_C_DECIMAL_INTS    Y  
#define STB_C_LEX_C_HEX_INTS        Y
#define STB_C_LEX_C_OCTAL_INTS      Y
#define STB_C_LEX_C_DECIMAL_FLOATS  Y
#define STB_C_LEX_C99_HEX_FLOATS    Y
#define STB_C_LEX_C_IDENTIFIERS     Y
#define STB_C_LEX_C_DQ_STRINGS      Y
#define STB_C_LEX_C_SQ_STRINGS      Y
#define STB_C_LEX_C_CHARS           Y
#define STB_C_LEX_C_COMMENTS        Y
#define STB_C_LEX_CPP_COMMENTS      Y
#define STB_C_LEX_C_COMPARISONS     Y
#define STB_C_LEX_C_LOGICAL         Y
#define STB_C_LEX_C_SHIFTS          Y 
#define STB_C_LEX_C_INCREMENTS      Y
#define STB_C_LEX_C_ARROW           Y
#define STB_C_LEX_EQUAL_ARROW       Y
#define STB_C_LEX_C_BITWISEEQ       Y
#define STB_C_LEX_C_ARITHEQ         Y
```

解说：这一段定义宏（如 `STB_C_LEX_C_DECIMAL_INTS, STB_C_LEX_C_HEX_INTS, STB_C_LEX_C_OCTAL_INTS, STB_C_LEX_C_DECIMAL_FLOATS, STB_C_LEX_C99_HEX_FLOATS`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 2 段（第 21-26 行）

```cpp
#define STB_C_LEX_PARSE_SUFFIXES    Y
#define STB_C_LEX_DECIMAL_SUFFIXES  "uUlL"
#define STB_C_LEX_HEX_SUFFIXES      "lL"
#define STB_C_LEX_OCTAL_SUFFIXES    "lL"
#define STB_C_LEX_FLOAT_SUFFIXES    "uulL"
```

解说：这一段定义宏（如 `STB_C_LEX_PARSE_SUFFIXES, STB_C_LEX_DECIMAL_SUFFIXES, STB_C_LEX_HEX_SUFFIXES, STB_C_LEX_OCTAL_SUFFIXES, STB_C_LEX_FLOAT_SUFFIXES`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 3 段（第 27-34 行）

```cpp
#define STB_C_LEX_0_IS_EOF             N
#define STB_C_LEX_INTEGERS_AS_DOUBLES  N
#define STB_C_LEX_MULTILINE_DSTRINGS   Y
#define STB_C_LEX_MULTILINE_SSTRINGS   Y
#define STB_C_LEX_USE_STDLIB           N
#define STB_C_LEX_DOLLAR_IDENTIFIER    Y
#define STB_C_LEX_FLOAT_NO_DECIMAL     Y
```

解说：这一段定义宏（如 `STB_C_LEX_0_IS_EOF, STB_C_LEX_INTEGERS_AS_DOUBLES, STB_C_LEX_MULTILINE_DSTRINGS, STB_C_LEX_MULTILINE_SSTRINGS, STB_C_LEX_USE_STDLIB`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 4 段（第 35-38 行）

```cpp
#define STB_C_LEX_DEFINE_ALL_TOKEN_NAMES  Y  
#define STB_C_LEX_DISCARD_PREPROCESSOR    Y  
#define STB_C_LEXER_DEFINITIONS
```

解说：这一段定义宏（如 `STB_C_LEX_DEFINE_ALL_TOKEN_NAMES, STB_C_LEX_DISCARD_PREPROCESSOR, STB_C_LEXER_DEFINITIONS`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 5 段（第 39-46 行）

```cpp
#define STB_C_LEXER_IMPLEMENTATION
#define STB_C_LEXER_SELF_TEST
#include "../stb_c_lexer.h"
#include <stdint.h>
#include <stdlib.h>
#include <string.h>
#include <stdio.h>
```

解说：这一段定义宏（如 `STB_C_LEXER_IMPLEMENTATION, STB_C_LEXER_SELF_TEST`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 6 段（第 48-74 行）

```cpp
extern "C" int LLVMFuzzerTestOneInput(const uint8_t* data, size_t size)
{
	if(size<3){
		return 0;
	}
	char *input_stream = (char *)malloc(size);
	if (input_stream == NULL){
		return 0;
	}
	memcpy(input_stream, data, size);

	stb_lexer lex;
	char *input_end = input_stream+size-1;
	char *store = (char *)malloc(0x10000);
	int len = 0x10000;
	
	stb_c_lexer_init(&lex, input_stream, input_end, store, len);
	while (stb_c_lexer_get_token(&lex)) {
		if (lex.token == CLEX_parse_error) {
			break;
		}
	}
	
	free(input_stream);
	free(store);
	return 0;
}
```

解说：这一段涉及动态内存管理，负责申请、调整或释放运行时数据结构需要的存储空间。
