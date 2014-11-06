# Getting help

One of the biggest problems facing a new Unix user is the sheer volume of commands available. One of the downsides of the command line compared to a graphical interface is that there is no ordered searchable menu. Even if you know that a command exists you still may not know how to use all its features. So how do you discover new commands, and how do you learn how to use existing ones?

## The Unix manual

The traditional approach is to use the built in Unix manual, which is provided by the `man` command:

```
$ man time
```

(use the arrow keys to move up and down and `q` to quit the manual).

The manual has an entry for most of the standard Unix commands and for some of the less common ones too. It even has an entry for itself:

```
$ man man 
```

You can search for articles in the manual by keyword using the `-k` argument:

```
$ man -k compiler
...
fort77               (1p) - FORTRAN compiler (FORTRAN)
gcc                  (1)  - GNU project C and C++ compiler
zic                  (8)  - time zone compiler
```

Most keyword searches in the manual return large lists of results (we show only a small part of the output above), so you will need to spend some time sifting through them to find the command you are looking for. 

The Unix manual is divided into multiple sections and a given command could appear in many different sections. In the output of `man -k` above you will see numbers in parentheses, which indicate the section of the manual referred to by a particular entry. If you want to look at a specific section of the manual then you need to provide the section name as an argument. 

Compare the results of these two commands:

	
```
$ man 1 cp

$ man 1p cp
```

## Other sources of help

In truth the Unix manual is not particularly friendly to beginners and you’d be excused for trying to look elsewhere for help.

Nowadays there is a wealth of information about Unix on the Internet. In fact there is so much information that it is hard to know where to look, although a well constructed Google search can help you narrow down the results. Sometimes nothing beats a good old-fashioned textbook, and there are plenty to choose from. A popular choice for beginners is *Learning the UNIX Operating System* by Peek, Todino-Gonguet and Strang.

If you are a VLSCI user then you can always ask us for help in a number of ways:

* Come over to our offices and speak to someone in person.

* Write your question in an email and send it to [help@vlsci.org.au](mailto:help@vlsci.org.au)
