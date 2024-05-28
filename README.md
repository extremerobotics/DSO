# DSO
DSO Visual Odometry Algorithm

This project is based on JakobEngel's DSO implementation, most of the troubleshooting can be done in the following link: https://github.com/JakobEngel/dso

The main participation of this repository is reworking the dependencies, especially Pangolin, Aruco and boost in order to be able to make DSO work with OpenCV4 and other updated dependencies. This repository was also created in order to facilitate the creation of new datasets for DSO (TUM-Dataset format). A requirements.txt file was also added to facilitate installation

#System Specifications
Ubuntu 20.4 Virtual Machine
AMD64 (x86-64)
8Gb RAM allocated

# Installation
```
git clone --recurse-submodules https://github.com/extremerobotics/DSO.git
```
#Dependencies
```
sudo apt-get install libsuitesparse-dev libeigen3-dev 
sudo apt-get install libopencv-dev
sudo apt-get install libfmt-dev
```

#Pangolin
Put this in requirements.txt pip install --upgrade pybind11

The github repository is already installed as a submodule, you can find the github page for debugging if needed here: https://github.com/stevenlovegrove/Pangolin. This readme is largely taken from that github repository' s readme

```
cd Pangolin
```
## Install dependencies (as described above, or your preferred method)
```
./scripts/install_prerequisites.sh recommended
```
## Configure and build
```
cmake -B build
sudo cmake --build build
```
# Back to Building DSO

## Installing Libzip
```
cd ~/DSO
sudo apt-get install zlib1g-dev
cd dso/thirdparty
tar -zxvf libzip-1.1.1.tar.gz
cd libzip-1.1.1/
./configure
make
sudo make install
sudo cp lib/zipconf.h /usr/local/include/zipconf.h   # (no idea why that is needed).
```
##Installing Boost 1.67.0
```
cd ~/DSO/dso/thirdparty
tar -xzvf boost_1_67_0.tar.gz
cd boost_1_67_0
./bootstrap.sh
./b2
sudo ./b2 install
```
# Building dso
```
cd ~/DSO/dso
mkdir build
cd build
cmake ..
make -j4
```
Now DSO should be working

# Mono_Dataset_code

To make a dataset you now have to go to mono_dataset_code directory
```
cd ~/DSO/mono_dataset_code
```
## Build Aruco
```
cd aruco
mkdir build
cd build
cmake ..
sudo make
sudo make install
```
## Build mono_dataset_code
```
cd ~/DSO/mono_dataset_code
cmake . && make
```
# Create a TUM Dataset

Now that you built all the needed software, here's how to create a dataset for DSO

## Camera calibration
Print cd ~/DSO/mono_dataset_code/bin/marker.pdf (Physical printing), put the marker on a big, flat, white surface and take between 500-1000 pictures of it using the same camera you used for your dataset. Make sure to take it from a far enough distance. The marker should take ~1/64th of your images. Try having a lot of low exposure time pictures.
```
cd ~/DSO/mono_dataset_code/bin
./vignetteCalibration ~/PathToImagesFolder/ #The folder with the calibration images must be called images
```
This should output 2 a csv with the camera / len's intrinsic parameters as well as vignette.png. You should then create a camera.txt file following these instructions:

##### Geometric Calibration File.


###### Calibration File for Pre-Rectified Images

    Pinhole fx fy cx cy 0
    in_width in_height
    "crop" / "full" / "none" / "fx fy cx cy 0"
    out_width out_height

###### Calibration File for FOV camera model:

    FOV fx fy cx cy omega
    in_width in_height
    "crop" / "full" / "fx fy cx cy 0"
    out_width out_height


###### Calibration File for Radio-Tangential camera model

    RadTan fx fy cx cy k1 k2 r1 r2
    in_width in_height
    "crop" / "full" / "fx fy cx cy 0"
    out_width out_height


###### Calibration File for Equidistant camera model

    EquiDistant fx fy cx cy k1 k2 k3 k4
    in_width in_height
    "crop" / "full" / "fx fy cx cy 0"
    out_width out_height


(note: for backwards-compatibility, "Pinhole", "FOV" and "RadTan" can be omitted). See the respective
`::distortCoordinates` implementation in  `Undistorter.cpp` for the exact corresponding projection function.
Furthermore, it should be straight-forward to implement other camera models.


**Explanation:**
 Across all models `fx fy cx cy` denotes the focal length / principal point **relative to the image width / height**, 
i.e., DSO computes the camera matrix `K` as

		K(0,0) = width * fx
		K(1,1) = height * fy
		K(0,2) = width * cx - 0.5
		K(1,2) = height * cy - 0.5
For backwards-compatibility, if the given `cx` and `cy` are larger than 1, DSO assumes all four parameters to directly be the entries of K, 
and ommits the above computation. 


**That strange "0.5" offset:**
 Internally, DSO uses the convention that the pixel at integer position (1,1) in the image, i.e. the pixel in the second row and second column,
contains the integral over the continuous image function from (0.5,0.5) to (1.5,1.5), i.e., approximates a "point-sample" of the 
continuous image functions at (1.0, 1.0).
In turn, there seems to be no unifying convention across calibration toolboxes whether the pixel at integer position (1,1)
contains the integral over (0.5,0.5) to (1.5,1.5), or the integral over (1,1) to (2,2). The above conversion assumes that 
the given calibration in the calibration file uses the latter convention, and thus applies the -0.5 correction.
Note that this also is taken into account when creating the scale-pyramid (see `globalCalib.cpp`).

###Note: this step was already done for David's Blackfly camera

## Creating pcalib.txt
```
cd ~/DSO/mono_dataset_code/bin
mkdir calib
```
1. Add camera.txt, images.zip and times.txt in the calib folder
2. images.zip should be an archive of jpg files with names in format: XXXX.jpg where XXXX is imageID (leading zeroes starting with 0000)
3. times.txt is a text file with three whitespace separated columns:
imageID timestamp exposureTime
Where timestamp is a POSIX time and exposureTime is a real number denoting exposure time in ms

``` cd ~/DSO/mono_dataset_code/bin
./responseCalib ~/DSO/mono_dataset_code/bin/calib/
```
This should produce a directory with pcalib.txt

If you have any doubt, read this thread in this github repository, it was the best source for the instructions I found: https://github.com/tum-vision/mono_dataset_code/issues/5#issuecomment-293414937

### Usage
You should now be able to run your dataset on DSO
```
cd ~/DSO/dso
		bin/dso_dataset \
			files=~/DSO/mono_dataset_code/bin/calib/images.zip \
			calib=~/DSO/mono_dataset_code/bin/calib/camera.txt \
			gamma=~/DSO/mono_dataset_code/bin/photoCalibResult/pcalib.txt \
			vignette=XXXXX/sequence_XX/vignette.png \
			preset=0 \
			mode=0
