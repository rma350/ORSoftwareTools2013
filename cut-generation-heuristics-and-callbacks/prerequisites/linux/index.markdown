---
layout: page
title:  "Linux Instructions"
author: Ross Anderson
---

These instructions were lasted tested on Ubuntu 12.04 on December 29th, 2012. Undoubtedly, they will become outdated, so use them with caution after the course ends.

# Determine if you have 32 bit or 64 bit Ubuntu

*From the terminal* type `uname -m`. If the output is `i686` or `i386`, you have a 32 bit operating system. If the output is `x86_64` you have a 64 bit operating system.

Alternatively, *with the mouse*, open the program _System Monitor_ (in 12.04 with Unity, open the dashboard and type _System Monitor_), go to the _System Tab_, and the bit value for the operating system will be in the first line.

If you are unsure of what version of Ubuntu you have, the information is displayed in the same location in the _System Monitor_. Alternatively, type `lsb_release -a` into the terminal.

 [What are bits?](../facts/bits/index.html)

# Get Java

You have a variety of installation options as explained [here](https://help.ubuntu.com/community/Java). I would recommend installing something through the terminal, as then you don't have to worry about bits and your java will automatically update through the package manager. Make sure you get the Java Development Kit (JDK) for Java 6 or greater.


[What is the JDK/JRE?](../facts/java/index.html)

# Get Eclipse

You can install Eclipse 3.7 through the package manager with the `command sudo apt-get install eclipse`. In the past, there have been some advantages to using Sun's version on java instead of OpenJDK. Instructions to how to configure eclipse to use a certain version of Java can be found [here](https://help.ubuntu.com/community/EclipseIDE), as well as instructions on how to do a user installation (not through the package manager). Again this is not recommended.

*Warning Dropbox users:* I would recommend against making your workspace be inside your Dropbox. This ultimately causes problems.

[What is Eclipse?](../facts/eclipse/index.html)

[Which version of Eclipse?](../facts/eclipse-version/index.html)


# Get CPLEX

First, get an IBM academic initiative membership by going [here](https://www-304.ibm.com/ibm/university/academic/pub/page/membership). If you don't have an account, click any of the links that say Join now, then apply as a faculty. If you already have an account but you haven't used it in a year, you can just click renew below the Join now on the right hand side of the page instead. You will need to create an IBM ID and then fill out some forms. Save your IBM log in information as you will need it every time you want to download CPLEX. This will not be the last time!

Next, we will download CPLEX *12.5*. Go [here](https://www-304.ibm.com/ibm/university/academic/pub/jsps/assetredirector.jsp?asset_id=1070), click the link to the software download catalog in the second bullet point, then in the first search bar (find by search text) paste the following:

`IBM ILOG CPLEX Optimization Studio V12.5 Multiplatform Multilingual eAssembly`

*Warning:* Note that instructions on the webpage are out of date, they tell you to search for version 12.4.


There will only be one result, the query itself. For the download method, first try and select the _download director_ (if you can't get this to work, you can use _http transfer_ instead).

For 32 bit Ubuntu, also select

`IBM ILOG CPLEX Optimization Studio V12.5 for Linux x86-32 Multilingual (CIC4KML)`

For 64 bit Ubuntu, instead select

`IBM ILOG CPLEX Optimization Studio V12.5 for Linux x86-64 Multilingual (CIC4LML)`


Scroll to the bottom and accept the licence agreement, then hit download. A java applet will pop up to manage the download. If you get an error in the applet saying your java is out of date and a second pop up, hit OK until the second pop up goes away then hit _Run anyway_ on the applet. Again, if you can't get the applet to work, just select Http Transfer instead of Download Director as the Download Method. The download could take as long as half an hour.

When the download has finished, run the .exe you downloaded. The Download Director will indicate the directory where the final was downloaded. On my machine, the default location (I used chrome and did not use the download director) was

`/home/ross/Downloads/cplex_studio125.linux-x86-64.bin`

To run the file, you first must make it executable. With a mouse, from the file browser, right click on the file, click _Properties_, click the _Permissions_ tab, then check the Executable checkbox. From a terminal, simply go to the directory where the file is located, then type `chmod a+x cplex_studio125.linux-x86-64.bin`. To run the file, from a terminal in the directory of the file, type `sudo ./cplex_studio125.linux-x86-64.bin`. Be sure to run as root, otherwise you will not have permission to install in the default directory.

Note the directory of installation. The default on my machine was:

`/opt/ibm/ILOG/CPLEX_Studio125`
