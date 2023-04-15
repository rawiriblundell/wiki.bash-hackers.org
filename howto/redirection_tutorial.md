

====== Illustrated Redirection Tutorial ======

{{keywords&gt;bash shell scripting tutorial redirection redirect file descriptor}}

This tutorial is not a complete guide to redirection, it will not
cover here docs, here strings, name pipes etc... I just hope it'll help
you to understand what things like ''3&gt;&amp;2'', ''2&gt;&amp;1'' or ''1&gt;&amp;3-'' do.


====== stdin, stdout, stderr ======

When Bash starts, normally, 3 file descriptors are opened, ''0'', ''1'' and ''2''
also known as standard input (''stdin''), standard output (''stdout'') and
standard error (''stderr'').

For example, with Bash running in a Linux terminal emulator, you'll see:

&lt;code&gt;
# lsof +f g -ap $BASHPID -d 0,1,2
COMMAND   PID USER   FD   TYPE FILE-FLAG DEVICE SIZE/OFF NODE NAME
bash    12135 root    0u   CHR     RW,LG 136,13      0t0   16 /dev/pts/5
bash    12135 root    1u   CHR     RW,LG 136,13      0t0   16 /dev/pts/5
bash    12135 root    2u   CHR     RW,LG 136,13      0t0   16 /dev/pts/5
&lt;/code&gt;

This ''/dev/pts/5'' is a pseudo terminal used to emulate a real
terminal. Bash reads (''stdin'') from this terminal and prints
via ''stdout'' and ''stderr'' to this terminal.

&lt;code&gt;
                  ---       +-----------------------+
standard input   ( 0 ) ----&gt;| /dev/pts/5            |
                  ---       +-----------------------+

                  ---       +-----------------------+
standard output  ( 1 ) ----&gt;| /dev/pts/5            |
                  ---       +-----------------------+

                  ---       +-----------------------+
standard error   ( 2 ) ----&gt;| /dev/pts/5            |
                  ---       +-----------------------+
&lt;/code&gt;

When a command, a compound command, a subshell etc. is executed, it inherits
these file descriptors. For instance ''echo foo'' will send the text ''foo'' to the file
descriptor ''1'' inherited from the shell, which is connected to ''/dev/pts/5''.

====== Simple Redirections ======
===== Output Redirection &quot;n&gt; file&quot; =====

''&gt;'' is probably the simplest redirection.

''echo foo &gt; file''

the ''&gt; file'' after the command alters the file descriptors belonging to the
command ''echo''. It changes the file descriptor ''1'' (''&gt; file'' is the same as
''1&gt;file'') so that it points to the file ''file''. They will look like:

&lt;code&gt;
                  ---       +-----------------------+
standard input   ( 0 ) ----&gt;| /dev/pts/5            |
                  ---       +-----------------------+

                  ---       +-----------------------+
standard output  ( 1 ) ----&gt;| file                  |
                  ---       +-----------------------+

                  ---       +-----------------------+
standard error   ( 2 ) ----&gt;| /dev/pts/5            |
                  ---       +-----------------------+
&lt;/code&gt;

Now characters written by our command, ''echo'', that are sent to the
standard output, i.e., the file descriptor ''1'', end up in the file
named ''file''.

In the same way, command ''2&gt; file'' will change the standard error and
will make it point to ''file''. Standard error is used by applications to print errors.

What will ''command 3&gt; file'' do? It will open a new file descriptor
pointing to ''file''. The command will then start with:

&lt;code&gt;
                  ---       +-----------------------+
standard input   ( 0 ) ----&gt;| /dev/pts/5            |
                  ---       +-----------------------+

                  ---       +-----------------------+
standard output  ( 1 ) ----&gt;| /dev/pts/5            |
                  ---       +-----------------------+

                  ---       +-----------------------+
standard error   ( 2 ) ----&gt;| /dev/pts/5            |
                  ---       +-----------------------+

                  ---       +-----------------------+
new descriptor   ( 3 ) ----&gt;| file                  |
                  ---       +-----------------------+
&lt;/code&gt;

What will the command do with this descriptor?  It depends. Often nothing.
We will see later why we might want other file descriptors.

