# The file system

Unix provides a *file system* to store and organise data which resides on permanent storage such as disk drives (the actual storage media could be any technology). The file system consists of directories and files. If you are a Windows or Mac user, directories represent the same concept as *folders* on those systems. Files contain data and programs, and directories contain files and possibly other directories, thus forming a hierarchy. At the top of the hierarchy is the *root* directory, which is usually denoted by forward-slash character (/).

The diagram below illustrates a small part of a typical Unix file system, rendered as an upside down tree (the root is at the top and the branches extend downwards). The example is for illustrative purposes only, the file system on a real computer would contain many more files and directories.

![Example file system tree](using_unix/img/filesystem_tree.png)

In the diagram directories are indicated by red ellipses and files are indicated by yellow rectangles. Note that directories may contain other (sub) directories and also files, and directories can be empty too. The names of the files and directories have been chosen from a real system, but they are not significant in this discussion. However, there are a few things to note about names in general:

1. The syntax for names does not distinguish between directories and files.

2. Names do not have to be unique. In the example above there are three directories with the name `bin`.

3. On most Unix systems file names are case sensitive.

4. Filenames may contain space characters, but this practice is often discouraged because some older Unix tools don’t handle them well.

As we shall see shortly, Unix provides a naming scheme called *file paths* which accommodates duplicate names but allows us to refer to things unambiguously.

## The home directory

Each user has their own private directory called their *home*, where they can store their own files and subdirectories. You can discover the name of your home directory with the following command:

```
$ echo $HOME
/home/username
```

The exact location of your home directory will vary from system to system, but it will usually end in your username.

## The working directory

The working directory indicates the place in the file system where you are working. Many commands operate on files in the working directory by default unless you specify otherwise. When you first log into the computer your working directory is set to be your home directory. You can discover the name of the working directory with the `pwd` command (for "print working directory"):

```
$ pwd
/usr/local/bin
```

You can change the working directory with the `cd` command (more on this later).

## File paths

A *file path* identifies files and directories within the context of the file system hierarchy. They are called "paths" because they are written as a sequence of steps between two places. The steps indicate which directories you must traverse to get from the start to the end of the path. There are two basic forms of file paths, distinguished by where they start: 

1. *Absolute file paths*: start at the root directory, for example: `/usr/local/bin`

2. *Relative file paths*: start at the working directory, for example: `src/C/Makefile` (note the absence of a forward-slash at the start of the path).

Unix uses the forward-slash character as a path separator. The notation `D/F` indicates that `F` is contained in the directory `D`. The root directory is just a special case with nothing on the left of the slash.

Consider this absolute path as an example:

```
/usr/local/bin/whatson
```

* `usr` is a directory contained in the `/` (root) directory.

* `local` is a directory contained in the `/usr` directory.

* `bin` is a directory contained in the `/usr/local` directory.

* `whatson` is a file (or perhaps a directory) contained in the `/usr/local/bin` directory.

Relative file paths are interpreted by appending them onto the working directory. For example, suppose we have the following relative path:

```
src/fractal.c
```

If the working directory is:

```
/home/username
```

then the absolute path of the file would be:

```
/home/username/src/fractal.c
```

Absolute paths unambiguously refer to a particular file or directory on the system, but they can become quite long and thus tedious to type. Relative paths are interpreted with respect to the working directory, which means that they are usually short. Indeed, the relative path of any file in your working directory is just the name of the file on its own - it couldn’t be any shorter than that!

### Special file path names

Unix also provides a few shorthand notations for certain paths:

* The tilde character (`~`) refers to your home directory, for example:

```
~/src/fractal.c
```

is equivalent to:

```
/home/username/src/fractal.c
```

if your home directory is:

```
/home/username/
```

* The single dot (.) refers to the working directory, for example:

```
./README.txt
```

is equivalent to:
	
```
README.txt
```

* The double dot (`..`) refers to the parent directory, for example:

```
../../sequences.txt
```

refers to the file `sequences.txt` in the parent of the parent (i.e. grandparent) of the working directory.

## Listing the contents of a directory

The contents of a directory can be listed with the `ls` command (lower case `LS`). In its simplest form it lists the names of the immediate contents of the working directory in alphabetical order:

```
$ ls
aadvark.c  a.out  data  memo.txt  zebra.hs
```

