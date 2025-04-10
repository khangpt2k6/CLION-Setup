# CLion Setting for Docker on multiple platforms

This guide provides step-by-step instructions for setting up JetBrains CLion with Docker for ROS Melodic development environments, enabling consistent build environments and simplified deployment.

## Prerequisites

- [CLion](https://www.jetbrains.com/clion/download/) installed on your system
- [Docker](https://docs.docker.com/get-docker/) installed and running
- Basic familiarity with ROS and Docker concepts

## Step 1: Create a Dockerfile

Create a file named `Dockerfile` in your project directory with the following content:

```dockerfile
FROM ros:melodic

# Install required tools for development and debugging
RUN apt-get update && apt-get install -y \
    cmake \
    gdb \
    git \
    && rm -rf /var/lib/apt/lists/*

# Set up the workspace directory
WORKDIR /root
RUN mkdir .ssh
RUN mkdir -p ~/catkin_ws/src

# Set working directory to the catkin workspace
WORKDIR /root/catkin_ws

# Add ROS environment setup to bashrc
RUN echo "source /opt/ros/melodic/setup.bash" >> /root/.bashrc

# Default command
CMD ["bash"]
```

## Step 2: Build the Docker Image

Build your Docker image by running the following command in the terminal from the directory containing your Dockerfile:

```bash
docker buildx build --no-cache --tag=ros-melodic .
```

Verify the image was created successfully:

```bash
docker images | grep ros-melodic
```

## Step 3: Configure CLion to Use Docker

1. Open CLion and navigate to **Settings | Build, Execution, Deployment | Toolchains**
2. Click the **+** button and select **Docker**
3. Enter a name for your Docker toolchain (e.g., "ROS Melodic Docker")
4. In the Docker dropdown, select the appropriate Docker connection
5. For the Docker image, select your image

## Step 4: Configure Container Settings

1. Click the gear icon next to the Docker image selection
   ![image](https://github.com/user-attachments/assets/9fe95719-1824-4359-a997-aaa1fbeb1d38)

2. Configure container settings:
   - **Container name**: Provide a name for your container (e.g., `ros-melodic-container`)
   - **Run options**: Add necessary Docker run options, such as:
     ```
     -it --net=host --rm
     ```
   - Add any required environment variables by clicking the **+** button in the Environment variables section

## Step 5: Configure CMake and Debugger

1. In the toolchain configuration, ensure:
   - **CMake**: Docker CMake
   - **Make**: Docker Make
   - **C Compiler**: Docker GCC
   - **C++ Compiler**: Docker G++
   - **Debugger**: Docker GDB

2. CLion should automatically detect the versions of all tools from the Docker container

3. Click **Apply** → **OK** to save the configuration
   
![image](https://github.com/user-attachments/assets/6dcb88ab-5dd2-484c-850e-01077fd3c923)
## Step 6: Set Up a CMake Project

1. In CLion, go to **File** → **New Project**
2. Select **C++ Executable** or **CMake Project** depending on your needs
3. Set your project location and name
4. Ensure your newly created Docker toolchain is selected
5. Click **Create**

## Step 7: Working with the ROS Workspace

After setting up your project, you can:

1. Run `catkin_make` directly from CLion's terminal to build your ROS packages
2. Edit, build, and debug your ROS code directly in CLion
3. Execute ROS commands within the Docker container

## Step 8: Accessing Container Files in CLion

1. Open the Services tool window by:
- Pressing Alt+\ to open the Main Menu, then select View → Tool Windows → Services
- Or use the shortcut Alt+8 (Windows/Linux) or ⌘8 (macOS)

2. In the Services panel, expand the Docker section
3. Navigate to the Containers list and find your ROS Melodic container
4. Right-click on your container (in this case "ros-melodic-container") and select Browse Files
5. CLion will open a file browser showing the complete file system of your Docker container


