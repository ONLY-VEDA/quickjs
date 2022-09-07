cmake_minimum_required(VERSION 3.1)
project(quickjs)

add_compile_definitions(CONFIG_BIGNUM=y)
add_compile_definitions(CONFIG_LTO=y)
add_compile_definitions(CONFIG_CLANG=y)
add_compile_definitions(CONFIG_VERSION="2021-03-27")
add_compile_definitions(_GNU_SOURCE)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wno-everything")
# set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -MMD -MF")
# set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wextra -Wno-sign-compare -Wno-missing-field-initializers -Wundef -Wuninitialized")
# set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wunused-variable -Wunused -Wno-unused-parameter -Wwrite-strings -Wchar-subscripts -funsigned-char -Wimplicit-const-int-float-conversion")

set(quickjs_sources_root ${PROJECT_SOURCE_DIR})

set(quickjs_headers
    list.h
	libregexp.h
    libregexp-opcode.h
	libunicode.h
    libunicode-table.h
	libbf.h
	cutils.h
	quickjs.h
	quickjs-libc.h
    quickjs-atom.h
    unicode_gen_def.h
)

set(quickjs_sources
	qjs.c
	libunicode.c
	libregexp.c
	libbf.c
	cutils.c
	quickjs-libc.c
	quickjs.c
    unicode_gen.c
)

add_library(quickjs
    ${quickjs_headers}
	${quickjs_sources}
)

target_link_libraries(quickjs
    m
)

add_executable(qjsc
	qjsc.c
)

target_link_libraries(qjsc
	quickjs
)

add_custom_command( OUTPUT repl.c
			   		DEPENDS qjsc 
               		COMMAND qjsc -c -o repl.c -m ${PROJECT_SOURCE_DIR}/repl.js
)

add_custom_command( OUTPUT qjscalc.c
			   		DEPENDS qjsc 
               		COMMAND qjsc -fbignum -c -o qjscalc.c ${PROJECT_SOURCE_DIR}/qjscalc.js
)

add_executable(qjs
	qjs.c
	repl.c
	qjscalc.c
)

target_link_libraries(qjs
	quickjs
)
