====== Editing files via scripts with ed ======

{{keywords&gt;bash shell scripting arguments file editor edit ed sed}}

===== Why ed? =====

Like ''sed'', ''ed'' is a line editor. However, if you try to change file contents with ''sed'', and the file is open elsewhere and read by some process, you will find out that GNU ''sed'' and its ''-i'' option will not allow you to edit the file. There are circumstances where you may need that, e.g. editing active and open files, the lack of GNU, or other ''sed'', with &quot;in-place&quot; option available.

Why ''ed''?
  * maybe your ''sed'' doesn't support in-place edit
  * maybe you need to be as portable as possible
  * maybe you need to really edit in-file (and not create a new file like GNU ''sed'')
  * last but not least: standard ''ed'' has very good editing and addressing possibilities, compared to standard ''sed''

Don't get me wrong, this is **not** meant as anti-''sed'' article! It's just meant to show you another way to do the job.




===== Commanding ed =====

Since ''ed'' is an interactive text editor, it reads and executes commands that come from ''stdin''. There are several ways to feed our commands to ed:

**__Pipelines__**
&lt;code&gt;
echo '&lt;ED-COMMANDS&gt;' | ed &lt;FILE&gt;
&lt;/code&gt;

To inject the needed newlines, etc. it may be easier to use the builtin command, ''printf'' (&quot;help printf&quot;). Shown here as an example Bash function to prefix text to file content:
&lt;code&gt;

# insertHead &quot;$text&quot; &quot;$file&quot;

insertHead() {
  printf '%s\n' H 1i &quot;$1&quot; . w | ed -s &quot;$2&quot;
}
&lt;/code&gt;

**__Here-strings__**
&lt;code&gt;
ed &lt;FILE&gt; &lt;&lt;&lt; '&lt;ED-COMMANDS&gt;'
&lt;/code&gt;

**__Here-documents__**
&lt;code&gt;
ed &lt;FILE&gt; &lt;&lt;EOF
&lt;ED-COMMANDS&gt;
EOF
&lt;/code&gt;

Which one you prefer is your choice. I will use the here-strings, since it looks best here IMHO.

There are other ways to provide input to ''ed''. For example, process substitution. But these should be enough for daily needs.

Since ''ed'' wants commands separated by newlines, I'll use a special Bash quoting method, the C-like strings ''&lt;nowiki&gt;$'TEXT'&lt;/nowiki&gt;'', as it can interpret a set of various escape sequences and special characters. I'll use the ''-s'' option to make it less verbose.



===== The basic interface =====

Check the ''ed'' manpage for details

Similar to ''vi'' or ''vim'', ''ed'' has a &quot;command mode&quot; and an &quot;interactive mode&quot;. For non-interactive use, the command mode is the usual choice.

Commands to ''ed'' have a simple and regular structure: zero, one, or two addresses followed by a single-character command, possibly followed by parameters to that command. These addresses specify one or more lines in the text buffer. Every command that requires addresses has default addresses, so the addresses can often be omitted.

The line addressing is relative to the //current line//. If the edit buffer is not empty, the initial value for the //current line// shall be the last line in the edit buffer, otherwise zero. Generally, the //current line// is the last line affected by a command. All addresses can only address single lines, not blocks of lines!

Line addresses or commands using //regular expressions// interpret POSIX Basic Regular Expressions (BRE). A null BRE is used to reference the most recently used BRE. Since ''ed'' addressing is only for single lines, no RE can ever match a newline.



===== Debugging your ed scripts =====

By default, ''ed'' is not very talkative and will simply print a &quot;?&quot; when an error occurs. Interactively you can use the ''h'' command to get a short message explaining the last error. You can also turn on a mode that makes ''ed'' automatically print this message with the ''H'' command. It is a good idea to always add this command at the beginning of your ed scripts:

&lt;code&gt;
bash &gt; ed -s file &lt;&lt;&lt; $'H\n,df'
?
script, line 2: Invalid command suffix
&lt;/code&gt;

While working on your script, you might make errors and destroy your file, you might be tempted to try your script doing something like:
&lt;code&gt;
# Works, but there is better

# copy my original file
cp file file.test

# try my script on the file
ed -s file.test &lt;&lt;&lt; $'H\n&lt;ed commands&gt;\nw'

# see the results
cat file.test
&lt;/code&gt;
There is a much better way though, you can use the ed command ''p'' to print the file, now your testing would look like:

&lt;code&gt;
ed -s file &lt;&lt;&lt; $'H\n&lt;ed commands&gt;\n,p'
&lt;/code&gt;
the '','' (comma) in front of the ''p'' command is a shortcut for ''1,$'' which defines an address range for the first to the last line, '',p'' thus means print the whole file, after it has been modified. When your script runs sucessfully, you only have to replace the '',p'' by a ''w''.

