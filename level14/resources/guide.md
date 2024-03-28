## Step 0: exploring potential files
level14@SnowCrash:~$ ls -la
total 12
dr-x------ 1 level14 level14  100 Mar  5  2016 .
d--x--x--x 1 root    users    340 Aug 30  2015 ..
-r-x------ 1 level14 level14  220 Apr  3  2012 .bash_logout
-r-x------ 1 level14 level14 3518 Aug 30  2015 .bashrc
-r-x------ 1 level14 level14  675 Apr  3  2012 .profile

level14@SnowCrash:~$ find / -user flag14 -group flag14 2>/dev/null | grep -v proc
level14@SnowCrash:~$ 

Since we get nothing we can explore the gettheflag command

## Step1: exploring gettheflag

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

### Potential vulnerabilities
1. Use of ptrace:

At address 0x08048989, the program calls ptrace. This system call is often used to prevent debugging of the binary. If ptrace returns a non-negative result (checked by test %eax,%eax and followed by jns), it typically means that ptrace detected an attempt to trace the program, usually indicative of reverse engineering efforts.
Potential Vulnerability: A common way to bypass this check is to modify the binary to skip the call to ptrace or to alter its condition check afterward.

2. Call to getenv:

At address 0x080489af, the program calls getenv. This function retrieves the value of an environment variable.
Potential Vulnerability: If the returned environment variable is used unsafely, such as being passed to functions that execute commands (system, exec, etc.) or used in a way that doesn't properly handle special characters, it can lead to environment variable injection attacks. This would depend on subsequent instructions not shown here.

3. Stack Adjustments and Fixed-Size Buffer:

The instructions at 0x0804894a and 0x0804894d adjust the stack pointer and allocate a fixed-size buffer on the stack (sub $0x120,%esp). This is a common setup for buffer usage.
Potential Vulnerability: a common security concern would be a buffer overflow vulnerability. If the program later attempts to copy user-supplied data into this buffer without properly checking the length, it could lead to overflow conditions where an attacker can overwrite adjacent memory, potentially allowing for arbitrary code execution or control flow hijacking.

### Step2: Exploiting vulnerabilities

Bypassing ptrace
