# stb_tilemap_editor.h 代码解说

源文件：`stb_tilemap_editor.h`

本文按源文件中的自然代码段切分，每段先给出原始代码，再用中文解释这段代码在做什么以及它为什么存在。

## 第 1 段（第 1-80 行）

```c
// stb_tilemap_editor.h - v0.42 - Sean Barrett - http://nothings.org/stb
// placed in the public domain - not copyrighted - first released 2014-09
//
// Embeddable tilemap editor for C/C++
//
//
// TABLE OF CONTENTS
//    FAQ
//    How to compile/use the library
//    Additional configuration macros
//    API documentation
//    Info on editing multiple levels
//    Revision history
//    Todo
//    Credits
//    License
//
//
// FAQ
//
//   Q: What counts as a tilemap for this library?
//
//   A: An array of rectangles, where each rectangle contains a small
//      stack of images.
//
//   Q: What are the limitations?
//
//   A: Maps are limited to 4096x4096 in dimension.
//      Each map square can only contain a stack of at most 32 images.
//      A map can only use up to 32768 distinct image tiles.
//
//   Q: How do I compile this?
//
//   A: You need to #define several symbols before #including it, but only
//      in one file. This will cause all the function definitions to be
//      generated in that file. See the "HOW TO COMPILE" section.
//
//   Q: What advantages does this have over a standalone editor?
//
//   A: For one, you can integrate the editor into your game so you can
//      flip between editing and testing without even switching windows.
//      For another, you don't need an XML parser to get at the map data.
//
//   Q: Can I live-edit my game maps?
//
//   A: Not really, the editor keeps its own map representation.
//
//   Q: How do I save and load maps?
//
//   A: You have to do this yourself. The editor provides serialization
//      functions (get & set) for reading and writing the map it holds.
//      You can choose whatever format you want to store the map to on
//      disk; you just need to provide functions to convert. (For example,
//      I actually store the editor's map representation to disk basically
//      as-is; then I have a single function that converts from the editor
//      map representation to the game representation, which is used both
//      to go from editor-to-game and from loaded-map-to-game.)
//
//   Q: I want to have tiles change appearance based on what's
//      adjacent, or other tile-display/substitution trickiness.
//
//   A: You can do this when you convert from the editor's map
//      representation to the game representation, but there's
//      no way to show this live in the editor.
//
//   Q: The editor appears to be put map location (0,0) at the top left?
//      I want to use a different coordinate system in my game (e.g. y
//      increasing upwards, or origin at the center).
//
//   A: You can do this when you convert from the editor's map
//      representation to the game representation. (Don't forget to
//      translate link coordinates as well!)
//
//   Q: The editor appears to put pixel (0,0) at the top left? I want
//      to use a different coordinate system in my game.
//
//   A: The editor defines an "editor pixel coordinate system" with
//      (0,0) at the top left and requires you to display things in
//      that coordinate system. You can freely remap those coordinates
//      to anything you want on screen.
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 2 段（第 81-160 行）

```c
//
//   Q: How do I scale the user interface?
//
//   A: Since you do all the rendering, you can scale up all the rendering
//      calls that the library makes to you. If you do, (a) you need
//      to also scale up the mouse coordinates, and (b) you may want
//      to scale the map display back down so that you're only scaling
//      the UI and not everything. See the next question.
//
//   Q: How do I scale the map display?
//
//   A: Use stbte_set_spacing() to change the size that the map is displayed
//      at. Note that the "callbacks" to draw tiles are used for both drawing
//      the map and drawing the tile palette, so that callback may need to
//      draw at two different scales. You should choose the scales to match
//       You can tell them apart because the
//      tile palette gets NULL for the property pointer.
//
//   Q: How does object editing work?
//
//   A: One way to think of this is that in the editor, you're placing
//      spawners, not objects. Each spawner must be tile-aligned, because
//      it's only a tile editor. Each tile (stack of layers) gets
//      an associated set of properties, and it's up to you to
//      determine what properties should appear for a given tile,
//      based on e.g. the spawners that are in it.
//
//   Q: How are properties themselves handled?
//
//   A: All properties, regardless of UI behavior, are internally floats.
//      Each tile has an array of floats associated with it, which is
//      passed back to you when drawing the tiles so you can draw
//      objects appropriately modified by the properties.
//
//   Q: What if I want to have two different objects/spawners in
//      one tile, both of which have their own properties?
//
//   A: Make sure STBTE_MAX_PROPERTIES is large enough for the sum of
//      properties in both objects, and then you have to explicitly
//      map the property slot #s to the appropriate objects. They'll
//      still all appear in a single property panel; there's no way
//      to get multiple panels.
//
//   Q: Can I do one-to-many linking?
//
//   A: The library only supports one link per tile. However, you
//      can have multiple tiles all link to a single tile. So, you
//      can fake one-to-many linking by linking in the reverse
//      direction.
//
//   Q: What if I have two objects in the same tile, and they each
//      need an independent link? Or I have two kinds of link associated
//      with a single object?
//
//   A: There is no way to do this. (Unless you can reverse one link.)
//
//   Q: How does cut & paste interact with object properties & links?
//
//   A: Currently the library has no idea which properties or links
//      are associated with which layers of a tile. So currently, the
//      library will only copy properties & links if the layer panel
//      is set to allow all layers to be copied, OR if you set the
//      "props" in the layer panel to "always". Similarly, you can
//      set "props" to "none" so it will never copy.
//
//   Q: What happens if the library gets a memory allocation failure
//      while I'm editing? Will I lose my work?
//
//   A: The library allocates all editor memory when you create
//      the tilemap. It allocates a maximally-sized map and a
//      fixed-size undo buffer (and the fixed-size copy buffer
//      is static), and never allocates memory while it's running.
//      So it can't fail due to running out of memory.
//
//   Q: What happens if the library crashes while I'm editing? Will
//      I lose my work?
//
//   A: Yes. Save often.
//
//
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 3 段（第 161-240 行）

```c
// HOW TO COMPILE
//
//   This header file contains both the header file and the
//   implementation file in one. To create the implementation,
//   in one source file define a few symbols first and then
//   include this header:
//
//      #define STB_TILEMAP_EDITOR_IMPLEMENTATION
//      // this triggers the implementation
//
//      void STBTE_DRAW_RECT(int x0, int y0, int x1, int y1, unsigned int color);
//      // this must draw a filled rectangle (exclusive on right/bottom)
//      // color = (r<<16)|(g<<8)|(b)
//
//      void STBTE_DRAW_TILE(int x0, int y0,
//                    unsigned short id, int highlight, float *data);
//      // this draws the tile image identified by 'id' in one of several
//      // highlight modes (see STBTE_drawmode_* in the header section);
//      // if 'data' is NULL, it's drawing the tile in the palette; if 'data'
//      // is not NULL, it's drawing a tile on the map, and that is the data
//      // associated with that map tile
//
//      #include "stb_tilemap_editor.h"
//
//   Optionally you can define the following functions before the include;
//   note these must be macros (but they can just call a function) so
//   this library can #ifdef to detect if you've defined them:
//
//      #define STBTE_PROP_TYPE(int n, short *tiledata, float *params) ...
//      // Returns the type of the n'th property of a given tile, which
//      // controls how it is edited. Legal types are:
//      //     0                    /* no editable property in this slot */
//      //     STBTE_PROP_int       /* uses a slider to adjust value     */
//      //     STBTE_PROP_float     /* uses a weird multi-axis control   */
//      //     STBTE_PROP_bool      /* uses a checkbox to change value   */
//      // And you can bitwise-OR in the following flags:
//      //     STBTE_PROP_disabled
//      // Note that all of these are stored as floats in the param array.
//      // The integer slider is limited in precision based on the space
//      // available on screen, so for wide-ranged integers you may want
//      // to use floats instead.
//      //
//      // Since the tiledata is passed to you, you can choose which property
//      // is bound to that slot based on that data.
//      //
//      // Changing the type of a parameter does not cause the underlying
//      // value to be clamped to the type min/max except when the tile is
//      // explicitly selected.
//
//      #define STBTE_PROP_NAME(int n, short *tiledata, float *params) ...
//      // these return a string with the name for slot #n in the float
//      // property list for the tile.
//
//      #define STBTE_PROP_MIN(int n, short *tiledata) ...your code here...
//      #define STBTE_PROP_MAX(int n, short *tiledata) ...your code here...
//      // These return the allowable range for the property values for
//      // the specified slot. It is never called for boolean types.
//
//      #define STBTE_PROP_FLOAT_SCALE(int n, short *tiledata, float *params)
//      // This rescales the float control for a given property; by default
//      // left mouse drags add integers, right mouse drags adds fractions,
//      // but you can rescale this per-property.
//
//      #define STBTE_FLOAT_CONTROL_GRANULARITY       ... value ...
//      // This returns the number of pixels of mouse motion necessary
//      // to advance the object float control. Default is 4
//
//      #define STBTE_ALLOW_LINK(short *src, float *src_data,  \
//                               short *dest, float *dest_data) ...your code...
//      // this returns true or false depending on whether you allow a link
//      // to be drawn from a tile 'src' to a tile 'dest'. if you don't
//      // define this, linking will not be supported
//
//      #define STBTE_LINK_COLOR(short *src, float *src_data,  \
//                               short *dest, float *dest_data) ...your code...
//      // return a color encoded as a 24-bit unsigned integer in the
//      // form 0xRRGGBB. If you don't define this, default colors will
//      // be used.
//
//
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 4 段（第 241-320 行）

```c
//      [[ support for those below is not implemented yet ]]
//
//      #define STBTE_HITTEST_TILE(x0,y0,id,mx,my)   ...your code here...
//      // this returns true or false depending on whether the mouse
//      // pointer at mx,my is over (touching) a tile of type 'id'
//      // displayed at x0,y0. Normally stb_tilemap_editor just does
//      // this hittest based on the tile geometry, but if you have
//      // tiles whose images extend out of the tile, you'll need this.
//
// ADDITIONAL CONFIGURATION
//
//   The following symbols set static limits which determine how much
//   memory will be allocated for the editor. You can override them
//   by making similar definitions, but memory usage will increase.
//
//      #define STBTE_MAX_TILEMAP_X      200   // max 4096
//      #define STBTE_MAX_TILEMAP_Y      200   // max 4096
//      #define STBTE_MAX_LAYERS         8     // max 32
//      #define STBTE_MAX_CATEGORIES     100
//      #define STBTE_UNDO_BUFFER_BYTES  (1 << 24) // 16 MB
//      #define STBTE_MAX_COPY           90000  // e.g. 300x300
//      #define STBTE_MAX_PROPERTIES     10     // max properties per tile
//
// API
//
//   Further documentation appears in the header-file section below.
//
// EDITING MULTIPLE LEVELS
//
//   You can only have one active editor instance. To switch between multiple
//   levels, you can either store the levels in your own format and copy them
//   in and out of the editor format, or you can create multiple stbte_tilemap
//   objects and switch between them. The latter has the advantage that each
//   stbte_tilemap keeps its own undo state. (The clipboard is global, so
//   either approach allows cut&pasting between levels.)
//
// REVISION HISTORY
//   0.42  fix compilation errors
//   0.41  fix warnings
//   0.40  fix warning
//   0.39  fix warning
//   0.38  fix warning
//   0.37  fix warning
//   0.36  minor compiler support
//   0.35  layername button changes
//          - layername buttons grow with the layer panel
//          - fix stbte_create_map being declared as stbte_create
//          - fix declaration of stbte_create_map
//   0.30  properties release
//          - properties panel for editing user-defined "object" properties
//          - can link each tile to one other tile
//          - keyboard interface
//          - fix eraser tool bug (worked in complex cases, failed in simple)
//          - undo/redo tools have visible disabled state
//          - tiles on higher layers draw on top of adjacent lower-layer tiles
//   0.20  erasable release
//          - eraser tool
//          - fix bug when pasting into protected layer
//          - better color scheme
//          - internal-use color picker
//   0.10  initial release
//
// TODO
//
//   Separate scroll state for each category
//   Implement paint bucket
//   Support STBTE_HITTEST_TILE above
//  ?Cancel drags by clicking other button? - may be fixed
//   Finish support for toolbar at side
//
// CREDITS
//
//
//   Main editor & features
//      Sean Barrett
//   Additional features:
//      Josh Huelsman
//   Bugfixes:
//      Ryan Whitworth
//      Eugene Opalev
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 5 段（第 321-327 行）

```c
//      Rob Loach
//      github:wernsey
//
// LICENSE
//
//   See end of file for license information.
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 6 段（第 328-333 行）

```c


///////////////////////////////////////////////////////////////////////
//
//   HEADER SECTION
```

解说：这一段是说明性注释，记录附近代码的用途、限制或维护提示，方便读者理解后续实现。

## 第 7 段（第 334-336 行）

```c
#ifndef STB_TILEMAP_INCLUDE_STB_TILEMAP_EDITOR_H
#define STB_TILEMAP_INCLUDE_STB_TILEMAP_EDITOR_H
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 8 段（第 337-344 行）

```c
#ifdef _WIN32
  #ifndef _CRT_SECURE_NO_WARNINGS
  #define _CRT_SECURE_NO_WARNINGS
  #endif
  #include <stdlib.h>
  #include <stdio.h>
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 9 段（第 345-354 行）

```c
typedef struct stbte_tilemap stbte_tilemap;

// these are the drawmodes used in STBTE_DRAW_TILE
enum
{
   STBTE_drawmode_deemphasize = -1,
   STBTE_drawmode_normal      =  0,
   STBTE_drawmode_emphasize   =  1,
};
```

解说：这一段声明枚举类型 `stbte_tilemap`，把一组相关常量集中命名，便于后续代码用语义化名字表达状态或选项。

## 第 10 段（第 355-361 行）

```c
// these are the property types
#define STBTE_PROP_none     0
#define STBTE_PROP_int      1
#define STBTE_PROP_float    2
#define STBTE_PROP_bool     3
#define STBTE_PROP_disabled 4
```

解说：这一段定义宏（如 `STBTE_PROP_none, STBTE_PROP_int, STBTE_PROP_float, STBTE_PROP_bool, STBTE_PROP_disabled`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 11 段（第 362-366 行）

```c
////////
//
// creation
//
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 12 段（第 367-377 行）

```c
extern stbte_tilemap *stbte_create_map(int map_x, int map_y, int map_layers, int spacing_x, int spacing_y, int max_tiles);
// create an editable tilemap
//   map_x      : dimensions of map horizontally (user can change this in editor), <= STBTE_MAX_TILEMAP_X
//   map_y      : dimensions of map vertically (user can change this in editor)    <= STBTE_MAX_TILEMAP_Y
//   map_layers : number of layers to use (fixed), <= STBTE_MAX_LAYERS
//   spacing_x  : initial horizontal distance between left edges of map tiles in stb_tilemap_editor pixels
//   spacing_y  : initial vertical distance between top edges of map tiles in stb_tilemap_editor pixels
//   max_tiles  : maximum number of tiles that can defined
//
// If insufficient memory, returns NULL
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 13 段（第 378-386 行）

```c
extern void stbte_define_tile(stbte_tilemap *tm, unsigned short id, unsigned int layermask, const char * category);
// call this repeatedly for each tile to install the tile definitions into the editable tilemap
//   tm        : tilemap created by stbte_create_map
//   id        : unique identifier for each tile, 0 <= id < 32768
//   layermask : bitmask of which layers tile is allowed on: 1 = layer 0, 255 = layers 0..7
//               (note that onscreen, the editor numbers the layers from 1 not 0)
//               layer 0 is the furthest back, layer 1 is just in front of layer 0, etc
//   category  : which category this tile is grouped in
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 14 段（第 387-390 行）

```c
extern void stbte_set_display(int x0, int y0, int x1, int y1);
// call this once to set the size; if you resize, call it again
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 15 段（第 391-395 行）

```c
/////////
//
// every frame
//
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 16 段（第 396-397 行）

```c
extern void stbte_draw(stbte_tilemap *tm);
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 17 段（第 398-404 行）

```c
extern void stbte_tick(stbte_tilemap *tm, float time_in_seconds_since_last_frame);

////////////
//
//  user input
//
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 18 段（第 405-408 行）

```c
// if you're using SDL, call the next function for SDL_MOUSEMOTION, SDL_MOUSEBUTTONDOWN, SDL_MOUSEBUTTONUP, SDL_MOUSEWHEEL;
// the transformation lets you scale from SDL mouse coords to stb_tilemap_editor coords
extern void stbte_mouse_sdl(stbte_tilemap *tm, const void *sdl_event, float xscale, float yscale, int xoffset, int yoffset);
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 19 段（第 409-413 行）

```c
// otherwise, hook these up explicitly:
extern void stbte_mouse_move(stbte_tilemap *tm, int x, int y, int shifted, int scrollkey);
extern void stbte_mouse_button(stbte_tilemap *tm, int x, int y, int right, int down, int shifted, int scrollkey);
extern void stbte_mouse_wheel(stbte_tilemap *tm, int x, int y, int vscroll);
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 20 段（第 414-439 行）

```c
// note: at the moment, mouse wheel events (SDL_MOUSEWHEEL) are ignored.

// for keyboard, define your own mapping from keys to the following actions.
// this is totally optional, as all features are accessible with the mouse
enum stbte_action
{
   STBTE_tool_select,
   STBTE_tool_brush,
   STBTE_tool_erase,
   STBTE_tool_rectangle,
   STBTE_tool_eyedropper,
   STBTE_tool_link,
   STBTE_act_toggle_grid,
   STBTE_act_toggle_links,
   STBTE_act_undo,
   STBTE_act_redo,
   STBTE_act_cut,
   STBTE_act_copy,
   STBTE_act_paste,
   STBTE_scroll_left,
   STBTE_scroll_right,
   STBTE_scroll_up,
   STBTE_scroll_down,
};
extern void stbte_action(stbte_tilemap *tm, enum stbte_action act);
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 21 段（第 440-448 行）

```c
////////////////
//
//  save/load
//
//  There is no editor file format. You have to save and load the data yourself
//  through the following functions. You can also use these functions to get the
//  data to generate game-formatted levels directly. (But make sure you save
//  first! You may also want to autosave to a temp file periodically, etc etc.)
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 22 段（第 449-450 行）

```c
#define STBTE_EMPTY    -1
```

解说：这一段定义宏（如 `STBTE_EMPTY`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 23 段（第 451-453 行）

```c
extern void stbte_get_dimensions(stbte_tilemap *tm, int *max_x, int *max_y);
// get the dimensions of the level, since the user can change them
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 24 段（第 454-457 行）

```c
extern short* stbte_get_tile(stbte_tilemap *tm, int x, int y);
// returns an array of shorts that is 'map_layers' in length. each short is
// either one of the tile_id values from define_tile, or STBTE_EMPTY.
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 25 段（第 458-462 行）

```c
extern float *stbte_get_properties(stbte_tilemap *tm, int x, int y);
// get the property array associated with the tile at x,y. this is an
// array of floats that is STBTE_MAX_PROPERTIES in length; you have to
// interpret the slots according to the semantics you've chosen
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 26 段（第 463-465 行）

```c
extern void stbte_get_link(stbte_tilemap *tm, int x, int y, int *destx, int *desty);
// gets the link associated with the tile at x,y.
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 27 段（第 466-469 行）

```c
extern void stbte_set_dimensions(stbte_tilemap *tm, int max_x, int max_y);
// set the dimensions of the level, overrides previous stbte_create_map()
// values or anything the user has changed
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 28 段（第 470-473 行）

```c
extern void stbte_clear_map(stbte_tilemap *tm);
// clears the map, including the region outside the defined region, so if the
// user expands the map, they won't see garbage there
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 29 段（第 474-476 行）

```c
extern void stbte_set_tile(stbte_tilemap *tm, int x, int y, int layer, signed short tile);
// tile is your tile_id from define_tile, or STBTE_EMPTY
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 30 段（第 477-479 行）

```c
extern void stbte_set_property(stbte_tilemap *tm, int x, int y, int n, float val);
// set the value of the n'th slot of the tile at x,y
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 31 段（第 480-483 行）

```c
extern void stbte_set_link(stbte_tilemap *tm, int x, int y, int destx, int desty);
// set a link going from x,y to destx,desty. to force no link,
// use destx=desty=-1
```

解说：这一段进行变量初始化或状态更新，为后续计算准备输入数据，或保存当前步骤得到的中间结果。

## 第 32 段（第 484-488 行）

```c
////////
//
// optional
//
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 33 段（第 489-492 行）

```c
extern void stbte_set_background_tile(stbte_tilemap *tm, short id);
// selects the tile to fill the bottom layer with and used to clear bottom tiles to;
// should be same ID as
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 34 段（第 493-496 行）

```c
extern void stbte_set_sidewidths(int left, int right);
// call this once to set the left & right side widths. don't call
// it again since the user can change it
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 35 段（第 497-500 行）

```c
extern void stbte_set_spacing(stbte_tilemap *tm, int spacing_x, int spacing_y, int palette_spacing_x, int palette_spacing_y);
// call this to set the spacing of map tiles and the spacing of palette tiles.
// if you rescale your display, call it again (e.g. you can implement map zooming yourself)
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 36 段（第 501-504 行）

```c
extern void stbte_set_layername(stbte_tilemap *tm, int layer, const char *layername);
// sets a string name for your layer that shows in the layer selector. note that this
// makes the layer selector wider. 'layer' is from 0..(map_layers-1)
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 37 段（第 505-506 行）

```c
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 38 段（第 507-508 行）

```c
#ifdef STB_TILEMAP_EDITOR_IMPLEMENTATION
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 39 段（第 509-513 行）

```c
#ifndef STBTE_ASSERT
#define STBTE_ASSERT assert
#include <assert.h>
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 40 段（第 514-519 行）

```c
#ifdef _MSC_VER
#define STBTE__NOTUSED(v)  (void)(v)
#else
#define STBTE__NOTUSED(v)  (void)sizeof(v)
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 41 段（第 520-523 行）

```c
#ifndef STBTE_MAX_TILEMAP_X
#define STBTE_MAX_TILEMAP_X      200
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 42 段（第 524-527 行）

```c
#ifndef STBTE_MAX_TILEMAP_Y
#define STBTE_MAX_TILEMAP_Y      200
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 43 段（第 528-531 行）

```c
#ifndef STBTE_MAX_LAYERS
#define STBTE_MAX_LAYERS         8
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 44 段（第 532-535 行）

```c
#ifndef STBTE_MAX_CATEGORIES
#define STBTE_MAX_CATEGORIES     100
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 45 段（第 536-539 行）

```c
#ifndef STBTE_MAX_COPY
#define STBTE_MAX_COPY           65536
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 46 段（第 540-543 行）

```c
#ifndef STBTE_UNDO_BUFFER_BYTES
#define STBTE_UNDO_BUFFER_BYTES  (1 << 24) // 16 MB
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 47 段（第 544-548 行）

```c
#ifndef STBTE_PROP_TYPE
#define STBTE__NO_PROPS
#define STBTE_PROP_TYPE(n,td,tp)   0
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 48 段（第 549-552 行）

```c
#ifndef STBTE_PROP_NAME
#define STBTE_PROP_NAME(n,td,tp)  ""
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 49 段（第 553-556 行）

```c
#ifndef STBTE_MAX_PROPERTIES
#define STBTE_MAX_PROPERTIES           10
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 50 段（第 557-560 行）

```c
#ifndef STBTE_PROP_MIN
#define STBTE_PROP_MIN(n,td,tp)  0
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 51 段（第 561-564 行）

```c
#ifndef STBTE_PROP_MAX
#define STBTE_PROP_MAX(n,td,tp)  100.0
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 52 段（第 565-568 行）

```c
#ifndef STBTE_PROP_FLOAT_SCALE
#define STBTE_PROP_FLOAT_SCALE(n,td,tp)  1   // default scale size
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 53 段（第 569-572 行）

```c
#ifndef STBTE_FLOAT_CONTROL_GRANULARITY
#define STBTE_FLOAT_CONTROL_GRANULARITY 4
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 54 段（第 574-575 行）

```c
#define STBTE__UNDO_BUFFER_COUNT  (STBTE_UNDO_BUFFER_BYTES>>1)
```

解说：这一段定义宏（如 `STBTE__UNDO_BUFFER_COUNT`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 55 段（第 576-585 行）

```c
#if STBTE_MAX_TILEMAP_X > 4096 || STBTE_MAX_TILEMAP_Y > 4096
#error "Maximum editable map size is 4096 x 4096"
#endif
#if STBTE_MAX_LAYERS > 32
#error "Maximum layers allowed is 32"
#endif
#if STBTE_UNDO_BUFFER_COUNT & (STBTE_UNDO_BUFFER_COUNT-1)
#error "Undo buffer size must be a power of 2"
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 56 段（第 586-589 行）

```c
#if STBTE_MAX_PROPERTIES == 0
#define STBTE__NO_PROPS
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 57 段（第 590-594 行）

```c
#ifdef STBTE__NO_PROPS
#undef STBTE_MAX_PROPERTIES
#define STBTE_MAX_PROPERTIES 1  // so we can declare arrays
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 58 段（第 595-599 行）

```c
typedef struct
{
   short x,y;
} stbte__link;
```

解说：这一段声明结构体 `stbte__link`，把相关字段组织成一个数据对象，供后续算法在函数之间传递和维护状态。

## 第 59 段（第 600-608 行）

```c
enum
{
   STBTE__base,
   STBTE__outline,
   STBTE__text,

