====== Command substitution ======

{{keywords&gt;bash shell scripting expansion substitution text variable output execute stdout save result return value}}

&lt;code&gt;
$( &lt;COMMANDS&gt; )

` &lt;COMMANDS&gt; `
&lt;/code&gt;

The command substitution expands to the output of commands. These commands are executed in a subshell, and their ''stdout'' data is what the substitution syntax expands to.

All **trailing** newlines are removed (below is an example for a workaround).

In later steps, **if not quoted**, the results undergo [[syntax:expansion:wordsplit | word splitting]] and [[syntax:expansion:globs | pathname expansion]]. You have to remember that, because the word splitting will also remove embedded newlines and other ''IFS'' characters and break the results up into several words. Also you'll probably get unexpected pathname matches. **If you need the literal results, quote the command substitution!**

The second form ''`COMMAND`'' is more or less obsolete for Bash, since it has some trouble with nesting (&quot;inner&quot; backticks need to be escaped) and escaping characters. Use ''$(COMMAND)'', it's also POSIX!

When you [[syntax:ccmd:grouping_subshell | call an explicit subshell]] ''(COMMAND)'' inside the command substitution ''$()'', then take care, this way is **wrong**:
&lt;code&gt;
$((COMMAND))
&lt;/code&gt;
Why? because it collides with the syntax for [[syntax:expansion:arith | arithmetic expansion]]. You need to separate the command substitution from the inner ''(COMMAND)'':
&lt;code&gt;
$( (COMMAND) )
&lt;/code&gt;

===== Specialities =====

When the inner command is only an input redirection, and nothing else, for example
&lt;code&gt;
$( &lt;FILE )
# or
` &lt;FILE `
&lt;/code&gt;
then Bash attempts to read the given file and act just if the given command was ''cat FILE''.


===== A closer look at the two forms =====

In general you really should only use the form ''$()'', it's escaping-neutral, it's nestable, it's also POSIX. But take a look at the following code snips to decide yourself which form you need under specific circumstances:

**__Nesting__**

Backtick form ''`...`'' is not directly nestable. You will have to escape the &quot;inner&quot; backticks. Also, the deeper you go, the more escape characters you need. Ugly.

&lt;code&gt;
echo `echo `ls``      # INCORRECT
echo `echo \`ls\``    # CORRECT
echo $(echo $(ls))    # CORRECT
&lt;/code&gt;

**__Parsing__**

All is based on the fact that the backquote-form is simple character substitution, while every ''$()''-construct opens an own, subsequent parsing step. Everything inside ''$()'' is interpreted as if written normal on a commandline. No special escaping of **nothing** is needed:

&lt;code&gt;
echo &quot;$(echo &quot;$(ls)&quot;)&quot; # nested double-quotes - no problem
&lt;/code&gt;

**__Constructs you should avoid__**

It's not all shiny with ''$()'', at least for my current Bash (''3.1.17(1)-release''. :!: __**Update:** Fixed since ''3.2-beta'' together with a misinterpretion of '))' being recognized as arithmetic expansion [by redduck666]__). This command seems to incorrectly close the substitution step and echo prints &quot;ls&quot; and &quot;)&quot;:
&lt;code&gt;
echo $(
# some comment ending with a )
ls
)
&lt;/code&gt;

It seems that every closing &quot;)&quot; confuses this construct. Also a (very uncommon ;-)) construct like:
&lt;code&gt;
echo $(read VAR; case &quot;$var&quot; in foo) blah ;; esac) # spits out some error, when it sees the &quot;;;&quot;

# fixes it:
echo $(read VAR; case &quot;$var&quot; in (foo) blah ;; esac) # will work, but just let it be, please ;-)
&lt;/code&gt;

**__Conclusion:__**

In general, the ''$()'' should be the preferred method:
  * it's clean syntax
  * it's intuitive syntax
  * it's more readable
  * it's nestable
  * its inner parsing is separate

===== Examples =====

**To get the date:**
&lt;code&gt;
DATE=&quot;$(date)&quot;
&lt;/code&gt;

**To copy a file and get ''cp'' error output:**
&lt;code&gt;
COPY_OUTPUT=&quot;$(cp file.txt /some/where 2&gt;&amp;1)&quot;
&lt;/code&gt;
Attention: Here, you need to redirect ''cp'' ''STDERR'' to its ''STDOUT'' target, because command substitution only catches ''STDOUT''!

**Catch stdout and preserve trailing newlines:**
&lt;code&gt;
var=$(echo -n $'\n'); echo -n &quot;$var&quot;; # $var == &quot;&quot;
var=$(echo -n $'\n'; echo -n x); var=&quot;${var%x}&quot;; echo -n &quot;$var&quot; # $var == &quot;\n&quot;
&lt;/code&gt;

This adds &quot;x&quot; to the output, which prevents the trailing newlines of the previous commands' output from being deleted by $().

By removing this &quot;x&quot; later on, we are left with the previous commands' output with its trailing newlines.

===== See also =====
  * Internal: [[syntax:expansion:intro | Introduction to expansion and substitution]]
  * Internal: [[scripting:obsolete | Obsolete and deprecated syntax]]
