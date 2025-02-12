# Linux Prison 3  

## Challenge Description
In this challenge, the goal was to extract the flag despite system restrictions.

```bash
ssh ctf@hearsay3.ctf.dscjssstuniv.in -p 9103
Password: ctf
```

## Initial Exploration
Upon accessing the system, the following files were listed:

```bash
$ ls
README.txt
flag.txt
flags
more_flags
```

However, the `cat` command is unavailable:

```bash
$ cat flag.txt
-sh: 2: cat: not found
```

This necessitates exploring alternative methods to read the file.

## Understanding the Constraints
Listing the files in the directory with `ls -la`:

```bash
$ ls -la
total 36
drwxr-x--- 1 ctf  ctf  4096 Feb 10 08:19 .
drwxr-xr-x 1 root root 4096 Feb 10 08:14 ..
-rw------- 1 ctf  ctf   327 Feb 10 08:43 .bash_history
-rw-r--r-- 1 ctf  ctf   220 Mar 31  2024 .bash_logout
-rw-r--r-- 1 ctf  ctf  3771 Mar 31  2024 .bashrc
drwx------ 2 ctf  ctf  4096 Feb 10 08:15 .cache
-rw-r--r-- 1 ctf  ctf   807 Mar 31  2024 .profile
-rw-r--r-- 1 root root   37 Feb 10 08:14 flag.txt
```

Key observation: `flag.txt` is owned by root but has read permissions for all users (`-rw-r--r--`), meaning we can read it using other available commands.

## Exploring Available Commands
To find an alternative to `cat`, check available commands:

```bash
$ which less more head tail strings awk python3
```

Since `tail` is available, it can be used to read the flag. It effectively bypasses the `cat` restriction.

## Exploiting the Flag
Using `tail`, the flag can be accessed:

```bash
$ tail /home/ctf/flag.txt
```

This command successfully retrieves the flag:

```
dscctf{cat_not_working_huh}
```

## Conclusion
This challenge demonstrates that restricting a single command (`cat`) is not sufficient for security. Alternative commands such as `tail`, `less`, or `head` can still provide access to file contents. A more secure approach would be to enforce proper file permissions or restrict access at the system level.

