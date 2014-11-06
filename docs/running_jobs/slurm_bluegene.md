# Managing BlueGene/Q jobs with SLURM

## Srun

The srun command is used to execute applications on Avoca. The slurm module, loaded by default, ensures that the srun command is included in your path. Once an allocation (either portion of a block or a whole block if requesting 512 or more nodes) has been assigned to your job (by SLURM with sbatch or salloc), it is initialised and then your SLURM submission script should call srun to execute your program.

The simplest usage for srun is:

```
srun <your program> <your program args>
```

Please note that you don't run your script with srun, just the executable.

A more comprehensive usage for srun is:

```
srun --ntasks=<total number of MPI tasks> --ntasks-per-node=<num of MPI tasks per node> <your program> <your program args>
```

where:

* `<num of MPI tasks per node>` - is the number of MPI processes/tasks you wish to have spread across each 16 CPU-core node in your allocation.
* `<total number of MPI tasks>` - is the total number of MPI processes/tasks you want the application to be run across. Use this if you don't want the application using the maximum number of tasks available to the allocation, where the maximum is ntasks-per-node multiplied by the number of nodes you request at the top of your SLURM script.
* If not specified, the default value for this option will be 16 MPI processes per node, with each MPI process having 1GB of RAM and up to four threads. The value that you should be using depends on the type of application that you are executing (what type of parallel code it is). See below for further details.

## Parallel applications

### MPI applications

Applications that have been written using purely-MPI code will need to use a value for the `--ntasks-per-node` option that is determined by the amount of memory that each MPI process of the job requires. All possible values of ntasks-per-node are listed below along with the corresponding amount of memory per MPI process/task:

<table border="1">
<tr><th>Number of MPI processes per node</th><th>Memory per MPI process</th></tr>
<tr><td>64</td><td>256MB</td></tr>
<tr><td>32</td><td>512MB</td></tr>
<tr><td>16</td><td>1GB</td></tr>
<tr><td>8</td><td>2GB</td></tr>
<tr><td>4</td><td>4GB</td></tr>
<tr><td>2</td><td>8GB</td></tr>
<tr><td>1</td><td>16GB</td></tr>
</table>

<p></p>

As mentioned, the default is 16 and you should probably start with that. This arrangement is the closest thing to Virtual-Node mode from the Blue Gene/P. Moving to 32 tasks-per-node can quite often give you a performance improvement but this leaves each of your MPI processes with only 512MB RAM. Some applications may see further performance improvements by moving to 64 MPI tasks per node, but this further halves the memory per process down to 256MB. If you find that you are receiving out of memory type errors you may need to decrease the number of MPI tasks per node, which increases the amount of memory per MPI task. Unfortunately the ideal value of ntasks-per-node will vary from application to application. And it may even be the case that the same application when running different datasets, requires a different value of ntasks-per-node.

As an example, let's assume that we have a purely MPI application, that is going to run with a dataset that means each MPI process requires 1GB of RAM. This forces us to use 16 MPI processes per node. Using more than 16 isn't an option because it leaves us with too little memory per MPI process and using fewer is just wasting resources - cores will go unused on each node which will mean we're using more SUs than we should be for a given computation. Below is a sample SLURM script that could be used for this case:

```
#!/bin/sh
# Here we're asking for two days
#SBATCH --time=2-0
# We want 32 nodes (512 CPU cores)
#SBATCH --nodes=32
#SBATCH --job-name="purempi_example"
#SBATCH --output="purempi.out"

srun --ntasks-per-node=16 ~/bin/mpiheat/heat_2d ~/data/mpihead/initial2d-512.in
```

### Multithreaded applications (pthreads, OpenMP)

Applications that have been parallelised using POSIX Threads (pthreads) or OpenMP can be run on a single Blue Gene/Q node. A single node comprises 16 cores, and each of those cores can execute four threads in hardware. In this way your multithreaded application can utilise up to 64 threads. The entire memory of your single node is also available to your multithreaded application - all 16GB. You must run with `--ntasks-per-node=1` for this type of application. Below is a sample SLURM script that could be used for a multithreaded application:

```
#!/bin/sh
# Here we're asking for 2 hours
#SBATCH --time=2:0:0
# We want only one node because we've got a simple multi-threaded program
#SBATCH --nodes=1
#SBATCH --job-name="threaded_example"
#SBATCH --output="threaded.out"

srun --ntasks-per-node=1 ~/bin/threadedpi/calc
```

### Hybrid applications: mixed MPI & multithreading

Hybrid applications are those that use both MPI and multithreading. They use MPI to communicate between nodes and some form of multithreading within nodes. This method of programming, although far less common today is almost certainly going to be the way forward for future high performance computing. Just as with pure MPI applications, the amount of memory that your application requires per node will put a constraint on the possible values of `--ntasks-per-node` that you can use. For example, if your application and dataset require 4GB per multi-threaded node, this will limit you to either 1, 2 or 4 MPI processes per node. The appropriate value for `--ntasks-per-node` will be application dependent and will depend on how many threads the multithreaded component of the application can scale to. At the extreme end of the scale, if the multithreaded component of your application can scale to 64 threads, you can run with 1 MPI process per node. It may be quite possible that your application sits somewhere in the middle, and may work best with 4 MPI processes per node, each of those processes running 8 (or perhaps 16 threads). Once you have selected (and set) the value of `--ntasks-per-node` you will also need to communicate to your application the number of threads it should spawn on each node. This may be done by setting the `OMP_NUM_THREADS` environment variable prior to calling srun, or it may even be a command line argument to your application. Note that the total number of threads per node must be less than or equal to 64, so for example `ntasks-per-node` multiplied by `OMP_NUM_THREADS` has a maximum possible value of 64.

As an example, let's assume that we have a hybrid application, that uses MPI and OpenMP and that we know each node requires 4GB of RAM because of the size of our dataset. This means that we are left with the choice of 1, 2, or 4 MPI processes per node, because going higher than this would leave us with too little memory per node. Let us also assume that we know the multithreading in this application scales well to eight threads per node. This leads us to a choice of 4 MPI processes per node with each of those using 8 threads. Below is a sample SLURM script that could be used for this case:

```
#!/bin/sh
# Here we're asking for one day and 12 hours
#SBATCH --time=1-12
# We want 16 nodes (256 CPU cores)
#SBATCH --nodes=16
#SBATCH --job-name="hybrid_example"
#SBATCH --output="hybrid.out"

export OMP_NUM_THREADS=8
srun --ntasks-per-node=4 ~/bin/hybridwave/calc_wave ~/data/hybridwave/wave128.in
```

As another example, let's take the application NAMD. Below is a sample SLURM script that could be used for this case:

```
#!/bin/sh
# Here we're asking for one day
#SBATCH --time=1-0
# We want 4 nodes (64 CPU cores)
#SBATCH --nodes=4
#SBATCH --job-name="hybrid_example"
#SBATCH --output="hybrid.out"

srun --ntasks-per-node=XXX NAMD
```
 
## Serial applications

You may run a single-threaded application on only one node and it will get access to the entire 16GB of RAM on that node. This may be a viable model to work in if your application requires a lot of memory (up to 16GB obviously). Below is a sample SLURM script that could be used for such a case:

```
#!/bin/sh
# Here we're asking for one day
#SBATCH --time=1-0
# We want 1 node (16 CPU cores)
#SBATCH --nodes=1
#SBATCH --job-name="serial_example"
#SBATCH --output="singlethread.out"

srun --ntasks-per-node=1 ~/bin/serialcombiner/comb_huge ~/data/serialcombiner/huge128K.in
```

If you have single threaded applications that do not need to use 16GB of RAM you can still run them in the mode above but it may not be the most efficient use of your SUs. At the moment there is no way to run multiple single-threaded programs in a single node. We are looking into this however and hope to have a solution to this problem in the future.

## Schedulers and Queuing Jobs on Avoca

There is a page discussing converting existing Tambo jobs scripts to run on Avoca.

The Blue Gene uses SLURM instead of PBS, it is not dissimilar, but it is different.  SLURM provides a more sophisticated scheduler for Blue Gene systems.

## Job script generator

