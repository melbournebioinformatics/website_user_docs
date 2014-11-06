# Processes

## Programs and the search path

Almost every command you run in Unix is just an ordinary program which resides in the file system somewhere (although a small number of commands are built into the shell directly). 

Unix programs are found in executable files, and come in two flavours:

1. Binary files which contain machine instructions.

2. Text files which contain scripts.

The programs which contain machine instructions are loaded into memory and executed directly by the computer’s hardware, whereas the programs which contain scripts contain text commands to be executed by an interpreter (e.g. BASH, Python, Perl).

Each time you enter a command, the shell parses what you typed and determines the name of the program to run, which is often just the first word of the command text. For example, in the following the command the program to run is called `chmod`:

```
$ chmod -R ug=rwX,o= my_data
```

Once the shell has determined the name of the program to run it searches the file system for an executable file with the same name. This raises some important questions, such as where does it search, and what if there is more than one matching file? The shell solves these issues with a list of directories called a search path. You can print out the value of your search path using the following command:

```
$ echo $PATH
```

```
/usr/local/bin:/bin:/usr/bin
```

Each directory in the search path is separated by a colon (`:`) character. When the shell searches for a program it looks in each directory of the search path from left to right until a either a match is found or there are no more directories to consider. The first match is taken to be the program to run, therefore directories towards the left of the search path take precedence over directories towards the right. If no match is found then the shell will report an error of the form "`command not found`".

You can ask the shell to tell you where it finds a command using the `which` command:

```
$ which chmod
/bin/chmod
```

If the program is not in your search path then you must tell the shell exactly where to find it, for instance by giving an absolute path for the file. If the program is in the working directory then you should prefix its name with the two characters `./` (because the dot is shorthand notation for the working directory):

```
$ ./myprog -o out
```

## Process management

In Unix terms, a process (also called a task) represents the running instance of a program combined with all its associated state. The state of a process includes, amongst other things, its allocated memory, a collection of environment variables, and various odds-and-ends to enable it to perform input and output. Processes are identified by a number called a *process ID* (PID), and each process is owned by a user, which affects what privileges it has on the system.

You can find out what processes are running with the `ps` command:

```
$ ps
  PID TTY          TIME CMD
21788 pts/30   00:00:00 bash
32360 pts/30   00:00:00 ps
```

By default `ps` prints a simple report about all the process that are owned by you. In the example above there are two processes, the first is the `bash` shell, and the second is the `ps` command itself (yes, `ps` prints information about its own process!). There are four columns in the output which provide the following information:

1. The PID of the process, e.g. `21788`

2. The terminal device connected to the process (for interactive processes), e.g. `pts/30`

3. The total amount of time the process has spent on the CPU, e.g. `00:00:00`

4. The name of the program running in the process, e.g. `bash`

You can see all the processes on the system with `ps` `-e`:

```
$ ps -e
```

```
PID TTY    TIME CMD
1 ?        00:00:05 init
2 ?        00:00:02 migration/0
3 ?        00:00:00 ksoftirqd/0
...
```

There are usually a large number of processes on a system at any moment, so the output of the above command is likely to be very long.

Unix is a multitasking operating system, which means that it can share the available computing resources amongst multiple active processes. It does this by maintaining a queue of processes that are runnable. A scheduler in the kernel decides which process to run next on a particular CPU. Running processes are allocated a slice of CPU time. When its time slice is up the running process is preempted by the scheduler and put back in the queue until it gets another turn to run in the future. A process can also be blocked, which means it is not presently runnable because it is waiting for some external event, such as input from the user.

You can get a dynamic view of the current processes on the system using the `top` command, which shows the same kind of information as `ps` but in a continually updated panel (to see just your own processes type ‘`u`’ and then your username, and to quit press ‘`q`’).

### Job control

Interactive programs, including the shell, read input from, and write output to a terminal device. Due to its multitasking nature, Unix allows a user to run multiple processes simultaneously. It is reasonable to let multiple processes share a terminal for output (which can be interleaved between them), but it is not reasonable for multiple processes to read their input from the terminal at the same time (otherwise chaos would ensue). The shell allows you to control which process has read access to the terminal by maintaining the concept of *foreground*, *background* and *suspended* jobs. A foreground job is a process that can read from the terminal, and there can only be one such job at any time. A background job is a process that can write to the terminal but not read from it. A suspended job is blocked from running, and it remains in that state until it is either awoken or terminated. There can be multiple background and suspended jobs at any time. Each job is numbered by the shell, which makes it easy to refer to in future commands, but the job number should not be confused with the PID.