===== Input Redirection &quot;n&lt; file&quot; =====

When you run a commandusing ''command &lt; file'', it changes the
file descriptor ''0'' so that it looks like:

&lt;code&gt;
                  ---       +-----------------------+
standard input   ( 0 ) &lt;----| file                  |
                  ---       +-----------------------+

                  ---       +-----------------------+
standard output  ( 1 ) ----&gt;| /dev/pts/5            |
                  ---       +-----------------------+

                  ---       +-----------------------+
standard error   ( 2 ) ----&gt;| /dev/pts/5            |
                  ---       +-----------------------+
&lt;/code&gt;

If the command reads from ''stdin'', it now will read from ''file'' and not
from the console.

As with ''&gt;'', ''&lt;'' can be used to open a new file descriptor for reading,
''command 3&lt;file''. Later we will see how this can be useful.


===== Pipes | =====

What does this ''|'' do? Among other things, it connects the standard output of
the command on the left to the standard input of the command on the right.
That is, it creates a special file, a pipe, which is opened as a write destinaton
for the left command, and as a read source for the right command.

&lt;code&gt;
           echo foo               |                cat

 ---       +--------------+               ---       +--------------+
( 0 ) ----&gt;| /dev/pts/5   |     ------&gt;  ( 0 ) ----&gt;|pipe (read)   |
 ---       +--------------+    /          ---       +--------------+
                              /
 ---       +--------------+  /            ---       +--------------+
( 1 ) ----&gt;| pipe (write) | /            ( 1 ) ----&gt;| /dev/pts     |
 ---       +--------------+               ---       +--------------+

 ---       +--------------+               ---       +--------------+
( 2 ) ----&gt;| /dev/pts/5   |              ( 2 ) ----&gt;| /dev/pts/    |
 ---       +--------------+               ---       +--------------+

&lt;/code&gt;

This is possible because the redirections are set up by the shell
**before** the commands are executed, and the commands inherit the file
descriptors.

====== More On File Descriptors ======
===== Duplicating File Descriptor 2&gt;&amp;1 =====

We have seen how to open (or redirect) file descriptors. Let us see
how to duplicate them, starting with the classic ''2&gt;&amp;1''.  What does this
mean? That something written on the file descriptor ''2'' will go where
file descriptor ''1'' goes. In a shell ''command 2&gt;&amp;1'' is not a very
interesting example so we will use ''ls /tmp/ doesnotexist 2&gt;&amp;1 | less''

&lt;code&gt;
   ls /tmp/ doesnotexist 2&gt;&amp;1     |                   less

 ---       +--------------+              ---       +--------------+
( 0 ) ----&gt;| /dev/pts/5   |     ------&gt; ( 0 ) ----&gt;|from the pipe |
 ---       +--------------+    /   ---&gt;  ---       +--------------+
                              /   /
 ---       +--------------+  /   /       ---       +--------------+
( 1 ) ----&gt;| to the pipe  | /   /       ( 1 ) ----&gt;|  /dev/pts    |
 ---       +--------------+    /         ---       +--------------+
      	       	       	      /
 ---       +--------------+  /           ---       +--------------+
( 2 ) ----&gt;|  to the pipe | /           ( 2 ) ----&gt;| /dev/pts/    |
 ---       +--------------+              ---       +--------------+
&lt;/code&gt;

Why is it called //duplicating//? Because after ''2&gt;&amp;1'', we have 2 file
descriptors pointing to the same file. Take care not to call this
&quot;File Descriptor Aliasing&quot;; if we redirect ''stdout'' after ''2&gt;&amp;1'' to a
file ''B'', file descriptor ''2'' will still be opened on the file ''A'' where it was.  This is
often misunderstood by people wanting to redirect both standard input
and standard output to the file. Continue reading for more on this.

So if you have two file descriptors ''s'' and ''t'' like:
&lt;code&gt;
                  ---       +-----------------------+
 a descriptor    ( s ) ----&gt;| /some/file            |
                  ---       +-----------------------+
                  ---       +-----------------------+
 a descriptor    ( t ) ----&gt;| /another/file         |
                  ---       +-----------------------+
&lt;/code&gt;

