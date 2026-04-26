# stb_c_lexer.h 代码解说

源文件：`stb_c_lexer.h`

本文按源文件中的自然代码段切分，每段先给出原始代码，再用中文解释这段代码在做什么以及它为什么存在。

## 第 1 段（第 1-46 行）

```c
// stb_c_lexer.h - v0.12 - public domain Sean Barrett 2013
// lexer for making little C-like languages with recursive-descent parsers
//
// This file provides both the interface and the implementation.
// To instantiate the implementation,
//      #define STB_C_LEXER_IMPLEMENTATION
// in *ONE* source file, before #including this file.
//
// The default configuration is fairly close to a C lexer, although
// suffixes on integer constants are not handled (you can override this).
//
// History:
//     0.12 fix compilation bug for NUL support; better support separate inclusion
//     0.11 fix clang static analysis warning
//     0.10 fix warnings
//     0.09 hex floats, no-stdlib fixes
//     0.08 fix bad pointer comparison
//     0.07 fix mishandling of hexadecimal constants parsed by strtol
//     0.06 fix missing next character after ending quote mark (Andreas Fredriksson)
//     0.05 refixed get_location because github version had lost the fix
//     0.04 fix octal parsing bug
//     0.03 added STB_C_LEX_DISCARD_PREPROCESSOR option
//          refactor API to simplify (only one struct instead of two)
//          change literal enum names to have 'lit' at the end
//     0.02 first public release
//
// Status:
//     - haven't tested compiling as C++
//     - haven't tested the float parsing path
//     - haven't tested the non-default-config paths (e.g. non-stdlib)
//     - only tested default-config paths by eyeballing output of self-parse
//
//     - haven't implemented multiline strings
//     - haven't implemented octal/hex character constants
//     - haven't implemented support for unicode CLEX_char
//     - need to expand error reporting so you don't just get "CLEX_parse_error"
//
// Contributors:
//   Arpad Goretity (bugfix)
//   Alan Hickman (hex floats)
//   github:mundusnine (bugfix)
//
// LICENSE
//
//   See end of file for license information.
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 2 段（第 47-55 行）

```c
#ifdef STB_C_LEXER_IMPLEMENTATION
#ifndef STB_C_LEXER_DEFINITIONS
// to change the default parsing rules, copy the following lines
// into your C/C++ file *before* including this, and then replace
// the Y's with N's for the ones you don't want. This needs to be
// set to the same values for every place in your program where
// stb_c_lexer.h is included.
// --BEGIN--
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 3 段（第 56-59 行）

```c
#if defined(Y) || defined(N)
#error "Can only use stb_c_lexer in contexts where the preprocessor symbols 'Y' and 'N' are not defined"
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 4 段（第 60-82 行）

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

## 第 5 段（第 83-88 行）

```c
#define STB_C_LEX_PARSE_SUFFIXES    N   // letters after numbers are parsed as part of those numbers, and must be in suffix list below
#define STB_C_LEX_DECIMAL_SUFFIXES  ""  // decimal integer suffixes e.g. "uUlL" -- these are returned as-is in string storage
#define STB_C_LEX_HEX_SUFFIXES      ""  // e.g. "uUlL"
#define STB_C_LEX_OCTAL_SUFFIXES    ""  // e.g. "uUlL"
#define STB_C_LEX_FLOAT_SUFFIXES    ""  //
```

解说：这一段定义宏（如 `STB_C_LEX_PARSE_SUFFIXES, STB_C_LEX_DECIMAL_SUFFIXES, STB_C_LEX_HEX_SUFFIXES, STB_C_LEX_OCTAL_SUFFIXES, STB_C_LEX_FLOAT_SUFFIXES`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 6 段（第 89-96 行）

```c
#define STB_C_LEX_0_IS_EOF             N  // if Y, ends parsing at '\0'; if N, returns '\0' as token
#define STB_C_LEX_INTEGERS_AS_DOUBLES  N  // parses integers as doubles so they can be larger than 'int', but only if STB_C_LEX_STDLIB==N
#define STB_C_LEX_MULTILINE_DSTRINGS   N  // allow newlines in double-quoted strings
#define STB_C_LEX_MULTILINE_SSTRINGS   N  // allow newlines in single-quoted strings
#define STB_C_LEX_USE_STDLIB           Y  // use strtod,strtol for parsing #s; otherwise inaccurate hack
#define STB_C_LEX_DOLLAR_IDENTIFIER    Y  // allow $ as an identifier character
#define STB_C_LEX_FLOAT_NO_DECIMAL     Y  // allow floats that have no decimal point if they have an exponent
```

解说：这一段定义宏（如 `STB_C_LEX_0_IS_EOF, STB_C_LEX_INTEGERS_AS_DOUBLES, STB_C_LEX_MULTILINE_DSTRINGS, STB_C_LEX_MULTILINE_SSTRINGS, STB_C_LEX_USE_STDLIB`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 7 段（第 97-99 行）

```c
#define STB_C_LEX_DEFINE_ALL_TOKEN_NAMES  N   // if Y, all CLEX_ token names are defined, even if never returned
                                              // leaving it as N should help you catch config bugs
