# MMDetection3D with Minkowski Engine
This Dockerfile was created to use the [MMDetection3D](https://github.com/open-mmlab/mmdetection3d/tree/main) library with the [Minkowski Engine](https://github.com/NVIDIA/MinkowskiEngine). The Dockerfile is a combination of the two Docker files provided in each library's install guide.

Ensure docker version >= 19.03. To build run the following command from `Docker` folder:
```docker build -t mmdetection3d mmdet3d_minkowski/```

Check that Minkowski Engine is loaded correctly by running the command:
```docker run mmdetection3d python3 -c "import MinkowskiEngine; print(MinkowskiEngine.__version__)"```

To run, use the command:
```docker run -it -v /data/dir/on/local/machine:/data_dir_name_on_docker -v /path_to_on_local/mmdetection3d/checkpoints:/mmdetection3d/checkpoints --gpus all --shm-size=8g mmdetection3d```
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

