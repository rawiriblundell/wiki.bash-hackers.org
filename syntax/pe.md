# Parameter expansion

![](keywords>bash shell scripting expansion substitution text variable parameter mangle substitute change check defined null array arrays)

## Introduction

One core functionality of Bash is to manage **parameters**. A parameter
is an entity that stores values and is referenced by a **name**, a
**number** or a **special symbol**.

- parameters referenced by a name are called **variables** (this also
  applies to [arrays](/syntax/arrays))
- parameters referenced by a number are called **positional parameters**
  and reflect the arguments given to a shell
- parameters referenced by a **special symbol** are auto-set parameters
  that have different special meanings and uses

**Parameter expansion** is the procedure to get the value from the
referenced entity, like expanding a variable to print its value. On
expansion time you can do very nasty things with the parameter or its
value. These things are described here.

**If you saw** some parameter expansion syntax somewhere, and need to
check what it can be, try the overview section below!

**Arrays** can be special cases for parameter expansion, every
applicable description mentions arrays below. Please also see the
[article about arrays](/syntax/arrays).

For a more technical view what a parameter is and which types exist,
[see the dictionary entry for "parameter"](/dict/terms/parameter).

## Overview

Looking for a specific syntax you saw, without knowing the name?

- [Simple usage](#simple_usage)
  - `$PARAMETER`
  - `${PARAMETER}`
- [Indirection](#indirection)
  - `${!PARAMETER}`
- [Case modification](#case_modification)
  - `${PARAMETER^}`
  - `${PARAMETER^^}`
  - `${PARAMETER,}`
  - `${PARAMETER,,}`
  - `${PARAMETER~}`
  - `${PARAMETER~~}`
- [Variable name expansion](#variable_name_expansion)
  - `${!PREFIX*}`
  - `${!PREFIX@}`
- [Substring removal](#substring_removal) (also for **filename
  manipulation**!)
  - `${PARAMETER#PATTERN}`
  - `${PARAMETER##PATTERN}`
  - `${PARAMETER%PATTERN}`
  - `${PARAMETER%%PATTERN}`
- [Search and replace](#search_and_replace)
  - `${PARAMETER/PATTERN/STRING}`
  - `${PARAMETER//PATTERN/STRING}`
  - `${PARAMETER/PATTERN}`
  - `${PARAMETER//PATTERN}`
- [String length](#string_length)
  - `${#PARAMETER}`
- [Substring expansion](#substring_expansion)
  - `${PARAMETER:OFFSET}`
  - `${PARAMETER:OFFSET:LENGTH}`
- [Use a default value](#use_a_default_value)
  - `${PARAMETER:-WORD}`
  - `${PARAMETER-WORD}`
- [Assign a default value](#assign_a_default_value)
  - `${PARAMETER:=WORD}`
  - `${PARAMETER=WORD}`
- [Use an alternate value](#use_an_alternate_value)
  - `${PARAMETER:+WORD}`
  - `${PARAMETER+WORD}`
- [Display error if null or unset](#display_error_if_null_or_unset)
  - `${PARAMETER:?WORD}`
  - `${PARAMETER?WORD}`

## Simple usage

`$PARAMETER`

`${PARAMETER}`

The easiest form is to just use a parameter's name within braces. This
is identical to using `$FOO` like you see it everywhere, but has the
advantage that it can be immediately followed by characters that would
be interpreted as part of the parameter name otherwise. Compare these
two expressions (`WORD="car"` for example), where we want to print a
word with a trailing "s":

    echo "The plural of $WORD is most likely $WORDs"
    echo "The plural of $WORD is most likely ${WORD}s"

<u>Why does the first one fail?</u> It prints nothing, because a
parameter (variable) named "`WORDs`" is undefined and thus printed as ""
(*nothing*). Without using braces for parameter expansion, Bash will
interpret the sequence of all valid characters from the introducing
"`$`" up to the last valid character as name of the parameter. When
using braces you just force Bash to **only interpret the name inside
your braces**.

Also, please remember, that **parameter names are** (like nearly
everything in UNIX(r)) **case sensitive!**

The second form with the curly braces is also needed to access
positional parameters (arguments to a script) beyond `$9`:

    echo "Argument  1 is: $1"
    echo "Argument 10 is: ${10}"

### Simple usage: Arrays

See also the [article about general array syntax](/syntax/arrays)

For arrays you always need the braces. The arrays are expanded by
individual indexes or mass arguments. An individual index behaves like a
normal parameter, for the mass expansion, please read the article about
arrays linked above.

- \${array\[5\]}
- \${array\[\*\]}
- \${array\[@\]}

## Indirection

`${!PARAMETER}`

In some cases, like for example

    ${PARAMETER}

    ${PARAMETER:0:3}

you can instead use the form

    ${!PARAMETER}

to enter a level of indirection. The referenced parameter is not
`PARAMETER` itself, but the parameter whose name is stored as the value
of `PARAMETER`. If the parameter `PARAMETER` has the value "`TEMP`",
then `${!PARAMETER}` will expand to the value of the parameter named
`TEMP`:

    read -rep 'Which variable do you want to inspect? ' look_var

    printf 'The value of "%s" is: "%s"\n' "$look_var" "${!look_var}" 

Of course the indirection also works with special variables:

    # set some fake positional parameters
    set one two three four

    # get the LAST argument ("#" stores the number of arguments, so "!#" will reference the LAST argument)
    echo ${!#}

You can think of this mechanism as being roughly equivalent to taking
any parameter expansion that begins with the parameter name, and
substituting the `!PARAMETER` part with the value of PARAMETER.

    echo "${!var^^}"
    # ...is equivalent to
    eval 'echo "${'"$var"'^^}"'

It was an unfortunate design decision to use the `!` prefix for
indirection, as it introduces parsing ambiguity with other parameter
expansions that begin with `!`. Indirection is not possible in
combination with any parameter expansion whose modifier requires a
prefix to the parameter name. Specifically, indirection isn't possible
on the `${!var@}`, `${!var*}`, `${!var[@]}`, `${!var[*]}`, and `${#var}`
forms. This means the `!` prefix can't be used to retrieve the indices
of an array, the length of a string, or number of elements in an array
indirectly (see [syntax/arrays#indirection](syntax/arrays#indirection)
for workarounds). Additionally, the `!`-prefixed parameter expansion
conflicts with ksh-like shells which have the more powerful
"name-reference" form of indirection, where the exact same syntax is
used to expand to the name of the variable being referenced.

Indirect references to [array names](/syntax/arrays) are also possible
since the Bash 3 series (exact version unknown), but undocumented. See
[syntax/arrays#indirection](syntax/arrays#indirection) for details.

Chet has added an initial implementation of the ksh `nameref`
declaration command to the git devel branch. (`declare -n`, `local -n`,
etc, will be supported). This will finally address many issues around
passing and returning complex datatypes to/from functions.

## Case modification

`${PARAMETER^}`

`${PARAMETER^^}`

`${PARAMETER,}`

`${PARAMETER,,}`

`${PARAMETER~}`

`${PARAMETER~~}`

These expansion operators modify the case of the letters in the expanded
text.

The `^` operator modifies the first character to uppercase, the `,`
operator to lowercase. When using the double-form (`^^` and `,,`), all
characters are converted.

\<wrap center round info 60%\>

The (**currently undocumented**) operators `~` and `~~` reverse the case
of the given text (in `PARAMETER`).`~` reverses the case of first letter
of words in the variable while `~~` reverses case for all. Thanks to
`Bushmills` and `geirha` on the Freenode IRC channel for this finding.

\</wrap\>

<u>**Example: Rename all `*.txt` filenames to lowercase**</u>

    for file in *.txt; do
      mv "$file" "${file,,}"
    done

<u>**Note:**</u> Case modification is a handy feature you can apply to a
name or a title. Or is it? Case modification was an important aspect of
the Bash 4 release. Bash version 4, RC1 would perform word splitting,
and then case modification, resulting in title case (where every word is
capitalized). It was decided to apply case modification to values, not
words, for the Bash 4 release. Thanks Chet.

### Case modification: Arrays

Case modification can be used to create the proper capitalization for
names or titles. Just assign it to an array:

`declare -a title=(my hello world john smith)`

For [array](/syntax/arrays) expansion, the case modification applies to
**every expanded element, no matter if you expand an individual index or
mass-expand** the whole array using `@` or `*` subscripts. Some
examples:

Assume: `array=(This is some Text)`

- `echo "${array[@],}"`
  - =\> `this is some text`
- `echo "${array[@],,}"`
  - =\> `this is some text`
- `echo "${array[@]^}"`
  - =\> `This Is Some Text`
- `echo "${array[@]^^}"`
  - =\> `THIS IS SOME TEXT`

<!-- -->

     * ''echo "${array[2]^^}"''
       * => ''SOME''

## Variable name expansion

`${!PREFIX*}`

`${!PREFIX@}`

This expands to a list of all set **variable names** beginning with the
string `PREFIX`. The elements of the list are separated by the first
character in the `IFS`-variable (\<space\> by default).

This will show all defined variable names (not values!) beginning with
"BASH":

    $ echo ${!BASH*}
    BASH BASH_ARGC BASH_ARGV BASH_COMMAND BASH_LINENO BASH_SOURCE BASH_SUBSHELL BASH_VERSINFO BASH_VERSION

This list will also include [array names](/syntax/arrays).

## Substring removal

`${PARAMETER#PATTERN}`

`${PARAMETER##PATTERN}`

`${PARAMETER%PATTERN}`

`${PARAMETER%%PATTERN}`

This one can **expand only a part** of a parameter's value, **given a
pattern to describe what to remove** from the string. The pattern is
interpreted just like a pattern to describe a filename to match
(globbing). See [Pattern matching](/syntax/pattern) for more.

Example string (*just a quote from a big man*):

    MYSTRING="Be liberal in what you accept, and conservative in what you send"

### From the beginning

`${PARAMETER#PATTERN}` and `${PARAMETER##PATTERN}`

This form is to remove the described [pattern](/syntax/pattern) trying
to **match it from the beginning of the string**. The operator "`#`"
will try to remove the shortest text matching the pattern, while "`##`"
tries to do it with the longest text matching. Look at the following
examples to get the idea (matched text ~~marked striked~~, remember it
will be removed!):

| Syntax             | Result                                                               |
|--------------------|----------------------------------------------------------------------|
| `${MYSTRING#*in}`  | ~~Be liberal in~~ what you accept, and conservative in what you send |
| `${MYSTRING##*in}` | ~~Be liberal in what you accept, and conservative in~~ what you send |

### From the end

`${PARAMETER%PATTERN}` and `${PARAMETER%%PATTERN}`

In the second form everything will be the same, except that Bash now
tries to match the pattern from the end of the string:

| Syntax             | Result                                                               |
|:-------------------|:---------------------------------------------------------------------|
| `${MYSTRING%in*}`  | Be liberal in what you accept, and conservative ~~in what you send~~ |
| `${MYSTRING%%in*}` | Be liberal ~~in what you accept, and conservative in what you send~~ |

The second form nullifies variables that begin with `in`, by working
from the end.

### Common use

<u>**How the heck does that help to make my life easier?**</u>

Well, maybe the most common use for it is to **extract parts of a
filename**. Just look at the following list with examples:

- **Get name without extension**
  - `${FILENAME%.*}`
  - =\> `bash_hackers.txt`
- **Get extension**
  - `${FILENAME##*.}`
  - =\> `bash_hackers.txt`
- **Get directory name**
  - `${PATHNAME%/*}`
  - =\> `/home/bash/bash_hackers.txt`
- **Get filename**
  - `${PATHNAME##*/}`
  - =\> `/home/bash/bash_hackers.txt`

These are the syntaxes for filenames with a single extension. Depending
on your needs, you might need to adjust shortest/longest match.

### Substring removal: Arrays

As for most parameter expansion features, working on
[arrays](/syntax/arrays) **will handle each expanded element**, for
individual expansion and also for mass expansion.

Simple example, removing a trailing `is` from all array elements (on
expansion):

Assume: `array=(This is a text)`

- `echo "${array[@]%is}"`
  - =\> `Th a text`
  - (it was: `This is a text`)

All other variants of this expansion behave the same.

## Search and replace

`${PARAMETER/PATTERN/STRING}`

`${PARAMETER//PATTERN/STRING}`

`${PARAMETER/PATTERN}`

`${PARAMETER//PATTERN}`

This one can substitute (*replace*) a substring [matched by a
pattern](/syntax/pattern), on expansion time. The matched substring will
be entirely removed and the given string will be inserted. Again some
example string for the tests:

    MYSTRING="Be liberal in what you accept, and conservative in what you send"

The two main forms only differ in **the number of slashes** after the
parameter name: `${PARAMETER/PATTERN/STRING}` and
`${PARAMETER//PATTERN/STRING}`

The first one (*one slash*) is to only substitute **the first
occurrence** of the given pattern, the second one (*two slashes*) is to
substitute **all occurrences** of the pattern.

First, let's try to say "happy" instead of "conservative" in our example
string:

    ${MYSTRING//conservative/happy}

=\>
`Be liberal in what you accept, and conservativehappy in what you send`

Since there is only one "conservative" in that example, it really
doesn't matter which of the two forms we use.

Let's play with the word "in", I don't know if it makes any sense, but
let's substitute it with "by".

<u>**First form: Substitute first occurrence**</u>

    ${MYSTRING/in/by}

=\> `Be liberal inby what you accept, and conservative in what you send`

<u>**Second form: Substitute all occurrences**</u>

    ${MYSTRING//in/by}

=\>
`Be liberal inby what you accept, and conservative inby what you send`

<u>**Anchoring**</u> Additionally you can "anchor" an expression: A `#`
(hashmark) will indicate that your expression is matched against the
beginning portion of the string, a `%` (percent-sign) will do it for the
end portion.

    MYSTRING=xxxxxxxxxx
    echo ${MYSTRING/#x/y}  # RESULT: yxxxxxxxxx
    echo ${MYSTRING/%x/y}  # RESULT: xxxxxxxxxy

If the replacement part is completely omitted, the matches are replaced
by the nullstring, i.e., they are removed. This is equivalent to
specifying an empty replacement:

    echo ${MYSTRING//conservative/}
    # is equivalent to
    echo ${MYSTRING//conservative}

### Search and replace: Arrays

This parameter expansion type applied to [arrays](/syntax/arrays)
**applies to all expanded elements**, no matter if an individual element
is expanded, or all elements using the mass expansion syntaxes.

A simple example, changing the (lowercase) letter `t` to `d`:

Assume: `array=(This is a text)`

- `echo "${array[@]/t/d}"`
  - =\> `This is a dext`
- `echo "${array[@]//t/d}"`
  - =\> `This is a dexd`

## String length

`${#PARAMETER}`

When you use this form, the length of the parameter's value is expanded.
Again, a quote from a big man, to have a test text:

    MYSTRING="Be liberal in what you accept, and conservative in what you send"

Using echo `${#MYSTRING}`...

=\> `64`

The length is reported in characters, not in bytes. Depending on your
environment this may not always be the same (multibyte-characters, like
in UTF8 encoding).

There's not much to say about it, mh?

### (String) length: Arrays

For [arrays](/syntax/arrays), this expansion type has two meanings:

- For **individual** elements, it reports the string length of the
  element (as for every "normal" parameter)
- For the **mass subscripts** `@` and `*` it reports the number of set
  elements in the array

Example:

Assume: `array=(This is a text)`

- `echo ${#array[1]}`
  - =\> 2 (the word "is" has a length of 2)
- `echo ${#array[@]}`
  - =\> 4 (the array contains 4 elements)

<u>**Attention:**</u> The number of used elements does not need to
conform to the highest index. Sparse arrays are possible in Bash, that
means you can have 4 elements, but with indexes 1, 7, 20, 31. **You
can't loop through such an array with a counter loop based on the number
of elements!**

## Substring expansion

`${PARAMETER:OFFSET}`

`${PARAMETER:OFFSET:LENGTH}`

This one can expand only a **part** of a parameter's value, given a
**position to start** and maybe a **length**. If `LENGTH` is omitted,
the parameter will be expanded up to the end of the string. If `LENGTH`
is negative, it's taken as a second offset into the string, counting
from the end of the string.

`OFFSET` and `LENGTH` can be **any** [arithmetic
expression](/syntax/arith_expr). **Take care:** The `OFFSET` starts at
0, not at 1!

Example string (a quote from a big man):
`MYSTRING="Be liberal in what you accept, and conservative in what you send"`

### Using only Offset

In the first form, the expansion is used without a length value, note
that the offset 0 is the first character:

    echo ${MYSTRING:35}

=\>
`<del>Be liberal in what you accept, and </del>conservative in what you send`

### Using Offset and Length

In the second form we also give a length value:

    echo ${MYSTRING:35:12}

=\>
`<del>Be liberal in what you accept, and </del>conservative<del> in what you send</del>`

### Negative Offset Value

If the given offset is negative, it's counted from the end of the
string, i.e. an offset of -1 is the last character. In that case, the
length still counts forward, of course. One special thing is to do when
using a negative offset: You need to separate the (negative) number from
the colon:

    ${MYSTRING: -10:5}
    ${MYSTRING:(-10):5}

Why? Because it's interpreted as the parameter expansion syntax to [use
a default value](/syntax/pe#use_a_default_value).

### Negative Length Value

If the `LENGTH` value is negative, it's used as offset from the end of
the string. The expansion happens from the first to the second offset
then:

    echo "${MYSTRING:11:-17}"

=\>
`<del>Be liberal </del>in what you accept, and conservative<del> in what you send</del>`

This works since Bash 4.2-alpha, see also
[bashchanges](/scripting/bashchanges).

### Substring/Element expansion: Arrays

For [arrays](/syntax/arrays), this expansion type has again 2 meanings:

- For **individual** elements, it expands to the specified substring (as
  for every “normal” parameter)
- For the **mass subscripts** `@` and `*` it mass-expands individual
  array elements denoted by the 2 numbers given (*starting element*,
  *number of elements*)

Example:

Assume: `array=(This is a text)`

- `echo ${array[0]:2:2}`
  - =\> `is` (the "is" in "This", array element 0)
- `echo ${array[@]:1:2}`
  - =\> `is a` (from element 1 inclusive, 2 elements are expanded, i.e.
    element 1 and 2)

## Use a default value

`${PARAMETER:-WORD}`

`${PARAMETER-WORD}`

If the parameter `PARAMETER` is unset (never was defined) or null
(empty), this one expands to `WORD`, otherwise it expands to the value
of `PARAMETER`, as if it just was `${PARAMETER}`. If you omit the `:`
(colon), like shown in the second form, the default value is only used
when the parameter was **unset**, not when it was empty.

    echo "Your home directory is: ${HOME:-/home/$USER}."
    echo "${HOME:-/home/$USER} will be used to store your personal data."

If `HOME` is unset or empty, everytime you want to print something
useful, you need to put that parameter syntax in.

    #!/bin/bash

    read -p "Enter your gender (just press ENTER to not tell us): " GENDER
    echo "Your gender is ${GENDER:-a secret}."

It will print "Your gender is a secret." when you don't enter the
gender. Note that the default value is **used on expansion time**, it is
**not assigned to the parameter**.

### Use a default value: Arrays

For [arrays](/syntax/arrays), the behaviour is very similar. Again, you
have to make a difference between expanding an individual element by a
given index and mass-expanding the array using the `@` and `*`
subscripts.

- For individual elements, it's the very same: If the expanded element
  is `NULL` or unset (watch the `:-` and `-` variants), the default text
  is expanded
- For mass-expansion syntax, the default text is expanded if the array
  - contains no element or is unset (the `:-` and `-` variants mean the
    **same** here)
  - contains only elements that are the nullstring (the `:-` variant)

In other words: The basic meaning of this expansion type is applied as
consistent as possible to arrays.

Example code (please try the example cases yourself):


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
    array=("")

    echo ${array[@]:-This array is NULL or unset}
    echo ${array[@]-This array is NULL or unset}


    ### CASE 4: An array with only two elements, a nullstring and a normal word
    array=("" word)

    echo ${array[@]:-This array is NULL or unset}
    echo ${array[@]-This array is NULL or unset}

## Assign a default value

`${PARAMETER:=WORD}`

`${PARAMETER=WORD}`

This one works like the [using default
values](/syntax/pe#use_a_default_value), but the default text you give
is not only expanded, but also **assigned** to the parameter, if it was
unset or null. Equivalent to using a default value, when you omit the
`:` (colon), as shown in the second form, the default value will only be
assigned when the parameter was **unset**.

    echo "Your home directory is: ${HOME:=/home/$USER}."
    echo "$HOME will be used to store your personal data."

After the first expansion here (`${HOME:=/home/$USER}`), `HOME` is set
and usable.

Let's change our code example from above:

    #!/bin/bash

    read -p "Enter your gender (just press ENTER to not tell us): " GENDER
    echo "Your gender is ${GENDER:=a secret}."
    echo "Ah, in case you forgot, your gender is really: $GENDER"

### Assign a default value: Arrays

For [arrays](/syntax/arrays) this expansion type is limited. For an
individual index, it behaves like for a "normal" parameter, the default
value is assigned to this one element. The mass-expansion subscripts `@`
and `*` **can not be used here** because it's not possible to assign to
them!

## Use an alternate value

`${PARAMETER:+WORD}`

`${PARAMETER+WORD}`

This form expands to nothing if the parameter is unset or empty. If it
is set, it does not expand to the parameter's value, **but to some text
you can specify**:

    echo "The Java application was installed and can be started.${JAVAPATH:+ NOTE: JAVAPATH seems to be set}"

The above code will simply add a warning if `JAVAPATH` is set (because
it could influence the startup behaviour of that imaginary application).

Some more unrealistic example... Ask for some flags (for whatever
reason), and then, if they were set, print a warning and also print the
flags:

    #!/bin/bash

    read -p "If you want to use special flags, enter them now: " SPECIAL_FLAGS
    echo "The installation of the application is finished${SPECIAL_FLAGS:+ (NOTE: there are special flags set: $SPECIAL_FLAGS)}."

If you omit the colon, as shown in the second form
(`${PARAMETER+WORD}`), the alternate value will be used if the parameter
is set (and it can be empty)! You can use it, for example, to complain
if variables you need (and that can be empty) are undefined:

    # test that with the three stages:

    # unset foo
    # foo=""
    # foo="something"

    if [[ ${foo+isset} = isset ]]; then
      echo "foo is set..."
    else
      echo "foo is not set..."
    fi

### Use an alternate value: Arrays

Similar to the cases for [arrays](/syntax/arrays) to expand to a default
value, this expansion behaves like for a "normal" parameter when using
individual array elements by index, but reacts differently when using
the mass-expansion subscripts `@` and `*`:

      * For individual elements, it's the very same: If the expanded element is **not** NULL or unset (watch the :+ and + variants), the alternate text is expanded
      * For mass-expansion syntax, the alternate text is expanded if the array
        * contains elements where min. one element is **not** a nullstring (the :+ and + variants mean the same here)
        * contains **only** elements that are **not** the nullstring (the :+ variant)

For some cases to play with, please see the code examples in the
[description for using a default value](#use_a_default_valuearrays).

## Display error if null or unset

`${PARAMETER:?WORD}`

`${PARAMETER?WORD}`

If the parameter `PARAMETER` is set/non-null, this form will simply
expand it. Otherwise, the expansion of `WORD` will be used as appendix
for an error message:

    $ echo "The unset parameter is: ${p_unset?not set}"
    bash: p_unset: not set

After printing this message,

- an interactive shell has `$?` to a non-zero value
- a non-interactive shell exits with a non-zero exit code

The meaning of the colon (`:`) is the same as for the other parameter
expansion syntaxes: It specifies if

- only unset or
- unset and empty parameters

are taken into account.

## Code examples

### Substring removal

Removing the first 6 characters from a text string:

    STRING="Hello world"

    # only print 'Hello'
    echo "${STRING%??????}"

    # only print 'world'
    echo "${STRING#??????}"

    # store it into the same variable
    STRING=${STRING#??????}

## Bugs and Portability considerations

- **Fixed in 4.2.36**
  ([patch](ftp://ftp.cwru.edu/pub/bash/bash-4.2-patches/bash42-036)).
  Bash doesn't follow either POSIX or its own documentation when
  expanding either a quoted `"$@"` or `"${arr[@]}"` with an adjacent
  expansion. `"$@$x"` expands in the same way as `"$*$x"` - i.e. all
  parameters plus the adjacent expansion are concatenated into a single
  argument. As a workaround, each expansion needs to be quoted
  separately. Unfortunately, this bug took a very long time to
  notice.`~ $ set -- a b c; x=foo; printf '<%s> ' "$@$x" "$*""$x" "$@""$x"
  <a b cfoo> <a b cfoo> <a> <b> <cfoo>
  `

<!-- -->

- Almost all shells disagree about the treatment of an unquoted `$@`,
  `${arr[@]}`, `$*`, and `${arr[*]}` when
  [IFS](http://mywiki.wooledge.org/IFS) is set to null. POSIX is unclear
  about the expected behavior. A null IFS causes both [word
  splitting](/syntax/expansion/wordsplit) and [pathname
  expansion](/syntax/expansion/globs) to behave randomly. Since there
  are few good reasons to leave `IFS` set to null for more than the
  duration of a command or two, and even fewer to expand `$@` and `$*`
  unquoted, this should be a rare issue. **Always quote
  them**!`touch x 'y z'
  for sh in bb {{d,b}a,{m,}k,z}sh; do
      echo "$sh"
      "$sh" -s a 'b c' d \* </dev/fd/0
  done <<\EOF
  ${ZSH_VERSION+:} false && emulate sh
  IFS=
  printf '<%s> ' $*
  echo
  printf "<%s> " $@
  echo
  EOF
  ``bb
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
  `When `IFS` is set to a non-null value, or unset, all shells behave
  the same - first expanding into separate args, then applying pathname
  expansion and word-splitting to the results, except for zsh, which
  doesn't do pathname expansion in its default mode.

<!-- -->

- Additionally, shells disagree about various wordsplitting behaviors,
  the behavior of inserting delimiter characters from IFS in `$*`, and
  the way adjacent arguments are concatenated, when IFS is modified in
  the middle of expansion through
  side-effects.`for sh in bb {{d,b}a,po,{m,}k,z}sh; do
      printf '%-4s: ' "$sh"
      "$sh" </dev/fd/0
  done <<\EOF
  ${ZSH_VERSION+:} false && emulate sh
  set -f -- a b c
  unset -v IFS
  printf '<%s> ' ${*}${IFS=}${*}${IFS:=-}"${*}"
  echo
  EOF
  ``bb  : <a b cabc> <a-b-c>
  dash: <a b cabc> <a-b-c>
  bash: <a> <b> <ca> <b> <c-a b c>
  posh: <a> <b> <ca b c> <a-b-c>
  mksh: <a> <b> <ca b c> <a-b-c>
  ksh : <a> <b> <ca> <b> <c> <a b c>
  zsh : <a> <b> <ca> <b> <c> <a-b-c>
  `ksh93 and mksh can additionally achieve this side effect (and others)
  via the `${ cmds;}` expansion. I haven't yet tested every possible
  side-effect that can affect expansion halfway through expansion that
  way.

<!-- -->

- As previously mentioned, the Bash form of indirection by prefixing a
  parameter expansion with a `!` conflicts with the same syntax used by
  mksh, zsh, and ksh93 for a different purpose. Bash will "slightly"
  modify this expansion in the next version with the addition of
  namerefs.

<!-- -->

- Bash (and most other shells) don't allow .'s in identifiers. In ksh93,
  dots in variable names are used to reference methods (i.e. "Discipline
  Functions"), attributes, special shell variables, and to define the
  "real value" of an instance of a class.

<!-- -->

- In ksh93, the `_` parameter has even more uses. It is used in the same
  way as `self` in some object-oriented languages; as a placeholder for
  some data local to a class; and also as the mechanism for class
  inheritance. In most other contexts, `_` is compatible with Bash.

<!-- -->

- Bash only evaluates the subscripts of the slice expansion (`${x:y:z}`)
  if the parameter is set (for both nested expansions and arithmetic).
  For ranges, Bash evaluates as little as possible, i.e., if the first
  part is out of range, the second won't be evaluated. ksh93 and mksh
  always evaluate the subscript parts even if the parameter is unset.
  ` $ bash -c 'n="y[\$(printf yo >&2)1]" m="y[\$(printf jo >&2)1]"; x=(); echo "${x[@]:n,6:m}"' # No output
   $ bash -c 'n="y[\$(printf yo >&2)1]" m="y[\$(printf jo >&2)1]"; x=([5]=hi); echo "${x[@]:n,6:m}"'
  yo
   $ bash -c 'n="y[\$(printf yo >&2)1]" m="y[\$(printf jo >&2)1]"; x=([6]=hi); echo "${x[@]:n,6:m}"'
  yojo
   $ bash -c 'n="y[\$(printf yo >&2)1]" m="y[\$(printf jo >&2)1]"; x=12345; echo "${x:n,5:m}"'
  yojo
   $ bash -c 'n="y[\$(printf yo >&2)1]" m="y[\$(printf jo >&2)1]"; x=12345; echo "${x:n,6:m}"'
  yo
  `

### Quote Nesting

- In most shells, when dealing with an "alternate" parameter expansion
  that expands to multiple words, and nesting such expansions, not all
  combinations of nested quoting are possible.

<!-- -->

    # Bash
     $ typeset -a a=(meh bleh blerg) b
     $ IFS=e
     $ printf "<%s> " "${b[@]-"${a[@]}" "${a[@]}"}"; echo # The entire PE is quoted so Bash considers the inner quotes redundant.
    <meh> <bleh> <blerg meh> <bleh> <blerg>
     $ printf "<%s> " "${b[@]-${a[@]} ${a[@]}}"; echo # The outer quotes cause the inner expansions to be considered quoted.
    <meh> <bleh> <blerg meh> <bleh> <blerg>
     $ b=(meep beep)
     $ printf "<%s> " "${b[@]-"${a[@]}" "${a[@]}"}" "${b[@]-${a[@]} ${a[@]}}"; echo # Again no surprises. Outer quotes quote everything recursively.
    <meep> <beep> <meep> <beep>

Now lets see what can happen if we leave the outside unquoted.

    # Bash
     $ typeset -a a=(meh bleh blerg) b
     $ IFS=e
     $ printf "<%s> " ${b[@]-"${a[@]}" "${a[@]}"}; echo # Inner quotes make inner expansions quoted.
    <meh> <bleh> <blerg meh> <bleh> <blerg>
     $ printf "<%s> " ${b[@]-${a[@]} ${a[@]}}; echo' # No quotes at all wordsplits / globs, like you'd expect.
    <m> <h> <bl> <h> <bl> <rg m> <h> <bl> <h> <bl> <rg>

This all might be intuitive, and is the most common implementation, but
this design sucks for a number of reasons. For one, it means Bash makes
it absolutely impossible to expand any part of the inner region
*unquoted* while leaving the outer region quoted. Quoting the outer
forces quoting of the inner regions recursively (except nested command
substitutions of course). Word-splitting is necessary to split words of
the inner region, which cannot be done together with outer quoting.
Consider the following (only slightly far-fetched) code:

    # Bash (non-working example)

    unset -v IFS # make sure we have a default IFS

    if some crap; then
        typeset -a someCmd=(myCmd arg1 'arg2 yay!' 'third*arg*' 4)
    fi

    someOtherCmd=mycommand
    typeset -a otherArgs=(arg3 arg4)

    # What do you think the programmer expected to happen here?
    # What do you think will actually happen...

    "${someCmd[@]-"$someOtherCmd" arg2 "${otherArgs[@]}"}" arg5

This final line is perhaps not the most obvious, but I've run into cases
were this type of logic can be desirable and realistic. We can deduce
what was intended:

- If `someCmd` is set, then the resulting expansion should run the
  command: `"myCmd" "arg1" "arg2 yay!" "third*arg*" "4" "arg5"`
- Otherwise, if `someCmd` is not set, expand `$someOtherCmd` and the
  inner args, to run a different command:
  `"mycommand" "arg2" "arg3" "arg4" "arg5"`.

Unfortunately, it is impossible to get the intended result in Bash (and
most other shells) without taking a considerably different approach. The
only way to split the literal inner parts is through word-splitting,
which requires that the PE be unquoted. But, the only way to expand the
outer expansion correctly without word-splitting or globbing is to quote
it. Bash will actually expand the command as one of these:

    # The quoted PE produces a correct result here...
     $ bash -c 'typeset -a someCmd=(myCmd arg1 "arg2 yay!" "third*arg*" 4); printf "<%s> " "${someCmd[@]-"$someOtherCmd" arg2 "${otherArgs[@]}"}" arg5; echo'
    <myCmd> <arg1> <arg2 yay!> <third*arg*> <4> <arg5>

    # ...but in the opposite case the first 3 arguments are glued together. There are no workarounds.
     $ bash -c 'typeset -a otherArgs=(arg3 arg4); someOtherCmd=mycommand; printf "<%s> " "${someCmd[@]-"$someOtherCmd" arg2 "${otherArgs[@]}"}" arg5; echo'
    <mycommand arg2 arg3> <arg4> <arg5>

    # UNLESS! we unquote the outer expansion allowing the inner quotes to
    # affect the necessary parts while allowing word-splitting to split the literals:
     $ bash -c 'typeset -a otherArgs=(arg3 arg4); someOtherCmd=mycommand; printf "<%s> " ${someCmd[@]-"$someOtherCmd" arg2 "${otherArgs[@]}"} arg5; echo'
    <mycommand> <arg2> <arg3> <arg4> <arg5>

    # Success!!!
     $ bash -c 'typeset -a someCmd=(myCmd arg1 "arg2 yay!" "third*arg*" 4); printf "<%s> " ${someCmd[@]-"$someOtherCmd" arg2 "${otherArgs[@]}"} arg5; echo'
    <myCmd> <arg1> <arg2> <yay!> <third*arg*> <4> <arg5>

    # ...Ah f^^k. (again, no workaround possible.)

#### The ksh93 exception

To the best of my knowledge, ksh93 is the only shell that acts
differently. Rather than forcing nested expansions into quoting, a quote
at the beginning and end of the nested region will cause the quote state
to reverse itself within the nested part. I have no idea whether it's an
intentional or documented effect, but it does solve the problem and
consequently adds a lot of potential power to these expansions.

All we need to do is add two extra double-quotes:

    # ksh93 passing the two failed tests from above:

     $ ksh -c 'otherArgs=(arg3 arg4); someOtherCmd="mycommand"; printf "<%s> " "${someCmd[@]-""$someOtherCmd" arg2 "${otherArgs[@]}""}" arg5; echo'
    <mycommand> <arg2> <arg3> <arg4> <arg5>

     $ ksh -c 'typeset -a someCmd=(myCmd arg1 "arg2 yay!" "third*arg*" 4); printf "<%s> " "${someCmd[@]-""$someOtherCmd" arg2 "${otherArgs[@]}""}" arg5; echo'
    <myCmd> <arg1> <arg2 yay!> <third*arg*> <4> <arg5>

This can be used to control the quote state of any part of any expansion
to an arbitrary depth. Sadly, it is the only shell that does this and
the difference may introduce a possible compatibility problem.

## See also

- Internal: [Introduction to expansion and
  substitution](/syntax/expansion/intro)
- Internal: [Arrays](/syntax/arrays)
