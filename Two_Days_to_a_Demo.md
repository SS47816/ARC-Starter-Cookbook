# Two Days to a Demo

Cookbook for the [Nvidia Official Demos on Jetson Xavier](https://developer.nvidia.com/embedded/twodaystoademo)

> by [stevelimyh](https://github.com/stevelimyh), [SS47816](https://github.com/SS47816), [nevinngyt](https://github.com/nevinngyt)
>
> @ NUS Advanced Robotics Centre
>
> MIT License

---
This guide is meant to showcase the Nvidea Jetson SDK capabilities of imagenet for image recognition, detectNet for Object Localisation and Segnet for sematic Segmentation. 

## [Hello AI World](https://github.com/dusty-nv/jetson-inference#hello-ai-world)
![Hello_AI_World](pics/Hello_AI_World.png)

### Prerequisites
#### Setting up Jetson with JetPack
JetPack should be ready on your Xavier if you followed the steps in our [Xavier Setup Guide](https://github.com/SS47816/ARC-Starter-Cookbook/blob/master/Xavier_Setup_Guide.md). (Please refer to this link if you have not set up Jetpack on your Xavier)

#### [Building the Inferencing Library](https://github.com/dusty-nv/jetson-inference/blob/master/docs/building-repo-2.md)
A library of TensorRT-accelerated deep learning networks for image recognition, object detection with localization (i.e. bounding boxes), and semantic segmentation. Noted that GoogleNet and ResNet-18 networks are selected and downloaded during the [build step](https://github.com/dusty-nv/jetson-inference/blob/master/docs/building-repo-2.md#downloading-models).

Summary of the commands to download, build, and install the project:
```bash
$ cd ~
$ sudo apt-get install git cmake
$ git clone --recursive https://github.com/dusty-nv/jetson-inference
$ cd jetson-inference
$ mkdir build
$ cd build
$ cmake ../
$ make -j$(nproc)
$ sudo make install
$ sudo ldconfig
```
## Inference

### [Image Classification with ImageNet](https://github.com/dusty-nv/jetson-inference/blob/master/docs/imagenet-console-2.md)
In this section, you will be going through a tutorial on **image recognition**, using classifcation networks that have been trained on large datasets to identify scenes and objects.

#### [ImageNet Sample Program]
- [imagenet.cpp](https://github.com/dusty-nv/jetson-inference/blob/master/examples/imagenet/imagenet.cpp)
- [imagenet.py](https://github.com/dusty-nv/jetson-inference/blob/master/python/examples/imagenet.py)

---

#### [Using ImageNet on Jetson]
```bash
$ cd jetson-inference/build/aarch64/bin

# C++
$ ./imagenet-console --network=googlenet images/orange_0.jpg output_0.jpg     # --network flag is optional (default is googlenet)

# Python
$ ./imagenet-console.py --network=googlenet images/orange_0.jpg output_0.jpg  # --network flag is optional (default is googlenet)

```

The first time you run each model, TensorRT will take a few minutes to optimize the network, so future runs using the model will load faster.

![ImageNet_Output_0](pics/output_0.jpg)

```bash
# C++
$ ./imagenet images/strawberry_0.jpg output_1.jpg

# Python
$ ./imagenet.py images/strawberry_0.jpg output_1.jpg

```

![ImageNet_Output_1](pics/output_1.jpg)

---

#### [Using Different Classification Models]
For this tutorial, GoogleNet and ResNet-18 networks are downloaded during the build step.

You can download additional networks, run the [Model Downloader](https://github.com/dusty-nv/jetson-inference/blob/master/docs/building-repo-2.md#downloading-models) tool

```bash
$ cd jetson-inference/tools
$ ./download-models.sh

```

The default classification model for the imagenet program is GoogleNet. Now, let's change that to ResNet-18:


```bash
# C++
$ ./imagenet --network=resnet-18 images/coral.jpg output_coral.jpg

# Python
$ ./imagenet.py --network=resnet-18 images/coral.jpg output_coral.jpg

```

![ImageNet_Output_Coral](pics/output_coral.jpg)

---

#### [Processing Video]
The imagenet program can handle [different types of streams](https://github.com/dusty-nv/jetson-inference/blob/master/docs/aux-streaming.md) other than image.
Now let's try running it on a test video from disk:

```bash
# Download test video (thanks to jell.yfish.us)
$ wget https://nvidia.box.com/shared/static/tlswont1jnyu3ix2tbf7utaekpzcx4rc.mkv -O jellyfish.mkv

# C++
$ ./imagenet --network=resnet-18 jellyfish.mkv jellyfish_resnet18.mkv

# Python
$ ./imagenet.py --network=resnet-18 jellyfish.mkv jellyfish_resnet18.mkv

```
The program output should be similar to this video.
<a href="https://www.youtube.com/watch?v=GhTleNPXqyU" target="_blank"><img src=https://github.com/dusty-nv/jetson-inference/raw/dev/docs/images/imagenet-jellyfish-video.jpg width="750"></a>

---



### Locating Objects with DetectNet

Next we're going to focus on object detection, and finding where in the frame various objects are located by extracting their bounding boxes. Unlike image classification, object detection networks are capable of detecting many different objects per frame. The detectNet object accepts an image as input, and outputs a list of coordinates of the detected bounding boxes along with their classes and confidence values.

First, let's try using the detectnet program to locates objects in static images. In addition to the input/output paths, there are some additional command-line options:

```
optional --network flag which changes the detection model being used (the default is SSD-Mobilenet-v2).
optional --overlay flag which can be comma-separated combinations of box, labels, conf, and none
The default is --overlay=box,labels,conf which displays boxes, labels, and confidence values
optional --alpha value which sets the alpha blending value used during overlay (the default is 120).
optional --threshold value which sets the minimum threshold for detection (the default is 0.5).
```

Note that there are additional command line parameters available for loading custom models. Launch the application with the `--help flag` to recieve more info about using them.

Here are some examples of detecting pedestrians in images with the default SSD-Mobilenet-v2 model:

![detectnet](/pics/detectnet-ssd-peds-1.jpg)


The following code is run on the console terminal to execute the model:
```
# C++
$ ./detectnet --network=ssd-mobilenet-v2 images/peds_0.jpg output.jpg     # --network flag is optional

# Python
$ ./detectnet.py --network=ssd-mobilenet-v2 images/peds_0.jpg output.jpg  # --network flag is optional
```
To find out the full list of classes the DetectNet models are trained to detect, please refer to [here]( https://github.com/dusty-nv/jetson-inference/blob/master/data/networks/ssd_coco_labels.txt)



**Processing a Directory or Sequence of Images/ Videos**

If you have multiple images or videos that you'd like to process at one time, first follow the instructions in this link ( https://github.com/dusty-nv/jetson-inference/blob/master/docs/aux-streaming.md#sequences) to load your images/video files, then you can launch the detectnet program with the path to a directory that contains images or a wildcard sequence:

```
# C++
./detectnet "images/peds_*.jpg" peds_output_%i.jpg

# Python
./detectnet.py "images/peds_*.jpg" peds_output_%i.jpg

```
You can also process videos from disk. There are some test videos found on your Jetson under /usr/share/visionworks/sources/data

```
# C++
./detectnet /usr/share/visionworks/sources/data/pedestrians.mp4 pedestrians_ssd.mp4

```
```
# Python
./detectnet.py /usr/share/visionworks/sources/data/pedestrians.mp4 pedestrians_ssd.mp4
```
You can specify which model to load by setting the `--network` flag on the command line to one of the corresponding CLI arguments from the table above. By default, SSD-Mobilenet-v2 if the optional --network flag isn't specified.

For example, if you chose to download SSD-Inception-v2 with the Model Downloader tool, you can use it like so:

```
# C++
$ ./detectnet --network=ssd-inception-v2 input.jpg output.jpg
```
```
# Python
$ ./detectnet.py --network=ssd-inception-v2 input.jpg output.jpg

```
For reference, below is the source code to detectnet.py:

```
import jetson.inference
import jetson.utils

import argparse
import sys

# parse the command line
parser = argparse.ArgumentParser(description="Locate objects in a live camera stream using an object detection DNN.")

parser.add_argument("input_URI", type=str, default="", nargs='?', help="URI of the input stream")
parser.add_argument("output_URI", type=str, default="", nargs='?', help="URI of the output stream")
parser.add_argument("--network", type=str, default="ssd-mobilenet-v2", help="pre-trained model to load (see below for options)")
parser.add_argument("--overlay", type=str, default="box,labels,conf", help="detection overlay flags (e.g. --overlay=box,labels,conf)\nvalid combinations are:  'box', 'labels', 'conf', 'none'")
parser.add_argument("--threshold", type=float, default=0.5, help="minimum detection threshold to use") 

try:
	opt = parser.parse_known_args()[0]
except:
	print("")
	parser.print_help()
	sys.exit(0)

# load the object detection network
net = jetson.inference.detectNet(opt.network, sys.argv, opt.threshold)

# create video sources & outputs
input = jetson.utils.videoSource(opt.input_URI, argv=sys.argv)
output = jetson.utils.videoOutput(opt.output_URI, argv=sys.argv)

# process frames until the user exits
while True:
	# capture the next image
	img = input.Capture()

	# detect objects in the image (with overlay)
	detections = net.Detect(img, overlay=opt.overlay)

	# print the detections
	print("detected {:d} objects in image".format(len(detections)))

	for detection in detections:
		print(detection)

	# render the image
	output.Render(img)

	# update the title bar
	output.SetStatus("{:s} | Network {:.0f} FPS".format(opt.network, net.GetNetworkFPS()))

	# print out performance info
	net.PrintProfilerTimes()

	# exit on input/output EOS
	if not input.IsStreaming() or not output.IsStreaming():
		break

```

The detectnet.cpp / detectnet.py sample that we used previously can also be used for realtime camera streaming. The types of supported cameras include:

- MIPI CSI cameras (csi://0)
- V4L2 cameras (/dev/video0)
- RTP/RTSP streams (rtsp://username:password@ip:port)


For more information about video streams and protocols, please see the [Camera Streaming and Multimedia page](https://github.com/dusty-nv/jetson-inference/blob/master/docs/aux-streaming.md)

For reference to more details on ImageNet execution, please refer to these resources:
- [Locating Objects with DetectNet](https://github.com/dusty-nv/jetson-inference/blob/master/docs/detectnet-console-2.md#detecting-objects-from-the-command-line)
- [Running the live camera demo] (https://github.com/dusty-nv/jetson-inference/blob/master/docs/detectnet-camera-2.md)



### Semantic Segmentation with Segnet

The next deep learning capability we'll cover in this tutorial is semantic segmentation. Semantic segmentation is based on image recognition, except the classifications occur at the pixel level as opposed to the entire image. This is accomplished by convolutionalizing a pre-trained image recognition backbone, which transforms the model into a Fully Convolutional Network (FCN) capable of per-pixel labeling. Especially useful for environmental perception, segmentation yields dense per-pixel classifications of many different potential objects per scene, including scene foregrounds and backgrounds.

``segNet`` accepts as input the 2D image, and outputs a second image with the per-pixel classification mask overlay. Each pixel of the mask corresponds to the class of object that was classified.

First, let's try using the segnet program to segment static images. In addition to the input/output paths, there are some additional command-line options:

```
optional --network flag changes the segmentation model being used (see above)
optional --visualize flag accepts mask and/or overlay modes (default is overlay)
optional --alpha flag sets the alpha blending value for overlay (default is 120)
optional --filter-mode flag accepts point or linear sampling (default is linear)
```
Launch the application with the ``--help`` flag for more info, and refer to the [Camera Streaming and Multimedia](https://github.com/dusty-nv/jetson-inference/blob/master/docs/aux-streaming.md) page for supported input/output protocols.

Here are some example usages of the program:

**C++**

```
$ ./segnet --network=<model> input.jpg output.jpg                  # overlay segmentation on original
$ ./segnet --network=<model> --alpha=200 input.jpg output.jpg      # make the overlay less opaque
$ ./segnet --network=<model> --visualize=mask input.jpg output.jpg # output the solid segmentation mask
```

**Python**

```
$ ./segnet.py --network=<model> input.jpg output.jpg                  # overlay segmentation on original
$ ./segnet.py --network=<model> --alpha=200 input.jpg output.jpg      # make the overlay less opaque
$ ./segnet.py --network=<model> --visualize=mask input.jpg output.jpg # output the segmentation mask
```

Here's an example of segmenting an urban street scene:

**C++**

``
$ ./segnet --network=fcn-resnet18-cityscapes images/city_0.jpg output.jpg
``

**Python**

``
$ ./segnet.py --network=fcn-resnet18-cityscapes images/city_0.jpg output.jpg
``

![segnet](/pics/segmentation-city.jpg)


This above example is from a dataset called Cityscapes. Please refer to [here](https://github.com/dusty-nv/jetson-inference/blob/master/docs/segnet-console-2.md) for more examples from other datasets

**Processing images/ Video**

Please refer to [here](https://github.com/dusty-nv/jetson-inference/blob/master/docs/aux-streaming.md#sequences) to load your files first. Then you can launch the program with the path to the directory that contains images or a wildcard sequence:

**C++**

```
$ ./segnet --network=fcn-resnet18-sun "images/room_*.jpg" room_output_%i.jpg
```

**Python**

```
$ ./segnet.py --network=fcn-resnet18-sun "images/room_*.jpg" room_output_%i.jpg
```

Note: when using wildcards, always enclose it in quotes ("*.jpg"). Otherwise, the OS will auto-expand the sequence and modify the order of arguments on the command-line, which may result in one of the input images being overwritten by the output. 

The segnet.cpp / segnet.py sample that we used previously can also be used for realtime camera streaming. The types of supported cameras include:

- MIPI CSI cameras (csi://0)
- V4L2 cameras (/dev/video0)
- RTP/RTSP streams (rtsp://username:password@ip:port)


For more information or reference for SegNet, please refer to these links
- [Semantic segmentation with SegNet 1](https://github.com/dusty-nv/jetson-inference/blob/master/docs/segnet-camera-2.md)
- [Running live camera demo](https://github.com/dusty-nv/jetson-inference/blob/master/docs/segnet-console-2.md)




## Training

### Transfer Learning in PyTorch

### Classification/Recognition

### Object Detection 


## Deploying Deep Learning
![deep-vision-primitives](pics/deep-vision-primitives.png)


---

## [Advanced - Deep Learning Nodes for ROS](https://github.com/dusty-nv/ros_deep_learning)
![ROS_Deep_Learning](pics/ROS_Deep_Learning.png)

### Install Dependencies
```bash
$ cd ~
$ sudo apt-get install git cmake
$ git clone --recursive https://github.com/dusty-nv/jetson-inference
$ cd jetson-inference
$ mkdir build
$ cd build
$ cmake ../
$ make -j$(nproc)
$ sudo make install
$ sudo ldconfig
```

### Install ROS (Melodic)

#### Installation and Setup `catkin_ws`
The installation steps on Xavier is no different from the steps on PC
You may follow the [ROS official installation guide](http://wiki.ros.org/melodic/Installation/Ubuntu) or if you are lazy, just use the condensed code below (from [ROS on Xavier](https://www.jetsonhacks.com/2018/10/26/robot-operating-system-ros-on-nvidia-jetson-agx-xavier-developer-kit/)):

```bash
# clone this guy's repo
git clone https://github.com/jetsonhacks/installROSXavier.git
cd installROSXavier

# install
./installROS.sh -p ros-melodic-desktop -p ros-melodic-rgbd-launch

# after the installation, setup the catkin_ws
./setupCatkinWorkspace.sh
```

#### Testing

In this section, you may follow the [official guide here](https://github.com/dusty-nv/ros_deep_learning) to use all the Demos provided by Nvidia.

Since there's not camera on the Xavier Developer Board, you need a additional camera moduel pluged into the Xavier to get the official guides work. 

However, you can still try some cool stuffs without a camera, by reading a video or image from file. To do that, you need to replace the input path in the all the `launch` commands with another appropriate param accroding to [this documentation](https://github.com/dusty-nv/jetson-inference/blob/master/docs/aux-streaming.md).

---

## [Advanced - TensorFlow to TensorRT Image Classification](https://github.com/NVIDIA-AI-IOT/tf_to_trt_image_classification)

### 1. Setup

### 2. Creating Frozen Graphs

### 3. Converting Frozen Graphs

### 4. Executing TensorRT

### 5. Benchmarking
