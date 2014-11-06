# Managing Memory

## Service charge for memory usage on the x86 clusters

Memory in HPC systems is just as valuable (and expensive) as other vital resources such as CPU and disk space.  Jobs which request a lot of memory per cpu (also referred to as core, process, task) can starve other cpus of memory, making them unusable. Consequently, jobs which use more than a certain amount of memory are charged for that usage. Note that this charge only applies to the x86 clusters (barcoo and merri) but not the Blue Gene (avoca).

Memory requests are charged as a surcharge -- after the job has finished.  The charge is calculated and deducted from the project's quota overnight.  It is worth checking that the surcharge has not effected any waiting jobs that may now not have enough quota.

## Surcharge rate

Any memory request of more than 16GB memory per CPU cpu will be charged the memory surcharge. The charge is a the rate of 1/8 SU, per additional GB, per hour for memory greater than 16GB.

Here is a formula which calculates the charge based on the number of cpu requested, the amount of memory requested per cpu, and the number of hours for which the job runs:

```
mem_charge(cpu, memory_per_cpu, hours) =  cpu * hours * (memory_per_cpu - 16GB)/8
```

Note that this is in addition to the normal quota that would be charged (= cpu * hours), so in effect the surcharge is a multiplier.  That is, for every 1 SU for cpu usage the job will incur an additional `(memory_per_cpu - 16GB)/8` SU for memory usage.

Here is a worked example: an x86 job requests 128 cores with 20GB per cpu and runs for three days:

cpu usage charge:  128 * (3 * 24)

memory surcharge: 128 * (3 * 24)  *  (20 - 16)/8

Total charge: 9,216 + 9,216 * 0.5 = 13824

Therefore, asking for 20GB per cpu (4GB above the threshold) adds half the cpu amount, this can have a significant impact!

# How to tell the system how much memory a job needs?

You must tell the (x86) systems if you need more than 1GByte memory per process

The way the memory request is made depends on the type of job you need to run. It is important not to mix up the two methods as this will mean that the job will not get the resources you intended.

For single cpu and multi-cpu MPI jobs, each cpu has its own private memory allocation, requested by the option: `--mem-per-cpu=`

For SMP or multithreaded jobs, all available memory needs to be accessible to all cpus (or threads). To access memory in this way, the memory needs to be physically on one node (motherboard).  This type of memory is requested by the option: `--mem=`

For MPI parallel and single jobs you tell the system how much memory you need per cpu (i.e. divide the total need by the number of cpu requested).

### MPI example:

```
# A parallel job using 32 cores and a total of 64GBytes Ram (= 2GBytes per core)
#SBATCH --ntasks=32
#SBATCH --mem-per-cpu=2048
#SBATCH --time=10:00:00
mpirun my_mpi_job
```

### Single cpu example:

```
# A serial or single cpu job using 4GBytes Ram
#SBATCH --ntasks=1
#SBATCH --mem-per-cpu=4096
#SBATCH --time=10:00:00
myjob
```

### SMP example:

For SMP or multithreaded applications you request the total memory required and specify that it is to be used exclusively for your job.

```
#SBATCH --mem=102400
#SBATCH --exclusive
./myjob -n$SLURM_CPUS_ON_NODE     # if your program needs to know the number of cpu, use $SLURM_CPUS_ON_NODE
```

## How much memory do I need ?

Unfortunately determining the maximum amount of memory that a job needs requires experimentation.  One approach is to run a small version of the job (or a short version of the real thing) and see what memory is being used.

If your job uses more memory than you requested, it will be killed.  The error message is unlikely to say that it way killed due to memory (depends on the program).  Most likely there will be a message similar to: `signal 9 (Killed)`.

To see how much memory a job used use the sacct command and view the maximum virtual memory size.  For example, for a job number of JOBID, use:

```
sacct -j JOBID --format=jobid,maxvmsize
```

this displays the maximum virtual memory size used by the program (in MB). This command can only be used after the job has finished.

Note that for MPI jobs we recomend using mpirun, however the memory usage will be wrong in this case.

To profile memory for MPI jobs please use `srun`, but note that the run time maybe slower.
