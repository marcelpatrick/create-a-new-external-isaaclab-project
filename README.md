# create-a-new-external-isaaclab-project

- This tutorial builds on LyncheeAI's tutorial (https://lycheeai-hub.com/isaac-lab/build-your-own-isaac-lab-external-project-template-generator) and presents concepts in a more beginner-friendly way.
- It uses the original IsaacLab project: https://github.com/isaac-sim/IsaacLab.git
- It allows you to create an External IsaacLab Project

# Prerequisites: 
- Install IsaacLab and create a Python environment: https://github.com/marcelpatrick/IsaacSim-IsaacLab-installation-for-Windows-Easy-Tutorial?search=1. 

# 0. Concepts: Running a custom task internally inside the IsaacLab Project vs on an External Project

Let's say you want to create your own IsaacLab task: simulate a task of your choice, modify the Reinforcement Learning parameters, the robot policies etc. You have 2 options: 

### Option A: Internal Run: (input your code into your local IsaacSim project) 
After you clone the original IsaacLab project from NVIDIA's GitHub repository, you can modify this instance directly on your local machine, adding your custom task and code to your local IsaacLab project and its folder structure. It becomes available alongside all the built-in tasks:

```
IsaacLab/
├── source/
│   ├── extensions/
│   │   ├── omni.isaac.lab_tasks/
│   │   │   ├── ... (official built in Isaac Lab tasks)
│   │   │   └── your_task/  ← You add your stuff here
```
**Problem**: If NVIDIA releases IsaacLab v2.0, you have to merge your changes with their updates manually. You would have to clone the new version to your computer and manually track where changes were made to make sure it works with your task. If they change the folder structure or how other scripts in the project communicate with tasks, it can break your code. Makes it messy to find all the changes they made to the project and how they conflict with your code.

### Option B: External project (your own separate folder)
Your code lives in its own folder, completely outside the IsaacLab project. Doesn't modify IsaacLab's original folder structure.
Your external project imports IsaacLab as a Python library dependency and accesses IsaacLab features through it.

```
┌─────────────────────────────────────────┐
│  YourProject/Your task                  │
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
**Benefit**: If Nvidia releases an update, all you have to do is clone the new version to your computer and register/install it. It is easier and cleaner to update because you just have to download and install a new IsaacLab version to your machine, no need to change your code.

# 1. Create a new external project using the Template Wizard
- This will download the project structure and its template python scripts

- Open Anaconda prompt
- Activate your isaaclab python environment `conda activate env_isaaclab`
  - If you haven't created your env yet check this turorial: https://github.com/marcelpatrick/IsaacSim-IsaacLab-installation-for-Windows-Easy-Tutorial?search=1 
- navigate to the root `(env_isaaclab) C:\Users\[YOUR USER]\IsaacLab>.` of the isaaclab project with `cd isaaclab`
- Run the Template Wizard (it comes with the IsaacLab project previously installed: type `isaaclab.bat --new` (on windows) or `.\isaaclab.bat --new` on Anaconda PowerShell
- select a task type (using `external` in this case)
- select project path
- select project name: in this example, I'm using `MyIsaacLabProject`
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
    └───MyIsaacLabProject
        ├───config
        ├───docs
        └───MyIsaacLabProject
            └───tasks
                ├───direct
                │   ├───MyIsaacLabProject
                │   │   └───agents
                │   └───MyIsaacLabProject_marl
                │       └───agents
                └───manager_based
                    └───MyIsaacLabProject
                        ├───agents
                        └───mdp
```

The generated folder (path: `"C:\Users\[YOUR USER]\MyIsaacLabProject\source\MyIsaacLabProject\MyIsaacLabProject\tasks\manager_based\MyIsaacLabProject"`) actually contains the cartpole task. Template manager automatically generates this task by default for testing purposes. 
- Rename this folder to `cartpole` for consistency - as we will add other tasks to the folder structure. 

 # 2. Install the Project
 
 Now that you downloaded the templated project you need to install it. 
 
 Inside your project folder, run: `C:\Users\[YOUR USER]\[YOUR PROJECT NAME]>python -m pip install -e source/[YOUR PROJECT NAME]` - eg: `C:\Users\[YOUR USER]\MyIsaacLabProject>python -m pip install -e source/MyIsaacLabProject`
 
 This does:
 1- installs your project as a Python package in your local Python environment (`env_isaaclab` in this example) - making your project's code discoverable by Python and importable
   - `pip install -e` adds your project folder to Python's search path within that specific conda environment. Now Python knows: "when someone imports your_project, look in `/path/to/your/project`"
 2- Registers your environments on Gymnnasium: runs the `gym.register()` function in your `__init__.py` file (more about Gymnasium here: https://github.com/marcelpatrick/isaaclab-rl-manager-workflow-simple#2-register-the-environment-on-gymnasium)
 
List available environments with `python scripts/list_envs.py`: `C:\Users\[YOUR USER]\[YOUR PROJECT NAME]>python scripts/list_envs.py`
- you should see your project listed

# 3. Run a training task

Open Anaconda Prompt: conda activate env_isaaclab
- To just open the IsaacLab project in vscode type: `code MyIsaacLabProject`
- To run the cartpole task that comes by default in the cloned project: Here we will run the Cartpole task with its default Reinforcement Learning parameters.
  - From the root folder run: `python MyIsaacLabProject/scripts/rsl_rl/train.py --task=Template-Myisaaclabproject-v0` Or navigate to the project folder `cd MyIsaacLabProject` and from there run `python scripts/rsl_rl/train.py --task=Template-Myisaaclabproject-v0`
    
- The correct path for running the project will also depend on which library you have installed. Eg. if you have rl_games instead of rsl_rl, run: `(env_isaaclab) C:\Users\[YOUR USER]\MyIsaacLabProject>python scripts/rl_games/train.py --task=Template-MyIsaacLabProject-v0`

Stop with `Ctrl C`

**Errors**
- If you get something like `couldn’t access: MyIsaacLabProject` make sure the project was installed (refer to “2. Install the Project” in https://github.com/marcelpatrick/create-a-new-external-isaaclab-project/blob/main/README.md) 
- If you get something like ``gymnasium.error.VersionNotFound: Environment version `v32` for environment `Template-Myisaaclabproject` doesn't exist.`` OR  ``gymnasium.error.NameNotFound: Environment `Template-Myisaasdfsdfg` doesn't exist.`` Make sure your **TASK ID** is correct. -> Copy it from `C:\Users\[YOUR USER]\MyIsaacLabProject\source\MyIsaacLabProject\MyIsaacLabProject\tasks\manager_based\myisaaclabproject\__init__.py` > `id="Template-Myisaaclabproject-v0"`

# 4- Next Steps
- After this, you can go to the next level, add new tasks to your project and customize their reward function and training parameters. Check: https://github.com/marcelpatrick/Custom-IsaacLab-Manager-based-External-project 
