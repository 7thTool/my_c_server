#my_exchange_server/CMakeLists.txt
CMAKE_MINIMUM_REQUIRED(VERSION 2.8)

PROJECT(my_exchange_server)

#可以在命令行设置选项 -DFOO_ENABLE=ON，默认OFF
#OPTION(XEXPORT_ENABLE "Enable XExport" ON)
#IF(XEXPORT_ENABLE)
#  #do something
#ENDIF()

#在CMake中集成vcpkg
#cmake .. -DCMAKE_TOOLCHAIN_FILE=.../vcpkg.cmake
#使用静态库
#1、打开vcxproj工程文件，在xml的段里面增加如下两句话即可
#<VcpkgTriplet>x86-windows-static</VcpkgTriplet>
#<VcpkgEnabled>true</VcpkgEnabled>
#2、添加lib依赖
#$(VcpkgRoot)debug\lib\*.lib
#3、在CMake中集成静态库，需要额外指令
#cmake .. -DCMAKE_TOOLCHAIN_FILE=.../vcpkg.cmake -DVCPKG_TARGET_TRIPLET=x86-windows-static
#cmake -G "Visual Studio 14 Win64"
#eg:cmake .. -DCMAKE_TOOLCHAIN_FILE=D:\share\vcpkg\scripts\buildsystems/vcpkg.cmake -DVCPKG_TARGET_TRIPLET=x64-windows-static -G "Visual Studio 14 Win64"

#原型1,2,3,4,5,6
SET(my_exchange_server_PROTOTYPE 6)
#版本号，注意0会导致CONFIGURE_FILE替换@VAR@失败
SET(my_exchange_server_VERSION_MAJOR 1)
SET(my_exchange_server_VERSION_MINOR 1)
SET(my_exchange_server_VERSION_BUILD 1)
SET(my_exchange_server_VERSION "${my_exchange_server_VERSION_MAJOR}.${my_exchange_server_VERSION_MINOR}.${my_exchange_server_VERSION_BUILD}")

CONFIGURE_FILE(./include/XExport.h.in ${PROJECT_SOURCE_DIR}/include/XExport.h @ONLY)

#ADD_SUBDIRECTORY(main)
ADD_SUBDIRECTORY(my_exchange_server my_exchange_server)
ADD_SUBDIRECTORY(my_exchange_server_static)
#ADD_SUBDIRECTORY(lib_main)
ADD_SUBDIRECTORY(chat)

#定义 target 依赖的其他 target,确保在编译本 target 之前,其他的 target 已经被构建。 
#ADD_DEPENDENCIES(target-name depend-target1 depend-target2 …) 
ADD_DEPENDENCIES(chat_client my_exchange_server)
ADD_DEPENDENCIES(chat_server my_exchange_server)

#CMake中有一个变量CMAKE_BUILD_TYPE，可以取值枚举入下：Debug   Release  RelWithDebInfo  和  MinSizeRel
#1、通过命令行的方式
#cmake  -DCMAKE_BUILD_TYPE=Debug ..
#cmake .. -DCMAKE_BUILD_TYPE=Release -D_MSC_VER=1900 -G"NMake Makefiles"
#2、
#set(CMAKE_BUILD_TYPE Debug CACHE STRING "set build type to debug")
#set(CMAKE_BUILD_TYPE "Debug")
MESSAGE(STATUS "This is my_exchange_server CMAKE BUILD_TYPE " ${CMAKE_BUILD_TYPE})

#ELSE(WIN32)之类的语句很容易引起歧义，可以这样消除这个问题
SET(CMAKE_ALLOW_LOOSE_LOOP_CONSTRUCTS ON)
#然后可以这样使用
IF(WIN32)
	MESSAGE(STATUS "PATH: $ENV{PATH}")
	#MESSAGE(STATUS "ping www.baidu.com ...")
	#EXEC_PROGRAM(ping ARGS "www.baidu.com" OUTPUT_VARIABLE LS_OUTPUT RETURN_VALUE LS_RVALUE)
	#MESSAGE(STATUS ${LS_OUTPUT})
ELSE()
	#使用$ENV{NAME}指令就可以调用系统的环境变量了
	MESSAGE(STATUS "HOME dir: $ENV{HOME}")
	#设置环境变量的方式是：
	#SET(ENV{变量名} 值)
	
	EXEC_PROGRAM(ls ARGS "*.c" OUTPUT_VARIABLE LS_OUTPUT RETURN_VALUE LS_RVALUE)
	IF(NOT LS_RVALUE)
		MESSAGE(STATUS "ls result: " ${LS_OUTPUT})
	ENDIF()
ENDIF()
#多平台判断
IF(WIN32)
  IF(MINGW)
    MESSAGE(STATUS "WIN32 AND MINGW")
  ELSEIF(ANDROID)
    MESSAGE("WIN32 AND ANDROID")
  ELSE()
  	IF(MSVC14)
  		MESSAGE(STATUS "MSVC14")
      #set(OOLUA_BUILD_COMMAND "vs2015x86_build.bat")
    ELSE()
    	MESSAGE(STATUS "WIN32")
    ENDIF()
  ENDIF()
ELSEIF(UNIX)
  IF(ANDROID)
    MESSAGE("UNIX AND ANDROID")
  ELSE()
    MESSAGE(STATUS "UNIX")
  ENDIF()
ELSEIF(APPLE)
	MESSAGE(STATUS "APPLE")
ELSE()
	MESSAGE(FATAL_ERROR "This is unknown Paltform")
ENDIF()

#INSTALL(TARGETS...)在对应的目标目录的CMakeLists.txt定义
INSTALL(FILES README.md DESTINATION share/doc/my_exchange_server)
INSTALL(PROGRAMS run.sh DESTINATION bin)
INSTALL(DIRECTORY doc/ DESTINATION share/doc/my_exchange_server)
#在build目录执行安装指令cmake -DCMAKE_INSTALL_PREFIX=/tmp/my_exchange_server/usr ..
#如果我没有定义CMAKE_INSTALL_PREFIX会安装到什么地方？
#你可以尝试以下，cmake ..;make;make install，你会发现
#CMAKE_INSTALL_PREFIX的默认定义是/usr/local
