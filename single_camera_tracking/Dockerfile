FROM openptrack/open_ptrack-dep
LABEL maintainer "Samir Tabriz"

ARG branch=master

RUN /bin/bash -c "source /opt/ros/kinetic/setup.bash \
    && git clone https://github.com/openptrack/open_ptrack_v2 ~/workspace/ros/src/open_ptrack \
    && cd ~/workspace/ros/src/open_ptrack \
    && git checkout $branch \
    && cd ../..  \
    && export LD_LIBRARY_PATH=/root/workspace/ros/devel/lib:/opt/ros/kinetic/lib:/opt/ros/kinetic/lib/x86_64-linux-gnu:/usr/local/lib/x86_64-linux-gnu:/usr/local/lib/i386-linux-gnu:/usr/lib/x86_64-linux-gnu:/usr/lib/i386-linux-gnu:/usr/local/cuda/lib64:/usr/local/opencv3/lib \
    && catkin_make"

RUN /bin/bash -c "source /root/workspace/ros/devel/setup.bash \
    && roscd yolo_detector/darknet_opt \
    && wget -O coco.weights https://pjreddie.com/media/files/yolo.weights"

RUN cd /root/workspace/ros/src/open_ptrack/recognition/install_scripts \
    && wget http://dlib.net/files/shape_predictor_68_face_landmarks.dat.bz2 \
    && bunzip2 shape_predictor_68_face_landmarks.dat.bz2 \ 
    && mv shape_predictor_68_face_landmarks.dat ../data/ \
    && wget https://storage.cmusatyalab.org/openface-models/nn4.small2.v1.t7 \
    && mv nn4.small2.v1.t7 ../data/

RUN ln -s /root/workspace/ros/src/open_ptrack /root/open_ptrack

RUN apt-key adv --keyserver keys.gnupg.net --recv-key C8B3A55A6F3EFCDE || sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-key C8B3A55A6F3EFCDE
RUN add-apt-repository "deb http://realsense-hw-public.s3.amazonaws.com/Debian/apt-repo xenial main" -u
RUN rm -f /etc/apt/sources.list.d/realsense-public.list
RUN apt-get update
RUN apt-get install -y librealsense2-dkms
RUN apt-get install -y librealsense2-utils
RUN apt-get install -y librealsense2-dev librealsense2-dbg


RUN /bin/bash -c "source /opt/ros/kinetic/setup.bash \
    && mkdir -p /root/catkin_ws/src && cd /root/catkin_ws/src \
    && git clone https://github.com/intel-ros/realsense.git \
    && catkin_init_workspace \
    && cd .. \
    && catkin_make clean \
    && catkin_make -DCATKIN_ENABLE_TESTING=False -DCMAKE_BUILD_TYPE=Release \
    && catkin_make install \
    && echo 'source ~/catkin_ws/devel/setup.bash' >> ~/.bashrc \
    && apt-get install ros-kinetic-rgbd-launch"

WORKDIR /root