Using a ''t&gt;&amp;s'' (where ''t'' and ''s'' are numbers) it means: 

&gt; Copy whatever file descriptor ''s'' contains into file descriptor ''t''

So you got a copy of this descriptor:

&lt;code&gt;
                  ---       +-----------------------+
 a descriptor    ( s ) ----&gt;| /some/file            |
                  ---       +-----------------------+
                  ---       +-----------------------+
 a descriptor    ( t ) ----&gt;| /some/file            |
                  ---       +-----------------------+
&lt;/code&gt;

Internally each of these is represented by a file descriptor opened by the operating system's ''fopen'' calls, and is likely just a pointer to the file which has been opened for reading (''stdin'' or file descriptor ''0'') or writing (''stdout'' /''stderr''). 

Note that the file reading or writing positions are also duplicated. If you have already
read a line of ''s'', then after ''t&gt;&amp;s'' if you read a line from ''t'', you will
get the second line of the file. 

Similarly for output file descriptors, writing a line to file descriptor ''s'' will append a line to a file as will writing a line to file descriptor ''t''.

&lt;note tip&gt;The syntax is somewhat confusing in that you would think that the arrow would point in the direction of the copy, but it's reversed. So it's ''target&gt;&amp;source'' effectively.&lt;/note&gt;

So, as a simple example (albeit slightly contrived), is the following:

&lt;code&gt;
exec 3&gt;&amp;1         # Copy 1 into 3
exec 1&gt; logfile   # Make 1 opened to write to logfile
lotsa_stdout      # Outputs to fd 1, which writes to logfile
exec 1&gt;&amp;3         # Copy 3 back into 1
echo Done         # Output to original stdout
&lt;/code&gt;


===== Order Of Redirection, i.e., &quot;&gt; file 2&gt;&amp;1&quot; vs. &quot;2&gt;&amp;1 &gt;file&quot; =====

While it doesn't matter where the redirections appears on the command
line, their order does matter. They are set up from left to right.

  * ''2&gt;&amp;1 &gt;file''

A common error, is to do ''command 2&gt;&amp;1 &gt; file'' to redirect both ''stderr''
and ''stdout'' to ''file''. Let's see what's going on. First we type the
command in our terminal, the descriptors look like this:

&lt;code&gt;
                  ---       +-----------------------+
standard input   ( 0 ) ----&gt;| /dev/pts/5            |
                  ---       +-----------------------+

                  ---       +-----------------------+
standard output  ( 1 ) ----&gt;| /dev/pts/5            |
                  ---       +-----------------------+

                  ---       +-----------------------+
standard error   ( 2 ) ----&gt;| /dev/pts/5            |
                  ---       +-----------------------+
&lt;/code&gt;

Then our shell, Bash sees ''2&gt;&amp;1'' so it duplicates 1, and the file
descriptor look like this:

&lt;code&gt;
                  ---       +-----------------------+
standard input   ( 0 ) ----&gt;| /dev/pts/5            |
                  ---       +-----------------------+

                  ---       +-----------------------+
standard output  ( 1 ) ----&gt;| /dev/pts/5            |
                  ---       +-----------------------+

                  ---       +-----------------------+
standard error   ( 2 ) ----&gt;| /dev/pts/5            |
                  ---       +-----------------------+
&lt;/code&gt;

That's right, nothing has changed, 2 was already pointing to the same
place as 1. Now Bash sees ''&gt; file'' and thus changes ''stdout'':

&lt;code&gt;
                  ---       +-----------------------+
standard input   ( 0 ) ----&gt;| /dev/pts/5            |
                  ---       +-----------------------+

                  ---       +-----------------------+
standard output  ( 1 ) ----&gt;| file                  |
                  ---       +-----------------------+

                  ---       +-----------------------+
standard error   ( 2 ) ----&gt;| /dev/pts/5            |
                  ---       +-----------------------+
&lt;/code&gt;

And that's not what we want.

  * ''&gt;file 2&gt;&amp;1''

Now let's look at the correct ''command &gt;file 2&gt;&amp;1''. We start as in the
previous example, and Bash sees ''&gt; file'':

&lt;code&gt;
                  ---       +-----------------------+