```

解说：这一段定义宏（如 `STB_C_LEX_DEFINE_ALL_TOKEN_NAMES`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 8 段（第 100-104 行）

```c
#define STB_C_LEX_DISCARD_PREPROCESSOR    Y   // discard C-preprocessor directives (e.g. after prepocess
                                              // still have #line, #pragma, etc)

//#define STB_C_LEX_ISWHITE(str)    ... // return length in bytes of whitespace characters if first char is whitespace
```

解说：这一段定义宏（如 `STB_C_LEX_DISCARD_PREPROCESSOR`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 9 段（第 105-109 行）

```c
#define STB_C_LEXER_DEFINITIONS         // This line prevents the header file from replacing your definitions
// --END--
#endif
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 10 段（第 110-112 行）

```c
#ifndef INCLUDE_STB_C_LEXER_H
#define INCLUDE_STB_C_LEXER_H
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 11 段（第 113-133 行）

```c
typedef struct
{
   // lexer variables
   char *input_stream;
   char *eof;
   char *parse_point;
   char *string_storage;
   int   string_storage_len;

   // lexer parse location for error messages
   char *where_firstchar;
   char *where_lastchar;

   // lexer token variables
   long token;
   double real_number;
   long   int_number;
   char *string;
   int string_len;
} stb_lexer;
```

解说：这一段声明结构体 `stb_lexer`，把相关字段组织成一个数据对象，供后续算法在函数之间传递和维护状态。

## 第 12 段（第 134-139 行）

```c
typedef struct
{
   int line_number;
   int line_offset;
} stb_lex_location;
```

解说：这一段声明结构体 `stb_lex_location`，把相关字段组织成一个数据对象，供后续算法在函数之间传递和维护状态。

## 第 13 段（第 140-176 行）

```c
#ifdef __cplusplus
extern "C" {
#endif

extern void stb_c_lexer_init(stb_lexer *lexer, const char *input_stream, const char *input_stream_end, char *string_store, int store_length);
// this function initialize the 'lexer' structure
//   Input:
//   - input_stream points to the file to parse, loaded into memory
//   - input_stream_end points to the end of the file, or NULL if you use 0-for-EOF
//   - string_store is storage the lexer can use for storing parsed strings and identifiers
//   - store_length is the length of that storage

extern int stb_c_lexer_get_token(stb_lexer *lexer);
// this function returns non-zero if a token is parsed, or 0 if at EOF
//   Output:
//   - lexer->token is the token ID, which is unicode code point for a single-char token, < 0 for a multichar or eof or error
//   - lexer->real_number is a double constant value for CLEX_floatlit, or CLEX_intlit if STB_C_LEX_INTEGERS_AS_DOUBLES
//   - lexer->int_number is an integer constant for CLEX_intlit if !STB_C_LEX_INTEGERS_AS_DOUBLES, or character for CLEX_charlit
//   - lexer->string is a 0-terminated string for CLEX_dqstring or CLEX_sqstring or CLEX_identifier
//   - lexer->string_len is the byte length of lexer->string

extern void stb_c_lexer_get_location(const stb_lexer *lexer, const char *where, stb_lex_location *loc);
// this inefficient function returns the line number and character offset of a
// given location in the file as returned by stb_lex_token. Because it's inefficient,
// you should only call it for errors, not for every token.
// For error messages of invalid tokens, you typically want the location of the start
// of the token (which caused the token to be invalid). For bugs involving legit
// tokens, you can report the first or the range.
//    Output:
//    - loc->line_number is the line number in the file, counting from 1, of the location
//    - loc->line_offset is the char-offset in the line, counting from 0, of the location


#ifdef __cplusplus
}
#endif
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 14 段（第 177-213 行）

```c
enum
{
   CLEX_eof = 256,
   CLEX_parse_error,
   CLEX_intlit        ,
   CLEX_floatlit      ,
   CLEX_id            ,
   CLEX_dqstring      ,
   CLEX_sqstring      ,
   CLEX_charlit       ,
   CLEX_eq            ,
   CLEX_noteq         ,
   CLEX_lesseq        ,
   CLEX_greatereq     ,
   CLEX_andand        ,
   CLEX_oror          ,
   CLEX_shl           ,
   CLEX_shr           ,
   CLEX_plusplus      ,
   CLEX_minusminus    ,
   CLEX_pluseq        ,
   CLEX_minuseq       ,
   CLEX_muleq         ,
   CLEX_diveq         ,
   CLEX_modeq         ,
   CLEX_andeq         ,
   CLEX_oreq          ,
   CLEX_xoreq         ,
   CLEX_arrow         ,
   CLEX_eqarrow       ,
   CLEX_shleq, CLEX_shreq,

   CLEX_first_unused_token

};
#endif // INCLUDE_STB_C_LEXER_H
```

解说：这一段声明枚举类型，把一组相关常量集中命名，便于后续代码用语义化名字表达状态或选项。

