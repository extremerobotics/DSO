# DSO
DSO Visual Odometry Algorithm

This project is based on JakobEngel's DSO implementation, most of the troubleshooting can be done in the following link: https://github.com/JakobEngel/dso

The main participation of this repository is reworking the dependencies, especially Pangolin, Aruco and boost in order to be able to make DSO work with OpenCV4 and other updated dependencies. This repository was also created in order to facilitate the creation of new datasets for DSO (TUM-Dataset format). A requirements.txt file was also added to facilitate installation

#System Specifications
Ubuntu 20.4 Virtual Machine
AMD64 (x86-64)
8Gb RAM allocated

# Installation
git clone --recurse-submodules https://github.com/extremerobotics/DSO.git

#Dependencies
sudo apt-get install libsuitesparse-dev libeigen3-dev 
sudo apt-get install libopencv-dev
sudo apt-get install libfmt-dev


#Pangolin
Put this in requirements.txt pip install --upgrade pybind11

The github repository is already installed as a submodule, you can find the github page for debugging if needed here: https://github.com/stevenlovegrove/Pangolin. This readme is largely taken from that github repository' s readme

Please note that the installed Pangolin version is not the latest for compatibility reasons ! 
 
cd Pangolin

## Install dependencies (as described above, or your preferred method)
./scripts/install_prerequisites.sh recommended

## Configure and build
cmake -B build
sudo cmake --build build

# Back to Building DSO

## Installing Libzip
cd ~/DSO
sudo apt-get install zlib1g-dev
cd dso/thirdparty
tar -zxvf libzip-1.1.1.tar.gz
cd libzip-1.1.1/
./configure
make
sudo make install
sudo cp lib/zipconf.h /usr/local/include/zipconf.h   # (no idea why that is needed).

##Installing Boost 1.67.0

cd ~/DSO/dso/thirdparty
tar -xzvf boost_1_67_0.tar.gz
cd boost_1_67_0
./bootstrap.sh
./b2
sudo ./b2 install

# Building dso

cd ~/DSO/dso
mkdir build
cd build
cmake ..
make -j4

Now DSO should be working

#Mono_Dataset_code

To make a dataset you now have to go to ~/DSO/mono_dataset_code

##Build Aruco

cd aruco
mkdir build
cd build
cmake ..
sudo make
sudo make install

##Build mono_dataset_code

cd ~/DSO/mono_dataset_code
cmake . && make
