103.2. Process text streams using filters

*Weight: 3*

Description: Candidates should be able to apply filters to text streams.

## Objectives
- Send text files and output streams through text utility filters to modify the output using standard UNIX commands found in the GNU textutils package.


- cat
- cut
- expand
- fmt
- head
- od
- join
- nl
- paste
- pr
- sed
- sort
- split
- tail
- tr
- unexpand
- uniq
- wc

## Streams
In **UNIX** world a lot of data is in TEXT form. Log files, configurations, user inputs,  list of files, ... . **Filtering** this data means taking an input stream of text and performing some conversion on the text before sending it to an output stream. In this contect, a **streams** is nothing more than *"a sequence of bytes that can be read or written using library functions that hide the details of an underlying device from the application"*. 

In simple words, a text stream is an input of text from keyboard, a file, a network device, .. and filtering it is authomatically changing it. 

As you saw in previous section, modern programming environments and shells (including bash) use three standard I/O streams:

- **stdin** is the standard input stream, which provides input to commands.
- **stdout** is the standard output stream, which displays output from commands.
- **stderr** is the standard error stream, which displays error output from commands 

## Piping ( | )
In *normal* cases, you give input from keyboard and output to the monitor. But in real life of a system admin, most inputs come from another commands. If you want to give the output of ````command1```` as the input of ````command2````, you should **PIPE** them as ````command1 | command2````. 


> this | looks like a pipe!

````
jadi@funlife:~/w/lpic/101$ ls -1 | sort
12
62
amir
jadi
neda
you
jadi@funlife:~/w/lpic/101$ ls -1 | sort -r
you
neda
jadi
amir
62
12
````

> UNIX philosophy is building small, strong tools and combine them

## Redirection ( > )
Another useful way of controlling the streams is ````>````. This help you to redirect your output (mostly to a file).

````
jadi@funlife:~/w/lpic/101$ ls -ltrh 
total 0
-rw-rw-r-- 1 jadi jadi 0 Jan  4 17:33 12
-rw-rw-r-- 1 jadi jadi 0 Jan  4 17:33 62
-rw-rw-r-- 1 jadi jadi 0 Jan  4 17:33 neda
-rw-rw-r-- 1 jadi jadi 0 Jan  4 17:33 jadi
-rw-rw-r-- 1 jadi jadi 0 Jan  4 17:33 you
-rw-rw-r-- 1 jadi jadi 0 Jan  4 17:34 amir
jadi@funlife:~/w/lpic/101$ ls -ltrh > directory_data
jadi@funlife:~/w/lpic/101$ cat directory_data 
total 0
-rw-rw-r-- 1 jadi jadi 0 Jan  4 17:33 12
-rw-rw-r-- 1 jadi jadi 0 Jan  4 17:33 62
-rw-rw-r-- 1 jadi jadi 0 Jan  4 17:33 neda
-rw-rw-r-- 1 jadi jadi 0 Jan  4 17:33 jadi
-rw-rw-r-- 1 jadi jadi 0 Jan  4 17:33 you
-rw-rw-r-- 1 jadi jadi 0 Jan  4 17:34 amir
-rw-rw-r-- 1 jadi jadi 0 Jan  4 17:37 directory_data
````

## cat
this command simply outputs its input stream (or the filename you give it). As you saw in previous section. As most commands, if you do not give an input to it, it will read the data from the keyboard.

````
jadi@funlife:~/w/lpic/101$ cat > mydata
test
this is the seccond line
bye
jadi@funlife:~/w/lpic/101$ cat mydata 
test
this is the seccond line
bye
````

> When inputting data, ````ctrl+d```` will end the stream. 

it is also possible to add files to each other using cat:

````
jadi@funlife:~/w/lpic/101$ cat mydata directory_data 
test
this is the seccond line
bye
total 0
-rw-rw-r-- 1 jadi jadi 0 Jan  4 17:33 12
-rw-rw-r-- 1 jadi jadi 0 Jan  4 17:33 62
-rw-rw-r-- 1 jadi jadi 0 Jan  4 17:33 neda
-rw-rw-r-- 1 jadi jadi 0 Jan  4 17:33 jadi
-rw-rw-r-- 1 jadi jadi 0 Jan  4 17:33 you
-rw-rw-r-- 1 jadi jadi 0 Jan  4 17:34 amir
-rw-rw-r-- 1 jadi jadi 0 Jan  4 17:37 directory_data
````

## od
This command *dump*s files (shows files in formats other than text). Normal behaviour is OctalDump (base 8):

````
jadi@funlife:~/w/lpic/101$ od mydata 
0000000 062564 072163 072012 064550 020163 071551 072040 062550
0000020 071440 061545 067543 062156 066040 067151 005145 074542
0000040 005145
0000042
````

Not good.. lets use two switches:
- -t will tell what format to print (````-t a```` for showing only named characters or ````-t c```` for showing escaped chars)
- -A for choosing how to show offsets (````-A```  ````D````ecimal, ````O````ctal, ````H````ex or ````N````one)

> ````od```` is very useful to find problems in your text files - say finding out if you are using tabs or correct line endings

## split
Will split files. It is very useful for transferring HUGE files on smaller media (say splitting a 3TB file to 8GB parts and moving them to another machine with a USB Disk). 

````
jadi@funlife:~/w/lpic/101$ cat mydata 
hello
this is seccond line
but as you can see we are
still writing
and this is getting longer
.
.
and longer
and longer!
jadi@funlife:~/w/lpic/101$ ls
mydata
jadi@funlife:~/w/lpic/101$ split -l 2 mydata 
jadi@funlife:~/w/lpic/101$ ls
mydata	xaa  xab  xac  xad  xae
jadi@funlife:~/w/lpic/101$ cat xab
but as you can see we are
still writing
````

- on normal case, split uses xaa, xab, xac, .. for output files. If can be changed with ````split -l 2 mydata output```` which will lead to outputaa, outputab, ..
- the ````-l 2``` switch told the split to put 2 lines in output files. It is possible to use ````-b 42```` to split every 42 bytes or even ````-n 5```` to force 5 output files.
- if you want numeric output (x00, x01, ..) use ````-d````

## wc
wc is *word count*. It counts the characters, lines and bytes in the input stream.

````
jadi@funlife:~/w/lpic/101$ wc mydata 
  9  25 121 mydata
````

>It is very nowmal to count the line numbers with ````-l```` switch. 

## head & tail
Shows the *head* (top) of a file or its *tail* (bottom). The default lines to show is 10 but you can specify with ````-n20```` or ````-20````. 

>````tali -f```` will continue showing the new lines which are being written at the eng of the file. Very useful. 

## expand & unexpand