standard input   ( 0 ) ----&gt;| /dev/pts/5            |
                  ---       +-----------------------+

                  ---       +-----------------------+
standard output  ( 1 ) ----&gt;| file                  |
                  ---       +-----------------------+

                  ---       +-----------------------+
standard error   ( 2 ) ----&gt;| /dev/pts/5            |
                  ---       +-----------------------+
&lt;/code&gt;

Then it sees our duplication ''2&gt;&amp;1'':

&lt;code&gt;
                  ---       +-----------------------+
standard input   ( 0 ) ----&gt;| /dev/pts/5            |
                  ---       +-----------------------+

                  ---       +-----------------------+
standard output  ( 1 ) ----&gt;| file                  |
                  ---       +-----------------------+

                  ---       +-----------------------+
standard error   ( 2 ) ----&gt;| file                  |
                  ---       +-----------------------+
&lt;/code&gt;

And voila, both ''1'' and ''2'' are redirected to file.

===== Why sed 's/foo/bar/' file &gt;file Doesn't Work =====

This is a common error, we want to modify a file using something that
reads from a file and writes the result to ''stdout''. To do this, we redirect stdout
to the file we want to modify. The problem here is that, as we
have seen, the redirections are setup before the command is actually
executed.

So **BEFORE** sed starts, standard output has already been redirected, with
the additional side effect that, because we used &gt;, &quot;file&quot; gets truncated. When ''sed'' starts to
read the file, it contains nothing.


===== exec =====

In Bash the ''exec'' built-in replaces the shell with the specified
program. So what does this have to do with redirection?
''exec'' also allow us to manipulate the file descriptors.
If you don't specify a program, the redirection after ''exec'' modifies the
file descriptors of the current shell.

For example, all the commands after ''exec 2&gt;file'' will have file
descriptors like:
&lt;code&gt;
                  ---       +-----------------------+
standard input   ( 0 ) ----&gt;| /dev/pts/5            |
                  ---       +-----------------------+

                  ---       +-----------------------+
standard output  ( 1 ) ----&gt;| /dev/pts/5            |
                  ---       +-----------------------+

                  ---       +-----------------------+
standard error   ( 2 ) ----&gt;| file	            |
                  ---       +-----------------------+
&lt;/code&gt;

All the the errors sent to ''stderr'' by the commands after the ''exec 2&gt;file''
will go to the file, just as if you had the command in a script and
ran ''myscript 2&gt;file''.

''exec'' can be used, if, for instance, you want to log the errors the
commands in your script produce, just add ''exec 2&gt;myscript.errors'' at
the beginning of your script.

Let's see another use case. We want to read a file line by line, this
is easy, we just do:

&lt;code&gt;
 while read -r line;do echo &quot;$line&quot;;done &lt; file
&lt;/code&gt;

Now, we want, after printing each line, to do a pause, waiting for
the user to press a key:

&lt;code&gt;
 while read -r line;do echo &quot;$line&quot;; read -p &quot;Press any key&quot; -n 1;done &lt; file
&lt;/code&gt;

And, surprise this doesn't work. Why? because the shell descriptor of
the while loop looks like:

&lt;code&gt;
                  ---       +-----------------------+
standard input   ( 0 ) ----&gt;| file                  |
                  ---       +-----------------------+

                  ---       +-----------------------+
standard output  ( 1 ) ----&gt;| /dev/pts/5            |
                  ---       +-----------------------+

                  ---       +-----------------------+
standard error   ( 2 ) ----&gt;| /dev/pts/5            |
                  ---       +-----------------------+
&lt;/code&gt;

and our read inherits these descriptors, and our command (''read -p &quot;Press any key&quot; -n 1'')
inherits them, and thus reads from file and not from our terminal.

A quick look at ''help read'' tells us that we can specify a file
descriptor from which ''read'' should read. Cool. Now let's use ''exec'' to
get another descriptor:

&lt;code&gt;
 exec 3&lt;file
 while read -u 3 line;do echo &quot;$line&quot;; read -p &quot;Press any key&quot; -n 1;done
&lt;/code&gt;

Now the file descriptors look like:

&lt;code&gt;
                  ---       +-----------------------+
