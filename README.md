# uros_rtps
## Setup
- Hardware: NUCLEO-F767ZI
- Host: Ubuntu_22.04 & ROS2_Humble
## Steps to Build and Run
1. Clone the [micro_ros_stm32cubemx_utils](git@github.com:micro-ROS/micro_ros_stm32cubemx_utils.git) under STM32CubeIDE workspace. This is my preference to share it across multiple projects in the workspace.
2. `docker pull microros/micro_ros_static_library_builder:humble`
3. `docker run --rm -v ./project --env MICROROS_USE_EMBEDDEDRTPS=ON --env MICROROS_LIBRARY_FOLDER=micro_ros_stm32cubemx_utils/microros_static_library_ide microros/micro_ros_static_library_builder:humble`
4. Browse to `./micro_ros_stm32cubemx_utils/microros_static_library_ide/libmicroros` and check for libmicroros.a and /include folder & files.
5. Back under STM32CubeIDE workspace, clone this repository.
6. Open STM32CubeIDE and import both repos into the workspace. micro_ros_stm32cubemx_utils has to remain open while building this project. The project settings are such that micro_ros_stm32cubemx_utils will be looked for in the workspace.
8. Update the IP addresses and subnet mask in MX_LWIP_Init() of this project under LWIP->App->lwip.c
9. Build and flash the dev kit.
10. In a terminal, source ros2 setup.bash and list topics `ros2 topic list`
11. Subscribe to cubemx_publisher `ros2 topic echo cubemx_publisher` if available

## Observations
1. When the topic echo appears to be working at the terminal, Wireshark captures HEARTBEAT, INFO_TS, and topic DATA sent from STM to the host. Sometimes, the topic DATA comes in sporadic bursts.
2. Sometimes cubemx_publisher topic never becomes available. In this case, Wireshark shows that the host continuously sends ACKNACK and HEARTBEAT messages to STM and STM only sends INFO_TS and DATA(p) to 239.255.0.1.
3. Trying to run a second subscriber always breaks STM. In this case, the capture shows a gradual degradation in STM's communication and eventually 'destination unreachable' condition.
4. Testing with a rounter connected to the Internet, host wirelessly connected to the router (and STM wired) shows considerably worse performance than the host and STM connected to a switch without Internet connection.