   STBTE__num_color_aspects,
};
```

解说：这一段声明枚举类型，把一组相关常量集中命名，便于后续代码用语义化名字表达状态或选项。

## 第 60 段（第 609-620 行）

```c
enum
{
   STBTE__idle,
   STBTE__over,
   STBTE__down,
   STBTE__over_down,
   STBTE__selected,
   STBTE__selected_over,
   STBTE__disabled,
   STBTE__num_color_states,
};
```

解说：这一段声明枚举类型，把一组相关常量集中命名，便于后续代码用语义化名字表达状态或选项。

## 第 61 段（第 621-639 行）

```c
enum
{
   STBTE__cexpander,
   STBTE__ctoolbar,
   STBTE__ctoolbar_button,
   STBTE__cpanel,
   STBTE__cpanel_sider,
   STBTE__cpanel_sizer,
   STBTE__cscrollbar,
   STBTE__cmapsize,
   STBTE__clayer_button,
   STBTE__clayer_hide,
   STBTE__clayer_lock,
   STBTE__clayer_solo,
   STBTE__ccategory_button,

   STBTE__num_color_modes,
};
```

解说：这一段声明枚举类型，把一组相关常量集中命名，便于后续代码用语义化名字表达状态或选项。

## 第 62 段（第 640-649 行）

```c
#ifdef STBTE__COLORPICKER
static char *stbte__color_names[] =
{
   "expander", "toolbar", "tool button", "panel",
   "panel c1", "panel c2", "scollbar", "map button",
   "layer", "hide", "lock", "solo",
   "category",
};
#endif // STBTE__COLORPICKER
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 63 段（第 650-707 行）

```c
      // idle,    over,     down,    over&down, selected, sel&over, disabled
static int stbte__color_table[STBTE__num_color_modes][STBTE__num_color_aspects][STBTE__num_color_states] =
{
   {
      { 0x000000, 0x84987c, 0xdcdca8, 0xdcdca8, 0x40c040, 0x60d060, 0x505050, },
      { 0xa4b090, 0xe0ec80, 0xffffc0, 0xffffc0, 0x80ff80, 0x80ff80, 0x606060, },
      { 0xffffff, 0xffffff, 0xffffff, 0xffffff, 0xffffff, 0xffffff, 0x909090, },
   }, {
      { 0x808890, 0x606060, 0x606060, 0x606060, 0x606060, 0x606060, 0x606060, },
      { 0x605860, 0x606060, 0x606060, 0x606060, 0x606060, 0x606060, 0x606060, },
      { 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, 0x000000, },
   }, {
      { 0x3c5068, 0x7088a8, 0x647488, 0x94b4dc, 0x8890c4, 0x9caccc, 0x404040, },
      { 0x889cb8, 0x889cb8, 0x889cb8, 0x889cb8, 0x84c4e8, 0xacc8ff, 0x0c0c08, },
      { 0xbcc4cc, 0xffffff, 0xffffff, 0xffffff, 0xffffff, 0xffffff, 0x707074, },
   }, {
      { 0x403848, 0x403010, 0x403010, 0x403010, 0x403010, 0x403010, 0x303024, },
      { 0x68546c, 0xc08040, 0xc08040, 0xc08040, 0xc08040, 0xc08040, 0x605030, },
      { 0xf4e4ff, 0xffffff, 0xffffff, 0xffffff, 0xffffff, 0xffffff, 0x909090, },
   }, {
      { 0xb4b04c, 0xacac60, 0xc0ffc0, 0xc0ffc0, 0x40c040, 0x60d060, 0x505050, },
      { 0xa0a04c, 0xd0d04c, 0xffff80, 0xffff80, 0x80ff80, 0x80ff80, 0x606060, },
      { 0xffffff, 0xffffff, 0xffffff, 0xffffff, 0xffffff, 0xffffff, 0x909090, },
   }, {
      { 0x40c440, 0x60d060, 0xc0ffc0, 0xc0ffc0, 0x40c040, 0x60d060, 0x505050, },
      { 0x40c040, 0x80ff80, 0x80ff80, 0x80ff80, 0x80ff80, 0x80ff80, 0x606060, },
      { 0xffffff, 0xffffff, 0xffffff, 0xffffff, 0xffffff, 0xffffff, 0x909090, },
   }, {
      { 0x9090ac, 0xa0a0b8, 0xbcb8cc, 0xbcb8cc, 0x909040, 0x909040, 0x909040, },
      { 0xa0a0b8, 0xb0b4d0, 0xa0a0b8, 0xa0a0b8, 0xa0a050, 0xa0a050, 0xa0a050, },
      { 0x808088, 0x808030, 0x808030, 0x808030, 0x808030, 0x808030, 0x808030, },
   }, {
      { 0x704c70, 0x885c8c, 0x9c68a4, 0xb870bc, 0xb490bc, 0xb490bc, 0x302828, },
      { 0x646064, 0xcca8d4, 0xc060c0, 0xa07898, 0xe0b8e0, 0xe0b8e0, 0x403838, },
      { 0xdccce4, 0xffffff, 0xffffff, 0xffffff, 0xffffff, 0xffffff, 0x909090, },
   }, {
      { 0x704c70, 0x885c8c, 0x9c68a4, 0xb870bc, 0xb490bc, 0xb490bc, 0x302828, },
      { 0xb09cb4, 0xcca8d4, 0xc060c0, 0xa07898, 0xe0b8e0, 0xe0b8e0, 0x403838, },
      { 0xdccce4, 0xffffff, 0xffffff, 0xffffff, 0xffffff, 0xffffff, 0x909090, },
   }, {
      { 0x646494, 0x888cb8, 0xb0b0b0, 0xb0b0cc, 0x9c9cf4, 0x8888b0, 0x50506c, },
      { 0x9090a4, 0xb0b4d4, 0xb0b0dc, 0xb0b0cc, 0xd0d0fc, 0xd0d4f0, 0x606060, },
      { 0xb4b4d4, 0xe4e4ff, 0xffffff, 0xffffff, 0xe0e4ff, 0xececff, 0x909090, },
   }, {
      { 0x646444, 0x888c64, 0xb0b0b0, 0xb0b088, 0xaca858, 0x88886c, 0x505050, },
      { 0x88886c, 0xb0b490, 0xb0b0b0, 0xb0b088, 0xd8d898, 0xd0d4b0, 0x606060, },
      { 0xb4b49c, 0xffffd8, 0xffffff, 0xffffd4, 0xffffdc, 0xffffcc, 0x909090, },
   }, {
      { 0x906464, 0xb48c8c, 0xd4b0b0, 0xdcb0b0, 0xff9c9c, 0xc88888, 0x505050, },
      { 0xb47c80, 0xd4b4b8, 0xc4a8a8, 0xdcb0b0, 0xffc0c0, 0xfce8ec, 0x606060, },
      { 0xe0b4b4, 0xffdcd8, 0xffd8d4, 0xffe0e4, 0xffece8, 0xffffff, 0x909090, },
   }, {
      { 0x403848, 0x403848, 0x403848, 0x886894, 0x7c80c8, 0x7c80c8, 0x302828, },
      { 0x403848, 0x403848, 0x403848, 0x403848, 0x7c80c8, 0x7c80c8, 0x403838, },
      { 0xc8c4c8, 0xffffff, 0xffffff, 0xffffff, 0xe8e8ec, 0xffffff, 0x909090, },
   },
};
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 64 段（第 708-716 行）

```c
#define STBTE_COLOR_TILEMAP_BACKGROUND      0x000000
#define STBTE_COLOR_TILEMAP_BORDER          0x203060
#define STBTE_COLOR_TILEMAP_HIGHLIGHT       0xffffff
#define STBTE_COLOR_GRID                    0x404040
#define STBTE_COLOR_SELECTION_OUTLINE1      0xdfdfdf
#define STBTE_COLOR_SELECTION_OUTLINE2      0x303030
#define STBTE_COLOR_TILEPALETTE_OUTLINE     0xffffff
#define STBTE_COLOR_TILEPALETTE_BACKGROUND  0x000000
```

解说：这一段定义宏（如 `STBTE_COLOR_TILEMAP_BACKGROUND, STBTE_COLOR_TILEMAP_BORDER, STBTE_COLOR_TILEMAP_HIGHLIGHT, STBTE_COLOR_GRID, STBTE_COLOR_SELECTION_OUTLINE1`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 65 段（第 717-720 行）

```c
#ifndef STBTE_LINK_COLOR
#define STBTE_LINK_COLOR(src,sp,dest,dp)    0x5030ff
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 66 段（第 721-724 行）

```c
#ifndef STBTE_LINK_COLOR_DRAWING
#define STBTE_LINK_COLOR_DRAWING            0xff40ff
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 67 段（第 725-728 行）

```c
#ifndef STBTE_LINK_COLOR_DISALLOWED
#define STBTE_LINK_COLOR_DISALLOWED         0x602060
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 68 段（第 729-743 行）

```c

// disabled, selected, down, over
static unsigned char stbte__state_to_index[2][2][2][2] =
{
   {
      { { STBTE__idle    , STBTE__over          }, { STBTE__down    , STBTE__over_down }, },
      { { STBTE__selected, STBTE__selected_over }, { STBTE__down    , STBTE__over_down }, },
   },{
      { { STBTE__disabled, STBTE__disabled      }, { STBTE__disabled, STBTE__disabled  }, },
      { { STBTE__selected, STBTE__selected_over }, { STBTE__disabled, STBTE__disabled  }, },
   }
};
#define STBTE__INDEX_FOR_STATE(disable,select,down,over) stbte__state_to_index[disable][select][down][over]
#define STBTE__INDEX_FOR_ID(id,disable,select) STBTE__INDEX_FOR_STATE(disable,select,STBTE__IS_ACTIVE(id),STBTE__IS_HOT(id))
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 69 段（第 744-781 行）

```c
#define STBTE__FONT_HEIGHT    9
static short stbte__font_offset[95+16];
static short stbte__fontdata[769] =
{
   4,9,6,9,9,9,9,8,9,8,4,9,7,7,7,7,4,2,6,8,6,6,7,3,4,4,8,6,3,6,2,6,6,6,6,6,6,
   6,6,6,6,6,2,3,5,4,5,6,6,6,6,6,6,6,6,6,6,6,6,7,6,7,7,7,6,7,6,6,6,6,7,7,6,6,
   6,4,6,4,7,7,3,6,6,5,6,6,5,6,6,4,5,6,4,7,6,6,6,6,6,6,6,6,6,7,6,6,6,5,2,5,8,
   0,0,0,0,2,253,130,456,156,8,72,184,64,2,125,66,64,160,64,146,511,146,146,
   511,146,146,511,146,511,257,341,297,341,297,341,257,511,16,56,124,16,16,16,
   124,56,16,96,144,270,261,262,136,80,48,224,192,160,80,40,22,14,15,3,448,496,
   496,240,232,20,10,5,2,112,232,452,450,225,113,58,28,63,30,60,200,455,257,
   257,0,0,0,257,257,455,120,204,132,132,159,14,4,4,14,159,132,132,204,120,8,
   24,56,120,56,24,8,32,48,56,60,56,48,32,0,0,0,0,111,111,7,7,0,0,7,7,34,127,
   127,34,34,127,127,34,36,46,107,107,58,18,99,51,24,12,102,99,48,122,79,93,
   55,114,80,4,7,3,62,127,99,65,65,99,127,62,8,42,62,28,28,62,42,8,8,8,62,62,
   8,8,128,224,96,8,8,8,8,8,8,96,96,96,48,24,12,6,3,62,127,89,77,127,62,64,66,
   127,127,64,64,98,115,89,77,71,66,33,97,73,93,119,35,24,28,22,127,127,16,39,
   103,69,69,125,57,62,127,73,73,121,48,1,1,113,121,15,7,54,127,73,73,127,54,
   6,79,73,105,63,30,54,54,128,246,118,8,28,54,99,65,20,20,20,20,65,99,54,28,
   8,2,3,105,109,7,2,30,63,33,45,47,46,124,126,19,19,126,124,127,127,73,73,127,
   54,62,127,65,65,99,34,127,127,65,99,62,28,127,127,73,73,73,65,127,127,9,9,
   9,1,62,127,65,73,121,121,127,127,8,8,127,127,65,65,127,127,65,65,32,96,64,
   64,127,63,127,127,8,28,54,99,65,127,127,64,64,64,64,127,127,6,12,6,127,127,
   127,127,6,12,24,127,127,62,127,65,65,65,127,62,127,127,9,9,15,6,62,127,65,
   81,49,127,94,127,127,9,25,127,102,70,79,73,73,121,49,1,1,127,127,1,1,63,127,
   64,64,127,63,15,31,48,96,48,31,15,127,127,48,24,48,127,127,99,119,28,28,119,
   99,7,15,120,120,15,7,97,113,89,77,71,67,127,127,65,65,3,6,12,24,48,96,65,
   65,127,127,8,12,6,3,6,12,8,64,64,64,64,64,64,64,3,7,4,32,116,84,84,124,120,
   127,127,68,68,124,56,56,124,68,68,68,56,124,68,68,127,127,56,124,84,84,92,
   24,8,124,126,10,10,56,380,324,324,508,252,127,127,4,4,124,120,72,122,122,
   64,256,256,256,506,250,126,126,16,56,104,64,66,126,126,64,124,124,24,56,28,
   124,120,124,124,4,4,124,120,56,124,68,68,124,56,508,508,68,68,124,56,56,124,
   68,68,508,508,124,124,4,4,12,8,72,92,84,84,116,36,4,4,62,126,68,68,60,124,
   64,64,124,124,28,60,96,96,60,28,28,124,112,56,112,124,28,68,108,56,56,108,
   68,284,316,352,320,508,252,68,100,116,92,76,68,8,62,119,65,65,127,127,65,
   65,119,62,8,16,24,12,12,24,24,12,4,
};
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 70 段（第 782-789 行）

```c
typedef struct
{
   short id;
   unsigned short category_id;
   char *category;
   unsigned int layermask;
} stbte__tileinfo;
```

解说：这一段声明结构体 `stbte__tileinfo`，把相关字段组织成一个数据对象，供后续算法在函数之间传递和维护状态。

## 第 71 段（第 790-791 行）

```c
#define MAX_LAYERMASK    (1 << (8*sizeof(unsigned int)))
```

解说：这一段定义宏（如 `MAX_LAYERMASK`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 72 段（第 792-793 行）

```c
typedef short stbte__tiledata;
```

解说：这一段通过 `typedef` 给类型取别名，减少平台差异或复杂类型签名对后续代码的干扰。

## 第 73 段（第 794-795 行）

```c
#define STBTE__NO_TILE   -1
```

解说：这一段定义宏（如 `STBTE__NO_TILE`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 74 段（第 796-808 行）

```c
enum
{
   STBTE__panel_toolbar,
   STBTE__panel_colorpick,
   STBTE__panel_info,
   STBTE__panel_layers,
   STBTE__panel_props,
   STBTE__panel_categories,
   STBTE__panel_tiles,

   STBTE__num_panel,
};
```

解说：这一段声明枚举类型，把一组相关常量集中命名，便于后续代码用语义化名字表达状态或选项。

## 第 75 段（第 809-816 行）

```c
enum
{
   STBTE__side_left,
   STBTE__side_right,
   STBTE__side_top,
   STBTE__side_bottom,
};
```

解说：这一段声明枚举类型，把一组相关常量集中命名，便于后续代码用语义化名字表达状态或选项。

## 第 76 段（第 817-836 行）

```c
enum
{
   STBTE__tool_select,
   STBTE__tool_brush,
   STBTE__tool_erase,
   STBTE__tool_rect,
   STBTE__tool_eyedrop,
   STBTE__tool_fill,
   STBTE__tool_link,

   STBTE__tool_showgrid,
   STBTE__tool_showlinks,

   STBTE__tool_undo,
   STBTE__tool_redo,
   // copy/cut/paste aren't included here because they're displayed differently

   STBTE__num_tool,
};
```

解说：这一段声明枚举类型，把一组相关常量集中命名，便于后续代码用语义化名字表达状态或选项。

## 第 77 段（第 837-839 行）

```c
// icons are stored in the 0-31 range of ASCII in the font
static int toolchar[] = { 26,24,25,20,23,22,18, 19,17, 29,28, };
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 78 段（第 840-846 行）

```c
enum
{
   STBTE__propmode_default,
   STBTE__propmode_always,
   STBTE__propmode_never,
};
```

解说：这一段声明枚举类型，把一组相关常量集中命名，便于后续代码用语义化名字表达状态或选项。

## 第 79 段（第 847-860 行）

```c
enum
{
   STBTE__paint,

   // from here down does hittesting
   STBTE__tick,
   STBTE__mousemove,
   STBTE__mousewheel,
   STBTE__leftdown,
   STBTE__leftup,
   STBTE__rightdown,
   STBTE__rightup,
};
```

解说：这一段声明枚举类型，把一组相关常量集中命名，便于后续代码用语义化名字表达状态或选项。

## 第 80 段（第 861-869 行）

```c
typedef struct
{
   int expanded, mode;
   int delta_height;     // number of rows they've requested for this
   int side;
   int width,height;
   int x0,y0;
} stbte__panel;
```

解说：这一段声明结构体 `stbte__panel`，把相关字段组织成一个数据对象，供后续算法在函数之间传递和维护状态。

## 第 81 段（第 870-874 行）

```c
typedef struct
{
   int x0,y0,x1,y1,color;
} stbte__colorrect;
```

解说：这一段声明结构体 `stbte__colorrect`，把相关字段组织成一个数据对象，供后续算法在函数之间传递和维护状态。

## 第 82 段（第 875-876 行）

```c
#define STBTE__MAX_DELAYRECT 256
```

解说：这一段定义宏（如 `STBTE__MAX_DELAYRECT`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 83 段（第 877-917 行）

```c
typedef struct
{
   int tool, active_event;
   int active_id, hot_id, next_hot_id;
   int event;
   int mx,my, dx,dy;
   int ms_time;
   int shift, scrollkey;
   int initted;
   int side_extended[2];
   stbte__colorrect delayrect[STBTE__MAX_DELAYRECT];
   int delaycount;
   int show_grid, show_links;
   int brush_state; // used to decide which kind of erasing
   int eyedrop_x, eyedrop_y, eyedrop_last_layer;
   int pasting, paste_x, paste_y;
   int scrolling, start_x, start_y;
   int last_mouse_x, last_mouse_y;
   int accum_x, accum_y;
   int linking;
   int dragging;
   int drag_x, drag_y, drag_w, drag_h;
   int drag_offx, drag_offy, drag_dest_x, drag_dest_y;
   int undoing;
   int has_selection, select_x0, select_y0, select_x1, select_y1;
   int sx,sy;
   int x0,y0,x1,y1, left_width, right_width; // configurable widths
   float alert_timer;
   const char *alert_msg;
   float dt;
   stbte__panel panel[STBTE__num_panel];
   short copybuffer[STBTE_MAX_COPY][STBTE_MAX_LAYERS];
   float copyprops[STBTE_MAX_COPY][STBTE_MAX_PROPERTIES];
#ifdef STBTE_ALLOW_LINK
   stbte__link copylinks[STBTE_MAX_COPY];
#endif
   int copy_src_x, copy_src_y;
   stbte_tilemap *copy_src;
   int copy_width,copy_height,has_copy,copy_has_props;
} stbte__ui_t;
```

解说：这一段声明结构体 `stbte__ui_t`，把相关字段组织成一个数据对象，供后续算法在函数之间传递和维护状态。

## 第 84 段（第 918-920 行）

```c
// there's only one UI system at a time, so we can globalize this
static stbte__ui_t stbte__ui = { STBTE__tool_brush, 0 };
```

解说：这一段进行变量初始化或状态更新，为后续计算准备输入数据，或保存当前步骤得到的中间结果。

## 第 85 段（第 921-924 行）

```c
#define STBTE__INACTIVE()     (stbte__ui.active_id == 0)
#define STBTE__IS_ACTIVE(id)  (stbte__ui.active_id == (id))
#define STBTE__IS_HOT(id)     (stbte__ui.hot_id    == (id))
```

解说：这一段定义宏（如 `STBTE__INACTIVE, STBTE__IS_ACTIVE, STBTE__IS_HOT`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 86 段（第 925-927 行）

```c
#define STBTE__BUTTON_HEIGHT            (STBTE__FONT_HEIGHT + 2 * STBTE__BUTTON_INTERNAL_SPACING)
#define STBTE__BUTTON_INTERNAL_SPACING  (2 + (STBTE__FONT_HEIGHT>>4))
```

解说：这一段定义宏（如 `STBTE__BUTTON_HEIGHT, STBTE__BUTTON_INTERNAL_SPACING`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 87 段（第 928-934 行）

```c
typedef struct
{
   const char *name;
   int locked;
   int hidden;
} stbte__layer;
```

解说：这一段声明结构体 `stbte__layer`，把相关字段组织成一个数据对象，供后续算法在函数之间传递和维护状态。

## 第 88 段（第 935-941 行）

```c
enum
{
   STBTE__unlocked,
   STBTE__protected,
   STBTE__locked,
};
```

解说：这一段声明枚举类型，把一组相关常量集中命名，便于后续代码用语义化名字表达状态或选项。

## 第 89 段（第 942-977 行）

```c
struct stbte_tilemap
{
    stbte__tiledata data[STBTE_MAX_TILEMAP_Y][STBTE_MAX_TILEMAP_X][STBTE_MAX_LAYERS];
    float props[STBTE_MAX_TILEMAP_Y][STBTE_MAX_TILEMAP_X][STBTE_MAX_PROPERTIES];
    #ifdef STBTE_ALLOW_LINK
    stbte__link link[STBTE_MAX_TILEMAP_Y][STBTE_MAX_TILEMAP_X];
    int linkcount[STBTE_MAX_TILEMAP_Y][STBTE_MAX_TILEMAP_X];
    #endif
    int max_x, max_y, num_layers;
    int spacing_x, spacing_y;
    int palette_spacing_x, palette_spacing_y;
    int scroll_x,scroll_y;
    int cur_category, cur_tile, cur_layer;
    char *categories[STBTE_MAX_CATEGORIES];
    int num_categories, category_scroll;
    stbte__tileinfo *tiles;
    int num_tiles, max_tiles, digits;
    unsigned char undo_available_valid;
    unsigned char undo_available;
    unsigned char redo_available;
    unsigned char padding;
    int cur_palette_count;
    int palette_scroll;
    int tileinfo_dirty;
    stbte__layer layerinfo[STBTE_MAX_LAYERS];
    int has_layer_names;
    int layername_width;
    int layer_scroll;
    int propmode;
    int solo_layer;
    int undo_pos, undo_len, redo_len;
    short background_tile;
    unsigned char id_in_use[32768>>3];
    short *undo_buffer;
};
```

解说：这一段声明结构体 `undo_buffer`，把相关字段组织成一个数据对象，供后续算法在函数之间传递和维护状态。

## 第 90 段（第 978-979 行）

```c
static char *default_category = (char*) "[unassigned]";
```

解说：这一段进行变量初始化或状态更新，为后续计算准备输入数据，或保存当前步骤得到的中间结果。

## 第 91 段（第 980-1006 行）

```c
static void stbte__init_gui(void)
{
   int i,n;
   stbte__ui.initted = 1;
   // init UI state
   stbte__ui.show_links = 1;
   for (i=0; i < STBTE__num_panel; ++i) {
      stbte__ui.panel[i].expanded     = 1; // visible if not autohidden
      stbte__ui.panel[i].delta_height = 0;
      stbte__ui.panel[i].side         = STBTE__side_left;
   }
   stbte__ui.panel[STBTE__panel_toolbar  ].side = STBTE__side_top;
   stbte__ui.panel[STBTE__panel_colorpick].side = STBTE__side_right;

   if (stbte__ui.left_width == 0)
      stbte__ui.left_width = 80;
   if (stbte__ui.right_width == 0)
      stbte__ui.right_width = 80;

   // init font
   n=95+16;
   for (i=0; i < 95+16; ++i) {
      stbte__font_offset[i] = n;
      n += stbte__fontdata[i];
   }
}
```

解说：这一段实现或声明库接口 `stbte__init_gui`，把“te  init gui”相关能力封装成可被外部或内部复用的函数。

## 第 92 段（第 1007-1066 行）

```c
stbte_tilemap *stbte_create_map(int map_x, int map_y, int map_layers, int spacing_x, int spacing_y, int max_tiles)
{
   int i;
   stbte_tilemap *tm;
   STBTE_ASSERT(map_layers >= 0 && map_layers <= STBTE_MAX_LAYERS);
   STBTE_ASSERT(map_x >= 0 && map_x <= STBTE_MAX_TILEMAP_X);
   STBTE_ASSERT(map_y >= 0 && map_y <= STBTE_MAX_TILEMAP_Y);
   if (map_x < 0 || map_y < 0 || map_layers < 0 ||
       map_x > STBTE_MAX_TILEMAP_X || map_y > STBTE_MAX_TILEMAP_Y || map_layers > STBTE_MAX_LAYERS)
      return NULL;

   if (!stbte__ui.initted)
      stbte__init_gui();

   tm = (stbte_tilemap *) malloc(sizeof(*tm) + sizeof(*tm->tiles) * max_tiles + STBTE_UNDO_BUFFER_BYTES);
   if (tm == NULL)
      return NULL;

   tm->tiles = (stbte__tileinfo *) (tm+1);
   tm->undo_buffer = (short *) (tm->tiles + max_tiles);
   tm->num_layers = map_layers;
   tm->max_x = map_x;
   tm->max_y = map_y;
   tm->spacing_x = spacing_x;
   tm->spacing_y = spacing_y;
   tm->scroll_x = 0;
   tm->scroll_y = 0;
   tm->palette_scroll = 0;
   tm->palette_spacing_x = spacing_x+1;
   tm->palette_spacing_y = spacing_y+1;
   tm->cur_category = -1;
   tm->cur_tile = 0;
   tm->solo_layer = -1;
   tm->undo_len = 0;
   tm->redo_len = 0;
   tm->undo_pos = 0;
   tm->category_scroll = 0;
   tm->layer_scroll = 0;
   tm->propmode = 0;
   tm->has_layer_names = 0;
   tm->layername_width = 0;
   tm->undo_available_valid = 0;

   for (i=0; i < tm->num_layers; ++i) {
      tm->layerinfo[i].hidden = 0;
      tm->layerinfo[i].locked = STBTE__unlocked;
      tm->layerinfo[i].name   = 0;
   }

   tm->background_tile = STBTE__NO_TILE;
   stbte_clear_map(tm);

   tm->max_tiles = max_tiles;
   tm->num_tiles = 0;
   for (i=0; i < 32768/8; ++i)
      tm->id_in_use[i] = 0;
   tm->tileinfo_dirty = 1;
   return tm;
}
```

解说：这一段涉及动态内存管理，负责申请、调整或释放运行时数据结构需要的存储空间。

## 第 93 段（第 1067-1079 行）

```c
void stbte_set_background_tile(stbte_tilemap *tm, short id)
{
   int i;
   STBTE_ASSERT(id >= -1);
   // STBTE_ASSERT(id < 32768);
   if (id < -1)
      return;
   for (i=0; i < STBTE_MAX_TILEMAP_X * STBTE_MAX_TILEMAP_Y; ++i)
      if (tm->data[0][i][0] == -1)
         tm->data[0][i][0] = id;
   tm->background_tile = id;
}
```

解说：这一段实现或声明库接口 `stbte_set_background_tile`，把“te set background tile”相关能力封装成可被外部或内部复用的函数。

## 第 94 段（第 1080-1087 行）

```c
void stbte_set_spacing(stbte_tilemap *tm, int spacing_x, int spacing_y, int palette_spacing_x, int palette_spacing_y)
{
   tm->spacing_x = spacing_x;
   tm->spacing_y = spacing_y;
   tm->palette_spacing_x = palette_spacing_x;
   tm->palette_spacing_y = palette_spacing_y;
}
```

解说：这一段实现或声明库接口 `stbte_set_spacing`，把“te set spacing”相关能力封装成可被外部或内部复用的函数。

## 第 95 段（第 1088-1093 行）

```c
void stbte_set_sidewidths(int left, int right)
{
   stbte__ui.left_width  = left;
   stbte__ui.right_width = right;
}
```

解说：这一段实现或声明库接口 `stbte_set_sidewidths`，把“te set sidewidths”相关能力封装成可被外部或内部复用的函数。

## 第 96 段（第 1094-1101 行）

```c
void stbte_set_display(int x0, int y0, int x1, int y1)
{
   stbte__ui.x0 = x0;
   stbte__ui.y0 = y0;
   stbte__ui.x1 = x1;
   stbte__ui.y1 = y1;
}
```

解说：这一段实现或声明库接口 `stbte_set_display`，把“te set display”相关能力封装成可被外部或内部复用的函数。

## 第 97 段（第 1102-1120 行）

```c
void stbte_define_tile(stbte_tilemap *tm, unsigned short id, unsigned int layermask, const char * category_c)
{
   char *category = (char *) category_c;
   STBTE_ASSERT(id < 32768);
   STBTE_ASSERT(tm->num_tiles < tm->max_tiles);
   STBTE_ASSERT((tm->id_in_use[id>>3]&(1<<(id&7))) == 0);
   if (id >= 32768 || tm->num_tiles >= tm->max_tiles || (tm->id_in_use[id>>3]&(1<<(id&7))))
      return;

   if (category == NULL)
      category = (char*) default_category;
   tm->id_in_use[id>>3] |= 1 << (id&7);
   tm->tiles[tm->num_tiles].category    = category;
   tm->tiles[tm->num_tiles].id        = id;
   tm->tiles[tm->num_tiles].layermask = layermask;
   ++tm->num_tiles;
   tm->tileinfo_dirty = 1;
}
```

解说：这一段实现或声明库接口 `stbte_define_tile`，把“te define tile”相关能力封装成可被外部或内部复用的函数。

## 第 98 段（第 1121-1122 行）

```c
static int stbte__text_width(const char *str);
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 99 段（第 1123-1134 行）

