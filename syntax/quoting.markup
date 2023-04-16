====== Quotes and escaping ======

{{keywords>bash shell scripting quoting quotes escape backslash marks singlequotes doublequotes single double}}

Quoting and escaping are important, as they influence the way Bash acts upon your input. There are three recognized types:
  * **per-character escaping** using a backslash: ''\$stuff''
  * **weak quoting** with double-quotes: ''&quot;stuff&quot;''
  * **strong quoting** with single-quotes: ''<nowiki>'stuff'</nowiki>''

All three forms have the very same purpose: **They give you general control over parsing, expansion and expansion results.**

Besides these basic variants, there are some special quoting methods (like interpreting ANSI-C escapes in a string) you'll meet below.

:!: **ATTENTION** :!: The quote characters (''&quot;'', double quote and ''<nowiki>'</nowiki>'', single quote) are a syntax element that influence parsing. It is not related to the quote characters passed as text to the command line! The syntax quotes are removed before the command is called! Example:
<code>
### NO NO NO: this passes three strings:
###      (1)  &quot;my
###      (2)  multiword
###      (3)  argument&quot;
MYARG=&quot;\&quot;my multiword argument\&quot;&quot;
somecommand $MYARG

### THIS IS NOT (!) THE SAME AS ###
command &quot;my multiword argument&quot;

### YOU NEED ###
MYARG=&quot;my multiword argument&quot;
command &quot;$MYARG&quot;
</code>

===== Per-character escaping =====

Per-character escaping is useful in on expansions and substitutions. In general, a character that has a special meaning to Bash, like the dollar-sign (''$'') can be masked to not have a special meaning using the backslash:

<code>
echo \$HOME is set to \&quot;$HOME\&quot;
</code>
  * ''\$HOME'' won't expand because it's not in variable-expansion syntax anymore
  * The backslash changes the quotes into literals - otherwise Bash would interpret them

The sequence ''\<newline>'' (an unquoted backslash, followed by a ''<newline>'' character) is interpreted as **line continuation**. It is removed from the input stream and thus effectively ignored. Use it to beautify your code:
<code>
# escapestr_sed()
# read a stream from stdin and escape characters in text that could be interpreted as
# special characters by sed
escape_sed() {
 sed \
  -e 's/\//\\\//g' \
  -e 's/\&/\\\&/g'
}
</code>

The backslash can be used to mask every character that has a special meaning to bash. __Exception:__ Inside a single-quoted string (see below).





===== Weak quoting =====

Inside a weak-quoted string there's **no special interpretion of**:
  * spaces as word-separators (on inital command line splitting and on [[syntax:expansion:wordsplit | word splitting]]!)
  * single-quotes to introduce strong-quoting (see below)
  * characters for pattern matching
  * tilde expansion
  * pathname expansion
  * process substitution

Everything else, especially [[syntax:pe | parameter expansion]], is performed!

<code>
ls -l &quot;*&quot;
</code>
Will not be expanded. ''ls'' gets the literal ''*'' as argument. It will, unless you have a file named ''*'', spit out an error.

<code>
echo &quot;Your PATH is: $PATH&quot;
</code>
Will work as expected. ''$PATH'' is expanded, because it's double (weak) quoted.

If a backslash in double quotes (&quot;weak quoting&quot;) occurs, there are 2 ways to deal with it
  * if the baskslash is followed by a character that would have a special meaning even inside double-quotes, the backslash is removed and the following character looses its special meaning
  * if the backslash is followed by a character without special meaning, the backslash is not removed

In particuar this means that ''&quot;\$&quot;'' will become ''$'', but ''&quot;\x&quot;'' will become ''\x''.


===== Strong quoting =====

Strong quoting is very easy to explain:

Inside a single-quoted string **nothing** is interpreted, except the single-quote that closes the string.

<code>
echo 'Your PATH is: $PATH'
</code>
''$PATH'' won't be expanded, it's interpreted as ordinary text because it's surrounded by strong quotes.

In practise that means, to produce a text like ''Here's my test...'' as a single-quoted string, you have to leave and re-enter the single quoting to get the character &quot;''<nowiki>'</nowiki>''&quot; as literal text:
<code>
# WRONG
echo 'Here's my test...'

# RIGHT
echo 'Here'\''s my test...'

# ALTERNATIVE: It's also possible to mix-and-match quotes for readability:
echo &quot;Here's my test&quot;
</code>


===== ANSI C like strings =====

Bash provides another quoting mechanism: Strings that contain ANSI C-like escape sequences. The Syntax is:
<code>
$'string'
</code>
where the following escape sequences are decoded in ''string'':
^ Code ^ Meaning ^
| ''\&quot;'' | double-quote |
| ''\%%'%%'' | single-quote |
| ''\\'' | backslash |
| ''\a'' | terminal alert character (bell) |
| ''\b'' | backspace |
| ''\e'' | escape (ASCII 033) |
| ''\E'' | escape (ASCII 033) **\E is non-standard** |
| ''\f'' | form feed |
| ''\n'' | newline |
| ''\r'' | carriage return |
| ''\t'' | horizontal tab |
| ''\v'' | vertical tab |
| ''\cx'' | a control-x character, for example, ''%%$'\cZ'%%'' to print the control sequence composed of Ctrl-Z (''^Z'') |
| ''\uXXXX'' | Interprets ''XXXX'' as a hexadecimal number and prints the corresponding character from the character set (4 digits) (Bash 4.2-alpha) |
| ''\UXXXXXXXX'' | Interprets ''XXXX'' as a hexadecimal number and prints the corresponding character from the character set (8 digits) (Bash 4.2-alpha) |
| ''\nnn'' | the eight-bit character whose value is the octal value nnn (one to three digits) |
| ''\xHH'' | the eight-bit character whose value is the hexadecimal value HH (one or two hex digits) |



This is especially useful when you want to pass special characters as arguments to some programs, like passing a newline to sed.

The resulting text is treated as if it were **single-quoted**. No further expansion happens.

The ''<nowiki>$'...'</nowiki>'' syntax comes from ksh93, but is portable to most modern shells including pdksh. A [[http://austingroupbugs.net/view.php?id=249#c590 | specification]] for it was accepted for SUS issue 7. There are still some stragglers, such as most ash variants including dash, (except busybox built with &quot;bash compatibility&quot; features).
===== I18N/L10N =====

A dollar-sign followed by a double-quoted string, for example
<code>
echo $&quot;generating database...&quot;
</code>
means I18N. If there is a translation available for that string, it is used instead of the given text. If not, or if the locale is ''C''/''POSIX'', the dollar sign is simply ignored, which results in a normal double quoted string.

If the string was replaced (translated), the result is double quoted.

In case you're a C programmer: The purpose of ''$&quot;...&quot;'' is the same as for ''gettext()'' or ''_()''.

For useful examples to localize your scripts, please see [[http://tldp.org/LDP/abs/html/localization.html | Appendix I of the Advanced Bash Scripting Guide]].

**Attention:** There is a security hole. Please read [[http://www.gnu.org/software/gettext/manual/html_node/bash.html | the gettext documentation]]

===== Common mistakes =====


==== String lists in for-loops ====

The [[syntax:ccmd:classic_for | classic for loop]] uses a list of words to iterate through. The list can also be in a variable:
<code>
mylist=&quot;DOG CAT BIRD HORSE&quot;
</code>

**__WRONG__** way to iterate through this list:
<code>
for animal in &quot;$mylist&quot;; do
    echo $animal
done
</code>
Why? Due to the double-quotes, technically, the expansion of ''$mylist'' is seen as **one word**. The for loop iterates exactly one time, with ''animal'' set to the whole list.

**__RIGHT__** way to iterate through this list:
<code>
for animal in $mylist; do
    echo $animal
done
</code>

==== Working out the test-command ====

The command ''test'' or ''[ ... ]'' ([[commands:classictest | the classic test command]]) is an ordinary command, so ordinary syntax rules apply. Let's take string comparison as an example:
<code>
[ WORD = WORD ]
</code>

The '']'' at the end is a convenience; if you type ''which ['' you will see that there is in fact a binary file with that name. So if we were writing this as a test command it would be:

<code>
test WORD = WORD
</code>


When you compare variables, it's wise to quote them. Let's create a test string with spaces:
<code>
mystring=&quot;my string&quot;
</code>

And now check that string against the word &quot;testword&quot;:

<code>
[ $mystring = testword ] # WRONG!
</code>
This fails! These are too many arguments for the string comparison test. After expansion is performed, you really execute:
<code>
[ my string = testword ]
test my string = testword
</code>
Which is wrong, because ''my'' and ''string'' are two separate arguments.

So what you really want to do is:
<code>
[ &quot;$mystring&quot; = testword ] # RIGHT!
</code>

<code>
test 'my string' = testword
</code>

Now the command has three parameters, which makes sense for a binary (two argument) operator.

**__Hint:__** Inside the [[syntax:ccmd:conditional_expression | conditional expression]] (''<nowiki>[[ ]]</nowiki>'') Bash doesn't perform word splitting, and thus you don't need to quote your variable references - they are always seen as &quot;one word&quot;.

===== See also =====

  * Internal: [[syntax:words | Some words about words...]]
  * Internal: [[syntax:expansion:wordsplit | Word splitting]]
  * Internal: [[syntax:expansion:intro | Introduction to expansions and substitutions]]

