# Weak ciphertext

After connecting to the vm via ssh and logging into level00

## Objective

The objective is to find the password that will log us in with the flagXX account, where XX is the level

### 1. If we just run the command getflag. We will have 

```
level00@SnowCrash:~$ getflag
Check flag.Here is your token : 
Nope there is no token here for you sorry. Try again :)
```

They tell us to find the password for the user flagXX

We want to able to do su flag00 -> finding the files to which user flag00 has some right 

### 2. We run thw following command

```
find / -user flag00 -group flag00 2>/dev/null | grep -v proc
```

This command is used to find all files and directories on the system owned by flag00 
and also belonging to the flag00 group, while ignoring errors and excluding entries from the /proc directory.

This gives the following 
```
/usr/sbin/john
/rofs/usr/sbin/john
```

### 3. We explore the files given 

```
ls -la /usr/sbin/john
```

gives

```
4 ----r--r-- 1 flag00 flag00 15 Mar  5  2016 /usr/sbin/john
```

So it has reading rights

### 4. We cat into the first file to see what it has

```
level00@SnowCrash:~$ cat /usr/sbin/john 

cdiiddwpgswtgt
```

### 5. We decypher it using an online page

https://www.dcode.fr/cipher-identifier

it gives us the result when we use it as the password

```
level00@SnowCrash:~$ su flag00

Password: 

Don't forget to launch getflag !

flag00@SnowCrash:~$ getflag

Check flag.Here is your token : x24ti5gi3x0ol2eh4esiuxias
```