## 第 15 段（第 214-219 行）

```c
#ifdef STB_C_LEXER_IMPLEMENTATION

// Hacky definitions so we can easily #if on them
#define Y(x) 1
#define N(x) 0
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 16 段（第 220-228 行）

```c
#if STB_C_LEX_INTEGERS_AS_DOUBLES(x)
typedef double     stb__clex_int;
#define intfield   real_number
#define STB__clex_int_as_double
#else
typedef long       stb__clex_int;
#define intfield   int_number
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 17 段（第 229-231 行）

```c
// Convert these config options to simple conditional #defines so we can more
// easily test them once we've change the meaning of Y/N
```

解说：这一段是说明性注释，记录附近代码的用途、限制或维护提示，方便读者理解后续实现。

## 第 18 段（第 232-235 行）

```c
#if STB_C_LEX_PARSE_SUFFIXES(x)
#define STB__clex_parse_suffixes
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 19 段（第 236-239 行）

```c
#if STB_C_LEX_C99_HEX_FLOATS(x)
#define STB__clex_hex_floats
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 20 段（第 240-243 行）

```c
#if STB_C_LEX_C_HEX_INTS(x)
#define STB__clex_hex_ints
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 21 段（第 244-247 行）

```c
#if STB_C_LEX_C_DECIMAL_INTS(x)
#define STB__clex_decimal_ints
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 22 段（第 248-251 行）

```c
#if STB_C_LEX_C_OCTAL_INTS(x)
#define STB__clex_octal_ints
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 23 段（第 252-255 行）

```c
#if STB_C_LEX_C_DECIMAL_FLOATS(x)
#define STB__clex_decimal_floats
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 24 段（第 256-259 行）

```c
#if STB_C_LEX_DISCARD_PREPROCESSOR(x)
#define STB__clex_discard_preprocessor
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 25 段（第 260-264 行）

```c
#if STB_C_LEX_USE_STDLIB(x) && (!defined(STB__clex_hex_floats) || __STDC_VERSION__ >= 199901L)
#define STB__CLEX_use_stdlib
#include <stdlib.h>
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 26 段（第 265-271 行）

```c
// Now for the rest of the file we'll use the basic definition where
// where Y expands to its contents and N expands to nothing
#undef  Y
#define Y(a) a
#undef N
#define N(a)
```

解说：这一段定义宏（如 `Y, N`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 27 段（第 272-281 行）

```c
// API function
void stb_c_lexer_init(stb_lexer *lexer, const char *input_stream, const char *input_stream_end, char *string_store, int store_length)
{
   lexer->input_stream = (char *) input_stream;
   lexer->eof = (char *) input_stream_end;
   lexer->parse_point = (char *) input_stream;
   lexer->string_storage = string_store;
   lexer->string_storage_len = store_length;
}
```

解说：这一段实现或声明库接口 `stb_c_lexer_init`，把“c lexer init”相关能力封装成可被外部或内部复用的函数。

## 第 28 段（第 282-301 行）

```c
// API function
void stb_c_lexer_get_location(const stb_lexer *lexer, const char *where, stb_lex_location *loc)
{
   char *p = lexer->input_stream;
   int line_number = 1;
   int char_offset = 0;
   while (*p && p < where) {
      if (*p == '\n' || *p == '\r') {
         p += (p[0]+p[1] == '\r'+'\n' ? 2 : 1); // skip newline
         line_number += 1;
         char_offset = 0;
      } else {
         ++p;
         ++char_offset;
      }
   }
   loc->line_number = line_number;
   loc->line_offset = char_offset;
}
```

解说：这一段实现或声明库接口 `stb_c_lexer_get_location`，把“c lexer get location”相关能力封装成可被外部或内部复用的函数。

## 第 29 段（第 302-311 行）

```c
// main helper function for returning a parsed token
static int stb__clex_token(stb_lexer *lexer, int token, char *start, char *end)
{
   lexer->token = token;
   lexer->where_firstchar = start;
   lexer->where_lastchar = end;
   lexer->parse_point = end+1;
   return 1;
}
```

解说：这一段实现或声明库接口 `stb__clex_token`，把“clex token”相关能力封装成可被外部或内部复用的函数。

## 第 30 段（第 312-318 行）

```c
// helper function for returning eof
static int stb__clex_eof(stb_lexer *lexer)
{
   lexer->token = CLEX_eof;
   return 0;
}
```

解说：这一段实现或声明库接口 `stb__clex_eof`，把“clex eof”相关能力封装成可被外部或内部复用的函数。

## 第 31 段（第 319-323 行）

```c
static int stb__clex_iswhite(int x)
{
   return x == ' ' || x == '\t' || x == '\r' || x == '\n' || x == '\f';
}
```

解说：这一段实现或声明库接口 `stb__clex_iswhite`，把“clex iswhite”相关能力封装成可被外部或内部复用的函数。

## 第 32 段（第 324-331 行）