```c
void stbte_set_layername(stbte_tilemap *tm, int layer, const char *layername)
{
   STBTE_ASSERT(layer >= 0 && layer < tm->num_layers);
   if (layer >= 0 && layer < tm->num_layers) {
      int width;
      tm->layerinfo[layer].name = layername;
      tm->has_layer_names = 1;
      width = stbte__text_width(layername);
      tm->layername_width = (width > tm->layername_width ? width : tm->layername_width);
   }
}
```

解说：这一段实现或声明库接口 `stbte_set_layername`，把“te set layername”相关能力封装成可被外部或内部复用的函数。

## 第 100 段（第 1135-1140 行）

```c
void stbte_get_dimensions(stbte_tilemap *tm, int *max_x, int *max_y)
{
   *max_x = tm->max_x;
   *max_y = tm->max_y;
}
```

解说：这一段实现或声明库接口 `stbte_get_dimensions`，把“te get dimensions”相关能力封装成可被外部或内部复用的函数。

## 第 101 段（第 1141-1148 行）

```c
short* stbte_get_tile(stbte_tilemap *tm, int x, int y)
{
   STBTE_ASSERT(x >= 0 && x < tm->max_x && y >= 0 && y < tm->max_y);
   if (x < 0 || x >= STBTE_MAX_TILEMAP_X || y < 0 || y >= STBTE_MAX_TILEMAP_Y)
      return NULL;
   return tm->data[y][x];
}
```

解说：这一段实现或声明库接口 `stbte_get_tile`，把“te get tile”相关能力封装成可被外部或内部复用的函数。

## 第 102 段（第 1149-1156 行）

```c
float *stbte_get_properties(stbte_tilemap *tm, int x, int y)
{
   STBTE_ASSERT(x >= 0 && x < tm->max_x && y >= 0 && y < tm->max_y);
   if (x < 0 || x >= STBTE_MAX_TILEMAP_X || y < 0 || y >= STBTE_MAX_TILEMAP_Y)
      return NULL;
   return tm->props[y][x];
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 103 段（第 1157-1173 行）

```c
void stbte_get_link(stbte_tilemap *tm, int x, int y, int *destx, int *desty)
{
   int gx=-1,gy=-1;
   STBTE_ASSERT(x >= 0 && x < tm->max_x && y >= 0 && y < tm->max_y);
#ifdef STBTE_ALLOW_LINK
   if (x >= 0 && x < STBTE_MAX_TILEMAP_X && y >= 0 && y < STBTE_MAX_TILEMAP_Y) {
      gx = tm->link[y][x].x;
      gy = tm->link[y][x].y;
      if (gx >= 0)
         if (!STBTE_ALLOW_LINK(tm->data[y][x], tm->props[y][x], tm->data[gy][gx], tm->props[gy][gx]))
            gx = gy = -1;
   }
#endif
   *destx = gx;
   *desty = gy;
}
```

解说：这一段实现或声明库接口 `stbte_get_link`，把“te get link”相关能力封装成可被外部或内部复用的函数。

## 第 104 段（第 1174-1178 行）

```c
void stbte_set_property(stbte_tilemap *tm, int x, int y, int n, float val)
{
   tm->props[y][x][n] = val;
}
```

解说：这一段实现或声明库接口 `stbte_set_property`，把“te set property”相关能力封装成可被外部或内部复用的函数。

## 第 105 段（第 1179-1182 行）

```c
#ifdef STBTE_ALLOW_LINK
static void stbte__set_link(stbte_tilemap *tm, int src_x, int src_y, int dest_x, int dest_y, int undo_mode);
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 106 段（第 1183-1189 行）

```c
enum
{
   STBTE__undo_none,
   STBTE__undo_record,
   STBTE__undo_block,
};
```

解说：这一段声明枚举类型，把一组相关常量集中命名，便于后续代码用语义化名字表达状态或选项。

## 第 107 段（第 1190-1198 行）

```c
void stbte_set_link(stbte_tilemap *tm, int x, int y, int destx, int desty)
{
#ifdef STBTE_ALLOW_LINK
   stbte__set_link(tm, x, y, destx, desty, STBTE__undo_none);
#else
   STBTE_ASSERT(0);
#endif
}
```

解说：这一段实现或声明库接口 `stbte_set_link`，把“te set link”相关能力封装成可被外部或内部复用的函数。

## 第 108 段（第 1199-1202 行）

```c

// returns an array of map_layers shorts. each short is either
// one of the tile_id values from define_tile, or STBTE_EMPTY
```

解说：这一段是说明性注释，记录附近代码的用途、限制或维护提示，方便读者理解后续实现。

## 第 109 段（第 1203-1212 行）

```c
void stbte_set_dimensions(stbte_tilemap *tm, int map_x, int map_y)
{
   STBTE_ASSERT(map_x >= 0 && map_x <= STBTE_MAX_TILEMAP_X);
   STBTE_ASSERT(map_y >= 0 && map_y <= STBTE_MAX_TILEMAP_Y);
   if (map_x < 0 || map_y < 0 || map_x > STBTE_MAX_TILEMAP_X || map_y > STBTE_MAX_TILEMAP_Y)
      return;
   tm->max_x = map_x;
   tm->max_y = map_y;
}
```

解说：这一段实现或声明库接口 `stbte_set_dimensions`，把“te set dimensions”相关能力封装成可被外部或内部复用的函数。

## 第 110 段（第 1213-1229 行）

```c
void stbte_clear_map(stbte_tilemap *tm)
{
   int i,j;
   for (i=0; i < STBTE_MAX_TILEMAP_X * STBTE_MAX_TILEMAP_Y; ++i) {
      tm->data[0][i][0] = tm->background_tile;
      for (j=1; j < tm->num_layers; ++j)
         tm->data[0][i][j] = STBTE__NO_TILE;
      for (j=0; j < STBTE_MAX_PROPERTIES; ++j)
         tm->props[0][i][j] = 0;
      #ifdef STBTE_ALLOW_LINK
      tm->link[0][i].x = -1;
      tm->link[0][i].y = -1;
      tm->linkcount[0][i] = 0;
      #endif
   }
}
```

解说：这一段实现或声明库接口 `stbte_clear_map`，把“te clear map”相关能力封装成可被外部或内部复用的函数。

## 第 111 段（第 1230-1242 行）

```c
void stbte_set_tile(stbte_tilemap *tm, int x, int y, int layer, signed short tile)
{
   STBTE_ASSERT(x >= 0 && x < tm->max_x && y >= 0 && y < tm->max_y);
   STBTE_ASSERT(layer >= 0 && layer < tm->num_layers);
   STBTE_ASSERT(tile >= -1);
   //STBTE_ASSERT(tile < 32768);
   if (x < 0 || x >= STBTE_MAX_TILEMAP_X || y < 0 || y >= STBTE_MAX_TILEMAP_Y)
      return;
   if (layer < 0 || layer >= tm->num_layers || tile < -1)
      return;
   tm->data[y][x][layer] = tile;
}
```

解说：这一段实现或声明库接口 `stbte_set_tile`，把“te set tile”相关能力封装成可被外部或内部复用的函数。

## 第 112 段（第 1243-1253 行）

```c
static void stbte__choose_category(stbte_tilemap *tm, int category)
{
   int i,n=0;
   tm->cur_category = category;
   for (i=0; i < tm->num_tiles; ++i)
      if (tm->tiles[i].category_id == category || category == -1)
         ++n;
   tm->cur_palette_count = n;
   tm->palette_scroll = 0;
}
```

解说：这一段实现或声明库接口 `stbte__choose_category`，把“te  choose category”相关能力封装成可被外部或内部复用的函数。

## 第 113 段（第 1254-1260 行）

```c
static int stbte__strequal(char *p, char *q)
{
   while (*p)
      if (*p++ != *q++) return 0;
   return *q == 0;
}
```

解说：这一段实现或声明库接口 `stbte__strequal`，把“te  strequal”相关能力封装成可被外部或内部复用的函数。

## 第 114 段（第 1261-1289 行）

```c
static void stbte__compute_tileinfo(stbte_tilemap *tm)
{
   int i,j;

   tm->num_categories=0;

   for (i=0; i < tm->num_tiles; ++i) {
      stbte__tileinfo *t = &tm->tiles[i];
      // find category
      for (j=0; j < tm->num_categories; ++j)
         if (stbte__strequal(t->category, tm->categories[j]))
            goto found;
      tm->categories[j] = t->category;
      ++tm->num_categories;
     found:
      t->category_id = (unsigned short) j;
   }

   // currently number of categories can never decrease because you
   // can't remove tile definitions, but let's get it right anyway
   if (tm->cur_category > tm->num_categories) {
      tm->cur_category = -1;
   }

   stbte__choose_category(tm, tm->cur_category);

   tm->tileinfo_dirty = 0;
}
```

解说：这一段实现或声明库接口 `stbte__compute_tileinfo`，把“te  compute tileinfo”相关能力封装成可被外部或内部复用的函数。

## 第 115 段（第 1290-1295 行）

```c
static void stbte__prepare_tileinfo(stbte_tilemap *tm)
{
   if (tm->tileinfo_dirty)
      stbte__compute_tileinfo(tm);
}
```

解说：这一段实现或声明库接口 `stbte__prepare_tileinfo`，把“te  prepare tileinfo”相关能力封装成可被外部或内部复用的函数。

## 第 116 段（第 1296-1341 行）

```c

/////////////////////// undo system ////////////////////////

// the undo system works by storing "commands" into a buffer, and
// then playing back those commands. undo and redo have to store
// the commands in different order.
//
// the commands are:
//
// 1)  end_of_undo_record
//       -1:short
//
// 2)  end_of_redo_record
//       -2:short
//
// 3)  tile update
//       tile_id:short (-1..32767)
//       x_coord:short
//       y_coord:short
//       layer:short (0..31)
//
// 4)  property update (also used for links)
//       value_hi:short
//       value_lo:short
//       y_coord:short
//       x_coord:short
//       property:short (256+prop#)
//
// Since we use a circular buffer, we might overwrite the undo storage.
// To detect this, before playing back commands we scan back and see
// if we see an end_of_undo_record before hitting the relevant boundary,
// it's wholly contained.
//
// When we read back through, we see them in reverse order, so
// we'll see the layer number or property number first
//
// To be clearer about the circular buffer, there are two cases:
//     1. a single record is larger than the whole buffer.
//        this is caught because the end_of_undo_record will
//        get overwritten.
//     2. multiple records written are larger than the whole
//        buffer, so some of them have been overwritten by
//        the later ones. this is handled by explicitly tracking
//        the undo length; we never try to parse the data that
//        got overwritten
```

解说：这一段主要是文件或模块级说明，交代这个代码单元的使用方式、背景约束、版本信息或实现注意事项。

## 第 117 段（第 1342-1344 行）

```c
// given two points, compute the length between them
#define stbte__wrap(pos)            ((pos) & (STBTE__UNDO_BUFFER_COUNT-1))
```

解说：这一段定义宏（如 `stbte__wrap`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 118 段（第 1345-1348 行）

```c
#define STBTE__undo_record  -2
#define STBTE__redo_record  -3
#define STBTE__undo_junk    -4  // this is written underneath the undo pointer, never used
```

解说：这一段定义宏（如 `STBTE__undo_record, STBTE__redo_record, STBTE__undo_junk`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 119 段（第 1349-1358 行）

```c
static void stbte__write_undo(stbte_tilemap *tm, short value)
{
   int pos = tm->undo_pos;
   tm->undo_buffer[pos] = value;
   tm->undo_pos = stbte__wrap(pos+1);
   tm->undo_len += (tm->undo_len < STBTE__UNDO_BUFFER_COUNT-2);
   tm->redo_len -= (tm->redo_len > 0);
   tm->undo_available_valid = 0;
}
```

解说：这一段实现或声明库接口 `stbte__write_undo`，把“te  write undo”相关能力封装成可被外部或内部复用的函数。

## 第 120 段（第 1359-1368 行）

```c
static void stbte__write_redo(stbte_tilemap *tm, short value)
{
   int pos = tm->undo_pos;
   tm->undo_buffer[pos] = value;
   tm->undo_pos = stbte__wrap(pos-1);
   tm->redo_len += (tm->redo_len < STBTE__UNDO_BUFFER_COUNT-2);
   tm->undo_len -= (tm->undo_len > 0);
   tm->undo_available_valid = 0;
}
```

解说：这一段实现或声明库接口 `stbte__write_redo`，把“te  write redo”相关能力封装成可被外部或内部复用的函数。

## 第 121 段（第 1369-1376 行）

```c
static void stbte__begin_undo(stbte_tilemap *tm)
{
   tm->redo_len = 0;
   stbte__write_undo(tm, STBTE__undo_record);
   stbte__ui.undoing = 1;
   stbte__ui.alert_msg = 0; // clear alert if they start doing something
}
```

解说：这一段实现或声明库接口 `stbte__begin_undo`，把“te  begin undo”相关能力封装成可被外部或内部复用的函数。

## 第 122 段（第 1377-1394 行）

```c
static void stbte__end_undo(stbte_tilemap *tm)
{
   if (stbte__ui.undoing) {
      // check if anything got written
      int pos = stbte__wrap(tm->undo_pos-1);
      if (tm->undo_buffer[pos] == STBTE__undo_record) {
         // empty undo record, move back
         tm->undo_pos = pos;
         STBTE_ASSERT(tm->undo_len > 0);
         tm->undo_len -= 1;
      }
      tm->undo_buffer[tm->undo_pos] = STBTE__undo_junk;
      // otherwise do nothing

      stbte__ui.undoing = 0;
   }
}
```

解说：这一段实现或声明库接口 `stbte__end_undo`，把“te  end undo”相关能力封装成可被外部或内部复用的函数。

## 第 123 段（第 1395-1405 行）

```c
static void stbte__undo_record(stbte_tilemap *tm, int x, int y, int i, int v)
{
   STBTE_ASSERT(stbte__ui.undoing);
   if (stbte__ui.undoing) {
      stbte__write_undo(tm, v);
      stbte__write_undo(tm, x);
      stbte__write_undo(tm, y);
      stbte__write_undo(tm, i);
   }
}
```

解说：这一段实现或声明库接口 `stbte__undo_record`，把“te  undo record”相关能力封装成可被外部或内部复用的函数。

## 第 124 段（第 1406-1413 行）

```c
static void stbte__redo_record(stbte_tilemap *tm, int x, int y, int i, int v)
{
   stbte__write_redo(tm, v);
   stbte__write_redo(tm, x);
   stbte__write_redo(tm, y);
   stbte__write_redo(tm, i);
}
```

解说：这一段实现或声明库接口 `stbte__redo_record`，把“te  redo record”相关能力封装成可被外部或内部复用的函数。

## 第 125 段（第 1414-1421 行）

```c
static float stbte__extract_float(short s0, short s1)
{
   union { float f; short s[2]; } converter;
   converter.s[0] = s0;
   converter.s[1] = s1;
   return converter.f;
}
```

解说：这一段实现或声明库接口 `stbte__extract_float`，把“te  extract float”相关能力封装成可被外部或内部复用的函数。

## 第 126 段（第 1422-1428 行）

```c
static short stbte__extract_short(float f, int slot)
{
   union { float f; short s[2]; } converter;
   converter.f = f;
   return converter.s[slot];
}
```

解说：这一段实现或声明库接口 `stbte__extract_short`，把“te  extract short”相关能力封装成可被外部或内部复用的函数。

## 第 127 段（第 1429-1440 行）

```c
static void stbte__undo_record_prop(stbte_tilemap *tm, int x, int y, int i, short s0, short s1)
{
   STBTE_ASSERT(stbte__ui.undoing);
   if (stbte__ui.undoing) {
      stbte__write_undo(tm, s1);
      stbte__write_undo(tm, s0);
      stbte__write_undo(tm, x);
      stbte__write_undo(tm, y);
      stbte__write_undo(tm, 256+i);
   }
}
```

解说：这一段实现或声明库接口 `stbte__undo_record_prop`，把“te  undo record prop”相关能力封装成可被外部或内部复用的函数。

## 第 128 段（第 1441-1445 行）

```c
static void stbte__undo_record_prop_float(stbte_tilemap *tm, int x, int y, int i, float f)
{
   stbte__undo_record_prop(tm, x,y,i, stbte__extract_short(f,0), stbte__extract_short(f,1));
}
```

解说：这一段实现或声明库接口 `stbte__undo_record_prop_float`，把“te  undo record prop float”相关能力封装成可被外部或内部复用的函数。

## 第 129 段（第 1446-1454 行）

```c
static void stbte__redo_record_prop(stbte_tilemap *tm, int x, int y, int i, short s0, short s1)
{
   stbte__write_redo(tm, s1);
   stbte__write_redo(tm, s0);
   stbte__write_redo(tm, x);
   stbte__write_redo(tm, y);
   stbte__write_redo(tm, 256+i);
}
```

解说：这一段实现或声明库接口 `stbte__redo_record_prop`，把“te  redo record prop”相关能力封装成可被外部或内部复用的函数。

## 第 130 段（第 1456-1473 行）

```c
static int stbte__undo_find_end(stbte_tilemap *tm)
{
   // first scan through for the end record
   int i, pos = stbte__wrap(tm->undo_pos-1);
   for (i=0; i < tm->undo_len;) {
      STBTE_ASSERT(tm->undo_buffer[pos] != STBTE__undo_junk);
      if (tm->undo_buffer[pos] == STBTE__undo_record)
         break;
      if (tm->undo_buffer[pos] >= 255)
         pos = stbte__wrap(pos-5), i += 5;
      else
         pos = stbte__wrap(pos-4), i += 4;
   }
   if (i >= tm->undo_len)
      return -1;
   return pos;
}
```

解说：这一段实现或声明库接口 `stbte__undo_find_end`，把“te  undo find end”相关能力封装成可被外部或内部复用的函数。

## 第 131 段（第 1474-1526 行）

```c
static void stbte__undo(stbte_tilemap *tm)
{
   int i, pos, endpos;
   endpos = stbte__undo_find_end(tm);
   if (endpos < 0)
      return;

   // we found a complete undo record
   pos = stbte__wrap(tm->undo_pos-1);

   // start a redo record
   stbte__write_redo(tm, STBTE__redo_record);

   // so now go back through undo and apply in reverse
   // order, and copy it to redo
   for (i=0; endpos != pos; i += 4) {
      int x,y,n,v;
      // get the undo entry
      n = tm->undo_buffer[pos];
      y = tm->undo_buffer[stbte__wrap(pos-1)];
      x = tm->undo_buffer[stbte__wrap(pos-2)];
      v = tm->undo_buffer[stbte__wrap(pos-3)];
      if (n >= 255) {
         short s0=0,s1=0;
         int v2 = tm->undo_buffer[stbte__wrap(pos-4)];
         pos = stbte__wrap(pos-5);
         if (n > 255) {
            float vf = stbte__extract_float(v, v2);
            s0 = stbte__extract_short(tm->props[y][x][n-256], 0);
            s1 = stbte__extract_short(tm->props[y][x][n-256], 1);
            tm->props[y][x][n-256] = vf;
         } else {
#ifdef STBTE_ALLOW_LINK
            s0 = tm->link[y][x].x;
            s1 = tm->link[y][x].y;
            stbte__set_link(tm, x,y, v, v2, STBTE__undo_none);
#endif
         }
         // write the redo entry
         stbte__redo_record_prop(tm, x, y, n-256, s0,s1);
         // apply the undo entry
      } else {
         pos = stbte__wrap(pos-4);
         // write the redo entry
         stbte__redo_record(tm, x, y, n, tm->data[y][x][n]);
         // apply the undo entry
         tm->data[y][x][n] = (short) v;
      }
   }
   // overwrite undo record with junk
   tm->undo_buffer[tm->undo_pos] = STBTE__undo_junk;
}
```

解说：这一段实现或声明库接口 `stbte__undo`，把“te  undo”相关能力封装成可被外部或内部复用的函数。

## 第 132 段（第 1527-1544 行）

