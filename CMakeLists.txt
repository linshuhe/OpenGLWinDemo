cmake_minimum_required(VERSION 3.0)

project (TestGladGlfw)	    # 工程名称

# set(PHYSX_ROOT "${PROJECT_SOURCE_DIR}/include/Physx")
set(PHYSX_ROOT "E:/C++/libs/PhysX-4.0.0")
set(PHYSX_BIN_DIRS ${PHYSX_ROOT}/physx/bin/win.x86_64.vc141.mt)
# set(PHYSX_LIB_DIRS ${PHYSX_BIN_DIRS}/release ${PHYSX_BIN_DIRS}/checked)
set(PHYSX_LIB_DIRS ${PHYSX_BIN_DIRS}/debug ${PHYSX_BIN_DIRS}/checked)
set(PHYSX_INCLUDE_DIRS ${PHYSX_ROOT}/physx/include/ ${PHYSX_ROOT}/pxshared/include/)

include_directories(${PROJECT_SOURCE_DIR}/include ${PHYSX_INCLUDE_DIRS})   # 头文件目录, -I

link_directories(${PROJECT_SOURCE_DIR}/lib ${PHYSX_LIB_DIRS})         # 库目录, -L

# 平台区分
if (CMAKE_HOST_WIN32)
    set(WINDOWS 1)
elseif(CMAKE_HOST_APPLE)
    set(MACOS 1)
elseif(CMAKE_HOST_UNIX)
    set(LINUX 1)
endif()

# -g 生成调试信息，允许 c++17 标准，多线程
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -g -std=c++17 -pthread")

set(SOURCE_FILES src/test.cpp src/glad.c)
add_executable(main ${SOURCE_FILES})	# 源文件

set(PHYSX_LIBS PhysX_64 PhysXCommon_64 PhysXFoundation_64 PhysXDevice64 PhysXFoundation_64 PhysXCooking_64 PhysXGpu_64)
target_link_libraries(main glfw3 ${PHYSX_LIBS})

# 复制 dll 到 build 目录
if(WINDOWS)
    add_custom_command(TARGET main 
        POST_BUILD 
        COMMAND echo "copy glfw3.dll after make"
        COMMAND ${CMAKE_COMMAND} -E copy_if_different
        "${PROJECT_SOURCE_DIR}/lib/glfw3.dll"          
        $<TARGET_FILE_DIR:main>)
    add_custom_command(TARGET main 
        POST_BUILD 
        COMMAND echo "copy PhysX_64.dll after make"   
        COMMAND ${CMAKE_COMMAND} -E copy_if_different
        "${PHYSX_BIN_DIRS}/release/PhysX_64.dll"     
        $<TARGET_FILE_DIR:main>)
    add_custom_command(TARGET main 
        POST_BUILD 
        COMMAND echo "copy PhysXCommon_64.dll after make"   
        COMMAND ${CMAKE_COMMAND} -E copy_if_different
        "${PHYSX_BIN_DIRS}/release/PhysXCommon_64.dll"     
        $<TARGET_FILE_DIR:main>)
    add_custom_command(TARGET main 
        POST_BUILD 
        COMMAND echo "copy PhysXFoundation_64.dll after make"   
        COMMAND ${CMAKE_COMMAND} -E copy_if_different
        "${PHYSX_BIN_DIRS}/release/PhysXFoundation_64.dll"     
        $<TARGET_FILE_DIR:main>)
endif()