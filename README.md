## Dodo Alive! Team 6: Tron Locomotion
This work was created during the course phase of "Dodo Alive!", a Master-Lab @ Technical University of Munich with the goal to explore the locomootion of the Tron1 robot by LimX Dynamics. They already provide a locomotion policy with their device but it was trained on IsaacGym. We wanted to translate their work into IsaacLab and train our own policy

The idea was to use an existing IsaacLab template of the locomotion task from rls_rl and translate it to the robot we aimed to manipulate, the Tron1 by LimX Dynamics. We aproached this by first converting the URDF of the Tron1 robot to USD, the fileformat IsaacSim uses. (This can be found at assets/robots/tron/tron.usd)

Next, we dublicated the template of the G1 Robot of Unitree and changed all joint names with the correct Tron labeling and thus created a seperate template, which can be found under /source/isaaclab_tasks/isaaclab_tasks/manager_based/locomotion/velocity/config/tron

We now implemented some changes to the rough_env_cfg.py and flat_env_cfg.py which serve as the training environments. Especially important was to add a earlier termination root_hight_below_minimum in the more general velocity_env_cfg.py in order to terminate each learning attempt earlier and thus increase learning speed. 
Furthermore, we added the task "Isaac-Velocity-Flat-Tron-v0" for training


## Results: 

Using the train.py script at scripts/reinforcement_learning/rsl_rl with the following command:
``

./isaaclab.sh -p scripts/reinforcement_learning/rsl_rl/train.py --task=Isaac-Velocity-Flat-Tron-v0 --num_envs 4096 --log_project_name TronFlat  --headless

``

, so in headless mode with 4096 parallel instances, we managed to get very decent results after 1500 iterations. The robot follows the velocity commands very accuratly, although it hops quite unnormally. This would point to the fact that we found a local minimum in the solution space and need to tune rewards much more in order to get a real "walk" solution. 


This is a Fork of Shafeef Omar's help for the Dodo Alive! Team 6 - Tron Locomotion, which I was part of.




## Prerequisites

