# Task 5: Running your code

## Testing your code

Good news: you aren't going to write test cases to test your implementation. 

Bad news: you still need to test your code. In order to make sure that your blaster, blastee and middlebox function correctly you will have to use Mininet. The process will be explained below.

## Running your code

Instead of running your implementations with test scenarios, you will be running the agents in Mininet. We are providing you with a topology file \(`start_mininet.py`\) in Mininet. Comments are added so please read them to understand the setup. Please do not change the addresses\(IP and MAC\) or node/link setup. We will be using the same topology file when testing your code \(although we reserve the right to use different delay values\). To spin up your agents in Mininet:

1. Open up a terminal and type the following command. This will get Mininet started and build your topology:

   ```text
    $ sudo python start_mininet.py
   ```

2. Open up a xterm on each agent:

   ```text
    mininet> xterm middlebox
    mininet> xterm blastee
    mininet> xterm blaster
   ```

3. Start your agents:

   ```text
    middlebox# swyard middlebox.py -g 'dropRate=0.19'
    blastee# swyard blastee.py -g 'blasterIp=192.168.100.1 num=100'
    blaster# swyard blaster.py -g 'blasteeIp=192.168.200.1 num=100 length=100 senderWindow=5 timeout=300 recvTimeout=100'
   ```

Your task is: run your code in Mininet as described above. Using different parameters to get different results and analyze them. Using Wireshark on different agent to prove that your blaster, blastee and middlebox function is correct.

✅ Write the procedure and analysis in your report with screenshots.

