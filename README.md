# DSO
DSO Visual Odometry Algorithm

This project is based on JakobEngel's DSO implementation, most of the troubleshooting can be done in the following link: https://github.com/JakobEngel/dso

The main participation of this repository is reworking the dependencies, especially Pangolin and Aruco in order to be able to make DSO work with OpenCV4 and other updated dependencies. This repository was also created in order to facilitate the creation of new datasets for DSO (TUM-Dataset format). A requirements.txt file was also added to facilitate installation

#System Specifications
Ubuntu 20.4 Virtual Machine
AMD64 (x86-64)
8Gb RAM allocated

# Installation
git clone --recurse-submodules https://github.com/extremerobotics/DSO.git

#Dependencies
sudo apt-get install libsuitesparse-dev libeigen3-dev libboost-all-dev

#Pangolin
Put this in requirements.txt pip install --upgrade pybind11

The github repository is already installed as a submodule, you can find the github page for debugging if needed here: https://github.com/stevenlovegrove/Pangolin. This readme is largely taken from that github repository' s readme

Please note that the installed Pangolin version is not the latest for compatibility reasons ! 
 
* C++11

* OpenGL (Desktop / ES / ES2)
  * (lin) `sudo apt install libgl1-mesa-dev`

* Glew
  * (win) built automatically (assuming git is on your path)
  * (deb) `sudo apt install libglew-dev`
  * (mac) `sudo port install glew`

* CMake (for build environment)
  * (win) http://www.cmake.org/cmake/resources/software.html
  * (deb) `sudo apt install cmake`
  * (mac) `sudo port install cmake`
  
  * Python2 / Python3, for drop-down interactive console
  * (win) http://www.python.org/downloads/windows
  * (deb) `sudo apt install libpython2.7-dev`
  * (mac) preinstalled with osx
  * (for pybind11) `git submodule init && git submodule update`
  * (useful modules) `sudo python3 -mpip install numpy pyopengl Pillow pybind11`
  
# Optional dependencies

* Wayland
  * pkg-config: `sudo apt install pkg-config`
  * Wayland and EGL:`sudo apt install libegl1-mesa-dev libwayland-dev libxkbcommon-dev wayland-protocols`
  
  * FFMPEG (For video decoding and image rescaling)
  * (deb) `sudo apt install ffmpeg libavcodec-dev libavutil-dev libavformat-dev libswscale-dev libavdevice-dev`

* DC1394 (For firewire input)
  * (deb) `sudo apt install libdc1394-22-dev libraw1394-dev`

* libuvc (For cross-platform webcam video input via libusb)
  * git://github.com/ktossell/libuvc.git

* libjpeg, libpng, libtiff, libopenexr (For reading still-image sequences)
  * (deb) `sudo apt install libjpeg-dev libpng12-dev libtiff5-dev libopenexr-dev`

## Building ##

Pangolin uses the CMake portable pre-build tool. To checkout and build pangolin in the
directory 'build', execute the following at a shell (or the equivelent using a GUI):

cd Pangolin
mkdir build
cd build
cmake ..
cmake --build .