You can ask `ls` to give you a longer, more detailed listing, by passing it the `-l` (lower case `L`, for "long") argument:

```
$ ls -l
total 88
-rw-r--r-- 1 foo	 staff    94 Feb  9  9:24 aadvark.c
-rwxr-xr-x 1 foo	 staff  6713 Mar  9 15:24 a.out
drwxr-xr-x 2 foo	 guest  4096 Mar  9 15:21 data
-rw-r--r-- 1 fred	 student  43 Jan 12 20:26 memo.txt
-rw-r--r-- 1 wilma	 student  12 Sep 19  2011 zebra.hs
```

The text `total 88` means that the current directory consumes 88 disk blocks, which says something about how much space it requires. This is a low-level detail that is not particularly important for the current discussion.

Next we see a list of information about five files. There are nine columns in each line which provide the following details:

1. Permissions of the file (who can read/write/execute it), e.g. `-rwxr-xr-x`

2. Number of links to the file (refer to information about file system links), e.g. `2`

3. Name of the user who owns the file, e.g. `fred`

4. Name of the user group associated with the file, e.g. `student`

5. Size of the file in bytes, e.g. `43`

6-8. 	Date/time that the file was last changed, e.g. `Mar 9 15:24`

9.   Name of the file or directory, e.g.  `memo.txt`

The `ls` command accepts an optional list of file paths as arguments. This allows you to list the contents of the named directories rather than just the working directory. For example:

```
$ ls ~/bin
c2hs  killmyjobs.sh
```

### Hidden files

By default the `ls` command does not display information about files whose names start with a dot character (these are often called *dot-files*). If you want to see those files then you can use the `-a` argument to list *all* files. The most common place to find dot-files is in your home directory. These are often configuration files that control the behaviour of programs that you use. For example, the file `~/.bashrc` contains configuration options for the BASH shell.

## Changing the working directory

When you log into a Unix computer your working directory is set to your home directory. You can change the working directory with the `cd` command (for "change directory"):

```
$ cd /usr/local/
```

The `cd` command accepts a file path as its argument, which specifies the new desired value of the working directory. The command will fail if the directory does not exist or you don’t have permission to read it. If you run the `cd` command without any arguments then it will set the working directory to your home directory.

## Making directories

You can create your own directory using the `mkdir` command:

```
$ mkdir test
```

The `mkdir` command accepts one or more file paths as its arguments which specify the names of the directories that you want to create. It will fail to create a directory if it already exists or if you have insufficient permissions.

## Copying and moving (renaming) files

You can copy one or more files from one place in the file system to another using the `cp` command:

```
$ cp .bashrc .bashrc.backup
```

The above example copies the file `.bashrc` to `.bashrc.backup` (and does not change `.bashrc`).

The file(s) being copied are called *sources* and the place being copied to is called the *destination*. If there is one source then the destination could be either a file or a directory, but if there are multiple sources then the destination must be a directory (because it is not possible to copy multiple files into a single file). If the destination is a directory then the sources are copied into the directory keeping their original names.

The `cp` command will not copy directories by default, but this can be overridden with the `-R` flag, which will cause `cp` to copy the contents of a directory recursively (that is the directory will be copied, plus all its files and subdirectories transitively).

You can move (rename) a file or a directory using the `mv` command:

```
$ mv .bashrc .bashrc.old
```

The above example renames the file `.bashrc` to the new name `.bashrc.old`.

__Warning: by default the cp and mv commands will overwrite a destination file if it already exists. This means you will lose the old contents of the destination file!__


## Removing files and directories

You can remove files with the `rm` command and remove directories with the `rmdir` command. 

The following command will remove the file called `.bashrc.backup` from the working directory:

```
$ rm .bashrc.backup
```

The following command will remove the directory called test from the working directory:

```
$ rmdir test
```

Note that `rmdir` only removes empty directories. If you want to recursively remove a directory and all its files (and subdirectories and so on) then you can pass the `-r` argument to `rm`:

```
$ rm -r test
```

__Warning: rm is a dangerous command (especially with the -r argument), it can cause you to lose precious data if you accidentally remove the wrong things. You must always maintain up-to-date backups of all your important files! Each time you use the rm command you should pause for a moment and ask yourself "do I really want to do this?". Some people use the -i argument which causes rm to prompt you before each removal.__


