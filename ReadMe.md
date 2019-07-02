# Python Virtual Environment for TICEE Bamboo agents (RHEL 7/Centos 7)

This repo holds a fully standalone python 3.6 virtual environment for executing jobs requiring python3 on Optimus Bamboo shared agents. A "standard" python3 virtual environment still has some dependencies on the python3 installation used to create it.

Differences to a "standard" python3 virtual environment:

- made using `--copies` option rather than the typical symlinks on linux
- includes libpython library
- includes lib/python3.6 folders
- includes activate_ld (modified from activate script) to set LD_LIBRARY_PATH and PYTHONPATH, and adjust hard-wired #! paths in venv scripts

If running under Bamboo shared agent the virtual environment must be activated by executing:  

    python-venv-centos/env/bin/activate_ld

This will set the relevant environment variables so that python will only resolve needed libraries and modules from within the virtual environment.

## How to reconstruct this virtual environment

1. Install python3.x
1. Create a new virtual environment in the usual manner, but using the `--copies` option:
    ```
    python3 -m venv --copies env
    ```
1. Copy the runtime library and python3.x library into the virtual environment (example assumes python3 installed in usual location):
   ```
    $ cp /lib64/libpython3.6m.so.1.0 env/lib
    $ cp -r /lib64/python3.6 /env/lib
    $ cd env/lib
    $ ln -s libpython3.6m.so.1.0 libpython3.6m.so
    ```
1. Modify activate_ld around line 65 and update **placeholder** variable definition to match the hardwired virtual environment path to replace in #! scripts:
    ```
    placeholder='/home/stuohey/dev'       <==== change this
    sedcmd="s#${placeholder}#${bamboo_build_working_directory}#"
    ```

1. Place a copy of  the updated activate_ld in the env/bin folder
