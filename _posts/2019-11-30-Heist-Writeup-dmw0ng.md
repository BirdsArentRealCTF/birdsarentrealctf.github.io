---
layout: single
title: "HTB Heist Writeup by dmw0ng"
header:
  teaser: /assets/images/teasers/heist.png
excerpt: "Heist is an easy box in which we first crack found creds on the website to access RPC. From there we enumerate users and use one of them with the previously obtained passwords to log into WinRM. We find out that a Firefox process memory dump in the disk and analyze it to discover credentials that allow us to escalate to Administrator and own the box."
---

Heist is an easy box in which we first crack found creds on the website to access RPC. From there we enumerate users and use one of them with the previously obtained passwords to log into WinRM. We find out that a Firefox process memory dump in the disk and analyze it to discover credentials that allow us to escalate to Administrator and own the box.

<iframe height="900" src="https://drive.google.com/viewerng/viewer?embedded=true&amp;url=https://birdsarentrealctf.dev/content/dmw0ng/heist/Hack_the_Box_-_Heist.pdf" width="900"></iframe>
