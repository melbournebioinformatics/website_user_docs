# Managing Storage

This page provides information on how storage is managed on the VLSCI HPC systems.

VLSCI now operates three main supercomputers, Barcoo, Merri and Avoca. When you first logon to one of them, you will find yourself in your default home directory. If you are in multiple projects you may have multiple home directories but only one default. You can change to your other home directories using the `cd` command. You can change your default home directory from the VLSCI Account Management tool, there is a link to it near the top right of this page.

At VLSCI, when your project is awarded resources, the award includes storage space and the quantity made available is related to what was requested in your RAS application. All the individual users in a project share the storage quota awarded to that project. So, while your home directory is private to you, the space it takes up contributes to the overall project's storage space usage. If your project's total storage exceeds the project's quota, you will not be able to save a new file until someone in the project has "cleaned up their storage space".

The project storage space for all VLSCI projects is arranged in a similar manner. Individual users home directories and one shared directory are grouped together in a directory named after the project. Users who need to share data with other project members should consider using the shared directory. Some projects, with large storage needs, have their shared directory setup differently so it can make a lot of storage available. In these cases, users probably need to ensure they work in the shared directory rather than their home directory, especially if working with large files.

VLSCI backs up your project space and can often recover files accidentally deleted files for you. However, there is a time limit and its therefore important that you contact Help (see right of this page) as soon as you become aware of needing help in this area.

## Storage Quota on Merri, Barcoo and Avoca

For all projects there is a quota which limits how much data the project can store in the /vlsci and (where applicable) /hsm file system. There are no quotas on the /scratch file systems. The quota operates differently depending on the file system:

1. For the /vlsci file system the quota limits the maximum amount of disk storage that all users of the project can consume.  If this limit is reached, attempts to write additional data will result in failure.  If your application checks return codes of write operations, it will be provided with an error code that indicates that the disk quota has been exceeded.  If your application doesn't check return codes properly, it might hang, crash or otherwise behave incorrectly when the quota is exceeded.

2. For the /hsm file system the quota limits the maximum amount of total storage (disk+tape) that all users of the project consume.  If this limit is exceeded, attempts to write additional data may result in failures similar to those described for the /vlsci file system.  You may also be unable to access files currently only resident on tape when your project is in a quota exceeded condition.
If you find yourself in the situation where your project has consumed all storage quota allocated, you and your fellow project members should delete any files that are no longer required to free up space.  On the /vlsci file system, the space consumed by the deleted files will immediately become available for use.  On the /hsm file system it may take up to 24 hours before the space consumed by the deleted files is again available for your use if the deleted files were only resident on tape.

You can find out how much storage your project is using as follows:

1. For the /vlsci filesystem use the command:
```
gpfsdf /vlsci/VRnnnn
```
where VRnnnn is your project name to determine current usage for an entire project.
You can also view a more detailed report on the usage by individual user within a project using the command:
```
cat /vlsci/VRnnnn/usage.report
```
Note that this report is only updated once every 24 hours.

2. For the /hsm filesystem use the command:
```
cat /hsm/VRnnnn/usage.report
```
where VRnnnn is your project name to determine current usage for the entire project as well as individual users within the project.  Note that this report is only updated once every 24 hours.

If your project has insufficient storage quota to effectively meet its stated research goals, the project leader should send a request and justification for additional storage quota by submitting a [help request](help@vlsci.unimelb.edu.au).

## More About Storage on Merri, Barcoo and Avoca

The Avoca, Barcoo and Merri HPC systems share a common storage infrastructure.  This means your home directory and shared project directory are common across these two systems.  If you create files on one system, you will be able to read, modify or delete those files from the other system.

The common storage infrastructure shared by Avoca, Barcoo and Merri has three separate storage areas that are potentially accessible by users of Avoca, Barcoo and Merri:

1. The /vlsci file system which uses disk storage devices.   The /vlsci filesystem is backed up daily.

2. The /hsm file system which uses a combination of disk and tape storage devices.  This file system keeps recently accessed files on disk, but may migrate large (>10MB) inactive files to tape. Should inactive files be subsequently accessed, they will automatically be migrated back to disk after a short delay of a few minutes.  The /hsm file system is backed up daily.

2. The /scratch file system which uses disk storage devices.  This file system is intended for temporary files that may be generated while jobs are executing on either Avoca, Barcoo and Merri.  When a job is executed on either Avoca, Barcoo and MERRI, the environment variable TMPDIR points to a directory on the /scratch file system where the job can write temporary files.  Files stored in this directory are deleted when the job ends.  The /scratch file system is not backed up.

**/vlsci**

Projects with modest storage requirements have both user home directories and the project shared directory allocated in the /vlsci file system.  Such projects cannot access the /hsm file system.

**/hsm**

Projects with large storage requirements (>=10TB) have user home directories allocated in the /vlsci file system, and the project shared directory allocated in the /hsm file system.

**/scratch**

Projects with large temporary storage requirements that must persist between successive jobs can request a dedicated temporary storage area on the /scratch file system for their project.  Requests can be made by project leaders using the VLSCI user portal.

The policy for such /scratch allocations is:

* Scratch space is intended for the temporary storage of files spanning jobs that may rely on the output of other jobs.
* Scratch space will have no disk quota limits applied (subject to availability).
* Files are not backed up.
* Files that have not been accessed for more than 30 days will be deleted.
* If space is needed, files will be deleted (oldest and largest files first) to ensure usable free space is available.
