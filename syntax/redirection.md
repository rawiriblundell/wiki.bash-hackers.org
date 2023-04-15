====== Redirection ======

&lt;wrap left todo&gt;Fix me: To be continued&lt;/wrap&gt;
\\

Redirection makes it possible to control where the output of a command goes to, and where the input of a command comes from. It's a mighty tool that, together with pipelines, makes the shell powerful. The redirection operators are checked whenever a [[syntax:grammar:parser_exec | simple command is about to be executed]].

Under normal circumstances, there are 3 files open, accessible by the file descriptors 0, 1 and 2, all connected to your terminal:
^Name^FD^Description^
|''stdin''|0|standard input stream (e.g. keyboard)|
|''stdout''|1|standard output stream (e.g. monitor)|
|''stderr''|2|standard error output stream (usually also on monitor)|

&lt;wrap center info&gt;The terms &quot;monitor&quot; and &quot;keyboard&quot; refer to the same device, the **terminal** here. Check your preferred UNIX(r)-FAQ for details, I'm too lazy to explain what a terminal is ;-) &lt;/wrap&gt;

Both, ''stdout'' and ''stderr'' are output file descriptors. Their difference is the **convention** that a program outputs payload on ''stdout'' and diagnostic- and error-messages on ''stderr''. If you write a script that outputs error messages, please make sure you follow this convention!

Whenever you **name** such a filedescriptor, i.e. you want to redirect this descriptor, you just use the number:
&lt;code&gt;
# this executes the cat-command and redirects its error messages (stderr) to the bit bucket
cat some_file.txt 2&gt;/dev/null
&lt;/code&gt;
Whenever you **reference** a descriptor, to point to its current target file, then you use a &quot;''&amp;''&quot; followed by a the descriptor number:
&lt;code&gt;
# this executes the echo-command and redirects its normal output (stdout) to the standard error target
echo &quot;There was an error&quot; 1&gt;&amp;2
&lt;/code&gt;

The redirection operation can be **anywhere** in a simple command, so these examples are equivalent:
&lt;code&gt;
cat foo.txt bar.txt &gt;new.txt
cat &gt;new.txt foo.txt bar.txt
&gt;new.txt cat foo.txt bar.txt
&lt;/code&gt;
&lt;wrap center important&gt;Every redirection operator takes one or two words as operands. If you have to use operands (e.g. filenames to redirect to) that contain spaces you **must** quote them!&lt;/wrap&gt;

===== Valid redirection targets and sources =====
This syntax is recognized whenever a ''TARGET'' or a ''SOURCE'' specification (like below in the details descriptions) is used.

^Syntax^Description^
|''FILENAME''|references a normal, ordinary filename from the filesystem (which can of course be a FIFO, too. Simply everything you can reference in the filesystem)|
|''&amp;N''|references the current target/source of the filedescriptor ''N'' (&quot;duplicates&quot; the filedescriptor)|
|''&amp;-''|closes the redirected filedescriptor, useful instead of ''&gt; /dev/null'' constructs (''&gt; &amp;-'')|
|''/dev/fd/N''|duplicates the filedescriptor ''N'', if ''N'' is a valid integer|
|''/dev/stdin''|duplicates filedescriptor 0 (''stdin'')|
|''/dev/stdout''|duplicates filedescriptor 1 (''stdout'')|
|''/dev/stderr''|duplicates filedescriptor 2 (''stderr'')|
|''/dev/tcp/HOST/PORT''|assuming ''HOST'' is a valid hostname or IP address, and ''PORT'' is a valid port number or service name: redirect from/to the corresponding TCP socket|
|''/dev/udp/HOST/PORT''|assuming ''HOST'' is a valid hostname or IP address, and ''PORT'' is a valid port number or service name: redirect from/to the corresponding UDP socket|

If a target/source specification fails to open, the whole redirection operation fails. Avoid referencing file descriptors above 9, since you may collide with file descriptors Bash uses internally.




===== Redirecting output =====
&lt;code&gt;
N &gt; TARGET
&lt;/code&gt;

This redirects the file descriptor number ''N'' to the target ''TARGET''. If ''N'' is omitted, ''stdout'' is assumed (FD 1). The ''TARGET'' is **truncated** before writing starts.

If the option ''noclobber'' is set with [[commands:builtin:set | the set builtin]], with cause the redirection to fail, when ''TARGET'' names a regular file that already exists. You can manually override that behaviour by forcing overwrite with the redirection operator ''&gt;|'' instead of ''&gt;''.


===== Appending redirected output =====
&lt;code&gt;
N &gt;&gt; TARGET
&lt;/code&gt;

This redirects the file descriptor number ''N'' to the target ''TARGET''. If ''N'' is omitted, ''stdout'' is assumed (FD 1). The ''TARGET'' is **not truncated** before writing starts.

===== Redirecting output and error output =====
&lt;code&gt;
&amp;&gt; TARGET
&lt;/code&gt;

&lt;code&gt;
&gt;&amp; TARGET
&lt;/code&gt;

This special syntax redirects both, ''stdout'' and ''stderr'' to the specified target. It's **equivalent** to
&lt;code&gt;
&gt; TARGET 2&gt;&amp;1
&lt;/code&gt;

Since Bash4, there's ''&amp;&lt;nowiki&gt;&gt;&gt;TARGET&lt;/nowiki&gt;'', which is equivalent to ''&lt;nowiki&gt;&gt;&gt;&lt;/nowiki&gt; TARGET 2&gt;&amp;1''.

