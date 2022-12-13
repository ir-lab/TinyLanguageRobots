# TinyLanguageRobots
A light-weighted, open-box 2D robot manipulation simulator.

This repo majorly includes 2 parts:
1.  **A lighted-weighted simulator (class `TinyRobotEnv`)** powered by pygame and openai gym. This is 
a 2D simulator, featuring the visual appearance of a UR5 robot. Inverse kinematics API is 
implemented as well.
2.  **Data collection toolkit** for collecting language-conditioned imitation 
learning datasets. In this toolkit, there includes the following items:
    - **A rule-based planner (class `Planner`)** that generates actions to perform, according to 
    users' rules, such as go to specific position and close the gripper.
    - **A recorder (class `Recorder`)** that records the image, states, sentence and all useful 
    information to files.

## Installation
```
git clone https://github.com/ir-lab/TinyLanguageRobots.git
pip install -r requirements.txt
```

## Hands on
```
python collect_data.py
```
In `collect_data.py`, some sample tasks are defined.
This command will automatically start collecting demonstrations under the current directory.
Please use this file as a reference for constructing your own tasks and dataset.

## API
### `CLASS TinyRobotEnv`
#### `TinyRobotEnv(config, render_mode)`
- **`config`** is a dictionModularity through Attention:
Efficient Training and Transfer of Language-Conditioned Policies for Robot Manipulationary indicating the initialization setups of the environment. An
example is at `./config.yaml`. It defines
the background scale in pixels (`desk_width`, `desk_height` and `scale`). It also defines the
robot (`robot`) and all the objects to be displayed (`objects`). In `objects`, all the objects
with a `position` property will be moveable.
- **`render_mode`** defines how to visualize the environment. When `render_mode == human`, it will
visualize it in a window. When `render_mode == rgb_array`, the simulator will return an image
instead of showing it in the window when rendering.

#### `step(action, eef_z)`
- **`action`** is an array with shape (4,), where each entry represents the action for each joint of the robot,
starting from the shoulder.
- **`eef_z`** represents the target height of the z axis.

#### `render(render_mode)`
- **`render_mode`** has been implemented for `human` and `rgb_array` options.

#### `ik(xyo)`
This function calculates the inverse kinematics for a given target eef position.
- **`xyo`** is the given eef position, an array of [x, y, orientation]

### `CLASS Planner`
In this class, users can write the code to define different tasks, and generate
actions for a given task.
#### `_generate_plan_()`
In this function, users can write code to define the action of each task. Please
refer to the code `./planner.py` for an example of how to do it.
#### `Planner(task, env)`
- **`task`** is a dictionary and should be aligned with the definition in `_generate_plan_()`.
- **`generate_action()`** returns the current action to take. The return value is ready to input 
to `TinyRobotEnv.step(...)`.
- **`ends()`** returns a boolean value of whether the plan has ended.

### `CLASS Recorder`
This class provides a solution to store the whole episode to files. For an episode,
a folder will be created. Images of each of the timesteps will be stored. The states,
task information and all other information will be logged as in a json file under 
that folder.
#### `Recorder(data_folder)`
- **`data_folder`** is a string indicating the folder of the target data storage location.
#### `record_step(step, img, state, sentence, action, task)`
- **`step`** is an integer indicating the current timestep.
- **`img`** is an array of representing the current img.
- **`state`** is a dictionary of current state. It will be logged in a json file.
- **`sentence`** is a string which is the language instruction to the task.
- **`action`** is an array showing the current action taken.
- **`task`** is a dictionary depicting the task the demonstration is currently executing.
#### `finish_recording()`
All the informations will be stored to the local json file when this function is called.
