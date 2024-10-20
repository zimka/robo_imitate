# Imitation learning 

| ![Lite6 Physical Teleoperation](./media/pick_object.gif)  | ![UR5e Webots Teleoperation](./media/move_object.gif) |
|:-------------------------------------------------------------------:|:----------------------------------------------------:|
| Object picking with imitation learning                         | Object picking when pose of object is randomized    |

<div align="center">
	<img src="./media/robo_imitate.png">
</div>

</br>

The Robo Imitate project allows you to:

- Collect data in both real and simulated environments. Learn more [here](xarm_bringup/scripts/README.md)
- Train a Diffusion Policy model. Learn more here
- Evaluate the trained model. Learn more here


>[!IMPORTANT]  
You need to have Docker installed. If you have an Nvidia GPU, you need to additionally follow this [guide](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html). Additionaly, you need to istall Isaac-Sim If you want to use simulation. 

### Installation
- Download our source code:
```sh
git clone https://github.com/MarijaGolubovic/robo_imitate.git && cd robo_imitate/docker
```

- Build docker container
```sh
make build-pc run exec
```
- Build ROS 2 packages
```sh
colcon build --symlink-install && source ./install/local_setup.bash
```

### Model evaluation
You can download pretrain model and aditional files from this link. Downloaded model and files you need to put inside folder `imitation/outputs/train`. If folder don't exist you need to create it.


Inside docker container run:
- Run ROS 2 controler
```sh
ros2 launch xarm_bringup lite6_cartesian_launch.py rviz:=false sim:=true
```
If you want to vizualize robot set `rviz` on true. If you want to use real enviroment set `sim` on false.

- Open anather terminal and run docker
```sh
make exec
```

- Run model inside docker
```sh
cd src/robo_imitate && ./imitation/inference
```

### Model training

Inside `robo_imitate` directory run follow commands:

```sh 
docker build --build-arg UID=$(id -u) -t imitation .
```

```sh
docker run -v $(pwd)/imitation/:/docker/app/imitation:Z --gpus all -it -e DATA_PATH=imitation/data/2024_09_09_19_47_17.parquet -e EPOCH=10 imitation
```

>[!TIP]
 If you want to run model training inside docker, run this command inside the folder `src/robo_imitate`. Before that, you need to build the docker (see section "Model evaluation").. 

```sh
python3 ./imitation/compute_stats --path imitation/data/2024_09_09_19_47_17.parquet  && python3 ./imitation/train_script --path imitation/data/2024_09_09_19_47_17.parquet  --epoch 1000
```