---
layout: single
classes: wide
title: "HTB Forwardslash Writeup by c4e"
header:
  teaser: /assets/images/teasers/forwardslash.png
excerpt: "Forwardslash is a hard-rated box (medium difficulty imo) in which we exploit an LFI in the web server to get access to some sensitive info that lets us SSH in. In our initial SSH session we exploit a SUID binary to obtain once again read access to a file with credentials that we use to move laterally to another user. From there we have sudo rights to access an encrypted luks image file, so we only have to bruteforce the key to then gain root and complete the machine."
comments: true
---

Forwardslash is a hard-rated box (medium difficulty imo) in which we exploit an LFI in the web server to get access to some sensitive info that lets us SSH in. In our initial SSH session we exploit a SUID binary to obtain once again read access to a file with credentials that we use to move laterally to another user. From there we have sudo rights to access an encrypted luks image file, so we only have to bruteforce the key to then gain root and complete the machine. 

<iframe height="900" src="https://drive.google.com/viewerng/viewer?embedded=true&amp;url=https://birdsarentrealctf.dev/content/c4e/forwardslash/Forwardslash-Writeup.pdf" width="900"></iframe>
