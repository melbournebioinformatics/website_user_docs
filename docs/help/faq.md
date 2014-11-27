# Frequently asked questions

## My Account

### How is my quota awarded?

The VLSCI Resource Allocation Scheme grants its users resources in Service Units (SUs). VLSCI deposits the quota part into your (VLSCI bank) account each quarter. The deposit is made up from a number of different resources and while we endeavour to get that mix right, it is not cast in stone and to some extent you may draw down on one component more than another. However, if you do so a lot and it upsets our total quantity of resources, we'll probably need to discuss it with you.

Overall, the RAS committee looks at the value of your application and compares it to the value you have requested. Your request may include components to cover x86 use; excess memory use; Blue Gene/P use; disk space and software costs.

### How is my quota used up? 

As you use resources at VLSCI, your (bank like) account is debited in the following manner:

* **x86 cpu hours**: One x86 CPU hour is one SU. For parallel jobs, of course, multiply by the number of cores used.
* **x86 excess memory use**: If you will be asking for a lot of memory per core, then you effectively make other cores unavailable to other users. VLSCI therefore 'charge' your account with a "memory surcharge" that is calculated to represent the value of the systems affected. See the memory section below for details. Note that on the Blue Gene, where you don't share nodes with other users, there is no excess memory use.
* **Blue Gene cpu hours**: Because of the greater cost efficiency of Blue Gene, you burn up Blue Gene hours at a much slower rate, however, you are probably running much bigger jobs too!

Additionally, the RAS Committee will have awarded you resources that do not show up in quarterly deposits or withdrawals, this may include:

* **Disk Space**: VLSCI considers one Giga Byte a month as one SU. So, a 100GBytes for six months represents 600 SU. You are allocated that storage when your project is initiated or renewed.

* **Software Costs**: If you have requested commercial software that is not apparently needed by other user groups, the cost, in dollars is considered by the RAS Committee as well.

### What is the Excess Memory Surcharge?

As mentioned above, if you request a lot of memory per core then you may starve other cores of memory, making them unusable. VLSCI has set some values that seem reasonable at present and uses them to calculate excess memory surcharge. These numbers are:

* Max memory per core with no memory surcharge = 16384MB. If your jobs request no more than this amount of memory, you will not see any excess memory charge.
* Excess Memory Rate= quarter of an SU per GigaByte ram per core per hours This is the rate your account is debited at for memory in excess of the above figure.

Here is a worked example, suppose an x86 job requests 128 cores with 20GB per core and while it requests a wall time of five days, it only runs for three.

```
Excess Memory = 20480MB - 16384MB= 4096MB = 4GB
Surcharge = 4 * (3 * 24) * 128 / 8 = 4608  SUs
```
The base charge for the job is 9,216 SUs so the addition of an extra 4608 SU has a medium impact!

However, if you ask for 32GB per core, then your excess memory charge would be

```
Surcharge = 16 * (3 * 24) * 128 / 8 = 18432 SU
```

Which *would* have a big impact on the amount of quota consumed.

It is therefore worthwhile ensuring you don't ask for a lot more memory than you need before then, if nothing else, it could make a lot of resources available to other users.

Please see the section below on memory usage.

### How do I see how much cpu quota do I have left?

Use the command `mybalance` to see how many CPU hours are left available to you in the current period.

Note that the quota management tool that VLSCI uses counts the usage of jobs that have not finished yet as well as those that have finished. Often a job will finish a lot earlier than you indicated in your wall time parameter, so the time available to you may appear to increase.

You will note that there is a Balance column, the scheduler needs to see enough quota in this column before it will run a job for you.

If you currently have jobs running, you will see a number in the Reserved column. This number represents how much quote has been reserved for running jobs (and therefore subtracted from Balance). The scheduler assumes a job runs for the full wall time so, as is usually the case, if the job finishes before then, you get a credit back when it does so. You can minimise this variation by setting a realistic wall time if possible.

### How do I see who has used up all the quota in my project?

Use the command mybalance it will show you a recent history of your project's quota including quota added and used.

