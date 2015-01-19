---
layout: page
title:  "Mac Instructions"
author: Ross Anderson
---

These instructions were lasted tested on Mac OS X version 10.6.8 on January 2, 2013.  Undoubtedly, they will become outdated, so use them with caution after the course ends.

# Ensure that you have 64 bit OS X

According to [this website](http://www.stata.com/support/faqs/windows/64-bit-compliance/), all Macs on OS X 10.5 or greater have a 64 bit operating system and a 64 bit processor, so if your Mac is from [2007 or later](http://en.wikipedia.org/wiki/OS_X#Version_10.5:_.22Leopard.22) then you will probably be fine. If your Mac is from before 2007, you would need an earlier version of CPLEX than the one that we require. Instructions to check which version of OS X you have are given in the first link.

[What are bits?](../facts/bits/index.html)

# Get Java

OS X comes with Java installed, so you should already have it.

[What is the JDK/JRE?](../facts/java/index.html)

# Get Eclipse

Download 64-bit Eclipse from [here](http://www.eclipse.org/downloads/). Install?? and open the program. When the program opens, it will ask you to select a workspace. This is the location on your computer where the source code for all of your java projects will be stored. The default on my computer was

`/Users/<yourusername>/Applications/IBM/ILOG/CPLEX_Studio125/`

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

Select

`IBM ILOG CPLEX Optimization Studio V12.5 for Mac OS 10 Multilingual (CIC4UML)`

Scroll to the bottom and accept the license agreement, then hit download. A java applet will pop up to manage the download. If you get an error in the applet saying your java is out of date and a second pop up, hit OK until the second pop up goes away then hit _Run anyway_ on the applet. Again, if you can't get the applet to work, just select Http Transfer instead of Download Director as the Download Method. The download could take as long as half an hour.

When the download has finished, run the .exe you downloaded. The Download Director will indicate the directory where the final was downloaded.  On my machine, the default location (I used Firefox and did not use the Download Director) was

`/Users/angela/Downloads/cplex_studio125.macos.bin`

To run the file, you first must make it executable. *From a terminal*, simply go to the directory where the file is located, then type `chmod a+x cplex_studio125.macos.bin`. To run the file, from a terminal in the directory of the file, type `sudo ./cplex_studio125.macos.bin`. Be sure to run as root, otherwise you will not have permission to install in the default directory.

Note the directory of installation. The default on my machine was:

`/Users/angela/Applications/IBM/ILOG/CPLEX_Studio125`