# Logging On or Connecting 

## Logging on to a VLSCI computer

Compute jobs on VLSCI computers are launched from the Unix command line. In order to access the Unix command line you must first log onto one of the VLSCI computers. The process of logging on establishes a connection between your own computer and one of the computers at VLSCI. Note that you must connect with a terminal session at least once before using tools that may, for example, copy files to your home directory. This is because that first connection is necessary to initialise your home directory on that particular system.

## Secure shell (ssh)

To log on to our computers you must use the ssh (secure shell) protocol. This allows you to type commands in a window on your local computer and have them executed on a VLSCI computer. To log on to a VLSCI computer using ssh you must specify the fully qualified name of the computer. VLSCI currently has three computers available to users: Barcoo, Merri and Avoca. Their fully qualified names are shown below:

* barcoo.vlsci.unimelb.edu.au
* merri.vlsci.unimelb.edu.au
* avoca.vlsci.unimelb.edu.au

The choice of which particular VLSCI computer to use will depend on the job that you want to run and the resources allocated to your project. Consult with your project leader if you are not sure which computer to use.

## Microsoft Windows Users

Windows users can use [putty](http://www.chiark.greenend.org.uk/~sgtatham/putty/) for ssh connections.  Click the 'download' from the top of that page, right click "putty.exe" and select "save as", save it on your desktop. There are a number of other versions and tools there, you probably don't need them. Double click the putty icon you have just saved and a config screen will open. When working with putty, its useful to have a "saved Session" to remember your settings. Lets make one to connect to Barcoo. Enter "barcoo.vlsci.unimelb.edu.au" into the hostname field and enter "Barcoo-VLSCI" in the Saved Session field, Click the Save Button. You can now initiate a connection to Barcoo by double clicking the "Barcoo-VLSCI" entry in Saved Sessions list. You can add addition sessions or choose particular setting for a saved session in a similar manner.

Windows users will use [WinSCP](http://winscp.net to transfer files). From that website, click "Go to download page", click "portable executable"  (it  won't need admin assistance to install). It downloads as a zip file, open the zip and copy the file called winscp.exe to your desktop (and perhaps read the one called license). Double click the file you just made on the desktop and you'll get a config page. Enter barcoo.vlsci.unimelb.edu.au into the hostname field and your user name into the User name field (security requires me to ask you not to enter your password at this stage). Click save and probably accept the suggested name. Now you can use that name to start an SCP session, note the first time you do, you may get a warning about "server host key", this is because its the first time the two computers have met, its quite safe to proceed.

Note that you cannot use WinSCP if you have never connected using a terminal session, the terminal session (using for example, Putty) is necessary to initialise your home directory.

## Linux Users

Linux users can use ssh directly from the command line (but remember, ssh in at least once before copying files to a particular machine).

```
ssh barcoo.vlsci.unimelb.edu.au
```

```
scp myfile myname@barcoo.vlsci.unimelb.edu.au:myfile
``` 

## Mac OS X Users

`ssh` is installed by default on Mac OS X. To use it you need to open the `Terminal` application, which is found under Applications - Utilities (or search for Terminal in the spotlight utility). The terminal gives you access to the Unix command line, from which you can invoke the ssh command.

## General Notes

You should be able to connect from anywhere you have a normal network connection. However, some organisations block outgoing ssh connections so if you do not see the password prompt, its worth checking with you local IT department to see if you can have the appropriate network ports opened up or need to use a proxy.

## Using X Windows

It is possible to send an X Windows screen back to your desktop. You will need to have what is called an XServer running on your desktop; for Linux and MacOSX before 10.8 (Mountain Lion) it is built in.

For MacOSX 10.8 (Mountain Lion) you will need to install [Xquartz](http://xquartz.macosforge.org/) and Microsoft Windows users will need to install something like XWin32 (commercial) or [XMing](http://www.straightrunning.com/XmingNotes/).

Xming is a great product if a little difficult to find the download links, look for the "Releases" block of table near top of page, and click the "Xming" link in the "Public Domain Releases" table (unless you want to go the donation path). Let the download happen and then grap the Xming-fonts from the same place. Many users will find that they need Admin help to install both.

The most common way to work is to connect to the remote VLSCI machine using putty (Windows) or the ssh command line (Mac or Linux) and then start a GUI application (such as nedit) and have it display on your desktop. Don't forget to turn on XForwarding in putty or use the -X or -Y command line switches if using command line ssh. Your local XServer that runs on your desktop then need only accept connections from `localhost` your local putty or ssh session.
