# OverTheWire-Bandit
Solutions to [OverTheWire: Bandit](https://overthewire.org/wargames/bandit/) wargame Level 1-33 (Completed)

## Level 0
Goal: The goal of this level is for you to log into the game using SSH. The host to which you need to connect is bandit.labs.overthewire.org, on port 2220. The username is bandit0 and the password is bandit0. Once logged in, go to the Level 1 page to find out how to beat Level 1.

Enter the wargame by using SSH to connect to host. The username and password are both **bandit0**.
```
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

## Level 0 -> Level 1
Goal: The password for the next level is stored in a file called readme located in the home directory. Use this password to log into bandit1 using SSH. Whenever you find a password for a level, use SSH (on port 2220) to log into that level and continue the game.

Display file content by using `cat`.
```
cat readme
```

## Level 1 -> Level 2
Goal: The password for the next level is stored in a file called - located in the home directory.

To open a file with special name like "-", you cannot `cat` directly.
```
# Method 1
cat < -

# Method 2
cat ./-
```

## Level 2 -> Level 3
Goal: The password for the next level is stored in a file called spaces in this filename located in the home directory

To read a file that has spaces in name, quote the filename.
```
cat "spaces in this filename"
```

## Level 3 -> Level 4
Goal: The password for the next level is stored in a hidden file in the `inhere` directory.

Hidden files that start with "." in their names, will not appear if you directly `ls`. Use `-a` to show hiddend files.

```
ls -a inhere

cat inhere/...Hiding-From-You
```

## Level 4 -> Level 5
Goal: The password for the next level is stored in the only human-readable file in the `inhere` directory. Tip: if your terminal is messed up, try the “reset” command.

There are 10 files in folder "inhere", we can use a loop to go through all files. Command `strings` will print only human-readable content in a file.

```
$ for i in {00..09}; do strings "./-file$i"; done
```

## Level 5 -> Level 6
Goal: The password for the next level is stored in a file somewhere under the `inhere` directory and has all of the following properties:
- human-readable
- 1033 bytes in size
- not executable

We can use command `find` to find a certain file or folder.

```
find . -type f -size 1033c | xargs strings
```

## Level 6 -> Level 7
Goal: The password for the next level is stored in a file somewhere under the `inhere` directory and has all of the following properties:
- owned by user bandit7
- owned by group bandit6
- 33 bytes in size


First we change to root directory using `cd /`. Then we can search on the server.

```
find . -type f -size 33c -user bandit7 -group bandit6 | xargs cat
```

## Level 7 -> Level 8
Goal: The password for the next level is stored in the file data.txt next to the word "millionth".

Command `grep` will help us find the line of text that contains "millionth".
```
grep millionth data.txt
```

## Level 8 -> Level 9
Goal: The password for the next level is stored in the file data.txt and is the only line of text that occurs only once.

Command `uniq` with flag `-u` will display only unique values appeared. However, for `uniq` to be able to filter out the unique lines, the input should be sorted. So we `sort` first and pipe it to `uniq`.

```
sort data.txt | uniq -u
```

## Level 9 -> Level 10
Goal: The password for the next level is stored in the file data.txt in one of the few human-readable strings, preceded by several "=" characters.

Similar to preview levels, use `strings` to filter out ASCII-like characters and we use grep to locate lines with "=".
```
strings data.txt | grep =
```

## Level 10 -> Level 11
Goal: The password for the next level is stored in the file data.txt, which contains base64 encoded data.

To decode the `base64` encoded data, we use `-d` flag.
```
base64 -d data.txt
```

## Level 11 -> Level 12
Goal: The password for the next level is stored in the file data.txt, where all lowercase (a-z) and uppercase (A-Z) letters have been rotated by 13 positions.

The rotation of 13 positions is done by `tr` command, which stands for "translate". `tr` is used like `tr [old_char_sets] [new_char_sets]`. There is no 
built-in command in Linux to encode and decode ROT13.

ROT will substitute A to N, ..., Z->M. `A-Za-z` stands for the 
```
tr 'A-Za-z' 'N-ZA-Mn-za-m' < data.txt
```

## Level 12 -> Level 13
Goal: The password for the next level is stored in the file data.txt, which is a hexdump of a file that has been repeatedly compressed. For this level it may be useful to create a directory under /tmp in which you can work. Use mkdir with a hard to guess directory name. Or better, use the command “mktemp -d”. Then copy the datafile using cp, and rename it using mv (read the manpages!).

`data.txt` is the output of a hexdump. To revert hexdump we can use command `xxd` with flag `-r` which means revert. First we use `mktemp -d` to create a temporary directory to store tmp files. Then we can use `file` to see what type of file it is.
```
# Assume you've saved your tmp dir to variable $tmpdir
xxd -r data.txt > $tmpdir/data.binary
file $tmpdir/data.binary
# Repeat this step to check each decompressed file and rename file accordingly.
gzip -d $filename
bzip2 -d $ filename
tar -xf $filename
```

## Level 13 -> Level 14
Goal: The password for the next level is stored in /etc/bandit_pass/bandit14 and can only be read by user bandit14. For this level, you don’t get the next password, but you get a private SSH key that can be used to log into the next level. Note: localhost is a hostname that refers to the machine you are working on.

We can use the provided ssh key to login as bandit14 without a password. Use flag `-i` of identity_file to specify the private key. 
```
ssh -i sshkey.private bandit14@localhost -p 2220
```

## Level 14 -> Level 15
Goal: The password for the next level can be retrieved by submitting the password of the current level to port 30000 on localhost.

First we get the password of bandit14 by `cat /etc/bandit_pass/bandit14`. Then we need to submit the password to port 30000.
```
nc localhost 30000
# Then enter your password.
```

## Level 15 -> Level 16
Goal: The password for the next level can be retrieved by submitting the password of the current level to port 30001 on localhost using SSL/TLS encryption.

```
openssl s_client -connect localhost:30001
# Then enter your passwrod.
```

## Level 16 -> Level 17
Goal: The credentials for the next level can be retrieved by submitting the password of the current level to a port on localhost in the range 31000 to 32000. First find out which of these ports have a server listening on them. Then find out which of those speak SSL/TLS and which don’t. There is only 1 server that will give the next credentials, the others will simply send back to you whatever you send to it.

`nmap` is a network scanner tool.
```
nmap -p 31000-32000 --open --script ssl-cert localhost
# There are a few ports to try.
openssl s_client -connect localhost:31790
``` 
But I encounter such case: when the password is entered, it prompted "KEYUPDATE". I am not sure what this means but adding flag `-ign_eof` fixed the problem.
```
openssl s_client -connect localhost:31790 -ign_eof
```
Use `mktemp -d`, store the ssh key and `chmod 400` to make the file executable. Use `ssh` to connect to next level.

## Level 17 -> Level 18
Goal: There are 2 files in the homedirectory: `passwords.old` and `passwords.new`. The password for the next level is in `passwords.new` and is the only line that has been changed between `passwords.old` and `passwords.new`.

Command `diff` will print the differences between files.
```
diff passwords.old passwords.new
```

## Level 18 -> Level 19
Goal: The password for the next level is stored in a file readme in the homedirectory. Unfortunately, someone has modified .bashrc to log you out when you log in with SSH.

`.bashrc` is a file that will be run when you log into a terminal. Since we cannot login, we need some other ways to cat the password. We can directly pass the command via `ssh`.
```
ssh bandit18@bandit.labs.overthewire.org -p 2220 cat readme
```

## Level 19 -> Level 20
Goal: To gain access to the next level, you should use the setuid binary in the homedirectory. Execute it without arguments to find out how to use it. The password for this level can be found in the usual place (/etc/bandit_pass), after you have used the setuid binary.

We can use the binary `bandit20-do` to perform command as `bandit20`. Since this file is not in system path, use the relative path to use it.
```
./bandit20-do cat /etc/bandit_pass/bandit20
```

## Level 20 -> Level 21
Goal: There is a setuid binary in the homedirectory that does the following: it makes a connection to localhost on the port you specify as a commandline argument. It then reads a line of text from the connection and compares it to the password in the previous level (bandit20). If the password is correct, it will transmit the password for the next level (bandit21).

We I directly tried `./suconnect 2220`, the line it reads from the connection is `SSH-2.0-OpenSSH_9.6p1`. If we can find a port that has the password of level 20 in the first line of the connection, then we will be able to get the password of level 21.

Here we need to use `nc` again to achieve such goal. We need to create a port that fits our needs...
```
echo -n '0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO' | nc -l -p 9999 &
```

Here the `-n` will make sure `echo` does not output the trailing newline, and `&` will make this process run in the background.
```
./suconne
```

## Level 21 -> Level 22
Goal: A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

First we `cd` to `/etc/cron.d/` and check the cronjobs there.
```
bandit21@bandit:/etc/cron.d$ cat cronjob_bandit22
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
```

Then we look into the scripts that bandit22 runs regularly.
```
bandit21@bandit:/etc/cron.d$ cat /usr/bin/cronjob_bandit22.sh
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```

The password of level 22 is save to `/tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv`.

## Level 22 -> Level 23
Goal: A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

Same as previous level, we see bandit23 cronjob.
```
bandit22@bandit:/etc/cron.d$ cat /usr/bin/cronjob_bandit23.sh
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
```

We know that `whoami` for bandit23 returns `bandit23`.
```
bandit22@bandit:/etc/cron.d$ echo I am user bandit23 | md5sum | cut -d ' ' -f 1
8ca319486bfbbc3663ea0fbe81326349
```

Therefore we can find the password at `/tmp/8ca319486bfbbc3663ea0fbe81326349`

## Level 23 -> Level 24
Goal: A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

Same as preview levels, we check the cronjob first.
```
bandit23@bandit:/etc/cron.d$ cat /usr/bin/cronjob_bandit24.sh
#!/bin/bash

myname=$(whoami)

cd /var/spool/$myname/foo
echo "Executing and deleting all scripts in /var/spool/$myname/foo:"
for i in * .*;
do
    if [ "$i" != "." -a "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" ./$i)"
        if [ "${owner}" = "bandit23" ]; then
            timeout -s 9 60 ./$i
        fi
        rm -f ./$i
    fi
done
```

Similarly we can know that the username is `bandit24`. Then this script will `cd` to `/var/spool/bandit24/foo`. This job will execute the file owned by `bandit23` (our current level) under this directory. So, we can write a shell script that cat the password of level 24 to our tmp directory.
```
#!/bin/bash
cat /etc/bandit_pass/bandit24 > /tmp/tmp.PfDlS1zMzF/password24
```

Then we copy the script into `/var/spool/bandit24/foo` and we should see `password24` in our temp dir.

## Level 24 -> Leve 25
Goal: A daemon is listening on port 30002 and will give you the password for bandit25 if given the password for bandit24 and a secret numeric 4-digit pincode. There is no way to retrieve the pincode except by going through all of the 10000 combinations, called brute-forcing. (You do not need to create new connections each time)

Let's connect to port 30002 to have a look how it looks like by using `nc localhost 30002`. We can enter multiple times in one connection so we don't need to connect multiple times. We can save all possibilities in one file.

```
# Create a temp dir by using "mktemp -d"
bandit24@bandit:/tmp/tmp.q8t5byiUoz$ for i in {0000..9999}
> do
> echo "gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 $i" >> tmp.txt
> done
```

Then we pass all possibilities to port 30002.
```
cat tmp.txt | nc localhost 30002
```

Level 25 -> Level 26