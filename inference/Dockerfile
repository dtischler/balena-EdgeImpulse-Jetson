FROM balenalib/jetson-nano-ubuntu:bionic-20211030

# Don't prompt with any configuration questions
ENV DEBIAN_FRONTEND noninteractive
ENV UDEV=1
WORKDIR /usr/app

# Install Utilities
RUN \
    apt-get update && apt-get install -y \
    lbzip2 xorg wget tar python3 libegl1 binutils && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# Download and install BSP binaries for L4T 32.6.1
RUN wget https://developer.nvidia.com/embedded/l4t/r32_release_v6.1/t210/jetson-210_linux_r32.6.1_aarch64.tbz2 && \
    tar xf jetson-210_linux_r32.6.1_aarch64.tbz2 && \
    cd Linux_for_Tegra && \
    sed -i 's/config.tbz2\"/config.tbz2\" --exclude=etc\/hosts --exclude=etc\/hostname/g' apply_binaries.sh && \
    sed -i 's/install --owner=root --group=root \"${QEMU_BIN}\" \"${L4T_ROOTFS_DIR}\/usr\/bin\/\"/#install --owner=root --group=root \"${QEMU_BIN}\" \"${L4T_ROOTFS_DIR}\/usr\/bin\/\"/g' nv_tegra/nv-apply-debs.sh && \
    sed -i 's/chroot . \//  /g' nv_tegra/nv-apply-debs.sh && \
    ./apply_binaries.sh -r / --target-overlay && cd .. \
    rm -rf jetson-210_linux_r32.6.1_aarch64.tbz2 && \
    rm -rf Linux_for_Tegra && \
    echo "/usr/lib/aarch64-linux-gnu/tegra" > /etc/ld.so.conf.d/nvidia-tegra.conf && ldconfig

RUN sed -i 's/r32.5 main/r32.6 main/g' /etc/apt/sources.list.d/nvidia.list    

RUN apt-get update && apt-get install -y -o Dpkg::Options::=--force-confdef \
    nvidia-l4t-gstreamer \
    libgstreamer1.0-0 gstreamer1.0-plugins-base \
    gobject-introspection gstreamer1.0-plugins-good \
    gstreamer1.0-plugins-bad \
    gstreamer1.0-plugins-ugly \
    gstreamer1.0-tools \
    gstreamer1.0-plugins-base-apps \
    pulseaudio && \
    apt-get clean && rm -rf /var/lib/apt/lists/*
    
RUN apt-get update && apt-get install -y cuda-toolkit-10-2 cuda-samples-10-2 && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

RUN wget -q -O - https://cdn.edgeimpulse.com/firmware/linux/jetson.sh | bash

COPY start.sh start.sh
CMD [ "/bin/bash", "start.sh" ]
