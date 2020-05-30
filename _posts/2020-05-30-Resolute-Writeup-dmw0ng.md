---
layout: single
title: "HTB Resolute Writeup by dmw0ng"
header:
  teaser: /assets/images/teasers/resolute.jpg
---

Resolute was a quite particular windows box that did not have a web server running. A password could be retrieved remotely from user descriptions comments, allowing a login into the box through WinRM. From there, another password can be grabbed from PSTranscripts to escalate to a user with DnsAdmin privieleges, which allowed us to further privesc to the Domain Admins group and finish the box.

<iframe height="900" src="https://drive.google.com/viewerng/viewer?embedded=true&amp;url=https://birdsarentrealctf.dev/content/dmw0ng/resolute/Hack_The_Box_-_Resolute.pdf" width="900"></iframe>
