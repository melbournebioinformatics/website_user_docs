# Login and logout

## Remote login

A *remote login* is a connection between your personal computer and another computer via a network (normally the internet). Your computer is *local* and the other computer is *remote*. In the case of VLSCI, the remote system will be one of our supercomputers. A remote log in allows you to type commands into your computer and have them execute on the remote system, with the results displayed on your screen.

![Connection between local and remote computers](fundamentals/using_unix/img/remote_local.png)

There are a few ways to remotely login to a Unix computer, but at VLSCI we require you to use the Secure Shell (ssh). It is secure because it encrypts all the data sent between the connected computers, which, amongst other things, prevents eavesdroppers from seeing your password in plain text.

In order to use ssh you must run an *ssh client* on your computer and tell it the hostname of the remote computer. These are the hostnames of the supercomputers at VLSCI:

* [avoca](http://www.google.com/url?q=http%3A%2F%2Ftambo.vlsci.unimelb.edu.au&sa=D&sntz=1&usg=AFQjCNG6pz8mkHRAA5AkX0Hg3AKQQWilyg)[.vlsci.unimelb.edu.au](http://tambo.vlsci.unimelb.edu.au)

* [merri.vlsci.unimelb.edu.au](http://merri.vlsci.unimelb.edu.au)

* [barcoo.vlsci.unimelb.edu.au](http://barcoo.vlsci.unimelb.edu.au)

Avoca is an IBM BlueGene/Q whereas Merri and Barcoo are x86 clusters. More information about these machines is available on this web page:

[http://www.vlsci.org.au/page/computer-software-configuration](http://www.vlsci.org.au/page/computer-software-configuration)

There are a variety of ssh clients available depending on which operating system you use; we show how to use ssh clients on Windows, OS X and Linux below.

### SSH login from Windows

On Microsoft Windows (95, 98, 2000, XP, Vista, 7) we recommend that you use the PuTTY ssh client. PuTTY (putty.exe) can be downloaded from this web page:

[http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

Documentation for using PuTTY is here:

[http://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html)

When you start PuTTY you should see a window which looks something like this:

![Putty Configuration Window](fundamentals/using_unix/img/putty_config.png)

To connect to one of the computers at VLSCI you should enter its hostname into the box entitled "Host Name (or IP address)", then click on the *Open* button. All of the settings should remain the same as they were when PuTTY started (which should be the same as they are in the picture above).

In some circumstances you will be presented with a window entitled *PuTTY Security Alert.* It will say something along the lines of "The server’s host key is not cached in the registry". This is nothing to worry about, and you should agree to continue (by clicking on Yes). You usually see this message the first time you try to connect to a particular remote computer.

If all goes well, a *terminal* window will open, showing a prompt with the text `login as:`. An example terminal window is shown below. You should type your VLSCI username and press *enter*. After entering your username you will be prompted for your password. Assuming you type the correct username and password the system should then display a welcome message, and then present you with a Unix prompt. If you get this far then you are ready to start entering Unix commands and thus begin using the remote computer.

![Putty terminal window](fundamentals/using_unix/img/putty_terminal.png)

### SSH login from Mac OS X and Linux

Both Mac OS X and Linux come with a version of ssh (called OpenSSH) that can be used from the command line. To use OpenSSH you must first start a terminal program on your computer. On OS X the standard terminal is called *Terminal*, and it is installed by default. On Linux there are many popular terminal programs including: *xterm*, *gnome-terminal*, *konsole* (if you aren’t sure, then xterm is a good default). When you’ve started the terminal you should see a command prompt. To log into Merri, for example, type this command at the prompt and press *return* (where the word `username` is replaced with your VLSCI username):

```
ssh username@merri.vlsci.unimelb.edu.au
```

The same procedure works for avoca and bruce, or any other machine where you have an account.

You may be presented with a message along the lines of:

```
The authenticity of host 'merri.vlsci.unimelb.edu.au (128.250.166.34)' can't be established.

...

Are you sure you want to continue connecting (yes/no)?
```

Although you should never ignore a warning, this particular one is nothing to be concerned about; type `yes` and then press *enter*. If all goes well you will be asked to enter your password. Assuming you type the correct username and password the system should then display a welcome message, and then present you with a Unix prompt. If you get this far then you are ready to start entering Unix commands and thus begin using the remote computer.

## Logout

When you have finished using the remote computer you can terminate your connection with the `logout` command, or simply quit your ssh client.