## File permissions

Every file and directory in the file system has permission attributes which specify whether the file may be read, written (modified) or executed by certain classes of users. The precise meaning of read, write and execute is explained by the table below (note the differences between files and directories):


<table border="1" padding="2">
  <tr>
    <td></td>
    <td>read</td>
    <td>write</td>
    <td>execute</td>
  </tr>
  <tr>
    <td>file</td>
    <td>Contents of file may be viewed.</td>
    <td>Contents of file may be changed.</td>
    <td>File may be executed as a program.</td>
  </tr>
  <tr>
    <td>directory</td>
    <td>A list of the directory contents may be viewed (i.e. user can ls the directory).</td>
    <td>User may create and remove files in the directory.</td>
    <td>The directory may be traversed by the user (i.e. user may cd into the directory).</td>
  </tr>
</table>

<p></p>

The permissions are further divided into three categories of users:

1. The owner of the file.

2. The user group associated with the file.

3. All other users on the computer.

The owner of the file is normally the user who created it. 

Each user belongs to one or more *groups* on the system. This provides a simple way to share files with a limited selection of users. You can find out which groups you are in with the groups command:

```
$ groups
guest tutorial printer
```

By setting the group permissions on files appropriately you can share them with other users on the system in the same group. Unfortunately each file can only have a single group at any one time, even though each user can belong to many groups.

The "other users" permissions apply to anyone who is not the owner of the file and not a member of the group of the file, in other words everyone else.

Recall from above that the output of the command `ls -l` shows, amongst other things, the permissions of the files in a directory, written as a ten character string like so:

```
-rwxr-x---
```

The meaning of each of the ten characters from left to right is as follows:

1. File type. Dash (`-`) means it is a regular file, (`d`) means it is a directory.

2-4. 	Owner read (`r`), write (`w`), execute (`x`).

5-7. 	Group read (`r`), write (`w`), execute (`x`).

8-10. 	Other read (`r`), write (`w`), execute (`x`).

A dash (-) in any of the read, write or execute positions means that the corresponding permission is denied.

The example `-rwxr-x---` can be decoded like so:

* It is a regular file (first character is a dash).

* The owner of the file can read, write and execute it (characters 2-4 are `rwx`).

* Any user in the group of the file can read and execute it, but they cannot write it (characters 5-7 are `r-x`).

* Everyone else who is not the owner of the file and not in the group of the file cannot read, write or execute it (characters 8-10 are `---`).

### Changing the permissions on a file

You can change the permissions of a file or directory using the `chmod` command. For example to add group read and write permissions to the file `fractal.c` we could use the command:

```
$ chmod g+rw fractal.c
```

The syntax of the chmod command is complicated, but basically it follows the pattern:

```
chmod user-code operator permission-string files
```

where:

* *user-code* is one of `u`,`g`,`o`,`a`

    * `u` : the owner of the file.

    * `g` : any user in the group of the file.

    * `o` : any user who is not the owner and not in the group of the file.

    * `a` : all users.

* *operator* is one of `+`,`-`,`=`

    * `+` : add these new permissions to the current permissions for the file.

    * `-` : remove these permissions from the current permissions of the file.

    * `=` : set these permissions of the file to be exactly these ones specified (but all other permissions remain unchanged).

* *permission-string* is some combination of `r`,`w`,`x`

More example uses of `chmod`:

* Add read and execute permissions for everyone to the file `myprog`:

```
$ chmod a+rx myprog
```

* Remove read permissions for anyone who is neither the owner of, nor in the group of the file `myprog`:

```
$ chmod o-r myprog
```

* Set the permissions of the file `myprog` to be read and execute to the owner (and leave the permissions of other users unchanged):

```
$ chmod u=rx myprog
```

The `chmod` command accepts a `-R` argument which causes it to recursively change the permissions in all the files and subdirectories of a specified directory, for example:

```
$ chmod -R g+rw my_data_files
```

### Changing the group of a file

You can change the group of a file or directory with the `chgrp` command, for example:

```
$ chgrp guest fractal.c
```

The first argument is the new group name and the rest of the arguments are the files and/or directories that you want to change.

The `chgrp` command accepts a `-R` argument which causes it to recursively change the group in all the files and subdirectories of a specified directory, for example:

```
$ chgrp -R staff my_data_files
```

### How to keep your files private

It is important to know how to keep your confidential data private, but as you can see from the discussion above, Unix file permissions can be complicated.

At VLSCI we have hundreds of users and most of them are working on research projects. Due to the nature of their work, our users typically want to share data with people in their research project but hide it from everyone else. To facilitate this, on our systems, each user belongs to one or more projects, and each project has a corresponding Unix group of the form VRwxyz, where wxyz is a unique four digit number, for example VR1234.

For research related data, we recommend that you only provide read and write access to the owner of the file (that is you) and the group of the file (which should be your research group). You should also restrict execute permissions on directories to the owner and group. Or in summary, the permissions string on confidential files and directories should have three dashes (`---`) on the far right hand side. An easy way to ensure this is set for a directory called, say `my_data`, is to use this command:

```
$ chmod -R ug=rwX,o= my_data
```

This recursively sets the permissions on files to be readable and writable to the owner and group but not to anyone else. Note carefully that the `X` is a capital letter. This is a special feature of `chmod` which sets the execute permissions on files and directories which were already executable to some user (this ensures that execute permissions are given to directories and programs but not to normal files).

If you belong to multiple projects then you have to be careful which group is set for your files and directories.

## File name patterns

In many circumstances you find yourself wanting to run the same command on a large collection of files whose names match a certain pattern. For instance you might like to remove group read permissions on all (and only) those files ending in "`.txt`". If there are lots of files that match the pattern then it is unlikely you will want to enter the same command once for every file. Fortunately the shell provides a concise language of patterns which solve this problem:

```
$ chmod g-r *.txt
```

In the example above we have used the pattern `*.txt` to match any file name which ends in the four characters "`.txt`" (which includes files with no characters to the left of the dot). The shell automatically replaces any pattern in a command with all of the file names it finds which match the pattern.

There are many things you can do with file name patterns, but the two key operators you need to know are the asterisk (`*`) and the question mark (`?`). The asterisk matches strings of length zero or greater, whereas the question mark matches any single character.

Some example patterns:

* All files that end in "`.c`":

```
*.c
```

* All files that start with "`fred`" and end in anything:

```
fred*
```

* All files that have "`fred`" somewhere in their name:

```
*fred*
```

* All files that start with "`sequence.`" and end with any three characters:

```
sequence.???
```

## Copying files between machines

You can copy files between your local computer and a remote computer using the `scp` ("secure copy") command. If your local computer uses Mac OS X or Linux then you can use `scp` from the command line. However, if your local computer uses Windows then we recommend you use the WinSCP tool:

[http://winscp.net/eng/index.php](http://winscp.net/eng/index.php)

The principals of `scp` are the same for all systems, but WinSCP uses a graphical interface instead of a command line. For the sake of brevity we show the syntax for the command line in this document and refer Windows users to the WinSCP manual:

[http://winscp.net/eng/docs/start](http://winscp.net/eng/docs/start)

To copy a file from your local computer to a remote compute (say Merri at VLSCI) you use the syntax:

```
scp source user@host:directory/dest
```

where *source* is the name of the file on your local computer, *directory*/*dest* is the path for the file on the remote computer (the place to put it), and *user* is your username on the remote computer. If your username is the same on the local and remote computers then you can omit it in the command. Relative destination paths are interpreted with respect to your home directory on the remote machine (which means an empty destination path refers to your home directory itself). Here is an example which copies the file `secrets.txt` from the local computer to `$HOME/private` on merri:

```
$ scp secrets.txt username@merri.vlsci.unimelb.edu.au:private/
```

(You should replace `username` with your actual username on the remote computer.)

To copy a file from the remote computer to your local computer you use the syntax:

```
scp user@host:directory/source dest
```

The same rules apply as before but *directory*/*source *is the path of the file on the remote computer and *dest* is the place to put it on the local computer. Here is an example which copies `$HOME/private/secrets.txt` on the remote computer to the working directory on the local computer:

```
$ scp username@merri.vlsci.unimelb.edu.au:private/secrets.txt .
```

(Note the dot at the end which refers to the working directory on the local computer).

The `scp` command supports a `-r` argument which tells it to copy entire directories recursively in a similar fashion to `cp -R`.
