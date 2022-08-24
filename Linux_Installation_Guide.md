# Cookbook #0: Linux Installation Guide

Cookbook for new Robotics students

> by [SS47816](https://github.com/SS47816)
>
> @ NUS Advanced Robotics Centre
>
> MIT License

## Prerequisite

* A PC with Nvidia GPU (Host machine)
* An USB stick (8G and above)
* Internet Connection
* [Ubuntu 20.04.4 ISO image](https://releases.ubuntu.com/20.04.4/)

## Installation

Please follow the respective guide based on your system:

### Windows + Ubuntu

* Follow [this guide](https://itsfoss.com/install-ubuntu-1404-dual-boot-mode-windows-8-81-uefi/)

#### MacOS + Ubuntu

* [Video Tutorial](https://www.youtube.com/watch?v=kRgKlcm1XPI)

**Notes:** For either system, in the installation settings, please allocate at least 30 GB for `/root` and 30 GB for `/home`

### Nvidia GPU Driver (version 510)

1. Check for available drivers:

   ```bash
   ubuntu-drivers devices
   ```

2. Run:

   ```bash
   sudo add-apt-repository ppa:graphics-drivers/ppa
   sudo apt-get update
   # version 510 is recommended
   sudo apt-get install nvidia-driver-510
   ```

3. Reboot your system

4. Check your GPU driver is properly installed:

   ```bash
   nvidia-smi
   ```

### CUDA Toolkit for Machine Learning

* **CUDA 11.4**

   1. Download the Nvidia SDK Manager from [here](https://developer.nvidia.com/nvidia-sdk-manager)

   2. Open the Nvidia SDK Manager and Install the the libraries compatible with JetPack 5.0 on the Host Machine

   3. Post-installation actions: (from the [official doc](https://docs.nvidia.com/cuda/archive/11.6.2/cuda-installation-guide-linux/index.html#post-installation-actions))

      ```bash
      # Add this line in the .bashrc file:
      export PATH=/usr/local/cuda-11.4/bin${PATH:+:${PATH}}
      export LD_LIBRARY_PATH=/usr/local/cuda-11.4/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
      ```

   4. Restart your terminal and check:

      ```bash
      nvcc -V
      ```

   5. Compile the sample codes:

      ```bash
      # Go to the sample code folder
      cd /usr/local/cuda-11.4/samples
      
      # Compile with errors ignored: 
      make -k
      ```

* **cuDNN v8.0.3**

   > JetPack 4.4 is using cuDNN 8.0.0 (Developer Preview)

   1. Download the [cuDNN v8.0.3, for CUDA 10.2](https://developer.nvidia.com/rdp/cudnn-archive#a-collapse763-10), select the [cuDNN Library for Linux (x86)](https://developer.nvidia.com/compute/machine-learning/cudnn/secure/8.0.3.33/10.2_20200825/cudnn-10.2-linux-x64-v8.0.3.33.tgz) one

   2. Follow the steps in this [guide](https://medium.com/@taylordenouden/installing-tensorflow-gpu-on-ubuntu-18-04-89a142325138):

      ```bash
      # Unpack the archive
      cd Downloads
      tar -zxvf cudnn-10.2-linux-x64-v8.0.3.33.tgz

      # Move the unpacked contents to your CUDA directory
      sudo cp -P cuda/lib64/libcudnn* /usr/local/cuda-10.2/lib64/
      sudo cp  cuda/include/cudnn.h /usr/local/cuda-10.2/include/
         
      # Give read access to all users
      sudo chmod a+r /usr/local/cuda-10.2/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
      ```

* **TensorRT 7.1.3**

  1. Download the  [TensorRT 7X for Ubuntu 1804 and CUDA 10.2](https://developer.nvidia.com/nvidia-tensorrt-7x-download)
  2. Follow the steps in [this doc](https://docs.nvidia.com/deeplearning/tensorrt/archives/tensorrt-601/tensorrt-install-guide/index.html):
  
      ```bash
      # Install TensorRT from the Debian local repo package
      sudo dpkg -i nv-tensorrt-repo-ubuntu1804-cuda10.2-trt7.1.3.4-ga-20200617_1-1_amd64.deb
      
      # add key
      sudo apt-key add /var/nv-tensorrt-repo-cuda10.2-trt7.1.3.4-ga-20200617/7fa2af80.pub
      
      # install
      sudo apt-get update
      sudo apt-get install tensorrt
      
      # if you are using python3
      sudo apt-get install python3-libnvinfer-dev
      # if you are using TensorRT with TensorFlow
      sudo apt-get install uff-converter-tf
      ```

  3. Check:

      ```bash
      dpkg -l | grep TensorRT
      ```

### [Python Virtual Environment](https://docs.python.org/3/library/venv.html)

1. Install:

   ```bash
   sudo apt install python3-venv
   ```

2. Create an virtual environment:

   ```bash
   # Return to your `~/home/` directory
   cd
   
   # For example, my virtual environment is named as `venv-isaac`
   python3 -m venv venv-isaac
   ```

3. Activate this new virtual environment

   ```bash
   # For example, my virtual environment is named as `venv-isaac`
   source venv-isaac/bin/activate
   ```

### Nvidia Isaac SDK

[Official Installation Guide](https://docs.nvidia.com/isaac/isaac/doc/setup.html#cuda-requirements-for-machine-learning)

1. Activate your virtual environment created just now

   ```bash
   # For example, my virtual environment is named as `venv_isaac`
   source venv_isaac/bin/activate
   ```

2. Download the Issac-SDK(v20.1) package from the official website

3. Unzip it, rename it, and move it to `~/home/` folder

4. Install Dependencies:

   This includes:

   * Bazel
   * TensorFlow 1.15
   * Pytorch

   ```bash
   # Navigate to your downloaded isaac-sdk package
   # For example, mine has been moved to `~/home/` and has been renamed to `isaac-sdk`
   cd 
   cd isaac_sdk
   
   # Install dependencies
   engine/build/scripts/install_dependencies.sh
   ```

5. Once the installation is finished, it will show:

   ```bash
   "Installation Succeeded"
   ```

   Else, you will need to troubleshoot the errors.

6. Activate bash completion by adding the following line to your `~/.bashrc`:

   ```bash
   source /usr/local/lib/bazel/bin/bazel-complete.bash
   ```

#### Troubleshoot

##### Issue #1 No matching distribution found for tensorflow-gpu==1.15

This is due to your pip is not the latest version (20.xxxx)

1. Run:

   ```bash
   pip install --upgarde pip
   ```

##### Issue #2 Can not perform a '--user' install. User site-packages are not visible in this virtualenv

1. Open the file `install_dependencies.sh`
2. Delete the `--user` flag located at Ln 27, Col 34
3. Save the file, and run the cmd again

##### Issue #3 Failed to initialize NVML: Driver/library version mismatch

1. Reboot
2. export path variable by running: (I just wrote this to my .bashrc file)

   ```bash
   export PATH=/usr/local/cuda-10.0/bin:/usr/local/cuda-10.0/NsightCompute-1.0${PATH:+:${PATH}}
   ```

### Nvidia Isaac Sim (Unity3D)

1. Download the Isaac Sim file from [here](https://developer.nvidia.com/isaac/downloads)
2. Unzip it to `$HOME/isaac_sim_unity3d`

#### Install Unity Editor for Editor Mode

1. Create a [Unity account](https://id.unity.com/account/new)

2. Download Unity Hub for Linux from the [Unity Forum](https://forum.unity.com/threads/unity-hub-v-1-3-2-is-now-available.594139/)

3. Run:

   ```bash
   chmod +x UnityHub.AppImage
   ./UnityHub.AppImage
   ```

4. Click the person icon in the upper right and select **Sign in**. Sign in with your Unity ID.

5. Select **Installs** on the left, then select **Add**.

6. In the **Add Unity Version** popup window, install 2019.3.x (no modules are required). The sample project for IsaacSim Unity3D is created with 2019.3.0f6, so any newer 2019.3 version can be used.

## Developer Tools on Linux

### [Git](https://git-scm.com/): version control tool

1. run the following command in the Terminal:

```bash
sudo apt update
sudo apt install git
```

**Note:** When you use git for the first time on your machine, you need to set your user-name and your email address:

```bash
# Set user-name
git config --global user.name "FIRST_NAME LAST_NAME"

# Set email address
git config --global user.email "MY_NAME@example.com"
```

If it is your own machine and you would like to save the trouble of typing your user-name and password every time, you may save them in the machine by running: ([as describled in this post](https://stackoverflow.com/questions/35942754/how-to-save-username-and-password-in-git-gitextension))

```bash
# Store credentials
git config --global credential.helper store

# Do something in git, for example
git pull
```

### [Docker](https://www.docker.com/): Empowering App Development for Developers

[Official Installation Instructions](https://docs.docker.com/engine/install/ubuntu/)

#### Install using the repository (Recommended)

##### Setup the repository

1. Update the `apt` package index and install packages to allow `apt` to use a repository over HTTPS:

   ```bash
   $ sudo apt-get update
   
   $ sudo apt-get install \
       apt-transport-https \
       ca-certificates \
       curl \
       gnupg-agent \
       software-properties-common
   ```

2. Add Docker’s official GPG key:

   ```bash
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
   ```

   Verify that you now have the key with the fingerprint `9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88`, by searching for the last 8 characters of the fingerprint.

   ```bash
   $ sudo apt-key fingerprint 0EBFCD88
   
   pub   rsa4096 2017-02-22 [SCEA]
         9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
   uid           [ unknown] Docker Release (CE deb) <docker@docker.com>
   sub   rsa4096 2017-02-22 [S]
   ```

3. Use the following command to set up the **stable** repository. To add the **nightly** or **test** repository, add the word `nightly` or `test` (or both) after the word `stable` in the commands below. [Learn about **nightly** and **test** channels](https://docs.docker.com/engine/install/).

   ```bash
   $ sudo add-apt-repository \
      "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
      $(lsb_release -cs) \
      stable"
   ```

##### INSTALL DOCKER ENGINE

1. Update the `apt` package index, and install the *latest version* of Docker Engine and containerd, or go to the next step to install a specific version:

   ```bash
   sudo apt-get update
   sudo apt-get install docker-ce docker-ce-cli containerd.io
   ```

2. Verify that Docker Engine is installed correctly by running the `hello-world` image.

   ```bash
   sudo docker run hello-world
   ```

### [CMake](https://cmake.org/): project build tool

1. Run one of the two commands:

   ```bash
   sudo apt install cmake # cmake 3.10 (recommended unless otherwise)
   sudo snap install cmake # cmake 3.17
   ```

### [Typora](https://typora.io/): a markdown file editor

```bash
# or run:
# sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys BA300B7755AFCFAE
wget -qO - https://typora.io/linux/public-key.asc | sudo apt-key add -

# add Typora's repository
sudo add-apt-repository 'deb https://typora.io/linux ./'
sudo apt-get update

# install typora
sudo apt-get install typora
```

### [Sublime Text](https://www.sublimetext.com/): a text editor

```bash
# Install the GPG key:
wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | sudo apt-key add -

# Ensure apt is set up to work with https sources:
sudo apt-get install apt-transport-https

# Select the stable build version:
echo "deb https://download.sublimetext.com/ apt/stable/" | sudo tee /etc/apt/sources.list.d/sublime-text.list

# Update apt sources and install Sublime Text
sudo apt-get update
sudo apt-get install sublime-text
```

### [VS Code](https://code.visualstudio.com/): an Integrated Development Environment (IDE)

#### Recommended Extensions

* C/C++
* Python
* Visual Studio IntelliCode
* GitLens
* Docker
* Visual Studio IntelliCode
* ROS
* TabNine (Optional, it's magic!)

---

## Additional Resources

### CUDA (Alternative Installation Guide) (Not recommended)

[Installation Guide](https://www.tensorflow.org/install/gpu)

[Installation Guide 2](https://www.cyberciti.biz/faq/ubuntu-linux-install-nvidia-driver-latest-proprietary-driver/)

```bash
# Add NVIDIA package repositories
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-repo-ubuntu1804_10.1.243-1_amd64.deb

sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
Other
sudo dpkg -i cuda-repo-ubuntu1804_10.1.243-1_amd64.deb
sudo apt-get update
wget http://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64/nvidia-machine-learning-repo-ubuntu1804_1.0.0-1_amd64.deb
sudo apt install ./nvidia-machine-learning-repo-ubuntu1804_1.0.0-1_amd64.deb
sudo apt-get update

# Install NVIDIA driver
sudo apt-get install --no-install-recommends nvidia-driver-430
# Reboot. Check that GPUs are visible using the command: nvidia-smi

# Install development and runtime libraries (~4GB)
sudo apt-get install --no-install-recommends \
    cuda-10-1 \
    libcudnn7=7.6.4.38-1+cuda10.1  \
    libcudnn7-dev=7.6.4.38-1+cuda10.1


# Install TensorRT. Requires that libcudnn7 is installed above.
sudo apt-get install -y --no-install-recommends libnvinfer6=6.0.1-1+cuda10.1 \
    libnvinfer-dev=6.0.1-1+cuda10.1 \
    libnvinfer-plugin6=6.0.1-1+cuda10.1

```

### [Anaconda](https://docs.anaconda.com/anaconda/install/linux/): a python virtual environment manager

1. Download the Python 3.7 version Anaconda 64-Bit (x86) Installer from [here](https://www.anaconda.com/products/individual#linux), and save it to `~/Downloads/`

2. Run the following commands in the terminal:

   ```bash
   # To use GUI packages with Linux, you will need to install the following extended dependencies for Qt:
   sudo apt-get install libgl1-mesa-glx libegl1-mesa libxrandr2 libxrandr2 libxss1 libxcursor1 libxcomposite1 libasound2 libxi6 libxtst6

   bash ~/Downloads/Anaconda3-2020.02-Linux-x86_64.sh
   ```

3. Follow the instructions in the terminal, and press Enter Key and type in `yes` when requested

4. Installation successful!

5. Run

   ```bash
   # Refresh your Terminal
   source ~/.bashrc
   
   # Disable the auto-activation of conda base environment (recommended)
   conda config --set auto_activate_base False
   ```

**User Guide:**

* [Manage Environments](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html)
* [Conda Cheat Sheet](https://docs.conda.io/projects/conda/en/4.6.0/_downloads/52a95608c49671267e40c689e0bc00ca/conda-cheatsheet.pdf)

#### Create an new environment

To create a environment according to specifications stored in a file, run:

```bash
conda env create -f environment.yml
```

### [Zoom](https://zoom.us/download#client_4meeting): Online Meetings

## Troubleshooting

### Fix Broken Libraries

```bash
apt --fix-broken install
```

### Remove CUDA & Nvidia Driver

```bash
sudo apt-get purge nvidia*
sudo apt-get autoremove
sudo apt-get autoclean
sudo rm -rf /usr/local/cuda*
```
