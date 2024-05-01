ARG ROS_DISTRO="iron"

FROM ros:$ROS_DISTRO-ros-core

# the process below installs RMF binaries using the steps defined in the readme of RMF's root repository:
# https://github.com/open-rmf/rmf/blob/1c08ff48216b2f36199bf62e12358edcd2ce0616/README.md

RUN apt update && apt install \
    python3-pip \
    curl \
    python3-colcon-mixin \
    ros-dev-tools \
    -y

RUN echo "deb http://packages.osrfoundation.org/gazebo/ubuntu-stable `lsb_release -cs` main" > /etc/apt/sources.list.d/gazebo-stable.list \
    && wget https://packages.osrfoundation.org/gazebo.key -O - | sudo apt-key add -
# RUN python3 -m pip install flask-socketio fastapi uvicorn # this is only needed for rmf demos

RUN rosdep init && rosdep update

RUN colcon mixin add default https://raw.githubusercontent.com/colcon/colcon-mixin-repository/master/index.yaml \
    && colcon mixin update default

RUN apt update && apt install ros-$ROS_DISTRO-rmw-cyclonedds-cpp ros-$ROS_DISTRO-rmf-dev -y

ENTRYPOINT ["/ros_entrypoint.sh"]
CMD ["bash"]