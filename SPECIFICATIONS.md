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

    > We don't need to support the devices below that, atleast through automation. *~comment from @garg3133*

- There are stages when the terminal gets updated with new logs after a command is run (eg. `./emulator -avd nightwatch-android-11` shows new logs when we do something with the emulator). It will be difficult to show those logs when running the commands from the tool.

    > we can push those logs into a separate file if the user asks for it by passing an appropriate flag, like `--log_dump <filename>`. *~comment from @garg3133*

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

### Iteration 2

- Ideas on how documentation can be improved from users' POV.
    
    - **Providing troubleshooting steps:** A lot of users find difficulty when something breaks while running any command from the tool. Since mobile-helper is an automation tool, most errors can be resolved by running commands manually. We should provide the steps for manually performing a process. This will come to great use and will save a lot of time for users stuck with errors.

    - **Visual Aids:** We can create flow charts showing the entire process of a workflow. For eg:
    ![image](./assets/image5.png)
    
        > The above shown image is just an example and doesn't represent any proposed flow for the tool.

    - **Making imporvements with user feedback:** From the discord community forum, we can see how users are interacting with our tool, what difficulties they are facing and how much is our documentation helpful to them. We can iteratively add improvements to the documentation.

- Ideas on how certain parts of documentation can be made more accessible to the users.

    - **Inline documentation and CLI usage instructions:** Providing important details directly inside the source code can make it more accessible for users. Some important details can also be highlighted along with the CLI usage instructions.

    - **Searchable documentation:** Since Nightwatchjs already has its website with optimized search capabilities, we can expose our documentation there to make it more accessible.

## Implementation

The implementation can be broken into there parts:
    
- Writing scripts for automating the SDK workflows.

- Creating commands for users to trigger the mobile-helper workflows.

- Building the integration (mobile-helper workflow) between the user input command and the final automation script.

The following are the SDK commands according to their priority for this project:

- Cricital Commands

```bash
# to pair and connect a real device
adb pair <device-ip>:<pairing-port> <pairing-code>

adb connect <device-ip>:<port>

# to launch an emulator
emulator -avd <name-of-emulator>

# to list all the SDK tools and system images available for download
sdkmanager --list

# to install the SDK tools and system images
sdkmanager <name-of-package>

# to list all the available devices
avdmanager list devices

# to list all the installed AVDs
avdmanager list avd

# to install the AVD
avdmanager create avd --name <name-of-avd> --package <system-image> --device <name-of-device>

# to list available updates
sdkmanager --list --include_obsolete

# to update a package
sdkmanager <name-of-package>
```

- Important Commands

```bash
# to list all the currently running devices
adb devices

# to disconnect a real device
adb disconnect <device-id>

# to stop an emulator
adb -s <emulator-id> emu kill

# to delete an AVD
avdmanager delete avd --name <name-of-avd>

# to uninstall a SDK tool or system image
sdkmanager --uninstall <name-of-package>
```

- Nice to have Commands

```bash
# to update all the packages
sdkmanager --update

# to stop all the emulators and disconnect all the devices
adb kill-server
```

The following steps will be encountered with the execution of the mobile-helper workflow:

- `AndroidSetup` class instance is created and the main `run()` function is called.

- Environment is verified. The steps include:
    - Checking Java installation
    - Checking the presence of `ANDROID_HOME` and `JAVA_HOME` in `process.env` and loading other environment variables to `process.env`

- After checking the environment, the control flow diverges from the main script and shifts to the execution of the desired SDK script.