standard input   ( 0 ) ----&gt;| /dev/pts/5            |
                  ---       +-----------------------+

                  ---       +-----------------------+
standard output  ( 1 ) ----&gt;| /dev/pts/5            |
                  ---       +-----------------------+

                  ---       +-----------------------+
standard error   ( 2 ) ----&gt;| /dev/pts/5            |
                  ---       +-----------------------+

                  ---       +-----------------------+
new descriptor   ( 3 ) ----&gt;| file                  |
                  ---       +-----------------------+
&lt;/code&gt;

and it works.


===== Closing The File Descriptors =====

Closing a file through a file descriptor is easy, just make it a duplicate of  -.
For instance, let's close ''stdin &lt;&amp;-'' and ''stderr 2&gt;&amp;-'':

&lt;code&gt;
 bash -c '{ lsof -a -p $$ -d0,1,2 ;} &lt;&amp;- 2&gt;&amp;-'
 COMMAND   PID USER   FD   TYPE DEVICE SIZE NODE NAME
 bash    10668 pgas    1u   CHR  136,2         4 /dev/pts/2
&lt;/code&gt;
we see that inside the ''{}'' that only ''1'' is still here.

Though the OS will probably clean up the mess, it is perhaps a
good idea to close the file descriptors you open. For instance, if you
open a file descriptor with ''exec 3&gt;file'', all the commands afterwards
will inherit it. It's probably better to do something like:

&lt;code&gt;
exec 3&gt;file
.....
#commands that uses 3
.....
exec 3&gt;&amp;-

#we don't need 3 any more
&lt;/code&gt;

I've seen some people using this as a way to discard, say stderr, using something like: command 2&gt;&amp;-.
Though it might work, I'm not sure if you can expect all applications to behave correctly with a closed stderr.

When in doubt, I use 2&gt;/dev/null.
 

====== An Example ======