```c
static const char *stb__strchr(const char *str, int ch)
{
   for (; *str; ++str)
      if (*str == ch)
         return str;
   return 0;
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 33 段（第 332-351 行）

```c
// parse suffixes at the end of a number
static int stb__clex_parse_suffixes(stb_lexer *lexer, long tokenid, char *start, char *cur, const char *suffixes)
{
   #ifdef STB__clex_parse_suffixes
   lexer->string = lexer->string_storage;
   lexer->string_len = 0;

   while ((*cur >= 'a' && *cur <= 'z') || (*cur >= 'A' && *cur <= 'Z')) {
      if (stb__strchr(suffixes, *cur) == 0)
         return stb__clex_token(lexer, CLEX_parse_error, start, cur);
      if (lexer->string_len+1 >= lexer->string_storage_len)
         return stb__clex_token(lexer, CLEX_parse_error, start, cur);
      lexer->string[lexer->string_len++] = *cur++;
   }
   #else
   suffixes = suffixes; // attempt to suppress warnings
   #endif
   return stb__clex_token(lexer, tokenid, start, cur-1);
}
```

解说：这一段实现或声明库接口 `stb__clex_parse_suffixes`，把“clex parse suffixes”相关能力封装成可被外部或内部复用的函数。

## 第 34 段（第 352-363 行）

```c
#ifndef STB__CLEX_use_stdlib
static double stb__clex_pow(double base, unsigned int exponent)
{
   double value=1;
   for ( ; exponent; exponent >>= 1) {
      if (exponent & 1)
         value *= base;
      base *= base;
   }
   return value;
}
```

解说：这一段实现或声明库接口 `stb__clex_pow`，把“clex pow”相关能力封装成可被外部或内部复用的函数。

## 第 35 段（第 364-445 行）

```c
static double stb__clex_parse_float(char *p, char **q)
{
   char *s = p;
   double value=0;
   int base=10;
   int exponent=0;

#ifdef STB__clex_hex_floats
   if (*p == '0') {
      if (p[1] == 'x' || p[1] == 'X') {
         base=16;
         p += 2;
      }
   }
#endif

   for (;;) {
      if (*p >= '0' && *p <= '9')
         value = value*base + (*p++ - '0');
#ifdef STB__clex_hex_floats
      else if (base == 16 && *p >= 'a' && *p <= 'f')
         value = value*base + 10 + (*p++ - 'a');
      else if (base == 16 && *p >= 'A' && *p <= 'F')
         value = value*base + 10 + (*p++ - 'A');
#endif
      else
         break;
   }

   if (*p == '.') {
      double pow, addend = 0;
      ++p;
      for (pow=1; ; pow*=base) {
         if (*p >= '0' && *p <= '9')
            addend = addend*base + (*p++ - '0');
#ifdef STB__clex_hex_floats
         else if (base == 16 && *p >= 'a' && *p <= 'f')
            addend = addend*base + 10 + (*p++ - 'a');
         else if (base == 16 && *p >= 'A' && *p <= 'F')
            addend = addend*base + 10 + (*p++ - 'A');
#endif
         else
            break;
      }
      value += addend / pow;
   }
#ifdef STB__clex_hex_floats
   if (base == 16) {
      // exponent required for hex float literal
      if (*p != 'p' && *p != 'P') {
         *q = s;
         return 0;
      }
      exponent = 1;
   } else
#endif
      exponent = (*p == 'e' || *p == 'E');

   if (exponent) {
      int sign = p[1] == '-';
      unsigned int exponent=0;
      double power=1;
      ++p;
      if (*p == '-' || *p == '+')
         ++p;
      while (*p >= '0' && *p <= '9')
         exponent = exponent*10 + (*p++ - '0');

#ifdef STB__clex_hex_floats
      if (base == 16)
         power = stb__clex_pow(2, exponent);
      else
#endif
         power = stb__clex_pow(10, exponent);
      if (sign)
         value /= power;
      else
         value *= power;
   }
   *q = p;
   return value;
}
```

解说：这一段实现或声明库接口 `stb__clex_parse_float`，把“clex parse float”相关能力封装成可被外部或内部复用的函数。

## 第 36 段（第 446-447 行）

```c
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 37 段（第 448-468 行）

```c
static int stb__clex_parse_char(char *p, char **q)
{
   if (*p == '\\') {
      *q = p+2; // tentatively guess we'll parse two characters
      switch(p[1]) {
         case '\\': return '\\';
         case '\'': return '\'';
         case '"': return '"';
         case 't': return '\t';
         case 'f': return '\f';
         case 'n': return '\n';
         case 'r': return '\r';
         case '0': return '\0'; // @TODO ocatal constants
         case 'x': case 'X': return -1; // @TODO hex constants
         case 'u': return -1; // @TODO unicode constants
      }
   }
   *q = p+1;
   return (unsigned char) *p;
}
```

解说：这一段实现或声明库接口 `stb__clex_parse_char`，把“clex parse char”相关能力封装成可被外部或内部复用的函数。

## 第 38 段（第 469-497 行）

