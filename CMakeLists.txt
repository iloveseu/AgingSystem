
cmake_minimum_required(VERSION 3.8.11)
Set(ProjectName AgingSystem)
project(${ProjectName})

if(NOT DEFINED ARCH_DIR)
# 自动检测架构
if(CMAKE_SIZEOF_VOID_P EQUAL 8)
set(ARCH_DIR "win64")
else()
set(ARCH_DIR "x86")
endif()
message(STATUS "自动设置ARCH_DIR为: ${ARCH_DIR}")
endif()

SET(CMAKE_BUILD_WITH_INSTALL_RPATH TRUE) 
SET(CMAKE_BUILD_TYPE "Release")
SET(CMAKE_INSTALL_RPATH   ../lib/ ../lib/qcefview_${ARCH_DIR}/lib/ ../lib/platforms/) 
set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} /MAP:MyExecutable.map")
   
set(CMAKE_AUTOMOC ON) # For meta object compiler
set(CMAKE_AUTORCC ON) # Resource files
set(CMAKE_AUTOUIC ON) # UI files

SET(NOMINMAX 1)    
add_definitions(-DNOMINMAX)

cmake_policy(SET CMP0074 NEW)

message(STATUS "C++ 编译器: ${CMAKE_CXX_COMPILER}")

set(Qt5_ROOT "${CMAKE_CURRENT_SOURCE_DIR}/lib/lib_qt5_${ARCH_DIR}/")
set(TS_FILES ./translations/zh_CN.ts)

find_package(Qt5 REQUIRED Widgets Network Concurrent Xml Svg SerialPort Charts PrintSupport LinguistTools Sql)
qt5_create_translation(QM_FILES "${CMAKE_CURRENT_SOURCE_DIR}/src" ${TS_FILES})
SET(RCC_FILES other/icon.qrc other/qss.qrc other/app.rc src/ftp_server/certificates.qrc)                
FILE(GLOB SC_FILES "*.cpp" "*.h" ".ui" ".inc")

include_directories(src)
aux_source_directory(src SC_FILES)

include_directories(src/widget)
aux_source_directory(src/widget SC_FILES)

include_directories(src/param)
aux_source_directory(src/param SC_FILES)

include_directories(src/part_widget)
aux_source_directory(src/part_widget SC_FILES)

include_directories(src/cip_handler)
aux_source_directory(src/cip_handler SC_FILES)

include_directories(src/log)
aux_source_directory(src/log SC_FILES)

include_directories(src/ftp_server)
aux_source_directory(src/ftp_server SC_FILES)

include_directories(src/ecc_signature)
aux_source_directory(src/ecc_signature SC_FILES)

include_directories(src/aes_encrpty)
aux_source_directory(src/aes_encrpty SC_FILES)

include_directories(src/mqtt)
aux_source_directory(src/mqtt SC_FILES)

include_directories(lib/lib_nanomsg_${ARCH_DIR}/include)
link_directories(lib/lib_nanomsg_${ARCH_DIR}/lib)

include_directories(lib/fmtlog_${ARCH_DIR}/include)
link_directories   (lib/fmtlog_${ARCH_DIR}/lib)

include_directories(lib/qcefview_${ARCH_DIR}/include)
link_directories   (lib/qcefview_${ARCH_DIR}/lib)

include_directories(lib/lib_qt5_${ARCH_DIR}/include)
link_directories   (lib/lib_qt5_${ARCH_DIR}/lib)

include_directories(lib/lib_jsoncpp_${ARCH_DIR}/include)
link_directories   (lib/lib_jsoncpp_${ARCH_DIR}/lib)

include_directories(lib/lib_qt_mqtt_${ARCH_DIR}/include)
link_directories   (lib/lib_qt_mqtt_${ARCH_DIR}/lib)

include_directories(lib/lib_openxlsx_${ARCH_DIR}/include/OpenXLSX)
link_directories   (lib/lib_openxlsx_${ARCH_DIR}/lib)

include_directories(lib/lib_ssl_${ARCH_DIR}/include)
link_directories   (lib/lib_ssl_${ARCH_DIR}/lib)

include_directories(lib/lib_ssh_${ARCH_DIR}/include)
link_directories   (lib/lib_ssh_${ARCH_DIR}/lib)

include_directories(lib/lib_zlib_${ARCH_DIR}/include)
link_directories   (lib/lib_zlib_${ARCH_DIR}/lib)

include_directories(lib/lib_curl_${ARCH_DIR}/include)
link_directories   (lib/lib_curl_${ARCH_DIR}/lib)
add_definitions(-DCURL_STATICLIB)


if(MSVC)
    add_compile_options(/std:c++latest)
