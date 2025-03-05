FROM osrf/ros:humble-desktop
ARG USERNAME=savinien
ARG USER_UID=1000
ARG USER_GID=$USER_UID

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
SHELL ["/bin/bash", "-c"]

WORKDIR /home/savinien
RUN mkdir -p fastlio/src
RUN git clone https://github.com/Livox-SDK/livox_ros_driver2.git ws_livox/src/livox_ros_driver2
RUN git clone https://github.com/Livox-SDK/Livox-SDK2.git
WORKDIR /home/savinien/fastlio/src
RUN git clone https://github.com/Ericsii/FAST_LIO.git --recursive
RUN rosdep update

#Livox SDK
WORKDIR /home/savinien/Livox-SDK2
RUN mkdir build
WORKDIR /home/savinien/Livox-SDK2/build
RUN cmake .. && make -j2
RUN make install

#livox ros2 driver
WORKDIR /home/savinien/ws_livox/src/livox_ros_driver2
RUN source /opt/ros/humble/setup.bash && ./build.sh humble
RUN source /home/savinien/ws_livox/install/setup.bash && ros2 pkg list

#fastlio 
WORKDIR /home/savinien/fastlio/
RUN source /home/savinien/ws_livox/install/setup.bash && source ../ws_livox/install/setup.bash && rosdep install --from-paths src --ignore-src -y
RUN source /home/savinien/ws_livox/install/setup.bash && source ../ws_livox/install/setup.bash && colcon build

RUN apt-get install nano

# ********************************************************
# * Anything else you want to do like clean up goes here *
# ********************************************************

# [Optional] Set the default user. Omit if you want to keep the default as root.
USER $USERNAME
CMD ["/bin/bash"]
