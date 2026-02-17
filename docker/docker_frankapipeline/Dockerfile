# syntax=docker/dockerfile:1.4

# BUILD COMMAND:
# 
#      DOCKER_BUILDKIT=1 docker build --ssh default -t frankapipeline ./docker_frankapipeline
#
#
# RUN COMMANDS:
#
#     docker run -it --rm --runtime=nvidia --gpus all -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix -v /home/ott/datasets/:/datasets --net=host --privileged frankapipeline

# OR with /cshome/share/ott/datasets:

#     docker run -it --rm --runtime=nvidia --gpus all -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix -v /cshome/share/ott/datasets/:/datasets --net=host --privileged frankapipeline


FROM nvidia/cuda:12.9.1-cudnn-runtime-ubuntu24.04

# Set environment variables
ENV DEBIAN_FRONTEND=noninteractive
ENV CONDA_AUTO_UPDATE_CONDA=false
ENV PATH=/opt/miniconda3/bin:$PATH

# Install system dependencies
RUN apt-get update && apt-get install -y \
    wget \
    git \
    build-essential \
    nano \
    wget \
    tmux \
    sudo \
    unzip

RUN yes | unminimize

RUN sudo apt install -y libgtk2.0-dev libgtk-3-dev pkg-config usbutils libxkbcommon-x11-0



################################################################################
###  Miniconda Installation
################################################################################
RUN wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O /tmp/miniconda.sh && \
    bash /tmp/miniconda.sh -b -p /opt/miniconda3 && \
    rm /tmp/miniconda.sh && \
    /opt/miniconda3/bin/conda clean -ya

# Initialize conda
RUN /opt/miniconda3/bin/conda init bash

RUN . ~/.bashrc && \
    conda tos accept --override-channels --channel https://repo.anaconda.com/pkgs/main && \
    conda tos accept --override-channels --channel https://repo.anaconda.com/pkgs/r



WORKDIR /workspace

SHELL ["/bin/bash", "-c"]

################################################################################
###  Deoxys Installation
################################################################################
# For some reason, this  deoxys installation's server does not work
# Therefore, we use the deoxys_autostart docker container
# In theory, we would not need the full deoxys install here, but currently I don't want to remove the unrequired parts
# TODO in the future this could be reomoved. 
RUN git clone https://github.com/UT-Austin-RPL/deoxys_control && \
    cd deoxys_control/deoxys && \
    # Only needed, if libfranka 0.8.0 is needed due to old robot system version
    # sed -i 's/read version/export version=0.8.0/g' ./InstallPackage && \
    sed -i 's/read version/export version=0.9.2/g' ./InstallPackage && \
    bash ./InstallPackage

# Only build the deoxys client library
RUN cd deoxys_control/deoxys && \
    make -j build_deoxys=1

RUN . ~/.bashrc && \
    conda create -n "deoxys" python=3.10 ipython ipykernel pylint jupyter pip pyzmq PyYAML easydict google-api-python-client pybullet numpy hidapi glfw numba termcolor simplejson mujoco==3.1.0 protobuf==3.20.0 matplotlib h5py scipy -c conda-forge -y

# Change IP of NUC, as we run the NUC and deoxys code on the same device
RUN sed -i 's/IP: 172.16.0.3/IP: 172.16.0.1/g' deoxys_control/deoxys/config/charmander.yml

# Add deoxys to pythonpath to enable local imports
RUN echo "export PYTHONPATH=/workspace/deoxys_control/deoxys:$PYTHONPATH" >> ~/.bashrc

RUN echo "echo 'ATTENTION: The Deoxys Server installation in this container does not work, use the one from the deoxys container! This one only exists to use the client library. Cleanup should be done in the future.' " >> ~/.bashrc

RUN echo "conda activate deoxys" >> ~/.bashrc

# ### END INSTALL DEOXYS


################################################################################
###  Install franka_pipeline Package
################################################################################
# Install additional dependencies for franka pipeline into the deoxys environment: 
SHELL ["/opt/miniconda3/bin/conda", "run", "-n", "deoxys", "/bin/bash", "-c"]

RUN mkdir -p -m 0700 ~/.ssh && \
    ssh-keyscan -H github.com >> ~/.ssh/known_hosts

RUN --mount=type=ssh \
    git clone git@github.com:David0tt/franka_pipeline.git

# Already install the franka description package for visualization in rerun
# TODO It is required in a specific version, since afterwards, some changes were made that break the visualization.
RUN cd /workspace/franka_pipeline/franka_pipeline/visualization && \
    git clone https://github.com/frankarobotics/franka_description && \
    cd franka_description && \
    git checkout 68a3288ac16e5b2149777b3c241e5743ac8e06ce


################################################################################
###  Environment installation 
###  (TODO should be used to requirements.txt, setup.py or pyproject.toml at some point)
################################################################################
RUN pip install typer robosuite
RUN pip install lerobot

# Installation (for rerun 3D Vis)
RUN pip install trimesh urdf-parser-py Pillow --quiet && \
    pip install pycollada --quiet && \
    pip install xacro && \
    pip install urdf-parser-py trimesh scipy

###  FIXES
# For some reason, the installed pybullet is built against numpy<2; Fix this by upgrading -> it is built against the correct numpy
RUN pip install --upgrade pybullet

# Fix headless opencv installation
RUN pip uninstall -y opencv-python-headless
RUN pip uninstall -y opencv-python
RUN pip install --no-cache-dir opencv-python==4.12.0.88



################################################################################
###  Install RealSense SDK and pyrealsense2
################################################################################
RUN sudo apt-get update && sudo apt-get install -y lsb-release curl gnupg    
RUN sudo mkdir -p /etc/apt/keyrings && \
    curl -sSf https://librealsense.realsenseai.com/Debian/librealsenseai.asc | gpg --dearmor | sudo tee /etc/apt/keyrings/librealsenseai.gpg > /dev/null && \
    echo "deb [signed-by=/etc/apt/keyrings/librealsenseai.gpg] https://librealsense.realsenseai.com/Debian/apt-repo `lsb_release -cs` main" | sudo tee /etc/apt/sources.list.d/librealsense.list && \
    sudo apt-get update

RUN sudo apt install -y librealsense2-utils librealsense2-dev
    # Optionally librealsense2-dbg

RUN pip install pyrealsense2



################################################################################
###  Fixes
################################################################################

# Fix for Conda masking system EGL drivers (NVIDIA)
RUN echo "export NVIDIA_DRIVER_CAPABILITIES=all" >> ~/.bashrc
RUN echo "export __EGL_VENDOR_LIBRARY_DIRS=/usr/share/glvnd/egl_vendor.d:/opt/miniconda3/envs/deoxys/share/glvnd/egl_vendor.d" >> ~/.bashrc
# Alternative switching to glx also works:
# RUN echo "export MUJOCO_GL=glx" >> ~/.bashrc


WORKDIR /workspace/franka_pipeline