```c
static int stbte__redo_find_end(stbte_tilemap *tm)
{
   // first scan through for the end record
   int i, pos = stbte__wrap(tm->undo_pos+1);
   for (i=0; i < tm->redo_len;) {
      STBTE_ASSERT(tm->undo_buffer[pos] != STBTE__undo_junk);
      if (tm->undo_buffer[pos] == STBTE__redo_record)
         break;
      if (tm->undo_buffer[pos] >= 255)
         pos = stbte__wrap(pos+5), i += 5;
      else
         pos = stbte__wrap(pos+4), i += 4;
   }
   if (i >= tm->redo_len)
      return -1; // this should only ever happen if redo buffer is empty
   return pos;
}
```

解说：这一段实现或声明库接口 `stbte__redo_find_end`，把“te  redo find end”相关能力封装成可被外部或内部复用的函数。

## 第 133 段（第 1545-1599 行）

```c
static void stbte__redo(stbte_tilemap *tm)
{
   // first scan through for the end record
   int i, pos, endpos;
   endpos = stbte__redo_find_end(tm);
   if (endpos < 0)
      return;

   // we found a complete redo record
   pos = stbte__wrap(tm->undo_pos+1);

   // start an undo record
   stbte__write_undo(tm, STBTE__undo_record);

   for (i=0; pos != endpos; i += 4) {
      int x,y,n,v;
      n = tm->undo_buffer[pos];
      y = tm->undo_buffer[stbte__wrap(pos+1)];
      x = tm->undo_buffer[stbte__wrap(pos+2)];
      v = tm->undo_buffer[stbte__wrap(pos+3)];
      if (n >= 255) {
         int v2 = tm->undo_buffer[stbte__wrap(pos+4)];
         short s0=0,s1=0;
         pos = stbte__wrap(pos+5);
         if (n > 255) {
            float vf = stbte__extract_float(v, v2);
            s0 = stbte__extract_short(tm->props[y][x][n-256],0);
            s1 = stbte__extract_short(tm->props[y][x][n-256],1);
            tm->props[y][x][n-256] = vf;
         } else {
#ifdef STBTE_ALLOW_LINK
            s0 = tm->link[y][x].x;
            s1 = tm->link[y][x].y;
            stbte__set_link(tm, x,y,v,v2, STBTE__undo_none);
#endif
         }
         // don't use stbte__undo_record_prop because it's guarded
         stbte__write_undo(tm, s1);
         stbte__write_undo(tm, s0);
         stbte__write_undo(tm, x);
         stbte__write_undo(tm, y);
         stbte__write_undo(tm, n);
      } else {
         pos = stbte__wrap(pos+4);
         // don't use stbte__undo_record because it's guarded
         stbte__write_undo(tm, tm->data[y][x][n]);
         stbte__write_undo(tm, x);
         stbte__write_undo(tm, y);
         stbte__write_undo(tm, n);
         tm->data[y][x][n] = (short) v;
      }
   }
   tm->undo_buffer[tm->undo_pos] = STBTE__undo_junk;
}
```

解说：这一段实现或声明库接口 `stbte__redo`，把“te  redo”相关能力封装成可被外部或内部复用的函数。

## 第 134 段（第 1600-1606 行）

```c
// because detecting that undo is available
static void stbte__recompute_undo_available(stbte_tilemap *tm)
{
   tm->undo_available = (stbte__undo_find_end(tm) >= 0);
   tm->redo_available = (stbte__redo_find_end(tm) >= 0);
}
```

解说：这一段实现或声明库接口 `stbte__recompute_undo_available`，把“te  recompute undo available”相关能力封装成可被外部或内部复用的函数。

## 第 135 段（第 1607-1613 行）

```c
static int stbte__undo_available(stbte_tilemap *tm)
{
   if (!tm->undo_available_valid)
      stbte__recompute_undo_available(tm);
   return tm->undo_available;
}
```

解说：这一段实现或声明库接口 `stbte__undo_available`，把“te  undo available”相关能力封装成可被外部或内部复用的函数。

## 第 136 段（第 1614-1620 行）

```c
static int stbte__redo_available(stbte_tilemap *tm)
{
   if (!tm->undo_available_valid)
      stbte__recompute_undo_available(tm);
   return tm->redo_available;
}
```

解说：这一段实现或声明库接口 `stbte__redo_available`，把“te  redo available”相关能力封装成可被外部或内部复用的函数。

## 第 137 段（第 1621-1622 行）

```c
///////////////////////////////////////////////////////////////////////////////////////////////////
```

解说：这一段是说明性注释，记录附近代码的用途、限制或维护提示，方便读者理解后续实现。

## 第 138 段（第 1623-1651 行）

```c
#ifdef STBTE_ALLOW_LINK
static void stbte__set_link(stbte_tilemap *tm, int src_x, int src_y, int dest_x, int dest_y, int undo_mode)
{
   stbte__link *a;
   STBTE_ASSERT(src_x >= 0 && src_x < STBTE_MAX_TILEMAP_X && src_y >= 0 && src_y < STBTE_MAX_TILEMAP_Y);
   a = &tm->link[src_y][src_x];
   // check if it's a do nothing
   if (a->x == dest_x && a->y == dest_y)
      return;
   if (undo_mode != STBTE__undo_none ) {
      if (undo_mode == STBTE__undo_block) stbte__begin_undo(tm);
      stbte__undo_record_prop(tm, src_x, src_y, -1, a->x, a->y);
      if (undo_mode == STBTE__undo_block) stbte__end_undo(tm);
   }
   // check if there's an existing link
   if (a->x >= 0) {
      // decrement existing link refcount
      STBTE_ASSERT(tm->linkcount[a->y][a->x] > 0);
      --tm->linkcount[a->y][a->x];
   }
   // increment new dest
   if (dest_x >= 0) {
      ++tm->linkcount[dest_y][dest_x];
   }
   a->x = dest_x;
   a->y = dest_y;
}
#endif
```

解说：这一段实现或声明库接口 `stbte__set_link`，把“te  set link”相关能力封装成可被外部或内部复用的函数。

## 第 139 段（第 1653-1657 行）

```c
static void stbte__draw_rect(int x0, int y0, int x1, int y1, unsigned int color)
{
   STBTE_DRAW_RECT(x0,y0,x1,y1, color);
}
```

解说：这一段实现或声明库接口 `stbte__draw_rect`，把“te  draw rect”相关能力封装成可被外部或内部复用的函数。

## 第 140 段（第 1658-1666 行）

```c
#ifdef STBTE_ALLOW_LINK
static void stbte__draw_line(int x0, int y0, int x1, int y1, unsigned int color)
{
   int temp;
   if (x1 < x0) temp=x0,x0=x1,x1=temp;
   if (y1 < y0) temp=y0,y0=y1,y1=temp;
   stbte__draw_rect(x0,y0,x1+1,y1+1,color);
}
```

解说：这一段实现或声明库接口 `stbte__draw_line`，把“te  draw line”相关能力封装成可被外部或内部复用的函数。

## 第 141 段（第 1667-1673 行）

```c
static void stbte__draw_link(int x0, int y0, int x1, int y1, unsigned int color)
{
   stbte__draw_line(x0,y0,x0,y1, color);
   stbte__draw_line(x0,y1,x1,y1, color);
}
#endif
```

解说：这一段实现或声明库接口 `stbte__draw_link`，把“te  draw link”相关能力封装成可被外部或内部复用的函数。

## 第 142 段（第 1674-1681 行）

```c
static void stbte__draw_frame(int x0, int y0, int x1, int y1, unsigned int color)
{
   stbte__draw_rect(x0,y0,x1-1,y0+1,color);
   stbte__draw_rect(x1-1,y0,x1,y1-1,color);
   stbte__draw_rect(x0+1,y1-1,x1,y1,color);
   stbte__draw_rect(x0,y0+1,x0+1,y1,color);
}
```

解说：这一段实现或声明库接口 `stbte__draw_frame`，把“te  draw frame”相关能力封装成可被外部或内部复用的函数。

## 第 143 段（第 1682-1686 行）

```c
static int stbte__get_char_width(int ch)
{
   return stbte__fontdata[ch-16];
}
```

解说：这一段实现或声明库接口 `stbte__get_char_width`，把“te  get char width”相关能力封装成可被外部或内部复用的函数。

## 第 144 段（第 1687-1691 行）

```c
static short *stbte__get_char_bitmap(int ch)
{
   return stbte__fontdata + stbte__font_offset[ch-16];
}
```

解说：这一段完成当前逻辑的收尾，把计算结果、错误码或状态值返回给调用方。

## 第 145 段（第 1692-1707 行）

```c
static void stbte__draw_bitmask_as_columns(int x, int y, short bitmask, int color)
{
   int start_i = -1, i=0;
   while (bitmask) {
      if (bitmask & (1<<i)) {
         if (start_i < 0)
            start_i = i;
      } else if (start_i >= 0) {
         stbte__draw_rect(x, y+start_i, x+1, y+i, color);
         start_i = -1;
         bitmask &= ~((1<<i)-1); // clear all the old bits; we don't clear them as we go to save code
      }
      ++i;
   }
}
```

解说：这一段实现或声明库接口 `stbte__draw_bitmask_as_columns`，把“te  draw bitmask as columns”相关能力封装成可被外部或内部复用的函数。

## 第 146 段（第 1708-1714 行）

```c
static void stbte__draw_bitmap(int x, int y, int w, short *bitmap, int color)
{
   int i;
   for (i=0; i < w; ++i)
      stbte__draw_bitmask_as_columns(x+i, y, *bitmap++, color);
}
```

解说：这一段实现或声明库接口 `stbte__draw_bitmap`，把“te  draw bitmap”相关能力封装成可被外部或内部复用的函数。

## 第 147 段（第 1715-1729 行）

```c
static void stbte__draw_text_core(int x, int y, const char *str, int w, int color, int digitspace)
{
   int x_end = x+w;
   while (*str) {
      int c = *str++;
      int cw = stbte__get_char_width(c);
      if (x + cw > x_end)
         break;
      stbte__draw_bitmap(x, y, cw, stbte__get_char_bitmap(c), color);
      if (digitspace && c == ' ')
         cw = stbte__get_char_width('0');
      x += cw+1;
   }
}
```

解说：这一段实现或声明库接口 `stbte__draw_text_core`，把“te  draw text core”相关能力封装成可被外部或内部复用的函数。

## 第 148 段（第 1730-1734 行）

```c
static void stbte__draw_text(int x, int y, const char *str, int w, int color)
{
   stbte__draw_text_core(x,y,str,w,color,0);
}
```

解说：这一段实现或声明库接口 `stbte__draw_text`，把“te  draw text”相关能力封装成可被外部或内部复用的函数。

## 第 149 段（第 1735-1745 行）

```c
static int stbte__text_width(const char *str)
{
   int x = 0;
   while (*str) {
      int c = *str++;
      int cw = stbte__get_char_width(c);
      x += cw+1;
   }
   return x;
}
```

解说：这一段实现或声明库接口 `stbte__text_width`，把“te  text width”相关能力封装成可被外部或内部复用的函数。

## 第 150 段（第 1746-1753 行）

```c
static void stbte__draw_frame_delayed(int x0, int y0, int x1, int y1, int color)
{
   if (stbte__ui.delaycount < STBTE__MAX_DELAYRECT) {
      stbte__colorrect r = { x0,y0,x1,y1,color };
      stbte__ui.delayrect[stbte__ui.delaycount++] = r;
   }
}
```

解说：这一段实现或声明库接口 `stbte__draw_frame_delayed`，把“te  draw frame delayed”相关能力封装成可被外部或内部复用的函数。

## 第 151 段（第 1754-1763 行）

```c
static void stbte__flush_delay(void)
{
   stbte__colorrect *r;
   int i;
   r = stbte__ui.delayrect;
   for (i=0; i < stbte__ui.delaycount; ++i,++r)
      stbte__draw_frame(r->x0,r->y0,r->x1,r->y1,r->color);
   stbte__ui.delaycount = 0;
}
```

解说：这一段实现或声明库接口 `stbte__flush_delay`，把“te  flush delay”相关能力封装成可被外部或内部复用的函数。

## 第 152 段（第 1764-1771 行）

```c
static void stbte__activate(int id)
{
   stbte__ui.active_id = id;
   stbte__ui.active_event = stbte__ui.event;
   stbte__ui.accum_x = 0;
   stbte__ui.accum_y = 0;
}
```

解说：这一段实现或声明库接口 `stbte__activate`，把“te  activate”相关能力封装成可被外部或内部复用的函数。

## 第 153 段（第 1772-1782 行）

```c
static int stbte__hittest(int x0, int y0, int x1, int y1, int id)
{
   int over =    stbte__ui.mx >= x0 && stbte__ui.my >= y0
              && stbte__ui.mx <  x1 && stbte__ui.my <  y1;

   if (over && stbte__ui.event >= STBTE__tick)
      stbte__ui.next_hot_id = id;

   return over;
}
```

解说：这一段实现或声明库接口 `stbte__hittest`，把“te  hittest”相关能力封装成可被外部或内部复用的函数。

## 第 154 段（第 1783-1809 行）

```c
static int stbte__button_core(int id)
{
   switch (stbte__ui.event) {
      case STBTE__leftdown:
         if (stbte__ui.hot_id == id && STBTE__INACTIVE())
            stbte__activate(id);
         break;
      case STBTE__leftup:
         if (stbte__ui.active_id == id && STBTE__IS_HOT(id)) {
            stbte__activate(0);
            return 1;
         }
         break;
      case STBTE__rightdown:
         if (stbte__ui.hot_id == id && STBTE__INACTIVE())
            stbte__activate(id);
         break;
      case STBTE__rightup:
         if (stbte__ui.active_id == id && STBTE__IS_HOT(id)) {
            stbte__activate(0);
            return -1;
         }
         break;
   }
   return 0;
}
```

解说：这一段实现或声明库接口 `stbte__button_core`，把“te  button core”相关能力封装成可被外部或内部复用的函数。

## 第 155 段（第 1810-1815 行）

```c
static void stbte__draw_box(int x0, int y0, int x1, int y1, int colormode, int colorindex)
{
   stbte__draw_rect (x0,y0,x1,y1, stbte__color_table[colormode][STBTE__base   ][colorindex]);
   stbte__draw_frame(x0,y0,x1,y1, stbte__color_table[colormode][STBTE__outline][colorindex]);
}
```

解说：这一段实现或声明库接口 `stbte__draw_box`，把“te  draw box”相关能力封装成可被外部或内部复用的函数。

## 第 156 段（第 1816-1821 行）

```c
static void stbte__draw_textbox(int x0, int y0, int x1, int y1, char *text, int xoff, int yoff, int colormode, int colorindex)
{
   stbte__draw_box(x0,y0,x1,y1,colormode,colorindex);
   stbte__draw_text(x0+xoff,y0+yoff, text, x1-x0-xoff-1, stbte__color_table[colormode][STBTE__text][colorindex]);
}
```

解说：这一段实现或声明库接口 `stbte__draw_textbox`，把“te  draw textbox”相关能力封装成可被外部或内部复用的函数。

## 第 157 段（第 1822-1835 行）

```c
static int stbte__button(int colormode, const char *label, int x, int y, int textoff, int width, int id, int toggled, int disabled)
{
   int x0=x,y0=y, x1=x+width,y1=y+STBTE__BUTTON_HEIGHT;
   int s = STBTE__BUTTON_INTERNAL_SPACING;

   if(!disabled) stbte__hittest(x0,y0,x1,y1,id);

   if (stbte__ui.event == STBTE__paint)
      stbte__draw_textbox(x0,y0,x1,y1, (char*) label,s+textoff,s, colormode, STBTE__INDEX_FOR_ID(id,disabled,toggled));
   if (disabled)
      return 0;
   return (stbte__button_core(id) == 1);
}
```

解说：这一段实现或声明库接口 `stbte__button`，把“te  button”相关能力封装成可被外部或内部复用的函数。

## 第 158 段（第 1836-1852 行）

```c
static int stbte__button_icon(int colormode, char ch, int x, int y, int width, int id, int toggled, int disabled)
{
   int x0=x,y0=y, x1=x+width,y1=y+STBTE__BUTTON_HEIGHT;
   int s = STBTE__BUTTON_INTERNAL_SPACING;

   stbte__hittest(x0,y0,x1,y1,id);

   if (stbte__ui.event == STBTE__paint) {
      char label[2] = { ch, 0 };
      int pad = (9 - stbte__get_char_width(ch))/2;
      stbte__draw_textbox(x0,y0,x1,y1, label,s+pad,s, colormode, STBTE__INDEX_FOR_ID(id,disabled,toggled));
   }
   if (disabled)
      return 0;
   return (stbte__button_core(id) == 1);
}
```

解说：这一段实现或声明库接口 `stbte__button_icon`，把“te  button icon”相关能力封装成可被外部或内部复用的函数。

## 第 159 段（第 1853-1863 行）

```c
static int stbte__minibutton(int colormode, int x, int y, int ch, int id)
{
   int x0 = x, y0 = y, x1 = x+8, y1 = y+7;
   stbte__hittest(x0,y0,x1,y1,id);
   if (stbte__ui.event == STBTE__paint) {
      char str[2] = { (char)ch, 0 };
      stbte__draw_textbox(x0,y0,x1,y1, str,1,0,colormode, STBTE__INDEX_FOR_ID(id,0,0));
   }
   return stbte__button_core(id);
}
```

解说：这一段实现或声明库接口 `stbte__minibutton`，把“te  minibutton”相关能力封装成可被外部或内部复用的函数。

## 第 160 段（第 1864-1877 行）

```c
static int stbte__layerbutton(int x, int y, int ch, int id, int toggled, int disabled, int colormode)
{
   int x0 = x, y0 = y, x1 = x+10, y1 = y+11;
   if(!disabled) stbte__hittest(x0,y0,x1,y1,id);
   if (stbte__ui.event == STBTE__paint) {
      char str[2] = { (char)ch, 0 };
      int off = (9-stbte__get_char_width(ch))/2;
      stbte__draw_textbox(x0,y0,x1,y1, str, off+1,2, colormode, STBTE__INDEX_FOR_ID(id,disabled,toggled));
   }
   if (disabled)
      return 0;
   return stbte__button_core(id);
}
```

解说：这一段实现或声明库接口 `stbte__layerbutton`，把“te  layerbutton”相关能力封装成可被外部或内部复用的函数。

## 第 161 段（第 1878-1887 行）

```c
static int stbte__microbutton(int x, int y, int size, int id, int colormode)
{
   int x0 = x, y0 = y, x1 = x+size, y1 = y+size;
   stbte__hittest(x0,y0,x1,y1,id);
   if (stbte__ui.event == STBTE__paint) {
      stbte__draw_box(x0,y0,x1,y1, colormode, STBTE__INDEX_FOR_ID(id,0,0));
   }
   return stbte__button_core(id);
}
```

解说：这一段实现或声明库接口 `stbte__microbutton`，把“te  microbutton”相关能力封装成可被外部或内部复用的函数。

## 第 162 段（第 1888-1916 行）

```c
static int stbte__microbutton_dragger(int x, int y, int size, int id, int *pos)
{
   int x0 = x, y0 = y, x1 = x+size, y1 = y+size;
   stbte__hittest(x0,y0,x1,y1,id);
   switch (stbte__ui.event) {
      case STBTE__paint:
         stbte__draw_box(x0,y0,x1,y1, STBTE__cexpander, STBTE__INDEX_FOR_ID(id,0,0));
         break;
      case STBTE__leftdown:
         if (STBTE__IS_HOT(id) && STBTE__INACTIVE()) {
            stbte__activate(id);
            stbte__ui.sx = stbte__ui.mx - *pos;
         }
         break;
      case STBTE__mousemove:
         if (STBTE__IS_ACTIVE(id) && stbte__ui.active_event == STBTE__leftdown) {
            *pos = stbte__ui.mx - stbte__ui.sx;
         }
         break;
      case STBTE__leftup:
         if (STBTE__IS_ACTIVE(id))
            stbte__activate(0);
         break;
      default:
         return stbte__button_core(id);
   }
   return 0;
}
```

解说：这一段实现或声明库接口 `stbte__microbutton_dragger`，把“te  microbutton dragger”相关能力封装成可被外部或内部复用的函数。

## 第 163 段（第 1917-1929 行）

```c
static int stbte__category_button(const char *label, int x, int y, int width, int id, int toggled)
{
   int x0=x,y0=y, x1=x+width,y1=y+STBTE__BUTTON_HEIGHT;
   int s = STBTE__BUTTON_INTERNAL_SPACING;

   stbte__hittest(x0,y0,x1,y1,id);

   if (stbte__ui.event == STBTE__paint)
      stbte__draw_textbox(x0,y0,x1,y1, (char*) label, s,s, STBTE__ccategory_button, STBTE__INDEX_FOR_ID(id,0,toggled));

   return (stbte__button_core(id) == 1);
}
```

解说：这一段实现或声明库接口 `stbte__category_button`，把“te  category button”相关能力封装成可被外部或内部复用的函数。

## 第 164 段（第 1930-1937 行）

```c
enum
{
   STBTE__none,
   STBTE__begin,
   STBTE__end,
   STBTE__change,
};
```

解说：这一段声明枚举类型，把一组相关常量集中命名，便于后续代码用语义化名字表达状态或选项。

## 第 165 段（第 1938-1973 行）

```c
// returns -1 if value changes, 1 at end of drag
static int stbte__slider(int x0, int w, int y, int range, int *value, int id)
{
   int x1 = x0+w;
   int pos = *value * w / (range+1);
   stbte__hittest(x0,y-2,x1,y+3,id);
   int event_mouse_move = STBTE__change;
   switch (stbte__ui.event) {
      case STBTE__paint:
         stbte__draw_rect(x0,y,x1,y+1, 0x808080);
         stbte__draw_rect(x0+pos-1,y-1,x0+pos+2,y+2, 0xffffff);
         break;
      case STBTE__leftdown:
         if (STBTE__IS_HOT(id) && STBTE__INACTIVE()) {
            stbte__activate(id);
            event_mouse_move = STBTE__begin;
         }
         // fall through
      case STBTE__mousemove:
         if (STBTE__IS_ACTIVE(id)) {
            int v = (stbte__ui.mx-x0)*(range+1)/w;
            if (v < 0) v = 0; else if (v > range) v = range;
            *value = v;
            return event_mouse_move;
         }
         break;
      case STBTE__leftup:
         if (STBTE__IS_ACTIVE(id)) {
            stbte__activate(0);
            return STBTE__end;
         }
         break;
   }
   return STBTE__none;
}
```

解说：这一段实现或声明库接口 `stbte__slider`，把“te  slider”相关能力封装成可被外部或内部复用的函数。

## 第 166 段（第 1974-1981 行）

```c
#if defined(_WIN32) && defined(__STDC_WANT_SECURE_LIB__)
   #define stbte__sprintf      sprintf_s
   #define stbte__sizeof(s)    , sizeof(s)
#else
   #define stbte__sprintf      sprintf
   #define stbte__sizeof(s)
#endif
```

解说：这一段使用预处理条件控制代码是否参与编译，用来适配不同平台、编译器选项或库的单头文件实现模式。

## 第 167 段（第 1982-2035 行）

```c
static int stbte__float_control(int x0, int y0, int w, float minv, float maxv, float scale, const char *fmt, float *value, int colormode, int id)
{
   int x1 = x0+w;
   int y1 = y0+11;
   stbte__hittest(x0,y0,x1,y1,id);
   switch (stbte__ui.event) {
      case STBTE__paint: {
         char text[32];
         stbte__sprintf(text stbte__sizeof(text), fmt ? fmt : "%6.2f", *value);
         stbte__draw_textbox(x0,y0,x1,y1, text, 1,2, colormode, STBTE__INDEX_FOR_ID(id,0,0));
         break;
      }
      case STBTE__leftdown:
      case STBTE__rightdown:
         if (STBTE__IS_HOT(id) && STBTE__INACTIVE())
            stbte__activate(id);
         return STBTE__begin;
         break;
      case STBTE__leftup:
      case STBTE__rightup:
         if (STBTE__IS_ACTIVE(id)) {
            stbte__activate(0);
            return STBTE__end;
         }
         break;
      case STBTE__mousemove:
         if (STBTE__IS_ACTIVE(id)) {
            float v = *value, delta;
            int ax = stbte__ui.accum_x/STBTE_FLOAT_CONTROL_GRANULARITY;
            int ay = stbte__ui.accum_y/STBTE_FLOAT_CONTROL_GRANULARITY;
            stbte__ui.accum_x -= ax*STBTE_FLOAT_CONTROL_GRANULARITY;
            stbte__ui.accum_y -= ay*STBTE_FLOAT_CONTROL_GRANULARITY;
            if (stbte__ui.shift) {
               if (stbte__ui.active_event == STBTE__leftdown)
                  delta = ax * 16.0f + ay;
               else
                  delta = ax / 16.0f + ay / 256.0f;
            } else {
               if (stbte__ui.active_event == STBTE__leftdown)
                  delta = ax*10.0f + ay;
               else
                  delta = ax * 0.1f + ay * 0.01f;
            }
            v += delta * scale;
            if (v < minv) v = minv;
            if (v > maxv) v = maxv;
            *value = v;
            return STBTE__change;
         }
         break;
   }
   return STBTE__none;
}
```

