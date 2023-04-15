====== Parameter expansion ======

{{keywords>bash shell scripting expansion substitution text variable parameter mangle substitute change check defined null array arrays}}


===== Introduction =====

One core functionality of Bash is to manage **parameters**. A parameter is an entity that stores values and is referenced by a **name**, a **number** or a **special symbol**.
  * parameters referenced by a name are called **variables** (this also applies to [[syntax:arrays|arrays]])
  * parameters referenced by a number are called **positional parameters** and reflect the arguments given to a shell
  * parameters referenced by a **special symbol** are auto-set parameters that have different special meanings and uses

**Parameter expansion** is the procedure to get the value from the referenced entity, like expanding a variable to print its value. On expansion time you can do very nasty things with the parameter or its value. These things are described here.

**If you saw** some parameter expansion syntax somewhere, and need to check what it can be, try the overview section below!

**Arrays** can be special cases for parameter expansion, every applicable description mentions arrays below. Please also see the [[syntax:arrays|article about arrays]].

For a more technical view what a parameter is and which types exist, [[dict:terms:parameter | see the dictionary entry for &quot;parameter&quot;]].

===== Overview =====
Looking for a specific syntax you saw, without knowing the name?
  * [[#simple_usage | Simple usage]]
    * ''$PARAMETER''
    * ''${PARAMETER}''
  * [[#indirection | Indirection]]
    * ''${!PARAMETER}''
  * [[#case_modification | Case modification ]]
    * ''${PARAMETER^}''
    * ''${PARAMETER^^}''
    * ''${PARAMETER,}''
    * ''${PARAMETER,,}''
    * ''${PARAMETER~}''
    * ''${PARAMETER~~}''
  * [[#variable_name_expansion | Variable name expansion]]
    * ''${!PREFIX*}''
    * ''${!PREFIX@}''
  * [[#substring_removal | Substring removal]] (also for **filename manipulation**!)
    * ''${PARAMETER#PATTERN}''
    * ''${PARAMETER##PATTERN}''
    * ''${PARAMETER%PATTERN}''
    * ''<nowiki>${PARAMETER%%PATTERN}</nowiki>''
  * [[#search_and_replace | Search and replace]]
    * ''${PARAMETER/PATTERN/STRING}''
    * ''<nowiki>${PARAMETER//PATTERN/STRING}</nowiki>''
    * ''${PARAMETER/PATTERN}''
    * ''<nowiki>${PARAMETER//PATTERN}</nowiki>''
  * [[#string_length | String length ]]
    * ''${#PARAMETER}''
  * [[#substring_expansion | Substring expansion]]
    * ''${PARAMETER:OFFSET}''
    * ''${PARAMETER:OFFSET:LENGTH}''
  * [[#use_a_default_value | Use a default value]]
    * ''${PARAMETER:-WORD}''
    * ''${PARAMETER-WORD}''
  * [[#assign_a_default_value | Assign a default value]]
    * ''${PARAMETER:=WORD}''
    * ''${PARAMETER=WORD}''
  * [[#use_an_alternate_value | Use an alternate value]]
    * ''${PARAMETER:+WORD}''
    * ''${PARAMETER+WORD}''
  * [[#display_error_if_null_or_unset | Display error if null or unset]]
    * ''${PARAMETER:?WORD}''
    * ''${PARAMETER?WORD}''

===== Simple usage =====
''$PARAMETER''

''${PARAMETER}''

The easiest form is to just use a parameter's name within braces. This is identical to using ''$FOO'' like you see it everywhere, but has the advantage that it can be immediately followed by characters that would be interpreted as part of the parameter name otherwise. Compare these two expressions (''WORD=&quot;car&quot;'' for example), where we want to print a word with a trailing &quot;s&quot;:

<code>
echo &quot;The plural of $WORD is most likely $WORDs&quot;
echo &quot;The plural of $WORD is most likely ${WORD}s&quot;
</code>

__Why does the first one fail?__ It prints nothing, because a parameter (variable) named &quot;''WORDs''&quot; is undefined and thus printed as &quot;&quot; (//nothing//). Without using braces for parameter expansion, Bash will interpret the sequence of all valid characters from the introducing &quot;''$''&quot; up to the last valid character as name of the parameter. When using braces you just force Bash to **only interpret the name inside your braces**.

Also, please remember, that **parameter names are** (like nearly everything in UNIX(r)) **case sensitive!**

The second form with the curly braces is also needed to access positional parameters (arguments to a script) beyond ''$9'':
<code>
echo &quot;Argument  1 is: $1&quot;
echo &quot;Argument 10 is: ${10}&quot;
</code>

==== Simple usage: Arrays ====

See also the [[syntax:arrays|article about general array syntax]]

For arrays you always need the braces. The arrays are expanded by individual indexes or mass arguments. An individual index behaves like a normal parameter, for the mass expansion, please read the article about arrays linked above.

  * ${array[5]}
  * ${array[*]}
  * ${array[@]}

===== Indirection =====

''${!PARAMETER}''

In some cases, like for example

<code>
${PARAMETER}

${PARAMETER:0:3}
</code>

you can instead use the form

<code>${!PARAMETER}</code>

to enter a level of indirection. The referenced parameter is not ''PARAMETER'' itself, but the parameter whose name is stored as the value of ''PARAMETER''. If the parameter ''PARAMETER'' has the value &quot;''TEMP''&quot;, then ''${!PARAMETER}'' will expand to the value of the parameter named ''TEMP'':
<code>
read -rep 'Which variable do you want to inspect? ' look_var

printf 'The value of &quot;%s&quot; is: &quot;%s&quot;\n' &quot;$look_var&quot; &quot;${!look_var}&quot; 
</code>

Of course the indirection also works with special variables:

<code>
# set some fake positional parameters
set one two three four

# get the LAST argument (&quot;#&quot; stores the number of arguments, so &quot;!#&quot; will reference the LAST argument)
echo ${!#}
</code>

You can think of this mechanism as being roughly equivalent to taking any parameter expansion that begins with the parameter name, and substituting the ''!PARAMETER'' part with the value of PARAMETER.

<code>
echo &quot;${!var^^}&quot;
# ...is equivalent to
eval 'echo &quot;${'&quot;$var&quot;'^^}&quot;'
</code>

It was an unfortunate design decision to use the ''!'' prefix for indirection, as it introduces parsing ambiguity with other parameter expansions that begin with ''!''. Indirection is not possible in combination with any parameter expansion whose modifier requires a prefix to the parameter name. Specifically, indirection isn't possible on the ''${!var@}'', ''${!var*}'', ''${!var[@]}'', ''${!var[*]}'', and ''${#var}'' forms. This means the ''!'' prefix can't be used to retrieve the indices of an array, the length of a string, or number of elements in an array indirectly (see [[syntax/arrays#indirection]] for workarounds). Additionally, the ''!''-prefixed parameter expansion conflicts with ksh-like shells which have the more powerful &quot;name-reference&quot; form of indirection, where the exact same syntax is used to expand to the name of the variable being referenced.

Indirect references to [[syntax:arrays|array names]] are also possible since the Bash 3 series (exact version unknown), but undocumented. See [[syntax/arrays#indirection]] for details.

Chet has added an initial implementation of the ksh ''nameref'' declaration command to the git devel branch. (''declare -n'', ''local -n'', etc, will be supported). This will finally address many issues around passing and returning complex datatypes to/from functions.

===== Case modification =====

''${PARAMETER^}''

''${PARAMETER^^}''

''${PARAMETER,}''

''${PARAMETER,,}''

''${PARAMETER~}''

''${PARAMETER~~}''

These expansion operators modify the case of the letters in the expanded text.

The ''^'' operator modifies the first character to uppercase, the '','' operator to lowercase. When using the double-form (''^^'' and '',,''), all characters are converted.

<wrap center round info 60%>

The (**currently undocumented**) operators ''~'' and ''~~'' reverse the case of the given text (in ''PARAMETER'').''~'' reverses the case of first letter of words in the variable while ''~~'' reverses case for all.  Thanks to ''Bushmills'' and ''geirha'' on the Freenode IRC channel for this finding.

</wrap>


__**Example: Rename all ''*.txt'' filenames to lowercase**__
<code>
for file in *.txt; do
  mv &quot;$file&quot; &quot;${file,,}&quot;
done
</code>

__**Note:**__  Case modification is a handy feature you can apply to a name or a title.   Or is it?  Case modification was an important aspect of the Bash 4 release. Bash version 4, RC1 would perform word splitting, and then case modification, resulting in title case (where every word is capitalized).  It was decided to apply case modification to values, not words, for the Bash 4 release.  Thanks Chet.


==== Case modification: Arrays ====

Case modification can be used to create the proper capitalization for names or titles.  Just assign it to an array:

''declare -a title=(my hello world john smith)''

For [[syntax:arrays|array]] expansion, the case modification applies to **every expanded element, no matter if you expand an individual index or mass-expand** the whole array using ''@'' or ''*'' subscripts. Some examples:

Assume: ''array=(This is some Text)''

  * ''echo &quot;${array[@],}&quot;''
    * => ''this is some text''
  * ''echo &quot;${array[@],,}&quot;''
    * => ''this is some text''
  * ''echo &quot;${array[@]^}&quot;''
    * => ''This Is Some Text''
  * ''echo &quot;${array[@]^^}&quot;''
    * => ''THIS IS SOME TEXT''
   * ''echo &quot;${array[2]^^}&quot;''
     * => ''SOME''

===== Variable name expansion =====

''${!PREFIX*}''

''${!PREFIX@}''

This expands to a list of all set **variable names** beginning with the string ''PREFIX''. The elements of the list are separated by the first character in the ''IFS''-variable (<space> by default).

This will show all defined variable names (not values!) beginning with &quot;BASH&quot;:
<code>
$ echo ${!BASH*}
BASH BASH_ARGC BASH_ARGV BASH_COMMAND BASH_LINENO BASH_SOURCE BASH_SUBSHELL BASH_VERSINFO BASH_VERSION
</code>

This list will also include [[syntax:arrays|array names]].

===== Substring removal =====
''${PARAMETER#PATTERN}''

''${PARAMETER##PATTERN}''

''${PARAMETER%PATTERN}''

''<nowiki>${PARAMETER%%PATTERN}</nowiki>''

This one can **expand only a part** of a parameter's value, **given a pattern to describe what to remove** from the string. The pattern is interpreted just like a pattern to describe a filename to match (globbing). See [[syntax:pattern | Pattern matching]] for more.

Example string (//just a quote from a big man//):
<code>
MYSTRING=&quot;Be liberal in what you accept, and conservative in what you send&quot;
</code>


==== From the beginning ====
''${PARAMETER#PATTERN}'' and ''${PARAMETER##PATTERN}''

This form is to remove the described [[syntax:pattern | pattern]] trying to **match it from the beginning of the string**.
The operator &quot;''#''&quot; will try to remove the shortest text matching the pattern, while &quot;''##''&quot; tries to do it with the longest text matching. Look at the following examples to get the idea (matched text <del>marked striked</del>, remember it will be removed!):
^Syntax^Result^
|''${MYSTRING#*in}''|<del>Be liberal in</del> what you accept, and conservative in what you send|
|''${MYSTRING##*in}''|<del>Be liberal in what you accept, and conservative in</del> what you send|

==== From the end ====
''${PARAMETER%PATTERN}'' and ''<nowiki>${PARAMETER%%PATTERN}</nowiki>''

In the second form everything will be the same, except that Bash now tries to match the pattern from the end of the string:
^ Syntax                                 ^ Result                                                                          ^
| ''${MYSTRING%in*}''                    | Be liberal in what you accept, and conservative <del>in what you send</del>  |
| ''<nowiki>${MYSTRING%%in*}</nowiki>''  | Be liberal <del>in what you accept, and conservative in what you send</del>  |

The second form nullifies variables that begin with ''in'', by working from the end.
==== Common use ====
__**How the heck does that help to make my life easier?**__

Well, maybe the most common use for it is to **extract parts of a filename**. Just look at the following list with examples:

  * **Get name without extension**
    * ''${FILENAME%.*}''
    * => ''bash_hackers<del>.txt</del>''
  * **Get extension**
    * ''${FILENAME##*.}''
    * => ''<del>bash_hackers.</del>txt''
  * **Get directory name**
    * ''${PATHNAME%/*}''
    * => ''/home/bash<del>/bash_hackers.txt</del>''
  * **Get filename**
    * ''${PATHNAME##*/}''
    * => ''<del>/home/bash/</del>bash_hackers.txt''

These are the syntaxes for filenames with a single extension. Depending on your needs, you might need to adjust shortest/longest match.

==== Substring removal: Arrays ====

As for most parameter expansion features, working on [[syntax:arrays|arrays]] **will handle each expanded element**, for individual expansion and also for mass expansion.

Simple example, removing a trailing ''is'' from all array elements (on expansion):

Assume: ''array=(This is a text)''

  * ''echo &quot;${array[@]%is}&quot;''
    * => ''Th  a text''
    * (it was: ''Th<del>is</del> <del>is</del> a text'')

All other variants of this expansion behave the same.



===== Search and replace =====
''${PARAMETER/PATTERN/STRING}''

''<nowiki>${PARAMETER//PATTERN/STRING}</nowiki>''

''<nowiki>${PARAMETER/PATTERN}</nowiki>''

''<nowiki>${PARAMETER//PATTERN}</nowiki>''

This one can substitute (//replace//) a substring [[syntax:pattern | matched by a pattern]], on expansion time. The matched substring will be entirely removed and the given string will be inserted. Again some example string for the tests:
<code>
MYSTRING=&quot;Be liberal in what you accept, and conservative in what you send&quot;
</code>

The two main forms only differ in **the number of slashes** after the parameter name: ''${PARAMETER/PATTERN/STRING}'' and ''<nowiki>${PARAMETER//PATTERN/STRING}</nowiki>''

The first one (//one slash//) is to only substitute **the first occurrence** of the given pattern, the second one (//two slashes//) is to substitute **all occurrences** of the pattern.

First, let's try to say &quot;happy&quot; instead of &quot;conservative&quot; in our example string:
<code>
${MYSTRING//conservative/happy}
</code>
=> ''Be liberal in what you accept, and <del>conservative</del>happy in what you send''

Since there is only one &quot;conservative&quot; in that example, it really doesn't matter which of the two forms we use.

Let's play with the word &quot;in&quot;, I don't know if it makes any sense, but let's substitute it with &quot;by&quot;.

__**First form: Substitute first occurrence**__
<code>${MYSTRING/in/by}</code>
=> ''Be liberal <del>in</del>by what you accept, and conservative in what you send''

__**Second form: Substitute all occurrences**__
<code>${MYSTRING//in/by}</code>
=> ''Be liberal <del>in</del>by what you accept, and conservative <del>in</del>by what you send''

__**Anchoring**__
Additionally you can &quot;anchor&quot; an expression:
A ''#'' (hashmark) will indicate that your expression is matched against the beginning portion of the string, a ''%'' (percent-sign) will do it for the end portion.

<code>MYSTRING=xxxxxxxxxx
echo ${MYSTRING/#x/y}  # RESULT: yxxxxxxxxx
echo ${MYSTRING/%x/y}  # RESULT: xxxxxxxxxy</code>

If the replacement part is completely omitted, the matches are replaced by the nullstring, i.e., they are removed. This is equivalent to specifying an empty replacement:
<code>
echo ${MYSTRING//conservative/}
# is equivalent to
echo ${MYSTRING//conservative}
</code>


==== Search and replace: Arrays ====

This parameter expansion type applied to [[syntax:arrays|arrays]] **applies to all expanded elements**, no matter if an individual element is expanded, or all elements using the mass expansion syntaxes.

A simple example, changing the (lowercase) letter ''t'' to ''d'':

Assume: ''array=(This is a text)''

  * ''echo &quot;${array[@]/t/d}&quot;''
    * => ''This is a dext''
  * ''echo &quot;${array[@]<nowiki>//</nowiki>t/d}&quot;''
    * => ''This is a dexd''

===== String length =====
''${#PARAMETER}''

When you use this form, the length of the parameter's value is expanded. Again, a quote from a big man, to have a test text:

<code>MYSTRING=&quot;Be liberal in what you accept, and conservative in what you send&quot;</code>

Using echo ''${#MYSTRING}''...

=> ''64''

The length is reported in characters, not in bytes. Depending on your environment this may not always be the same (multibyte-characters, like in UTF8 encoding).

There's not much to say about it, mh?

==== (String) length: Arrays ====

For  [[syntax:arrays|arrays]], this expansion type has two meanings:
  * For **individual** elements, it reports the string length of the element (as for every &quot;normal&quot; parameter)
  * For the **mass subscripts** ''@'' and ''*'' it reports the number of set elements in the array

Example:

Assume: ''array=(This is a text)''

  * ''echo ${#array[1]}''
    * => 2 (the word &quot;is&quot; has a length of 2)
  * ''echo ${#array[@]}''
    * => 4 (the array contains 4 elements)

__**Attention:**__ The number of used elements does not need to conform to the highest index. Sparse arrays are possible in Bash, that means you can have 4 elements, but with indexes 1, 7, 20, 31. **You can't loop through such an array with a counter loop based on the number of elements!**

===== Substring expansion =====
''${PARAMETER:OFFSET}''

''${PARAMETER:OFFSET:LENGTH}''

This one can expand only a **part** of a parameter's value, given a **position to start** and maybe a **length**. If ''LENGTH'' is omitted, the parameter will be expanded up to the end of the string. If ''LENGTH'' is negative, it's taken as a second offset into the string, counting from the end of the string.

''OFFSET'' and ''LENGTH'' can be **any** [[syntax:arith_expr | arithmetic expression]]. **Take care:** The ''OFFSET'' starts at 0, not at 1!

Example string (a quote from a big man):
''MYSTRING=&quot;Be liberal in what you accept, and conservative in what you send&quot;''

==== Using only Offset ====
In the first form, the expansion is used without a length value, note that the offset 0 is the first character:
<code>echo ${MYSTRING:35}</code>
=> ''<del>Be liberal in what you accept, and </del>conservative in what you send''

==== Using Offset and Length ====
In the second form we also give a length value:
<code>echo ${MYSTRING:35:12}</code>
=> ''<del>Be liberal in what you accept, and </del>conservative<del> in what you send</del>''

==== Negative Offset Value ====
If the given offset is negative, it's counted from the end of the string, i.e. an offset of -1 is the last character. In that case, the length still counts forward, of course. One special thing is to do when using a negative offset: You need to separate the (negative) number from the colon:
<code>
${MYSTRING: -10:5}
${MYSTRING:(-10):5}
</code>
Why? Because it's interpreted as the parameter expansion syntax to [[syntax:pe#use_a_default_value | use a default value]].

==== Negative Length Value ====
If the ''LENGTH'' value is negative, it's used as offset from the end of the string. The expansion happens from the first to the second offset then:
<code>
echo &quot;${MYSTRING:11:-17}&quot;
</code>
=> ''<del>Be liberal </del>in what you accept, and conservative<del> in what you send</del>''

This works since Bash 4.2-alpha, see also [[scripting:bashchanges]].



==== Substring/Element expansion: Arrays ====

For [[syntax:arrays|arrays]], this expansion type has again 2 meanings:
  * For **individual** elements, it expands to the specified substring (as for every “normal” parameter)
  * For the **mass subscripts** ''@'' and ''*'' it mass-expands individual array elements denoted by the 2 numbers given (//starting element//, //number of elements//)

Example:

Assume: ''array=(This is a text)''

  * ''echo ${array[0]:2:2}''
    * => ''is'' (the &quot;is&quot; in &quot;This&quot;, array element 0)
  * ''echo ${array[@]:1:2}''
    * => ''is a'' (from element 1 inclusive, 2 elements are expanded, i.e. element 1 and 2)

===== Use a default value =====

''${PARAMETER:-WORD}''

''${PARAMETER-WORD}''

If the parameter ''PARAMETER'' is unset (never was defined) or null (empty), this one expands to ''WORD'', otherwise it expands to the value of ''PARAMETER'', as if it just was ''${PARAMETER}''. If you omit the '':'' (colon), like shown in the second form, the default value is only used when the parameter was **unset**, not when it was empty.

<code>
echo &quot;Your home directory is: ${HOME:-/home/$USER}.&quot;
echo &quot;${HOME:-/home/$USER} will be used to store your personal data.&quot;
</code>

If ''HOME'' is unset or empty, everytime you want to print something useful, you need to put that parameter syntax in.

<code>
#!/bin/bash

read -p &quot;Enter your gender (just press ENTER to not tell us): &quot; GENDER
echo &quot;Your gender is ${GENDER:-a secret}.&quot;
</code>

It will print &quot;Your gender is a secret.&quot; when you don't enter the gender. Note that the default value is **used on expansion time**, it is **not assigned to the parameter**.


==== Use a default value: Arrays ====

For [[syntax:arrays|arrays]], the behaviour is very similar. Again, you have to make a difference between expanding an individual element by a given index and mass-expanding the array using the ''@'' and ''*'' subscripts.

  * For individual elements, it's the very same: If the expanded element is ''NULL'' or unset (watch the '':-'' and ''-'' variants), the default text is expanded
  * For mass-expansion syntax, the default text is expanded if the array
    * contains no element or is unset (the '':-'' and ''-'' variants mean the **same** here)
    * contains only elements that are the nullstring (the '':-'' variant)

In other words: The basic meaning of this expansion type is applied as consistent as possible to arrays.

Example code (please try the example cases yourself):

<code>

####
# Example cases for unset/empty arrays and nullstring elements
####


### CASE 1: Unset array (no array)

# make sure we have no array at all
unset array

echo ${array[@]:-This array is NULL or unset}
echo ${array[@]-This array is NULL or unset}

### CASE 2: Set but empty array (no elements)

# declare an empty array
array=()

echo ${array[@]:-This array is NULL or unset}
echo ${array[@]-This array is NULL or unset}


### CASE 3: An array with only one element, a nullstring
array=(&quot;&quot;)

echo ${array[@]:-This array is NULL or unset}
echo ${array[@]-This array is NULL or unset}


### CASE 4: An array with only two elements, a nullstring and a normal word
array=(&quot;&quot; word)

echo ${array[@]:-This array is NULL or unset}
echo ${array[@]-This array is NULL or unset}
</code>

===== Assign a default value =====

''${PARAMETER:=WORD}''

''${PARAMETER=WORD}''

This one works like the [[syntax:pe#use_a_default_value | using default values]], but the default text you give is not only expanded, but also **assigned** to the parameter, if it was unset or null. Equivalent to using a default value, when you omit the '':'' (colon), as shown in the second form, the default value will only be assigned when the parameter was **unset**.

<code>
echo &quot;Your home directory is: ${HOME:=/home/$USER}.&quot;
echo &quot;$HOME will be used to store your personal data.&quot;
</code>

After the first expansion here (''${HOME:=/home/$USER}''), ''HOME'' is set and usable.

Let's change our code example from above:

<code>
#!/bin/bash

read -p &quot;Enter your gender (just press ENTER to not tell us): &quot; GENDER
echo &quot;Your gender is ${GENDER:=a secret}.&quot;
echo &quot;Ah, in case you forgot, your gender is really: $GENDER&quot;
</code>

==== Assign a default value: Arrays ====

For [[syntax:arrays|arrays]] this expansion type is limited. For an individual index, it behaves like for a &quot;normal&quot; parameter, the default value is assigned to this one element. The mass-expansion subscripts ''@'' and ''*'' **can not be used here** because it's not possible to assign to them!

===== Use an alternate value =====

''${PARAMETER:+WORD}''

''${PARAMETER+WORD}''

This form expands to nothing if the parameter is unset or empty. If it is set, it does not expand to the parameter's value, **but to some text you can specify**:
<code>
echo &quot;The Java application was installed and can be started.${JAVAPATH:+ NOTE: JAVAPATH seems to be set}&quot;
</code>
The above code will simply add a warning if ''JAVAPATH'' is set (because it could influence the startup behaviour of that imaginary application).

Some more unrealistic example... Ask for some flags (for whatever reason), and then, if they were set, print a warning and also print the flags:
<code>
#!/bin/bash

read -p &quot;If you want to use special flags, enter them now: &quot; SPECIAL_FLAGS
echo &quot;The installation of the application is finished${SPECIAL_FLAGS:+ (NOTE: there are special flags set: $SPECIAL_FLAGS)}.&quot;
</code>

If you omit the colon, as shown in the second form (''${PARAMETER+WORD}''), the alternate value will be used if the parameter is set (and it can be empty)! You can use it, for example, to complain if variables you need (and that can be empty) are undefined:
<code>
# test that with the three stages:

# unset foo
# foo=&quot;&quot;
# foo=&quot;something&quot;

if [[ ${foo+isset} = isset ]]; then
  echo &quot;foo is set...&quot;
else
  echo &quot;foo is not set...&quot;
fi
</code>

==== Use an alternate value: Arrays ====

Similar to the cases for [[syntax:arrays|arrays]] to expand to a default value, this expansion behaves like for a &quot;normal&quot; parameter when using individual array elements by index, but reacts differently when using the mass-expansion subscripts ''@'' and ''*'':

    * For individual elements, it's the very same: If the expanded element is **not** NULL or unset (watch the :+ and + variants), the alternate text is expanded
    * For mass-expansion syntax, the alternate text is expanded if the array
      * contains elements where min. one element is **not** a nullstring (the :+ and + variants mean the same here)
      * contains **only** elements that are **not** the nullstring (the :+ variant)

For some cases to play with, please see the code examples in the [[#use_a_default_valuearrays|description for using a default value]].

===== Display error if null or unset =====

''${PARAMETER:?WORD}''

''${PARAMETER?WORD}''

If the parameter ''PARAMETER'' is set/non-null, this form will simply expand it. Otherwise, the expansion of ''WORD'' will be used as appendix for an error message:
<code>
$ echo &quot;The unset parameter is: ${p_unset?not set}&quot;
bash: p_unset: not set
</code>

After printing this message,
  * an interactive shell has ''$?'' to a non-zero value
  * a non-interactive shell exits with a non-zero exit code

The meaning of the colon ('':'') is the same as for the other parameter expansion syntaxes: It specifies if
  * only unset or
  * unset and empty parameters
are taken into account.


===== Code examples =====

==== Substring removal ====

Removing the first 6 characters from a text string:
<code>
STRING=&quot;Hello world&quot;

# only print 'Hello'
echo &quot;${STRING%??????}&quot;

# only print 'world'
echo &quot;${STRING#??????}&quot;

# store it into the same variable
STRING=${STRING#??????}
</code>

===== Bugs and Portability considerations =====

  * **Fixed in 4.2.36** ([[ftp://ftp.cwru.edu/pub/bash/bash-4.2-patches/bash42-036 | patch]]). Bash doesn't follow either POSIX or its own documentation when expanding either a quoted ''&quot;$@&quot;'' or ''&quot;${arr[@]}&quot;'' with an adjacent expansion. ''&quot;$@$x&quot;'' expands in the same way as ''&quot;$*$x&quot;'' - i.e. all parameters plus the adjacent expansion are concatenated into a single argument. As a workaround, each expansion needs to be quoted separately. Unfortunately, this bug took a very long time to notice.<code>
~ $ set -- a b c; x=foo; printf '<%s> ' &quot;$@$x&quot; &quot;$*&quot;&quot;$x&quot; &quot;$@&quot;&quot;$x&quot;
<a b cfoo> <a b cfoo> <a> <b> <cfoo>
</code>

  * Almost all shells disagree about the treatment of an unquoted ''$@'', ''${arr[@]}'', ''$*'', and ''${arr[*]}'' when [[http://mywiki.wooledge.org/IFS | IFS]] is set to null. POSIX is unclear about the expected behavior. A null IFS causes both [[syntax:expansion:wordsplit | word splitting]] and [[syntax:expansion:globs | pathname expansion]] to behave randomly. Since there are few good reasons to leave ''IFS'' set to null for more than the duration of a command or two, and even fewer to expand ''$@'' and ''$*'' unquoted, this should be a rare issue. **Always quote them**!<code>
touch x 'y z'
for sh in bb {{d,b}a,{m,}k,z}sh; do
    echo &quot;$sh&quot;
    &quot;$sh&quot; -s a 'b c' d \* </dev/fd/0
done <<\EOF
${ZSH_VERSION+:} false && emulate sh
IFS=
printf '<%s> ' $*
echo
printf &quot;<%s> &quot; $@
echo
EOF
</code><code>
bb
<ab cd*>
<ab cd*>
dash
<ab cd*>
<ab cd*>
bash
<a> <b c> <d> <x> <y z>
<a> <b c> <d> <x> <y z>
mksh
<a b c d *>
<a b c d *>
ksh
<a> <b c> <d> <x> <y z>
<a> <b c> <d> <x> <y z>
zsh
<a> <b c> <d> <x> <y z>
<a> <b c> <d> <x> <y z>
</code>When ''IFS'' is set to a non-null value, or unset, all shells behave the same - first expanding into separate args, then applying pathname expansion and word-splitting to the results, except for zsh, which doesn't do pathname expansion in its default mode.

  * Additionally, shells disagree about various wordsplitting behaviors, the behavior of inserting delimiter characters from IFS in ''$*'', and the way adjacent arguments are concatenated, when IFS is modified in the middle of expansion through side-effects.<code>
for sh in bb {{d,b}a,po,{m,}k,z}sh; do
    printf '%-4s: ' &quot;$sh&quot;
    &quot;$sh&quot; </dev/fd/0
done <<\EOF
${ZSH_VERSION+:} false && emulate sh
set -f -- a b c
unset -v IFS
printf '<%s> ' ${*}${IFS=}${*}${IFS:=-}&quot;${*}&quot;
echo
EOF
</code><code>
bb  : <a b cabc> <a-b-c>
dash: <a b cabc> <a-b-c>
bash: <a> <b> <ca> <b> <c-a b c>
posh: <a> <b> <ca b c> <a-b-c>
mksh: <a> <b> <ca b c> <a-b-c>
ksh : <a> <b> <ca> <b> <c> <a b c>
zsh : <a> <b> <ca> <b> <c> <a-b-c>
</code>ksh93 and mksh can additionally achieve this side effect (and others) via the ''${ cmds;}'' expansion. I haven't yet tested every possible side-effect that can affect expansion halfway through expansion that way.

  * As previously mentioned, the Bash form of indirection by prefixing a parameter expansion with a ''!'' conflicts with the same syntax used by mksh, zsh, and ksh93 for a different purpose. Bash will &quot;slightly&quot; modify this expansion in the next version with the addition of namerefs.

  * Bash (and most other shells) don't allow .'s in identifiers. In ksh93, dots in variable names are used to reference methods (i.e. &quot;Discipline Functions&quot;), attributes, special shell variables, and to define the &quot;real value&quot; of an instance of a class.

  * In ksh93, the ''_'' parameter has even more uses. It is used in the same way as ''self'' in some object-oriented languages; as a placeholder for some data local to a class; and also as the mechanism for class inheritance. In most other contexts, ''_'' is compatible with Bash.

  * Bash only evaluates the subscripts of the slice expansion (''${x:y:z}'') if the parameter is set (for both nested expansions and arithmetic). For ranges, Bash evaluates as little as possible, i.e., if the first part is out of range, the second won't be evaluated. ksh93 and mksh always evaluate the subscript parts even if the parameter is unset. <code>
 $ bash -c 'n=&quot;y[\$(printf yo >&2)1]&quot; m=&quot;y[\$(printf jo >&2)1]&quot;; x=(); echo &quot;${x[@]:n,6:m}&quot;' # No output
 $ bash -c 'n=&quot;y[\$(printf yo >&2)1]&quot; m=&quot;y[\$(printf jo >&2)1]&quot;; x=([5]=hi); echo &quot;${x[@]:n,6:m}&quot;'
yo
 $ bash -c 'n=&quot;y[\$(printf yo >&2)1]&quot; m=&quot;y[\$(printf jo >&2)1]&quot;; x=([6]=hi); echo &quot;${x[@]:n,6:m}&quot;'
yojo
 $ bash -c 'n=&quot;y[\$(printf yo >&2)1]&quot; m=&quot;y[\$(printf jo >&2)1]&quot;; x=12345; echo &quot;${x:n,5:m}&quot;'
yojo
 $ bash -c 'n=&quot;y[\$(printf yo >&2)1]&quot; m=&quot;y[\$(printf jo >&2)1]&quot;; x=12345; echo &quot;${x:n,6:m}&quot;'
yo
</code>

==== Quote Nesting ====

  * In most shells, when dealing with an &quot;alternate&quot; parameter expansion that expands to multiple words, and nesting such expansions, not all combinations of nested quoting are possible.
<code>
# Bash
 $ typeset -a a=(meh bleh blerg) b
 $ IFS=e
 $ printf &quot;<%s> &quot; &quot;${b[@]-&quot;${a[@]}&quot; &quot;${a[@]}&quot;}&quot;; echo # The entire PE is quoted so Bash considers the inner quotes redundant.
<meh> <bleh> <blerg meh> <bleh> <blerg>
 $ printf &quot;<%s> &quot; &quot;${b[@]-${a[@]} ${a[@]}}&quot;; echo # The outer quotes cause the inner expansions to be considered quoted.
<meh> <bleh> <blerg meh> <bleh> <blerg>
 $ b=(meep beep)
 $ printf &quot;<%s> &quot; &quot;${b[@]-&quot;${a[@]}&quot; &quot;${a[@]}&quot;}&quot; &quot;${b[@]-${a[@]} ${a[@]}}&quot;; echo # Again no surprises. Outer quotes quote everything recursively.
<meep> <beep> <meep> <beep>
</code>

Now lets see what can happen if we leave the outside unquoted.
<code>
# Bash
 $ typeset -a a=(meh bleh blerg) b
 $ IFS=e
 $ printf &quot;<%s> &quot; ${b[@]-&quot;${a[@]}&quot; &quot;${a[@]}&quot;}; echo # Inner quotes make inner expansions quoted.
<meh> <bleh> <blerg meh> <bleh> <blerg>
 $ printf &quot;<%s> &quot; ${b[@]-${a[@]} ${a[@]}}; echo' # No quotes at all wordsplits / globs, like you'd expect.
<m> <h> <bl> <h> <bl> <rg m> <h> <bl> <h> <bl> <rg>
</code>

This all might be intuitive, and is the most common implementation, but this design sucks for a number of reasons. For one, it means Bash makes it absolutely impossible to expand any part of the inner region //unquoted// while leaving the outer region quoted. Quoting the outer forces quoting of the inner regions recursively (except nested command substitutions of course). Word-splitting is necessary to split words of the inner region, which cannot be done together with outer quoting. Consider the following (only slightly far-fetched) code:

<code>
# Bash (non-working example)

unset -v IFS # make sure we have a default IFS

if some crap; then
    typeset -a someCmd=(myCmd arg1 'arg2 yay!' 'third*arg*' 4)
fi

someOtherCmd=mycommand
typeset -a otherArgs=(arg3 arg4)

# What do you think the programmer expected to happen here?
# What do you think will actually happen...

&quot;${someCmd[@]-&quot;$someOtherCmd&quot; arg2 &quot;${otherArgs[@]}&quot;}&quot; arg5
</code>

This final line is perhaps not the most obvious, but I've run into cases were this type of logic can be desirable and realistic. We can deduce what was intended:

  * If ''someCmd'' is set, then the resulting expansion should run the command: ''&quot;myCmd&quot; &quot;arg1&quot; &quot;arg2 yay!&quot; &quot;third*arg*&quot; &quot;4&quot; &quot;arg5&quot;''
  * Otherwise, if ''someCmd'' is not set, expand ''$someOtherCmd'' and the inner args, to run a different command: ''&quot;mycommand&quot; &quot;arg2&quot; &quot;arg3&quot; &quot;arg4&quot; &quot;arg5&quot;''.

Unfortunately, it is impossible to get the intended result in Bash (and most other shells) without taking a considerably different approach. The only way to split the literal inner parts is through word-splitting, which requires that the PE be unquoted. But, the only way to expand the outer expansion correctly without word-splitting or globbing is to quote it. Bash will actually expand the command as one of these:

<code>
# The quoted PE produces a correct result here...
 $ bash -c 'typeset -a someCmd=(myCmd arg1 &quot;arg2 yay!&quot; &quot;third*arg*&quot; 4); printf &quot;<%s> &quot; &quot;${someCmd[@]-&quot;$someOtherCmd&quot; arg2 &quot;${otherArgs[@]}&quot;}&quot; arg5; echo'
<myCmd> <arg1> <arg2 yay!> <third*arg*> <4> <arg5>

# ...but in the opposite case the first 3 arguments are glued together. There are no workarounds.
 $ bash -c 'typeset -a otherArgs=(arg3 arg4); someOtherCmd=mycommand; printf &quot;<%s> &quot; &quot;${someCmd[@]-&quot;$someOtherCmd&quot; arg2 &quot;${otherArgs[@]}&quot;}&quot; arg5; echo'
<mycommand arg2 arg3> <arg4> <arg5>

# UNLESS! we unquote the outer expansion allowing the inner quotes to
# affect the necessary parts while allowing word-splitting to split the literals:
 $ bash -c 'typeset -a otherArgs=(arg3 arg4); someOtherCmd=mycommand; printf &quot;<%s> &quot; ${someCmd[@]-&quot;$someOtherCmd&quot; arg2 &quot;${otherArgs[@]}&quot;} arg5; echo'
<mycommand> <arg2> <arg3> <arg4> <arg5>

# Success!!!
 $ bash -c 'typeset -a someCmd=(myCmd arg1 &quot;arg2 yay!&quot; &quot;third*arg*&quot; 4); printf &quot;<%s> &quot; ${someCmd[@]-&quot;$someOtherCmd&quot; arg2 &quot;${otherArgs[@]}&quot;} arg5; echo'
<myCmd> <arg1> <arg2> <yay!> <third*arg*> <4> <arg5>

# ...Ah f^^k. (again, no workaround possible.)
</code>

=== The ksh93 exception ===
To the best of my knowledge, ksh93 is the only shell that acts differently. Rather than forcing nested expansions into quoting, a quote at the beginning and end of the nested region will cause the quote state to reverse itself within the nested part. I have no idea whether it's an intentional or documented effect, but it does solve the problem and consequently adds a lot of potential power to these expansions.

All we need to do is add two extra double-quotes:
<code>
# ksh93 passing the two failed tests from above:

 $ ksh -c 'otherArgs=(arg3 arg4); someOtherCmd=&quot;mycommand&quot;; printf &quot;<%s> &quot; &quot;${someCmd[@]-&quot;&quot;$someOtherCmd&quot; arg2 &quot;${otherArgs[@]}&quot;&quot;}&quot; arg5; echo'
<mycommand> <arg2> <arg3> <arg4> <arg5>

 $ ksh -c 'typeset -a someCmd=(myCmd arg1 &quot;arg2 yay!&quot; &quot;third*arg*&quot; 4); printf &quot;<%s> &quot; &quot;${someCmd[@]-&quot;&quot;$someOtherCmd&quot; arg2 &quot;${otherArgs[@]}&quot;&quot;}&quot; arg5; echo'
<myCmd> <arg1> <arg2 yay!> <third*arg*> <4> <arg5>
</code>

This can be used to control the quote state of any part of any expansion to an arbitrary depth. Sadly, it is the only shell that does this and the difference may introduce a possible compatibility problem.
===== See also =====
  * Internal: [[syntax:expansion:intro | Introduction to expansion and substitution]]
  * Internal: [[syntax:arrays|Arrays]]
