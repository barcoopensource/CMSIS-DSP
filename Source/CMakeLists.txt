cmake_minimum_required (VERSION 3.23)
cmake_policy(SET CMP0077 NEW)
project(CMSISDSP)

# DSP Sources
SET(DSP ${CMAKE_CURRENT_SOURCE_DIR}/..)

option(NEON "Neon acceleration" OFF)
option(NEONEXPERIMENTAL "Neon experimental acceleration" OFF)
option(HELIUMEXPERIMENTAL "Helium experimental acceleration" OFF)
option(LOOPUNROLL "Loop unrolling" ON)
option(ROUNDING "Rounding" OFF)
option(MATRIXCHECK "Matrix Checks" OFF)
option(HELIUM "Helium acceleration (MVEF and MVEI supported)" OFF)
option(MVEF "MVEF intrinsics supported" OFF)
option(MVEI "MVEI intrinsics supported" OFF)
option(MVEFLOAT16 "Float16 MVE intrinsics supported" OFF)
option(DISABLEFLOAT16 "Disable building float16 kernels" OFF)
option(HOST "Build for host" OFF)
option(AUTOVECTORIZE "Prefer autovectorizable code to one using C intrinsics" OFF)
option(LAXVECTORCONVERSIONS "Lax vector conversions" ON)

###########################
#
# CMSIS DSP
#
###########################

set(CMAKE_POSITION_INDEPENDENT_CODE ON)

add_library(CMSISDSP STATIC)

include(BasicMathFunctions/Config.cmake)

include(ComplexMathFunctions/Config.cmake)

include(QuaternionMathFunctions/Config.cmake)

include(ControllerFunctions/Config.cmake)
 
include(FastMathFunctions/Config.cmake)
 
include(FilteringFunctions/Config.cmake)
 
include(MatrixFunctions/Config.cmake)

include(StatisticsFunctions/Config.cmake)

include(SupportFunctions/Config.cmake)

include(TransformFunctions/Config.cmake)
 
include(CommonTables/Config.cmake)
  
include(SVMFunctions/Config.cmake)

include(BayesFunctions/Config.cmake)

include(DistanceFunctions/Config.cmake)

include(InterpolationFunctions/Config.cmake)

include(WindowFunctions/Config.cmake)

file(GLOB_RECURSE CMSISDSP_HEADERS
     "${DSP}/Include/*.h"
)

include(GNUInstallDirs)

### Includes
target_include_directories(CMSISDSP PUBLIC
    "$<BUILD_INTERFACE:${DSP}/Include>"
    "$<INSTALL_INTERFACE:include>"
)

set(BUILD_INCLUDE_DIRS "${DSP}/Include")
if (DEFINED CMSISCORE)
    list(APPEND BUILD_INCLUDE_DIRS "${CMSISCORE}")
endif()

target_sources(
    CMSISDSP
    PUBLIC
    FILE_SET HEADERS
        BASE_DIRS "${DSP}/Include"
        FILES "${CMSISDSP_HEADERS}"
)

target_include_directories(CMSISDSP PUBLIC
    "$<BUILD_INTERFACE:${BUILD_INCLUDE_DIRS}>"
    "$<INSTALL_INTERFACE:include/cmsis>"
)

include(configDsp.cmake)

configDsp(CMSISDSP)

install(TARGETS CMSISDSP
    EXPORT cmsisTargets
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    FILE_SET HEADERS DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/cmsis
)

install(EXPORT cmsisTargets
    FILE CMSISDSPConfig.cmake
    NAMESPACE CMSIS::
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/CMSISDSP
)
