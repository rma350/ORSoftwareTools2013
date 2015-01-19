---
layout: page
title:  "Windows Instructions"
author: Ross Anderson
---


These instructions were lasted tested on Windows 7 on December 26th, 2012. Undoubtedly, they will become outdated, so use them with caution after the course ends.

# Determine if you have 32 bit or 64 bit Windows

Follow the instructions [here](http://support.microsoft.com/kb/827218).

 [What are bits?](../facts/bits/index.html)

# Get Java

Install the Java Development Toolkit 7 (JDK) by downloading and running the .exe [here](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html). If you already have Java 6 installed, it is sufficient. If you have 32 bit windows, be sure to get Windows x86 and if you have 64 bit windows, get Windows x64.

[What is the JDK/JRE?](../facts/java/index.html)

# Get Eclipse

Download Eclipse Classic 4.2.1 [here](http://www.eclipse.org/downloads/). Be sure to select 32 bit if you have 32 bit Windows and 64 bit if you have 64 bit Windows. Eclipse does not install like a regular program. All you need to do is unzip the folder you download wherever you want Eclipse to be installed. For example, since my Windows user name is `Ross`, I put the folder in

`C:\Users\Ross`

Once you have the eclipse folder where you want it, try running eclipse by double clicking `eclipse.exe`. Continuing the example above, the full path would be

`C:\Users\Ross\eclipse\eclipse.exe`

Be sure not to click `eclipsec.exe` by mistake! When the program opens, it will ask you to select a workspace. This is the location on your computer where the source code for all of your java projects will be stored. The default on my computer was

`C:\Users\Ross\workspace`

but you can change this to be stored anywhere.

*Warning Dropbox users:* I would recommend against making your workspace be inside your Dropbox. This ultimately causes problems.

[What is Eclipse?](../facts/eclipse/index.html)

[Which version of Eclipse?](../facts/eclipse-version/index.html)


# Get CPLEX

First, get an IBM academic initiative membership by going [here](https://www-304.ibm.com/ibm/university/academic/pub/page/membership). If you don't have an account, click any of the links that say Join now, then apply as a faculty. If you already have an account but you haven't used it in a year, you can just click renew below the Join now on the right hand side of the page instead. You will need to create an IBM ID and then fill out some forms. Save your IBM log in information as you will need it every time you want to download CPLEX. This will not be the last time!

Next, we will download CPLEX *12.5*. Go [here](https://www-304.ibm.com/ibm/university/academic/pub/jsps/assetredirector.jsp?asset_id=1070), click the link to the software download catalog in the second bullet point, then in the first search bar (find by search text) paste the following:

`IBM ILOG CPLEX Optimization Studio V12.5 Multiplatform Multilingual eAssembly`

*Warning:* Note that instructions on the webpage are out of date, they tell you to search for version 12.4.


There will only be one result, the query itself. For the download method, first try and select the _download director_ (if you can't get this to work, you can use _http transfer_ instead).

For 32 bit Windows, also select

`IBM ILOG CPLEX Optimization Studio V12.5 for Windows x86-32 Multilingual (CIC4IML)`

For 64 bit Windows, instead select

`IBM ILOG CPLEX Optimization Studio V12.5 for Windows x86-64 Multilingual (CIC4JML)`

Scroll to the bottom and accept the licence agreement, then hit download. A java applet will pop up to manage the download. If you get an error in the applet saying your java is out of date and a second pop up, hit OK until the second pop up goes away then hit _Run anyway_ on the applet. Again, if you can't get the applet to work, just select Http Transfer instead of Download Director as the Download Method. The download could take as long as half an hour.

When the download has finished, run the .exe you downloaded. The Download Director will indicate the directory where the final was downloaded. On my machine, the default location was

`C:\Users\Ross\DownloadDirector\cplex_studio125.win-x86-64.exe`

Note the directory of installation. The default on my machine was:

`C:\Program Files\IBM\ILOG\CPLEX_Studio125`

The sample code was stored at

`C:\Users\Ross\My Documents\IBM\ILOG\CPLEX_Studio125`

Be sure to select the checkbox for _Update path_. When the download finishes, you do not need to open CPLEX studio or the readme.