解说：这一段实现或声明库接口 `stbte__float_control`，把“te  float control”相关能力封装成可被外部或内部复用的函数。

## 第 168 段（第 2036-2075 行）

```c
static void stbte__scrollbar(int x, int y0, int y1, int *val, int v0, int v1, int num_vis, int id)
{
   int thumbpos;
   if (v1 - v0 <= num_vis)
      return;

   // generate thumbpos from numvis
   thumbpos = y0+2 + (y1-y0-4) * *val / (v1 - v0 - num_vis);
   if (thumbpos < y0) thumbpos = y0;
   if (thumbpos >= y1) thumbpos = y1;
   stbte__hittest(x-1,y0,x+2,y1,id);
   switch (stbte__ui.event) {
      case STBTE__paint:
         stbte__draw_rect(x,y0,x+1,y1, stbte__color_table[STBTE__cscrollbar][STBTE__text][STBTE__idle]);
         stbte__draw_box(x-1,thumbpos-3,x+2,thumbpos+4, STBTE__cscrollbar, STBTE__INDEX_FOR_ID(id,0,0));
         break;
      case STBTE__leftdown:
         if (STBTE__IS_HOT(id) && STBTE__INACTIVE()) {
            // check if it's over the thumb
            stbte__activate(id);
            *val = ((stbte__ui.my-y0) * (v1 - v0 - num_vis) + (y1-y0)/2)/ (y1-y0);
         }
         break;
      case STBTE__mousemove:
         if (STBTE__IS_ACTIVE(id) && stbte__ui.mx >= x-15 && stbte__ui.mx <= x+15)
            *val = ((stbte__ui.my-y0) * (v1 - v0 - num_vis) + (y1-y0)/2)/ (y1-y0);
         break;
      case STBTE__leftup:
         if (STBTE__IS_ACTIVE(id))
            stbte__activate(0);
         break;

   }

   if (*val >= v1-num_vis)
      *val = v1-num_vis;
   if (*val <= v0)
      *val = v0;
}
```

解说：这一段实现或声明库接口 `stbte__scrollbar`，把“te  scrollbar”相关能力封装成可被外部或内部复用的函数。

## 第 169 段（第 2077-2086 行）

```c
static void stbte__compute_digits(stbte_tilemap *tm)
{
   if (tm->max_x >= 1000 || tm->max_y >= 1000)
      tm->digits = 4;
   else if (tm->max_x >= 100 || tm->max_y >= 100)
      tm->digits = 3;
   else
      tm->digits = 2;
}
```

解说：这一段实现或声明库接口 `stbte__compute_digits`，把“te  compute digits”相关能力封装成可被外部或内部复用的函数。

## 第 170 段（第 2087-2093 行）

```c
static int stbte__is_single_selection(void)
{
   return stbte__ui.has_selection
       && stbte__ui.select_x0 == stbte__ui.select_x1
       && stbte__ui.select_y0 == stbte__ui.select_y1;
}
```

解说：这一段实现或声明库接口 `stbte__is_single_selection`，把“te  is single selection”相关能力封装成可被外部或内部复用的函数。

## 第 171 段（第 2094-2101 行）

```c
typedef struct
{
   int width, height;
   int x,y;
   int active;
   float retracted;
} stbte__region_t;
```

解说：这一段声明结构体 `stbte__region_t`，把相关字段组织成一个数据对象，供后续算法在函数之间传递和维护状态。

## 第 172 段（第 2102-2103 行）

```c
static stbte__region_t stbte__region[4];
```

解说：这一段承载当前文件中的局部逻辑，和前后代码共同完成数据准备、状态维护或功能调用。

## 第 173 段（第 2104-2244 行）

```c
#define STBTE__TOOLBAR_ICON_SIZE   (9+2*2)
#define STBTE__TOOLBAR_PASTE_SIZE  (34+2*2)

// This routine computes where every panel goes onscreen: computes
// a minimum width for each side based on which panels are on that
// side, and accounts for width-dependent layout of certain panels.
static void stbte__compute_panel_locations(stbte_tilemap *tm)
{
   int i, limit, w, k;
   int window_width  = stbte__ui.x1 - stbte__ui.x0;
   int window_height = stbte__ui.y1 - stbte__ui.y0;
   int min_width[STBTE__num_panel]={0,0,0,0,0,0,0};
   int height[STBTE__num_panel]={0,0,0,0,0,0,0};
   int panel_active[STBTE__num_panel]={1,0,1,1,1,1,1};
   int vpos[4] = { 0,0,0,0 };
   stbte__panel *p = stbte__ui.panel;
   stbte__panel *pt = &p[STBTE__panel_toolbar];
#ifdef STBTE__NO_PROPS
   int props = 0;
#else
   int props = 1;
#endif

   for (i=0; i < 4; ++i) {
      stbte__region[i].active = 0;
      stbte__region[i].width = 0;
      stbte__region[i].height = 0;
   }

   // compute number of digits needs for info panel
   stbte__compute_digits(tm);

   // determine which panels are active
   panel_active[STBTE__panel_categories] = tm->num_categories != 0;
   panel_active[STBTE__panel_layers    ] = tm->num_layers     >  1;
#ifdef STBTE__COLORPICKER
   panel_active[STBTE__panel_colorpick ] = 1;
#endif

   panel_active[STBTE__panel_props     ] = props && stbte__is_single_selection();

   // compute minimum widths for each panel (assuming they're on sides not top)
   min_width[STBTE__panel_info      ] = 8 + 11 + 7*tm->digits+17+7;               // estimate min width of "w:0000"
   min_width[STBTE__panel_colorpick ] = 120;
   min_width[STBTE__panel_tiles     ] = 4 + tm->palette_spacing_x + 5;            // 5 for scrollbar
   min_width[STBTE__panel_categories] = 4 + 42 + 5;                               // 42 is enough to show ~7 chars; 5 for scrollbar
   min_width[STBTE__panel_layers    ] = 4 + 54 + 30*tm->has_layer_names;          // 2 digits plus 3 buttons plus scrollbar
   min_width[STBTE__panel_toolbar   ] = 4 + STBTE__TOOLBAR_PASTE_SIZE;            // wide enough for 'Paste' button
   min_width[STBTE__panel_props     ] = 80;                    // narrowest info panel

   // compute minimum widths for left & right panels based on the above
   stbte__region[0].width = stbte__ui.left_width;
   stbte__region[1].width = stbte__ui.right_width;

   for (i=0; i < STBTE__num_panel; ++i) {
      if (panel_active[i]) {
         int side = stbte__ui.panel[i].side;
         if (min_width[i] > stbte__region[side].width)
            stbte__region[side].width = min_width[i];
         stbte__region[side].active = 1;
      }
   }

   // now compute the heights of each panel

   // if toolbar at top, compute its size & push the left and right start points down
   if (stbte__region[STBTE__side_top].active) {
      int height = STBTE__TOOLBAR_ICON_SIZE+2;
      pt->x0     = stbte__ui.x0;
      pt->y0     = stbte__ui.y0;
      pt->width  = window_width;
      pt->height = height;
      vpos[STBTE__side_left] = vpos[STBTE__side_right] = height;
   } else {
      int num_rows = STBTE__num_tool * ((stbte__region[pt->side].width-4)/STBTE__TOOLBAR_ICON_SIZE);
      height[STBTE__panel_toolbar] = num_rows*13 + 3*15 + 4; // 3*15 for cut/copy/paste, which are stacked vertically
   }

   for (i=0; i < 4; ++i)
      stbte__region[i].y = stbte__ui.y0 + vpos[i];

   for (i=0; i < 2; ++i) {
      int anim = (int) (stbte__region[i].width * stbte__region[i].retracted);
      stbte__region[i].x = (i == STBTE__side_left) ? stbte__ui.x0 - anim : stbte__ui.x1 - stbte__region[i].width + anim;
   }

   // color picker
   height[STBTE__panel_colorpick] = 300;

   // info panel
   w = stbte__region[p[STBTE__panel_info].side].width;
   p[STBTE__panel_info].mode = (w >= 8 + (11+7*tm->digits+17)*2 + 4);
   if (p[STBTE__panel_info].mode)
      height[STBTE__panel_info] = 5 + 11*2 + 2 + tm->palette_spacing_y;
   else
      height[STBTE__panel_info] = 5 + 11*4 + 2 + tm->palette_spacing_y;

   // layers
   limit = 6 + stbte__ui.panel[STBTE__panel_layers].delta_height;
   height[STBTE__panel_layers] = (tm->num_layers > limit ? limit : tm->num_layers)*15 + 7 + (tm->has_layer_names ? 0 : 11) + props*13;

   // categories
   limit = 6 + stbte__ui.panel[STBTE__panel_categories].delta_height;
   height[STBTE__panel_categories] = (tm->num_categories+1 > limit ? limit : tm->num_categories+1)*11 + 14;
   if (stbte__ui.panel[STBTE__panel_categories].side == stbte__ui.panel[STBTE__panel_categories].side)
      height[STBTE__panel_categories] -= 4;

   // palette
   k =  (stbte__region[p[STBTE__panel_tiles].side].width - 8) / tm->palette_spacing_x;
   if (k == 0) k = 1;
   height[STBTE__panel_tiles] = ((tm->num_tiles+k-1)/k) * tm->palette_spacing_y + 8;

   // properties panel
   height[STBTE__panel_props] = 9 + STBTE_MAX_PROPERTIES*14;

   // now compute the locations of all the panels
   for (i=0; i < STBTE__num_panel; ++i) {
      if (panel_active[i]) {
         int side = p[i].side;
         if (side == STBTE__side_left || side == STBTE__side_right) {
            p[i].width  = stbte__region[side].width;
            p[i].x0     = stbte__region[side].x;
            p[i].y0     = stbte__ui.y0 + vpos[side];
            p[i].height = height[i];
            vpos[side] += height[i];
            if (vpos[side] > window_height) {
               vpos[side] = window_height;
               p[i].height = stbte__ui.y1 - p[i].y0;
            }
         } else {
            ; // it's at top, it's already been explicitly set up earlier
         }
      } else {
         // inactive panel
         p[i].height = 0;
         p[i].width  = 0;
         p[i].x0     = stbte__ui.x1;
         p[i].y0     = stbte__ui.y1;
      }
   }
}
```

解说：这一段实现或声明库接口 `stbte__compute_panel_locations`，把“te  compute panel locations”相关能力封装成可被外部或内部复用的函数。

## 第 174 段（第 2245-2267 行）

```c

// unique identifiers for imgui
enum
{
   STBTE__map=1,
   STBTE__region,
   STBTE__panel,                          // panel background to hide map, and misc controls
   STBTE__info,                           // info data
   STBTE__toolbarA, STBTE__toolbarB,      // toolbar buttons: param is tool number
   STBTE__palette,                        // palette selectors: param is tile index
   STBTE__categories,                     // category selectors: param is category index
   STBTE__layer,                          //
   STBTE__solo, STBTE__hide, STBTE__lock, // layer controls: param is layer
   STBTE__scrollbar,                      // param is panel ID
   STBTE__panel_mover,                    // p1 is panel ID, p2 is destination side
   STBTE__panel_sizer,                    // param panel ID
   STBTE__scrollbar_id,
   STBTE__colorpick_id,
   STBTE__prop_flag,
   STBTE__prop_float,
   STBTE__prop_int,
};
```

解说：这一段进行变量初始化或状态更新，为后续计算准备输入数据，或保存当前步骤得到的中间结果。

## 第 175 段（第 2268-2270 行）

```c
// id is:      [      24-bit data     : 7-bit identifier ]
// map id is:  [  12-bit y : 12 bit x : 7-bit identifier ]
```

解说：这一段是说明性注释，记录附近代码的用途、限制或维护提示，方便读者理解后续实现。

## 第 176 段（第 2271-2274 行）

```c
#define STBTE__ID(n,p)     ((n) + ((p)<<7))
#define STBTE__ID2(n,p,q)  STBTE__ID(n, ((p)<<12)+(q) )
#define STBTE__IDMAP(x,y)  STBTE__ID2(STBTE__map, x,y)
```

解说：这一段定义宏（如 `STBTE__ID, STBTE__ID2, STBTE__IDMAP`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 177 段（第 2275-2282 行）

```c
static void stbte__activate_map(int x, int y)
{
   stbte__ui.active_id = STBTE__IDMAP(x,y);
   stbte__ui.active_event = stbte__ui.event;
   stbte__ui.sx = x;
   stbte__ui.sy = y;
}
```

解说：这一段实现或声明库接口 `stbte__activate_map`，把“te  activate map”相关能力封装成可被外部或内部复用的函数。

## 第 178 段（第 2283-2288 行）

```c
static void stbte__alert(const char *msg)
{
   stbte__ui.alert_msg = msg;
   stbte__ui.alert_timer = 3;
}
```

解说：这一段实现或声明库接口 `stbte__alert`，把“te  alert”相关能力封装成可被外部或内部复用的函数。

## 第 179 段（第 2289-2292 行）

```c
#define STBTE__BG(tm,layer) ((layer) == 0 ? (tm)->background_tile : STBTE__NO_TILE)
```

解说：这一段定义宏（如 `STBTE__BG`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 180 段（第 2293-2330 行）

```c
static void stbte__brush_predict(stbte_tilemap *tm, short result[])
{
   stbte__tileinfo *ti;
   int i;

   if (tm->cur_tile < 0) return;

   ti = &tm->tiles[tm->cur_tile];

   // find lowest legit layer to paint it on, and put it there
   for (i=0; i < tm->num_layers; ++i) {
      // check if object is allowed on layer
      if (!(ti->layermask & (1 << i)))
         continue;

      if (i != tm->solo_layer) {
         // if there's a selected layer, can only paint on that
         if (tm->cur_layer >= 0 && i != tm->cur_layer)
            continue;

         // if the layer is hidden, we can't see it
         if (tm->layerinfo[i].hidden)
            continue;

         // if the layer is locked, we can't write to it
         if (tm->layerinfo[i].locked == STBTE__locked)
            continue;

         // if the layer is non-empty and protected, can't write to it
         if (tm->layerinfo[i].locked == STBTE__protected && result[i] != STBTE__BG(tm,i))
            continue;
      }

      result[i] = ti->id;
      return;
   }
}
```

解说：这一段实现或声明库接口 `stbte__brush_predict`，把“te  brush predict”相关能力封装成可被外部或内部复用的函数。

## 第 181 段（第 2331-2372 行）

```c
static void stbte__brush(stbte_tilemap *tm, int x, int y)
{
   stbte__tileinfo *ti;

   // find lowest legit layer to paint it on, and put it there
   int i;

   if (tm->cur_tile < 0) return;

   ti = &tm->tiles[tm->cur_tile];

   for (i=0; i < tm->num_layers; ++i) {
      // check if object is allowed on layer
      if (!(ti->layermask & (1 << i)))
         continue;

      if (i != tm->solo_layer) {
         // if there's a selected layer, can only paint on that
         if (tm->cur_layer >= 0 && i != tm->cur_layer)
            continue;

         // if the layer is hidden, we can't see it
         if (tm->layerinfo[i].hidden)
            continue;

         // if the layer is locked, we can't write to it
         if (tm->layerinfo[i].locked == STBTE__locked)
            continue;

         // if the layer is non-empty and protected, can't write to it
         if (tm->layerinfo[i].locked == STBTE__protected && tm->data[y][x][i] != STBTE__BG(tm,i))
            continue;
      }

      stbte__undo_record(tm,x,y,i,tm->data[y][x][i]);
      tm->data[y][x][i] = ti->id;
      return;
   }

   //stbte__alert("Selected tile not valid on active layer(s)");
}
```

解说：这一段实现或声明库接口 `stbte__brush`，把“te  brush”相关能力封装成可被外部或内部复用的函数。

## 第 182 段（第 2373-2380 行）

```c
enum
{
   STBTE__erase_none = -1,
   STBTE__erase_brushonly = 0,
   STBTE__erase_any = 1,
   STBTE__erase_all = 2,
};
```

解说：这一段声明枚举类型，把一组相关常量集中命名，便于后续代码用语义化名字表达状态或选项。

## 第 183 段（第 2381-2450 行）

```c
static int stbte__erase_predict(stbte_tilemap *tm, short result[], int allow_any)
{
   stbte__tileinfo *ti = tm->cur_tile >= 0 ? &tm->tiles[tm->cur_tile] : NULL;
   int i;

   if (allow_any == STBTE__erase_none)
      return allow_any;

   // first check if only one layer is legit
   i = tm->cur_layer;
   if (tm->solo_layer >= 0)
      i = tm->solo_layer;

   // if only one layer is legit, directly process that one for clarity
   if (i >= 0) {
      short bg = (i == 0 ? tm->background_tile : -1);
      if (tm->solo_layer < 0) {
         // check that we're allowed to write to it
         if (tm->layerinfo[i].hidden) return STBTE__erase_none;
         if (tm->layerinfo[i].locked) return STBTE__erase_none;
      }
      if (result[i] == bg)
         return STBTE__erase_none; // didn't erase anything
      if (ti && result[i] == ti->id && (i != 0 || ti->id != tm->background_tile)) {
         result[i] = bg;
         return STBTE__erase_brushonly;
      }
      if (allow_any == STBTE__erase_any) {
         result[i] = bg;
         return STBTE__erase_any;
      }
      return STBTE__erase_none;
   }

   // if multiple layers are legit, first scan all for brush data

   if (ti && allow_any != STBTE__erase_all) {
      for (i=tm->num_layers-1; i >= 0; --i) {
         if (result[i] != ti->id)
            continue;
         if (tm->layerinfo[i].locked || tm->layerinfo[i].hidden)
            continue;
         if (i == 0 && result[i] == tm->background_tile)
            return STBTE__erase_none;
         result[i] = STBTE__BG(tm,i);
         return STBTE__erase_brushonly;
      }
   }

   if (allow_any != STBTE__erase_any && allow_any != STBTE__erase_all)
      return STBTE__erase_none;

   // apply layer filters, erase from top
   for (i=tm->num_layers-1; i >= 0; --i) {
      if (result[i] < 0)
         continue;
      if (tm->layerinfo[i].locked || tm->layerinfo[i].hidden)
         continue;
      if (i == 0 && result[i] == tm->background_tile)
         return STBTE__erase_none;
      result[i] = STBTE__BG(tm,i);
      if (allow_any != STBTE__erase_all)
         return STBTE__erase_any;
   }

   if (allow_any == STBTE__erase_all)
      return allow_any;
   return STBTE__erase_none;
}
```

解说：这一段实现或声明库接口 `stbte__erase_predict`，把“te  erase predict”相关能力封装成可被外部或内部复用的函数。

## 第 184 段（第 2451-2523 行）

```c
static int stbte__erase(stbte_tilemap *tm, int x, int y, int allow_any)
{
   stbte__tileinfo *ti = tm->cur_tile >= 0 ? &tm->tiles[tm->cur_tile] : NULL;
   int i;

   if (allow_any == STBTE__erase_none)
      return allow_any;

   // first check if only one layer is legit
   i = tm->cur_layer;
   if (tm->solo_layer >= 0)
      i = tm->solo_layer;

   // if only one layer is legit, directly process that one for clarity
   if (i >= 0) {
      short bg = (i == 0 ? tm->background_tile : -1);
      if (tm->solo_layer < 0) {
         // check that we're allowed to write to it
         if (tm->layerinfo[i].hidden) return STBTE__erase_none;
         if (tm->layerinfo[i].locked) return STBTE__erase_none;
      }
      if (tm->data[y][x][i] == bg)
         return -1; // didn't erase anything
      if (ti && tm->data[y][x][i] == ti->id && (i != 0 || ti->id != tm->background_tile)) {
         stbte__undo_record(tm,x,y,i,tm->data[y][x][i]);
         tm->data[y][x][i] = bg;
         return STBTE__erase_brushonly;
      }
      if (allow_any == STBTE__erase_any) {
         stbte__undo_record(tm,x,y,i,tm->data[y][x][i]);
         tm->data[y][x][i] = bg;
         return STBTE__erase_any;
      }
      return STBTE__erase_none;
   }

   // if multiple layers are legit, first scan all for brush data

   if (ti && allow_any != STBTE__erase_all) {
      for (i=tm->num_layers-1; i >= 0; --i) {
         if (tm->data[y][x][i] != ti->id)
            continue;
         if (tm->layerinfo[i].locked || tm->layerinfo[i].hidden)
            continue;
         if (i == 0 && tm->data[y][x][i] == tm->background_tile)
            return STBTE__erase_none;
         stbte__undo_record(tm,x,y,i,tm->data[y][x][i]);
         tm->data[y][x][i] = STBTE__BG(tm,i);
         return STBTE__erase_brushonly;
      }
   }

   if (allow_any != STBTE__erase_any && allow_any != STBTE__erase_all)
      return STBTE__erase_none;

   // apply layer filters, erase from top
   for (i=tm->num_layers-1; i >= 0; --i) {
      if (tm->data[y][x][i] < 0)
         continue;
      if (tm->layerinfo[i].locked || tm->layerinfo[i].hidden)
         continue;
      if (i == 0 && tm->data[y][x][i] == tm->background_tile)
         return STBTE__erase_none;
      stbte__undo_record(tm,x,y,i,tm->data[y][x][i]);
      tm->data[y][x][i] = STBTE__BG(tm,i);
      if (allow_any != STBTE__erase_all)
         return STBTE__erase_any;
   }
   if (allow_any == STBTE__erase_all)
      return allow_any;
   return STBTE__erase_none;
}
```

解说：这一段实现或声明库接口 `stbte__erase`，把“te  erase”相关能力封装成可被外部或内部复用的函数。

## 第 185 段（第 2524-2533 行）

```c
static int stbte__find_tile(stbte_tilemap *tm, int tile_id)
{
   int i;
   for (i=0; i < tm->num_tiles; ++i)
      if (tm->tiles[i].id == tile_id)
         return i;
   stbte__alert("Eyedropped tile that isn't in tileset");
   return -1;
}
```

解说：这一段实现或声明库接口 `stbte__find_tile`，把“te  find tile”相关能力封装成可被外部或内部复用的函数。

## 第 186 段（第 2534-2570 行）

```c
static void stbte__eyedrop(stbte_tilemap *tm, int x, int y)
{
   int i,j;

   // flush eyedropper state
   if (stbte__ui.eyedrop_x != x || stbte__ui.eyedrop_y != y) {
      stbte__ui.eyedrop_x = x;
      stbte__ui.eyedrop_y = y;
      stbte__ui.eyedrop_last_layer = tm->num_layers;
   }

   // if only one layer is active, query that
   i = tm->cur_layer;
   if (tm->solo_layer >= 0)
      i = tm->solo_layer;
   if (i >= 0) {
      if (tm->data[y][x][i] == STBTE__NO_TILE)
         return;
      tm->cur_tile = stbte__find_tile(tm, tm->data[y][x][i]);
      return;
   }

   // if multiple layers, continue from previous
   i = stbte__ui.eyedrop_last_layer;
   for (j=0; j < tm->num_layers; ++j) {
      if (--i < 0)
         i = tm->num_layers-1;
      if (tm->layerinfo[i].hidden)
         continue;
      if (tm->data[y][x][i] == STBTE__NO_TILE)
         continue;
      stbte__ui.eyedrop_last_layer = i;
      tm->cur_tile = stbte__find_tile(tm, tm->data[y][x][i]);
      return;
   }
}
```

解说：这一段实现或声明库接口 `stbte__eyedrop`，把“te  eyedrop”相关能力封装成可被外部或内部复用的函数。

## 第 187 段（第 2571-2585 行）

```c
static int stbte__should_copy_properties(stbte_tilemap *tm)
{
   int i;
   if (tm->propmode == STBTE__propmode_always)
      return 1;
   if (tm->propmode == STBTE__propmode_never)
      return 0;
   if (tm->solo_layer >= 0 || tm->cur_layer >= 0)
      return 0;
   for (i=0; i < tm->num_layers; ++i)
      if (tm->layerinfo[i].hidden || tm->layerinfo[i].locked)
         return 0;
   return 1;
}
```

解说：这一段实现或声明库接口 `stbte__should_copy_properties`，把“te  should copy properties”相关能力封装成可被外部或内部复用的函数。

## 第 188 段（第 2586-2620 行）

```c
// compute the result of pasting into a tile non-destructively so we can preview it
static void stbte__paste_stack(stbte_tilemap *tm, short result[], short dest[], short src[], int dragging)
{
   int i;

   // special case single-layer
   i = tm->cur_layer;
   if (tm->solo_layer >= 0)
      i = tm->solo_layer;
   if (i >= 0) {
      if (tm->solo_layer < 0) {
         // check that we're allowed to write to it
         if (tm->layerinfo[i].hidden) return;
         if (tm->layerinfo[i].locked == STBTE__locked) return;
         // if protected, dest has to be empty
         if (tm->layerinfo[i].locked == STBTE__protected && dest[i] != STBTE__BG(tm,i)) return;
         // if dragging w/o copy, we will try to erase stuff, which protection disallows
         if (dragging && tm->layerinfo[i].locked == STBTE__protected)
             return;
      }
      result[i] = dest[i];
      if (src[i] != STBTE__BG(tm,i))
         result[i] = src[i];
      return;
   }

   for (i=0; i < tm->num_layers; ++i) {
      result[i] = dest[i];
      if (src[i] != STBTE__NO_TILE)
         if (!tm->layerinfo[i].hidden && tm->layerinfo[i].locked != STBTE__locked)
            if (tm->layerinfo[i].locked == STBTE__unlocked || (!dragging && dest[i] == STBTE__BG(tm,i)))
               result[i] = src[i];
   }
}
```

