cmake_minimum_required(VERSION 3.9 FATAL_ERROR)
project(prob3)
set(CMAKE_INTERPROCEDURAL_OPTIMIZATION TRUE)
set(CMAKE_CXX_FLAGS "-Wall -Wextra")
set(CMAKE_CXX_FLAGS_DEBUG "-g")
set(CMAKE_CXX_FLAGS_RELEASE "-O2 -g")
find_package(ROOT REQUIRED)
set(CMAKE_CXX_STANDARD 14)
include_directories(include
        ${ROOT_INCLUDE_DIRS})

function(addfile filename)
  file(GLOB ${filename}filelist src/${filename}.*)
  add_library(${filename} ${${filename}filelist})
  set_property(TARGET ${filename} PROPERTY POSITION_INDEPENDENT_CODE ON)
endfunction()

addfile(EarthDensity)
addfile(BargerPropagator)
addfile(mosc)
addfile(mosc3)
addfile(FullSMEPropagator)
# addfile(py_wrapper)

# file(GLOB libs src/*)
# add_library(mylib ${libs})

add_executable(probRoot probRoot.cc ${headers})
target_link_libraries(probRoot ${ROOT_LIBRARIES} BargerPropagator EarthDensity mosc3 mosc)

add_executable(probLinear probLinear.cc ${headers})
target_link_libraries(probLinear ${ROOT_LIBRARIES} FullSMEPropagator BargerPropagator EarthDensity mosc3 mosc)

add_executable(probAnalytic probAnalytic.cc ${headers})
target_link_libraries(probAnalytic ${ROOT_LIBRARIES} mosc3 mosc)

add_executable(probLV probLV.cc ${headers})
target_link_libraries(probLV ${ROOT_LIBRARIES} FullSMEPropagator BargerPropagator EarthDensity mosc3 mosc)

add_library(ThreeProb SHARED src/py_wrapper.cc)
target_link_libraries(ThreeProb PUBLIC ${ROOT_LIBRARIES} FullSMEPropagator BargerPropagator EarthDensity mosc3 mosc)

install(TARGETS probRoot probLinear probAnalytic probLV ThreeProb DESTINATION out/) 
file(GLOB scripts scripts/*)
install(FILES ${scripts} README DESTINATION out/) 