# MMDetection3D with Minkowski Engine
This Dockerfile was created to use the [MMDetection3D](https://github.com/open-mmlab/mmdetection3d/tree/main) library with the [Minkowski Engine](https://github.com/NVIDIA/MinkowskiEngine). The Dockerfile is a combination of the two Docker files provided in each library's install guide.

Ensure docker version >= 19.03. To build run the following command from `Docker` folder:
```docker build -t mmdetection3d mmdet3d_minkowski/```