1. Install **Isaac Sim** and **Isaac Lab**, following the official instructions:  
   👉 [IsaacLab Pip Installation Guide](https://isaac-sim.github.io/IsaacLab/main/source/setup/installation/pip_installation.html)

2. Important notes:  
   - Make sure to install Isaac Sim **via pip**.  
   - Recommended version: **Isaac Sim 4.5.0**.  
     - **Isaac Sim 5.0.0** also works, but you may encounter some package version conflicts.  
   - It is highly recommended to create a virtual environment with **Python 3.10**, for example:  
     ```bash
     conda create -n env_isaaclab_python10 python=3.10
     conda activate env_isaaclab_python10
     ```

3. Once Isaac Sim and Isaac Lab are installed, clone this repository and activate the virtual environment.

---

## Converting URDF to USD

### Step 1: Configure Environment Variable

```bash
export ROS_PACKAGE_PATH=~/IsaacLabTron/scripts:$ROS_PACKAGE_PATH
```


- Purpose: Tells the system where to look for package:`//robot_description/... paths`.

- Since the URDF file references STL mesh files under `robot_description/pointfoot/PF_P441A/meshes/`, we need to include the `scripts/` directory in `ROS_PACKAGE_PATH`.


### Step 2: Create Output Directory
```bash
mkdir -p ~/IsaacLabTron/assets/robots/tron
```

- Purpose: Prepares the output directory.

- The converted USD file will be saved as `assets/robots/tron/tron.usd`.

- If this directory does not exist, the conversion process will fail.

### Step 3: Run the URDF → USD Conversion
```bash
mkdir -p 
cd ~/IsaacLabTron
./isaaclab.sh -p scripts/tools/convert_urdf.py \
  ~/IsaacLabTron/scripts/robot_description/pointfoot/PF_P441A/urdf/robot.urdf \
  assets/robots/tron/tron.usd
```

- `cd /home/pc/IsaacLabTron`: Navigate to the IsaacLab project root.

- `./isaaclab.sh -p`: Launches IsaacLab with Python mode.

- `scripts/tools/convert_urdf.py`: The conversion tool script.

- `/.../robot.urdf`: Input URDF file path.

- `assets/robots/tron/tron.usd`: Output USD file path.

### Conversion Result

If the process completes successfully, the converted USD file will be generated at:
`
~/IsaacLabTron/assets/robots/tron/tron.usd
`
This USD file can then be directly loaded into Isaac Sim as the robot model.


![Isaac Lab](docs/source/_static/isaaclab.jpg)

---

# Isaac Lab

[![IsaacSim](https://img.shields.io/badge/IsaacSim-4.5.0-silver.svg)](https://docs.isaacsim.omniverse.nvidia.com/latest/index.html)
[![Python](https://img.shields.io/badge/python-3.10-blue.svg)](https://docs.python.org/3/whatsnew/3.10.html)
[![Linux platform](https://img.shields.io/badge/platform-linux--64-orange.svg)](https://releases.ubuntu.com/20.04/)
[![Windows platform](https://img.shields.io/badge/platform-windows--64-orange.svg)](https://www.microsoft.com/en-us/)
[![pre-commit](https://img.shields.io/github/actions/workflow/status/isaac-sim/IsaacLab/pre-commit.yaml?logo=pre-commit&logoColor=white&label=pre-commit&color=brightgreen)](https://github.com/isaac-sim/IsaacLab/actions/workflows/pre-commit.yaml)
[![docs status](https://img.shields.io/github/actions/workflow/status/isaac-sim/IsaacLab/docs.yaml?label=docs&color=brightgreen)](https://github.com/isaac-sim/IsaacLab/actions/workflows/docs.yaml)
[![License](https://img.shields.io/badge/license-BSD--3-yellow.svg)](https://opensource.org/licenses/BSD-3-Clause)
[![License](https://img.shields.io/badge/license-Apache--2.0-yellow.svg)](https://opensource.org/license/apache-2-0)


**Isaac Lab** is a GPU-accelerated, open-source framework designed to unify and simplify robotics research workflows, such as reinforcement learning, imitation learning, and motion planning. Built on [NVIDIA Isaac Sim](https://docs.isaacsim.omniverse.nvidia.com/latest/index.html), it combines fast and accurate physics and sensor simulation, making it an ideal choice for sim-to-real transfer in robotics.

Isaac Lab provides developers with a range of essential features for accurate sensor simulation, such as RTX-based cameras, LIDAR, or contact sensors. The framework's GPU acceleration enables users to run complex simulations and computations faster, which is key for iterative processes like reinforcement learning and data-intensive tasks. Moreover, Isaac Lab can run locally or be distributed across the cloud, offering flexibility for large-scale deployments.


## Key Features

Isaac Lab offers a comprehensive set of tools and environments designed to facilitate robot learning:
- **Robots**: A diverse collection of robots, from manipulators, quadrupeds, to humanoids, with 16 commonly available models.
- **Environments**: Ready-to-train implementations of more than 30 environments, which can be trained with popular reinforcement learning frameworks such as RSL RL, SKRL, RL Games, or Stable Baselines. We also support multi-agent reinforcement learning.
- **Physics**: Rigid bodies, articulated systems, deformable objects
- **Sensors**: RGB/depth/segmentation cameras, camera annotations, IMU, contact sensors, ray casters.


## Getting Started

Our [documentation page](https://isaac-sim.github.io/IsaacLab) provides everything you need to get started, including detailed tutorials and step-by-step guides. Follow these links to learn more about:

- [Installation steps](https://isaac-sim.github.io/IsaacLab/main/source/setup/installation/index.html#local-installation)
- [Reinforcement learning](https://isaac-sim.github.io/IsaacLab/main/source/overview/reinforcement-learning/rl_existing_scripts.html)
- [Tutorials](https://isaac-sim.github.io/IsaacLab/main/source/tutorials/index.html)
- [Available environments](https://isaac-sim.github.io/IsaacLab/main/source/overview/environments.html)


## Isaac Sim Version Dependency

Isaac Lab is built on top of Isaac Sim and requires specific versions of Isaac Sim that are compatible with each release of Isaac Lab.
Below, we outline the recent Isaac Lab releases and GitHub branches and their corresponding dependency versions for Isaac Sim.

| Isaac Lab Version             | Isaac Sim Version |
| ----------------------------- | ----------------- |
| `main` branch                 | Isaac Sim 4.5     |
| `v2.1.0`                      | Isaac Sim 4.5     |
| `v2.0.2`                      | Isaac Sim 4.5     |
| `v2.0.1`                      | Isaac Sim 4.5     |
| `v2.0.0`                      | Isaac Sim 4.5     |
| `feature/isaacsim_5_0` branch | Isaac Sim 5.0     |

Note that the `feature/isaacsim_5_0` will contain active updates and may contain some breaking changes
until the official Isaac Lab 2.2 release.
It currently requires the [Isaac Sim 5.0 branch](https://github.com/isaac-sim/IsaacSim) available on GitHub built from source.
Please refer to the README in the `feature/isaacsim_5_0` branch for instructions for using Isaac Lab with Isaac Sim 5.0.
We are actively working on introducing backwards compatibility support for Isaac Sim 4.5 for this branch.


## Contributing to Isaac Lab

We wholeheartedly welcome contributions from the community to make this framework mature and useful for everyone.
These may happen as bug reports, feature requests, or code contributions. For details, please check our
[contribution guidelines](https://isaac-sim.github.io/IsaacLab/main/source/refs/contributing.html).

## Show & Tell: Share Your Inspiration

We encourage you to utilize our [Show & Tell](https://github.com/isaac-sim/IsaacLab/discussions/categories/show-and-tell) area in the
`Discussions` section of this repository. This space is designed for you to:

* Share the tutorials you've created
* Showcase your learning content
* Present exciting projects you've developed

By sharing your work, you'll inspire others and contribute to the collective knowledge
of our community. Your contributions can spark new ideas and collaborations, fostering
innovation in robotics and simulation.

## Troubleshooting

Please see the [troubleshooting](https://isaac-sim.github.io/IsaacLab/main/source/refs/troubleshooting.html) section for
common fixes or [submit an issue](https://github.com/isaac-sim/IsaacLab/issues).

For issues related to Isaac Sim, we recommend checking its [documentation](https://docs.omniverse.nvidia.com/app_isaacsim/app_isaacsim/overview.html)
or opening a question on its [forums](https://forums.developer.nvidia.com/c/agx-autonomous-machines/isaac/67).

## Support

* Please use GitHub [Discussions](https://github.com/isaac-sim/IsaacLab/discussions) for discussing ideas, asking questions, and requests for new features.
* Github [Issues](https://github.com/isaac-sim/IsaacLab/issues) should only be used to track executable pieces of work with a definite scope and a clear deliverable. These can be fixing bugs, documentation issues, new features, or general updates.

## Connect with the NVIDIA Omniverse Community

Have a project or resource you'd like to share more widely? We'd love to hear from you! Reach out to the
NVIDIA Omniverse Community team at OmniverseCommunity@nvidia.com to discuss potential opportunities
for broader dissemination of your work.

Join us in building a vibrant, collaborative ecosystem where creativity and technology intersect. Your
contributions can make a significant impact on the Isaac Lab community and beyond!

## License

The Isaac Lab framework is released under [BSD-3 License](LICENSE). The `isaaclab_mimic` extension and its corresponding standalone scripts are released under [Apache 2.0](LICENSE-mimic). The license files of its dependencies and assets are present in the [`docs/licenses`](docs/licenses) directory.

## Acknowledgement

Isaac Lab development initiated from the [Orbit](https://isaac-orbit.github.io/) framework. We would appreciate if you would cite it in academic publications as well:

```
@article{mittal2023orbit,
   author={Mittal, Mayank and Yu, Calvin and Yu, Qinxi and Liu, Jingzhou and Rudin, Nikita and Hoeller, David and Yuan, Jia Lin and Singh, Ritvik and Guo, Yunrong and Mazhar, Hammad and Mandlekar, Ajay and Babich, Buck and State, Gavriel and Hutter, Marco and Garg, Animesh},
   journal={IEEE Robotics and Automation Letters},
   title={Orbit: A Unified Simulation Framework for Interactive Robot Learning Environments},
   year={2023},
   volume={8},
   number={6},
   pages={3740-3747},
   doi={10.1109/LRA.2023.3270034}
}
```
