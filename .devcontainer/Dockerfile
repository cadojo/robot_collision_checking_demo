FROM ros:humble
ARG USERNAME=USERNAME
ARG USER_UID=1000
ARG USER_GID=$USER_UID

# Delete user if it exists in container (e.g Ubuntu Noble: ubuntu)
RUN if id -u $USER_UID ; then userdel `id -un $USER_UID` ; fi

# Create the user
RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    && apt-get update \
    && apt-get install -y sudo \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME

RUN apt-get update && apt-get upgrade -y
RUN apt-get install -y python3-pip 
ENV SHELL /bin/bash

USER $USERNAME

RUN sudo apt-get update && sudo apt-get install -y \
    python3-colcon-clean git libboost-all-dev

RUN cd /tmp && git clone https://github.com/danfis/libccd.git && cd libccd \
    && mkdir -p build && cd build \
    && cmake -G 'Unix Makefiles' -DENABLE_DOUBLE_PRECISION=ON .. \
    && make -j$(nproc) && sudo make install

RUN cd /tmp && git clone https://github.com/flexible-collision-library/fcl.git \
    && cd fcl && mkdir -p build && cd build \
    && cmake .. && make -j$(nproc) && sudo make install

CMD ["/bin/bash"]