# Exploiting Execution Environment Manipulation to Bypass UID Security Checks

The security vulnerability in this scenario is not a flaw in the binary itself but rather the ability to manipulate the execution environment (using GDB) to bypass security checks. The binary relies on the UID for a security check without considering that a debugger can manipulate the execution flow and data. This reliance on easily manipulated conditions is the underlying security weakness.

## We explore the files

level13@SnowCrash:~$ ls -la
total 20
dr-x------ 1 level13 level13  120 Mar  5  2016 .
d--x--x--x 1 root    users    340 Aug 30  2015 ..
-r-x------ 1 level13 level13  220 Apr  3  2012 .bash_logout
-r-x------ 1 level13 level13 3518 Aug 30  2015 .bashrc
-rwsr-sr-x 1 flag13  level13 7303 Aug 30  2015 level13
-r-x------ 1 level13 level13  675 Apr  3  2012 .profile

level13@SnowCrash:~$ ./level13 
UID 2013 started us but we we expect 4242

This tells us that the binary will omly provide the desired output
if the UID is 4242 

### Exploring the binary

running strings, it outputs a list of recognizable strings found within the binary file. 

level13@SnowCrash:~$ strings ./level13 
/lib/ld-linux.so.2
__gmon_start__
libc.so.6
_IO_stdin_used
exit
strdup
printf
getuid (retrieves the current user's UID (User Identifier).)
__libc_start_main
GLIBC_2.0
PTRh`
UWVS


### Using gdb to analyze the binary

(gdb) disas main
Dump of assembler code for function main:
   0x0804858c <+0>:	push   %ebp
   0x0804858d <+1>:	mov    %esp,%ebp
   0x0804858f <+3>:	and    $0xfffffff0,%esp
   0x08048592 <+6>:	sub    $0x10,%esp
   0x08048595 <+9>:	call   0x8048380 <getuid@plt>
   0x0804859a <+14>:	cmp    $0x1092,%eax
   0x0804859f <+19>:	je     0x80485cb <main+63>
   0x080485a1 <+21>:	call   0x8048380 <getuid@plt>
   0x080485a6 <+26>:	mov    $0x80486c8,%edx
   0x080485ab <+31>:	movl   $0x1092,0x8(%esp)
   0x080485b3 <+39>:	mov    %eax,0x4(%esp)
   0x080485b7 <+43>:	mov    %edx,(%esp)
   0x080485ba <+46>:	call   0x8048360 <printf@plt>
   0x080485bf <+51>:	movl   $0x1,(%esp)
   0x080485c6 <+58>:	call   0x80483a0 <exit@plt>
   0x080485cb <+63>:	movl   $0x80486ef,(%esp)
   0x080485d2 <+70>:	call   0x8048474 <ft_des>
   0x080485d7 <+75>:	mov    $0x8048709,%edx
   0x080485dc <+80>:	mov    %eax,0x4(%esp)
   0x080485e0 <+84>:	mov    %edx,(%esp)
   0x080485e3 <+87>:	call   0x8048360 <printf@plt>
   0x080485e8 <+92>:	leave  


GDB allows the modification of registers during the execution,

So we can see the call to getuid
0x08048595 <+9>:	call   0x8048380 <getuid@plt>

The return value from getuid will be placed in the eax register, a general-purpose register commonly used to store return values from functions.

The line
0x0804859a <+14>:	cmp    $0x1092,%eax
Performs a comparison among two values
The $0x1092 is an immediate value (in hexadecimal notation) representing the number 4242 in decimal. %eax refers to the eax register, which, as mentioned above, should now contain the UID returned by getuid.

### Using gdb to modify the register 

(gdb) break *0x0804859a
Breakpoint 1 at 0x804859a
(gdb) run
Starting program: /home/user/level13/level13 

Breakpoint 1, 0x0804859a in main ()
(gdb) set var $eax=4242
(gdb) continue
Continuing.
your token is 2A31L79asukciNyi8uppkEuSx
[Inferior 1 (process 32692) exited with code 050]
