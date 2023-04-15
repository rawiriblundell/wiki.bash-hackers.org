====== Expansions and substitutions ======

{{keywords&gt;bash shell scripting expansion substitution text variable filename macro wildcard}}

Before executing your commands, Bash checks whether there are any syntax elements in the command line that should be interpreted rather than taken literally.  After splitting the command line into tokens (words), Bash scans for these special elements and interprets them,  resulting in a changed command line: the elements are said to be **expanded** to or **substituted** to **new text and maybe new tokens** (words).

The most simple example of this behaviour is a referenced variable:
&lt;code&gt;
mystring=&quot;Hello world&quot;
echo &quot;$mystring&quot;
&lt;/code&gt;
The ''echo'' program definitely doesn't care about what a shell variable is. It is Bash's job to deal with the variable. Bash **expands** the string &quot;''$mystring''&quot; to &quot;''Hello world''&quot;, so that ''echo'' will only see ''Hello world'', not the variable or anything else!

After all these expansions and substitutions are done, all quotes that are not meant literally (i.e., [[syntax:quoting | the quotes that marked contiguous words]], as part of the shell syntax) are removed from the commandline text, so the called program won't see them. This step is called **quote-removal**.

===== Overview =====

Saw a possible expansion syntax but don't know what it is? Here's a small list.

  * [[syntax:pe | Parameter expansion]] (it has its own [[syntax:pe#overview | overview section]])
    * ''$WORD''
    * ''${STUFF...}''
  * [[syntax:expansion:globs | Pathname expansion]]
    * ''*.txt''
    * ''page_1?.html''
  * [[syntax:expansion:arith | Arithmetic expansion]]
    * ''&lt;nowiki&gt;$(( EXPRESSION ))&lt;/nowiki&gt;''
    * ''$[ EXPRESSION ]''
  * [[syntax:expansion:cmdsubst | Command substitution]]
    * ''$( COMMAND )''
    * ''` COMMAND `''
  * [[syntax:expansion:tilde | Tilde expansion]]
    * ''~''
    * ''~+''
    * ''~-''
  * [[syntax:expansion:brace | Brace expansion]]
    * ''{X,Y,Z}''
    * ''{X..Y}''
    * ''{X..Y..Z}''
  * [[syntax:expansion:proc_subst | Process substitution]]
    * ''&lt;( COMMAND )''
    * ''&gt;( COMMAND )''

===== Order =====
Bash performs expansions and substitutions in a defined order. This explains why globbing (pathname expansion), for example, is safe to use on filenames with spaces (because it happens **after** the final word splitting!).

The order is (from first to last):

  * [[syntax:expansion:brace | Brace expansion]]
  * [[syntax:expansion:tilde | Tilde expansion]]
  * The following expansions happen at the same time, in a left-to-right fashion on the commandline (see below)
    * [[syntax:pe | Parameter expansion]]
    * [[syntax:expansion:arith | Arithmetic expansion]]
    * [[syntax:expansion:cmdsubst | Command substitution]]
  * [[syntax:expansion:wordsplit | Word splitting]]
  * [[syntax:expansion:globs | Pathname expansion]]

[[syntax:expansion:proc_subst | Process substitution]] is performed **simultaneously** with [[syntax:pe | parameter expansion]], [[syntax:expansion:cmdsubst | command substitution]] and [[syntax:expansion:arith | arithmetic expansion]]. It is only performed when the underlying operating system supports it.

The 3 steps [[syntax:pe | parameter expansion]], [[syntax:expansion:arith | arithmetic expansion]] and [[syntax:expansion:cmdsubst | command substitution]] happen at the same time in a left-to-right fashion on nthe commandline. This means
&lt;code&gt;
i=1
echo $i $((i++)) $i
&lt;/code&gt;
will output ''1 1 2'' and not ''1 1 1''.
