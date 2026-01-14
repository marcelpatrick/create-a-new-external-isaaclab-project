# create-a-new-external-isaaclab-project

This tutorial build on LyncheeAI's tutorial (https://lycheeai-hub.com/isaac-lab/build-your-own-isaac-lab-external-project-template-generator) and tries to present some of the concepts in a more beginner friendly way.

# 1. Download the project structure and it's template python scripts

- Open Anaconda prompt
- navigate to the root `(env_isaaclab) C:\Users\[YOUR USER]\IsaacLab>.` of the isaaclab project with `cd isaaclab`
- type `isaaclab.bat --new` (on windows)
- select a task type (using `external` in this case)

## Differences: External vs Internal projects

Option A: Internal task (modifying Isaac Lab directly)
You add your code inside the Isaac Lab repository itself.
```
IsaacLab/
├── source/
│   ├── extensions/
│   │   ├── omni.isaac.lab_tasks/
│   │   │   ├── ... (official Isaac Lab tasks)
│   │   │   └── your_cube_stacking_task/  ← You add your stuff here
```
Problem: If NVIDIA releases Isaac Lab v2.0, you have to manually merge your changes with their updates. Messy.

Option B: External project (your own separate folder)
Your code lives in its own folder, completely outside Isaac Lab. It just imports Isaac Lab as a dependency.

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

When you run `./isaaclab.sh --new` and choose "external,"
- select project path
- select project name
- select a workflow type: manager-based vs direct or all (`all` in this example)
- Choose RL library: backend and algorithm. using rl_games and rsl_rl in this example

 it generates this:

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

 # 2. Install the Project
 
 Now that you downloaded the templated project you need to install it. This does:
 
 1- installs your project as a Python package in your local Python environment (`env_isaaclab` in this example) - making your project's code discoverable by Python and importable
   - `pip install -e` adds your project folder to Python's search path within that specific conda environment. Now Python knows: "when someone imports your_project, look in `/path/to/your/project`"
 2- Registers your environments on Gymnnasium: runs the `gym.register()` function in your `__init__.py` file (more about Gymnasium here: https://github.com/marcelpatrick/isaaclab-rl-manager-workflow-simple#2-register-the-environment-on-gymnasium)

Inside your project folder, run: `C:\Users\[YOUR USER]\MyIsaacLabProject>python -m pip install -e source/MyIsaacLabProject`
 
- list available environments: `python scripts/list_envs.py`


## 3. Run a training task

On the Anaconda Prompt terminal, go to your projects root folder and run:
eg. `python scripts/rsl_rl/train.py --task=Template-Myisaaclabproject-Direct-v0`

