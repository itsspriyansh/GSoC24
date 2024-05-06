# Project Details and Specifications :dart:

> This section is about all the proposed problems, their proposed solutions and technical requirements. This section will be constantly updated until we reach down to the most optimal solution to the best of our knowledge.

## What problem are we solving?

Currently mobile-helper-tool sets up Android SDK with all the required binaries and installs emulator for pixel-5. It also installs chromedriver and firefox for the developers who wish to perform testing on the emulator.

Now we have identified some problems and proposed some use cases that we wish to implement in the tool to address the problems. These new features will improve usability of the tool and increase its reach among wider community. 

The following are the currently identified problems:

### 1. Unavailability of the ability to connect a real device wirelessly with the computer.

Users can connect their device with their computer using a USB cable. However, this can be inconvenient for some users in some circumstances. Some users might like to connect their devices wirelessly for their specific requirements. We wish to add a workflow allowing users to connect their device wirelessly without any physical connection whatsoever. This will improve accessibility of the users.

### 2. Launching emulators and Managing multiple devices (real and emulator) is currently not possible with the current implementation of the tool.

There are a lot of possible scenarios where developers would like to connect and manage multiple devices (real and emulator). The tool currently offers no such functionality to facilitate this requirement. We plan to create a workflow such that users can connect their devices, launch multiple emulators, view all the connected devices and disconnect any specific device they wish to. This improves the usability of the device to a great extent.

### 3. Searching the location of each binary to execute their commands is a hassle.

The SDK offers a wide range of commands that users can execute depending on their requirements. We are planning to create several workflows for the most popular SDK commands. However, creating workflows for all the use cases covering all the available commands is not practically possible in a limited time period. The tool might cover the majority of the available commands in the future but as for now an alternative approach has to be developed to avoid the hassle of searching the locations of the binaries and improving user experience.

### 4. Tool installs only a specific system images and emulator

The Android SDK offers a huge number of emulator devices that are not only mobile devices, but also android TVs and smart watches. There would be users/developers across the world who wish to test or try using any specific emulator depending on their requirements. The mobile-helper-tool currently sets up an emulator for pixel-5. To increase the reach and usability of the tool, we wish to add support for installing multiple emulators and corresponding system images.

### 5. Once the SDK is installed, there is no support to update the tools.

There are constant improvements in tools and technologies like upgradation of API levels, bug fixes, improvements in command line tools etc. We need to keep our tools up to date and hence this use case is very crucial. The tool currently installs the SDK and emulator but there is no support for keeping a check on the latest updates and updating anything according to our requirements.

## What are the requirements and constraints of the problem?

We can divide our requirements into three sub categories: critical, important and nice-to-have.

### Requirements

- **Cricital Requirements**
    - Ability to connect to a device wirelessly using ADB.
    - Ability to launch a specific emulator device.
    - Installation of various system images.
    - Installation of various emulator devices.
    - Showing a list of available emulators and their corresponding system images.
    - Showing a list of available updates for SDK tools and installing any update.

- **Important Requirements**
    - Showing a list of all the connected devices.
    - Ability to disconnect a connected device.
    - Ability to shut down an emulator.
    - Ability to run various binaries from the tool itself.
    - Showing a list of all the installed system images.
    - Ability to uninstall system images and emulators.

- **Nice to have Requirements**
    - Ability to disconnect all the connected devices.
    - Ability to update all SDK tools at once.

### Constraints

- ADB wireless connection with pairing code is available only with android version 11 (API level 30) or higher.

- There are stages when the terminal gets updated with new logs after a command is run (eg. `./emulator -avd nightwatch-android-11` shows new logs when we do something with the emulator). It will be difficult to show those logs when running the commands from the tool.

## What are the possible solutions for solving these problems?

### Iteration 1

- Workflow 1
    
    - Users will run a sub command `wireless` with either of the three flags `--connect`, `--disconnect` and `--show`.
    
    - Upon passing the `--connect` flag, script for connecting the device will be triggered and users will be shown the instructions to proceed.
    
    - Upon passing the `--disconnect` flag, users will be shown a list of all the connected devices along with a **disconnect all** option. Selecting an option will trigger the script to disconnect the specific device.

    - Upon passing the `--list` flag, users will be shown a list of all the connected real devices.

- Workflow 2 

    - Users will run a subcommand `emulator` with either of these available options: `--connect`, `--disconnect` and `--list` for this workflow.

    - `--connect` flag will show a list of all the available emulators and upon choosing an option will launch the emulator.

    - `--disconnect` flag will show a list of all the connected emulators along with a **disconnect all** option and upon choosing an option will shut down the specific emulator.

    - `--list` will show a list of all the connected emulators.

        > *Question: Would this be a good idea to create these `emulator` and `wireless` subcommands? My thought process behind this was to ensure we have minimum number of unique flags and since both emulator and real device have similar start/stop or connect/disconnect and list features, we could just cover everything related to a specific device in a subcommand and then corresponding options will be common for both the device. This will also recduces number of options for installation of emulator and system images since we can just use `emulator --install` and `image --install` rather than creating more flag like `--emulator` and `--system-image` to work with related flags like `--install` and `--uninstall`. This will also be helpful for making future additions to the tool where we might need to cover more workflows inside any previously created subcommand.*

- Workflow 3

    - Users will run the same subcommand `emulator` as above. Here it comes with two more options: `--install` and `--uninstall`.

    - `--install` will show a list of all the available emulators that user can install. Upon selecting an emulator device, a list of all the corresponding system images will be shown. Upon selecting a system image, the installation for both the system image and the emulator will initiate.

    - `--uninstall` will show the list of all the currently installed emulators. Upon selecting an emulator, it will be uninstalled from the computer.

        > *Question: Shall we rather list down all the present emulators and list of system images in an interfaces (Hardcoded in the codebase) instead of trying to extract the details from `sdkmanager` command output? Because we know that the list of available emulators is not going to be dynamic and will mostly contain fixed items with fixed corresponding system images that we should exactly know.*

        > *Question: Upon uninstalling an emulator, the corresponding system image, if not used anymore, might also require to be deleted. Shall we prompt the user if they want to delete the corresponding system image? Can we keep a track whether any system image is used by any other device as well even though one device is uninstalled?*

- Workflow 4

    - Users can use the `--update` flag to check a list of all the available updates provided by the SDK manager.

    - Users can either choose any specific update or just select the **update all** option to install all the available updates.

- Workflow 5

    - Users can use nightwatch's centralized CLI for running any of the installed binaries.

    - Results genereted by the binaries will shown in the nightwatch CLI itself.

        > *As earlier discussed, this appears a more optimal and natural approach to me. Allowing users to run the commands from the same nightwatch CLI is better way of solving this problem then trying to set up the `PATH` variable or do something else. This approach is easy and straightforward from user point of view as well.*

