FROM ros:melodic

RUN apt-get update \
 && apt-get install -y --no-install-recommends \
 && apt-get install -y --no-install-recommends wget nano build-essential \
                                               zip unzip libomp-dev python3-pip \
                                               libmetis-dev \
                                               ros-melodic-pcl-ros ros-melodic-image-transport

RUN pip3 install -U pip setuptools
RUN pip3 install numpy scipy optuna evo

WORKDIR /root
RUN wget -O gtsam.zip https://github.com/borglab/gtsam/archive/4.0.0-alpha2.zip
RUN unzip gtsam.zip
RUN mkdir /root/gtsam-4.0.0-alpha2/build
WORKDIR /root/gtsam-4.0.0-alpha2/build
RUN cmake .. && make -j16
RUN make install

RUN mkdir -p /root/catkin_ws/src
WORKDIR /root/catkin_ws/src
RUN /bin/bash -c '. /opt/ros/melodic/setup.bash; catkin_init_workspace'

RUN sed -i '9c #include "lz4.h"' /usr/include/flann/util/serialization.h
RUN sed -i '10c #include "lz4hc.h"' /usr/include/flann/util/serialization.h

COPY LeGO-LOAM-BOR /root/catkin_ws/src/LeGO-LOAM-BOR/
# RUN git clone https://github.com/koide3/LeGO-LOAM-BOR
# RUN cd LeGO-LOAM-BOR && git checkout kitti

WORKDIR /root/catkin_ws
RUN /bin/bash -c '. /opt/ros/melodic/setup.bash; catkin_make -DCMAKE_BUILD_TYPE=Release'

RUN sed -i "6i source \"/root/catkin_ws/devel/setup.bash\"" /ros_entrypoint.sh

RUN apt-get clean && rm -rf /var/lib/apt/lists/*

WORKDIR /

ENTRYPOINT ["/ros_entrypoint.sh"]
CMD ["bash"]
