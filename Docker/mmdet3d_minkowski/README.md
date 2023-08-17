# MMDetection3D with Minkowski Engine
This Dockerfile was created to use the [MMDetection3D](https://github.com/open-mmlab/mmdetection3d/tree/main) library with the [Minkowski Engine](https://github.com/NVIDIA/MinkowskiEngine). The Dockerfile is a combination of the two Docker files provided in each library's install guide.

## Buidling
Ensure docker version >= 19.03. To build run the following command from `Docker` folder:
```docker build -t mmdetection3d mmdet3d_minkowski/```

Check that Minkowski Engine is loaded correctly by running the command:
```docker run mmdetection3d python3 -c "import MinkowskiEngine; print(MinkowskiEngine.__version__)"```

## Running
To run, use the command:
```docker run -it -v /data/dir/on/local/machine:/data_dir_name_on_docker -v /path_to_on_local/mmdetection3d/checkpoints:/mmdetection3d/checkpoints --gpus all --shm-size=8g mmdetection3d```
Note that `-v /path_to_on_local/mmdetection3d/checkpoints:/mmdetection3d/checkpoints` is mounting the locally downloaded checkpoint files to the docker. It might make more sense to change the Dockerfile to download these checkpoints to the mmdetection3d directory in the docker node.

inspired from mmdet command:
```docker run --gpus all --shm-size=8g -it -v {DATA_DIR}:/mmdetection3d/data mmdetection3d```
## Docker run flags
basic `docker run` command takes the following form:
```docker run [OPTIONS] IMAGE[:TAG|@DIGEST] [COMMAND] [ARG...]```
* `-it`
    * `-i`: Keep STDIN open even if not attached
    * `-t`: Allocate a pseudo-tty
    * For interactive processes, `-i` and `-t` must be used together to allocate a tty for the container process. 
* `-v`: mounts volume. Consists of 3 fields, separated by colon characters. 
    * in case of named volumes, first field is name of the volume, and is unique on a given host machine. For anonymous volumes, the first field is omitted.
    * second field is path where the file or directory are mounted in the container.
    * third field is optional, and is a comma-separated list of optoins, such as ro. 
    * e.g. -v source:destination
* `shm-size`: Size of /dev/shm. 

## Using MMDetection3D with Minkowski Engine
I am using this to analyze LiDAR point cloud data with the pretrained models in MMDetection3D. 

After the `docker run` command I open my data directory and open python, when use the following commands:

```from mmdet3d.apis import init_model, inference_detector
import numpy as np
checkpoint_file = {checkpoint_file_path}
config_file = {config_file_path}
model = init_model(config_file, checkpoint_file)
pcd = {name_of_point_cloud_file.ply}
result, data = inference_detector(model, pcd)
preds = result.get('pred_instances_3d')
lidar_boxes = preds.get('bboxes_3d')
boxes_np = lidar_boxes.cpu().numpy()
np.save('name',boxes_np)```