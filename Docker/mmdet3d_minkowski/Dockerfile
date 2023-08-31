# Use use previous versions, modify these variables
ARG PYTORCH="1.9.0"
ARG CUDA="11.1"
ARG CUDNN="8"

FROM pytorch/pytorch:${PYTORCH}-cuda${CUDA}-cudnn${CUDNN}-devel

##############################################
# You should modify this to match your GPU compute capability
# ENV TORCH_CUDA_ARCH_LIST="6.0 6.1 7.0+PTX"
# moved up from mmdet3d 
# PTX may be a problem?
# https://developer.nvidia.com/cuda-gpus#compute GeForce RTX 3070
ENV TORCH_CUDA_ARCH_LIST="6.0 6.1 6.2 7.0 7.2 7.5 8.0 8.6 8.6+PTX" \
    TORCH_NVCC_FLAGS="-Xfatbin -compress-all" \
    CMAKE_PREFIX_PATH="$(dirname $(which conda))/../" \
    FORCE_CUDA="1"

# Avoid Public GPG key error for mmdet3d
# https://github.com/NVIDIA/nvidia-docker/issues/1631
RUN rm /etc/apt/sources.list.d/cuda.list
RUN rm /etc/apt/sources.list.d/nvidia-ml.list
RUN apt-key del 7fa2af80
RUN apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/3bf863cc.pub
RUN apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64/7fa2af80.pub

# Install the required packages for both mmdet3d and minkowski engine
RUN apt-get update
RUN apt-get install -y ffmpeg libsm6 libxext6 git ninja-build libglib2.0-0 libsm6 libxrender-dev libxext6 cmake build-essential libopenblas-dev xterm xauth openssh-server tmux wget mate-desktop-environment-core
RUN apt-get clean
RUN rm -rf /var/lib/apt/lists/*

# install Minkowski Engine
# For faster build, use more jobs.
ENV MAX_JOBS=4
RUN git clone --recursive "https://github.com/NVIDIA/MinkowskiEngine"
RUN cd MinkowskiEngine; python setup.py install --force_cuda --blas=openblas

# (Optional, use Mirror to speed up downloads)
# RUN sed -i 's/http:\/\/archive.ubuntu.com\/ubuntu\//http:\/\/mirrors.aliyun.com\/ubuntu\//g' /etc/apt/sources.list && \
#    pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple

# Install MMEngine, MMCV and MMDetection
RUN pip install openmim && \
    mim install "mmengine" "mmcv>=2.0.0rc4" "mmdet>=3.0.0"

# Install MMDetection3D
RUN conda clean --all \
    && git clone https://github.com/open-mmlab/mmdetection3d.git -b main /mmdetection3d \
    && cd /mmdetection3d \
    && pip install --no-cache-dir -e .
# debug
RUN pwd
# Download pretrained models from config folder
# make checkpoint directory
RUN mkdir checkpoints
RUN cd checkpoints
# grep model download urls
# this is messy sorry I'm not great at grep and regex scares me
RUN grep -rh 'Weights: ' ../configs > test.txt
RUN grep -Eo '(http|https)://[a-zA-Z0-9./?=_%:-]*(.pth)' test.txt > models.txt
# download each url into checkpoint folder
RUN xargs -n 1 curl -O < models.txt
# now download the project pretrained models
RUN grep -rh 'https://download' ../projects  > test2.txt
RUN grep -Eo '(http|https)://(download)[a-zA-Z0-9./?=_%:-]*(.pth)' test2.txt > projects.txt
# download project models
RUN xargs -n 1 curl -O < projects.txt
# can probably delete, seems to just set the directory that the docker opens in
WORKDIR /mmdetection3d