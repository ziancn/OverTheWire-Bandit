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