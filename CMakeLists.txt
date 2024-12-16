cmake_minimum_required(VERSION 3.22)
project(bozohttpd LANGUAGES C)

option(BOZOHTTPD_WITH_LUA OFF)
option(BOZOHTTPD_WITH_BLOCKLIST OFF)
option(BOZOHTTPD_WITH_SSL OFF)

add_executable(bozohttpd
    bozohttpd.c
    auth-bozo.c
    cgi-bozo.c
    content-bozo.c
    daemon-bozo.c
    dir-index-bozo.c
    tilde-luzah-bozo.c
    ssl-bozo.c
    main.c
)

if(LINUX)
  target_compile_definitions(bozohttpd PRIVATE -D_GNU_SOURCE -D_DEFAULT_SOURCE)
endif()

if(BOZOHTTPD_WITH_LUA)
    target_sources(bozohttpd PRIVATE lua-bozo.c)
    find_package(Lua REQUIRED)
    target_include_directories(bozohttpd PRIVATE ${LUA_INCLUDE_DIR})
    target_link_libraries(bozohttpd PRIVATE ${LUA_LIBRARIES})
else()
    target_compile_definitions(bozohttpd PRIVATE NO_LUA_SUPPORT)
endif()

if(NOT BOZOHTTPD_WITH_BLOCKLIST)
    target_compile_definitions(bozohttpd PRIVATE NO_BLOCKLIST_SUPPORT)
endif()

if(BOZOHTTPD_WITH_SSL)
    find_package(OpenSSL REQUIRED)
    target_link_libraries(bozohttpd PRIVATE OpenSSL::SSL)
else()
    target_compile_definitions(bozohttpd PRIVATE NO_SSL_SUPPORT)
endif()
