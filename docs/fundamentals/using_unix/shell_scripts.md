# Shell scripting

A shell script is an executable text file which contains a canned sequence of shell commands. Scripts allow you automate complicated tasks and avoid repeating yourself.

The first line in a shell script is special. It starts with a magic two letter sequence `#!` which is followed immediately by the path of the shell program:

```
#!/bin/bash
```

The rest of the file can contain any sequence of shell commands, exactly as you would type them at the prompt. For example we could turn our previous pipeline into a shell script by creating a text file with the following contents:

```
#!/bin/bash
cat psout | sort | tail -1
```

If you save the script to a file called `myprog` then you can make it executable like so:

```
$ chmod u+x myprog
```

And you can run `myprog` as a command in the normal way:

```
$ ./myprog
 PID TTY          TIME CMD
```

(note the `./` at the start of the file name because it is in the working directory and not in the search path).

The `bash` command language is actually quite full-featured. It has many of the things you’d expect to find in a programming language such as variables, conditionals, loops, functions, arrays and much more. It is impossible to cover all the features of the shell in this document, so we refer you to the Bash Scripting Guide:

[http://tldp.org/LDP/abs/html/](http://tldp.org/LDP/abs/html/)

The downside of shell scripting is that the syntax is optimised for making shell commands as concise as possible (to make them easy to type at the prompt), which tends to make the other constructs awkward. As such it is well suited to automating small to medium tasks which consist predominantly of calls to other commands on the system. For larger tasks which are more algorithmically complex it is better to choose a more conventional programming language.
