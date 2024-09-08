cmake_minimum_required(VERSION 3.20)

project(VapourSynth-BM3DCUDA VERSION 2.6 LANGUAGES CXX)

set(VAPOURSYNTH_INCLUDE_DIRECTORY "" CACHE PATH "Path to VapourSynth headers")

add_subdirectory(cpu_source)

