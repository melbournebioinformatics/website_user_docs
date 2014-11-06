# The shell and the command line

When you login to a Unix machine you are confronted with a prompt: a short piece of text indicating that the system is ready for you to type a command. The prompt on Merri (a computer at VLSCI) might look like this:

```
[username@merri ~]$
```

where the word `username` is replaced by your actual username on the system. The content of the prompt is configurable, and it might be different on other systems that you encounter. In this document, for simplicity, we show the prompt as a single dollar sign (`$`).

The command line itself is provided to you by a program called the *shell*. The shell acts as an intermediary between you and the underlying operating system. Each time you enter a command, the shell parses it and then asks the system to carry it out. The shell is also programmable, which means you can automate sequences of commands that would be tedious to enter manually. Most Unix systems provide several alternative shells for you to use, but most GNU/Linux systems use the BASH shell as the default. This document assumes you are using BASH, but most of the examples should work equally well in other shells.

The diagram below shows how the shell relates to various other components in a computer system:

![The shell and other processes](using_unix/img/shell_processes.png)

At the bottom of the diagram sits the computer hardware. Immediately above the hardware is a software abstraction called an *operating system kernel.* The kernel is a piece of software which manages the resources of the computer (disk drives, screen, keyboard *etcetera*) and makes them available to your programs. User applications sit on top of the stack, and interface with the hardware via the kernel. The shell is just an ordinary user application that happens to provide a convenient command interpreter. 

It should be noted that users can interact with Unix in other ways besides the shell. Graphical user interfaces (GUIs) are also available, and most users run a shell inside a GUI window nowadays. Nonetheless, this document focuses on the shell because its programmability offers productivity gains unrivalled by other interfaces. 

## An example command

Here’s a simple example command to get you started. Type the following command into the shell and press enter:

```
$ date
```

the output will look something like this:

```
Tue Mar  6 21:05:56 EST 2012
```

As you can see the output of the command is the current local date and time.

Note that once the previous command has completed the shell displays a new prompt indicating that it is ready for the next command to be entered.

Many Unix commands accept arguments (sometimes called flags) which request specific features to be activated. For example you can ask the `date` command to produce its output in Coordinated Universal Time (UTC) like so:

```
$ date -u
Tue Mar  6 10:06:14 UTC 2012
```

It is not uncommon for a command to accept many different arguments, and, in most cases, more than one argument can be supplied at the same time. Arguments are separated by one or more space characters and they are usually case-sensitive. 

How do you find out what arguments are accepted by a command? In the case of `date` you can pass it the `--help` argument and it will print out some usage information, but unfortunately not all commands behave the same way. This is related to the more general question about how to get help about using a Unix system, which we cover in more detail in the section entitled *Getting Help* later in this document.

## Command history

A convenient feature of the shell is that it keeps a record of the most recent commands that you have entered. It is often the case that previous commands are repeated in the near future. You can go back to previous commands by using the up-arrow key (and forwards with the down-arrow key). The command history saves you from typing it all in again each time, which is very useful when the command was large.

You can also view your most recent commands with `history`:

```
$ history
...
980  ls -la
981  ./myprog
992  man awk
```

(Your output will almost certainly look different than the example above, because you will have a different history of commands.)

Each command in the history is numbered and the shell lets you run the command again by prefixing its number with an exclamation mark. For example, given the history above, the command `!980` would cause `ls -la` to be run again.

## Command editing

The command line can be edited in a number of ways:

* Forward and back arrows move the cursor left and right.

* The delete key erases the character to the left of the cursor.

* Control-k deletes everything from the cursor to the end of the line.

* Control-u deletes everything from the cursor to the start of the line.

* Control-e moves the cursor to the end of the line.

* Control-a moves the cursor to the start of the line.

* Control-r searches in reverse through the history.

### Tab completion

Pressing the tab key causes the shell to expand the text sitting just prior to the cursor:

* If zero names match then nothing happens.

* If exactly one file name matches then the text is expanded to that name.

* If more than one file name matches then a list of matching names will be displayed. You will need to type one or more characters to disambiguate before pressing tab to continue the completion.
