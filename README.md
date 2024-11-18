# Tutorials for Software Installation on Jetson Xaiver NX

This page is used to take down notes for all the hardworks I've been through to get everything working correctly.


## Build Spinnaker SDE with ROS2
The original [tutorial](https://docs.ros.org/en/iron/p/spinnaker_camera_driver/) is a good starting point. But for the use on Jetson, some annoying errors need to be addressed.

1. **error: <camera_info_manager/camera_info_manager.hpp>**
	- solution:

   		`sudo apt install ros-foxy-image-common`
   
2. **error: std:: "optional" in namespace "std" does not name a template type.**
	- solution: should change or add the c++ standard in CMakeLists.txt
   ```
	set(CMAKE_CXX_STANDARD_17)
	set(CMAKE_CXX_STANDARD_REQUIRED ON)
   ```
  
## Install OpenCV with CUDA Enabled
While the [tutorial](https://developer.ridgerun.com/wiki/index.php/Compiling_OpenCV_from_Source) is a good reference to build opencv with cuda. But on jetson, we need a little bit tweaks to make it work on python3.
```
cd opencv
mkdir build
cd build
cmake -D CMAKE_BUILD_TYPE=RELEASE \
-D CMAKE_INSTALL_PREFIX=/usr/local \
-D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib/modules/ \
-D WITH_CUDA=ON \
-D OPENCV_GENERATE_PKGCONFIG=ON \
-D BUILD_EXAMPLES=OFF \
-D INSTALL_PYTHON_EXAMPLES=OFF \
-D INSTALL_C_EXAMPLES=OFF \
-D PYTHON_EXECUTABLE=$(which python2) \
-D BUILD_opencv_python2=OFF \
-D PYTHON3_EXECUTABLE=$(which python3) \
-D BUILD_opencv_python3=ON \
-D PYTHON_DEFAULT_EXECUTABLE=/usr/bin/python3
-D PYTHON3_INCLUDE_DIR=$(python3 -c "from distutils.sysconfig import get_python_inc; print(get_python_inc())") \
-D PYTHON3_PACKAGES_PATH=$(python3 -c "from distutils.sysconfig import get_python_lib; print(get_python_lib())") \
 ..
```
To make sure python3 can find the correct build of cv2. We need to make sure no cv2 is installed locally.

```
python3 -m pip uninstall opencv-python
```

Finally, if everything is correct, the following command should return a value larger than 0.

```
$ python3
>>> cv2.cuda.getCudaEnabledDeviceCount() # should return 1 or larger than 1
```