### How do I see how much disk space I have used, or have left?

Use the command `mydisk` to see how much is used and left in your home directory. If you are in several projects, all are listed.

### I cannot find the application or binary I need, but I believe it is installed.

VLSCI uses a tool called modules to set up your environment for any particular application. While we arrange for some to be loaded by default, you probably need to choose to load the ones you want to use. You can see a full list of applications supported by modules by typing: `module avail`

You can then choose to load (or similarly unload) the default version of one of those modules (for example, gcc) with: `module load gcc`

You can pick a specific version of the software by specifying the full name, for example: `module load gcc/4.4.3`

### The software I need to use has some license restrictions and I find I cannot use it.

Some applications installed on the VLSCI systems are subject to license restrictions. These restrictions may limit who can use them (eg academic use only) or require you to cite the application when publishing work that involved use of the application. When you signed your personal account application form, you agreed to comply with any such restrictions. VLSCI keeps a register of specific applications and who has formally agreed to the terms that relate to that application. Please send an email to the help desk to be added to that register, you need to state clearly that you have read the license restrictions and agree to comply with them.

We regret that the restrictions associated with some applications prevent some people from being allowed to use them at all.

### I am in two (or more) different projects. How do I handle that?

VLSCI grants disk space and cpu cycles to projects rather than to individuals. Normally, your personal home directory sits within the project disk space. However, if you are associated with more than one project,  that home directory can clearly only exist within one of the project spaces.  This means that before you login you need to let the system know which project you want to use.  All quota and permissions will be set-up based on which project the system thinks you are logged in under.

