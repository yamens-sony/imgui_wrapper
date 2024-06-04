cmake_minimum_required(VERSION 3.5)
project("imgui_wrapper" VERSION 0.0.1)

# default to C++17
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 17)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()


# set up include and link directories
include_directories(imgui)
include_directories(imgui/backends)
include_directories(implot)
include_directories(quickhull)

# set library
add_library(${PROJECT_NAME} SHARED
                imgui/backends/imgui_impl_glfw.cpp
                imgui/backends/imgui_impl_opengl3.cpp
                imgui/imgui_draw.cpp
                imgui/imgui_tables.cpp
                imgui/imgui_widgets.cpp
                imgui/imgui.cpp

                implot/implot_items.cpp
                implot/implot.cpp

                quickhull/QuickHull.cpp
                )


target_link_libraries(imgui_wrapper GL )

install(DIRECTORY imgui/ DESTINATION include/imgui FILES_MATCHING  REGEX ".*\\.(h|hpp)")
install(DIRECTORY implot/ DESTINATION include/implot  FILES_MATCHING  REGEX ".*\\.(h|hpp)")
install(DIRECTORY quickhull/ DESTINATION include/quickhull FILES_MATCHING  REGEX ".*\\.(h|hpp)")

install(
  TARGETS ${PROJECT_NAME}
  EXPORT export_${PROJECT_NAME}
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
  INCLUDES
  DESTINATION include)


install(EXPORT export_imgui_wrapper
  FILE imgui_wrapperTargets.cmake
  DESTINATION cmake
)

include(CMakePackageConfigHelpers)
configure_package_config_file(${CMAKE_CURRENT_SOURCE_DIR}/Config.cmake.in
    "${CMAKE_CURRENT_BINARY_DIR}/imgui_wrapperConfig.cmake"
    INSTALL_DESTINATION cmake
    NO_SET_AND_CHECK_MACRO
    NO_CHECK_REQUIRED_COMPONENTS_MACRO
    )

write_basic_package_version_file(
  "${CMAKE_CURRENT_BINARY_DIR}/imgui_wrapperConfigVersion.cmake"
  VERSION "${imgui_wrapper_VERSION}"
  COMPATIBILITY AnyNewerVersion
  )

install(FILES
    ${PROJECT_BINARY_DIR}/imgui_wrapperConfig.cmake
    ${PROJECT_BINARY_DIR}/imgui_wrapperConfigVersion.cmake
    DESTINATION cmake)

export(EXPORT export_imgui_wrapper
    FILE ${CMAKE_CURRENT_BINARY_DIR}/imgui_wrapperTargets.cmake)
