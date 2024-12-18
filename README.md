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