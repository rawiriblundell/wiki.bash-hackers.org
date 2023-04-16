# Scripting with style

FIXME continue

These are some coding guidelines that helped me to read and understand
my own code over the years. They also will help to produce code that
will be a bit more robust than "if something breaks, I know how to fix
it".

This is not a bible, of course. But I have seen so much ugly and
terrible code (not only in shell) during all the years, that I'm 100%
convinced there needs to be *some* code layout and style. No matter
which one you use, use it throughout your code (at least don't change it
within the same shellscript file); don't change your code layout with
your mood.

Some good code layout helps you to read your own code after a while. And
of course it helps others to read the code.

## Indentation guidelines

Indentation is nothing that technically influences a script, it's only
for us humans.

I'm used to seeing/using indentation of *two space characters* (though
many may prefer 4 spaces, see below in the discussion section):

- it's easy and fast to type
- it's not a hard-tab that's displayed differently in different
  environments
- it's wide enough to give a visual break and small enough to not waste
  too much space on the line

Speaking of hard-tabs: Avoid them if possible. They only make trouble. I
can imagine one case where they're useful: Indenting
[here-documents](/syntax/redirection#here_documents).

### Breaking up lines

Whenever you need to break lines of long code, you should follow one of
these two rules:

<u>**Indention using command width:**</u>

    activate some_very_long_option \
             some_other_option

<u>**Indention using two spaces:**</u>

    activate some_very_long_option \
      some_other_option

Personally, with some exceptions, I prefer the first form because it
supports the visual impression of "these belong together".

### Breaking compound commands

[Compound commands](/syntax/ccmd/intro) form the structures that make a
shell script different from a stupid enumeration of commands. Usually
they contain a kind of "head" and a "body" that contains command lists.
This type of compound command is relatively easy to indent.

I'm used to (not all points apply to all compound commands, just pick
the basic idea):

- put the introducing keyword and the initial command list or parameters
  on one line ("head")
- put the "body-introducing" keyword on the same line
- the command list of the "body" on separate lines, indented by two
  spaces
- put the closing keyword on a separated line, indented like the initial
  introducing keyword

What?! Well, here again:

##### Symbolic

    HEAD_KEYWORD parameters; BODY_BEGIN
      BODY_COMMANDS
    BODY_END

##### if/then/elif/else

This construct is a bit special, because it has keywords (`elif`,
`else`) "in the middle". The visually appealing way is to indent them
like this:

    if ...; then
      ...
    elif ...; then
      ...
    else
      ...
    fi

