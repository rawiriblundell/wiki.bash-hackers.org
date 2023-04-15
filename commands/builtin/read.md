====== The read builtin command ======
//read something about read here!//

===== Synopsis =====
&lt;code&gt;
read [-ers] [-u &lt;FD&gt;] [-t &lt;TIMEOUT&gt;] [-p &lt;PROMPT&gt;] [-a &lt;ARRAY&gt;] [-n &lt;NCHARS&gt;] [-N &lt;NCHARS&gt;] [-d &lt;DELIM&gt;] [-i &lt;TEXT&gt;] [&lt;NAME...&gt;]
&lt;/code&gt;

===== Description =====
The ''read'' builtin reads **one line** of data (text, user input, ...) from standard input or a supplied filedescriptor number into one or more variables named by ''&lt;NAME...&gt;''.

Since Bash 4.3-alpha, ''read'' skips any ''NUL'' (ASCII code 0) characters in input.

If ''&lt;NAME...&gt;'' is given, the line is word-split using [[syntax:shellvars#IFS|IFS]] variable, and every word is assigned to one ''&lt;NAME&gt;''. The remaining words are all assigned to the last ''&lt;NAME&gt;'' if more words than variable names are present.

&lt;WRAP center round info 90%&gt;
If no ''&lt;NAME&gt;'' is given, the whole line read (without performing word-splitting!) is assigned to the shell variable [[syntax:shellvars#REPLY|REPLY]]. Then, ''REPLY'' really contains the line as it was read, without stripping pre- and postfix spaces and other things!
&lt;code&gt;
while read -r; do
  printf '&quot;%s&quot;\n' &quot;$REPLY&quot;
done &lt;&lt;&lt;&quot;  a line with prefix and postfix space  &quot;
&lt;/code&gt;
&lt;/WRAP&gt;

If a timeout is given, or if the shell variable [[syntax:shellvars#TMOUT|TMOUT]] is set, it is counted from initially waiting for input until the completion of input (i.e. until the complete line is read). That means the timeout can occur during input, too.



==== Options ====

^Option^Description^
|''-a &lt;ARRAY&gt;''|read the data word-wise into the specified array ''&lt;ARRAY&gt;'' instead of normal variables|
|''-d &lt;DELIM&gt;''|recognize ''&lt;DELIM&gt;'' as data-end, rather than ''&lt;newline&gt;''|
|''-e''|on interactive shells: use Bash's readline interface to read the data. Since version 5.1-alpha, this can also be used on specified file descriptors using ''-u''|
|''-i &lt;STRING&gt;''|preloads the input buffer with text from ''&lt;STRING&gt;'', only works when Readline (''-e'') is used|
|''-n &lt;NCHARS&gt;''|reads ''&lt;NCHARS&gt;'' characters of input, then quits|
|''-N &lt;NCHARS&gt;''|reads ''&lt;NCHARS&gt;'' characters of input, //ignoring any delimiter//, then quits|
|''-p &lt;PROMPT&gt;''|the prompt string ''&lt;PROMPT&gt;'' is output (without a trailing automatic newline) before the read is performed|
|''-r''|raw input - **disables** interpretion of **backslash escapes** and **line-continuation** in the read data|
|''-s''|secure input - don't echo input if on a terminal (passwords!)|
|''-t &lt;TIMEOUT&gt;''|wait for data ''&lt;TIMEOUT&gt;'' seconds, then quit (exit code 1). Fractional seconds (&quot;5.33&quot;) are allowed since Bash 4. A value of 0 immediately returns and indicates if data is waiting in the exit code. Timeout is indicated by an exit code greater than 128. If timeout arrives before data is read completely (before end-of-line), the partial data is saved.|
|''-u &lt;FD&gt;''|use the filedescriptor number ''&lt;FD&gt;'' rather than ''stdin'' (0)|

When both, ''-a &lt;ARRAY&gt;'' and a variable name ''&lt;NAME&gt;'' is given, then the array is set, but not the variable.

Of course it's valid to set individual array elements without using ''-a'':
&lt;code&gt;
read MYARRAY[5]
&lt;/code&gt;

&lt;WRAP center round important 90%&gt;

Reading into array elements using the syntax above **may cause [[syntax:expansion:globs | pathname expansion]] to occur**.

Example: You are in a directory with a file named ''x1'', and you want to read into an array ''x'', index ''1'' with
&lt;code&gt;
read x[1]
&lt;/code&gt;
then pathname expansion will expand to the filename ''x1'' and break your processing!

Even worse, if ''nullglob'' is set, your array/index will disappear.

To avoid this, either **disable pathname expansion** or **quote** the array name and index:
&lt;code&gt;
read 'x[1]'
&lt;/code&gt;

&lt;/WRAP&gt;

==== Return status ====

^Status  ^Reason  ^
|0     |no error  |
|0     |error when assigning to a read-only variable ((fixed in 4.2-rc1))  |
|2     |invalid option  |
|&gt;128  |timeout (see ''-t'')  |
|!=0   |invalid filedescriptor supplied to ''-u''  |
|!=0   |end-of-file reached  |

==== read without -r ====

Essentially all you need to know about ''-r'' is to **ALWAYS** use it. The exact behavior you get without ''-r'' is completely useless even for weird purposes. It basically allows the escaping of input which matches something in IFS, and also escapes line continuations. It's explained pretty well in the [[http://pubs.opengroup.org/onlinepubs/9699919799/utilities/read.html#tag_20_109 | POSIX read]] spec.

&lt;code&gt;
2012-05-23 13:48:31	geirha	it should only remove the backslashes, not change \n and \t and such into newlines and tabs
2012-05-23 13:49:00	ormaaj	so that's what read without -r does?
2012-05-23 13:49:16	geirha	no, -r doesn't remove the backslashes
2012-05-23 13:49:34	ormaaj	I thought read &lt;&lt;&lt;'str' was equivalent to read -r &lt;&lt;&lt;$'str'
2012-05-23 13:49:38	geirha	# read x y &lt;&lt;&lt; 'foo\ bar baz'; echo &quot;&lt;$x&gt;&lt;$y&gt;&quot;
2012-05-23 13:49:40	shbot	geirha: &lt;foo bar&gt;&lt;baz&gt;
2012-05-23 13:50:32	geirha	no, read without -r is mostly pointless. Damn bourne
2012-05-23 13:51:08	ormaaj	So it's mostly (entirely) used to escape spaces
2012-05-23 13:51:24	ormaaj	and insert newlines
2012-05-23 13:51:47	geirha	ormaaj: you mostly get the same effect as using \ at the prompt
2012-05-23 13:52:04	geirha	echo \&quot;  outputs a &quot; ,  read x &lt;&lt;&lt; '\&quot;'  reads a &quot;
2012-05-23 13:52:32	ormaaj	oh weird
2012-05-23 13:52:46	 *	ormaaj struggles to think of a point to that...
2012-05-23 13:53:01	geirha	ormaaj: ask Bourne :P
2012-05-23 13:53:20	geirha	(not Jason)
2012-05-23 13:53:56	ormaaj	hm thanks anyway :)
&lt;/code&gt;

===== Examples =====
==== Rudimentary cat replacement ====

A rudimentary replacement for the ''cat'' command: read lines of input from a file and print them on the terminal.
&lt;code&gt;
opossum() {
  while read -r; do
    printf &quot;%s\n&quot; &quot;$REPLY&quot;
  done &lt;&quot;$1&quot;
}
&lt;/code&gt;

__**Note:**__ Here, ''read -r'' and the default ''REPLY'' is used, because we want to have the real literal line, without any mangeling. ''printf'' is used, because (depending on settings), ''echo'' may interpret some baskslash-escapes or switches (like ''-n'').



==== Press any key... ====

Remember the MSDOS ''pause'' command? Here's something similar:
&lt;code&gt;
pause() {
  local dummy
  read -s -r -p &quot;Press any key to continue...&quot; -n 1 dummy
}
&lt;/code&gt;

Notes:
  * ''-s'' to suppress terminal echo (printing)
  * ''-r'' to not interpret special characters (like waiting for a second character if somebody presses the backslash)

==== Reading Columns ====

=== Simple Split ===

Read can be used to split a string:
&lt;code&gt;
var=&quot;one two three&quot;
read -r col1 col2 col3 &lt;&lt;&lt; &quot;$var&quot;
printf &quot;col1: %s col2: %s col3 %s\n&quot; &quot;$col1&quot; &quot;$col2&quot; &quot;$col3&quot;
&lt;/code&gt; 

Take care that you cannot use a pipe: 
&lt;code&gt;
echo &quot;$var&quot; | read col1 col2 col3 # does not work!
printf &quot;col1: %s col2: %s col3 %s\n&quot; &quot;$col1&quot; &quot;$col2&quot; &quot;$col3&quot;
&lt;/code&gt;
Why? because the commands of the pipe run in subshells that cannot modify the parent shell. As a result, the variables
''col1'', ''col2'' and ''col3'' of the parent shell are not modified (see article: [[scripting:processtree]]).

If the variable has more fields than there are variables, the last variable get the remaining of the line:
&lt;code&gt;
read col1 col2 col3 &lt;&lt;&lt; &quot;one two three four&quot;
printf &quot;%s\n&quot; &quot;$col3&quot; #prints three four
&lt;/code&gt;


=== Changing The Separator ===

By default reads separates the line in fields using spaces or tabs. You can modify this using the //special variable// [[syntax:shellvars#IFS | IFS]],
the Internal Field Separator.
&lt;code&gt;
IFS=&quot;:&quot; read -r col1 col2 &lt;&lt;&lt; &quot;hello:world&quot;
printf &quot;col1: %s col2: %s\n&quot; &quot;$col1&quot; &quot;$col2&quot;
&lt;/code&gt;
Here we use the ''var=value command'' syntax to set the environment of ''read'' temporarily. We could have set ''IFS'' normally,
but then we would have to take care to save its value and restore it afterward (''OLD=$IFS IFS=&quot;:&quot;; read ....;IFS=$OLD'').

The default ''IFS'' is special in that 2 fields can be separated by one or more space or tab. When you set ''IFS'' to something besides whitespace (space or tab), the
fields are separated by **exactly** one character:
&lt;code&gt;
IFS=&quot;:&quot; read -r col1 col2 col3 &lt;&lt;&lt; &quot;hello::world&quot;
printf &quot;col1: %s col2: %s col3 %s\n&quot; &quot;$col1&quot; &quot;$col2&quot; &quot;$col3&quot;
&lt;/code&gt; 
See how the ''&lt;nowiki&gt;::&lt;/nowiki&gt;'' in the middle infact defines an additional //empty field//.

The fields are separated by exactly one character, but the character can be different between each field:
&lt;code&gt;
IFS=&quot;:|@&quot; read -r col1 col2 col3 col4 &lt;&lt;&lt; &quot;hello:world|in@bash&quot;
printf &quot;col1: %s col2: %s col3 %s col4 %s\n&quot; &quot;$col1&quot; &quot;$col2&quot; &quot;$col3&quot; &quot;$col4&quot;
&lt;/code&gt; 


==== Are you sure? ====
&lt;code&gt;
asksure() {
echo -n &quot;Are you sure (Y/N)? &quot;
while read -r -n 1 -s answer; do
  if [[ $answer = [YyNn] ]]; then
    [[ $answer = [Yy] ]] &amp;&amp; retval=0
    [[ $answer = [Nn] ]] &amp;&amp; retval=1
    break
  fi
done

echo # just a final linefeed, optics...

return $retval
}

### using it
if asksure; then
  echo &quot;Okay, performing rm -rf / then, master....&quot;
else
  echo &quot;Pfff...&quot;
fi
&lt;/code&gt;

==== Ask for a path with a default value ====

__**Note:**__ The ''-i'' option was introduced with Bash 4

&lt;code&gt;
read -e -p &quot;Enter the path to the file: &quot; -i &quot;/usr/local/etc/&quot; FILEPATH
&lt;/code&gt;

The user will be prompted, he can just accept the default, or edit it.

==== Multichar-IFS: Parsing a simple date/time string ====

Here, ''IFS'' contains both, a colon and a space. The fields of the date/time string are recognized correctly.

&lt;code&gt;
datetime=&quot;2008:07:04 00:34:45&quot;
IFS=&quot;: &quot; read -r year month day hour minute second &lt;&lt;&lt; &quot;$datetime&quot;
&lt;/code&gt;

===== Portability considerations =====
  * POSIX(r) only specified the ''-r'' option (raw read); ''-r'' is not only POSIX, you can find it in earlier Bourne source code
  * POSIX(r) doesn't support arrays
  * ''REPLY'' is not POSIX(r), you need to set ''IFS'' to the empty string to get the whole line for shells that don't know ''REPLY''. &lt;code&gt;
while IFS= read -r line; do
  ...
done &lt; text.txt
&lt;/code&gt;




===== See also =====
  * Internal: [[commands:builtin:printf | The printf builtin command]]