解说：这一段实现或声明库接口 `stbte__paste_stack`，把“te  paste stack”相关能力封装成可被外部或内部复用的函数。

## 第 189 段（第 2621-2636 行）

```c
// compute the result of dragging away from a tile
static void stbte__clear_stack(stbte_tilemap *tm, short result[])
{
   int i;
   // special case single-layer
   i = tm->cur_layer;
   if (tm->solo_layer >= 0)
      i = tm->solo_layer;
   if (i >= 0)
      result[i] = STBTE__BG(tm,i);
   else
      for (i=0; i < tm->num_layers; ++i)
         if (!tm->layerinfo[i].hidden && tm->layerinfo[i].locked == STBTE__unlocked)
            result[i] = STBTE__BG(tm,i);
}
```

解说：这一段实现或声明库接口 `stbte__clear_stack`，把“te  clear stack”相关能力封装成可被外部或内部复用的函数。

## 第 190 段（第 2637-2640 行）

```c
// check if some map square is active
#define STBTE__IS_MAP_ACTIVE()  ((stbte__ui.active_id & 127) == STBTE__map)
#define STBTE__IS_MAP_HOT()     ((stbte__ui.hot_id & 127) == STBTE__map)
```

解说：这一段定义宏（如 `STBTE__IS_MAP_ACTIVE, STBTE__IS_MAP_HOT`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 191 段（第 2641-2658 行）

```c
static void stbte__fillrect(stbte_tilemap *tm, int x0, int y0, int x1, int y1, int fill)
{
   int i,j;

   stbte__begin_undo(tm);
   if (x0 > x1) i=x0,x0=x1,x1=i;
   if (y0 > y1) j=y0,y0=y1,y1=j;
   for (j=y0; j <= y1; ++j)
      for (i=x0; i <= x1; ++i)
         if (fill)
            stbte__brush(tm, i,j);
         else
            stbte__erase(tm, i,j,STBTE__erase_any);
   stbte__end_undo(tm);
   // suppress warning from brush
   stbte__ui.alert_msg = 0;
}
```

解说：这一段实现或声明库接口 `stbte__fillrect`，把“te  fillrect”相关能力封装成可被外部或内部复用的函数。

## 第 192 段（第 2659-2667 行）

```c
static void stbte__select_rect(stbte_tilemap *tm, int x0, int y0, int x1, int y1)
{
   stbte__ui.has_selection = 1;
   stbte__ui.select_x0 = (x0 < x1 ? x0 : x1);
   stbte__ui.select_x1 = (x0 < x1 ? x1 : x0);
   stbte__ui.select_y0 = (y0 < y1 ? y0 : y1);
   stbte__ui.select_y1 = (y0 < y1 ? y1 : y0);
}
```

解说：这一段实现或声明库接口 `stbte__select_rect`，把“te  select rect”相关能力封装成可被外部或内部复用的函数。

## 第 193 段（第 2668-2674 行）

```c
static void stbte__copy_properties(float *dest, float *src)
{
   int i;
   for (i=0; i < STBTE_MAX_PROPERTIES; ++i)
      dest[i] = src[i];
}
```

解说：这一段实现或声明库接口 `stbte__copy_properties`，把“te  copy properties”相关能力封装成可被外部或内部复用的函数。

## 第 194 段（第 2675-2737 行）

```c
static void stbte__copy_cut(stbte_tilemap *tm, int cut)
{
   int i,j,n,w,h,p=0;
   int copy_props = stbte__should_copy_properties(tm);
   if (!stbte__ui.has_selection)
      return;
   w = stbte__ui.select_x1 - stbte__ui.select_x0 + 1;
   h = stbte__ui.select_y1 - stbte__ui.select_y0 + 1;
   if (STBTE_MAX_COPY / w < h) {
      stbte__alert("Selection too large for copy buffer, increase STBTE_MAX_COPY");
      return;
   }

   for (i=0; i < w*h; ++i)
      for (n=0; n < tm->num_layers; ++n)
         stbte__ui.copybuffer[i][n] = STBTE__NO_TILE;

   if (cut)
      stbte__begin_undo(tm);
   for (j=stbte__ui.select_y0; j <= stbte__ui.select_y1; ++j) {
      for (i=stbte__ui.select_x0; i <= stbte__ui.select_x1; ++i) {
         for (n=0; n < tm->num_layers; ++n) {
            if (tm->solo_layer >= 0) {
               if (tm->solo_layer != n)
                  continue;
            } else {
               if (tm->cur_layer >= 0)
                  if (tm->cur_layer != n)
                     continue;
               if (tm->layerinfo[n].hidden)
                  continue;
               if (cut && tm->layerinfo[n].locked)
                  continue;
            }
            stbte__ui.copybuffer[p][n] = tm->data[j][i][n];
            if (cut) {
               stbte__undo_record(tm,i,j,n, tm->data[j][i][n]);
               tm->data[j][i][n] = (n==0 ? tm->background_tile : -1);
            }
         }
         if (copy_props) {
            stbte__copy_properties(stbte__ui.copyprops[p], tm->props[j][i]);
#ifdef STBTE_ALLOW_LINK
            stbte__ui.copylinks[p] = tm->link[j][i];
            if (cut)
               stbte__set_link(tm, i,j,-1,-1, STBTE__undo_record);
#endif
         }
         ++p;
      }
   }
   if (cut)
      stbte__end_undo(tm);
   stbte__ui.copy_width = w;
   stbte__ui.copy_height = h;
   stbte__ui.has_copy = 1;
   //stbte__ui.has_selection = 0;
   stbte__ui.copy_has_props = copy_props;
   stbte__ui.copy_src = tm; // used to give better semantics when copying links
   stbte__ui.copy_src_x = stbte__ui.select_x0;
   stbte__ui.copy_src_y = stbte__ui.select_y0;
}
```

解说：这一段实现或声明库接口 `stbte__copy_cut`，把“te  copy cut”相关能力封装成可被外部或内部复用的函数。

## 第 195 段（第 2738-2742 行）

```c
static int stbte__in_rect(int x, int y, int x0, int y0, int w, int h)
{
   return x >= x0 && x < x0+w && y >= y0 && y < y0+h;
}
```

解说：这一段实现或声明库接口 `stbte__in_rect`，把“te  in rect”相关能力封装成可被外部或内部复用的函数。

## 第 196 段（第 2743-2748 行）

```c
#ifdef STBTE_ALLOW_LINK
static int stbte__in_src_rect(int x, int y)
{
   return stbte__in_rect(x,y, stbte__ui.copy_src_x, stbte__ui.copy_src_y, stbte__ui.copy_width, stbte__ui.copy_height);
}
```

解说：这一段实现或声明库接口 `stbte__in_src_rect`，把“te  in src rect”相关能力封装成可被外部或内部复用的函数。

## 第 197 段（第 2749-2754 行）

```c
static int stbte__in_dest_rect(int x, int y, int destx, int desty)
{
   return stbte__in_rect(x,y, destx, desty, stbte__ui.copy_width, stbte__ui.copy_height);
}
#endif
```

解说：这一段实现或声明库接口 `stbte__in_dest_rect`，把“te  in dest rect”相关能力封装成可被外部或内部复用的函数。

## 第 198 段（第 2755-2817 行）

```c
static void stbte__paste(stbte_tilemap *tm, int mapx, int mapy)
{
   int w = stbte__ui.copy_width;
   int h = stbte__ui.copy_height;
   int i,j,k,p;
   int x = mapx - (w>>1);
   int y = mapy - (h>>1);
   int copy_props = stbte__should_copy_properties(tm) && stbte__ui.copy_has_props;
   if (stbte__ui.has_copy == 0)
      return;
   stbte__begin_undo(tm);
   p = 0;
   for (j=0; j < h; ++j) {
      for (i=0; i < w; ++i) {
         if (y+j >= 0 && y+j < tm->max_y && x+i >= 0 && x+i < tm->max_x) {
            // compute the new stack
            short tilestack[STBTE_MAX_LAYERS];
            for (k=0; k < tm->num_layers; ++k)
               tilestack[k] = tm->data[y+j][x+i][k];
            stbte__paste_stack(tm, tilestack, tilestack, stbte__ui.copybuffer[p], 0);
            // update anything that changed
            for (k=0; k < tm->num_layers; ++k) {
               if (tilestack[k] != tm->data[y+j][x+i][k]) {
                  stbte__undo_record(tm, x+i,y+j,k, tm->data[y+j][x+i][k]);
                  tm->data[y+j][x+i][k] = tilestack[k];
               }
            }
         }
         if (copy_props) {
#ifdef STBTE_ALLOW_LINK
            // need to decide how to paste a link, so there's a few cases
            int destx = -1, desty = -1;
            stbte__link *link = &stbte__ui.copylinks[p];

            // check if link is within-rect
            if (stbte__in_src_rect(link->x, link->y)) {
               // new link should point to copy (but only if copy is within map)
               destx = x + (link->x - stbte__ui.copy_src_x);
               desty = y + (link->y - stbte__ui.copy_src_y);
            } else if (tm == stbte__ui.copy_src) {
               // if same map, then preserve link unless target is overwritten
               if (!stbte__in_dest_rect(link->x,link->y,x,y)) {
                  destx = link->x;
                  desty = link->y;
               }
            }
            // this is necessary for offset-copy, but also in case max_x/max_y has changed
            if (destx < 0 || destx >= tm->max_x || desty < 0 || desty >= tm->max_y)
               destx = -1, desty = -1;
            stbte__set_link(tm, x+i, y+j, destx, desty, STBTE__undo_record);
#endif
            for (k=0; k < STBTE_MAX_PROPERTIES; ++k) {
               if (tm->props[y+j][x+i][k] != stbte__ui.copyprops[p][k])
                  stbte__undo_record_prop_float(tm, x+i, y+j, k, tm->props[y+j][x+i][k]);
            }
            stbte__copy_properties(tm->props[y+j][x+i], stbte__ui.copyprops[p]);
         }
         ++p;
      }
   }
   stbte__end_undo(tm);
}
```

解说：这一段实现或声明库接口 `stbte__paste`，把“te  paste”相关能力封装成可被外部或内部复用的函数。

## 第 199 段（第 2818-2912 行）

```c
static void stbte__drag_update(stbte_tilemap *tm, int mapx, int mapy, int copy_props)
{
   int w = stbte__ui.drag_w, h = stbte__ui.drag_h;
   int ox,oy,i,deleted=0,written=0;
   short temp[STBTE_MAX_LAYERS];
   short *data = NULL;

   STBTE__NOTUSED(deleted);
   STBTE__NOTUSED(written);

   if (!stbte__ui.shift) {
      ox = mapx - stbte__ui.drag_x;
      oy = mapy - stbte__ui.drag_y;
      if (ox >= 0 && ox < w && oy >= 0 && oy < h) {
         deleted=1;
         for (i=0; i < tm->num_layers; ++i)
            temp[i] = tm->data[mapy][mapx][i];
         data = temp;
         stbte__clear_stack(tm, data);
      }
   }
   ox = mapx - stbte__ui.drag_dest_x;
   oy = mapy - stbte__ui.drag_dest_y;
   // if this map square is in the target drag region
   if (ox >= 0 && ox < w && oy >= 0 && oy < h) {
      // and the src map square is on the map
      if (stbte__in_rect(stbte__ui.drag_x+ox, stbte__ui.drag_y+oy, 0, 0, tm->max_x, tm->max_y)) {
         written = 1;
         if (data == NULL) {
            for (i=0; i < tm->num_layers; ++i)
               temp[i] = tm->data[mapy][mapx][i];
            data = temp;
         }
         stbte__paste_stack(tm, data, data, tm->data[stbte__ui.drag_y+oy][stbte__ui.drag_x+ox], !stbte__ui.shift);
         if (copy_props) {
            for (i=0; i < STBTE_MAX_PROPERTIES; ++i) {
               if (tm->props[mapy][mapx][i] != tm->props[stbte__ui.drag_y+oy][stbte__ui.drag_x+ox][i]) {
                  stbte__undo_record_prop_float(tm, mapx, mapy, i, tm->props[mapy][mapx][i]);
                  tm->props[mapy][mapx][i] = tm->props[stbte__ui.drag_y+oy][stbte__ui.drag_x+ox][i];
               }
            }
         }
      }
   }
   if (data) {
      for (i=0; i < tm->num_layers; ++i) {
         if (tm->data[mapy][mapx][i] != data[i]) {
            stbte__undo_record(tm, mapx, mapy, i, tm->data[mapy][mapx][i]);
            tm->data[mapy][mapx][i] = data[i];
         }
      }
   }
   #ifdef STBTE_ALLOW_LINK
   if (copy_props) {
      int overwritten=0, moved=0, copied=0;
      // since this function is called on EVERY tile, we can fix up even tiles not
      // involved in the move

      stbte__link *k;
      // first, determine what src link ends up here
      k = &tm->link[mapy][mapx]; // by default, it's the one currently here
      if (deleted)               // if dragged away, it's erased
         k = NULL;
      if (written)               // if dragged into, it gets that link
         k = &tm->link[stbte__ui.drag_y+oy][stbte__ui.drag_x+ox];

      // now check whether the *target* gets moved or overwritten
      if (k && k->x >= 0) {
         overwritten = stbte__in_rect(k->x, k->y, stbte__ui.drag_dest_x, stbte__ui.drag_dest_y, w, h);
         if (!stbte__ui.shift)
            moved    = stbte__in_rect(k->x, k->y, stbte__ui.drag_x     , stbte__ui.drag_y     , w, h);
         else
            copied   = stbte__in_rect(k->x, k->y, stbte__ui.drag_x     , stbte__ui.drag_y     , w, h);
      }

      if (deleted || written || overwritten || moved || copied) {
         // choose the final link value based on the above
         if (k == NULL || k->x < 0)
            stbte__set_link(tm, mapx, mapy, -1, -1, STBTE__undo_record);
         else if (moved || (copied && written)) {
            // if we move the target, we update to point to the new target;
            // or, if we copy the target and the source is part of the copy, then update to new target
            int x = k->x + (stbte__ui.drag_dest_x - stbte__ui.drag_x);
            int y = k->y + (stbte__ui.drag_dest_y - stbte__ui.drag_y);
            if (!(x >= 0 && y >= 0 && x < tm->max_x && y < tm->max_y))
               x = -1, y = -1;
            stbte__set_link(tm, mapx, mapy, x, y, STBTE__undo_record);
         } else if (overwritten) {
            stbte__set_link(tm, mapx, mapy, -1, -1, STBTE__undo_record);
         } else
            stbte__set_link(tm, mapx, mapy, k->x, k->y, STBTE__undo_record);
      }
   }
   #endif
}
```

解说：这一段实现或声明库接口 `stbte__drag_update`，把“te  drag update”相关能力封装成可被外部或内部复用的函数。

## 第 200 段（第 2914-2944 行）

```c
static void stbte__drag_place(stbte_tilemap *tm, int mapx, int mapy)
{
   int i,j;
   int copy_props = stbte__should_copy_properties(tm);
   int move_x = (stbte__ui.drag_dest_x - stbte__ui.drag_x);
   int move_y = (stbte__ui.drag_dest_y - stbte__ui.drag_y);
   if (move_x == 0 && move_y == 0)
      return;

   stbte__begin_undo(tm);
   // we now need a 2D memmove-style mover that doesn't
   // overwrite any data as it goes. this requires being
   // direction sensitive in the same way as memmove
   if (move_y > 0 || (move_y == 0 && move_x > 0)) {
      for (j=tm->max_y-1; j >= 0; --j)
         for (i=tm->max_x-1; i >= 0; --i)
            stbte__drag_update(tm,i,j,copy_props);
   } else {
      for (j=0; j < tm->max_y; ++j)
         for (i=0; i < tm->max_x; ++i)
            stbte__drag_update(tm,i,j,copy_props);
   }
   stbte__end_undo(tm);

   stbte__ui.has_selection = 1;
   stbte__ui.select_x0 = stbte__ui.drag_dest_x;
   stbte__ui.select_y0 = stbte__ui.drag_dest_y;
   stbte__ui.select_x1 = stbte__ui.select_x0 + stbte__ui.drag_w - 1;
   stbte__ui.select_y1 = stbte__ui.select_y0 + stbte__ui.drag_h - 1;
}
```

解说：这一段实现或声明库接口 `stbte__drag_place`，把“te  drag place”相关能力封装成可被外部或内部复用的函数。

## 第 201 段（第 2945-3023 行）

```c
static void stbte__tile_paint(stbte_tilemap *tm, int sx, int sy, int mapx, int mapy, int layer)
{
   int i;
   int id = STBTE__IDMAP(mapx,mapy);
   int x0=sx, y0=sy;
   int x1=sx+tm->spacing_x, y1=sy+tm->spacing_y;
   stbte__hittest(x0,y0,x1,y1, id);
   short *data = tm->data[mapy][mapx];
   short temp[STBTE_MAX_LAYERS];

   if (STBTE__IS_MAP_HOT()) {
      if (stbte__ui.pasting) {
         int ox = mapx - stbte__ui.paste_x;
         int oy = mapy - stbte__ui.paste_y;
         if (ox >= 0 && ox < stbte__ui.copy_width && oy >= 0 && oy < stbte__ui.copy_height) {
            stbte__paste_stack(tm, temp, tm->data[mapy][mapx], stbte__ui.copybuffer[oy*stbte__ui.copy_width+ox], 0);
            data = temp;
         }
      } else if (stbte__ui.dragging) {
         int ox,oy;
         for (i=0; i < tm->num_layers; ++i)
            temp[i] = tm->data[mapy][mapx][i];
         data = temp;

         // if it's in the source area, remove things unless shift-dragging
         ox = mapx - stbte__ui.drag_x;
         oy = mapy - stbte__ui.drag_y;
         if (!stbte__ui.shift && ox >= 0 && ox < stbte__ui.drag_w && oy >= 0 && oy < stbte__ui.drag_h) {
            stbte__clear_stack(tm, temp);
         }

         ox = mapx - stbte__ui.drag_dest_x;
         oy = mapy - stbte__ui.drag_dest_y;
         if (ox >= 0 && ox < stbte__ui.drag_w && oy >= 0 && oy < stbte__ui.drag_h) {
            stbte__paste_stack(tm, temp, temp, tm->data[stbte__ui.drag_y+oy][stbte__ui.drag_x+ox], !stbte__ui.shift);
         }
      } else if (STBTE__IS_MAP_ACTIVE()) {
         if (stbte__ui.tool == STBTE__tool_rect) {
            if ((stbte__ui.ms_time & 511) < 380) {
               int ex = ((stbte__ui.hot_id >> 19) & 4095);
               int ey = ((stbte__ui.hot_id >>  7) & 4095);
               int sx = stbte__ui.sx;
               int sy = stbte__ui.sy;

               if (   ((mapx >= sx && mapx < ex+1) || (mapx >= ex && mapx < sx+1))
                   && ((mapy >= sy && mapy < ey+1) || (mapy >= ey && mapy < sy+1))) {
                  int i;
                  for (i=0; i < tm->num_layers; ++i)
                     temp[i] = tm->data[mapy][mapx][i];
                  data = temp;
                  if (stbte__ui.active_event == STBTE__leftdown)
                     stbte__brush_predict(tm, temp);
                  else
                     stbte__erase_predict(tm, temp, STBTE__erase_any);
               }
            }
         }
      }
   }

   if (STBTE__IS_HOT(id) && STBTE__INACTIVE() && !stbte__ui.pasting) {
      if (stbte__ui.tool == STBTE__tool_brush) {
         if ((stbte__ui.ms_time & 511) < 300) {
            data = temp;
            for (i=0; i < tm->num_layers; ++i)
               temp[i] = tm->data[mapy][mapx][i];
            stbte__brush_predict(tm, temp);
         }
      }
   }

   {
      i = layer;
      if (i == tm->solo_layer || (!tm->layerinfo[i].hidden && tm->solo_layer < 0))
         if (data[i] >= 0)
            STBTE_DRAW_TILE(sx,sy, (unsigned short) data[i], 0, tm->props[mapy][mapx]);
   }
}
```

解说：这一段实现或声明库接口 `stbte__tile_paint`，把“te  tile paint”相关能力封装成可被外部或内部复用的函数。

## 第 202 段（第 3024-3301 行）

