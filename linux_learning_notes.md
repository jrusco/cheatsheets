# Linux

## Text handling

### stdout

`>` redirect output (overwrites)
`>>` redirect output (appends)

`echo "This is a line" > hello.txt` prints the arg into a new file
`echo "new line" >> hello.txt` outputs to file, or adds it if file already exists

### stdin

`<` redirect input

`cat < hello.txt > banana.txt` takes input from source file and creates a new file with its content

### stderr

**file descriptors**: stdin `0`, stdout `1`, stderr `2`
A file descriptor is a non-negative number that is used to access a file or stream.

`ls /fake/directory 2> peanuts.txt` prints the stderr in the file
`ls /fake/directory > peanuts.txt 2>&1` prints the stderr and the stdout in the file
`ls /fake/directory &> peanuts.txt` shorcut for `2>&1`

### env

`env` shows the current environment vars
`$HOME`, `$USER`, `$PATH` can be printed using `echo $HOME` for example

### head

`head /var/log/syslog` print the first 10 lines of a file, or use `-n` to set the number of lines to display

### tail

Print the last lines of a file. Use `-f` to follow the file content as it grows.
`tail -f /var/log/syslog` Ideal for log files.

### join and split

<https://linuxjourney.com/lesson/join-split-command>

### grep

Look for text and files. Allows regex.
`grep fox sample.txt` search for ocurrences in file
`-i` case insensitive
`ls /somedir | grep '.txt$'` look for txt files inside folder

## VIM

Commands

To insert text you'll need to enter insert mode first.

### Edit modes

i - insert text before the cursor
O - insert text on the previous line
o - insert text on the next line
a - append text after the cursor
A - append text at the end of the line

### Edit

x - used to cut the selected text also used for deleting characters
dd - used to delete the current line
y - yank or copy whatever is selected
yy - yank or copy the current line
p - paste the copied text before the cursor

### Save and quit

:w - writes or saves the file
:q - quit out of vim
:wq - write and then quit
:q! - quit out of vim without saving the file
ZZ - equivalent of :wq, but one character faster
u - undo your last action
Ctrl-r - redo your last action

## Permissions

Example value: `drwxr-xr-x`
The permissions are grouped into 3 bits each. The first 3 bits are user permissions (`rwx`), then group permissions (`r-x`) and then other permissions (`r-x`). 

### Modifying permissions

* Adding permission: Give *the user set* exec permission on file `chmod u+x myfile`
* Combine: Give user and group -> write permision `chmod ug+w myfile`

### Change permissions using numerical format

* 4: read permission
* 2: write permission
* 1: execute permission

Example: `chmod 755 myfile`
`7` is *user* permission, `5` and `5` are *group* and *other* permissions.
`7 = 4 + 2 + 1`, so `7` is the user permissions and it has `rwx` permissions
`5 = 4 + 1`, the group has `rx` permissions.

### Change ownership

* `sudo chown userName fileName`
* `sudo chgrp groupName fileName`
* `sudo chown userName:groupName fileName`

### Process permissions: "real" and "effective" UID

When you launch a process, it runs with the same permissions as the user or group that ran it, this is known as an **effective user ID**. This UID is used to grant access rights to a process.
There is another UID, called the **real user ID** this is the ID of the user that launched the process. These are used to track down who the user who launched the process is.
One last UID is the **saved user ID**, this allows a process to switch between the effective UID and real UID, vice versa. This is useful because we don't want our process to run with elevated privileges all the time, it's just good practice to use special privileges at specific times.

#### Example use case

When running the passwd command, your **effective UID** is your user ID, let's say its 500 for now. Oh but wait, remember the passwd command has the SUID permission enabled. So when you run it, your **effective UID** is now 0 (0 is the UID of root). Now this program can access files as root.
Let's say you get a little taste of power and you want to modify Sally's password, Sally has a UID of 600. Well you'll be out of luck, fortunately the process also has your **real UID** in this case 500. It knows that your UID is 500 and therefore you can't modify the password of UID of 600. (This of course is always bypassed if you are a **superuser** on a machine and can control and change everything).

### The Sticky Bit

This permission bit, "sticks a file/directory" this means that only the owner or the root user can delete or modify the file.

Example: Everyone can `rwx`, but only root can **delete** the `/tmp` directory

```bash
ls -ld /tmp
drwxrwxrwxt 6 root root 4096 Dec 15 11:45 /tmp
```

#### Modify sticky bit

`sudo chmod +t mydir` or `sudo chmod 1755 mydir`

