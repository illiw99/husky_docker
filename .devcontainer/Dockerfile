FROM ros:humble
ARG USERNAME=USERNAME
ARG USER_UID=1000
ARG USER_GID=$USER_UID

# Delete user if it exists in container (e.g Ubuntu Noble: ubuntu)
RUN if id -u $USER_UID ; then userdel `id -un $USER_UID` ; fi

# Create the user
RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    #
    # [Optional] Add sudo support. Omit if you don't need to install software after connecting.
    && apt-get update \
    && apt-get install -y sudo \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME
RUN apt-get update && apt-get upgrade -y
RUN apt-get install -y python3-pip
ENV SHELL /bin/bash

# ********************************************************
# * Anything else you want to do like clean up goes here *
# ********************************************************

RUN apt update -y && apt install -y \
    tmux \
    tmuxinator

RUN apt update -y && apt install -y \
    ros-humble-rviz2 \
    ros-humble-rqt \
    ros-humble-rqt-common-plugins \
    ros-humble-perception-pcl

RUN pip install \
    open3d \
    textual

WORKDIR /home/dependencies
RUN git clone https://github.com/Livox-SDK/Livox-SDK2.git \
    && cd Livox-SDK2 \
    && mkdir build \
    && cd build \
    && cmake .. && make -j"$(nproc)" \
    && sudo make install

WORKDIR /home/dependencies
RUN git clone https://github.com/MIT-SPARK/TEASER-plusplus.git \
    && cd TEASER-plusplus \
    && pip install --upgrade pip setuptools wheel build packaging \
    && pip install .

WORKDIR /home/ws/dep
RUN git clone https://github.com/pitosalas/ros2sysmon.git
RUN git clone --recurse-submodules https://github.com/TUMFTM/Multi_LiCa.git
RUN git clone -b humble https://github.com/rsasaki0109/ndt_omp_ros2.git
RUN git clone https://github.com/tier4/pointcloud_concatenate.git

# https://github.com/Livox-SDK/livox_ros_driver2.git
RUN git clone -b humble https://github.com/atinfinity/livox_ros_driver2.git
RUN git clone https://github.com/Livox-SDK/livox_ros2_driver.git

RUN echo "source /opt/ros/humble/setup.bash" >> /home/$USERNAME/.bashrc && \
    echo "source /home/ws/install/setup.bash" >> /home/$USERNAME/.bashrc && \
    chown $USERNAME:$USERNAME /home/$USERNAME/.bashrc

WORKDIR /home/ws

CMD ["/bin/bash"]