Of course, even if the file is not modified by the ''p'' command, **it's always a good idea to have a backup copy!**
 

===== Editing your files =====

Most of these things can be done with ''sed''. But there are also things that can't be done in ''sed'' or can only be done with very complex code.






==== Simple word substitutions ====

Like ''sed'', ''ed'' also knows the common ''s/FROM/TO/'' command, and it can also take line-addresses. **If no substitution is made on the addressed lines, it's considered an error.**

=== Substitutions through the whole file ===

&lt;code&gt;
ed -s test.txt &lt;&lt;&lt; $',s/Windows(R)-compatible/POSIX-conform/g\nw'
&lt;/code&gt;

__Note:__ The comma as single address operator is an alias for ''1,$'' (&quot;all lines&quot;).

=== Substitutions in specific lines ===

On a line containing ''fruits'', do the substitution:
&lt;code&gt;
ed -s test.txt &lt;&lt;&lt; $'/fruits/s/apple/banana/g\nw'
&lt;/code&gt;

On the 5th line after the line containing ''fruits'', do the substitution:
&lt;code&gt;
ed -s test.txt &lt;&lt;&lt; $'/fruits/+5s/apple/banana/g\nw'
&lt;/code&gt;

==== Block operations ====

=== Delete a block of text ===

The simple one is a well-known (by position) block of text:
&lt;code&gt;
# delete lines number 2 to 4 (2, 3, 4)
ed -s test.txt &lt;&lt;&lt; $'2,5d\nw'
&lt;/code&gt;

This deletes all lines matching a specific regular expression:
&lt;code&gt;
# delete all lines matching foobar
ed -s test.txt &lt;&lt;&lt; $'g/foobar/d\nw'
&lt;/code&gt;
g/regexp/ applies the command following it to all the lines matching the regexp


=== Move a block of text ===
...using the ''m'' command: ''&lt;ADDRESS&gt; m &lt;TARGET-ADDRESS&gt;''

This is definitely something that can't be done easily with sed.

&lt;code&gt;
# moving lines 5-9 to the end of the file
ed -s test.txt &lt;&lt;&lt; $'5,9m$\nw'

# moving lines 5-9 to line 3
ed -s test.txt &lt;&lt;&lt; $'5,9m3\nw'
&lt;/code&gt;

=== Copy a block of text ===
...using the ''t'' command: ''&lt;ADDRESS&gt; t &lt;TARGET-ADDRESS&gt;''

You use the ''t'' command just like you use the ''m'' (move) command.

&lt;code&gt;
# make a copy of lines 5-9 and place it at the end of the file
ed -s test.txt &lt;&lt;&lt; $'5,9t$\nw'

# make a copy of lines 5-9 and place it at line 3
ed -s test.txt &lt;&lt;&lt; $'5,9t3\nw'
&lt;/code&gt;

=== Join all lines ===
...but leave the final newline intact. This is done by an extra command: ''j'' (join).

&lt;code&gt;
ed -s file &lt;&lt;&lt; $'1,$j\nw'
&lt;/code&gt;

Compared with two other methods (using ''tr'' or ''sed''), you don't have to delete all newlines and manually add one at the end.


==== File operations ====

=== Insert another file ===

How do you insert another file? As with ''sed'', you use the ''r'' (read) command. That inserts another file at the line before the last line (and prints the result to stdout - '',p''):
&lt;code&gt;
ed -s FILE1 &lt;&lt;&lt; $'$-1 r FILE2\n,p'
&lt;/code&gt;

To compare, here's a possible ''sed'' solution which must use Bash arithmetic and the external program ''wc'':
&lt;code&gt;
sed &quot;$(($(wc -l &lt; FILE1)-1))r FILE2&quot; FILE1

# UPDATE here's one which uses GNU sed's &quot;e&quot; parameter for the s-command
#   it executes the commands found in pattern space. I'll take that as a
#   security risk, but well, sometimes GNU &gt; security, you know...
sed '${h;s/.*/cat FILE2/e;G}' FILE1
&lt;/code&gt;

Another approach, in two invocations of sed, that avoids the use of external commands completely:

&lt;code&gt;
sed $'${s/$/\\n-||-/;r FILE2\n}' FILE1 | sed '0,/-||-/{//!h;N;//D};$G'
&lt;/code&gt;

===== Pitfalls =====



==== ed is not sed ===

ed and sed might look similar, but the same command(s) might act differently:

**__ /foo/d __**

In sed /foo/d will delete all lines matching foo, in ed the commands are not repeated 
on each line so this command will search the next line matching foo and delete it.
If you want to delete all lines matching foo, or do a subsitution on all lines matching foo
you have to tell ed about it with the g (global) command:

&lt;code&gt;
echo $'1\n1\n3' &gt; file

#replace all lines matching 1 by &quot;replacement&quot;
ed -s file &lt;&lt;&lt; $'g/1/s/1/replacement/\n,p' 

#replace the first line matching 1 by &quot;replacement&quot;
#(because it starts searching from the last line)
ed -s file &lt;&lt;&lt; $'s/1/replacement/\n,p'
&lt;/code&gt;

**__ an error stops the script __**

You might think that it's not a problem and that the same thing happens with sed and you're right, with the exception that if ed
does not find a pattern it's an error, while sed just continues with the next line.
For instance, let's say that you want to change foo to bar on the first line of the file and add something after the next line,
ed will stop if it cannot find foo on the first line, sed will continue.

&lt;code&gt;
#Gnu sed version
sed -e '1s/foo/bar/' -e '$a\something' file

#First ed version, does nothing if foo is not found on the first line:
ed -s file &lt;&lt;&lt; $'H\n1s/foo/bar/\na\nsomething\n.\nw'
&lt;/code&gt;

If you want the same behaviour you can use g/foo/ to trick ed. g/foo/ will apply the command on all lines matching foo,
thus the substitution will succeed and ed will not produce an error when foo is not found:

&lt;code&gt;
#Second version will add the line with &quot;something&quot; even if foo is not found
ed -s file &lt;&lt;&lt; $'H\n1g/foo/s/foo/bar/\na\nsomething\n.\nw'
&lt;/code&gt;

In fact, even a substitution that fails after a g/ / command does not seem to cause an error, i.e. you can 
use a trick like g/./s/foo/bar/ to attempt the substitution on all non blank lines  

==== here documents ====

**__ shell parameters are expanded __**
 
If you don't quote the delimiter, $ has a special meaning. This sounds obvious but it's
easy to forget this fact when you use addresses like $-1 or commands like $a. Either quote the $ or the delimiter:
&lt;code&gt;
#fails
ed -s file &lt;&lt; EOF
$a
last line
.
w
EOF 

#ok
ed -s file &lt;&lt; EOF
\$a
last line
.
w
EOF 

#ok again
ed -s file &lt;&lt; 'EOF'
$a
last line
.
w
EOF 
&lt;/code&gt;

**__ &quot;.&quot; is not a command __**

The . used to terminate the command &quot;a&quot; must be the only thing on the line.
take care if you indent the commands:

&lt;code&gt;
#ed doesn't care about the spaces before the commands, but the . must be the only thing on the line:
ed -s file &lt;&lt; EOF
    a
my content
.
    w
EOF
&lt;/code&gt;

===== Simulate other commands =====

Keep in mind that in all the examples below, the entire file will be read into memory.

==== A simple grep ====

&lt;code&gt;
ed -s file &lt;&lt;&lt; 'g/foo/p'

# equivalent
ed -s file &lt;&lt;&lt; 'g/foo/'
&lt;/code&gt;

The name ''grep'' is derived from the notaion ''g/RE/p'' (global =&gt; regular expression =&gt; print).
ref http://www.catb.org/~esr/jargon/html/G/grep.html



==== wc -l ====

Since the default for the ''ed'' &quot;print line number&quot; command is the last line, a simple ''='' (equal sign) will print this line number and thus the number of lines of the file:

&lt;code&gt;
ed -s file &lt;&lt;&lt; '='
&lt;/code&gt;


==== cat ====
Yea, it's a joke...

&lt;code&gt;
ed -s file &lt;&lt;&lt; $',p'
&lt;/code&gt;

...but a similar thing to ''cat'' showing line-endings and escapes can be done with the ''list'' command (l):

&lt;code&gt;
ed -s file &lt;&lt;&lt; $',l'
&lt;/code&gt;


FIXME to be continued

===== Links =====

Reference:
  * [[http://www.gnu.org/software/ed/manual/ed_manual.html | Gnu ed]] - if we had to guess, you're probably using this one.
  * POSIX [[http://pubs.opengroup.org/onlinepubs/9699919799/utilities/ed.html#tag_20_38 | ed]], [[http://pubs.opengroup.org/onlinepubs/9699919799/utilities/ex.html#tag_20_40 | ex ]], and [[http://pubs.opengroup.org/onlinepubs/9699919799/utilities/vi.html#tag_20_152 | vi ]]
  * [[ http://sdf.lonestar.org/index.cgi?tutorials/ed ]] - ed cheatsheet on sdf.org

Misc info / tutorials:
  * [[ http://mywiki.wooledge.org/BashFAQ/021 | How can I replace a string with another string in a variable, a stream, a file, or in all the files in a directory? ]] - BashFAQ
