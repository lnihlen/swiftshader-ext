cmake_minimum_required(VERSION 3.12)
project(swiftshader-ext)
include(ExternalProject)

set(SCIN_EXT_INSTALL_DIR "${PROJECT_BINARY_DIR}/install-ext" CACHE PATH "Scintallor External Dependencies")
file(MAKE_DIRECTORY "${SCIN_EXT_INSTALL_DIR}")
file(MAKE_DIRECTORY "${SCIN_EXT_INSTALL_DIR}/share/vulkan/icd.d")

set(SWIFTSHADER_GIT_TAG 8def9063c4fdefa8e644198d2254ef279952c88b)

if (APPLE)
    set(SCIN_SWIFTSHADER_ARGS -DCMAKE_BUILD_TYPE=Release -DSWIFTSHADER_BUILD_SAMPLES=OFF -DSWIFTSHADER_BUILD_TESTS=OFF -DSWIFTSHADER_WARNINGS_AS_ERRORS=OFF -DSWIFTSHADER_BUILD_VULKAN=ON -DSWIFTSHADER_BUILD_EGL=OFF -DSWIFTSHADER_BUILD_GLESv2=OFF -DSWIFTSHADER_BUILD_GLES_CM=OFF -DCMAKE_CXX_COMPILER=${CMAKE_CXX_COMPILER} -DCMAKE_C_COMPILER=${CMAKE_C_COMPILER})
    set(SCIN_SWIFTSHADER_INSTALL cp "<BINARY_DIR>/Debug/libvk_swiftshader.dylib" "<BINARY_DIR>/Darwin/vk_swiftshader_icd.json" "${SCIN_EXT_INSTALL_DIR}/share/vulkan/icd.d/.")
elseif(UNIX)
    set(SCIN_SWIFTSHADER_ARGS -DCMAKE_BUILD_TYPE=Debug -DSWIFTSHADER_BUILD_SAMPLES=OFF -DSWIFTSHADER_BUILD_TESTS=OFF -DSWIFTSHADER_WARNINGS_AS_ERRORS=OFF -DSWIFTSHADER_BUILD_VULKAN=ON -DSWIFTSHADER_BUILD_EGL=OFF -DSWIFTSHADER_BUILD_GLESv2=OFF -DSWIFTSHADER_BUILD_GLES_CM=OFF -DCMAKE_CXX_COMPILER=${CMAKE_CXX_COMPILER} -DCMAKE_C_COMPILER=${CMAKE_C_COMPILER} -DCMAKE_SHARED_LINKER_FLAGS=\"-fuse-ld=gold\")
    set(SCIN_SWIFTSHADER_INSTALL cp "<BINARY_DIR>/Linux/libvk_swiftshader.so" "<BINARY_DIR>/Linux/vk_swiftshader_icd.json" "${SCIN_EXT_INSTALL_DIR}/share/vulkan/icd.d")
elseif(WIN32)
    set(SCIN_SWIFTSHADER_ARGS -DPYTHON_EXECUTABLE=${PYTHON_EXECUTABLE} -DCMAKE_BUILD_TYPE=Release -DSWIFTSHADER_BUILD_PVR=OFF -DSWIFTSHADER_BUILD_TESTS=OFF -DSWIFTSHADER_WARNINGS_AS_ERRORS=OFF -DSWIFTSHADER_BUILD_VULKAN=ON -DCMAKE_GENERATOR_PLATFORM=x64 -Thost=x64 ..)
    set(SCIN_SWIFTSHADER_INSTALL cp "<BINARY_DIR>/Release/vk_swiftshader.dll" "${SCIN_EXT_INSTALL_DIR}/share/vulkan/icd.d")
else()
    message(FATAL_ERROR "unsupported build platform")
endif()

ExternalProject_Add(swiftshader
    PREFIX ext
    STEP_TARGETS install
    GIT_REPOSITORY https://github.com/google/swiftshader
    GIT_TAG ${SWIFTSHADER_GIT_TAG}
    GIT_PROGRESS ON
    UPDATE_COMMAND ""
    CMAKE_ARGS ${SCIN_SWIFTSHADER_ARGS}
    INSTALL_COMMAND ${SCIN_SWIFTSHADER_INSTALL}
)