&lt;wrap center important&gt;This syntax is deprecated and should not be used. See the page about [[scripting:obsolete |  obsolete and deprecated syntax]].&lt;/wrap&gt;


===== Appending redirected output and error output =====

To append the cumulative redirection of ''stdout'' and ''stderr'' to a file you simply do
&lt;code&gt;
&gt;&gt; FILE 2&gt;&amp;1
&lt;/code&gt;

&lt;code&gt;
&amp;&gt;&gt; FILE
&lt;/code&gt;

===== Transporting stdout and stderr through a pipe =====
&lt;code&gt;
COMMAND1 2&gt;&amp;1 | COMMAND2
&lt;/code&gt;

&lt;code&gt;
COMMAND1 |&amp; COMMAND2
&lt;/code&gt;


===== Redirecting input =====
&lt;code&gt;
N &lt; SOURCE
&lt;/code&gt;

The input descriptor ''N'' uses ''SOURCE'' as its data source. If ''N'' is omitted, filedescriptor 0 (''stdin'') is assumed.


===== Here documents =====
&lt;BOOKMARK:tag_heredoc&gt;

&lt;code&gt;
&lt;&lt;TAG
...
TAG
&lt;/code&gt;

&lt;code&gt;
&lt;&lt;-TAG
...
TAG
&lt;/code&gt;

A here-document is an input redirection using source data specified directly at the command line (or in the script), no &quot;external&quot; source. The redirection-operator ''&lt;nowiki&gt;&lt;&lt;&lt;/nowiki&gt;'' is used together with a tag ''TAG'' that's used to mark the end of input later:

&lt;code&gt;
# display help

cat &lt;&lt;EOF
Sorry...
No help available yet for $PROGRAM.
Hehe...
EOF
&lt;/code&gt;

As you see, substitutions are possible. To be precise, the following substitutions and expansions are performed in the here-document data:
  * [[syntax:pe | Parameter expansion]]
  * [[syntax:expansion:cmdsubst | Command substitution]]
  * [[syntax:expansion:arith | Arithmetic expansion]]

You can avoid that by quoting the tag:
&lt;code&gt;
cat &lt;&lt;&quot;EOF&quot;
This won't be expanded: $PATH
EOF
&lt;/code&gt;

Last but not least, if the redirection operator ''&lt;nowiki&gt;&lt;&lt;&lt;/nowiki&gt;'' is followed by a ''-'' (dash), all **leading TAB** from the document data will be ignored. This might be useful to have optical nice code also when using here-documents.

The tag you use **must** be the only word in the line, to be recognized as end-of-here-document marker.

&lt;wrap center info&gt;It seems that here-documents (tested on versions ''1.14.7'', ''2.05b'' and ''3.1.17'') are correctly terminated when there is an EOF before the end-of-here-document tag. The reason is unknown, but it seems to be done on purpose. Bash 4 introduced a warning message when end-of-file is seen before the tag is reached.&lt;/wrap&gt;



===== Here strings =====

&lt;code&gt;
&lt;&lt;&lt; WORD
&lt;/code&gt;

The here-strings are a variation of the here-documents. The word ''WORD'' is taken for the input redirection:

&lt;code&gt;
cat &lt;&lt;&lt; &quot;Hello world... $NAME is here...&quot;
&lt;/code&gt;

Just beware to quote the ''WORD'' if it contains spaces. Otherwise the rest will be given as normal parameters.

The here-string will append a newline (''\n'') to the data.

===== Multiple redirections =====

More redirection operations can occur in a line of course. The order is **important**! They're evaluated from **left to right**.
If you want to redirect both, ''stderr'' and ''stdout'' to the same file (like ''/dev/null'', to hide it), this is **the wrong way**:
&lt;code bash&gt;
# { echo OUTPUT; echo ERRORS &gt;&amp;2; } is to simulate something that outputs to STDOUT and STDERR
# you can test with it
{ echo OUTPUT; echo ERRORS &gt;&amp;2; } 2&gt;&amp;1 1&gt;/dev/null
&lt;/code&gt;
Why? Relatively easy:
  * initially, ''stdout'' points to your terminal (you read it)
  * same applies to ''stderr'', it's connected to your terminal
  * ''2&gt;&amp;1'' redirects ''stderr'' away from the terminal to the target for ''stdout'': **the terminal** (again...)
  * ''1&gt;/dev/null'' redirects ''stdout'' away from your terminal to the file ''/dev/null''
What remains? ''stdout'' goes to ''/dev/null'', ''stderr'' still (or better: &quot;again&quot;) goes to the terminal. You have to swap the order to make it do what you want:
&lt;code bash&gt;
{ echo OUTPUT; echo ERRORS &gt;&amp;2; } 1&gt;/dev/null 2&gt;&amp;1
&lt;/code&gt;

===== Examples =====

How to make a program quiet (assuming all output goes to ''STDOUT'' and ''STDERR''?
&lt;code&gt;
command &gt;/dev/null 2&gt;&amp;1
&lt;/code&gt;

===== See also =====
  * Internal: [[howto:redirection_tutorial | Illustrated Redirection Tutorial]]
  * Internal: [[commands:builtin:set#tag_noclobber | The noclobber option]]
  * Internal: [[commands:builtin:exec | The exec builtin command]]
  * Internal: [[syntax:grammar:parser_exec | Simple commands parsing and execution]]
  * Internal: [[syntax:expansion:proc_subst | Process substitution syntax]]
  * Internal: [[scripting:obsolete | Obsolete and deprecated syntax]]
