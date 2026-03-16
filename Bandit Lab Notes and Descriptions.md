Bandit Wargames Levels 0-33
OverTheWire Bandit Lab Notes

Name: 		Brian Dodd
Purpose: 	Document commands and concepts learned during the Bandit Linux security challenge
------------------------------------------------------------------------------------------------------------
Level 0 > Level 1
Concept: 	Basic SSH login to a remote Linux server.
Cmd Used: 	ssh bandit0@bandit.labs.overthewire.org -p 2220
Explanation: 	Connected to the Bandit server using SSH on port 2220 with the provided credentials.
Result: 		Successfully accessed the Bandit server environment.

Level 1 > Level 2
Concept: 	Reading a file in the current directory.
Cmd Used: 	ls
		cat readme
Explanation: 	Listed files in the directory and used cat to display the contents of the readme file containing the 		password.
Result: 		Password retrieved from the readme file.

Level 2 > Level 3
Concept: 	Handling unusually named files.
Cmd Used: 	cat ./-
Explanation: 	The filename was -, which Linux interprets as stdin. Prefixing with ./ allowed the file to be 		treated as a normal filename.
Result: 		Successfully displayed the password stored in the file.

Level 3 > Level 4
Concept: 	Reading files that contain spaces in the filename.
Cmd Used: 	cat "spaces in this filename"
Explanation: 	The filename contained spaces, which normally separate arguments in the shell. Quotation marks 		ensured the name was treated as a single argument.
Result: 		Password extracted from the file.

Level 4 > Level 5
Concept: 	Finding hidden files in a directory.
Cmd Used: 	ls -a
		cat .hiddenfilename
Explanation: 	Hidden files in Linux begin with a dot (.). Using ls -a revealed the hidden file, which was then 		read using cat.
Result: 		Password obtained from the hidden file.

Level 5 > Level 6
Concept: 	Identifying human-readable files among multiple files.
Cmd Used:	file *
		cat filename
Explanation: 	Used the file command to inspect file types and locate the only human-readable file among 		several binary files.
Result: 		Password discovered in the readable file.

Level 6 > Level 7
Concept: 	Finding files based on size and readability.
Cmd Used: 	find . -type f -size 1033c
		cat ./path/to/file
Explanation: 	Used the find command to locate a file that matched the given conditions: 1033 bytes, human-readable, and non-executable.
Result: 		Password retrieved from the matching file.

Level 7 > Level 8
Concept: 	Finding files with specific user and group ownership.
Cmd Used: 	find / -user bandit7 -group bandit6 -size 33c 2>/dev/null
Explanation: 	Searched the entire system for a file owned by the specified user and group with a size of 33 		bytes. Permission errors were suppressed.
Result: 		Located the correct file and extracted the password.

Level 8 > Level 9
Concept: 	Text searching with grep and command piping.
Cmd Used: 	cat data.txt | grep millionth
Explanation: 	The password was located on the line containing the word “millionth”. grep filtered the file to 		display only that line.
Result: 		Password identified from the filtered output.

Level 9 > Level 10
Concept: 	Finding unique lines using sort and uniq.
Cmd Used: 	sort data.txt | uniq -u
Explanation: 	The password appeared only once in the file. Sorting grouped identical lines together so uniq 		could identify the line appearing a single time.
Result: 		Unique line containing the password was displayed.

Level 10 > Level 11
Concept: 	Extracting readable text from a binary file using strings.
Cmd Used: 	strings data.txt | grep =
Explanation: 	The file contained binary data. The strings command extracted readable text segments, allowing 		the password to be identified among them.
Result: 		Password recovered from the readable string output.

Level 11 > Level 12
Concept: 	Decoding Base64 encoded data.
Cmd Used: 	cat data.txt | base64 -d
Explanation: 	The password was encoded using Base64. The base64 -d command decoded the encoded text to reveal the original password.
Result: 		Password decoded from the Base64 output.

Level 12 > Level 13
Concept: 	Decoding ROT13 substitution cipher using tr.
Cmd Used: 	cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'
Explanation: 	The file was encoded using the ROT13 cipher. The tr command rotated each letter 13 places in the alphabet to restore the original text.
Result: 		Password successfully decoded.

Level 13 > Level 14
Concept: 	Working with hexdumps and layered compression formats.
Cmd Used: 	xxd -r data.txt > data
       file data
Explanation: 	The file was a hexdump of repeatedly compressed data. xxd -r converted the dump back to binary, then the file command helped identify compression types to decompress step-by-step.
Result: 		Final decompressed file revealed the password.

Level 14 > Level 15
Concept: 	Authenticating to a remote system using an SSH private key.
Cmd Used:	 ssh -i sshkey.private bandit14@bandit.labs.overthewire.org -p 2220
Explanation: 	A private SSH key was provided instead of a password. Using the -i option allowed authentication as the next Bandit user.
Result: 		Accessed the next account and retrieved the password from the password directory.

Level 15 > Level 16
Concept: 	Basic network communication using Netcat.
Cmd Used:	 nc localhost 30000
Explanation: 	Connected to a local TCP service running on port 30000. After submitting the current password, the service returned the next password.
Result: 		Received the password from the network service.

Level 16 > Level 17
Concept: 	Encrypted communication using SSL.
Cmd Used:	ncat --ssl localhost 30001
Explanation: 	The service required an encrypted SSL connection. Using ncat --ssl established a secure connection and allowed submission of the password.
Result: 		Server returned the next level’s password.


