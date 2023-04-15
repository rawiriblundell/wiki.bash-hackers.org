====== Tilde expansion ======

{{keywords&gt;bash shell scripting expansion substitution tilde home homedir shortcut}}

&lt;code&gt;
~
~/...

~NAME
~NAME/...

~+
~+/...

~-
~-/...
&lt;/code&gt;

The tilde expansion is used to expand to several specific pathnames:
  * home directories
  * current working directory
  * previous working directory

Tilde expansion is only performed, when the tilde-construct is at the beginning of a word, or a separate word.

If there's nothing to expand, i.e., in case of a wrong username or any other error condition, the tilde construct is not replaced, it stays what it is.

Tilde expansion is also performed everytime a variable is assigned:
  * after the **first** ''='': ''TARGET=~moonman/share''
  * after **every** '':'' (colon) in the assigned value: ''TARGET=file:~moonman/share''

&lt;note info&gt;
As of now (Bash 4.3-alpha) the following constructs **also** works, though it's not a variable assignment:
&lt;code&gt;
echo foo=~
echo foo=:~
&lt;/code&gt;
I don't know yet, if this is a bug or intended.
&lt;/note&gt;

This way you can correctly use the tilde expansion in your [[syntax:shellvars#PATH|PATH]]:
&lt;code&gt;
PATH=~/mybins:~peter/mybins:$PATH
&lt;/code&gt;

**Spaces in the referenced pathes?** A construct like...
&lt;code&gt;
~/&quot;my directory&quot;
&lt;/code&gt;
...is perfectly valid and works!

===== Home directory =====
&lt;code&gt;
~
~&lt;NAME&gt;
&lt;/code&gt;

This form expands to the home-directory of the current user (''~'') or the home directory of the given user (''~&lt;NAME&gt;'').

If the given user doesn't exist (or if his home directory isn't determinable, for some reason), it doesn't expand to something else, it stays what it is. The requested home directory is found by asking the operating system for the associated home directory for ''&lt;NAME&gt;''.

To find the home directory of the current user (''~''), Bash has a precedence:
  * expand to the value of [[syntax:shellvars#HOME|HOME]] if it's defined
  * expand to the home directory of the user executing the shell (operating system)
That means, the variable ''HOME'' can override the &quot;real&quot; home directory, at least regarding tilde expansion.


===== Current working directory =====
&lt;code&gt;
~+
&lt;/code&gt;

This expands to the value of the [[syntax:shellvars#PWD|PWD]] variable, which holds the currect working directory:
&lt;code&gt;
echo &quot;CWD is $PWD&quot;
&lt;/code&gt;
is equivalent to (note it **must** be a separate word!):
&lt;code&gt;
echo &quot;CWD is&quot; ~+
&lt;/code&gt;


===== Previous working directory =====

&lt;code&gt;
~-
&lt;/code&gt;

This expands to the value of the [[syntax:shellvars#OLDPWD|OLDPWD]] variable, which holds the previous working directory (the one before the last ''cd''). If ''OLDPWD'' is unset (never changed the directory), it is not expanded.

&lt;code&gt;
$ pwd
/home/bash
$ cd /etc
$ echo ~-
/home/bash
&lt;/code&gt;


===== See also =====
  * Internal: [[syntax:expansion:intro | Introduction to expansion and substitution]]