To simplify the task of writing job submission scripts we provide an [interactive job script generator](https://www.vlsci.org.au/page/vlsci-job-script-generator-avoca).

## Basic Use of SLURM

### Submitting Jobs

To submit a job on Avoca, you generally use a SLURM script. Much of the information about how you want your job to run is in that script. Below is a SLURM script that will execute a simple program, mpi_test. It will be executed on 256 cores, run quite quickly but must finish within two hours because of the `--time=` statement.

```
#!/bin/sh
#SBATCH --time=2:0:0
#SBATCH --nodes=64
#SBATCH --job-name="mpi_test-example"
#SBATCH --output="mpi_test.out"
#SBATCH --account=ibm
srun /usr/local/examples/marksmpi/mpi_test
```

You can copy this script into your home directory, call it, perhaps, slurm-script and run it from there as a test with the command sbatch slurm-script.

Note that arguments that can be given to the sbatch command (used to submit a job script) can also be specified in the actual script itself. For example, putting `#SBATCH --nodes=64` in a script is the same as putting `--nodes=64` on the command line. However, many people find keeping all the relevant commands in the script is a good way of keeping track of lots of jobs.

### The various statements that belong in SLURM script

* `--time=2:0:0` Sets a time limit, in either "minutes", "minutes:seconds", "hours:minutes:seconds", "days-hours", "days-hours:minutes" or "days-hours:minutes:seconds". So the example above is setting a time limit of 2 hours. Thats a hard limit, if the job is still running after 2 hours, the scheduler will kill it.
* `--nodes` Sets the number of nodes required for the job. On the Blue Gene/Q a node is a compute card, with every node having sixteen cores. So requesting 64 nodes as in the example above will give you an allocation of 1024 cores.
* `--job-name` Sets the name that the job will be given. Make it easier to keep track of multiple jobs.
* `--output` Sets the filename for the output of the job script to be saved as. If not given the output will be written to a file named slurm-xxx.out, where xxx is the SLURM job number.
* `--account` If you are in multiple projects (with multiple accounts), you must specify which account you want this job to be recorded against (at the moment, changing your default account on the VLSCI Account Management System doesn't update it in SLURM's accounting system), for example: VRxxxx.

You will find a full list of the possible commands in a slurm script documented in the sbatch man page, try man sbatch. As always, you can always [submit a help request](help). Note that on the Blue Gene, some of the SLURM options listed in the man page don't make a lot of sense. You should choose your number or cores, procs and/or nodes with the --nodes= only, there are sixteen cores or procs to a node. You don't need any options relating to memory, each Blue Gene node has 16GB of RAM, and you don't ever share nodes with other users.

Some other optional statements mentioned in the man page will allow you to 

* indicate you are running under a different project than your default one,
* name the job and make tracking easier
* send email notifications (make sure you use both!).

The following are all the available possible job sizes for jobs running on Avoca. If you choose a job size that is not listed here SLURM will automatically round-up your request to the nearest job size supported. So, for example if you were to ask for 12 nodes, your job will be given 16 nodes, or if you ask for 40 nodes your job will be given 64. Note that you will be charged for the number of nodes that your job is given - 16 or 64 nodes in the earlier examples. So, you need to choose from one of the following job sizes:

* 1 nodes (16 cores) - minimum, smallest partition size
* 2 nodes (32 cores)
* 4 nodes (64 cores)
* 8 nodes (128 cores)
* 16 nodes (256 cores)
* 32 nodes (512 cores)
* 64 nodes (1024 cores)
* 128 nodes (2048 cores)
* 256 nodes (4096 cores)
* 512 nodes (8192 cores)
* 1024 nodes (16384 cores)
* 2048 nodes (32768 cores) - maximum
* 4096 nodes (65536 cores) - Only available under special arrangement

The `srun` command is used in your job scripts to execute programs on the Blue Gene. To use it, ensure that you have the vlsci module loaded (this will put the SLURM directories in your path).

To submit a script using sbatch just call sbatch and give it the name of the script:

```
sbatch example.script
```

This will return immediately with SLURM telling you the job id that it has assigned for your job:

```
Submitted batch job 5276
```

SLURM has now queued your job for submission. The output from the script will be saved to a file called `slurm-xxxx.out`, where xxxx is the SLURM job number. To save it to another file use the `--output=<filename>` option (or -o).

### Cancelling Jobs

To cancel a job (it can be running or still queued) the `scancel` command can be used. Give it the job id of the job you want cancelled, e.g. `scancel 5276`.

### Looking at the Queue

To see the state of the queue on Avoca use the squeue command. By default squeue shows you the output in the short format. For example:

```
markn@avoca:~> squeue
  JOBID PARTITION     NAME     USER  ST       TIME  NODES MIDPLANELIST(REASON)
   6929      main 20pi_100     aooi   R    2:02:39    128 bgq1000[20000x23331]
   5991      main PV1W+Dis jroberts   R 1-02:37:02    128 bgq1010[10000x13331]
   5990      main PV1W_201 jroberts   R 1-02:38:37    128 bgq1010[00000x03331]
   6731      main plycn_p0  creboul   R    6:35:40     64 bgq1010[20300x33331]
   6824      main 700_S173 jiapu_zh   R    5:40:36     16 bgq1010[30101x33131]
   6823      main 700_S173 jiapu_zh   R    5:40:40     16 bgq1010[20101x23131]
   6822      main 700_I214 jiapu_zh   R    5:40:48     16 bgq1010[30001x33031]
   6821      main 700_I214 jiapu_zh   R    5:40:53     16 bgq1010[20001x23031]
   7156      main sbatch_p     mike   R       2:10      4 bgq1010[30200x30230]
   7153      main sbatch_p     mike   R       5:53      4 bgq1010[21100x21130]
   7151      main sbatch_p     mike   R       7:11      4 bgq0010[33101x33131]
   7150      main sbatch_p     mike   R       7:27      4 bgq0010[02101x02131]
   7149      main sbatch_p     mike   R       7:52      4 bgq0010[21101x21131]
   7148      main sbatch_p     mike   R       7:57      4 bgq0010[01101x01131]
   7147      main sbatch_p     mike   R       9:06      4 bgq1010[22200x22230]
   7146      main sbatch_p     mike   R       9:26      4 bgq0010[33001x33031]
   7145      main sbatch_p     mike   R       9:29      4 bgq0010[32001x32031]
   7143      main sbatch_p     mike   R       9:57      4 bgq1010[23100x23130]
...
   7115      main     350K mingliu2   R      25:35      2 bgq0011[13201x13211]
   7116      main sbatch_p     mike   R      25:35      4 bgq1010[30100x30130]
   7118      main sbatch_p     mike   R      25:35      4 bgq1010[20201x20231]
```

This shows all the jobs that are currently running on the system, with the job name, the user, the job's state (R = Running, PD = Pending, CF = Configuring Hardware), how long the job has been running, how many nodes the job is using and where in the Blue Gene system the job is executing (MIDPLANELIST), or if it's not executing, why not (REASON).

If you want more information you can get the long form of the output by calling squeue with -l. For example:

```
markn@avoca:~> squeue -l
Mon Jul  2 16:17:44 2012
  JOBID PARTITION     NAME     USER    STATE       TIME TIMELIMIT  NODES MIDPLANELIST(REASON)
   6929      main 20pi_100     aooi  RUNNING    2:04:47  12:00:00    128 bgq1000[20000x23331]
   5991      main PV1W+Dis jroberts  RUNNING 1-02:39:10 2-00:00:00    128 bgq1010[10000x13331]
   5990      main PV1W_201 jroberts  RUNNING 1-02:40:45 4-00:00:00    128 bgq1010[00000x03331]
   6731      main plycn_p0  creboul  RUNNING    6:37:48 5-00:00:00     64 bgq1010[20300x33331]
   6824      main 700_S173 jiapu_zh  RUNNING    5:42:44  23:59:00     16 bgq1010[30101x33131]
   6823      main 700_S173 jiapu_zh  RUNNING    5:42:48  20:00:00     16 bgq1010[20101x23131]
   6822      main 700_I214 jiapu_zh  RUNNING    5:42:56  23:59:00     16 bgq1010[30001x33031]
   6821      main 700_I214 jiapu_zh  RUNNING    5:43:01  20:00:00     16 bgq1010[20001x23031]
   7156      main sbatch_p     mike  RUNNING       4:18 1-00:00:00      4 bgq1010[30200x30230]
   7153      main sbatch_p     mike  RUNNING       8:01 1-00:00:00      4 bgq1010[21100x21130]
   7151      main sbatch_p     mike  RUNNING       9:19 1-00:00:00      4 bgq0010[33101x33131]
   7150      main sbatch_p     mike  RUNNING       9:34 1-00:00:00      4 bgq0010[02101x02131]
   7149      main sbatch_p     mike  RUNNING       9:59 1-00:00:00      4 bgq0010[21101x21131]
   7148      main sbatch_p     mike  RUNNING      10:04 1-00:00:00      4 bgq0010[01101x01131]
   7147      main sbatch_p     mike  RUNNING      11:13 1-00:00:00      4 bgq1010[22200x22230]
   7146      main sbatch_p     mike  RUNNING      11:33 1-00:00:00      4 bgq0010[33001x33031]
   7145      main sbatch_p     mike  RUNNING      11:36 1-00:00:00      4 bgq0010[32001x32031]
   7143      main sbatch_p     mike  RUNNING      12:04 1-00:00:00      4 bgq1010[23100x23130]
...
   7115      main     350K mingliu2  RUNNING      27:42  10:30:00      2 bgq0011[13201x13211]
   7116      main sbatch_p     mike  RUNNING      27:42 1-00:00:00      4 bgq1010[30100x30130]
   7118      main sbatch_p     mike  RUNNING      27:42 1-00:00:00      4 bgq1010[20201x20231]
```

The long form of the output spells out the state that the job is in ("RUNNING", as opposed to "R" above) and also provides the time limit for each job (how much time the job has asked for).

You can even provide your own format string to dictate what the output will look like. For example, if I just wanted to see the job id, account/project, user, state and run time so far I could use the following:

```
markn@avoca:~> squeue -o "%.7i %.8a %.8u %.8T %.10M"
  JOBID  ACCOUNT     USER    STATE       TIME
   6929   vr0210     aooi  RUNNING    2:06:36
   5991   vr0069 jroberts  RUNNING 1-02:40:59
   5990   vr0069 jroberts  RUNNING 1-02:42:34
   6731   vr0071  creboul  RUNNING    6:39:37
   6824   vr0063 jiapu_zh  RUNNING    5:44:33
   6823   vr0063 jiapu_zh  RUNNING    5:44:37
   6822   vr0063 jiapu_zh  RUNNING    5:44:45
   6821   vr0063 jiapu_zh  RUNNING    5:44:50
   7156    vlsci     mike  RUNNING       6:07
   7153    vlsci     mike  RUNNING       9:50
   7151    vlsci     mike  RUNNING      11:08
   7150    vlsci     mike  RUNNING      11:23
   7149    vlsci     mike  RUNNING      11:48
   7148    vlsci     mike  RUNNING      11:53
   7147    vlsci     mike  RUNNING      13:02
   7146    vlsci     mike  RUNNING      13:22
   7145    vlsci     mike  RUNNING      13:25
   7143    vlsci     mike  RUNNING      13:53
...
   6566    vlsci     mike  RUNNING   13:05:17
   7116    vlsci     mike  RUNNING      29:31
   7118    vlsci     mike  RUNNING      29:31
```

See the man page for more details on formating output and other options to squeue.

Because Avoca is such a large machine, squeue can produce quite a large amount of output (which is why we have the `...` in the examples above). If you want to see only your jobs, use the `-u <username>` option.

```
markn@avoca:~> squeue -u markn
  JOBID PARTITION     NAME     USER  ST       TIME  NODES MIDPLANELIST(REASON)
   7169      main awesome     markn   R       0:08     64 bgq1000[00000x03330]
   7171      main awesome     markn   R       0:04     16 bgq0000[13000x13330]
   7168      main awesome     markn   R       0:11      8 bgq0000[22200x22231]
```

In order to provide a smooth transition to using just SLURM on Avoca, we've provided a showq wrapper script that provides output similar to the original Moab command (although it does not accept any options). For example:

```
markn@avoca:~> showq

active jobs------------------------
 JOBID                 USER      STATE   CPUS   TIME_LEFT             START_TIME
  6929                 aooi    RUNNING     2K     9:41:37    2012-07-02T14:12:57
  5991             jroberts    RUNNING     2K    21:07:13    2012-07-01T13:38:34
  5990             jroberts    RUNNING     2K  2-21:05:38    2012-07-01T13:36:59
  7169                markn    RUNNING   1024        8:36    2012-07-02T16:29:57
  6731              creboul    RUNNING   1024  4-17:08:35    2012-07-02T09:39:56
  7171                markn    RUNNING    256        8:40    2012-07-02T16:30:01
  6824          jiapu_zhang    RUNNING    256    18:02:39    2012-07-02T10:35:00
  6823          jiapu_zhang    RUNNING    256    14:03:35    2012-07-02T10:34:56
  6822          jiapu_zhang    RUNNING    256    18:02:27    2012-07-02T10:34:48
  6821          jiapu_zhang    RUNNING    256    14:03:22    2012-07-02T10:34:43
  7170                 mike    RUNNING     64    23:58:38    2012-07-02T16:29:59
  7168                markn    RUNNING    128        8:33    2012-07-02T16:29:54
  7165          mingliu2012    RUNNING     32    10:25:51    2012-07-02T16:27:12
  7163                 mike    RUNNING     64    23:54:04    2012-07-02T16:25:25
  7161                 mike    RUNNING     64    23:51:35    2012-07-02T16:22:56
  7156                 mike    RUNNING     64    23:42:05    2012-07-02T16:13:26
  7153                 mike    RUNNING     64    23:38:22    2012-07-02T16:09:43
  7151                 mike    RUNNING     64    23:37:04    2012-07-02T16:08:25
  7150                 mike    RUNNING     64    23:36:49    2012-07-02T16:08:10
  7149                 mike    RUNNING     64    23:36:24    2012-07-02T16:07:45
  7148                 mike    RUNNING     64    23:36:19    2012-07-02T16:07:40
  7147                 mike    RUNNING     64    23:35:10    2012-07-02T16:06:31
  7146                 mike    RUNNING     64    23:34:50    2012-07-02T16:06:11
  7145                 mike    RUNNING     64    23:34:47    2012-07-02T16:06:08
  7143                 mike    RUNNING     64    23:34:19    2012-07-02T16:05:40
...
  6568                 mike    RUNNING     64    10:52:13    2012-07-02T03:23:35
  7116                 mike    RUNNING     64    23:18:41    2012-07-02T15:50:03
  7118                 mike    RUNNING     64    23:18:41    2012-07-02T15:50:03

268 active jobs		34208 of 64K processors in use by local jobs	52.20%
			2138 of 4K nodes active				52.20%
				(8K procs/512 nodes unavailable		12.50%)

pending jobs----------------------
 JOBID                 USER     STATE   CPUS   TIMELIMIT    ESTIMATED_START_TIME

0 pending jobs

Total jobs: 268
```

## What's going on on Avoca?

### Getting more information about a job

To get more information about a job you can use `checkjob <jobid>`, which will show you a summary of the information SLURM has about your job. For example:

```
[markn@avoca mpi]$ checkjob 9226
JobId=9226 Name=awesome job
UserId=markn(589) GroupId=ibm(502)
Priority=8397 Account=ibm QOS=normal
JobState=RUNNING Reason=None Dependency=(null)
RunTime=00:00:05 TimeLimit=00:10:00 TimeMin=N/A
SubmitTime=2012-07-05T16:53:43 EligibleTime=2012-07-05T16:53:43
StartTime=2012-07-05T16:53:43 EndTime=2012-07-05T17:03:43
Partition=main AllocNode:Sid=avoca:11770
ReqMidplaneList=(null) ExcMidplaneList=(null)
MidplaneList=bgq0011[00301x03331]
BatchHost=avoca-la
NumNodes=16 NumCPUs=256 CPUs/Task=1 ReqS:C:T=*:*:*
Command=/vlsci/IBM/markn/mpi/ex2.script
WorkDir=/vlsci/IBM/markn/mpi
Block_ID=RMP29Ju115739861
```

`checkjob` on Avoca gives you just a summary of the information that SLURM has on your job. If you want to see it all, you can call `scontrol show job <jobid> -d`. For example:

```
[markn@avoca mpi]$ scontrol show job 9226 -d
JobId=9226 Name=awesome job
UserId=markn(589) GroupId=ibm(502)
Priority=8397 Account=ibm QOS=normal
JobState=RUNNING Reason=None Dependency=(null)
Requeue=0 Restarts=0 BatchFlag=1 ExitCode=0:0
DerivedExitCode=0:0
RunTime=00:02:45 TimeLimit=00:10:00 TimeMin=N/A
SubmitTime=2012-07-05T16:53:43 EligibleTime=2012-07-05T16:53:43
StartTime=2012-07-05T16:53:43 EndTime=2012-07-05T17:03:43
PreemptTime=None SuspendTime=None SecsPreSuspend=0
Partition=main AllocNode:Sid=avoca:11770
ReqMidplaneList=(null) ExcMidplaneList=(null)
MidplaneList=bgq0011[00301x03331]
BatchHost=avoca-la
NumNodes=16 NumCPUs=256 CPUs/Task=1 ReqS:C:T=*:*:*
CPUs=16*16
MinCPUsNode=1 MinMemoryNode=0 MinTmpDiskNode=0
Features=(null) Gres=(null) Reservation=(null)
Shared=OK Contiguous=0 Licenses=(null) Network=(null)
Command=/vlsci/IBM/markn/mpi/ex2.script
WorkDir=/vlsci/IBM/markn/mpi
Block_ID=RMP29Ju115739861
Connection=N,N,N,N Reboot=no Rotate=yes Geometry=1x4x1x4x1
CnloadImage=default
MloaderImage=/bgsys/drivers/ppcfloor/boot/firmware
IoloadImage=default
```

## Advanced SLURM usage

### Interactive allocations

There are often times when you want to run a series of very short jobs with small turnaround time between running each job. This can be very helpful for doing development or to debug code, where you find yourself making changes, recompiling then wanting to run a short test job before iterating through the whole process again.

With SLURM there is a way to ask for an allocation of a certain number of nodes, for a duration of time to do just this. You can do so using the `salloc` command. `salloc` takes as arguments on the command line the very same ones that would appear in your sbatch script: `--account` for your account (if you have more than one), `--nodes` for the number of nodes you're requesting, `--time` for the duration of time you'd like to use these nodes for, `--job-name` for the job name, etc.

`salloc` will block until the resources become available for your allocation (unless you use the `--immediate` option). For example to request 16 nodes (or 256 CPU cores) for 2 hours you would execute the following:

```
salloc -N 256 -t 2:0:0
```

SLURM will then allocate you a block and drop you into a shell where you can do your development and call srun:

```
salloc: Granted job allocation 7177
salloc: Block RMP29Ju093628970 is ready for job
```

Or if it cannot do so at this time it will queue the job and block after giving you output similar to:

```
salloc: Pending job allocation 7178
salloc: job 7178 queued and waiting for resources
```

As soon as you enter the shell that salloc returns you, the block has been allocated to you and consequently your quota will be used from that point onwards. So it's very important that once you've finished using the block you exit from that shell (otherwise you'll use up quota until the job goes over the time you've asked for from salloc and it gets killed):

```
exit
salloc: Relinquishing job allocation 7177
```

A trivial example would be:

```
salloc -N 16 -t 2:0:0
srun /vlsci/IBM/markn/mpi/mpi_array/mpi_arri_t-pcsamp
vim mpi_array.c
make mpi_array-pcsamp
srun /vlsci/IBM/markn/mpi/mpi_array/mpi_arri_t-pcsamp
vim mpi_array.c
make mpi_array-pcsamp
srun /vlsci/IBM/markn/mpi/mpi_array/mpi_arri_t-pcsamp
```

## Development/Testing jobs

Avoca has a handful of different queues that you can submit your job to, these are:

* `main`: this is the default queue that your job will end up in if you do not request a specific queue
* `testing`: this is a high priority queue meant for testing code and doing development - small, short jobs that require quick turnaround time

If you are doing development or you need to do a quick test run of a program to ensure that it runs you can submit your job to the testing queue. This will allow your job to start sooner than if it was submitted to the main queue.

For your job to be eligible to run in the testing queue it must request:

* nodes of less than or equal to four (64 cores or less)
* walltime of less than or equal to fifteen minutes

If you try to submit a job to the testing queue that is larger or longer running than these above restrictions, SLURM will not accept your job.

Queues in SLURM are called *partitions*, so in order to submit your job to the testing queue/partition, you must add the following to your batch script:

```
#SBATCH --partition=testing
```