Level 17 > Level 18
Concept: 	Port scanning and service discovery with Nmap.
Cmd Used: 	nmap -sV -p 31000-32000 localhost
       ncat --ssl localhost <port>
Explanation: 	A port scan identified which services were running within the given port range. The correct SSL port returned a private SSH key.
Result: 		Private key obtained for accessing the next user account.

Level 18 > Level 19
Concept: 	Comparing files to find differences.
Cmd Used: 	diff passwords.old passwords.new
Explanation: 	Two versions of a file were provided. The diff command highlighted the line that changed between them, which contained the password.
Result: 		Password located in the modified line.

Level 19 > Level 20
Concept: 	Running commands remotely during SSH login.
Cmd Used: 	ssh bandit18@bandit.labs.overthewire.org -p 2220 "cat readme"
Explanation: 	The shell immediately logged users out after login. Executing a command directly during the SSH connection allowed the password file to be read.
Result: 		Password displayed before the session terminated.

Level 20 > Level 21
Concept: 	Understanding SUID binaries and privilege escalation.
Cmd Used:	./bandit20-do cat /etc/bandit_pass/bandit20
Explanation: 	The SUID binary executed commands with the permissions of another user. This allowed access to a restricted password file.
Result: 		Password successfully retrieved with elevated permissions.

Level 21 > Level 22
Concept: 	Network communication between processes using Netcat.
Cmd Used:	 nc -l -p <port>
       ./suconnect <port>
Explanation: 	A provided binary connected to a specified port and transmitted the password after receiving the correct input. A Netcat listener captured the response.
Result: 		Password captured from the network connection.

Level 22 > Level 23
Concept: 	Investigating scheduled system tasks (cron jobs).
Cmd Used: 	cat /etc/cron.d/cronjob_bandit22
Explanation: 	The cron configuration revealed a script that periodically stored the next password in a temporary file. Reading that file revealed the password.
Result: 		Password obtained from the cron-generated file.


Level 23 > Level 24
Concept: 	Analyzing scripts used by cron jobs.
Cmd Used: 	cat /usr/bin/cronjob_bandit23.sh
Explanation: 	The cron script generated a filename using an MD5 hash. Reproducing the hash allowed the correct file path to be identified.
Result: 		Password found inside the generated temporary file.

Level 24 > Level 25
Concept: 	Exploiting cron jobs with custom bash scripts.
Cmd Used: 	cp script.sh /var/spool/bandit24/foo
Explanation: 	A cron job executed scripts placed in a specific directory. Uploading a custom script allowed copying the password into a readable file.
Result: 		Password written to a location accessible to the player.

Level 25 > Level 26
Concept: 	Brute-forcing authentication using bash scripting.
Cmd Used: 	cat list.txt | nc localhost 30002
Explanation: 	A script generated combinations of inputs and sent them to a service using Netcat until the 		correct combination returned the password.
Result: 		Correct input identified and password revealed.

Level 26 > Level 27
Concept: 	Escaping restricted environments using pager programs.
Cmd Used: 	v
		:set shell=/bin/bash
Explanation: 	The login environment was restricted. Triggering the more pager allowed entering vim, which 		could spawn a shell and escape the restriction.
Result: 		Full shell access obtained and password retrieved.

Level 27 > Level 28
Concept: 	Using SUID binaries to execute commands as another user.
Cmd Used: 	./bandit27-do cat /etc/bandit_pass/bandit27
Explanation: 	The provided SUID program executed commands with elevated permissions, allowing access to 		restricted files.
Result: 		Password read successfully.

Level 28 > Level 29
Concept: 	Basic Git repository cloning and navigation.
Cmd Used: 	git clone ssh://bandit27-git@localhost/home/bandit27-git/repo
Explanation: 	The password was stored in a Git repository. Cloning the repository locally allowed inspection of 		its files.
Result: 		Password found inside the repository contents.

Level 29 > Level 30
Concept: 	Investigating Git commit history.
Cmd Used: 	git log
		git diff <commit>
Explanation: 	The password had been removed in a later commit. Examining the commit history revealed the 		previous version containing it.
Result: 		Password recovered from earlier commit data.

Level 30 > Level 31
Concept: 	Working with Git branches.
Cmd Used: 	git branch -a
		git checkout dev
Explanation: 	The password was hidden in a non-default branch. Listing and switching branches exposed the 		repository version containing the password.
Result: 		Password located within the development branch.


Level 31 > Level 32
Concept: 	Using Git tags to locate hidden repository data.
Cmd Used: 	git tag
		git show <tag>
Explanation: 	The repository contained a tag pointing to a commit with secret data. Inspecting the tag revealed 		the stored password.
Result: 		Password extracted from the tagged commit.

Level 32 > Level 33
Concept: 	Submitting files to a remote Git repository.
Cmd Used: 	git add key.txt
		git commit -m "Upload file"
		git push
Explanation: 	A file with specific content had to be committed and pushed to the repository. The server 			validated the submission and returned the password.
Result: 		Password provided after successful push.

Level 33
Concept: 	Escaping a restricted shell using environment variables.
Cmd Used: 	$0
       whoami
       cat /etc/bandit_pass/bandit33
Explanation: 	The shell forced uppercase commands, preventing normal execution. Using $0, which references 		the current shell binary, spawned a normal shell session.
Result: 		Escaped the restricted shell and accessed the final password file.

