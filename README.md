# MocapNET Project
## Ensemble of SNN Encoders for 3D Human Pose Estimation in RGB Images

We present MocapNET, an ensemble of SNN encoders that estimates the 3D human body pose based on 2D joint estimations extracted from monocular RGB images. MocapNET provides an efficient divide and conquer strategy for supervised learning. It outputs skeletal information directly into the BVH format which can be rendered in real-time or imported without any additional processing in most popular 3D animation software. The proposed architecture achieves 3D human pose estimations at state of the art rates of 400Hz using only CPU processing.

![MocapNET](https://raw.githubusercontent.com/FORTH-ModelBasedTracker/MocapNET/master/doc/mocapnetBannerImages.png)

## Youtube Video
------------------------------------------------------------------ 

[![YouTube Link](https://raw.githubusercontent.com/FORTH-ModelBasedTracker/MocapNET/master/doc/youtubevideolink.jpg)  ](https://www.youtube.com/watch?v=fH5e-KMBvM0)



## Citation
------------------------------------------------------------------ 

Please cite the [following paper](http://users.ics.forth.gr/~argyros/mypapers/2019_09_BMVC_mocapnet.pdf) if this work helps your research :

```
@inproceedings{Qammaz2019,
  author = {Qammaz, Ammar and Argyros, Antonis A},
  title = {MocapNET: Ensemble of SNN Encoders for 3D Human Pose Estimation in RGB Images},
  booktitle = {British Machine Vision Conference (BMVC 2019) (to appear)},
  publisher = {BMVA},
  year = {2019},
  month = {September},
  address = {Cardiff, UK},
  url = {http://users.ics.forth.gr/ argyros/res_mocapnet.html},
  projects =  {CO4ROBOTS,MINGEI},
  pdflink = {http://users.ics.forth.gr/ argyros/mypapers/2019_09_BMVC_mocapnet.pdf},
  videolink = {https://youtu.be/fH5e-KMBvM0}
}

```


## Overview, System Requirements and Dependencies
------------------------------------------------------------------ 
MocapNET is a high performance 2D to 3D single person pose estimator.
This code base targets recent Linux (Ubuntu) machines, and relies on the Tensorflow C-API and OpenCV. 

Tensorflow is used as the Neural Network framework for our work and OpenCV is used to enable the acquisition of images from webcams or video files as well as to provide an easy visualization method.

We have provided an [initialization script](https://github.com/FORTH-ModelBasedTracker/MocapNET/blob/master/initialize.sh) that automatically handles most dependencies, as well as download all needed pretrained models. After running it the application should be ready for use.

Any issues not automatically resolved by the script can be reported on the [issues](https://github.com/FORTH-ModelBasedTracker/MocapNET/issues) section of this repository.

In order to enable an easy to use demo with as few dependencies as possible we have included a MocapNETBenchmark utility which has hardcoded [input](https://raw.githubusercontent.com/FORTH-ModelBasedTracker/MocapNET/master/MocapNETSimpleBenchmark/testCodeInput.hpp) and [output](https://github.com/FORTH-ModelBasedTracker/MocapNET/blob/master/MocapNETSimpleBenchmark/testCodeOutput.hpp) that can run even in a system without OpenCV to give you a performance estimation of our method. If you have OpenCV available you can use our live demo ( WebcamJointBIN binary ) that will use the VNect 2D joint estimator automatically downloaded using the [initialize.sh](https://github.com/FORTH-ModelBasedTracker/MocapNET/blob/master/initialize.sh) script. However in order to achieve higher accuracy estimations you are advised to set up [OpenPose](https://github.com/CMU-Perceptual-Computing-Lab/openpose) and use it to acquire JSON files with 2D detections that can be subsequently converted to 3D BVH files using the MocapNETJSON binary. They will provide superior accuracy compared to the VNect 2D joint detector which is used for faster performance in the live demo since 2D estimation is the bottleneck of the application. Our live demo will try to run the Vnect 2D Joint estimation on your GPU and MocapNET 3D estimation on the system CPU to achieve a combined framerate of over 60fps which in most cases matches or surpasses the acquisition rate of webcameras. Unfortunately there are many GPU compatibility issues with Tensorflow C-API builds since recent versions have dropped CUDA 9.0 support as well as compute capabilities that might be required by your system, you can edit the [initialize.sh](https://github.com/FORTH-ModelBasedTracker/MocapNET/blob/master/initialize.sh) script and change the variable TENSORFLOW_VERSION according to your needs. If you want CUDA 9.0 you should se it to 1.12.0. If you want CUDA 9.0 and have a card with older compute capabilities (5.2) then choose version 1.11.0. If all else fails you can always [recompile the tensorflow C-API](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/tools/lib_package/README.md) to match your specific hardware configuration.

If you are interested in generating BVH training data for your research, we have also provided the code that handles randomization and pose perturbation from the CMU dataset. After a successful compilation, dataset generation is accessible using the scripts  [createRandomizedDataset.sh](https://github.com/FORTH-ModelBasedTracker/MocapNET/blob/master/createRandomizedDataset.sh) and [createTestDataset.sh](https://github.com/FORTH-ModelBasedTracker/MocapNET/blob/master/createTestDataset.sh). All BVH manipulation code is imported from a secondary [github project](https://github.com/AmmarkoV/RGBDAcquisition/tree/master/opengl_acquisition_shared_library/opengl_depth_and_color_renderer/src/Library/MotionCaptureLoader) that is automatically downloaded, included and built using the [initialize.sh](https://github.com/FORTH-ModelBasedTracker/MocapNET/blob/master/initialize.sh) script. These [createRandomizedDataset.sh](https://github.com/FORTH-ModelBasedTracker/MocapNET/blob/master/createRandomizedDataset.sh) and [createTestDataset.sh](https://github.com/FORTH-ModelBasedTracker/MocapNET/blob/master/createTestDataset.sh) scripts will populate the dataset/ directory with CSV files that contain valid training samples based on the CMU dataset. It is [trivial](https://pythonspot.com/reading-csv-files-in-python/) to load these files using python. After loading them using them as training samples in conjunction with a deep learning framework like [Keras](https://keras.io/) you can facilitate learning of 2D to 3D BVH. 

## Building
------------------------------------------------------------------ 

To compile the library issue :

```
 sudo apt-get install build-essential cmake libopencv-dev 

./initialize.sh

```
 
After performing changes to the source code, you do not need to rerun the initialization script. You can recompile the code by using : 

```
cd build 
cmake .. 
make 
cd ..
```


## Testing
------------------------------------------------------------------ 

To test the library performance on the CPU of your computer issue :

```
./MocapNETBenchmark --cpu
```

The output should provide you with a model name of your CPU as well as the average framerate for 1000 samples evaluated, as seen in the following screenshot.

![MocapNETBenchmark](https://raw.githubusercontent.com/FORTH-ModelBasedTracker/MocapNET/master/doc/benchmarkview.png)


------------------------------------------------------------------ 

To test your environment and OpenCV installation as well as support of your webcam issue :
```
./WebcamBIN --from /dev/video0 
```

To test OpenCV support of your video files issue :
```
./WebcamBIN --from /path/to/yourfile.mp4
```

These tests only use OpenCV (without Tensorflow or any other dependencies) and are intended as a quick method that can identify and debug configuration problems on your system.
In case of problems playing back video files or your webcam you might want to consider compiling OpenCV yourself. The [getOpenCV3.2.sh](https://github.com/FORTH-ModelBasedTracker/MocapNET/blob/master/getOpenCV3.2.sh) script has been included to automatically fetch and make OpenCV 3.2 for your convinience. You should first remove the package manager version of OpenCV and keep track of the changes performed. 
 


## Live Demo
------------------------------------------------------------------ 

Assuming that the WecamBIN executable described previously is working correctly with your input source, to do a live test of the MocapNET library using a webcam issue :

```
./WebcamJointBIN --from /dev/video0 --live
```

To dump 5000 frames from the webcam to out.bvh instead of the live directive issue :

```
./WebcamJointBIN --from /dev/video0 --frames 5000
```


To test the library using a pre recorded video file issue :

```
./WebcamJointBIN --from /path/to/yourfile.mp4
```

The output window of WebcamJointBIN contains a heatmap depicting the 2D Joint estimations, an RGB image cropped and centered on the observed person, a 2D overlay of the 2D Skeleton as well as a window that has the 3D output retrieved by our method as seen in the following image. It should be noted that this demo is performance oriented and to that end it uses the fast [VNect](http://gvv.mpi-inf.mpg.de/projects/VNect/) artificial neural network as its 2D joint estimator. On recent systems the framerate achieved by the application should match the input framerate of your camera which is typically 30 or 60 fps. That being said the visualization provided will provide detailed framerate information for every part of the demo and the bottleneck is the 2D joint estimator.

![WebcamJointBin](https://raw.githubusercontent.com/FORTH-ModelBasedTracker/MocapNET/master/doc/demoview.jpg)


BVH output files can be easily viewed using a variety of compatible applicatons. We suggest [BVHacker](https://www.bvhacker.com/) that is free for download and compatible with [Wine](https://wiki.winehq.org/)


## Higher accuracy using OpenPose JSON files
------------------------------------------------------------------ 

In order to get higher accuracy output compared to the live demo which is more performance oriented, you can use OpenPose and the 2D output JSON files produced by it. The MocapNETJSON application can convert them to a BVH file. After downloading [OpenPose](https://github.com/CMU-Perceptual-Computing-Lab/openpose) and building it you can use it to acquire 2D JSON body pose data by running :

```
build/examples/openpose/openpose.bin -number_people_max 1 --hand --write_json /path/to/outputJSONDirectory/ -video /path/to/yourVideoFile.mp4
```

This will create files in the following fashion /path/to/outputJSONDirectory/yourVideoFile_XXXXXXXXXXXX_keypoints.json Notice that the filenames generated encode the serial number by padding it up to 12 characters (marked as X). You provide this information to our executable using the --seriallength commandline option.

You can convert them to a BVH file by issuing :
```
./MocapNETJSON --from /path/to/outputJSONDirectory/ --label yourVideoFile --seriallength 12 --size 1920 1080
```



## License
------------------------------------------------------------------ 
This library is provided under the [FORTH license](https://github.com/FORTH-ModelBasedTracker/MocapNET/blob/master/license.txt)
