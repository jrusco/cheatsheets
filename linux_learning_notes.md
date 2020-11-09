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

