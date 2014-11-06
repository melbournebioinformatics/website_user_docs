# Working with files

## File types

Unix treats all files (barring some special cases) as merely sequences of bytes. That is to say, for normal files, it does not distinguish between different types or formats. It is up to the programs which work with files to decide how to interpret their contents.

Having said that, users often think of files as having a *type* based on what kinds of operations they might like to perform on them. For example, we might think of a file as containing the source of a Python program, or as the sequence data from a DNA sample.

The `file` command tries to guess the type of a file based on its contents and various heuristics:

```
$ file main.c
main.c: ASCII C program text
```

```
$ file main.o
main.o: ELF 64-bit LSB relocatable, AMD x86-64, ..
```

```
$ file stats.r
stats.r ASCII English text
```

The last example above is a script from the R statistical programming language, which demonstrates that the `file` command is not perfect.

It is also common for users to distinguish files as being "text" or “binary” (though we emphasise again that Unix itself makes no such distinction). For example, the source code of a HTML page is regarded as text, whereas the contents of a compiled program is regarded as binary. The Unix philosophy has tended to encourage the use of text files because they are generally “human readable” whereas binary files are generally not. However, a text representation of data might not always be as compact as a binary one (especially when numbers are being stored).

Traditionally text files contained sequences of bytes representing members of the ASCII character set, where each character occupies exactly one byte in the file. However, ASCII is slowly being superseded by Unicode, which contains all the ASCII characters plus many others, including characters from non Latin-based languages. Unicode characters are stored in files using a multi-byte encoding scheme such as UTF-8 (which was designed to be backwards compatible with ASCII).

Unix comes with a large number of commands which help you manipulate text files, and on GNU/Linux they generally support Unicode. However, the ASCII subset of Unicode is still widely used, and we stick to that character set in this document for simplicity.

## Displaying the contents of files

You can display the entire contents of one or more files with the `cat` command:

```
$ cat poem.txt
fat cat
sat flat
```

The cat command is nice and simple, but it tends to be impractical for large files because it shows the entire contents at once. An alternative is the `less` command, which supports text scrolling and searching:

```
$ less stats.r
START_PHI <- 0
SIZE <- 100                # side of square grid in pixels
...
```

It is hard to show the output of the less command in this document, so we just show the first few lines in the example above. You can use the arrow keys to scroll up and down in the output, `/pattern` to search for "pattern" in the file, and `q` to quit. 

	

Sometimes you only want to view the first or last few lines of a file: this can be done with the `head` and `tail` commands respectively:

```
$ head -3 stats.r
START_PHI <- 0
SIZE <- 100                # side of square grid in pixels
```

```
$ tail -5 main.c
   init();
   visualise(argc, argv);
   finalise();
   return 0;
}
```

The first argument to `head` and `tail` is the number of lines to display, which, if you leave it out, defaults to 10.

## Searching for patterns in files

You can search for a pattern in one or more files using the `grep` command:

```
$ grep include *.c
main.c:#include "simulate.h"
queue.c:#include <assert.h>
queue.c:#include "types.h"
simulate.c:#include <gsl/gsl_rng.h>
```


The example above searches for all the lines that contain the pattern "`include`" in all the files in the working directory that end in “`.c`”.

When `grep` finds a match for the pattern in a file it prints out the whole line. When multiple files are searched it prints the name of the file before the matching line.

The pattern can be a *regular expression* which allows sophisticated searches to be performed. We don’t have space to describe regular expressions in this document, but there is quite a detailed entry on Wikipedia:

[http://en.wikipedia.org/wiki/Regular_expression](http://en.wikipedia.org/wiki/Regular_expression)

The `grep` command has many arguments which control its behaviour. A couple of the more common ones are `-R` for recursive search, `-i` for case-insensitive search.

## Comparing files for differences

You can find differences between two files using the `diff` command. For example, suppose we have two files with the contents of each shown below:

hello.c
```
#include <stdio.h>
int main(void) {
    printf ("Hello World\n");
    return 0;
}
```

bonjour.c
```
#include <stdio.h>
int main(void) {
    printf ("Bonjour Monde\n");
    return 0;
}
```

```
$ diff hello.c bonjour.c
3c3
<     printf ("Hello World\n");
>     printf ("Bonjour Monde\n");
```

The output of `diff` shows the lines where the files differ. The text `3c3` means that the 3rd line in the first file was changed to the 3rd line in the second file. The `<` character marks lines in the first file and the `>` character marks lines in the second file. If the files are the same then `diff` will produce no output.

## Editing files

There are many ways to edit files on Unix, but perhaps the simplest way is to use the full-screen text editor called `nano`.

To create a new file, run `nano` with no arguments:

```
$ nano
```

To edit an existing file, run `nano` with the name of the file to edit as an argument:


```
$ nano poem.txt
```

A great feature of `nano` is that it has only a few commands and the most important ones are displayed at the bottom of the screen, like so:

```
^G Get Help ^O WriteOut
^X Exit ^J Justify
```

The `^` character means "hold down the Control Key while pressing the character next to it". So, for example, to exit `nano` you should hold down the Control Key and simultaneously press the `x` key (despite appearances the single-letter characters in `nano`’s commands should be lowercase). If you have changed the file before exiting `nano` will prompt you to save the changes.

Experienced Unix users tend to prefer text editors with more features than `nano`. The most popular editors, especially for programmers, are `vim` and `emacs`. Unfortunately these editors have a much steeper learning curve, and so we don’t cover them in this document.

## Other handy commands which operate on files

There are lots of other commands on Unix which operate on files. We don’t have space to describe them all in detail, but here is a short list which you might like to lookup in the future:

<table border="1">
  <tr>
    <td>Command</td>
    <td>Description</td>
  </tr>
  <tr>
    <td>awk</td>
    <td>Data extraction and reporting scripting language.</td>
  </tr>
  <tr>
    <td>cut</td>
    <td>Remove sections from lines in a file.</td>
  </tr>
  <tr>
    <td>find</td>
    <td>Search for files in a directory hierarchy using a pattern syntax.</td>
  </tr>
  <tr>
    <td>gzip</td>
    <td>File compression (to make the file consume less space).</td>
  </tr>
  <tr>
    <td>sed</td>
    <td>Tool for filtering and transforming text, similar to awk.</td>
  </tr>
  <tr>
    <td>sort</td>
    <td>Sort lines of text files based on defined fields in each line.</td>
  </tr>
  <tr>
    <td>tar</td>
    <td>Store many files in a single archive.</td>
  </tr>
  <tr>
    <td>tr</td>
    <td>Translate or delete characters in a file.</td>
  </tr>
  <tr>
    <td>uniq</td>
    <td>Report or omit repeated lines in a file.</td>
  </tr>
  <tr>
    <td>wc</td>
    <td>Count the number of lines, words and characters in files.</td>
  </tr>
</table>

<p></p>
