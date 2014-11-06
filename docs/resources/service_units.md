# Service Units

When allocating systems to projects, the Resource Allocation Scheme Committee considers the scale of the project and the software being used and allocates a portion of Service Units (SU) to the project. 

A SU takes into account the fact that CPU hours vary depending on different hardware, memory requirements, disk space, and software licensing needs. It is calculated as follows:

One SU is equivalent to:

* One CPU hour on the older x86 system (Merri)
     * eg. a single core job running for 10 hours will need 10 SU.  Similarly, a 10 core job running for 1 hour will need 10 SU.
* 0.8 CPU hours on the newer x86 system (Barcoo). (The cores on this system are more efficient than the older systems.)
     * eg. a single core job running for 10 hours will need 12.5 SU.  Similarly, a 10 core job running for 1 hour will need 12.5 SU.
* 1/4 of a node card on the Blue Gene system (Avoca).  On the Blue Gene/Q, all jobs are charged per node, i.e. in units of 4 SU.  The more tasks that you can run per node, the better SU value you get.
     * e.g. a 2 node job running for 1 hour needs 8 SU, irrespective of if it uses 1 task per node or 64 tasks per node.
* Each 8GB RAM per core above 16GB.
     * eg. a 16 core job running for one hour and needing 24 GB per core will run up a memory charge of 16 SU ((24-16)/8 *16cores) in addition to the 16 SU for the CPU hours.  Note that this does not apply to the Blue Gene/Q.
* One GB per month of disk.
     * e.g. 100 GB disk for the year represents 100*12 = 1200 SU

Currently there is no charge for using the XeonPhi processors on Barcoo, however, as this also includes using the x86 cores, there is a charge for that component (at the same rate as above).

Further details about this in relation to an application for resources at VLSCI is included in the help text when completing applications for resources at VLSCI.

## How is RAS quota awarded?

See the [Resource Allocation Scheme](https://www.vlsci.org.au/page/ras) for information on awarding quota.

## How does quota get consumed?

As you use resources at VLSCI, your (bank like) account is debited in the following manner:

* **x86 CPU hours** One x86 CPU hour is one SU. For parallel jobs, of course, multiply by the number of cores used.
* **x86 memory use** If you will be asking for a lot of memory per core, then you effectively make other cores unavailable to other users. VLSCI therefore charge your account with a memory surcharge that is calculated to represent the value of the systems affected. See the memory section below for details.
* **Blue Gene CPU hours** Because of the greater cost efficiency of Blue Gene, you burn up Blue Gene hours at a much slower rate, however, you are probably running much bigger jobs too! Note that you don't share nodes with other users on the Blue Gene, so there is no excess memory use.

Additionally, the RAS Committee will have awarded you resources that do not show up in quarterly deposits or withdrawals, this may include:

* **Disk Space** VLSCI considers one gigabyte (GB) a month as one SU. So, a 100 GB for six months represents 600 SU. You are allocated that storage when your project is initiated or renewed.
* **Software Costs** If you have requested commercial software that is not apparently needed by other user groups, the cost, in dollars is considered by the RAS Committee as well. 

So, in summary, at present (and it will change in the future) you need one Service Unit (SU) for each:

* One CPU hour on the older x86 system (Merri)
    * eg. a single core job running for 10 hours will need 10 SU.  Similarly, a 10 core job running for 1 hour will need 10 SU.
* 0.8 CPU hours on the newer x86 system (Barcoo). (The cores on this system are more efficient than the older systems.)
    * eg. a single core job running for 10 hours will need 12.5 SU.  Similarly, a 10 core job running for 1 hour will need 12.5 SU.
* 1/4 of a node card on the Blue Gene system (Avoca).  On the Blue Gene/Q, all jobs are charged per node, i.e. in units of 4 SU.  The more tasks that you can run per node, the better SU value you get.
    * e.g. a 2 node job running for 1 hour needs 8 SU, irrespective of if it uses 1 task per node or 64 tasks per node.
* Each 8GB RAM per core above 16GB.
    * eg. a 16 core job running for one hour and needing 24 GB per core will run up a memory charge of 16 SU ((24-16)/8 *16cores) in addition to the 16 SU for the CPU hours.  Note that this does not apply to the Blue Gene/Q.
* One GB per month of disk.
    * e.g. 100 GB disk for the year represents 100*12 = 1200 SU

Currently there is no charge for using the XeonPhi processors on Barcoo, however, as this also includes using the x86 cores, there is a charge for that component.  (At the same rate as above.)

## How do I see how much CPU quota do I have left ?

Use the command `mybalance` to see how many CPU hours are left available to you in the current period.

Note that the quota management tool that VLSCI uses counts the usage of jobs that have not finished yet as well as those that have finished. Often a job will finish earlier than you indicated in your wall time parameter, so the time available to you may appear to increase.

You will note that there is a Balance column, the scheduler needs to see enough quota in this column before it will run a job for you.

If you currently have jobs running, you will see a number in the Reserved column. This number represents how much quote has been reserved for running jobs (and therefore subtracted from Balance). The scheduler assumes a job runs for the full wall time so, as is usually the case, if the job finishes before then, you get a credit back when it does so. You can minimise this variation by setting a realistic wall time if possible. 

## How do I see who has used up all the quota in my project ?

The `mybalance` command will show you a summary of your project's quota including quota added and used.

## Multiple Projects

Some people are in multiple projects. When they run a job, the accounting system will charge their default project for that job unless they specify otherwise. Generally, it's easy to tell the system that this job needs to be charged to some other project, use the `#SBATCH --account VRxxxx` in a slurm script. You can also include the same instruction on the sbatch line. Needless to say, you must substitute a real project that you are a member of for the `VRxxxx`.

Alternatively, if you will be working in the 'new' project for some time, it may be easier to change your default project using the User Portal.

## What if I don't use my quota?

It's important to understand that quota, or compute cycles cannot be 'banked' or put aside to use later. As time goes by, any unused quota is lost forever. If a project does not need a substantial part of its allocation in the forthcoming quarter they should inform VLSCI so that it can be allocated to another project. In many cases, this handing back process can be reversed if circumstances change.

VLSCI may reduce a project's quarterly allocation if the previous quarter's quota was substantially unused.