else()
    add_compile_options(-Wall -g -O0)
    set(CMAKE_CXX_STANDARD 17)
    set(CMAKE_C_STANDARD 11)
    link_libraries(pthread)
endif()

include_directories(src/run_platform)
aux_source_directory(src/run_platform SC_FILES)
link_directories   (lib/lib_common_${ARCH_DIR}/lib)

set(LIB_LIST Qt5::Widgets Qt5::Network Qt5::Concurrent Qt5::Xml Qt5::Svg Qt5::SerialPort Qt5::Charts Qt5::PrintSupport Qt5::Sql
    fmt.lib
    fmtLog.lib
    AdvAPI32
    Crypt32
    normaliz
    WinMM
    Wldap32
    WS2_32
    jsoncpp.lib
    jsoncpp_static.lib
    winmm.lib
    Qt5Mqtt.lib
    OpenXLSX.lib
    Imm32
    libssh2_static.lib
    zlib.lib
    libcrypto.lib
    libssl.lib
    libcurl.lib
    )

SET(DEBUG_INFO 1)

if(DEBUG_INFO)   
    #add_executable(${PROJECT_NAME} ${SC_FILES} ${RCC_FILES})
    add_executable(${PROJECT_NAME} ${SC_FILES} ${RCC_FILES} ${QM_FILES})
else()
    #add_executable(${PROJECT_NAME} WIN32 ${SC_FILES} ${RCC_FILES} )
    add_executable(${PROJECT_NAME} WIN32 ${SC_FILES} ${RCC_FILES} ${QM_FILES})
endif()


target_link_libraries(${PROJECT_NAME} ${LIB_LIST})
install(TARGETS ${PROJECT_NAME} DESTINATION bin)
install(DIRECTORY lib/lib_common_${ARCH_DIR}/bin/ DESTINATION bin)
install(DIRECTORY lib/lib_qt5_${ARCH_DIR}/plugins/platforms DESTINATION bin)
install(DIRECTORY lib/lib_qt5_${ARCH_DIR}/plugins/iconengines DESTINATION bin)
install(DIRECTORY lib/lib_qt5_${ARCH_DIR}/plugins/imageformats DESTINATION bin)
install(DIRECTORY lib/lib_qt5_${ARCH_DIR}/plugins/sqldrivers DESTINATION bin)
install(DIRECTORY lib/qcefview_${ARCH_DIR}/bin/ DESTINATION bin)
install(DIRECTORY other/config DESTINATION bin)
install(DIRECTORY other/document DESTINATION bin)
install(FILES ${QM_FILES} DESTINATION bin)
install(FILES lib/lib_qt5_${ARCH_DIR}/bin/Qt5Sql.dll DESTINATION bin)
install(FILES lib/lib_qt5_${ARCH_DIR}/bin/Qt5Network.dll DESTINATION bin)
install(FILES lib/lib_qt5_${ARCH_DIR}/bin/Qt5Concurrent.dll DESTINATION bin)
install(FILES lib/lib_qt5_${ARCH_DIR}/bin/Qt5Xml.dll DESTINATION bin)
install(FILES lib/lib_qt5_${ARCH_DIR}/bin/Qt5Widgets.dll DESTINATION bin)
install(FILES lib/lib_qt5_${ARCH_DIR}/bin/Qt5Gui.dll DESTINATION bin)
install(FILES lib/lib_qt5_${ARCH_DIR}/bin/Qt5Core.dll DESTINATION bin)
install(FILES lib/lib_qt5_${ARCH_DIR}/bin/Qt5Svg.dll DESTINATION bin)
install(FILES lib/lib_qt5_${ARCH_DIR}/bin/Qt5Charts.dll DESTINATION bin)
install(FILES lib/lib_qt5_${ARCH_DIR}/bin/Qt5SerialPort.dll DESTINATION bin)
install(FILES lib/lib_qt5_${ARCH_DIR}/bin/Qt5SerialPortd.dll DESTINATION bin)
install(FILES lib/lib_jsoncpp_${ARCH_DIR}/bin/jsoncpp.dll DESTINATION bin)
install(FILES other/test.html DESTINATION bin)
install(FILES other/refresh.gif DESTINATION bin)
install(FILES lib/lib_qt_mqtt_${ARCH_DIR}/bin/Qt5Mqtt.dll DESTINATION bin)
install(FILES lib/lib_ssl_${ARCH_DIR}/bin/libcrypto-3-x64.dll DESTINATION bin)
install(FILES lib/lib_ssl_${ARCH_DIR}/bin/libssl-3-x64.dll DESTINATION bin)    
install(FILES lib/lib_curl_${ARCH_DIR}/bin/libcurl.dll DESTINATION bin)    