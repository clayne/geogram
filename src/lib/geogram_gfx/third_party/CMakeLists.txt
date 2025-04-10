vor_reset_warning_level()

if(
    ${CMAKE_CXX_COMPILER_ID} STREQUAL "Clang" OR
    ${CMAKE_CXX_COMPILER_ID} STREQUAL "AppleClang"
)
  add_definitions(-Wno-unknown-warning-option)
  add_definitions(-Wno-zero-as-null-pointer-constant)
  add_definitions(-Wno-unused-parameter)
endif()

if(ANDROID)
  add_definitions(-Wno-unused-variable)
endif()

# Since we are in a CMake "object library" (i.e.,
# a set of object files compiled with same options),
# the automatically generated geogram_gfx_EXPORTS
# pre-processor definition of the parent library
# is not automatically inherited (this is why
# I set it manually)
if(BUILD_SHARED_LIBS)
   add_definitions(-Dgeogram_gfx_EXPORTS)
endif()


aux_source_directories(SOURCES "Source Files"            .)
aux_source_directories(SOURCES "Source Files\\glad"      glad)
aux_source_directories(SOURCES "Source Files\\ImGuiColorTextEdit" ImGuiColorTextEdit )

if(GEOGRAM_WITH_LUA)
   aux_source_directories(SOURCES "Source Files\\imgui_lua_bindings" imgui_lua_bindings)
endif()

add_library(geogram_gfx_third_party OBJECT ${SOURCES})

target_sources(
  geogram_gfx_third_party PRIVATE
    imgui/imgui.cpp
    imgui/imgui_demo.cpp
    imgui/imgui_draw.cpp
    imgui/imgui_tables.cpp
    imgui/imgui_widgets.cpp
    imgui/misc/cpp/imgui_stdlib.cpp
)

target_sources(
  geogram_gfx_third_party PRIVATE
    imgui/backends/imgui_impl_opengl3.cpp
)

target_include_directories(
  geogram_gfx_third_party PRIVATE
  ${PROJECT_SOURCE_DIR}/src/lib/geogram_gfx/third_party/imgui
)

if(ANDROID)
#  target_sources(
#    geogram_gfx_third_party PRIVATE
#       imgui/backends/imgui_impl_android.cpp
#  )
  target_include_directories(geogram_gfx_third_party PRIVATE
    ${ANDROID_NDK}/sources/android/native_app_glue
  )
else()
  target_sources(
    geogram_gfx_third_party PRIVATE
       imgui/backends/imgui_impl_glfw.cpp
  )
  target_include_directories(
    geogram_gfx_third_party PRIVATE
    ${PROJECT_SOURCE_DIR}/src/lib/third_party/glfw/include
  )
endif()



set_target_properties(
    geogram_gfx_third_party PROPERTIES
    FOLDER "GEOGRAM"
)
