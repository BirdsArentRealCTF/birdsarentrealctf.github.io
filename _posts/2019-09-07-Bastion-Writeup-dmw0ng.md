---
layout: single
title: "HTB Bastion Writeup by dmw0ng"
header:
  teaser: /assets/images/teasers/bastion.jpg
excerpt: "Bastion is an easy box that we start by getting a Windows backup from an open SMB share. We crack the SAM file and get a password. From there we ssh in the machine and find an mRemoteNG configuration file that we use to get the Adminisrator password and finish the box."
---

Bastion is an easy box that we start by getting a Windows backup from an open SMB share. We crack the SAM file and get a password. From there we ssh in the machine and find an mRemoteNG configuration file that we use to get the Adminisrator password and finish the box.

<iframe height="900" src="https://drive.google.com/viewerng/viewer?embedded=true&amp;url=https://birdsarentrealctf.dev/content/dmw0ng/bastion/Hack_the_Box_-_Bastion.pdf" width="100%"></iframe>
