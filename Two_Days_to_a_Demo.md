# Two Days to a Demo

Cookbook for the [Nvidia Official Demos on Jetson Xavier](https://developer.nvidia.com/embedded/twodaystoademo)

> by [stevelimyh](https://github.com/stevelimyh), [SS47816](https://github.com/SS47816)
>
> @ NUS Advanced Robotics Centre
>
> MIT License

---

## Hellow AI World
![Hello_AI_World](pics/Hello_AI_World.png)


---

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
