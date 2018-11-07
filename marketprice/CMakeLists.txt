#marketprice/CMakeLists.txt
CMAKE_MINIMUM_REQUIRED(VERSION 2.8)

#CMAKE_SIZEOF_VOID_P 表示 void* 的大小（例如为 4 或者 8），可以使用其来判断当前构建为 32 位还是 64 位

#CMAKE_C_COMPILER：指定C编译器
#CMAKE_CXX_COMPILER：指定C++编译器
IF(WIN32)
	#需要兼容XP时,定义_WIN32_WINNT 0x0501
	ADD_DEFINITIONS(-D_WIN32_WINNT=0x0501)
	#不用加EXPORTS定义，cmake会自动加marketprice_EXPORTS
  	ADD_DEFINITIONS(-DSM_DLL)
	SET(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} /MTd")
	SET(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} /MT")
	SET(CMAKE_CXX_FLAGS_RELWITHDEBINFO "${CMAKE_CXX_FLAGS_RELWITHDEBINFO} /MT")
	SET(CMAKE_CXX_FLAGS_MINSIZEREL "${CMAKE_CXX_FLAGS_MINSIZEREL} /MT")
ELSE()
	ADD_DEFINITIONS(-std=c11)
	ADD_DEFINITIONS(-fpermissive)
	ADD_DEFINITIONS(-fPIC)
	#-w的意思是关闭编译时的警告，也就是编译后不显示任何warning
	#-Wall选项意思是编译后显示所有警告
	#-W选项类似-Wall，会显示警告，但是只显示编译器认为会出现错误的警告
	#调试信息格式主要有下面几种：stabs，COFF，PE-COFF，OMF，IEEE-695和DWARF
	#其中DWARF在Linux中被普遍使用，dwarf2对dwarf1的改变很大，dwarf3大多是对dwarf2的扩充，可以支持C、C++、JAVA、Fortran等语言
	#使用readelf –w* marketprice命令，*是调试节名的第一个字母，如-wi就是查看.debug_info节的内容，-wl就是查看.debug_line节的内容
	#-g、-ggdb、-g3和-ggdb3，-g产生OS native format的debug信息，GDB可以使用之。而-ggdb产生的debug信息更倾向于给GDB使用的
	#如果你用的GDB调试器，那么使用-ggdb选项。如果是其他调试器，则使用-g。3只是debug信息级别，3这个级别可以调试宏。
	#SET(CMAKE_CXX_FLAGS_DEBUG "-std=c++11 ${CMAKE_CXX_FLAGS_DEBUG} -O0 -w -gdwarf-2 -ggdb3")
	#SET(CMAKE_CXX_FLAGS_RELEASE "-std=c++11 ${CMAKE_CXX_FLAGS_RELEASE} -O2 -Wall -DNODEBUG -gdwarf-2 -ggdb")
	SET(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -O0 -W -g3")
	SET(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -O3 -W")
	SET(CMAKE_CXX_FLAGS_RELWITHDEBINFO "${CMAKE_CXX_FLAGS_RELWITHDEBINFO} -O2 -W -gdwarf-3 -g")
	SET(CMAKE_CXX_FLAGS_MINSIZEREL "${CMAKE_CXX_FLAGS_MINSIZEREL} -Os -W")
  	INCLUDE_DIRECTORIES(/usr/local/include)
	LINK_DIRECTORIES(/usr/local/lib)
ENDIF()
CMAKE_POLICY(SET CMP0015 NEW)

aux_source_directory(../utils SRC_LIST)
aux_source_directory(../network SRC_LIST)
SET(SRC_LIST ${SRC_LIST}  
../utils/mp_config.c
../utils/mp_kline.c
../utils/mp_main.c
../utils/mp_message.c
../utils/mp_server.c
)
FOREACH(F ${SRC_LIST})
MESSAGE(${F})
ENDFOREACH(F)

#添加头文件搜索路径
INCLUDE_DIRECTORIES(../ ../include ${PROJECT_BINARY_DIR}/include)
#添加库文件搜索路径
LINK_DIRECTORIES(../ ../lib)

SET(LIBRARY_OUTPUT_PATH ${CMAKE_BINARY_DIR}/bin)

add_executable(marketprice ${SRC_LIST})
target_link_libraries(marketprice ev jansson mpdec rdkafka hiredis ssl crypto curl pthread)

