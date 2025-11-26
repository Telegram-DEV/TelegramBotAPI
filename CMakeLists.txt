cmake_minimum_required(VERSION 3.15)
project(TelegramBotAPI VERSION 1.0 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# ----------------------- Sources -----------------------
set(SRC_LIST
        Src/API.CPP
)

# ----------------------- Dependencies -----------------------
#find_package(Boost REQUIRED CONFIG COMPONENTS system json filesystem)
find_package(Boost 1.75 REQUIRED COMPONENTS system json filesystem)
find_package(OpenSSL REQUIRED)
find_package(Threads REQUIRED)
find_package(ZLIB REQUIRED)

# ----------------------- Library -----------------------
add_library(${PROJECT_NAME} ${SRC_LIST})
add_library(${PROJECT_NAME}::${PROJECT_NAME} ALIAS ${PROJECT_NAME})

target_include_directories(${PROJECT_NAME} PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/Include>
        $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}/${PROJECT_NAME}>
)

target_link_libraries(${PROJECT_NAME}
        PRIVATE
        Boost::system
        Boost::json
        Boost::filesystem
        OpenSSL::SSL
        OpenSSL::Crypto
        Threads::Threads
        ZLIB::ZLIB
)

set_target_properties(${PROJECT_NAME} PROPERTIES
        POSITION_INDEPENDENT_CODE ON
        VERSION ${PROJECT_VERSION}
        SOVERSION 1
        CXX_VISIBILITY_PRESETS hidden
        VISIBILITY_INLINES_HIDDEN YES
)

# ----------------------- Installation -----------------------
include(GNUInstallDirs)

install(TARGETS ${PROJECT_NAME}
        EXPORT ${PROJECT_NAME}-targets
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
        RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)

install(DIRECTORY Include/ DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/${PROJECT_NAME})

install(EXPORT ${PROJECT_NAME}-targets
        FILE ${PROJECT_NAME}Targets.cmake
        NAMESPACE ${PROJECT_NAME}::
        DESTINATION lib/cmake/${PROJECT_NAME})

# ----------------------- Package Config -----------------------
include(CMakePackageConfigHelpers)

set(PACKAGE_INCLUDE_INSTALL_DIR "${CMAKE_INSTALL_INCLUDEDIR}/${PROJECT_NAME}")
configure_package_config_file(
        CMake/TelegramBotAPIConfig.cmake.in
        "${CMAKE_CURRENT_BINARY_DIR}/TelegramBotAPIConfig.cmake"
        INSTALL_DESTINATION lib/cmake/TelegramBotAPI
        PATH_VARS PACKAGE_INCLUDE_INSTALL_DIR
)

write_basic_package_version_file(
        "${CMAKE_CURRENT_BINARY_DIR}/TelegramBotAPIConfigVersion.cmake"
        VERSION ${PROJECT_VERSION}
        COMPATIBILITY SameMajorVersion
)

install(
        FILES
        "${CMAKE_CURRENT_BINARY_DIR}/TelegramBotAPIConfig.cmake"
        "${CMAKE_CURRENT_BINARY_DIR}/TelegramBotAPIConfigVersion.cmake"
        DESTINATION lib/cmake/TelegramBotAPI
)