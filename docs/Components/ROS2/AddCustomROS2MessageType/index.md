# Add custom ROS2 message type

## ROS2 For Unity

AWSIM uses [Ros2 For Unity](https://github.com/RobotecAI/ros2-for-unity) module for ROS2 communication. `ROS2 For Unity` is a high-performance communication solution to connect Unity3D and ROS2 ecosystem in a ROS2 "native" way. 

This document briefly guides you on how to add your custom messages to the simulation. For a detailed description, please see [ROS2 For Unity Readme](https://github.com/RobotecAI/ros2-for-unity/blob/master/README.md).

## Prerequisites

`ROS2 For Unity` depends on a `ros2cs` - a C# .NET library for ROS2. You don't have to install it manually, but there are a few prerequisites that must be resolved first.

Please refer to the following prerequisites section for your OS:

=== "Ubuntu"
    [https://github.com/RobotecAI/ros2cs/blob/master/README-UBUNTU.md#prerequisites](https://github.com/RobotecAI/ros2cs/blob/master/README-UBUNTU.md#prerequisites)

=== "Windows"
    [https://github.com/RobotecAI/ros2cs/blob/1.2.0/README-WINDOWS.md#prerequisites](https://github.com/RobotecAI/ros2cs/blob/1.2.0/README-WINDOWS.md#prerequisites)

Additionall required tools:

- Git
- Shell:
    
    === "Ubuntu"
        `bash` for Ubuntu

    === "Windows"
        `powershell` for Windows 10 

## Build custom messages

To include a custom ROS2 message, you must build `ROS2 For Unity` with your custom message package. The following steps will assume that:

- the temporary `ROS2 For Unity` build folder is located in the home directory `~/` (Ubuntu) or on a `C:\` drive (Windows)
- your message package is called `custom_msgs` and is located in `~/custom_msgs` (Ubuntu) or `C:\custom_msgs`
- ROS2 version is `galactic` (`foxy` and `humble` are also supported, please check the current AWSIM version for more information about its ROS2 version) and is located in

    === "Ubuntu"
        `/opt/ros/galactic`

    === "Windows"
        `C:\ros2_galactic`

### Prepare workspace

1. Get `ROS2 For Unity` repository. 
    
    ```
    git clone https://github.com/RobotecAI/ros2-for-unity
    ```

1. Pull repositories

    === "Ubuntu"
        ```bash
        cd ~/ros2-for-unity
        . /opt/ros/galactic/setup.bash
        ./pull_repositories.sh
        ```

    === "Windows"
        ```powershell
        cd C:\ros2-for-unity
        C:\ros2_galactic\local_setup.ps1
        .\pull_repositories.ps1
        ```

### Setup `custom_msgs` package

1. If the package is hosted on a git repository:
    1. open `ros2-for-unity/ros2_for_unity_custom_messages.repos` file in your favorite editor,
    1. modify the file contents, so it points to `custom_msgs` repository. The final file should look similar to (change `<REPOSITORY_URL>` to repository address, `<BRANCH_NAME>` to desired branch and `<PACKAGE_NAME>` to your package name - in this case `custom_msgs`):
        
        ```
        # NOTE: Use this file if you want to build with custom messages that reside in a separate remote repo.
        # NOTE: use the following format
            
        repositories:
            src/ros2cs/custom_messages/<PACKAGE_NAME>:
                type: git
                url: <REPOSITORY_URL>
                version: <BRANCH_NAME>
        ```
    1. pull repositories:
        
        === "Ubuntu"
            ```bash
            ./pull_repositories.sh
            ```

        === "Windows"
            ```powershell
            .\pull_repositories.ps1
            ```

1. If the package is located on your hard-disk:
    1. copy `custom_msgs` package to `src/ros2cs/custom_messages`
        
        === "Ubuntu"
            ```bash
            cp -r ~/custom_msgs ~/ros2-for-unity/src/ros2cs/custom_messages/
            ```

        === "Windows"
            ```bash
            cp -r ~/custom_msgs ~/ros2-for-unity/src/ros2cs/custom_messages/
            ```

### Build ROS2 For Unity

1. Build ROS2 For Unity

    === "Ubuntu"
        ```
        ./build.sh --standalone
        ```

    === "Windows"
        ```
        .\build.ps1 -standalone
        ```

### Install `custom_msgs` to AWSIM

New ROS2 For Unity build, which you just made, contains multiple libraries that already exist in the AWSIM. To install `custom_msgs` and not copy all other unnecessary files, you should get the `custom_msgs` related libraries only and copy them to the analogous directories in `AWSIM/Assets/Ros2ForUnity`.

You can find them in following directories:

- `ros2-for-unity/install/asset/Ros2ForUnity/Plugins` which names matches `custom_msgs_*`

=== "Ubuntu"
    ```
    `ros2-for-unity/install/asset/Ros2ForUnity/Plugins/Linux/x86_64/` which names matches `libcustom_msgs_*`
    ```

=== "Windows"
    ```
    `ros2-for-unity/install/asset/Ros2ForUnity/Plugins/Windows/x86_64/` which names matches `custom_msgs_*`
    ```

To automate the process, you can use these commands (change `<AWSIM_DIR>` to your E2Simulator path and `<CUSTOM_MSGS_PACKAGE_NAME>` to a custom messages package):

=== "Ubuntu"
    ```
    find ~/ros2-for-unity/install/asset/Ros2ForUnity/Plugins -maxdepth 1 -name "<CUSTOM_MSGS_PACKAGE_NAME>*"    -type f -exec cp {} <AWSIM_DIR>/Assets/Ros2ForUnity/Plugins \;
    find ~/ros2-for-unity/install/asset/Ros2ForUnity/Plugins/Linux/x86_64 -maxdepth 1 -name     "lib<CUSTOM_MSGS_PACKAGE_NAME>*" -type f -exec cp {} <AWSIM_DIR>/Assets/Ros2ForUnity/Plugins/Linux/x86_64 \;
    ```

=== "Windows"
    ```
    Get-ChildItem C:\ros2-for-unity\install\asset\Ros2ForUnity\Plugins\* -Include @('<CUSTOM_MSGS_PACKAGE_NAME>*') | Copy-Item -Destination <AWSIM_DIR>\Assets\Ros2ForUnity\Plugins
    Get-ChildItem C:\ros2-for-unity\install\asset\Ros2ForUnity\Plugins\Windows\x86_64\* -Include @('<CUSTOM_MSGS_PACKAGE_NAME>*') | Copy-Item -Destination <AWSIM_DIR>\Assets\Ros2ForUnity\Plugins\Windows\x86_64
    ```