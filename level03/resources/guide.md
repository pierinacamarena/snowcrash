
## Step 1: Exploring content 
level03@SnowCrash:~$ ls -la
total 24
dr-x------ 1 level03 level03  120 Mar  5  2016 .
d--x--x--x 1 root    users    340 Aug 30  2015 ..
-r-x------ 1 level03 level03  220 Apr  3  2012 .bash_logout
-r-x------ 1 level03 level03 3518 Aug 30  2015 .bashrc
-rwsr-sr-x 1 flag03  level03 8627 Mar  5  2016 level03
-r-x------ 1 level03 level03  675 Apr  3  2012 .profile

### Observations:

- `flag03`: This is the owner of the file. The file is owned by the user `flag03`.s

- `level03`: This is the group that the file belongs to. The file is associated with the group `level03`.

The presence of the setuid bit (`s` in the owner's permission) is particularly noteworthy. It means that anyone executing this file will have the same privileges as the owner of the file (`flag03`) during its execution. This can have security implications, especially if the file allows for more privileged actions than intended for general users.

## Step 2: Investigating the file using ltrace 
ltrace is a common tool used in software development and security analysis for understanding the behavior of binaries, especially when source code is not available or when trying to diagnose runtime issues or vulnerabilities.

ltrace ./level03
__libc_start_main(0x80484a4, 1, 0xbffff7f4, 0x8048510, 0x8048580 <unfinished ...>
getegid()                                        = 2003
geteuid()                                        = 2003
setresgid(2003, 2003, 2003, 0xb7e5ee55, 0xb7fed280) = 0
setresuid(2003, 2003, 2003, 0xb7e5ee55, 0xb7fed280) = 0
system("/usr/bin/env echo Exploit me"Exploit me
 <unfinished ...>
--- SIGCHLD (Child exited) ---
<... system resumed> )                           = 0
+++ exited (status 0) +++

### Some key security notions: 

- PATH vulnerability 
    - PATH tells the system where to look for executable files when a command is type without a full path
    - Vulnerability: relying on relative paths -> Risk: the system might execute a different program than intended if the attacker can control what is in the path 

- System call of a program 
    - Noticing that a program uses system calls to execute common utilities (like echo) without specifying an absolute path can raise a red flag.
    - Noticing that an executable has the SUID bit set and runs with elevated privileges under certain conditions (e.g., runs as a different user) suggests that if you could control what the executable does
    -  output shows that the program makes a call to system("/usr/bin/env echo Exploit me") 
    - The use of /usr/bin/env to find echo implies it relies on the environment's PATH variable to locate the echo binary. This method does not specify an absolute path to echo, which indeed raises a red flag for a potential PATH vulnerability.
- SUID bit
    - geteuid() and getegid() return the effective user id and group id of the process. Since both return 2003 it tells us the process might be runing with elevated privileges
    - calls of setresuid() and setresgid() set the real effective user/group ids for the process, IF THE PROGRAM DID NOT HAVE ELEVATED PRIVILEGES, it would not need to explictly set 

### Connecting the Dots

- Putting these pieces together—knowing the system looks in PATH for how to execute commands, understanding the SUID bit gives a file special permissions, and seeing a program call a utility like echo without an absolute path—leads to the realization that manipulating PATH could make the system execute a different echo than intended.

- If we can make the system execute our version of echo, which actually runs something else (like getflag), then we can potentially escalate our privileges or execute commands we normally couldn’t.

### Step3: Create symbolic link

export PATH="/tmp:$PATH" 
- Prepending /tmp to ensure it looks first in /tmp (standard world writable directory, temporary so it does not raise suspicions) 

cd /tmp
- we need to be in temp to create a symbolic link

ln -s /bin/getflag echo
- creating symbolic link to /bin/getflag  (instead of running echo it will run /bin/getflag)

cd $OLDPWD

- go back to old path to run ./level03

./level03