```c
static int stb__clex_parse_string(stb_lexer *lexer, char *p, int type)
{
   char *start = p;
   char delim = *p++; // grab the " or ' for later matching
   char *out = lexer->string_storage;
   char *outend = lexer->string_storage + lexer->string_storage_len;
   while (*p != delim) {
      int n;
      if (*p == '\\') {
         char *q;
         n = stb__clex_parse_char(p, &q);
         if (n < 0)
            return stb__clex_token(lexer, CLEX_parse_error, start, q);
         p = q;
      } else {
         // @OPTIMIZE: could speed this up by looping-while-not-backslash
         n = (unsigned char) *p++;
      }
      if (out+1 > outend)
         return stb__clex_token(lexer, CLEX_parse_error, start, p);
      // @TODO expand unicode escapes to UTF8
      *out++ = (char) n;
   }
   *out = 0;
   lexer->string = lexer->string_storage;
   lexer->string_len = (int) (out - lexer->string_storage);
   return stb__clex_token(lexer, type, start, p);
}
```

解说：这一段实现或声明库接口 `stb__clex_parse_string`，把“clex parse string”相关能力封装成可被外部或内部复用的函数。

## 第 39 段（第 498-800 行）

```c
int stb_c_lexer_get_token(stb_lexer *lexer)
{
   char *p = lexer->parse_point;

   // skip whitespace and comments
   for (;;) {
      #ifdef STB_C_LEX_ISWHITE
      while (p != lexer->stream_end) {
         int n;
         n = STB_C_LEX_ISWHITE(p);
         if (n == 0) break;
         if (lexer->eof && lexer->eof - lexer->parse_point < n)
            return stb__clex_token(tok, CLEX_parse_error, p,lexer->eof-1);
         p += n;
      }
      #else
      while (p != lexer->eof && stb__clex_iswhite(*p))
         ++p;
      #endif

      STB_C_LEX_CPP_COMMENTS(
         if (p != lexer->eof && p[0] == '/' && p[1] == '/') {
            while (p != lexer->eof && *p != '\r' && *p != '\n')
               ++p;
            continue;
         }
      )

      STB_C_LEX_C_COMMENTS(
         if (p != lexer->eof && p[0] == '/' && p[1] == '*') {
            char *start = p;
            p += 2;
            while (p != lexer->eof && (p[0] != '*' || p[1] != '/'))
               ++p;
            if (p == lexer->eof)
               return stb__clex_token(lexer, CLEX_parse_error, start, p-1);
            p += 2;
            continue;
         }
      )

      #ifdef STB__clex_discard_preprocessor
         // @TODO this discards everything after a '#', regardless
         // of where in the line the # is, rather than requiring it
         // be at the start. (because this parser doesn't otherwise
         // check for line breaks!)
         if (p != lexer->eof && p[0] == '#') {
            while (p != lexer->eof && *p != '\r' && *p != '\n')
               ++p;
            continue;
         }
      #endif

      break;
   }

   if (p == lexer->eof)
      return stb__clex_eof(lexer);

   switch (*p) {
      default:
         if (   (*p >= 'a' && *p <= 'z')
             || (*p >= 'A' && *p <= 'Z')
             || *p == '_' || (unsigned char) *p >= 128    // >= 128 is UTF8 char
             STB_C_LEX_DOLLAR_IDENTIFIER( || *p == '$' ) )
         {
            int n = 0;
            lexer->string = lexer->string_storage;
            do {
               if (n+1 >= lexer->string_storage_len)
                  return stb__clex_token(lexer, CLEX_parse_error, p, p+n);
               lexer->string[n] = p[n];
               ++n;
            } while (
                  (p[n] >= 'a' && p[n] <= 'z')
               || (p[n] >= 'A' && p[n] <= 'Z')
               || (p[n] >= '0' && p[n] <= '9') // allow digits in middle of identifier
               || p[n] == '_' || (unsigned char) p[n] >= 128
                STB_C_LEX_DOLLAR_IDENTIFIER( || p[n] == '$' )
            );
            lexer->string[n] = 0;
            lexer->string_len = n;
            return stb__clex_token(lexer, CLEX_id, p, p+n-1);
         }

         // check for EOF
         STB_C_LEX_0_IS_EOF(
            if (*p == 0)
               return stb__clex_eof(lexer);
         )

      single_char:
         // not an identifier, return the character as itself
         return stb__clex_token(lexer, *p, p, p);

      case '+':
         if (p+1 != lexer->eof) {
            STB_C_LEX_C_INCREMENTS(if (p[1] == '+') return stb__clex_token(lexer, CLEX_plusplus, p,p+1);)
            STB_C_LEX_C_ARITHEQ(   if (p[1] == '=') return stb__clex_token(lexer, CLEX_pluseq  , p,p+1);)
         }
         goto single_char;
      case '-':
         if (p+1 != lexer->eof) {
            STB_C_LEX_C_INCREMENTS(if (p[1] == '-') return stb__clex_token(lexer, CLEX_minusminus, p,p+1);)
            STB_C_LEX_C_ARITHEQ(   if (p[1] == '=') return stb__clex_token(lexer, CLEX_minuseq   , p,p+1);)
            STB_C_LEX_C_ARROW(     if (p[1] == '>') return stb__clex_token(lexer, CLEX_arrow     , p,p+1);)
         }
         goto single_char;
      case '&':
         if (p+1 != lexer->eof) {
            STB_C_LEX_C_LOGICAL(  if (p[1] == '&') return stb__clex_token(lexer, CLEX_andand, p,p+1);)
            STB_C_LEX_C_BITWISEEQ(if (p[1] == '=') return stb__clex_token(lexer, CLEX_andeq , p,p+1);)
         }
         goto single_char;
      case '|':
         if (p+1 != lexer->eof) {
            STB_C_LEX_C_LOGICAL(  if (p[1] == '|') return stb__clex_token(lexer, CLEX_oror, p,p+1);)
            STB_C_LEX_C_BITWISEEQ(if (p[1] == '=') return stb__clex_token(lexer, CLEX_oreq, p,p+1);)
         }
         goto single_char;
      case '=':
         if (p+1 != lexer->eof) {
            STB_C_LEX_C_COMPARISONS(if (p[1] == '=') return stb__clex_token(lexer, CLEX_eq, p,p+1);)
            STB_C_LEX_EQUAL_ARROW(  if (p[1] == '>') return stb__clex_token(lexer, CLEX_eqarrow, p,p+1);)
         }
         goto single_char;
      case '!':
         STB_C_LEX_C_COMPARISONS(if (p+1 != lexer->eof && p[1] == '=') return stb__clex_token(lexer, CLEX_noteq, p,p+1);)
         goto single_char;
      case '^':
         STB_C_LEX_C_BITWISEEQ(if (p+1 != lexer->eof && p[1] == '=') return stb__clex_token(lexer, CLEX_xoreq, p,p+1));
         goto single_char;
      case '%':
         STB_C_LEX_C_ARITHEQ(if (p+1 != lexer->eof && p[1] == '=') return stb__clex_token(lexer, CLEX_modeq, p,p+1));
         goto single_char;
      case '*':
         STB_C_LEX_C_ARITHEQ(if (p+1 != lexer->eof && p[1] == '=') return stb__clex_token(lexer, CLEX_muleq, p,p+1));
         goto single_char;
      case '/':
         STB_C_LEX_C_ARITHEQ(if (p+1 != lexer->eof && p[1] == '=') return stb__clex_token(lexer, CLEX_diveq, p,p+1));
         goto single_char;
      case '<':
         if (p+1 != lexer->eof) {
            STB_C_LEX_C_COMPARISONS(if (p[1] == '=') return stb__clex_token(lexer, CLEX_lesseq, p,p+1);)
            STB_C_LEX_C_SHIFTS(     if (p[1] == '<') {
                                       STB_C_LEX_C_ARITHEQ(if (p+2 != lexer->eof && p[2] == '=')
                                                              return stb__clex_token(lexer, CLEX_shleq, p,p+2);)
                                       return stb__clex_token(lexer, CLEX_shl, p,p+1);
                                    }
                              )
         }
         goto single_char;
      case '>':
         if (p+1 != lexer->eof) {
            STB_C_LEX_C_COMPARISONS(if (p[1] == '=') return stb__clex_token(lexer, CLEX_greatereq, p,p+1);)
            STB_C_LEX_C_SHIFTS(     if (p[1] == '>') {
                                       STB_C_LEX_C_ARITHEQ(if (p+2 != lexer->eof && p[2] == '=')
                                                              return stb__clex_token(lexer, CLEX_shreq, p,p+2);)
                                       return stb__clex_token(lexer, CLEX_shr, p,p+1);
                                    }
                              )
         }
         goto single_char;

      case '"':
         STB_C_LEX_C_DQ_STRINGS(return stb__clex_parse_string(lexer, p, CLEX_dqstring);)
         goto single_char;
      case '\'':
         STB_C_LEX_C_SQ_STRINGS(return stb__clex_parse_string(lexer, p, CLEX_sqstring);)
         STB_C_LEX_C_CHARS(
         {
            char *start = p;
            lexer->int_number = stb__clex_parse_char(p+1, &p);
            if (lexer->int_number < 0)
               return stb__clex_token(lexer, CLEX_parse_error, start,start);
            if (p == lexer->eof || *p != '\'')
               return stb__clex_token(lexer, CLEX_parse_error, start,p);
            return stb__clex_token(lexer, CLEX_charlit, start, p+1);
         })
         goto single_char;

      case '0':
         #if defined(STB__clex_hex_ints) || defined(STB__clex_hex_floats)
            if (p+1 != lexer->eof) {
               if (p[1] == 'x' || p[1] == 'X') {
                  char *q;

                  #ifdef STB__clex_hex_floats
                  for (q=p+2;
                       q != lexer->eof && ((*q >= '0' && *q <= '9') || (*q >= 'a' && *q <= 'f') || (*q >= 'A' && *q <= 'F'));
                       ++q);
                  if (q != lexer->eof) {
                     if (*q == '.' STB_C_LEX_FLOAT_NO_DECIMAL(|| *q == 'p' || *q == 'P')) {
                        #ifdef STB__CLEX_use_stdlib
                        lexer->real_number = strtod((char *) p, (char**) &q);
                        #else
                        lexer->real_number = stb__clex_parse_float(p, &q);
                        #endif

                        if (p == q)
                           return stb__clex_token(lexer, CLEX_parse_error, p,q);
                        return stb__clex_parse_suffixes(lexer, CLEX_floatlit, p,q, STB_C_LEX_FLOAT_SUFFIXES);

                     }
                  }
                  #endif   // STB__CLEX_hex_floats

                  #ifdef STB__clex_hex_ints
                  #ifdef STB__CLEX_use_stdlib
                  lexer->int_number = strtol((char *) p, (char **) &q, 16);
                  #else
                  {
                     stb__clex_int n=0;
                     for (q=p+2; q != lexer->eof; ++q) {
                        if (*q >= '0' && *q <= '9')
                           n = n*16 + (*q - '0');
                        else if (*q >= 'a' && *q <= 'f')
                           n = n*16 + (*q - 'a') + 10;
                        else if (*q >= 'A' && *q <= 'F')
                           n = n*16 + (*q - 'A') + 10;
                        else
                           break;
                     }
                     lexer->int_number = n;
                  }
                  #endif
                  if (q == p+2)
                     return stb__clex_token(lexer, CLEX_parse_error, p-2,p-1);
                  return stb__clex_parse_suffixes(lexer, CLEX_intlit, p,q, STB_C_LEX_HEX_SUFFIXES);
                  #endif
               }
            }
         #endif // defined(STB__clex_hex_ints) || defined(STB__clex_hex_floats)
         // can't test for octal because we might parse '0.0' as float or as '0' '.' '0',
         // so have to do float first

         /* FALL THROUGH */
      case '1': case '2': case '3': case '4': case '5': case '6': case '7': case '8': case '9':

         #ifdef STB__clex_decimal_floats
         {
            char *q = p;
            while (q != lexer->eof && (*q >= '0' && *q <= '9'))
               ++q;
            if (q != lexer->eof) {
               if (*q == '.' STB_C_LEX_FLOAT_NO_DECIMAL(|| *q == 'e' || *q == 'E')) {
                  #ifdef STB__CLEX_use_stdlib
                  lexer->real_number = strtod((char *) p, (char**) &q);
                  #else
                  lexer->real_number = stb__clex_parse_float(p, &q);
                  #endif

                  return stb__clex_parse_suffixes(lexer, CLEX_floatlit, p,q, STB_C_LEX_FLOAT_SUFFIXES);

               }
            }
         }
         #endif // STB__clex_decimal_floats

         #ifdef STB__clex_octal_ints
         if (p[0] == '0') {
            char *q = p;
            #ifdef STB__CLEX_use_stdlib
            lexer->int_number = strtol((char *) p, (char **) &q, 8);
            #else
            stb__clex_int n=0;
            while (q != lexer->eof) {
               if (*q >= '0' && *q <= '7')
                  n = n*8 + (*q - '0');
               else
                  break;
               ++q;
            }
            if (q != lexer->eof && (*q == '8' || *q=='9'))
               return stb__clex_token(lexer, CLEX_parse_error, p, q);
            lexer->int_number = n;
            #endif
            return stb__clex_parse_suffixes(lexer, CLEX_intlit, p,q, STB_C_LEX_OCTAL_SUFFIXES);
         }
         #endif // STB__clex_octal_ints

         #ifdef STB__clex_decimal_ints
         {
            char *q = p;
            #ifdef STB__CLEX_use_stdlib
            lexer->int_number = strtol((char *) p, (char **) &q, 10);
            #else
            stb__clex_int n=0;
            while (q != lexer->eof) {
               if (*q >= '0' && *q <= '9')
                  n = n*10 + (*q - '0');
               else
                  break;
               ++q;
            }
            lexer->int_number = n;
            #endif
            return stb__clex_parse_suffixes(lexer, CLEX_intlit, p,q, STB_C_LEX_OCTAL_SUFFIXES);
         }
         #endif // STB__clex_decimal_ints
         goto single_char;
   }
}
```