To change your default project, login to the [VLSCI project management site](https://help.vlsci.unimelb.edu.au/user) select `My Projects` and click `Make default` next to the project you want to login under.  You need to do this the first time you login to a new project so a new home directory will be set-up for you.

For example, assume that you are in two projects, VR5200 and VR9999. Further, your user name is jsmith. Your real home directory is probably `/vlsci/VR5200` but you are now working on VR9999. When you logon, you will find (use the pwd command) that you are in `/vlsci/VR5200/jsmith`, if you work there on VR9999 your disk usage will be taken from the first project, not what you or your project manager desire. Any queued jobs will also be charged to your default project.

To avoid allocating disk and CPU usage to the wrong account, you can use the online tool to change your default project before logging in. It is possible to work with multiple projects without changing your default project, but you will need to be careful with group permissions and keeping track of which project you are charging jobs to. The safest way to launch jobs is to explicitly specify which project to charge. For instance, when you launch jobs on the cluster, add the command `#PBS -A VR5201` so the cpus usage is credited to the correct project, as well as `#PBS -W group_list=VR5201` to make sure that the permissions the job has when it runs are correct.

## Running jobs under the scheduler

### My job is taking longer than I expected, it might run out of wall time.

Jobs that run out of wall time will be automatically killed by the scheduler. As soon as you think this might happen, send a message to the help line telling us the job number and how much extra time it might need. We can sometimes extend the wall time and will do if we can.

### My job won't run, what is the problem?

There are a number of potential problems, let’s look at them one by one:

* A common problem is launching a job on a machine that you don't have quota on. Use mybalance to make sure you have quota for the porject and for the machine you are trying to launch the job on.  If you are in more that one project, make sure you use the correct account for the quota.

* My job is shown as *deferred* or *BatchHold* in `showq`: A common reason for this is that you have run out of CPU Quota. Try the  mybalance command and compare what you have available with what the job needs. The scheduler keeps a count of your usage (including jobs that have not finished yet) and prevents you using more than allowed. As running jobs often finish before the wall time you indicated, the usage associated with a running job may be reduced when the job finishes and your deferred job will start. Alternatively, sometimes the help desk can find extra cycles for you if you have run out, it’s worth asking.

* I have plenty of quota but it’s still deferred: VLSCI put a limit on how many jobs and CPUs you use simultaneously. Check the `mylimits` command to see what those limits are.

* I don't have many jobs running and hardly using any CPUs but it’s still deferred: If you have requested special hardware needs (ie) memory or cpus on the same node, you can often wait a bit longer until those resources are available. Occasionally, people ask for resources that will never become available such as ten cores on the one node or 250G ram. The scheduler, ever patient, will keep trying to run your jobs for you in the vain hope that we add some more hardware. Not a good idea, please qdel the job and reconfigure it.

* It is stuck in the *Idle* queue and there are plenty of CPUs unused. Well, sadly, sometimes having a suitable number of CPUs available is not enough. The scheduler manages a number of resources as well as CPU, most importantly memory. If a running job is using a lot of the memory on a node, it can leave none for the other CPUs. This means that those other CPUs cannot be used productively. Smart users tune their memory requirements so they can sneak in and use these idle cpus but if your job really needs more memory than available there is not much that can be done about it.

Please don't hesitate to ask the [help desk](help) what is going on. It is quite possible that there is a problem that’s easily fixed if you bring it to our attention or we can suggest an alternative, and possibly more productive way to run your jobs.

### How do you work out how many CPU Hours I have used?

VLSCI keeps a total of all jobs both finished and running and compares that with you quota for the period. Note that for jobs that have finished, it’s the actual run time times the number of CPUs but for running jobs, because the only indication the system has is the wall time you indicated, the scheduler assumes your job will run right through until wall time is exceeded and the job is killed. This is hopefully rare, most users wisely indicate a good safe wall time. So, when a job finished, your account often appears to get something back, it is the difference between actual run time and the indicated wall time. If you are running close to your quota, it’s sometimes worth ensuring your indicated wall times are not a great deal bigger than what you expect. That depends on, of course, you having a good idea of how long a job will run for, not always the case.

## Data and file space

### Is my data backed up?

Data stored on our systems is backed up. However we strongly recommend that you keep your own backups of important data. The storage system is RAID'ed and has redundant disks but that may not be enough if we are unlucky. However, VLSCI may be able to recover files you have recently accidentally deleted. Please contact the help desk as soon as you are aware of this need. Note that this is not the same as a backup protecting against systems failure, if VLSCI has an unlikely combination of disk failures, your files could be lost if you don't have your own backup system in place.

### I need a large data set for my research. How may that work?

VLSCI may be able to download and maintain the data set for you. In many cases, several users may need the same data and it is clearly a good idea to have just one copy using disk space and network bandwidth. The assumption here is that the data concerned is publicly available and you need only read access. Please contact the help desk.

### My project has a data set that all project members need to use. Where should I put it?

Just above your home directory, you will see the home directories of other members of your project. You will also see a directory, shared at that same level. All your project members will be able to write into this directory and files written there will, by default, be set so that other project members can use them. Space taken up by files in this shared directory contributes to your project's total allowed disk space usage.

Note that files you create under the `shared` directory will be readable by other members of your group but not, by default, writable. You can change their mode with the `chmod` command, for example, to ensure other group members can write to a file, `myfile`, you would chmod g+w myfile. You can also alter the default attributes a new file gets with umask. Generally, these sort of commands need be treated somewhat carefully!

### How do I see how much disk space I have available?

At VLSCI, disk space is granted to projects so you share the projects disk space with other members of your project. Each project has a disk volume and you can find the status of that volume with the `mydisk` command.

### What happens if I exceed my disk quota?

If the disk volume for your project is *full*, then neither you nor applications running under your name will be able to write to the disk. Other members of your project will also not be able to write.

## Memory

### Why do I need to tell the system how much memory I need?

Memory is a limited resource, if we tell the scheduler how much each job needs, it can fit jobs into slots on the system most efficiently. If you don't define your memory needs, the scheduler assumes you need the default figure, 1GB. That’s not a lot and many applications need more. If you actually try and use more than the scheduler thinks you should, it will kill your job. On the other hand, if you ask for (a lot) more than you need, you make it harder for the scheduler to squeeze you in and you waste resources. So it is important you get it right.

Remember, if you ask for more memory per core than that core's fair share of what’s available (typically 4GB, but depending on the system, as high as 16GB per core), then you probably make some other core on that node unavailable. Under those conditions, we need to charge your quota for those under-utilized cores as well as the ones you are actually using. Sad but true. Conversely, if you use less than typical memory per core, you often find that the scheduler can squeeze your job into one of those under-utilized cores and it gets to run straight away. It’s well worth looking at, see the Excess Memory Surcharge FAQ item.

### How do I tell the system how much memory I need?

Getting the memory setting right for your HPC run is an area where a lot of people have problems. First, it is important to realize that memory can be defined in two different ways, they are not interchangeable.

* For MPI parallel jobs, you tell the system how much memory you need per process (or *core*' or *cpu* depending on how the terms are used). Thats because (good) MPI applications can spread the load across all the processes running. The job itself is almost always launched (at the end of the script) with `mpiexec`. So, if your total job might need 100GB RAM and you are using 50 processors, the number you need is 2000MB, let’s call it 2500 to be safe, use `#PBS -l pvmem=2500mb`.

* Single cpu jobs work almost exactly the same as the individual mpi processes. But now, of course, you are talking about the total memory of the job. If the job still needs 100GB, it's `#PBS -l pvmem=100gb` and we know that the scheduler will need to search a bit harder to find that for you. That translates to possibly a longer wait in the queue.

* SMP or multithreaded applications are a different thing altogether. These sort of jobs must run on only one node and might use two through to eight cores. The job itself starts on just one core and is told (usually with a command line switch) how many cpus it should spread across. All processes share the one block of memory. In this model, you specify how much memory you need in total. Because this is a different model, you use a different PBS command to define the memory, its mem. Importantly, you also need to submit these kinds of jobs to a different queue, the smp queue, and you need to tell the scheduler how many cores to use on the node. Additionally, you usually need to tell the binary how many cores to use and this needs to match the number of core you asked the scheduler for. So an extract from your PBS script may look like this (assuming the job needs 100G and 8 processors):

```
#PBS -l mem=100gb #PBS -q smp ./myjob -n8 
```

### How much memory do I need?

This a lot harder question in many cases. With some applications and some problems is just a case of knowing how big a memory structure you are going to build but more often than not, some experimenting may be necessary. One approach is to run a small version of the job (or even a short version of the real thing) and see what memory is being used. As long as you expect it is going to remain stable throughout the job run you can add a safety margin and away you go ! When your job is running (on the x86 machines) there is a command you can use to see what’s happening on the compute nodes, first you need determine what node (or nodes) its running on and then ask ssh to run the whatson command on that node. Suppose its on barcoo036 for example:

```
ssh barcoo036 whatson
```

Look in the column under VSZ and you will see the number of K Bytes the process is actually using. Add something to be safe and express in `pvmem=100gb` statement in your PBS script.

## Avoca (Blue Gene/Q) FAQs

### How do the Quotas or Resource Grants work on Avoca?

Pretty much the same as on other systems at VLSCI. The Resource Allocation Committee grants you a certain quota of things like HPC CPU Hours or disk space and you get to use it in the same manner.

### Can I convert resources granted on one machine to another machine?

Yes, generally that can be done but it does depend on the availability of resources on both the machines in question. Note that one cpu hour on one machine is not the same as on another. For example, as the Blue Gene provides its CPU Hours in a great deal more cost effective manner than the x86 machines, you get a lot more CPU Hours for a Service Unit. Good when you go from x86 (merri to avoca) but might be a bit of a shock when going the other way. Please send a message to 
the [VLSCI help desk](help) for further or specific information.

### How do I see how my quota stands on Avoca?

VLSCI intends to have the same set of command to monitor your quota on all its machines. So use `mydisk`, `mylimits`, `mybalance` in exactly the same manner as you would on the x86 machines.

### What do you mean by the limit on jobs size?

We have placed a limit on how *big* a job can be. We measure that in node hours, so a job that uses a very large number of nodes can only run for a short period. Conversely, a job that uses the minimum number of nodes (64) can run a lot longer. We hope to relax those limits as we get a better understanding of our user's needs. You can see that limit using the `mylimits` command on Avoca.
