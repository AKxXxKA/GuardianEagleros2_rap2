
# GuardianEagle - Autonomous Drone with PX4, ROS 2, Gazebo, and YOLOv8

GuardianEagle is an innovative project aimed at developing a sophisticated autonomous drone system using cutting-edge technologies in flight control, robotic software infrastructure, simulation, and real-time object detection. The project integrates PX4, ROS 2, Gazebo, and YOLOv8 to create a reliable and intelligent aerial platform capable of autonomous navigation in complex environments. Our goal is to enhance the drone's capabilities for applications such as search and rescue, surveillance, and delivery services. By leveraging these advanced technologies, GuardianEagle seeks to push the boundaries of autonomous drone performance, providing practical solutions for real-world challenges.

## Installation

### Setting Up the Environment
1. **Create a Virtual Environment**
    ```bash
    python -m venv ~/guardian-eagle-venv
    source ~/guardian-eagle-venv/bin/activate
    ```

2. **Clone the Repository**
    ```bash
    git clone https://github.com/your-repo/GuardianEagle.git
    ```

### Installing Required Software

3. **Install PX4**
    ```bash
    cd ~
    git clone https://github.com/PX4/PX4-Autopilot.git --recursive
    bash ./PX4-Autopilot/Tools/setup/ubuntu.sh
    cd PX4-Autopilot/
    make px4_sitl
    ```

4. **Install ROS 2**
    ```bash
    cd ~
    sudo apt update && sudo apt install locales
    sudo locale-gen en_US en_US.UTF-8
    sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
    export LANG=en_US.UTF-8
    sudo apt install software-properties-common
    sudo add-apt-repository universe
    sudo apt update && sudo apt install curl -y
    sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null
    sudo apt update && sudo apt upgrade -y
    sudo apt install ros-humble-desktop
    sudo apt install ros-dev-tools
    source /opt/ros/humble/setup.bash && echo "source /opt/ros/humble/setup.bash" >> .bashrc
    pip install --user -U empy pyros-genmsg setuptools
    ```

5. **Set Up Micro XRCE-DDS Agent & Client**
    ```bash
    cd ~
    git clone https://github.com/eProsima/Micro-XRCE-DDS-Agent.git
    cd Micro-XRCE-DDS-Agent
    mkdir build
    cd build
    cmake ..
    make
    sudo make install
    sudo ldconfig /usr/local/lib/
    ```

6. **Build the ROS 2 Workspace**
    ```bash
    mkdir -p ~/ws_guardian_eagle/src/
    cd ~/ws_guardian_eagle/src/
    git clone https://github.com/PX4/px4_msgs.git
    git clone https://github.com/PX4/px4_ros_com.git
    cd ..
    source /opt/ros/humble/setup.bash
    colcon build
    ```

7. **Install MAVSDK and Dependencies**
    ```bash
    pip install mavsdk aioconsole pygame numpy opencv-python
    sudo apt install ros-humble-ros-gzgarden
    ```

8. **Install YOLOv8**
    ```bash
    pip install ultralytics
    ```

### Additional Configuration
- Add the following lines to your `.bashrc`:
    ```bash
    source /opt/ros/humble/setup.bash
    export GZ_SIM_RESOURCE_PATH=~/.gz/models
    ```
- Copy the models from the main repository to `~/.gz/models`
- Copy `default.sdf` from the worlds folder in the main repository to `~/PX4-Autopilot/Tools/simulation/gz/worlds/`
- Adjust the drone's camera angle:
    ```bash
    # Edit the file at ~/PX4-Autopilot/Tools/simulation/gz/models/x500_depth/model.sdf and change the <pose> tag on line 9 from:
    <pose>.12 .03 .242 0 0 0</pose>
    to:
    <pose>.15 .029 .21 0 0.7854 0</pose>
    ```

## Running GuardianEagle

### Flying Using Keyboard Controls
Open multiple terminal windows:
```bash
Terminal #1:
cd ~/Micro-XRCE-DDS-Agent
MicroXRCEAgent udp4 -p 8888

Terminal #2:
cd ~/PX4-Autopilot
PX4_SYS_AUTOSTART=4002 PX4_GZ_MODEL_POSE="268.08,-128.22,3.86,0.00,0,-0.7" PX4_GZ_MODEL=x500_depth ./build/px4_sitl_default/bin/px4

Terminal #3:
ros2 run ros_gz_image image_bridge /camera

Terminal #4:
source ~/guardian-eagle-venv/bin/activate
cd ~/GuardianEagle
python uav_camera_det.py

Terminal #5:
source ~/guardian-eagle-venv/bin/activate
cd ~/GuardianEagle
python keyboard-mavsdk-test.py
```
In the last terminal, a blank window will open for keyboard inputs. Focus on that window by clicking on it, then press "r" to arm the drone, and use WASD and arrow keys for flying, and "l" for landing.

### Flying Using ROS 2
Open multiple terminal windows:
```bash
Terminal #1:
cd ~/Micro-XRCE-DDS-Agent
MicroXRCEAgent udp4 -p 8888

Terminal #2:
cd ~/PX4-Autopilot
PX4_SYS_AUTOSTART=4002 PX4_GZ_MODEL_POSE="283.08,-136.22,3.86,0.00,0,-0.7" PX4_GZ_MODEL=x500_depth ./build/px4_sitl_default/bin/px4

Terminal #3:
ros2 run ros_gz_image image_bridge /camera

Terminal #4:
source ~/guardian-eagle-venv/bin/activate
cd ~/GuardianEagle
python uav_camera_det.py

Terminal #5:
cd ~/ws_guardian_eagle
source /opt/ros/humble/setup.bash
source install/local_setup.bash
ros2 run px4_ros_com offboard_control
```

## Acknowledgements
- [PX4 Autopilot](https://github.com/PX4/PX4-Autopilot)
- [Ultralytics YOLO](https://github.com/ultralytics/ultralytics)
- [ROS](https://www.ros.org/)
- [Gazebo](https://gazebosim.org/)

---
