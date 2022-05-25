# GaMD OpenMM Examples

  This repository provides some examples that you can use to validate your gamd-openmm installation is working and provide some example configuration files.

## Instructions

First, make sure that you have followed the directions for setting up OpenMM and the gamd-openmm project found under Installation in the README file of the [gamd-openmm](https://github.com/MiaoLab20/gamd-openmm) project.  Once you have activated your anaconda environment and you have the gamdRunner in your path, execute the following commands (not within the gamd-openmm directory):

```
git clone git@github.com:MiaoLab20/gamd-openmm-examples.git
cd gamd-openmm-examples

```

You'll find example alanine dipeptide project and a dihydrofolate reductase directories in this project.  The configuration files for both projects have been set to have a very short running time, so that you can validate that your installation is completely working without having to wait for a long simulation to run.  You can verify that your path is setup to the gamdRunner correctly by just typing *gamdRunner* on the command line.  You should see an output that appears something like the following:

```

usage: gamdRunner [-h] [-r] [-p PLATFORM] [-d DEVICE_INDEX] [-D] [-o OUTPUT_DIRECTORY] INPUT_FILE_TYPE INPUT_FILE
gamdRunner: error: the following arguments are required: INPUT_FILE_TYPE, INPUT_FILE

```

You can pass the --help flag to the gamdRunner to get a more verbose set of instructions about each of the available flags.  In this example, we'll use the alanine dipeptide files to validate that our installation is working properly.

### Validating your environment works

```
cd alanine-dipeptide
gamdRunner xml lower-dual.xml
```

This will run a CUDA based simulation.  You have don't have a CUDA capable GPU, please see the instructions below for how to start a simulation using a different platform.  You should see various running information output by the gamdRunner before it reports a prompt.  You may see a warning message about importing simtk.openmm being deprecated.  This can be safely ignored at this time.

When the program is complete, you should find a directory like quick-output/lower-dual/ with the output files for your gamd run.  Note that the runner copies a duplicate of your configuration file into the output directory called input.xml, so that you'll have everything together in one directory for archival.  This example generates the output as a .dcd file.  The gamd.log file contains information about the gamd run from the very beginning.  The file gamd-reweighting.log contains only the gamd information, since the start of the production phase for use in later analysis.  The file gamd-restart.dat contains information about the potential energy and standard deviation at the end of equilibration phase.  The file gamd-running.csv contains information about the different energy and gamd values, which can be useful in figuring out if you need to adjust your initial conditions.


### Learning to restart your job

We will use the dihydrofolate reductase to demonstate how to restart a job.  We will be starting the job in the terminal, and then killing it off by hitting Ctrl-C part way through.  These instructions will assume your starting path is in the gamd-openmm-examples directory to start.

```
cd dihydrofolate-reductase/
gamdRunner xml lower-dual.xml
```

Wait for twenty seconds or so after you see the message below and hit Ctrl-C.
```
Running:	60000 steps
```

At this point, you'll see a python Traceback and the words Keyboard Interrupt.  This is normal and shouldn't hurt anything.  Feel free to go explore the quick-output/lower-dual/ directory and see the state of the files at this point.  Now, to restart the job, execute the following commands from the dihydrofolate-reductase directory:

```
gamdRunner -r xml lower-dual.xml
```

You should see messages indicating that it is restarting from the saved checkpoint.  The gamdRunner is aware of the gamd log files, and will continue to add to those files.  Once it is complete, you can check your files in the output directory again.

### Extending a simulation

If you find that you need to extend the gamd production in a simulation, you can modify the gamd-production tag contents of your input file and use the restart functionality to start it up again.  It will continue where it left off.  The input.xml file in your output results directory will get updated with your new input.xml file.


### Running an OpenCL based simulation

It's important to note that the platform passed to OpenMM to use OpenCL is case sensitive.  We'll we've attempted to make the gamdRunner intelligent enough if you don't get it correct, the correct platform name/case is OpenCL.  Below is an example of how to execute the alanine dipeptide simulation using OpenCL.


```
cd alanine-dipeptide
gamdRunner -p OpenCL xml lower-dual.xml
```

### Running a CPU based simulation

It's important to note that the platform passed to OpenMM to use CPU is case sensitive.  We'll we've attempted to make the gamdRunner intelligent enough if you don't get it correct, the correct platform name/case is CPU.  If you want to limit the number of CPU cores that OpenMM utilizies, you should set the environment variable to OPENMM_CPU_THREADS to the number of cores you want it to you.  Below is an example of how to execute the alanine dipeptide simulation using the CPU and only three cores under the bash shell.


```
export OPENMM_CPU_THREADS=3
cd alanine-dipeptide
gamdRunner -p CPU xml lower-dual.xml
```

