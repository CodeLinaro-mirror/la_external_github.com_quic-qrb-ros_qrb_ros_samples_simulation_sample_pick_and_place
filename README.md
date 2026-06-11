## Simulation Sample Pick and Place

## 👋 Overview

- The RML-63 Robotic Arm pick-and-place demo is a C++-based ROS 2 node that demonstrates autonomous pick-and-place operations using MoveIt 2 for motion planning and Gazebo for physics simulation.

![image-20250723181610392](./resource/pick_and_place_architecture.jpg)

| Node Name                                                    | Function                                                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| qrb_ros_simulation | Sets up the Qualcomm robotic simulation environment. See qrb_ros_simulation. |
| qrb_ros_arm_pick_place     | Defines pick-and-place positions with ROS 2 `launch.py` configuration parameter support. |


## 🔎 Table of contents

- [Simulation Sample Pick and Place](#simulation-sample-pick-and-place)
- [👋 Overview](#-overview)
- [🔎 Table of contents](#-table-of-contents)
- [⚓ Used ROS Topics](#-used-ros-topics)
- [🚀 Usage](#-usage)
- [👨‍💻 Build from source](#-build-from-source)
- [❔ FAQs](#-faqs)
- [📜 License](#-license)

## ⚓ Used ROS Topics

| ROS Topic                       | Type                                          | Description                    |
| ------------------------------- | --------------------------------------------- | ------------------------------ |
| `/joint_states`                   | `<sensor_msgs/msg/JointState> `                   |       Real-time joint position, velocity, and effort data for all robot joints              |
| `/hand_controller/controller_state` | `<control_msgs.msg.ControllerState>` |  Current state and status information of the gripper controller |
| `/hand_controller/joint_trajectory` | `<trajectory_msgs.msg.JointTrajectory>` |       Trajectory commands sent to gripper joints for motion execution |
| `/rm_group_controller/controller_state` |     `<control_msgs.msg.ControllerState>` |  Current state and status information of the robotic arm controller |
| `/rm_group_controller/joint_trajectory` |     `<trajectory_msgs.msg.JointTrajectory>` |       Trajectory commands sent to arm joints for motion execution |
| `/robot_description` |        `<std_msgs.msg.String>` |       URDF robot description in XML format for robot modeling and visualization |
| `/robot_description_semantic` |       `<std_msgs.msg.String>` |       SRDF semantic robot description for MoveIt planning and configuration |

## 🚀 Usage

The following steps assume a `ROS 2 Jazzy` + `Gazebo` environment. Follow the official installation docs to install ROS 2 Jazzy and Gazebo. You can also use qrb_ros_simulation to launch a Docker environment and run the steps.

<details>
  <summary>Out-of-box usage details</summary>

1. Launch simulation environment on the HOST docker container.

- If you launch the simulation on a different host, use the same `ROS_DOMAIN_ID` to ensure devices can communicate via ROS.

- You can launch Gazebo with the following command:
  ```bash
  source install/setup.bash
  export ROS_DOMAIN_ID=55
  ros2 launch qrb_ros_sim_gazebo gazebo_rml_63_gripper.launch.py world_model:=warehouse initial_x:=2.2 initial_y:=-2 initial_z:=1.025 initial_yaw:=3.14159 initial_pitch:=0.0 initial_roll:=0.0 use_sim_time:=true
  ```

- After the world loads, click `Play` in Gazebo. Open a new terminal to launch the controller:
  ```bash
  source install/setup.bash
  export ROS_DOMAIN_ID=55
  ros2 launch qrb_ros_sim_gazebo gazebo_rml_63_gripper_load_controller.launch.py
  ```

- After Gazebo starts in the host Docker container, run the pick-and-place node on the device.

2. Launch MoveIt 2 and the demo to start the arm motion
    ```bash
    source /opt/ros/jazzy/setup.bash
    export ROS_DOMAIN_ID=55
    ros2 launch simulation_sample_pick_and_place simulation_sample_pick_and_place.launch.py
    ```

- When the node starts, you should see a log beginning with `[move_group-1] You can start planning now!`. In another terminal, start the pick-and-place node:
  ```bash
  source /opt/ros/jazzy/setup.bash
  export ROS_DOMAIN_ID=55
  ros2 run simulation_sample_pick_and_place qrb_ros_arm_pick_place
  ```

- You can then view the arm executing the pick-and-place operation in Gazebo.

</details>

<details>
  <summary>Build from source usage details</summary>

## 👨‍💻 Build from source

1. Source code is located at `sources/quic-qrb-ros/qrb_ros_samples/simulation_sample_pick_and_place/` in the downstream Ubuntu workspace.

2. Install build dependencies:
   ```shell
   ros-jazzy-moveit
   ```

3. Build the package:
   ```shell
   cd build-utils/ubuntu/
   python3 build.py --gen-debians --package ros-jazzy-simulation-sample-pick-and-place
   ```

4. Built `.deb` files are output to:
   ```
   <workspace>/debian_packages/oss/ros-jazzy-simulation-sample-pick-and-place/
   ```

5. Copy the `.deb` file to the target device:
   ```shell
   scp <workspace>/debian_packages/oss/ros-jazzy-simulation-sample-pick-and-place/ros-jazzy-simulation-sample-pick-and-place_*.deb <user>@<device-ip>:~
   ```

6. Install the `.deb` package on the target device:
   ```shell
   sudo apt install ./ros-jazzy-simulation-sample-pick-and-place_*.deb
   ```

7. Refer to `Out-of-box usage details` to run the pick-and-place node.

</details>

## ❔ FAQs
How do I move the Coke can to the origin pose?
You can execute the following command to move the Coke can to the origin pose:
```bash
gz service -s /world/warehouse/set_pose --reqtype gz.msgs.Pose --reptype gz.msgs.Boolean --timeout 1000 --req 'name: "coke1", position: { x: 3, y: -2.0, z: 1.02 }, orientation: { x: 0.0, y: 0.0, z: 0.0, w: 1.0 }'
```

## 📜 License

Project is licensed under the [BSD-3-Clause-Clear](https://spdx.org/licenses/BSD-3-Clause-Clear.html) License.
