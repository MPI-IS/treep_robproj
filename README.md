# treep_robproj

This is a demo related to the software tutorial "management of robotics project, part 2".
See: [https://owncloud.tuebingen.mpg.de/index.php/s/CNFA32EEY2eNNaE](https://owncloud.tuebingen.mpg.de/index.php/s/CNFA32EEY2eNNaE)

## Setup of the workspace

setup a software folder (name it was you wish)

```bash
mkdir ~/software
cd ~/software
```

setup a python virtual env

```bash
python -m venv ./env
source ./env/bin/activate
```

install the python dependencies

```bash
pip install treep colcon-common-extensions pyside2 empy catkin-pkg sphinx breathe matplotlib
```

install the system-wide dependencies

```bash
sudo apt install git python3-pip cmake libcereal-dev libboost-all-dev libgtest-dev libeigen3-dev libedit-dev libncurses5-dev freeglut3-dev libxmu-dev doxygen libxcb-xinerama0 libxrandr2 libxcursor1 libxinerama1 libglfw3-dev
```

clone this repo:

```bash
git clone git@gitlab.is.tue.mpg.de:robotics/treep_robproj.git
```

clone all the repos of the project:

```
treep --clone ROBPROJ
```

compile the workspace:

```
cd workspace
colcon build --cmake-args -DCMAKE_BUILD_TYPE=Release -DPYBIND11_TEST=OFF --no-warn-unused-cli
```

sourcing the workspace

```
source ./install/setup.bash
```

optionally, have your python virtual env and the
workspace sourced for all new terminals, by adding a the end
of the file `~/.bashrc` something like:

```bash
echo "- sourcing robotics management virtual env"
source ~/software/env/bin/activate
echo "- sourcing robotics management workspace"
source ~/software/workspace/install/setup.bash
```

it is also convenient to be able to compile the workspace
from any folder. Can also be added to the `~.bashrc` file:

```bash
echo "- adding robproj_compile function"
robproj_compile() {
    start_dir=$(pwd)
    cd ~/software/workspace/
    colcon build --cmake-args -DCMAKE_BUILD_TYPE=Release -DPYBIND11_TEST=OFF --no-warn-unused-cli
    cd ${start_dir}
}
```

## Run the demo

More info regarding the demo can be found in the slides [https://owncloud.tuebingen.mpg.de/index.php/s/CNFA32EEY2eNNaE](https://owncloud.tuebingen.mpg.de/index.php/s/CNFA32EEY2eNNaE)

### start the realtime process

In a terminal

```
robpro2_run_cos
```

This spawns a thread which will write data to the shared memory
at a desired frequency of 200Hz.

This process also pings a shared memory array with information about
the frequency its main loop is executed.

### check if the process is running realtime

While `robproj2_run_cos` runs, launch in another terminal:

```
robproj2_period_report
```

This reads the shared memory array and displays the average period
at which `robproj2_run_cos` runs.

### read the data from any python code

```python
import robproj2
data = robproj2.get_cos_value()
```

### display the data live

In another terminal:

```bash
robproj2_plot_cos_values
```

There is the issue that the spawned matplotlib window is difficult
(impossible?) to exit. Close the terminal to terminate the program.


### issues ?

If something hangs, stop all the executables and delete the files

```
/dev/shm/cos_signal
/dev/shm/cos_signal_PERIODS
/dev/shm/sem.cos_signal_PERIODS_mutex
```

They correspond to the shared memory used by all the program above.

### understand what is going on

Code in the project `~/software/workspace/src/robproj2` is heavily commented.
