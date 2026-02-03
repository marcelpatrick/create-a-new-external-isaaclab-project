# create-a-new-external-isaaclab-project

This tutorial build on LyncheeAI's tutorial (https://lycheeai-hub.com/isaac-lab/build-your-own-isaac-lab-external-project-template-generator) and tries to present some of the concepts in a more beginner friendly way.

It allows us to create an External IsaacLab Project

- Pre requisites: Install IsaacLab and create a python environment: https://github.com/marcelpatrick/IsaacSim-IsaacLab-installation-for-Windows-Easy-Tutorial?search=1. (original IsaacLab project: https://github.com/isaac-sim/IsaacLab.git ) 

# 0. Differences: External vs Internal projects

### Option A: Internal Project: (input your code into your local IsaacSim project) 
You have to clone the original IsaacLab project to your local machine. 
You add your code inside the IsaacLab project structure itself (inside the IsaacLab project folders) modifying your IsaacLab instance. 
```
IsaacLab/
├── source/
│   ├── extensions/
│   │   ├── omni.isaac.lab_tasks/
│   │   │   ├── ... (official Isaac Lab tasks)
│   │   │   └── your_cube_stacking_task/  ← You add your stuff here
```
Problem: If NVIDIA releases IsaacLab v2.0, you have to manually merge your changes with their updates. Makes it more messy to find all the changes they made to the IsaacSim project and how they conflict with your code because now you modified the IsaacSim project itself in your local instance.

### Option B: External project (your own separate folder)
Your code lives in its own folder, completely outside the IsaacLab project. Doesn't modify IsaacLab's original folder structure.
You don't need to clone the IsaacLab project repo locally to your computer.
It imports Isaac Lab as a Python library dependency and accesses IsaacLab features through this library.

```
┌─────────────────────────────────────────┐
│  YourCubeStackingProject/               │
│  (Your independent repo)                │
│                                         │
│  ├── scripts/                           │
│  │   └── train.py ──────────────────────┼───┐
│  ├── source/                            │   │
│  │   └── your_task/                     │   │
│  │       └── my_env.py ─────────────────┼───┤
│  └── ...                                │   │
└─────────────────────────────────────────┘   │
                                              │  import isaaclab
                                              │  import isaaclab.envs
                                              │  import isaaclab.scene
                                              ▼
┌─────────────────────────────────────────┐
│  IsaacLab/                              │
│  (NVIDIA's code, untouched)             │
│                                         │
│  ├── source/                            │
│  │   └── isaaclab/                      │
│  │       ├── envs/                      │
│  │       ├── scene/                     │
│  │       ├── robots/                    │
│  │       └── ...                        │
│  └── ...                                │
└─────────────────────────────────────────┘
```
Benefit: If Nvidia releases an update, it is easier and cleaner to update because you just have to point the settings in your project's config file to the new IsaacLab version - since you didn't modify the original IsaacLab code or folder structure. 
Dependency configs are defined inside the file: `extension.toml`

```py
[project]
name = "my-isaac-lab-project"
version = "0.1.0"
description = "My custom Isaac Lab environment"
requires-python = ">=3.10"
# ********* ISAACLAB VERSION GOES HERE: *********
dependencies = [
    "isaaclab==2.0.0",  # Pin to a specific version
]

[build-system]
requires = ["setuptools>=61.0", "wheel"]
build-backend = "setuptools.build_meta"
```

# 1. Download the project structure and it's template python scripts

- Open Anaconda prompt
- Activate your isaaclab python environment `conda activate env_isaaclab`
  - If you haven't created your env yet check this turorial: https://github.com/marcelpatrick/IsaacSim-IsaacLab-installation-for-Windows-Easy-Tutorial?search=1 
- navigate to the root `(env_isaaclab) C:\Users\[YOUR USER]\IsaacLab>.` of the isaaclab project with `cd isaaclab`
- Run the Template Wizard (it comes with the IsaacLab project previously installed: type `isaaclab.bat --new` (on windows)
- select a task type (using `external` in this case)
- select project path
- select project name: in this example I'm using `Myisaaclabproject2`
- select a workflow type: manager-based vs direct or all (`all` in this example)
- Choose RL library: backend and algorithm. using rl_games and rsl_rl in this example

 It generates this:

```
C:.
├───.vscode
│   └───tools
├───scripts
│   ├───rl_games
│   └───rsl_rl
│       └───__pycache__
└───source
    └───MyIsaacLabProject2
        ├───config
        ├───docs
        └───MyIsaacLabProject2
            └───tasks
                ├───direct
                │   ├───myisaaclabproject2
                │   │   └───agents
                │   └───myisaaclabproject2_marl
                │       └───agents
                └───manager_based
                    └───myisaaclabproject2
                        ├───agents
                        └───mdp
```

The generated foler `myusaaclabproject2` (path: `"C:\Users\[YOUR USER]\MyIsaacLabProject2\source\MyIsaacLabProject2\MyIsaacLabProject2\tasks\manager_based\myisaaclabproject2"`) actually contains the cartpole task. Template manager automatically generates this task by default for testing purposes. 
- rename this folder to `cartpole` for consistency - as we will add other tasks to the folder structure. 

 # 2. Install the Project
 
 Now that you downloaded the templated project you need to install it. 
 
 Inside your project folder, run: `C:\Users\[YOUR USER]\MyIsaacLabProject2>python -m pip install -e source/MyIsaacLabProject2
 
 This does:
 1- installs your project as a Python package in your local Python environment (`env_isaaclab` in this example) - making your project's code discoverable by Python and importable
   - `pip install -e` adds your project folder to Python's search path within that specific conda environment. Now Python knows: "when someone imports your_project, look in `/path/to/your/project`"
 2- Registers your environments on Gymnnasium: runs the `gym.register()` function in your `__init__.py` file (more about Gymnasium here: https://github.com/marcelpatrick/isaaclab-rl-manager-workflow-simple#2-register-the-environment-on-gymnasium)
 
List available environments: `python scripts/list_envs.py`
- you should see your project listed

## 3. Run a training task

On the Anaconda Prompt terminal, go to your projects root folder and run:
eg. `(env_isaaclab) C:\Users\[YOUR USER]\MyIsaacLabProject2>python scripts/rsl_rl/train.py --task=Template-Myisaaclabproject2-v0`

- This will depend on which library you have installed. Eg. if you have rl_games instead of rsl_rl, run: `(env_isaaclab) C:\Users\[YOUR USER]\MyIsaacLabProject2>python scripts/rl_games/train.py --task=Template-Myisaaclabproject2-v0`

Stop with `Ctrl C`
