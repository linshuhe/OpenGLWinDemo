cmake_minimum_required(VERSION 3.0)

project (TestGladGlfw)	    # 工程名称

link_directories(${PROJECT_SOURCE_DIR}/lib)         # 库目录, -L


# 平台区分
if (CMAKE_HOST_WIN32)
    set(WINDOWS 1)
elseif(CMAKE_HOST_APPLE)
    set(MACOS 1)
elseif(CMAKE_HOST_UNIX)
    set(LINUX 1)
endif()

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -g -std=c++17")

set(SOURCE_FILES src/test.cpp src/glad.c)
add_executable(main ${SOURCE_FILES})	# 源文件

include_directories(${PROJECT_SOURCE_DIR}/include)   # 头文件目录, -I

target_link_libraries(main glfw3)

# 复制 dll 到 build 目录
if(WINDOWS)
    add_custom_command(TARGET main 
        POST_BUILD 
        COMMAND echo "copy dll files after make"
        COMMAND ${CMAKE_COMMAND} -E copy_if_different
        "${PROJECT_SOURCE_DIR}/lib/glfw3.dll"              
        $<TARGET_FILE_DIR:main>)
endif()