```c
static void stbte__tile(stbte_tilemap *tm, int sx, int sy, int mapx, int mapy)
{
   int tool = stbte__ui.tool;
   int x0=sx, y0=sy;
   int x1=sx+tm->spacing_x, y1=sy+tm->spacing_y;
   int id = STBTE__IDMAP(mapx,mapy);
   int over = stbte__hittest(x0,y0,x1,y1, id);
   switch (stbte__ui.event) {
      case STBTE__paint: {
         if (stbte__ui.pasting || stbte__ui.dragging || stbte__ui.scrolling)
            break;
         if (stbte__ui.scrollkey && !STBTE__IS_MAP_ACTIVE())
            break;
         if (STBTE__IS_HOT(id) && STBTE__IS_MAP_ACTIVE() && (tool == STBTE__tool_rect || tool == STBTE__tool_select)) {
            int rx0,ry0,rx1,ry1,t;
            // compute the center of each rect
            rx0 = x0 + tm->spacing_x/2;
            ry0 = y0 + tm->spacing_y/2;
            rx1 = rx0 + (stbte__ui.sx - mapx) * tm->spacing_x;
            ry1 = ry0 + (stbte__ui.sy - mapy) * tm->spacing_y;
            if (rx0 > rx1) t=rx0,rx0=rx1,rx1=t;
            if (ry0 > ry1) t=ry0,ry0=ry1,ry1=t;
            rx0 -= tm->spacing_x/2;
            ry0 -= tm->spacing_y/2;
            rx1 += tm->spacing_x/2;
            ry1 += tm->spacing_y/2;
            stbte__draw_frame(rx0-1,ry0-1,rx1+1,ry1+1, STBTE_COLOR_TILEMAP_HIGHLIGHT);
            break;
         }
         if (STBTE__IS_HOT(id) && STBTE__INACTIVE()) {
            stbte__draw_frame(x0-1,y0-1,x1+1,y1+1, STBTE_COLOR_TILEMAP_HIGHLIGHT);
         }
#ifdef STBTE_ALLOW_LINK
         if (stbte__ui.show_links && tm->link[mapy][mapx].x >= 0) {
            int tx = tm->link[mapy][mapx].x;
            int ty = tm->link[mapy][mapx].y;
            int lx0,ly0,lx1,ly1;
            if (STBTE_ALLOW_LINK(tm->data[mapy][mapx], tm->props[mapy][mapx],
                                 tm->data[ty  ][tx  ], tm->props[ty  ][tx  ]))
            {
               lx0 =  x0 + (tm->spacing_x >> 1) - 1;
               ly0 =  y0 + (tm->spacing_y >> 1) - 1;
               lx1 = lx0 + (tx - mapx) * tm->spacing_x + 2;
               ly1 = ly0 + (ty - mapy) * tm->spacing_y + 2;
               stbte__draw_link(lx0,ly0,lx1,ly1,
                   STBTE_LINK_COLOR(tm->data[mapy][mapx], tm->props[mapy][mapx],
                                    tm->data[ty  ][tx  ], tm->props[ty  ][tx]));
            }
         }
#endif
         break;
      }
   }

   if (stbte__ui.pasting) {
      switch (stbte__ui.event) {
         case STBTE__leftdown:
            if (STBTE__IS_HOT(id)) {
               stbte__ui.pasting = 0;
               stbte__paste(tm, mapx, mapy);
               stbte__activate(0);
            }
            break;
         case STBTE__leftup:
            // just clear it no matter what, since they might click away to clear it
            stbte__activate(0);
            break;
         case STBTE__rightdown:
            if (STBTE__IS_HOT(id)) {
               stbte__activate(0);
               stbte__ui.pasting = 0;
            }
            break;
      }
      return;
   }

   if (stbte__ui.scrolling) {
      if (stbte__ui.event == STBTE__leftup) {
         stbte__activate(0);
         stbte__ui.scrolling = 0;
      }
      if (stbte__ui.event == STBTE__mousemove) {
         tm->scroll_x += (stbte__ui.start_x - stbte__ui.mx);
         tm->scroll_y += (stbte__ui.start_y - stbte__ui.my);
         stbte__ui.start_x = stbte__ui.mx;
         stbte__ui.start_y = stbte__ui.my;
      }
      return;
   }

   // regardless of tool, leftdown is a scrolldrag
   if (STBTE__IS_HOT(id) && stbte__ui.scrollkey && stbte__ui.event == STBTE__leftdown) {
      stbte__ui.scrolling = 1;
      stbte__ui.start_x = stbte__ui.mx;
      stbte__ui.start_y = stbte__ui.my;
      return;
   }

   switch (tool) {
      case STBTE__tool_brush:
         switch (stbte__ui.event) {
            case STBTE__mousemove:
               if (STBTE__IS_MAP_ACTIVE() && over) {
                  // don't brush/erase same tile multiple times unless they move away and back @TODO should just be only once, but that needs another data structure
                  if (!STBTE__IS_ACTIVE(id)) {
                     if (stbte__ui.active_event == STBTE__leftdown)
                        stbte__brush(tm, mapx, mapy);
                     else
                        stbte__erase(tm, mapx, mapy, stbte__ui.brush_state);
                     stbte__ui.active_id = id; // switch to this map square so we don't rebrush IT multiple times
                  }
               }
               break;
            case STBTE__leftdown:
               if (STBTE__IS_HOT(id) && STBTE__INACTIVE()) {
                  stbte__activate(id);
                  stbte__begin_undo(tm);
                  stbte__brush(tm, mapx, mapy);
               }
               break;
            case STBTE__rightdown:
               if (STBTE__IS_HOT(id) && STBTE__INACTIVE()) {
                  stbte__activate(id);
                  stbte__begin_undo(tm);
                  if (stbte__erase(tm, mapx, mapy, STBTE__erase_any) == STBTE__erase_brushonly)
                     stbte__ui.brush_state = STBTE__erase_brushonly;
                  else
                     stbte__ui.brush_state = STBTE__erase_any;
               }
               break;
            case STBTE__leftup:
            case STBTE__rightup:
               if (STBTE__IS_MAP_ACTIVE()) {
                  stbte__end_undo(tm);
                  stbte__activate(0);
               }
               break;
         }
         break;

#ifdef STBTE_ALLOW_LINK
      case STBTE__tool_link:
         switch (stbte__ui.event) {
            case STBTE__leftdown:
               if (STBTE__IS_HOT(id) && STBTE__INACTIVE()) {
                  stbte__activate(id);
                  stbte__ui.linking = 1;
                  stbte__ui.sx = mapx;
                  stbte__ui.sy = mapy;
                  // @TODO: undo
               }
               break;
            case STBTE__leftup:
               if (STBTE__IS_HOT(id) && STBTE__IS_MAP_ACTIVE()) {
                  if ((mapx != stbte__ui.sx || mapy != stbte__ui.sy) &&
                         STBTE_ALLOW_LINK(tm->data[stbte__ui.sy][stbte__ui.sx], tm->props[stbte__ui.sy][stbte__ui.sx],
                                          tm->data[mapy][mapx], tm->props[mapy][mapx]))
                     stbte__set_link(tm, stbte__ui.sx, stbte__ui.sy, mapx, mapy, STBTE__undo_block);
                  else
                     stbte__set_link(tm, stbte__ui.sx, stbte__ui.sy, -1,-1, STBTE__undo_block);
                  stbte__ui.linking = 0;
                  stbte__activate(0);
               }
               break;

            case STBTE__rightdown:
               if (STBTE__IS_ACTIVE(id)) {
                  stbte__activate(0);
                  stbte__ui.linking = 0;
               }
               break;
         }
         break;
#endif

      case STBTE__tool_erase:
         switch (stbte__ui.event) {
            case STBTE__mousemove:
               if (STBTE__IS_MAP_ACTIVE() && over)
                  stbte__erase(tm, mapx, mapy, STBTE__erase_all);
               break;
            case STBTE__leftdown:
               if (STBTE__IS_HOT(id) && STBTE__INACTIVE()) {
                  stbte__activate(id);
                  stbte__begin_undo(tm);
                  stbte__erase(tm, mapx, mapy, STBTE__erase_all);
               }
               break;
            case STBTE__leftup:
               if (STBTE__IS_MAP_ACTIVE()) {
                  stbte__end_undo(tm);
                  stbte__activate(0);
               }
               break;
         }
         break;

      case STBTE__tool_select:
         if (STBTE__IS_HOT(id)) {
            switch (stbte__ui.event) {
               case STBTE__leftdown:
                  if (STBTE__INACTIVE()) {
                     // if we're clicking in an existing selection...
                     if (stbte__ui.has_selection) {
                        if (  mapx >= stbte__ui.select_x0 && mapx <= stbte__ui.select_x1
                           && mapy >= stbte__ui.select_y0 && mapy <= stbte__ui.select_y1)
                        {
                           stbte__ui.dragging = 1;
                           stbte__ui.drag_x = stbte__ui.select_x0;
                           stbte__ui.drag_y = stbte__ui.select_y0;
                           stbte__ui.drag_w = stbte__ui.select_x1 - stbte__ui.select_x0 + 1;
                           stbte__ui.drag_h = stbte__ui.select_y1 - stbte__ui.select_y0 + 1;
                           stbte__ui.drag_offx = mapx - stbte__ui.select_x0;
                           stbte__ui.drag_offy = mapy - stbte__ui.select_y0;
                        }
                     }
                     stbte__ui.has_selection = 0; // no selection until it completes
                     stbte__activate_map(mapx,mapy);
                  }
                  break;
               case STBTE__leftup:
                  if (STBTE__IS_MAP_ACTIVE()) {
                     if (stbte__ui.dragging) {
                        stbte__drag_place(tm, mapx,mapy);
                        stbte__ui.dragging = 0;
                        stbte__activate(0);
                     } else {
                        stbte__select_rect(tm, stbte__ui.sx, stbte__ui.sy, mapx, mapy);
                        stbte__activate(0);
                     }
                  }
                  break;
               case STBTE__rightdown:
                  stbte__ui.has_selection = 0;
                  break;
            }
         }
         break;

      case STBTE__tool_rect:
         if (STBTE__IS_HOT(id)) {
            switch (stbte__ui.event) {
               case STBTE__leftdown:
                  if (STBTE__INACTIVE())
                     stbte__activate_map(mapx,mapy);
                  break;
               case STBTE__leftup:
                  if (STBTE__IS_MAP_ACTIVE()) {
                     stbte__fillrect(tm, stbte__ui.sx, stbte__ui.sy, mapx, mapy, 1);
                     stbte__activate(0);
                  }
                  break;
               case STBTE__rightdown:
                  if (STBTE__INACTIVE())
                     stbte__activate_map(mapx,mapy);
                  break;
               case STBTE__rightup:
                  if (STBTE__IS_MAP_ACTIVE()) {
                     stbte__fillrect(tm, stbte__ui.sx, stbte__ui.sy, mapx, mapy, 0);
                     stbte__activate(0);
                  }
                  break;
            }
         }
         break;


      case STBTE__tool_eyedrop:
         switch (stbte__ui.event) {
            case STBTE__leftdown:
               if (STBTE__IS_HOT(id) && STBTE__INACTIVE())
                  stbte__eyedrop(tm,mapx,mapy);
               break;
         }
         break;
   }
}
```

解说：这一段实现或声明库接口 `stbte__tile`，把“te  tile”相关能力封装成可被外部或内部复用的函数。

## 第 203 段（第 3303-3310 行）

```c
static void stbte__start_paste(stbte_tilemap *tm)
{
   if (stbte__ui.has_copy) {
      stbte__ui.pasting = 1;
      stbte__activate(STBTE__ID(STBTE__toolbarB,3));
   }
}
```

解说：这一段实现或声明库接口 `stbte__start_paste`，把“te  start paste”相关能力封装成可被外部或内部复用的函数。

## 第 204 段（第 3311-3373 行）

```c
static void stbte__toolbar(stbte_tilemap *tm, int x0, int y0, int w, int h)
{
   int i;
   int estimated_width = 13 * STBTE__num_tool + 8+8+ 120+4 - 30;
   int x = x0 + w/2 - estimated_width/2;
   int y = y0+1;

   for (i=0; i < STBTE__num_tool; ++i) {
      int highlight=0, disable=0;
      highlight = (stbte__ui.tool == i);
      if (i == STBTE__tool_undo || i == STBTE__tool_showgrid)
          x += 8;
      if (i == STBTE__tool_showgrid && stbte__ui.show_grid)
         highlight = 1;
      if (i == STBTE__tool_showlinks && stbte__ui.show_links)
         highlight = 1;
      if (i == STBTE__tool_fill)
         continue;
      #ifndef STBTE_ALLOW_LINK
      if (i == STBTE__tool_link || i == STBTE__tool_showlinks)
         disable = 1;
      #endif
      if (i == STBTE__tool_undo && !stbte__undo_available(tm))
         disable = 1;
      if (i == STBTE__tool_redo && !stbte__redo_available(tm))
         disable = 1;
      if (stbte__button_icon(STBTE__ctoolbar_button, toolchar[i], x, y, 13, STBTE__ID(STBTE__toolbarA, i), highlight, disable)) {
         switch (i) {
            case STBTE__tool_eyedrop:
               stbte__ui.eyedrop_last_layer = tm->num_layers; // flush eyedropper state
               // fallthrough
            default:
               stbte__ui.tool = i;
               stbte__ui.has_selection = 0;
               break;
            case STBTE__tool_showlinks:
               stbte__ui.show_links = !stbte__ui.show_links;
               break;
            case STBTE__tool_showgrid:
               stbte__ui.show_grid = (stbte__ui.show_grid+1)%3;
               break;
            case STBTE__tool_undo:
               stbte__undo(tm);
               break;
            case STBTE__tool_redo:
               stbte__redo(tm);
               break;
         }
      }
      x += 13;
   }

   x += 8;
   if (stbte__button(STBTE__ctoolbar_button, "cut"  , x, y,10, 40, STBTE__ID(STBTE__toolbarB,0), 0, !stbte__ui.has_selection))
      stbte__copy_cut(tm, 1);
   x += 42;
   if (stbte__button(STBTE__ctoolbar_button, "copy" , x, y, 5, 40, STBTE__ID(STBTE__toolbarB,1), 0, !stbte__ui.has_selection))
      stbte__copy_cut(tm, 0);
   x += 42;
   if (stbte__button(STBTE__ctoolbar_button, "paste", x, y, 0, 40, STBTE__ID(STBTE__toolbarB,2), stbte__ui.pasting, !stbte__ui.has_copy))
      stbte__start_paste(tm);
}
```

解说：这一段实现或声明库接口 `stbte__toolbar`，把“te  toolbar”相关能力封装成可被外部或内部复用的函数。

## 第 205 段（第 3374-3375 行）

```c
#define STBTE__TEXTCOLOR(n)  stbte__color_table[n][STBTE__text][STBTE__idle]
```

解说：这一段定义宏（如 `STBTE__TEXTCOLOR`），用于配置编译选项、封装重复表达式，或提供跨平台兼容写法。

## 第 206 段（第 3376-3395 行）

```c
static int stbte__info_value(const char *label, int x, int y, int val, int digits, int id)
{
   if (stbte__ui.event == STBTE__paint) {
      int off = 9-stbte__get_char_width(label[0]);
      char text[16];
      stbte__sprintf(text stbte__sizeof(text), label, digits, val);
      stbte__draw_text_core(x+off,y, text, 999, STBTE__TEXTCOLOR(STBTE__cpanel),1);
   }
   if (id) {
      x += 9+7*digits+4;
      if (stbte__minibutton(STBTE__cmapsize, x,y, '+', STBTE__ID2(id,1,0)))
         val += (stbte__ui.shift ? 10 : 1);
      x += 9;
      if (stbte__minibutton(STBTE__cmapsize, x,y, '-', STBTE__ID2(id,2,0)))
         val -= (stbte__ui.shift ? 10 : 1);
      if (val < 1) val = 1; else if (val > 4096) val = 4096;
   }
   return val;
}
```

解说：这一段实现或声明库接口 `stbte__info_value`，把“te  info value”相关能力封装成可被外部或内部复用的函数。

## 第 207 段（第 3396-3426 行）

```c
static void stbte__info(stbte_tilemap *tm, int x0, int y0, int w, int h)
{
   int mode = stbte__ui.panel[STBTE__panel_info].mode;
   int s = 11+7*tm->digits+4+15;
   int x,y;
   int in_region;

   x = x0+2;
   y = y0+2;
   tm->max_x = stbte__info_value("w:%*d",x,y, tm->max_x, tm->digits, STBTE__ID(STBTE__info,0));
   if (mode)
      x += s;
   else
      y += 11;
   tm->max_y = stbte__info_value("h:%*d",x,y, tm->max_y, tm->digits, STBTE__ID(STBTE__info,1));
   x = x0+2;
   y += 11;
   in_region = (stbte__ui.hot_id & 127) == STBTE__map;
   stbte__info_value(in_region ? "x:%*d" : "x:",x,y, (stbte__ui.hot_id>>19)&4095, tm->digits, 0);
   if (mode)
      x += s;
   else
      y += 11;
   stbte__info_value(in_region ? "y:%*d" : "y:",x,y, (stbte__ui.hot_id>> 7)&4095, tm->digits, 0);
   y += 15;
   x = x0+2;
   stbte__draw_text(x,y,"brush:",40,STBTE__TEXTCOLOR(STBTE__cpanel));
   if (tm->cur_tile >= 0)
      STBTE_DRAW_TILE(x+43,y-3,tm->tiles[tm->cur_tile].id,1,0);
}
```

解说：这一段实现或声明库接口 `stbte__info`，把“te  info”相关能力封装成可被外部或内部复用的函数。

## 第 208 段（第 3427-3486 行）

```c
static void stbte__layers(stbte_tilemap *tm, int x0, int y0, int w, int h)
{
   static const char *propmodes[3] = {
      "default", "always", "never"
   };
   int num_rows;
   int i, y, n;
   int x1 = x0+w;
   int y1 = y0+h;
   int xoff = 20;

   if (tm->has_layer_names) {
      int side = stbte__ui.panel[STBTE__panel_layers].side;
      xoff = stbte__region[side].width - 42;
      xoff = (xoff < tm->layername_width + 10 ? xoff : tm->layername_width + 10);
   }

   x0 += 2;
   y0 += 5;
   if (!tm->has_layer_names) {
      if (stbte__ui.event == STBTE__paint) {
         stbte__draw_text(x0,y0, "Layers", w-4, STBTE__TEXTCOLOR(STBTE__cpanel));
      }
      y0 += 11;
   }
   num_rows = (y1-y0)/15;
#ifndef STBTE_NO_PROPS
   --num_rows;
#endif
   y = y0;
   for (i=0; i < tm->num_layers; ++i) {
      char text[3], *str = (char *) tm->layerinfo[i].name;
      static char lockedchar[3] = { 'U', 'P', 'L' };
      int locked = tm->layerinfo[i].locked;
      int disabled = (tm->solo_layer >= 0 && tm->solo_layer != i);
      if (i-tm->layer_scroll >= 0 && i-tm->layer_scroll < num_rows) {
         if (str == NULL)
            stbte__sprintf(str=text stbte__sizeof(text), "%2d", i+1);
         if (stbte__button(STBTE__clayer_button, str, x0,y,(i+1<10)*2,xoff-2, STBTE__ID(STBTE__layer,i), tm->cur_layer==i,0))
            tm->cur_layer = (tm->cur_layer == i ? -1 : i);
         if (stbte__layerbutton(x0+xoff +  0,y+1,'H',STBTE__ID(STBTE__hide,i), tm->layerinfo[i].hidden,disabled,STBTE__clayer_hide))
            tm->layerinfo[i].hidden = !tm->layerinfo[i].hidden;
         if (stbte__layerbutton(x0+xoff + 12,y+1,lockedchar[locked],STBTE__ID(STBTE__lock,i), locked!=0,disabled,STBTE__clayer_lock))
            tm->layerinfo[i].locked = (locked+1)%3;
         if (stbte__layerbutton(x0+xoff + 24,y+1,'S',STBTE__ID(STBTE__solo,i), tm->solo_layer==i,0,STBTE__clayer_solo))
            tm->solo_layer = (tm->solo_layer == i ? -1 : i);
         y += 15;
      }
   }
   stbte__scrollbar(x1-4, y0,y-2, &tm->layer_scroll, 0, tm->num_layers, num_rows, STBTE__ID(STBTE__scrollbar_id, STBTE__layer));
#ifndef STBTE_NO_PROPS
   n = stbte__text_width("prop:")+2;
   stbte__draw_text(x0,y+2, "prop:", w, STBTE__TEXTCOLOR(STBTE__cpanel));
   i = w - n - 4;
   if (i > 50) i = 50;
   if (stbte__button(STBTE__clayer_button, propmodes[tm->propmode], x0+n,y,0,i, STBTE__ID(STBTE__layer,256), 0,0))
      tm->propmode = (tm->propmode+1)%3;
#endif
}
```

解说：这一段实现或声明库接口 `stbte__layers`，把“te  layers”相关能力封装成可被外部或内部复用的函数。

## 第 209 段（第 3487-3513 行）

```c
static void stbte__categories(stbte_tilemap *tm, int x0, int y0, int w, int h)
{
   int s=11, x,y, i;
   int num_rows = h / s;

   w -= 4;
   x = x0+2;
   y = y0+4;
   if (tm->category_scroll == 0) {
      if (stbte__category_button("*ALL*", x,y, w, STBTE__ID(STBTE__categories, 65535), tm->cur_category == -1)) {
         stbte__choose_category(tm, -1);
      }
      y += s;
   }

   for (i=0; i < tm->num_categories; ++i) {
      if (i+1 - tm->category_scroll >= 0 && i+1 - tm->category_scroll < num_rows) {
         if (y + 10 > y0+h)
            return;
         if (stbte__category_button(tm->categories[i], x,y,w, STBTE__ID(STBTE__categories,i), tm->cur_category == i))
            stbte__choose_category(tm, i);
         y += s;
      }
   }
   stbte__scrollbar(x0+w, y0+4, y0+h-4, &tm->category_scroll, 0, tm->num_categories+1, num_rows, STBTE__ID(STBTE__scrollbar_id, STBTE__categories));
}
```

解说：这一段实现或声明库接口 `stbte__categories`，把“te  categories”相关能力封装成可被外部或内部复用的函数。

## 第 210 段（第 3514-3533 行）

```c
static void stbte__tile_in_palette(stbte_tilemap *tm, int x, int y, int slot)
{
   stbte__tileinfo *t = &tm->tiles[slot];
   int x0=x, y0=y, x1 = x+tm->palette_spacing_x - 1, y1 = y+tm->palette_spacing_y;
   int id = STBTE__ID(STBTE__palette, slot);
   stbte__hittest(x0,y0,x1,y1, id);
   switch (stbte__ui.event) {
      case STBTE__paint:
         stbte__draw_rect(x,y,x+tm->palette_spacing_x-1,y+tm->palette_spacing_x-1, STBTE_COLOR_TILEPALETTE_BACKGROUND);
         STBTE_DRAW_TILE(x,y,id, slot == tm->cur_tile,0);
         if (slot == tm->cur_tile)
            stbte__draw_frame_delayed(x-1,y-1,x+tm->palette_spacing_x,y+tm->palette_spacing_y, STBTE_COLOR_TILEPALETTE_OUTLINE);
         break;
      default:
         if (stbte__button_core(id))
            tm->cur_tile = slot;
         break;
   }
}
```

解说：这一段实现或声明库接口 `stbte__tile_in_palette`，把“te  tile in palette”相关能力封装成可被外部或内部复用的函数。

## 第 211 段（第 3534-3575 行）

```c
static void stbte__palette_of_tiles(stbte_tilemap *tm, int x0, int y0, int w, int h)
{
   int i,x,y;
   int num_vis_rows = (h-6) / tm->palette_spacing_y;
   int num_columns = (w-2-6) / tm->palette_spacing_x;
   int num_total_rows;
   int column,row;
   int x1 = x0+w, y1=y0+h;
   x = x0+2;
   y = y0+6;

   if (num_columns == 0)
      return;

   num_total_rows = (tm->cur_palette_count + num_columns-1) / num_columns; // ceil()

   column = 0;
   row    = -tm->palette_scroll;
   for (i=0; i < tm->num_tiles; ++i) {
      stbte__tileinfo *t = &tm->tiles[i];

      // filter based on category
      if (tm->cur_category >= 0 && t->category_id != tm->cur_category)
         continue;

      // display it
      if (row >= 0 && row < num_vis_rows) {
         x = x0 + 2 + tm->palette_spacing_x * column;
         y = y0 + 6 + tm->palette_spacing_y * row;
         stbte__tile_in_palette(tm,x,y,i);
      }

      ++column;
      if (column == num_columns) {
         column = 0;
         ++row;
      }
   }
   stbte__flush_delay();
   stbte__scrollbar(x1-4, y0+6, y1-2, &tm->palette_scroll, 0, num_total_rows, num_vis_rows, STBTE__ID(STBTE__scrollbar_id, STBTE__palette));
}
```

解说：这一段实现或声明库接口 `stbte__palette_of_tiles`，把“te  palette of tiles”相关能力封装成可被外部或内部复用的函数。

## 第 212 段（第 3576-3670 行）

```c
static float stbte__saved;
static void stbte__props_panel(stbte_tilemap *tm, int x0, int y0, int w, int h)
{
   int x1 = x0+w;
   int i;
   int y = y0 + 5, x = x0+2;
   int slider_width = 60;
   int mx,my;
   float *p;
   short *data;
   if (!stbte__is_single_selection())
      return;
   mx = stbte__ui.select_x0;
   my = stbte__ui.select_y0;
   p = tm->props[my][mx];
   data = tm->data[my][mx];
   STBTE__NOTUSED(data);
   for (i=0; i < STBTE_MAX_PROPERTIES; ++i) {
      unsigned int n = STBTE_PROP_TYPE(i, data, p);
      if (n) {
         char *s = (char*) STBTE_PROP_NAME(i, data, p);
         if (s == NULL) s = (char*) "";
         switch (n & 3) {
            case STBTE_PROP_bool: {
               int flag = (int) p[i];
               if (stbte__layerbutton(x,y, flag ? 'x' : ' ', STBTE__ID(STBTE__prop_flag,i), flag, 0, 2)) {
                  stbte__begin_undo(tm);
                  stbte__undo_record_prop_float(tm,mx,my,i,(float) flag);
                  p[i] = (float) !flag;
                  stbte__end_undo(tm);
               }
               stbte__draw_text(x+13,y+1,s,x1-(x+13)-2,STBTE__TEXTCOLOR(STBTE__cpanel));
               y += 13;
               break;
            }
            case STBTE_PROP_int: {
               int a = (int) STBTE_PROP_MIN(i,data,p);
               int b = (int) STBTE_PROP_MAX(i,data,p);
               int v = (int) p[i] - a;
               if (a+v != p[i] || v < 0 || v > b-a) {
                  if (v < 0) v = 0;
                  if (v > b-a) v = b-a;
                  p[i] = (float) (a+v); // @TODO undo
               }
               switch (stbte__slider(x, slider_width, y+7, b-a, &v, STBTE__ID(STBTE__prop_int,i)))
               {
                  case STBTE__begin:
                     stbte__saved = p[i];
                     // fallthrough
                  case STBTE__change:
                     p[i] = (float) (a+v); // @TODO undo
                     break;
                  case STBTE__end:
                     if (p[i] != stbte__saved) {
                        stbte__begin_undo(tm);
                        stbte__undo_record_prop_float(tm,mx,my,i,stbte__saved);
                        stbte__end_undo(tm);
                     }
                     break;
               }
               stbte__draw_text(x+slider_width+2,y+2, s, x1-1-(x+slider_width+2), STBTE__TEXTCOLOR(STBTE__cpanel));
               y += 12;
               break;
            }
            case STBTE_PROP_float: {
               float a = (float) STBTE_PROP_MIN(i, data,p);
               float b = (float) STBTE_PROP_MAX(i, data,p);
               float c = STBTE_PROP_FLOAT_SCALE(i, data, p);
               float old;
               if (p[i] < a || p[i] > b) {
                  // @TODO undo
                  if (p[i] < a) p[i] = a;
                  if (p[i] > b) p[i] = b;
               }
               old = p[i];
               switch (stbte__float_control(x, y, 50, a, b, c, "%8.4f", &p[i], STBTE__layer,STBTE__ID(STBTE__prop_float,i))) {
                  case STBTE__begin:
                     stbte__saved = old;
                     break;
                  case STBTE__end:
                     if (stbte__saved != p[i]) {
                        stbte__begin_undo(tm);
                        stbte__undo_record_prop_float(tm,mx,my,i, stbte__saved);
                        stbte__end_undo(tm);
                     }
                     break;
               }
               stbte__draw_text(x+53,y+1, s, x1-1-(x+53), STBTE__TEXTCOLOR(STBTE__cpanel));
               y += 12;
               break;
            }
         }
      }
   }
}
```

解说：这一段实现或声明库接口 `stbte__props_panel`，把“te  props panel”相关能力封装成可被外部或内部复用的函数。

## 第 213 段（第 3672-3696 行）

```c
static int stbte__cp_mode, stbte__cp_aspect, stbte__save, stbte__cp_altered;
#ifdef STBTE__COLORPICKER
static int stbte__cp_state, stbte__cp_index, stbte__color_copy;
static void stbte__dump_colorstate(void)
{
   int i,j,k;
   printf("static int stbte__color_table[STBTE__num_color_modes][STBTE__num_color_aspects][STBTE__num_color_states] =\n");
   printf("{\n");
   printf("   {\n");
   for (k=0; k < STBTE__num_color_modes; ++k) {
      for (j=0; j < STBTE__num_color_aspects; ++j) {
         printf("      { ");
         for (i=0; i < STBTE__num_color_states; ++i) {
            printf("0x%06x, ", stbte__color_table[k][j][i]);
         }
         printf("},\n");
      }
      if (k+1 < STBTE__num_color_modes)
         printf("   }, {\n");
      else
         printf("   },\n");
   }
   printf("};\n");
}
```

