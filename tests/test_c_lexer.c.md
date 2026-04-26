# test_c_lexer.c 代码解说

源文件：`tests/test_c_lexer.c`

本文按源文件中的自然代码段切分，每段先给出原始代码，再用中文解释这段代码在做什么以及它为什么存在。

## 第 1 段（第 1-2 行）

```c
#include "stb_c_lexer.h"
```

解说：这一段引入编译所需的头文件或本项目内的依赖，使后面的类型、宏和函数声明能够被编译器识别。

## 第 2 段（第 3-25 行）

```c
#define STB_C_LEX_C_DECIMAL_INTS    Y   //  "0|[1-9][0-9]*"                        CLEX_intlit
#define STB_C_LEX_C_HEX_INTS        Y   //  "0x[0-9a-fA-F]+"                       CLEX_intlit
#define STB_C_LEX_C_OCTAL_INTS      Y   //  "[0-7]+"                               CLEX_intlit
#define STB_C_LEX_C_DECIMAL_FLOATS  Y   //  "[0-9]*(.[0-9]*([eE][-+]?[0-9]+)?)     CLEX_floatlit
#define STB_C_LEX_C99_HEX_FLOATS    N   //  "0x{hex}+(.{hex}*)?[pP][-+]?{hex}+     CLEX_floatlit
#define STB_C_LEX_C_IDENTIFIERS     Y   //  "[_a-zA-Z][_a-zA-Z0-9]*"               CLEX_id
#define STB_C_LEX_C_DQ_STRINGS      Y   //  double-quote-delimited strings with escapes  CLEX_dqstring
#define STB_C_LEX_C_SQ_STRINGS      N   //  single-quote-delimited strings with escapes  CLEX_ssstring
#define STB_C_LEX_C_CHARS           Y   //  single-quote-delimited character with escape CLEX_charlits
#define STB_C_LEX_C_COMMENTS        Y   //  "/* comment */"
#define STB_C_LEX_CPP_COMMENTS      Y   //  "// comment to end of line\n"
#define STB_C_LEX_C_COMPARISONS     Y   //  "==" CLEX_eq  "!=" CLEX_noteq   "<=" CLEX_lesseq  ">=" CLEX_greatereq
#define STB_C_LEX_C_LOGICAL         Y   //  "&&"  CLEX_andand   "||"  CLEX_oror
#define STB_C_LEX_C_SHIFTS          Y   //  "<<"  CLEX_shl      ">>"  CLEX_shr
#define STB_C_LEX_C_INCREMENTS      Y   //  "++"  CLEX_plusplus "--"  CLEX_minusminus
#define STB_C_LEX_C_ARROW           Y   //  "->"  CLEX_arrow
#define STB_C_LEX_EQUAL_ARROW       N   //  "=>"  CLEX_eqarrow
#define STB_C_LEX_C_BITWISEEQ       Y   //  "&="  CLEX_andeq    "|="  CLEX_oreq     "^="  CLEX_xoreq
#define STB_C_LEX_C_ARITHEQ         Y   //  "+="  CLEX_pluseq   "-="  CLEX_minuseq
                                        //  "*="  CLEX_muleq    "/="  CLEX_diveq    "%=" CLEX_modeq
                                        //  if both STB_C_LEX_SHIFTS & STB_C_LEX_ARITHEQ:
                                        //                      "<<=" CLEX_shleq    ">>=" CLEX_shreq
```

解说：这一段定义宏（如 `STB_C_LEX_C_DECIMAL_INTS, STB_C_LEX_C_HEX_INTS, STB_C_LEX_C_OCTAL_INTS, STB_C_LEX_C_DECIMAL_FLOATS, STB_C_LEX_C99_HEX_FLOATS`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 3 段（第 26-31 行）

```c
#define STB_C_LEX_PARSE_SUFFIXES    N   // letters after numbers are parsed as part of those numbers, and must be in suffix list below
#define STB_C_LEX_DECIMAL_SUFFIXES  ""  // decimal integer suffixes e.g. "uUlL" -- these are returned as-is in string storage
#define STB_C_LEX_HEX_SUFFIXES      ""  // e.g. "uUlL"
#define STB_C_LEX_OCTAL_SUFFIXES    ""  // e.g. "uUlL"
#define STB_C_LEX_FLOAT_SUFFIXES    ""  //
```

解说：这一段定义宏（如 `STB_C_LEX_PARSE_SUFFIXES, STB_C_LEX_DECIMAL_SUFFIXES, STB_C_LEX_HEX_SUFFIXES, STB_C_LEX_OCTAL_SUFFIXES, STB_C_LEX_FLOAT_SUFFIXES`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 4 段（第 32-39 行）

```c
#define STB_C_LEX_0_IS_EOF             Y  // if Y, ends parsing at '\0'; if N, returns '\0' as token
#define STB_C_LEX_INTEGERS_AS_DOUBLES  N  // parses integers as doubles so they can be larger than 'int', but only if STB_C_LEX_STDLIB==N
#define STB_C_LEX_MULTILINE_DSTRINGS   N  // allow newlines in double-quoted strings
#define STB_C_LEX_MULTILINE_SSTRINGS   N  // allow newlines in single-quoted strings
#define STB_C_LEX_USE_STDLIB           Y  // use strtod,strtol for parsing #s; otherwise inaccurate hack
#define STB_C_LEX_DOLLAR_IDENTIFIER    Y  // allow $ as an identifier character
#define STB_C_LEX_FLOAT_NO_DECIMAL     Y  // allow floats that have no decimal point if they have an exponent
```

解说：这一段定义宏（如 `STB_C_LEX_0_IS_EOF, STB_C_LEX_INTEGERS_AS_DOUBLES, STB_C_LEX_MULTILINE_DSTRINGS, STB_C_LEX_MULTILINE_SSTRINGS, STB_C_LEX_USE_STDLIB`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 5 段（第 40-42 行）

```c
#define STB_C_LEX_DEFINE_ALL_TOKEN_NAMES  N   // if Y, all CLEX_ token names are defined, even if never returned
                                              // leaving it as N should help you catch config bugs
```

解说：这一段定义宏（如 `STB_C_LEX_DEFINE_ALL_TOKEN_NAMES`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 6 段（第 43-47 行）

```c
#define STB_C_LEX_DISCARD_PREPROCESSOR    Y   // discard C-preprocessor directives (e.g. after prepocess
                                              // still have #line, #pragma, etc)

//#define STB_C_LEX_ISWHITE(str)    ... // return length in bytes of whitespace characters if first char is whitespace
```

解说：这一段定义宏（如 `STB_C_LEX_DISCARD_PREPROCESSOR`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 7 段（第 48-49 行）

```c
#define STB_C_LEXER_DEFINITIONS         // This line prevents the header file from replacing your definitions
```

解说：这一段定义宏（如 `STB_C_LEXER_DEFINITIONS`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 8 段（第 50-50 行）

```c
#include "stb_c_lexer.h"
```

解说：这一段引入编译所需的头文件或本项目内的依赖，使后面的类型、宏和函数声明能够被编译器识别。
