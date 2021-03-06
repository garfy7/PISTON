find_package(OpenGL)
find_package(GLUT)

find_package(VTK)
include(${VTK_USE_FILE})

find_package(Qt4)
set(QT_USE_QTOPENGL TRUE)
include(${QT_USE_FILE})
add_definitions(${QT_DEFINITIONS})

include_directories(${OPENGL_INCLUDE_DIR})
include_directories(${GLUT_INCLUDE_DIR})
include_directories(${CMAKE_CURRENT_BINARY_DIR})
include_directories(${CMAKE_SOURCE_DIR}/tutorial)

if (ENABLE_INTEROP)
  set(GPU_FLAGS "-DUSE_INTEROP")
endif ()

set(tutorial_HEADERS glwindow.h)
set(tutorial_FORMS simpleGUI.ui)
set(tutorial_SOURCES main.cpp ${CMAKE_SOURCE_DIR}/piston/util/quaternion.cpp)
QT4_WRAP_CPP(tutorial_HEADERS_MOC ${tutorial_HEADERS})
QT4_WRAP_UI(tutorial_FORMS_HEADERS ${tutorial_FORMS})

if (USE_CUDA)
  cuda_add_executable(tutorial1GPU tutorial1.cu ${tutorial_SOURCES} ${tutorial_HEADERS_MOC} ${tutorial_FORMS_HEADERS} OPTIONS ${GPU_FLAGS})
  target_link_libraries(tutorial1GPU GLEW pthread ${OPENGL_LIBRARIES} ${GLUT_LIBRARIES} ${QT_LIBRARIES})

  cuda_add_executable(tutorial2GPU tutorial2.cu ${tutorial_SOURCES} ${tutorial_HEADERS_MOC} ${tutorial_FORMS_HEADERS} OPTIONS ${GPU_FLAGS})
  target_link_libraries(tutorial2GPU GLEW pthread vtkIO vtkRendering vtkFiltering vtkCommon vtkGraphics ${OPENGL_LIBRARIES} ${GLUT_LIBRARIES} ${QT_LIBRARIES})

  cuda_add_executable(tutorial3GPU tutorial3.cu OPTIONS ${GPU_FLAGS})
  target_link_libraries(tutorial3GPU)
endif ()

add_executable(tutorial1OMP tutorial1.cpp ${tutorial_SOURCES} ${tutorial_HEADERS_MOC} ${tutorial_FORMS_HEADERS})
set_target_properties(tutorial1OMP PROPERTIES COMPILE_FLAGS "-fopenmp -DTHRUST_DEVICE_BACKEND=THRUST_DEVICE_BACKEND_OMP")
target_link_libraries(tutorial1OMP pthread gomp ${OPENGL_LIBRARIES} ${GLUT_LIBRARIES} ${QT_LIBRARIES})

add_executable(tutorial2OMP tutorial2.cpp ${tutorial_SOURCES} ${tutorial_HEADERS_MOC} ${tutorial_FORMS_HEADERS})
set_target_properties(tutorial2OMP PROPERTIES COMPILE_FLAGS "-fopenmp -DTHRUST_DEVICE_BACKEND=THRUST_DEVICE_BACKEND_OMP")
target_link_libraries(tutorial2OMP pthread vtkIO vtkRendering vtkFiltering vtkCommon vtkGraphics gomp ${OPENGL_LIBRARIES} ${GLUT_LIBRARIES} ${QT_LIBRARIES})

add_executable(tutorial3OMP tutorial3.cpp)
set_target_properties(tutorial3OMP PROPERTIES COMPILE_FLAGS "-fopenmp -DTHRUST_DEVICE_BACKEND=THRUST_DEVICE_BACKEND_OMP")
target_link_libraries(tutorial3OMP pthread gomp)