##### for

    for f in /etc/*; do
      ...
    done

##### while/until

    while [[ $answer != [YyNn] ]]; do
      ...
    done

##### The case construct

The `case` construct might need a bit more discussion here, since its
structure is a bit more complex.

In general, every new "layer" gets a new indentation level:

    case $input in
      hello)
        echo &quot;You said hello&quot;
      ;;
      bye)
        echo &quot;You said bye&quot;
        if foo; then
          bar
        fi
      ;;
      *)
        echo &quot;You said something weird...&quot;
      ;;
    esac

Some notes:

- if not 100% needed, the optional left parenthesis on the pattern is
  not used
- the patterns (`hello)`) and the corresponding action terminator (`;;`)
  are indented at the same level
- the action command lists are indented one more level (and continue to
  have their own indentation, if needed)
- though optional, the very last action terminator is given

## Syntax and coding guidelines

### Cryptic constructs

Cryptic constructs, we all know them, we all love them. If they are not
100% needed, avoid them, since nobody except you may be able to decipher
them.

It's - just like in C - the middle ground between smart, efficient and
readable.

If you need to use a cryptic construct, include a comment that explains
what your "monster" does.

### Variable names

Since all reserved variables are `UPPERCASE`, the safest way is to only
use `lowercase` variable names. This is true for reading user input,
loop counting variables, etc., ... (in the example: `file`)

- prefer `lowercase` variables
- if you use `UPPERCASE` names, **do not use reserved variable names**
  (see
  [SUS](http://pubs.opengroup.org/onlinepubs/9699919799/basedefs/V1_chap08.html#tag_08)
  for an incomplete list)
- if you use `UPPERCASE` names, prepend the name with a unique prefix
  (`MY_` in the example below)

<!-- -->

    #!/bin/bash

    # the prefix 'MY_'
    MY_LOG_DIRECTORY=/var/adm/

    for file in &quot;$MY_LOG_DIRECTORY&quot;/*; do
      echo &quot;Found Logfile: $file&quot;
    done

### Variable initialization

As in C, it's always a good idea to initialize your variables, though,
the shell will initialize fresh variables itself (better: Unset
variables will generally behave like variables containing a null
string).

It's no problem to pass an **environment variable** to the script. If
you blindly assume that all variables you use for the first time are
**empty**, anybody can **inject** content into a variable by passing it
via the environment.

The solution is simple and effective: **Initialize them**

    my_input=&quot;&quot;
    my_array=()
    my_number=0

If you do that for every variable you use, then you also have some
in-code documentation for them.

### Parameter expansion

Unless you are really sure what you're doing, **quote every parameter
expansion**.

There are some cases where this isn't needed from a technical point of
view, e.g.

- inside `[[ ... ]]` (other than the RHS of the `==`, `!=`, and `=~`
  operators)
- the parameter (`WORD`) in `case $WORD in ....`
- variable assignment: `VAR=$WORD`

But quoting these is never a mistake. If you quote every parameter
expansion, you'll be safe.

If you need to parse a parameter as a list of words, you can't quote, of
course, e.g.

    list=&quot;one two three&quot;

    # you MUST NOT quote $list here
    for word in $list; do
      ...
    done

### Function names

Function names should be all `lowercase` and meaningful. The function
names should be human readable. A function named `f1` may be easy and
quick to write down, but for debugging and especially for other people,
it reveals nothing. Good names help document your code without using
extra comments.

**do not use command names for your functions**. e.g. naming a script or
function `test`, will collide with the UNIX `test` command.

Unless absolutely necessary, only use alphanumeric characters and the
underscore for function names. `/bin/ls` is a valid function name in
Bash, but is not a good idea.

### Command substitution

As noted in [the article about command
substitution](/syntax/expansion/cmdsubst), you should use the `$( ... )`
form.

If portability is a concern, use the backquoted form `` ` ... ` ``.

In any case, if other expansions and word splitting are not wanted, you
should quote the command substitution!

### Eval

Well, like Greg says: **"If eval is the answer, surely you are asking
the wrong question."**

Avoid it, unless absolutely neccesary:

- `eval` can be your neckshot
- there are most likely other ways to achieve what you want
- if possible, re-think the way your script works, if it seems you can't
  avoid `eval` with your current method
- if you really, really, have to use it, then take care, and be sure
  about what you're doing

## Basic structure

The basic structure of a script simply reads:

    #!SHEBANG

    CONFIGURATION_VARIABLES

    FUNCTION_DEFINITIONS

    MAIN_CODE

### The shebang

If possible (I know it's not always possible!), use [a
shebang](/dict/terms/shebang).

Be careful with `/bin/sh`: The argument that "on Linux `/bin/sh` is
Bash" **is a lie** (and technically irrelevant)

The shebang serves two purposes for me:

- it specifies the interpreter to be used when the script file is called
  directly: If you code for Bash, specify `bash`!
- it documents the desired interpreter (so: use `bash` when you write a
  Bash-script, use `sh` when you write a general Bourne/POSIX script,
  ...)

### Configuration variables

I call variables that are meant to be changed by the user "configuration
variables" here.

Make them easy to find (directly at the top of the script), give them
meaningful names and maybe a short comment. As noted above, use
`UPPERCASE` for them only when you're sure about what you're doing.
`lowercase` will be the safest.

### Function definitions

Unless there are reasons not to, all function definitions should be
declared before the main script code runs. This gives a far better
overview and ensures that all function names are known before they are
used.

Since a function isn't parsed before it is executed, you usually don't
have to ensure they're in a specific order.

The portable form of the function definition should be used, without the
`function` keyword (here using the [grouping compound
command](/syntax/ccmd/grouping_plain)):

    getargs() {
      ...
    }

Speaking about the command grouping in function definitions using
`{ ...; }`: If you don't have a good reason to use another compound
command directly, you should always use this one.

## Behaviour and robustness

### Fail early

**Fail early**, this sounds bad, but usually is good. Failing early
means to error out as early as possible when checks indicate an error or
unmet condition. Failing early means to error out **before** your script
begins its work in a potentially broken state.

### Availability of commands

If you use external commands that may not be present on the path, or not
installed, check for their availability, then tell the user they're
missing.

Example:

    my_needed_commands=&quot;sed awk lsof who&quot;

    missing_counter=0
    for needed_command in $my_needed_commands; do
      if ! hash &quot;$needed_command&quot; >/dev/null 2>&1; then
        printf &quot;Command not found in PATH: %s\n&quot; &quot;$needed_command&quot; >&2
        ((missing_counter++))
      fi
    done

    if ((missing_counter > 0)); then
      printf &quot;Minimum %d commands are missing in PATH, aborting\n&quot; &quot;$missing_counter&quot; >&2
      exit 1
    fi

### Exit meaningfully

The [exit code](/dict/terms/exit_status) is your only way to directly
communicate with the calling process without any special provisions.

If your script exits, provide a meaningful exit code. That minimally
means:

- `exit 0` (zero) if everything is okay
- `exit 1` - in general non-zero - if there was an error

This, **and only this**, will enable the calling component to check the
operation status of your script.

You know: **"One of the main causes of the fall of the Roman Empire was
that, lacking zero, they had no way to indicate successful termination
of their C programs."** *-- Robert Firth*

## Misc

### Output and appearance

- if the script is interactive, if it works for you and if you think
  this is a nice feature, you can try to [save the terminal content and
  restore it](/snipplets/screen_saverestore) after execution
- output clean and understandable screen messages
- if applicable, you can use colors or specific prefixes to tag error
  and warning messages
  - make it easy for the user to identify those messages
- write normal output to `STDOUT`. write error, warning and diagnostic
  messages to `STDERR`
  - enables message filtering
  - keeps the script from mixing output data with diagnostic, or error
    messages
  - if the script gives syntax help (`-?` or `-h` or `--help`
    arguments), it should go to `STDOUT`
- if applicable, write error/diagnostic messages to a logfile
  - avoids screen clutter
  - messages are available for diagnostic use

### Input

- never blindly assume anything. If you want the user to input a number,
  **check for numeric input, leading zeros**, etc. If you have specific
  format or content needs, **always validate the input!**

### Tooling
