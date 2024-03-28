# Weak hashed password stored in world-readable file

## Step 0: explore passwords

```
level01@SnowCrash:~$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/bin/sh
bin:x:2:2:bin:/bin:/bin/sh
...
level06:x:2006:2006::/home/user/level06:/bin/bash
level07:x:2007:2007::/home/user/level07:/bin/bash
level08:x:2008:2008::/home/user/level08:/bin/bash
level09:x:2009:2009::/home/user/level09:/bin/bash
level10:x:2010:2010::/home/user/level10:/bin/bash
level11:x:2011:2011::/home/user/level11:/bin/bash
level12:x:2012:2012::/home/user/level12:/bin/bash
level13:x:2013:2013::/home/user/level13:/bin/bash
level14:x:2014:2014::/home/user/level14:/bin/bash
flag00:x:3000:3000::/home/flag/flag00:/bin/bash
flag01:42hDRfypTqqnw:3001:3001::/home/flag/flag01:/bin/bash
flag02:x:3002:3002::/home/flag/flag02:/bin/bash
flag03:x:3003:3003::/home/flag/flag03:/bin/bash
...

```

## John not available in the vm 

We will run it in a docker

### SETUP
1. create a directory called /JTR
```
cd /JTR
```
2. 
    ```
    echo '42hDRfypTqqnw' > psswd.txt
    ```

3. Create a dockerfile

```
FROM ubuntu:latest

RUN apt-get update && apt-get install -y \
    john

COPY . /app

WORKDIR /app

CMD ["bash"]
```

4. build the image 
```
docker build -t myjtr .
```

5. run container
```
docker run -it myjtr
```

6. run john 
```
root@c30292710cd6:/app# john psswd.txt --show
?:abcdefg

1 password hash cracked, 0 left
```


password: abcdefg

su flag01

getflag

f2av5il02puano7naaf6adaaf