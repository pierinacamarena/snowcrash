`level09@SnowCrash:~$ ls -la
total 24
dr-x------ 1 level09 level09  140 Mar  5  2016 .
d--x--x--x 1 root    users    340 Aug 30  2015 ..
-r-x------ 1 level09 level09  220 Apr  3  2012 .bash_logout
-r-x------ 1 level09 level09 3518 Aug 30  2015 .bashrc
-rwsr-sr-x 1 flag09  level09 7640 Mar  5  2016 level09
-r-x------ 1 level09 level09  675 Apr  3  2012 .profile
----r--r-- 1 flag09  level09   26 Mar  5  2016 token
level09@SnowCrash:~$ ./level09 
You need to provied only one arg.
level09@SnowCrash:~$ ./level09 token
tpmhr
level09@SnowCrash:~$ cat token 
f4kmm6p|=�p�n��DB�Du{��`

level09@SnowCrash:~$ ./level09 1
1
level09@SnowCrash:~$ ./level09 100
112
level09@SnowCrash:~$ ./level09 1000
1123
level09@SnowCrash:~$ ./level09 abc
ace

a simple transformation algorithm is applied to the input string or number. Each character of the input is being incremented by its position in the string (starting from 0). Encryption by offset

C program that reverses the algorithm

#include <stdio.h>

int main(int ac, char **av) {

        if (ac == 2) {
                for (int i = 0; av[1][i]; i ++)
                        printf("%c", av[1][i] - i);
        }
        printf("\n");
        return (0);
}

I write it in /tmp for permissions

level09@SnowCrash:/tmp$ gcc -std=c99 level9.c

level09@SnowCrash:/tmp$ ./a.out $(cat /home/user/level09/token)