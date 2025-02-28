# Autoware Nova Carter
Integration of NVIDIA Nova Carter with Autoware

## Prerequisites

- Docker installed on your system
- Git and VCS tool installed

## Installation

1. Clone the repository and its dependencies:
```bash
mkdir -p ~/autoware_nova_carter_ws/src
cd ~/autoware_nova_carter_ws/src
git clone https://github.com/tier4/autoware_nova_carter.git
vcs import . < autoware_nova_carter/build_depends.repos
```

2. Build the Docker image:
```bash
cd ~/autoware_nova_carter_ws/src/autoware_nova_carter
docker build -t autoware_nova_carter -f .docker/Dockerfile .
```

3. Build Autoware Launch Package:
```bash
cd ~/autoware_nova_carter_ws/src
git clone https://github.com/tier4/autoware_launch -b nova-carter-integration
./autoware_nova_carter/.docker/docker_autoware.sh
cd /autoware_nova_carter_ws
colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=Release \
    --continue-on-error \
    --packages-select autoware_launch autoware_nova_carter_description
```

## Running the System

### 1. Launch Nova Carter Sensor Drivers
**In Terminal 1:**
```bash
~/autoware_nova_carter_ws/src/autoware_nova_carter/.docker/docker_sensing_vehicle.sh
source /opt/autoware_nova_carter/setup.bash
ros2 launch autoware_nova_carter_sensing sensing.launch.xml
```

### 2. Launch Nova Carter Interface
**In Terminal 2:**
```bash
docker exec -it sensing_vehicle /bin/bash
source /opt/autoware_nova_carter/setup.bash
ros2 launch autoware_nova_carter_vehicle vehicle.launch.xml
```

### 3. Launch Autoware
**In Terminal 3:**
```bash
~/autoware_nova_carter_ws/src/autoware_nova_carter/.docker/docker_autoware.sh
source /autoware_nova_carter_ws/install/setup.bash
ros2 launch autoware_launch autoware.launch.xml \
    map_path:=/autoware_map/shinagawa_2F \
    vehicle_model:=autoware_nova_carter \
    sensor_model:=sample_sensor_kit \
    data_path:=/autoware_data
```

### 4. Launch Visualization
**In Terminal 4 (on Host Machine):**
```bash
source $HOME/autoware/install/setup.bash
rviz2 -d src/launcher/autoware_launch/autoware_launch/rviz/autoware.rviz
```