解说：这一段实现或声明库接口 `stb_c_lexer_get_token`，把“c lexer get token”相关能力封装成可被外部或内部复用的函数。

## 第 40 段（第 801-802 行）

```c
#endif // STB_C_LEXER_IMPLEMENTATION
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 41 段（第 803-807 行）

```c
#ifdef STB_C_LEXER_SELF_TEST
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <stdlib.h>
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 42 段（第 808-852 行）

```c
static void print_token(stb_lexer *lexer)
{
   switch (lexer->token) {
      case CLEX_id        : printf("_%s", lexer->string); break;
      case CLEX_eq        : printf("=="); break;
      case CLEX_noteq     : printf("!="); break;
      case CLEX_lesseq    : printf("<="); break;
      case CLEX_greatereq : printf(">="); break;
      case CLEX_andand    : printf("&&"); break;
      case CLEX_oror      : printf("||"); break;
      case CLEX_shl       : printf("<<"); break;
      case CLEX_shr       : printf(">>"); break;
      case CLEX_plusplus  : printf("++"); break;
      case CLEX_minusminus: printf("--"); break;
      case CLEX_arrow     : printf("->"); break;
      case CLEX_andeq     : printf("&="); break;
      case CLEX_oreq      : printf("|="); break;
      case CLEX_xoreq     : printf("^="); break;
      case CLEX_pluseq    : printf("+="); break;
      case CLEX_minuseq   : printf("-="); break;
      case CLEX_muleq     : printf("*="); break;
      case CLEX_diveq     : printf("/="); break;
      case CLEX_modeq     : printf("%%="); break;
      case CLEX_shleq     : printf("<<="); break;
      case CLEX_shreq     : printf(">>="); break;
      case CLEX_eqarrow   : printf("=>"); break;
      case CLEX_dqstring  : printf("\"%s\"", lexer->string); break;
      case CLEX_sqstring  : printf("'\"%s\"'", lexer->string); break;
      case CLEX_charlit   : printf("'%s'", lexer->string); break;
      #if defined(STB__clex_int_as_double) && !defined(STB__CLEX_use_stdlib)
      case CLEX_intlit    : printf("#%g", lexer->real_number); break;
      #else
      case CLEX_intlit    : printf("#%ld", lexer->int_number); break;
      #endif
      case CLEX_floatlit  : printf("%g", lexer->real_number); break;
      default:
         if (lexer->token >= 0 && lexer->token < 256)
            printf("%c", (int) lexer->token);
         else {
            printf("<<<UNKNOWN TOKEN %ld >>>\n", lexer->token);
         }
         break;
   }
}
```