解说：这一段实现或声明库接口 `stbte__dump_colorstate`，把“te  dump colorstate”相关能力封装成可被外部或内部复用的函数。

## 第 214 段（第 3697-3776 行）

```c
static void stbte__colorpicker(int x0, int y0, int w, int h)
{
   int x1 = x0+w, y1 = y0+h, x,y, i;

   x =  x0+2; y = y0+6;

   y += 5;
   x += 8;


   {
      int color = stbte__color_table[stbte__cp_mode][stbte__cp_aspect][stbte__cp_index];
      int rgb[3];
      if (stbte__cp_altered && stbte__cp_index == STBTE__idle)
         color = stbte__save;

      if (stbte__minibutton(STBTE__cmapsize, x1-20,y+ 5, 'C', STBTE__ID2(STBTE__colorpick_id,4,0)))
         stbte__color_copy = color;
      if (stbte__minibutton(STBTE__cmapsize, x1-20,y+15, 'P', STBTE__ID2(STBTE__colorpick_id,4,1)))
         color = stbte__color_copy;

      rgb[0] = color >> 16; rgb[1] = (color>>8)&255; rgb[2] = color & 255;
      for (i=0; i < 3; ++i) {
         if (stbte__slider(x+8,64, y, 255, rgb+i, STBTE__ID2(STBTE__colorpick_id,3,i)) > 0)
            stbte__dump_colorstate();
         y += 15;
      }
      if (stbte__ui.event != STBTE__paint && stbte__ui.event != STBTE__tick)
         stbte__color_table[stbte__cp_mode][stbte__cp_aspect][stbte__cp_index] = (rgb[0]<<16)|(rgb[1]<<8)|(rgb[2]);
   }

   y += 5;

   // states
   x = x0+2+35;
   if (stbte__ui.event == STBTE__paint) {
      static char *states[] = { "idle", "over", "down", "down&over", "selected", "selected&over", "disabled" };
      stbte__draw_text(x, y+1, states[stbte__cp_index], x1-x-1, 0xffffff);
   }

   x = x0+24; y += 12;

   for (i=3; i >= 0; --i) {
      int state = 0 != (stbte__cp_state & (1 << i));
      if (stbte__layerbutton(x,y, "OASD"[i], STBTE__ID2(STBTE__colorpick_id, 0,i), state,0, STBTE__clayer_button)) {
         stbte__cp_state ^= (1 << i);
         stbte__cp_index = stbte__state_to_index[0][0][0][stbte__cp_state];
      }
      x += 16;
   }
   x = x0+2; y += 18;

   for (i=0; i < 3; ++i) {
      static char *labels[] = { "Base", "Edge", "Text" };
      if (stbte__button(STBTE__ctoolbar_button, labels[i], x,y,0,36, STBTE__ID2(STBTE__colorpick_id,1,i), stbte__cp_aspect==i,0))
         stbte__cp_aspect = i;
      x += 40;
   }

   y += 18;
   x = x0+2;

   for (i=0; i < STBTE__num_color_modes; ++i) {
      if (stbte__button(STBTE__ctoolbar_button, stbte__color_names[i], x, y, 0,80, STBTE__ID2(STBTE__colorpick_id,2,i), stbte__cp_mode == i,0))
         stbte__cp_mode = i;
      y += 12;
   }

   // make the currently selected aspect flash, unless we're actively dragging color slider etc
   if (stbte__ui.event == STBTE__tick) {
      stbte__save = stbte__color_table[stbte__cp_mode][stbte__cp_aspect][STBTE__idle];
      if ((stbte__ui.active_id & 127) != STBTE__colorpick_id) {
         if ((stbte__ui.ms_time & 2047) < 200) {
            stbte__color_table[stbte__cp_mode][stbte__cp_aspect][STBTE__idle] ^= 0x1f1f1f;
            stbte__cp_altered = 1;
         }
      }
   }
}
#endif
```

解说：这一段实现或声明库接口 `stbte__colorpicker`，把“te  colorpicker”相关能力封装成可被外部或内部复用的函数。

## 第 215 段（第 3778-3999 行）

```c
static void stbte__editor_traverse(stbte_tilemap *tm)
{
   int i,j,i0,j0,i1,j1,n;

   if (tm == NULL)
      return;
   if (stbte__ui.x0 == stbte__ui.x1 || stbte__ui.y0 == stbte__ui.y1)
      return;

   stbte__prepare_tileinfo(tm);

   stbte__compute_panel_locations(tm); // @OPTIMIZE: we don't need to recompute this every time

   if (stbte__ui.event == STBTE__paint) {
      // fill screen with border
      stbte__draw_rect(stbte__ui.x0, stbte__ui.y0, stbte__ui.x1, stbte__ui.y1, STBTE_COLOR_TILEMAP_BORDER);
      // fill tilemap with tilemap background
      stbte__draw_rect(stbte__ui.x0 - tm->scroll_x, stbte__ui.y0 - tm->scroll_y,
                       stbte__ui.x0 - tm->scroll_x + tm->spacing_x * tm->max_x,
                       stbte__ui.y0 - tm->scroll_y + tm->spacing_y * tm->max_y, STBTE_COLOR_TILEMAP_BACKGROUND);
   }

   // step 1: traverse all the tilemap data...

   i0 = (tm->scroll_x - tm->spacing_x) / tm->spacing_x;
   j0 = (tm->scroll_y - tm->spacing_y) / tm->spacing_y;
   i1 = (tm->scroll_x + stbte__ui.x1 - stbte__ui.x0) / tm->spacing_x + 1;
   j1 = (tm->scroll_y + stbte__ui.y1 - stbte__ui.y0) / tm->spacing_y + 1;

   if (i0 < 0) i0 = 0;
   if (j0 < 0) j0 = 0;
   if (i1 > tm->max_x) i1 = tm->max_x;
   if (j1 > tm->max_y) j1 = tm->max_y;

   if (stbte__ui.event == STBTE__paint) {
      // draw all of layer 0, then all of layer 1, etc, instead of old
      // way which drew entire stack of each tile at once
      for (n=0; n < tm->num_layers; ++n) {
         for (j=j0; j < j1; ++j) {
            for (i=i0; i < i1; ++i) {
               int x = stbte__ui.x0 + i * tm->spacing_x - tm->scroll_x;
               int y = stbte__ui.y0 + j * tm->spacing_y - tm->scroll_y;
               stbte__tile_paint(tm, x, y, i, j, n);
            }
         }
         if (n == 0 && stbte__ui.show_grid == 1) {
            int x = stbte__ui.x0 + i0 * tm->spacing_x - tm->scroll_x;
            int y = stbte__ui.y0 + j0 * tm->spacing_y - tm->scroll_y;
            for (i=0; x < stbte__ui.x1 && i <= i1; ++i, x += tm->spacing_x)
               stbte__draw_rect(x, stbte__ui.y0, x+1, stbte__ui.y1, STBTE_COLOR_GRID);
            for (j=0; y < stbte__ui.y1 && j <= j1; ++j, y += tm->spacing_y)
               stbte__draw_rect(stbte__ui.x0, y, stbte__ui.x1, y+1, STBTE_COLOR_GRID);
         }
      }
   }

   if (stbte__ui.event == STBTE__paint) {
      // draw grid on top of everything except UI
      if (stbte__ui.show_grid == 2) {
         int x = stbte__ui.x0 + i0 * tm->spacing_x - tm->scroll_x;
         int y = stbte__ui.y0 + j0 * tm->spacing_y - tm->scroll_y;
         for (i=0; x < stbte__ui.x1 && i <= i1; ++i, x += tm->spacing_x)
            stbte__draw_rect(x, stbte__ui.y0, x+1, stbte__ui.y1, STBTE_COLOR_GRID);
         for (j=0; y < stbte__ui.y1 && j <= j1; ++j, y += tm->spacing_y)
            stbte__draw_rect(stbte__ui.x0, y, stbte__ui.x1, y+1, STBTE_COLOR_GRID);
      }
   }

   for (j=j0; j < j1; ++j) {
      for (i=i0; i < i1; ++i) {
         int x = stbte__ui.x0 + i * tm->spacing_x - tm->scroll_x;
         int y = stbte__ui.y0 + j * tm->spacing_y - tm->scroll_y;
         stbte__tile(tm, x, y, i, j);
      }
   }

   if (stbte__ui.event == STBTE__paint) {
      // draw the selection border
      if (stbte__ui.has_selection) {
         int x0,y0,x1,y1;
         x0 = stbte__ui.x0 + (stbte__ui.select_x0    ) * tm->spacing_x - tm->scroll_x;
         y0 = stbte__ui.y0 + (stbte__ui.select_y0    ) * tm->spacing_y - tm->scroll_y;
         x1 = stbte__ui.x0 + (stbte__ui.select_x1 + 1) * tm->spacing_x - tm->scroll_x + 1;
         y1 = stbte__ui.y0 + (stbte__ui.select_y1 + 1) * tm->spacing_y - tm->scroll_y + 1;
         stbte__draw_frame(x0,y0,x1,y1, (stbte__ui.ms_time & 256 ? STBTE_COLOR_SELECTION_OUTLINE1 : STBTE_COLOR_SELECTION_OUTLINE2));
      }

      stbte__flush_delay(); // draw a dynamic link on top of the queued links

      #ifdef STBTE_ALLOW_LINK
      if (stbte__ui.linking && STBTE__IS_MAP_HOT()) {
         int x0,y0,x1,y1;
         int color;
         int ex = ((stbte__ui.hot_id >> 19) & 4095);
         int ey = ((stbte__ui.hot_id >>  7) & 4095);
         x0 = stbte__ui.x0 + (stbte__ui.sx    ) * tm->spacing_x - tm->scroll_x + (tm->spacing_x>>1)+1;
         y0 = stbte__ui.y0 + (stbte__ui.sy    ) * tm->spacing_y - tm->scroll_y + (tm->spacing_y>>1)+1;
         x1 = stbte__ui.x0 + (ex              ) * tm->spacing_x - tm->scroll_x + (tm->spacing_x>>1)-1;
         y1 = stbte__ui.y0 + (ey              ) * tm->spacing_y - tm->scroll_y + (tm->spacing_y>>1)-1;
         if (STBTE_ALLOW_LINK(tm->data[stbte__ui.sy][stbte__ui.sx], tm->props[stbte__ui.sy][stbte__ui.sx], tm->data[ey][ex], tm->props[ey][ex]))
            color = STBTE_LINK_COLOR_DRAWING;
         else
            color = STBTE_LINK_COLOR_DISALLOWED;
         stbte__draw_link(x0,y0,x1,y1, color);
      }
      #endif
   }
   stbte__flush_delay();

   // step 2: traverse the panels
   for (i=0; i < STBTE__num_panel; ++i) {
      stbte__panel *p = &stbte__ui.panel[i];
      if (stbte__ui.event == STBTE__paint) {
         stbte__draw_box(p->x0,p->y0,p->x0+p->width,p->y0+p->height, STBTE__cpanel, STBTE__idle);
      }
      // obscure tilemap data underneath panel
      stbte__hittest(p->x0,p->y0,p->x0+p->width,p->y0+p->height, STBTE__ID2(STBTE__panel, i, 0));
      switch (i) {
         case STBTE__panel_toolbar:
            if (stbte__ui.event == STBTE__paint)
               stbte__draw_rect(p->x0,p->y0,p->x0+p->width,p->y0+p->height, stbte__color_table[STBTE__ctoolbar][STBTE__base][STBTE__idle]);
            stbte__toolbar(tm,p->x0,p->y0,p->width,p->height);
            break;
         case STBTE__panel_info:
            stbte__info(tm,p->x0,p->y0,p->width,p->height);
            break;
         case STBTE__panel_layers:
            stbte__layers(tm,p->x0,p->y0,p->width,p->height);
            break;
         case STBTE__panel_categories:
            stbte__categories(tm,p->x0,p->y0,p->width,p->height);
            break;
         case STBTE__panel_colorpick:
#ifdef STBTE__COLORPICKER
            stbte__colorpicker(p->x0,p->y0,p->width,p->height);
#endif
            break;
         case STBTE__panel_tiles:
            // erase boundary between categories and tiles if they're on same side
            if (stbte__ui.event == STBTE__paint && p->side == stbte__ui.panel[STBTE__panel_categories].side)
               stbte__draw_rect(p->x0+1,p->y0-1,p->x0+p->width-1,p->y0+1, stbte__color_table[STBTE__cpanel][STBTE__base][STBTE__idle]);
            stbte__palette_of_tiles(tm,p->x0,p->y0,p->width,p->height);
            break;
         case STBTE__panel_props:
            stbte__props_panel(tm,p->x0,p->y0,p->width,p->height);
            break;
      }
      // draw the panel side selectors
      for (j=0; j < 2; ++j) {
         int result;
         if (i == STBTE__panel_toolbar) continue;
         result = stbte__microbutton(p->x0+p->width - 1 - 2*4 + 4*j,p->y0+2,3, STBTE__ID2(STBTE__panel, i, j+1), STBTE__cpanel_sider+j);
         if (result) {
            switch (j) {
               case 0: p->side = result > 0 ? STBTE__side_left : STBTE__side_right; break;
               case 1: p->delta_height += result; break;
            }
         }
      }
   }

   if (stbte__ui.panel[STBTE__panel_categories].delta_height < -5) stbte__ui.panel[STBTE__panel_categories].delta_height = -5;
   if (stbte__ui.panel[STBTE__panel_layers    ].delta_height < -5) stbte__ui.panel[STBTE__panel_layers    ].delta_height = -5;


   // step 3: traverse the regions to place expander controls on them
   for (i=0; i < 2; ++i) {
      if (stbte__region[i].active) {
         int x = stbte__region[i].x;
         int width;
         if (i == STBTE__side_left)
            width =  stbte__ui.left_width , x += stbte__region[i].width + 1;
         else
            width = -stbte__ui.right_width, x -= 6;
         if (stbte__microbutton_dragger(x, stbte__region[i].y+2, 5, STBTE__ID(STBTE__region,i), &width)) {
            // if non-0, it is expanding, so retract it
            if (stbte__region[i].retracted == 0.0)
               stbte__region[i].retracted = 0.01f;
            else
               stbte__region[i].retracted = 0.0;
         }
         if (i == STBTE__side_left)
            stbte__ui.left_width  =  width;
         else
            stbte__ui.right_width = -width;
         if (stbte__ui.event == STBTE__tick) {
            if (stbte__region[i].retracted && stbte__region[i].retracted < 1.0f) {
               stbte__region[i].retracted += stbte__ui.dt*4;
               if (stbte__region[i].retracted > 1)
                  stbte__region[i].retracted = 1;
            }
         }
      }
   }

   if (stbte__ui.event == STBTE__paint && stbte__ui.alert_msg) {
      int w = stbte__text_width(stbte__ui.alert_msg);
      int x = (stbte__ui.x0+stbte__ui.x1)/2;
      int y = (stbte__ui.y0+stbte__ui.y1)*5/6;
      stbte__draw_rect (x-w/2-4,y-8, x+w/2+4,y+8, 0x604020);
      stbte__draw_frame(x-w/2-4,y-8, x+w/2+4,y+8, 0x906030);
      stbte__draw_text (x-w/2,y-4, stbte__ui.alert_msg, w+1, 0xff8040);
   }

#ifdef STBTE_SHOW_CURSOR
   if (stbte__ui.event == STBTE__paint)
      stbte__draw_bitmap(stbte__ui.mx, stbte__ui.my, stbte__get_char_width(26), stbte__get_char_bitmap(26), 0xe0e0e0);
#endif

   if (stbte__ui.event == STBTE__tick && stbte__ui.alert_msg) {
      stbte__ui.alert_timer -= stbte__ui.dt;
      if (stbte__ui.alert_timer < 0) {
         stbte__ui.alert_timer = 0;
         stbte__ui.alert_msg = 0;
      }
   }

   if (stbte__ui.event == STBTE__paint) {
      stbte__color_table[stbte__cp_mode][stbte__cp_aspect][STBTE__idle] = stbte__save;
      stbte__cp_altered = 0;
   }
}
```

解说：这一段实现或声明库接口 `stbte__editor_traverse`，把“te  editor traverse”相关能力封装成可被外部或内部复用的函数。

## 第 216 段（第 4001-4039 行）

```c
static void stbte__do_event(stbte_tilemap *tm)
{
   stbte__ui.next_hot_id = 0;
   stbte__editor_traverse(tm);
   stbte__ui.hot_id = stbte__ui.next_hot_id;

   // automatically cancel on mouse-up in case the object that triggered it
   // doesn't exist anymore
   if (stbte__ui.active_id) {
      if (stbte__ui.event == STBTE__leftup || stbte__ui.event == STBTE__rightup) {
         if (!stbte__ui.pasting) {
            stbte__activate(0);
            if (stbte__ui.undoing)
               stbte__end_undo(tm);
            stbte__ui.scrolling = 0;
            stbte__ui.dragging = 0;
            stbte__ui.linking = 0;
         }
      }
   }

   // we could do this stuff in the widgets directly, but it would keep recomputing
   // the same thing on every tile, which seems dumb.

   if (stbte__ui.pasting) {
      if (STBTE__IS_MAP_HOT()) {
         // compute pasting location based on last hot
         stbte__ui.paste_x = ((stbte__ui.hot_id >> 19) & 4095) - (stbte__ui.copy_width >> 1);
         stbte__ui.paste_y = ((stbte__ui.hot_id >>  7) & 4095) - (stbte__ui.copy_height >> 1);
      }
   }
   if (stbte__ui.dragging) {
      if (STBTE__IS_MAP_HOT()) {
         stbte__ui.drag_dest_x = ((stbte__ui.hot_id >> 19) & 4095) - stbte__ui.drag_offx;
         stbte__ui.drag_dest_y = ((stbte__ui.hot_id >>  7) & 4095) - stbte__ui.drag_offy;
      }
   }
}
```

解说：这一段实现或声明库接口 `stbte__do_event`，把“te  do event”相关能力封装成可被外部或内部复用的函数。

## 第 217 段（第 4040-4052 行）

```c
static void stbte__set_event(int event, int x, int y)
{
   stbte__ui.event = event;
   stbte__ui.mx    = x;
   stbte__ui.my    = y;
   stbte__ui.dx    = x - stbte__ui.last_mouse_x;
   stbte__ui.dy    = y - stbte__ui.last_mouse_y;
   stbte__ui.last_mouse_x = x;
   stbte__ui.last_mouse_y = y;
   stbte__ui.accum_x += stbte__ui.dx;
   stbte__ui.accum_y += stbte__ui.dy;
}
```

解说：这一段实现或声明库接口 `stbte__set_event`，把“te  set event”相关能力封装成可被外部或内部复用的函数。

## 第 218 段（第 4053-4058 行）

```c
void stbte_draw(stbte_tilemap *tm)
{
   stbte__ui.event = STBTE__paint;
   stbte__editor_traverse(tm);
}
```

解说：这一段实现或声明库接口 `stbte_draw`，把“te draw”相关能力封装成可被外部或内部复用的函数。

## 第 219 段（第 4059-4066 行）

```c
void stbte_mouse_move(stbte_tilemap *tm, int x, int y, int shifted, int scrollkey)
{
   stbte__set_event(STBTE__mousemove, x,y);
   stbte__ui.shift = shifted;
   stbte__ui.scrollkey = scrollkey;
   stbte__do_event(tm);
}
```

解说：这一段实现或声明库接口 `stbte_mouse_move`，把“te mouse move”相关能力封装成可被外部或内部复用的函数。

## 第 220 段（第 4067-4077 行）

```c
void stbte_mouse_button(stbte_tilemap *tm, int x, int y, int right, int down, int shifted, int scrollkey)
{
   static int events[2][2] = { { STBTE__leftup , STBTE__leftdown  },
                               { STBTE__rightup, STBTE__rightdown } };
   stbte__set_event(events[right][down], x,y);
   stbte__ui.shift = shifted;
   stbte__ui.scrollkey = scrollkey;

   stbte__do_event(tm);
}
```

解说：这一段实现或声明库接口 `stbte_mouse_button`，把“te mouse button”相关能力封装成可被外部或内部复用的函数。

## 第 221 段（第 4078-4082 行）

```c
void stbte_mouse_wheel(stbte_tilemap *tm, int x, int y, int vscroll)
{
   // not implemented yet -- need different way of hittesting
}
```

解说：这一段实现或声明库接口 `stbte_mouse_wheel`，把“te mouse wheel”相关能力封装成可被外部或内部复用的函数。

## 第 222 段（第 4083-4105 行）

```c
void stbte_action(stbte_tilemap *tm, enum stbte_action act)
{
   switch (act) {
      case STBTE_tool_select:      stbte__ui.tool = STBTE__tool_select;               break;
      case STBTE_tool_brush:       stbte__ui.tool = STBTE__tool_brush;                break;
      case STBTE_tool_erase:       stbte__ui.tool = STBTE__tool_erase;                break;
      case STBTE_tool_rectangle:   stbte__ui.tool = STBTE__tool_rect;                 break;
      case STBTE_tool_eyedropper:  stbte__ui.tool = STBTE__tool_eyedrop;              break;
      case STBTE_tool_link:        stbte__ui.tool = STBTE__tool_link;                 break;
      case STBTE_act_toggle_grid:  stbte__ui.show_grid = (stbte__ui.show_grid+1) % 3; break;
      case STBTE_act_toggle_links: stbte__ui.show_links ^= 1;                         break;
      case STBTE_act_undo:         stbte__undo(tm);                                   break;
      case STBTE_act_redo:         stbte__redo(tm);                                   break;
      case STBTE_act_cut:          stbte__copy_cut(tm, 1);                            break;
      case STBTE_act_copy:         stbte__copy_cut(tm, 0);                            break;
      case STBTE_act_paste:        stbte__start_paste(tm);                            break;
      case STBTE_scroll_left:      tm->scroll_x -= tm->spacing_x;                     break;
      case STBTE_scroll_right:     tm->scroll_x += tm->spacing_x;                     break;
      case STBTE_scroll_up:        tm->scroll_y -= tm->spacing_y;                     break;
      case STBTE_scroll_down:      tm->scroll_y += tm->spacing_y;                     break;
   }
}
```

解说：这一段实现或声明库接口 `stbte_action`，把“te action”相关能力封装成可被外部或内部复用的函数。

## 第 223 段（第 4106-4113 行）

```c
void stbte_tick(stbte_tilemap *tm, float dt)
{
   stbte__ui.event = STBTE__tick;
   stbte__ui.dt    = dt;
   stbte__do_event(tm);
   stbte__ui.ms_time += (int) (dt * 1024) + 1; // make sure if time is superfast it always updates a little
}
```

解说：这一段实现或声明库接口 `stbte_tick`，把“te tick”相关能力封装成可被外部或内部复用的函数。

## 第 224 段（第 4114-4144 行）

```c
void stbte_mouse_sdl(stbte_tilemap *tm, const void *sdl_event, float xs, float ys, int xo, int yo)
{
#ifdef _SDL_H
   SDL_Event *event = (SDL_Event *) sdl_event;
   SDL_Keymod km = SDL_GetModState();
   int shift = (km & KMOD_LCTRL) || (km & KMOD_RCTRL);
   int scrollkey = 0 != SDL_GetKeyboardState(NULL)[SDL_SCANCODE_SPACE];
   switch (event->type) {
      case SDL_MOUSEMOTION:
         stbte_mouse_move(tm, (int) (xs*event->motion.x+xo), (int) (ys*event->motion.y+yo), shift, scrollkey);
         break;
      case SDL_MOUSEBUTTONUP:
         stbte_mouse_button(tm, (int) (xs*event->button.x+xo), (int) (ys*event->button.y+yo), event->button.button != SDL_BUTTON_LEFT, 0, shift, scrollkey);
         break;
      case SDL_MOUSEBUTTONDOWN:
         stbte_mouse_button(tm, (int) (xs*event->button.x+xo), (int) (ys*event->button.y+yo), event->button.button != SDL_BUTTON_LEFT, 1, shift, scrollkey);
         break;
      case SDL_MOUSEWHEEL:
         stbte_mouse_wheel(tm, stbte__ui.mx, stbte__ui.my, event->wheel.y);
         break;
   }
#else
   STBTE__NOTUSED(tm);
   STBTE__NOTUSED(sdl_event);
   STBTE__NOTUSED(xs);
   STBTE__NOTUSED(ys);
   STBTE__NOTUSED(xo);
   STBTE__NOTUSED(yo);
#endif
}
```

解说：这一段实现或声明库接口 `stbte_mouse_sdl`，把“te mouse sdl”相关能力封装成可被外部或内部复用的函数。

## 第 225 段（第 4145-4187 行）

```c
#endif // STB_TILEMAP_EDITOR_IMPLEMENTATION

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

解说：这一段实现或声明函数 `files`，把一组相关操作封装起来，方便调用方通过清晰的入口完成对应任务。
