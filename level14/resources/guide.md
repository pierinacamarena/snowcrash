## Step 0: exploring potential files
```
level14@SnowCrash:~$ ls -la
total 12
dr-x------ 1 level14 level14  100 Mar  5  2016 .
d--x--x--x 1 root    users    340 Aug 30  2015 ..
-r-x------ 1 level14 level14  220 Apr  3  2012 .bash_logout
-r-x------ 1 level14 level14 3518 Aug 30  2015 .bashrc
-r-x------ 1 level14 level14  675 Apr  3  2012 .profile

level14@SnowCrash:~$ find / -user flag14 -group flag14 2>/dev/null | grep -v proc
level14@SnowCrash:~$ 
```

Since we get nothing we can explore the gettheflag command

## Step1: exploring gettheflag
```
(gdb) disas main
Dump of assembler code for function main:
   0x08048946 <+0>:	push   %ebp
   0x08048947 <+1>:	mov    %esp,%ebp
   0x08048949 <+3>:	push   %ebx
   0x0804894a <+4>:	and    $0xfffffff0,%esp
   0x0804894d <+7>:	sub    $0x120,%esp
   0x08048953 <+13>:	mov    %gs:0x14,%eax
   0x08048959 <+19>:	mov    %eax,0x11c(%esp)
   0x08048960 <+26>:	xor    %eax,%eax
   0x08048962 <+28>:	movl   $0x0,0x10(%esp)
   0x0804896a <+36>:	movl   $0x0,0xc(%esp)
   0x08048972 <+44>:	movl   $0x1,0x8(%esp)
   0x0804897a <+52>:	movl   $0x0,0x4(%esp)
   0x08048982 <+60>:	movl   $0x0,(%esp)
   0x08048989 <+67>:	call   0x8048540 <ptrace@plt>
   0x0804898e <+72>:	test   %eax,%eax
   0x08048990 <+74>:	jns    0x80489a8 <main+98>
   0x08048992 <+76>:	movl   $0x8048fa8,(%esp)
   0x08048999 <+83>:	call   0x80484e0 <puts@plt>
   0x0804899e <+88>:	mov    $0x1,%eax
   0x080489a3 <+93>:	jmp    0x8048eb2 <main+1388>
   0x080489a8 <+98>:	movl   $0x8048fc4,(%esp)
   0x080489af <+105>:	call   0x80484d0 <getenv@plt>

```

### Step1: Exploiting vulnerabilities
```
(gdb) b main
Breakpoint 1 at 0x804894a


(gdb) run
Starting program: /bin/getflag 
Breakpoint 1, 0x0804894a in main ()


(gdb)ju *0x08048b21
Continuing at 0x8048b21.
kooda2puivaav1idi4f57q8iq
*** stack smashing detected ***: /bin/getflag terminated

Program received signal SIGSEGV, Segmentation fault.
0xb7e1cb19 in ?? () from /lib/i386-linux-gnu/libgcc_s.so.1
```

```0x08048b21```
part of a sequence of comparisons against eax, which likely contains the result of getuid()

We find the password of level02

We can try the same process for leve14

we try
```
(gdb) ju *0x08048bbb
Continuing at 0x8048bbb.
7QiHafiNa3HVozsaXkawuYrTstxbpABHD8CPnHJ
*** stack smashing detected ***: /bin/getflag terminated
...
```
```
flag14@SnowCrash:~$ su flag14
Password: 
Congratulation. Type getflag to get the key and send it to me the owner of this livecd :)
flag14@SnowCrash:~$ getflag
Check flag.Here is your token : 7QiHafiNa3HVozsaXkawuYrTstxbpABHD8CPnHJ
```