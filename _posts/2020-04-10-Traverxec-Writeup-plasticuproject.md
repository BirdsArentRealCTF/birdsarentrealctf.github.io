---
layout: single
title: "HTB Traverxec Writeup by plasticuproject"
header:
  teaser: /assets/images/teasers/traverxec.png
excerpt: "Traverxec is an easy difficulty box in which we are able to leverage a directory traversal vulnerability in Nostromo to achieve remote command execution. We use a Metasploit exploit to gain a shell on the machine as www-data. Because of file/directory permission misconfiguration we can access a backup file containing user credentials, and then elevate our privileges to the root account via the user's sudo privilege and a known shell escape for journalctl where the less pager allows us to execute commands as root."
---

Traverxec is an easy difficulty box in which we are able to leverage a directory traversal vulnerability in Nostromo to achieve remote command execution. We use a Metasploit exploit to gain a shell on the machine as www-data. Because of file/directory permission misconfiguration we can access a backup file containing user credentials, and then elevate our privileges to the root account via the user's sudo privilege and a known shell escape for journalctl where the less pager allows us to execute commands as root.


<iframe height="900" src="https://drive.google.com/viewerng/viewer?embedded=true&amp;url=https://birdsarentrealctf.dev/content/plasticuproject/traverxec/Hack_the_Box_-_Traverxec.pdf" width="900"></iframe>