The default behaviour of the shell is to run a new command as a foreground job. This is not a problem for short running commands because they only stay in the foreground for a brief moment. However it is desirable to run longer computations which don’t need to read from the terminal in the background. This is achieved by placing an ampersand at the end of the command line:

```
$ xterm &
[1] 5391
```

The above example starts the `xterm` program and runs it in the background. The shell responds with the text "`[1] 5391`", which indicates this is job 1 with PID 5391.

You can find out the status of your current jobs with the jobs command:

```
$ jobs
[1]+  Running                 xterm &
```

You can bring a job back into the foreground with the `fg` command by specifying the job number:

```
$ fg 1
xterm
```

You can suspend the current foreground job by pressing Control-z. If we did that for the current example the shell would respond like so:

```
[1]+  Stopped                 xterm
```

This indicates that job 1 is suspended and control of the terminal is handed back to the shell. A suspended job can be returned to the foreground with `fg` or it can be sent to the background with the `bg` command:

```
$ bg 1
[1]+ xterm &
```

### Terminating processes

A running process can be terminated in a number of ways:

* On its own without request from the user (perhaps because it runs out of input).

* After the user asks it to quit, such as by pressing Control-x in `nano`.

* After receiving a signal from the operating system that asks (or forces) it to quit.

You can request the foreground job to quit by pressing Control-c. This sends the process an interrupt signal. Most processes will terminate gracefully when given this signal.

You can also send an interrupt signal to a process using the `kill` command by naming its PID:

```
$ kill 5391
$ jobs
[1]+  Exit 15                 xterm
```

In extreme circumstances a process might not terminate when given the interrupt signal. This could be intentional, or it could be because the process has run out of control. You can be more forceful and send the process a *kill* signal (-9), which cannot be ignored:

```
$ xterm &
[1] 8518
$ kill -9 8518
```

Obviously you can only kill processes that you own.

## Standard input and output

Every Unix process has three default streams associated with it: one input stream and two output streams associated with it. The input stream is called *standard input* (stdin), and the output streams are called *standard output *(stdout) and *standard error *(stderr):

![Standard input, output and error](using_unix/img/stdin_stdout.png)

As the names suggest, stdin is for input to the process, stdout is for normal output, and stderr is for error output.

### Output redirection

By default the shell connects stdin to the terminal keyboard and stdout and stderr to the terminal screen, however, it is possible to redirect the streams to and from files.

For example we can redirect the stdout of the `ps` command and save it to a file like so:

```
$ ps > psout
```

The greater-than character (`>`) tells the shell to connect the stdout of the process to the file named on the right-hand-side. In this case the file is called `psout`, but you can choose any name you like. If a file with the same name already exists then it will be overwritten with new content, so you must use redirection with caution.

We can verify that `psout` contains the output of the `ps` command with `cat`:

```
$ cat psout
PID TTY        	TIME CMD
7559 pts/8    	00:00:00 bash
11524 pts/8    	00:00:00 ps
```

You can redirect stderr in a similar fashion with `2>` (instead of `>`) and both of them at the same time with `&>`.

Standard input can be redirected to come from a file (instead of the keyboard) using the less-than (`<`) character, like so:

```
$ wc -w < psout
12
```

In the above example the `wc` command counts the number of words in the text in its stdin, which is redirected from the file `psout`.

Both types of redirection can be used in the same command:

```
$ wc -w < psout > wcout
```

The above example redirects the contents of `psout` into the stdin of `wc` and then redirects the stdout of `wc` to the file `wcout`.

### Pipes

In a similar fashion to redirection, the shell can also arrange for the stdout of one process to be connected to the stdin of another process (and merge the stderr streams):

![Process pipes](using_unix/img/process_pipe.png)

This connection of processes is achieved using a pipe (`|`). For example we can `cat` the contents of the `psout` file and pipe it into the `wc` command like so:

```
$ cat psout | wc -w
12
```

You can build up even longer pipelines by chaining more than two processes together:

```
$ cat psout | sort | tail -1
  PID TTY          TIME CMD
```

And you can use file redirection with pipelines too:

```
$ cat psout | sort | tail -1 > out
```

Pipes are a handy way to build up complex tasks from simpler programs, and they demonstrate the advantages of the Unix philosophy of providing small programs which "do one thing well".
