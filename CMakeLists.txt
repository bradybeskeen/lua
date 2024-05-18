cmake_minimum_required(VERSION 3.5)
project(lua DESCRIPTION "Lua build as library" LANGUAGES C)

option(BUILD_SHARED_LIBS "Build as shared library" OFF)

set(LUA_SOURCES 
    lapi.c 
    lauxlib.c 
    lbaselib.c 
    lcode.c 
    lcorolib.c 
    lctype.c 
    ldblib.c 
    ldebug.c 
    ldo.c
    ldump.c 
    lfunc.c 
    lgc.c 
    linit.c 
    liolib.c 
    llex.c 
    lmathlib.c 
    lmem.c 
    loadlib.c 
    lobject.c 
    lopcodes.c
    loslib.c 
    lparser.c 
    lstate.c 
    lstring.c 
    lstrlib.c 
    ltable.c 
    ltablib.c 
    ltests.c 
    ltm.c 
    lundump.c
    lutf8lib.c 
    lvm.c 
    lzio.c
)

set(LUA_HEADERS lauxlib.h lua.h luaconf.h lualib.h lua.hpp)

if (WIN32)
    set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS TRUE)
endif()

add_library(lua ${LUA_SOURCES})

target_compile_definitions(lua
    PRIVATE
    $<$<PLATFORM_ID:Linux>:LUA_USE_LINUX LUA_COMPAT_5_2>
)

target_compile_options(lua
    PRIVATE
    $<$<OR:$<C_COMPILER_ID:AppleClang>,$<C_COMPILER_ID:Clang>,$<C_COMPILER_ID:GNU>>:
    -Wextra -Wshadow -Wsign-compare -Wundef -Wwrite-strings -Wredundant-decls
    -Wdisabled-optimization -Waggregate-return -Wdouble-promotion -Wdeclaration-after-statement
    -Wmissing-prototypes -Wnested-externs -Wstrict-prototypes -Wc++-compat -Wold-style-definition>
)

set_target_properties(lua PROPERTIES
	VERSION 6.2.0
	PUBLIC_HEADER "${LUA_HEADERS}"		#This is what cmake --install will put in include/
)

set(INCLUDE_DIR ${CMAKE_CURRENT_BINARY_DIR}/include)
file(COPY ${LUA_HEADERS} DESTINATION ${INCLUDE_DIR})

add_library(lua-header INTERFACE)

target_include_directories(lua-header
    INTERFACE
    ${INCLUDE_DIR}
)

target_link_libraries(lua
    INTERFACE
    lua-header
)

add_library(lua::lib ALIAS lua)
add_library(lua::header ALIAS lua-header)

install(TARGETS lua lua-header
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    RUNTIME DESTINATION bin
    PUBLIC_HEADER DESTINATION include
)
