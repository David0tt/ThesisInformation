# Working with the Franka Emika Panda Robot Arm

## ⚠️ SAFETY INFORMATION ⚠️
The robot arm is not a toy! To prevent bodily harm and damages, make sure to use it safely and responsibly.

In particular, there are two emergency buttons:

- `Red Emergency Power-Off Button`: emergency power-off. This completely shuts down the robot arm. After such a shutdown, joint calibrations may need to be checked again. Only use this if the black software stop button does not suffice.
- `Black User Control Button / Software Stop Button`: software stop. This halts all control of the robot arm and allows manual movement (hold the two guiding buttons next to the gripper and move the arm). Always stay close to this button, especially when running previously untested code.

After use, always shut down the arm cleanly using the shutdown procedure below. Do not just hit the power-off button.


## Using the Web Interface

### Starting and Connecting
- Unlock the red power button
    - If the power button has been left in an “on” state after shutdown for a long time, the robot may not start immediately because the safety capacitors are still loaded. In that case: press the power button to cut power, wait ~20 seconds for the capacitors to unload, then unlock the power button again and start up
- Unlock the black user control button
- Wait until the robots LED has stopped blinking yellow and shows static yellow, indicating that the locks are engaged
- Open a browser and connect to the robot IP (`https://172.16.0.2/desk`). I recommend setting a bookmark.
    - Use Chrome for this; if you get a certificate warning click `Advanced` -> `Proceed to 172.16.0.2 (unsafe)`. Firefox does not work since it does not allow ignoring expired certificates.
    - If login is required use User: `admin`, Password: `pandabaer!`
- The Panda web interface should be visible


### Guiding mode (to manually move the robot arm)
- Joints have to be unlocked
- Press the user control button
- Press and hold the two guiding buttons to the side of the gripper to freely move the robot. Make sure to not press them too strongly, or the `panic-mode` also stops robot movement.
- You can change the guiding mode under `Guiding Mode` in the web interface, or by pressing the button on the top side of the gripper
- `Pilot Mode` switches between controlling the arm and controlling the gripper

### Enabling FCI (Franka Control Interface)
- Unlock the joints in the web interface
- In the menu, click `Activate FCI`
- As long as the pop-up is open, FCI is active

### Shutting Down the Panda
- Press the user control button
- Lock joints
- Burger Menu -> Shutdown
- When the shutdown is finished and the fans have stopped, press the Red Power-Off Button


## Using Command Line Scripts
Since all the commands above in the web interface simply use HTTP requests, we can reverse engineer them and run them from a script. This is implemented in [franka_lock_unlock.py](franka_lock_unlock.py)

Most common usage (unlocking the joints and exposing FCI):

     ./franka_lock_unlock.py -u -l -w -p -c -i 172.16.0.2 admin pandabaer!

Shut down the Robot:

     ./franka_lock_unlock.py -s 172.16.0.2 admin pandabaer!


## Shared Student Users for Lab PCs
The PCs in the robot lab provide a shared student user account. Login information can be provided on request. In general, prefer individual user accounts over shared users.

## User Setup
- To use the Panda arm, the user should be in the `realtime` group
- To use Docker, the user should be in the `docker` group
- Group membership can be edited with:

```bash
sudo nano /etc/group
```

## Controlling the Panda

### franka_pipeline (Recommended)
I have developed the [franka_pipeline](https://github.com/David0tt/franka_pipeline) package for fast iteration in our robotic setup.
To build (run from the root of the current ThesisInformation repository):

    DOCKER_BUILDKIT=1 docker build --ssh default -t frankapipeline docker/docker_frankapipeline
    docker build -t deoxys_autostart docker/docker_deoxys/

Start up the robot, unlock the joints, and enable FCI (see the sections above). Then run the containers for deoxys backend and franka_pipeline frontend:

    docker run -it --rm -v /cshome:/cshome -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix --net=host --privileged deoxys_autostart
    docker run -it --rm --runtime=nvidia --gpus all -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix -v /home/ott/datasets/:/datasets --net=host --privileged frankapipeline

Open a terminal in the frankapipeline container (either using the existing one, vscode remote extension or starting a new terminal with `docker exec -it [CONTAINER_ID] bin/bash`). In it, you should now be able to run

    python main.py # This should allow remote control with a SpaceMouse in simulation
    python main.py --real-robot # This should allow remote control with a SpaceMouse on the real robot
    python main.py --osc-demo --real-robot # If no SpaceMouse is available    

Always stay close to the emergency buttons when running new code!
For further instructions refer to the README of [franka_pipeline](https://github.com/David0tt/franka_pipeline). 


### Deoxys
For many applications it is sufficient to use [deoxys](https://github.com/UT-Austin-RPL/deoxys_control/) as a lightweight control stack. This repository provides a containerized installation. First, build the container:

    docker build -t deoxys docker/docker_deoxys/

Start up the robot, unlock the joints, and enable FCI (see the sections above). Then run the container:

    docker run -it --rm -v /cshome:/cshome -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix --net=host --privileged deoxys


If everything works, a `tmux` session with three panes should open; the right panes should show green info messages from deoxys auto-scripts. Switch to the free pane by pressing `ctrl+b` then `left arrow` and run an example:

    python examples/reset_robot_joints.py
    python examples/osc_control.py

Always stay close to the emergency buttons when running new code! You can now try all the examples in `examples/`. You can start your development based on deoxys, either in this container, by building upon this container, or in a different environment where you just build the deoxys Python frontend. A nice property of the container is that you can use it standalone for controlling the arm using the deoxys "server-side", and use the deoxys client-side in a different environment (e.g. another container or conda environment) without building the whole deoxys (i.e. building deoxys with only `make -j build_deoxys=1`). An example of this pattern can be seen in the [franka_pipeline](https://github.com/David0tt/franka_pipeline) package.

(If you are doing something in the containers, make sure to keep your work saved, or potentially run the container without `--rm`)


# Franka Emika Panda Overview
## Background and References

For a general overview of the web interface and Franka Emika packages:
https://www.youtube.com/watch?v=TRMIA2J29MA&list=PL9FoYHNFGS3TyHsLcL0-qNIi-e14Xw7np

The Panda is superseded by the Franka Research 3. Some newer stacks (e.g. `franka_ros2`) do not support the Panda. Franka Emika considers the Panda end-of-life, so workarounds may be necessary.


### Frameworks for controlling the Panda
There are different frameworks for controlling the Panda; the right choice depends on the application.

The choices for control frameworks generally are `Libfranka`, `Deoxys` and `ROS / franka_ros`, but they interact with various other packages.

- `Libfranka`: a low-level C++ library that directly interfaces with FCI. Use this if latency is critical. It provides low-level controllers and state only; higher-level tooling is typically added on top.
- `Deoxys`: a Python package exposing simple joint and Cartesian controllers. Good for lightweight Python-first workflows. For most ML (RL / VLA) applications this is generally the recommended choice.
- `franka_ros / franka_ros2`: integrates the robot into ROS. ROS provides a large ecosystem (motion planning, perception, etc.) but comes with significant complexity. For the Panda specifically, `franka_ros2` support is not official and may rely on community workarounds (e.g. https://github.com/boschresearch/franka_ros2).

- `FCI (Franka Control Interface)`: low-level communication protocol used by Libfranka to communicate with the robot arm.


## Camera Holder
A wrist camera holder can be 3D printed from the following `.stl` file:

https://github.com/lagadic/visp_ros/blob/master/tutorial/franka/real-robot/franka-rs-D435-camera-holder.stl
