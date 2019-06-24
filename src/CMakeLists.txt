cmake_minimum_required(VERSION 2.8.11)

project(websockets)

set(QT_MIN_VERSION "5.6.0")
find_package(Qt5 ${QT_MIN_VERSION} CONFIG REQUIRED
    Core
    Qml
    Network
    )
find_package(websocketpp REQUIRED)

set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_AUTOMOC ON)

file(GLOB_RECURSE DATABASE_SRCS
    RELATIVE ${CMAKE_CURRENT_SOURCE_DIR}
    ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/*.h
    )
add_library(websockets SHARED ${DATABASE_SRCS})
target_compile_definitions(websockets PUBLIC WEBSOCKETS_LIBRARY)
set_target_properties(websockets PROPERTIES CXX_STANDARD 14)
target_link_libraries(websockets
    core
    Qt5::Core
    Qt5::Qml
    Qt5::Network
    websocketpp::websocketpp
    $<$<BOOL:${WIN32}>:ws2_32>
    )

set(WEBSOCKETS_INSTALL_PATH "modules/QFlow/Websockets" CACHE PATH "qFlow Websockets Library Install Path")

define_property(TARGET PROPERTY QMLModulePath
    BRIEF_DOCS "Path to the module"
    FULL_DOCS "Path to the module"
    )
set_target_properties(websockets PROPERTIES
    QMLModulePath ${CMAKE_INSTALL_PREFIX}/plugins
    )
set_target_properties(websockets PROPERTIES
    EXPORT_PROPERTIES "QMLModulePath"
    )

set(PACKAGE_NAME "QFlowWebsockets")
install(TARGETS websockets
    EXPORT ${PACKAGE_NAME}Config
    RUNTIME DESTINATION ${WEBSOCKETS_INSTALL_PATH}
    ARCHIVE DESTINATION lib
    COMPONENT qflow
    )
install(EXPORT ${PACKAGE_NAME}Config DESTINATION lib/cmake/${PACKAGE_NAME})
install(FILES qmldir plugins.qmltypes DESTINATION ${WEBSOCKETS_INSTALL_PATH} COMPONENT qflow)