解说：这一段实现或声明函数 `print_token`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 43 段（第 853-856 行）

```c
/* Force a test
of parsing
multiline comments */
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 44 段（第 857-859 行）

```c
/*/ comment /*/
/**/ extern /**/
```

解说：这一段是说明性注释，记录附近代码的用途、限制或维护提示，方便读者理解后续实现。

## 第 45 段（第 860-874 行）

```c
void dummy(void)
{
   double some_floats[] = {
      1.0501, -10.4e12, 5E+10,
#if 0   // not supported in C++ or C-pre-99, so don't try to compile it, but let our parser test it
      0x1.0p+24, 0xff.FP-8, 0x1p-23,
#endif
      4.
   };
   (void) sizeof(some_floats);
   (void) some_floats[1];

   printf("test %d",1); // https://github.com/nothings/stb/issues/13
}
```

解说：这一段实现或声明函数 `dummy`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。

## 第 46 段（第 875-941 行）

```c
int main(int argc, char **argv)
{
   FILE *f = fopen("stb_c_lexer.h","rb");
   char *text = (char *) malloc(1 << 20);
   int len = f ? (int) fread(text, 1, 1<<20, f) : -1;
   stb_lexer lex;
   if (len < 0) {
      fprintf(stderr, "Error opening file\n");
      free(text);
      fclose(f);
      return 1;
   }
   fclose(f);

   stb_c_lexer_init(&lex, text, text+len, (char *) malloc(0x10000), 0x10000);
   while (stb_c_lexer_get_token(&lex)) {
      if (lex.token == CLEX_parse_error) {
         printf("\n<<<PARSE ERROR>>>\n");
         break;
      }
      print_token(&lex);
      printf("  ");
   }
   return 0;
}
#endif
/*
------------------------------------------------------------------------------
This software is available under 2 licenses -- choose whichever you prefer.
------------------------------------------------------------------------------
ALTERNATIVE A - MIT License
Copyright (c) 2017 Sean Barrett
Permission is hereby granted, free of charge, to any person obtaining a copy of
this software and associated documentation files (the "Software"), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies
of the Software, and to permit persons to whom the Software is furnished to do
so, subject to the following conditions:
The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.
THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
------------------------------------------------------------------------------
ALTERNATIVE B - Public Domain (www.unlicense.org)
This is free and unencumbered software released into the public domain.
Anyone is free to copy, modify, publish, use, compile, sell, or distribute this
software, either in source code form or as a compiled binary, for any purpose,
commercial or non-commercial, and by any means.
In jurisdictions that recognize copyright laws, the author or authors of this
software dedicate any and all copyright interest in the software to the public
domain. We make this dedication for the benefit of the public at large and to
the detriment of our heirs and successors. We intend this dedication to be an
overt act of relinquishment in perpetuity of all present and future rights to
this software under copyright law.
THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN
ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION
WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
------------------------------------------------------------------------------
*/
```

解说：这一段是程序入口，负责准备测试或工具运行所需的参数、调用核心逻辑，并把执行结果转换成进程退出状态。