This example comes from [[http://groups.google.com/group/comp.unix.shell/browse_thread/thread/64206d154894a4ef/ffe4c2e382034ed9#ffe4c2e382034ed9|this post (ffe4c2e382034ed9)]] on the comp.unix.shell group:

&lt;code&gt;
{
  {
    cmd1 3&gt;&amp;- |
      cmd2 2&gt;&amp;3 3&gt;&amp;-
  } 2&gt;&amp;1 &gt;&amp;4 4&gt;&amp;- |
    cmd3 3&gt;&amp;- 4&gt;&amp;-

} 3&gt;&amp;2 4&gt;&amp;1
&lt;/code&gt;

The redirections are processed from left to right, but as the file
descriptors are inherited we will also have to work from the outer to
the inner contexts. We will assume that we run this command in a
terminal. Let's start with the outer ''{ } 3&gt;&amp;2 4&gt;&amp;1''.

&lt;code&gt;
 ---       +-------------+    ---       +-------------+
( 0 ) ----&gt;| /dev/pts/5  |   ( 3 ) ----&gt;| /dev/pts/5  |
 ---       +-------------+    ---       +-------------+

 ---       +-------------+    ---       +-------------+
( 1 ) ----&gt;| /dev/pts/5  |   ( 4 ) ----&gt;| /dev/pts/5  |
 ---       +-------------+    ---       +-------------+

 ---       +-------------+
( 2 ) ----&gt;| /dev/pts/5  |
 ---       +-------------+
&lt;/code&gt;

We only made 2 copies of ''stderr'' and ''stdout''. ''3&gt;&amp;1 4&gt;&amp;1'' would have
produced the same result here because we ran the command in a terminal
and thus ''1'' and ''2'' go to the terminal. As an exercise, you can start
with ''1'' pointing to ''file.stdout'' and 2 pointing to ''file.stderr'', you will
see why these redirections are very nice.

Let's continue with the right part of the second pipe: ''| cmd3 3&gt;&amp;- 4&gt;&amp;-''

&lt;code&gt;
 ---       +-------------+
( 0 ) ----&gt;| 2nd pipe    |
 ---       +-------------+

 ---       +-------------+
( 1 ) ----&gt;| /dev/pts/5  |
 ---       +-------------+

 ---       +-------------+
( 2 ) ----&gt;| /dev/pts/5  |
 ---       +-------------+
&lt;/code&gt;

It inherits the previous file descriptors, closes 3 and 4 and sets up a pipe
for reading. Now for the left part of the second pipe ''{...}  2&gt;&amp;1 &gt;&amp;4 4&gt;&amp;- |''

&lt;code&gt;
 ---       +-------------+  ---       +-------------+
( 0 ) ----&gt;| /dev/pts/5  | ( 3 ) ----&gt;| /dev/pts/5  |
 ---       +-------------+  ---       +-------------+

 ---       +-------------+
( 1 ) ----&gt;| /dev/pts/5  |
 ---       +-------------+

 ---       +-------------+
( 2 ) ----&gt;| 2nd pipe    |
 ---       +-------------+
&lt;/code&gt;

First, The file descriptor ''1'' is connected to the pipe (''|''), then ''2'' is
made a copy of ''1'' and thus is made an fd to the pipe (''2&gt;&amp;1''), then ''1'' is
made a copy of ''4'' (''&gt;&amp;4''), then ''4'' is closed. These are the file
descriptors of the inner ''{}''. Lcet's go inside and have a look at the
right part of the first pipe: ''| cmd2 2&gt;&amp;3 3&gt;&amp;-''

&lt;code&gt;
 ---       +-------------+
( 0 ) ----&gt;| 1st pipe    |
 ---       +-------------+

 ---       +-------------+
( 1 ) ----&gt;| /dev/pts/5  |
 ---       +-------------+

 ---       +-------------+
( 2 ) ----&gt;| /dev/pts/5  |
 ---       +-------------+
&lt;/code&gt;

It inherits the previous file descriptors, connects 0 to the 1st pipe,
the file descriptor 2 is made a copy of 3, and 3 is closed. Finally, for the left
part of the pipe:

&lt;code&gt;
 ---       +-------------+
( 0 ) ----&gt;| /dev/pts/5  |
 ---       +-------------+

 ---       +-------------+
( 1 ) ----&gt;| 1st pipe    |
 ---       +-------------+

 ---       +-------------+
( 2 ) ----&gt;| 2nd pipe    |
 ---       +-------------+
&lt;/code&gt;

It also inherits the file descriptor of the left part of the 2nd
pipe, file descriptor ''1'' is connected to the first pipe, ''3'' is closed.

The purpose of all this becomes clear if we take only the commands:

&lt;code&gt;
                                                   cmd2

                                           ---       +-------------+
				       --&gt;( 0 ) ----&gt;| 1st pipe    |
				      /	   ---       +-------------+
				     /
                                    /	   ---       +-------------+
         cmd 1                	   /	  ( 1 ) ----&gt;| /dev/pts/5  |
				  /	   ---       +-------------+
				 /
 ---       +-------------+	/	   ---       +-------------+
( 0 ) ----&gt;| /dev/pts/5  |     /	  ( 2 ) ----&gt;| /dev/pts/5  |
 ---       +-------------+    /		   ---       +-------------+
			     /
 ---       +-------------+  /                       cmd3
( 1 ) ----&gt;| 1st pipe    | /
 ---       +-------------+                 ---       +-------------+
			     ------------&gt;( 0 ) ----&gt;| 2nd pipe    |
 ---       +-------------+ /               ---       +-------------+
( 2 ) ----&gt;| 2nd pipe    |/
 ---       +-------------+                 ---       +-------------+
			      	    	  ( 1 ) ----&gt;| /dev/pts/5  |
			       	    	   ---       +-------------+

				    	   ---       +-------------+
				       	  ( 2 ) ----&gt;| /dev/pts/5  |
				  	   ---       +-------------+

&lt;/code&gt;


As said previously, as an exercise, you can start with ''1'' open on a file
and ''2'' open on another file to see how the ''stdin'' from ''cmd2'' and ''cmd3'' goes
to the original ''stdin'' and how the ''stderr'' goes to the original ''stderr''.


====== Syntax ======

I used to have trouble choosing between ''0&amp;&lt;3'' ''3&amp;&gt;1'' ''3&gt;&amp;1'' ''&lt;nowiki&gt;-&gt;2&lt;/nowiki&gt;'' ''-&lt;&amp;0'' ''&amp;-&lt;0''
''0&lt;&amp;-'' etc... (I think probably because the syntax is more representative of the result, i.e., the redirection, than
what is done, i.e., opening, closing, or duplicating file descriptors).

If this fits your situation, then maybe the following &quot;rules&quot; will help
you, a redirection is always like the following:

&lt;code&gt;
 lhs op rhs
&lt;/code&gt;

  * ''lhs'' is always a file description, i.e., a number:
    * Either we want to open, duplicate, move or we want to close. If the op is ''&lt;'' then there is an implicit 0, if it's ''&gt;'' or ''&lt;nowiki&gt;&gt;&gt;&lt;/nowiki&gt;'', there is an implicit 1.


  * ''op'' is ''&lt;'', ''&gt;'', ''&lt;nowiki&gt;&gt;&gt;&lt;/nowiki&gt;'', ''&gt;|'', or ''&lt;&gt;'':
    * ''&lt;'' if the file decriptor in ''lhs'' will be read, ''&gt;'' if it will be written, ''&lt;nowiki&gt;&gt;&gt;&lt;/nowiki&gt;'' if data is to be appended to the file, ''&gt;|'' to overwrite an existing file  or ''&lt;&gt;'' if it will be both read and written.


  * ''rhs'' is the thing that the file descriptor will describe:
    * It can be the name of a file, the place where another descriptor goes (''&amp;1''), or, ''&amp;-'', which will close the file descriptor.
 

You might not like this description, and find it a bit incomplete or
inexact, but I think it really helps to easily find that, say ''&lt;nowiki&gt;&amp;-&gt;0&lt;/nowiki&gt;'' is
incorrect.

==== A note on style ====

The shell is pretty loose about what it considers a valid redirect. While opinions probably differ, this author has some (strong) recommendations:

  * **Always** keep redirections &quot;tightly grouped&quot; -- that is, **do not** include whitespace anywhere within the redirection syntax except within quotes if required on the RHS (e.g. a filename that contains a space). Since shells fundamentally use whitespace to delimit fields in general, it is visually much clearer for each redirection to be separated by whitespace, but grouped in chunks that contain no unnecessary whitespace.

  * **Do** always put a space between each redirection, and between the argument list and the first redirect.

  * **Always** place redirections together at the very end of a command after all arguments. Never precede a command with a redirect. Never put a redirect in the middle of the arguments.

  * **Never** use the Csh ''&amp;&gt;foo'' and ''&gt;&amp;foo'' shorthand redirects. Use the long form ''&gt;foo 2&gt;&amp;1''. (see: [[obsolete]])

&lt;code&gt;
# Good! This is clearly a simple commmand with two arguments and 4 redirections
cmd arg1 arg2 &lt;myFile 3&lt;&amp;1 2&gt;/dev/null &gt;&amp;2

# Good!
{ cmd1 &lt;&lt;&lt;'my input'; cmd2; } &gt;someFile

# Bad. Is the &quot;1&quot; a file descriptor or an argument to cmd? (answer: it's the FD). Is the space after the herestring part of the input data? (answer: No).
# The redirects are also not delimited in any obvious way.
cmd 2&gt;&amp; 1 &lt;&lt;&lt; stuff

# Hideously Bad. It's difficult to tell where the redirects are and whether they're even valid redirects.
# This is in fact one command with one argument, an assignment, and three redirects.
foo=bar&lt;baz bork&lt;&lt;&lt; blarg&gt;bleh
&lt;/code&gt;

====== Conclusion ======

I hope this tutorial worked for you.

I lied, I did not explain  ''1&gt;&amp;3-'', go check the manual ;-)

Thanks to Stéphane Chazelas from whom I stole both the intro and the
example....

The intro is inspired by this introduction, you'll find a nice
exercise there too:

  *  [[http://tldp.org/LDP/abs/html/ioredirintro.html| A Detailed Introduction to I/O and I/O Redirection]]

The last example comes from this post:

  *  [[http://groups.google.com/group/comp.unix.shell/browse_thread/thread/64206d154894a4ef/ffe4c2e382034ed9#ffe4c2e382034ed9 | comp.unix.shell: piping stdout and stderr to different processes]]


====== See also ======

