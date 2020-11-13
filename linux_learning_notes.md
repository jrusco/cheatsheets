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

### find

Search for files in a directory hier.
Example: `find . -type f -size 33c -group bandit6 -user bandit7 2>/dev/null -exec cat {} +`

* `find .` in current directory and its subdirs
* `-type f` files (`d` for dirs)
* `-size 33c` size of file exactly 33 bytes. Use `+33c` for "bigger than".
* `-group`, `-user` belonging to..
* `2>/dev/null` discard error messages such as "Permission denied"
* `-exec` do something with the results of `find`, in this case:
* `cat {} +` show the contents. `+` indicates that if there is more than one result, it should be appended to the `cat` param list. Not including `+` will only `cat` the first one.

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

## Processes

Brief summary. More in-detail info can be found at <https://linuxjourney.com/> Processes section.

## ps

`ps` shows a snapshot of the current processes

* PID: Process ID
* TTY: Controlling terminal associated with the process
* STAT: Process status code
* TIME: Total CPU usage time
* CMD: Name of executable/command

`ps aux`

* `a` displays all processes running, including the ones being ran by other users.
* `u` shows more details about the processes.
* `x` lists all processes that don't have a TTY associated with it.

`top` gives **real time information** about the processes running on your system instead of a snapshot.

## Controlling terminal

Processes are usually bound to a controlling terminal. If you were running a program on your shell window such as find and you closed the window, your process would also go with it. More details in <https://linuxjourney.com/lesson/controlling-terminal>

## Packages

### Compressing, tar and gzip

`gzip file1` compress a single file
`tar cvf mytarfile.tar file1 file2` pack a tar that contains 2 files
`tar xvf mytarfile.tar` unpack
`tar -xzvf package.tar.gz` decompress and unpack

### Compile Source Code

Often times you will encounter an obscure package that only comes in the form of pure source code. You'll need to:

* install the tools to compile source code: `sudo apt install build-essential`
* extract the package file: `tar -xzvf package.tar.gz`
* look at the README or INSTALL file (inside the package) for instructions

Depending on what compile method that the developer used, you'll have to use different commands. Most commonly you'll see basic `make` compilation. Inside the package contents will be a **configure** script, this script checks for dependencies on your system and if you are missing anything, you'll see an error and you'll need to fix those dependencies.

`./configure` -> The `./` allows you to execute a script in the current directory.

`sudo make install` Inside of the package contents, there is a file called **Makefile** that contains rules to building the software. When you run the make command, it looks at this file to build the software. This command actually installs the package, it will copy the correct files to the correct locations on your computer.

`sudo make uninstall` If you want to uninstall the package.

**Be wary when using make install**, you may not realize how much is actually going on in the background. If you decide to remove this package, you may not actually remove everything because you didn't realize what was added to your system. Instead forget everything about make install that I just explained to you and use the `checkinstall` command. This command will make a `.deb` file for you that you can **easily install and uninstall**.

`sudo checkinstall` Will essentially "make install" and build a .deb package and install it. This makes it easier to remove the package later on.

## Devices

(TODO)

## Filesystem

(TODO)

## Process Utilization



