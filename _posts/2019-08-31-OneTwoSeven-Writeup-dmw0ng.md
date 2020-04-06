---
layout: single
title: "HTB OneTwoSeven Writeup by dmw0ng"
header:
  teaser: /assets/images/teasers/onetwoseven.jpg
excerpt: "OneTwoSeven is a hard box that starts by logging into sftp and creating multiple symlinks to enumerate files. From one of these files we get credentials and move on to port-forward  to get access to a plugin upload website from which we can get RCE. For privesc we MITM attack an apt-get update that we have sudo rights with, create a malicious package and gain root access."
---

OneTwoSeven is a hard box that starts by logging into sftp and creating multiple symlinks to enumerate files. From one of these files we get credentials and move on to port-forward  to get access       to a plugin upload website from which we can get RCE. For privesc we MITM attack an apt-get update that we have sudo rights with, create a malicious package and gain root access."

<iframe height="900" src="https://drive.google.com/viewerng/viewer?embedded=true&amp;url=https://birdsarentrealctf.dev/content/dmw0ng/onetwoseven/Hack_the_Box_-_OneTwoSeven.pdf" width="900"></iframe>
