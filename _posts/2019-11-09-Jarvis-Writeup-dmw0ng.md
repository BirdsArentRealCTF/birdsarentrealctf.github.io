---
layout: single
title: "HTB Jarvis Writeup by dmw0ng"
header:
  teaser: /assets/images/teasers/jarvis.png
excerpt: "Jarvis is a medium difficulty box in which we are able to inject SQL to get credentials into a phpmyadmin instance. We use a phpmyadmin metasploit exploit to gain a shell on the machine as www-data. www-data has sudo access as pepper user to a python script which we escape into a bash shell and then use to exploit a SUID binary to get root."
---

Jarvis is a medium difficulty box in which we are able to inject SQL to get credentials into a phpmyadmin instance. We use a phpmyadmin metasploit exploit to gain a shell on the machine as www-data. www-data has sudo access as pepper user to a python script which we escape into a bash shell and then use to exploit a SUID binary to get root.


<iframe height="900" src="https://drive.google.com/viewerng/viewer?embedded=true&amp;url=https://birdsarentrealctf.dev/content/dmw0ng/jarvis/Hack_the_Box_-_Jarvis.pdf" width="900"></iframe>
