project( Segmentation )

# The version number
set(Segmentation_VERSION_MAJOR 1)
set(Segmentation_VERSION_MINOR 0)


# Source files used by Segmentation
set(libsrcs HistologicalEntities.cpp MorphologicOperations.cpp PixelOperations.cpp NeighborOperations.cpp ConnComponents.cpp )
set(gpulibsrcs HistologicalEntitiesGPU.cpp MorphologicOperationsGPU.cpp PixelOperationsGPU.cpp NeighborOperationsGPU.cpp UtilsCVImageIO.cpp)
set(cudalibsrcs cuda/ccl_uf.cu cuda/global_queue.cu cuda/global_queue_dist.cu cuda/global_queue_float.cu cuda/neighbor-ops.cu cuda/watershed-dw-korbes.cu cuda/queue.cu cuda/imrecon_queue_int_kernel.cu cuda/pixel-ops.cu cuda/change_kernel.cu cuda/imreconstruct_binary_kernel.cu cuda/imreconstruct_int_kernel.cu cuda/imreconstruct_float_kernel.cu )

include_directories(${Segmentation_SOURCE_DIR}/cuda)
include_directories(${cci_common_SOURCE_DIR})
link_directories(${cci_common_BINARY_DIR})

set(libname segment)

# Add rule to generate execution engine, as a shared library (.so)
#add_library("${libname}" SHARED ${libsrcs} )
if(CUDA_FOUND)
	cuda_add_library("${libname}" SHARED ${libsrcs} ${gpulibsrcs} ${cudalibsrcs})
	set_target_properties(${libname} PROPERTIES COMPILE_DEFINITIONS "WITH_CUDA") 
else(CUDA_FOUND)
	add_library("${libname}" SHARED ${libsrcs} ${gpulibsrcs})
endif(CUDA_FOUND)

# Link opencv stuff to the execution engine
target_link_libraries("${libname}" ${OpenCV_LIBS})


# if build test applications is set, than build them: currently just one
if(BUILD_TEST_APPLICATIONS)
	add_subdirectory("test/src")
endif(BUILD_TEST_APPLICATIONS)

