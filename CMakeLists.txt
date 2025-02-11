cmake_minimum_required(VERSION 3.15)
project(VR)

## Compile as C++11, supported in ROS Kinetic and newer
add_compile_options(-std=c++11)

find_package(catkin REQUIRED COMPONENTS
	roscpp
	rospy
	sensor_msgs
	std_msgs
	geometry_msgs
	message_generation
	cv_bridge
	image_transport
	message_filters
)


find_package( OpenCV REQUIRED
  NO_MODULE
  PATHS C:/Users/Dyros/cv/opencv-3.4.13/build2
  NO_DEFAULT_PATH
)
add_message_files(
	FILES
	matrix_3_4.msg
	trackers.msg
)

generate_messages(
	DEPENDENCIES
	std_msgs
	geometry_msgs
)


# Qt 5
# Important :
# Remember to set CMAKE_PREFIX_PATH to Qt5 cmake modules path for your targeted platform
# or set them here manually (not portable).
if(CMAKE_HOST_UNIX)
  list(APPEND CMAKE_PREFIX_PATH "/opt/Qt/5.15.2/gcc_64/lib/cmake")
else()
    if(${PLATFORM} MATCHES 64)
      set(suffix "_64")
    endif()
  list(APPEND DCMAKE_PREFIX_PATH "C:/Qt/5.15.2/msvc2019${suffix}/lib/cmake")
endif()

# Set CMake to run moc automatically has needed.
set(CMAKE_AUTOMOC ON)
# Set CMake to handle *.ui generation to avoid using QT5_WRAP_UI() macro.
set(CMAKE_AUTOUIC ON)
# Add the build dir to include paths to find UI's headers.
set(CMAKE_INCLUDE_CURRENT_DIR ON)

# Find Qt5 libraries.
foreach(module Core Gui Widgets)
    find_package(Qt5 REQUIRED COMPONENTS ${module})
    list(APPEND QT_INCLUDE_DIRS "${Qt5${module}_INCLUDE_DIRS}")
    list(APPEND QT_LIBRARIES    "${Qt5${module}_LIBRARIES}")
    list(APPEND QT_FLAGS        "${Qt5${module}_EXECUTABLE_COMPILE_FLAGS}")
    list(APPEND QT_DEFINITIONS  "${Qt5${module}_DEFINITIONS}")
endforeach()
# Correct QT_FLAGS having semicolons, which break the compile step on POSIX
list(REMOVE_DUPLICATES QT_FLAGS)


catkin_package( ## if you dont add this, executables are not found
  INCLUDE_DIRS include
  LIBRARIES VR
  CATKIN_DEPENDS roscpp rospy std_msgs geometry_msgs message_runtime message_filters image_transport cv_bridge sensor_msgs
)


add_executable(vr src/main.cpp src/render.cpp src/ricohStream.cpp src/hmdHandler.cpp  src/glad.c 
									src/Matrices.cpp
									src/lodepng.cpp
									src/pathtools.cpp
									src/strtools.cpp)
# add_executable(overlay src/main_rviz.cpp src/rvizOverlay.cpp src/glad.c
						# src/Matrices.cpp
						# src/lodepng.cpp
						# src/pathtools.cpp
						# src/strtools.cpp)

add_executable(overlay src/main_rviz2.cpp src/qtoverlaycontroller.cpp src/qtoverlaycontroller.h)

# add_executable(camera src/usbcamera.cpp)

include_directories(C:/Users/Dyros/Desktop/avatar/src/VR/include)
include_directories(${catkin_INCLUDE_DIRS})
target_link_libraries(vr ${catkin_LIBRARIES})


include_directories(C:/Users/Dyros/openvr/headers)
include_directories(C:/Users/Dyros/openvr/bin/win64)
target_link_libraries(vr C:/Users/Dyros/openvr-latest/lib/win64/openvr_api.lib)

include_directories(C:/Users/Dyros/eigen)

include_directories(${OpenCV_INCLUDE_DIRS})
target_link_libraries(vr ${OpenCV_LIBRARIES})


include_directories(C:/Users/Dyros/ffmpeg/include)

include_directories(
	C:/opt/ros/melodic/x64/include/SDL2
	C:/opt/ros/melodic/x64/include/SDL
	C:/opt/ros/melodic/x64/include/GL
)

include_directories(${QT_INCLUDE_DIRS})

target_link_libraries(vr 
	C:/opt/ros/melodic/x64/lib/SDL.lib
	C:/opt/ros/melodic/x64/lib/SDL2.lib
	C:/opt/ros/melodic/x64/lib/SDLmain.lib
	C:/opt/ros/melodic/x64/lib/SDL_image.lib
	C:/opt/ros/melodic/x64/lib/glew32.lib
	C:/opt/ros/melodic/x64/lib/glew32s.lib
	C:/opt/ros/melodic/x64/lib/freeglut.lib	
	C:/Users/Dyros/Desktop/avatar/src/VR/glfwLib/glfw3.lib
	C:/Users/Dyros/Desktop/avatar/src/VR/glfwLib/glfw3dll.lib
)

target_link_libraries(vr 

C:/Users/Dyros/ffmpeg/lib/avcodec.lib
C:/Users/Dyros/ffmpeg/lib/avdevice.lib
C:/Users/Dyros/ffmpeg/lib/avfilter.lib
C:/Users/Dyros/ffmpeg/lib/avformat.lib
C:/Users/Dyros/ffmpeg/lib/avutil.lib
C:/Users/Dyros/ffmpeg/lib/postproc.lib
C:/Users/Dyros/ffmpeg/lib/swresample.lib
C:/Users/Dyros/ffmpeg/lib/swscale.lib
)

target_link_libraries(overlay ${catkin_LIBRARIES})
target_link_libraries(overlay ${OpenCV_LIBRARIES})
target_link_libraries(overlay ${QT_LIBRARIES})

target_link_libraries(overlay 
	C:/Users/Dyros/openvr-latest/lib/win64/openvr_api.lib
)

# target_link_libraries(camera ${OpenCV_LIBRARIES})