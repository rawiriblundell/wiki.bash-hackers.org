# Debugging a script

![](keywords>bash shell scripting bug debug debugging)

These few lines are not intended as a full-fledged debugging tutorial,
but as hints and comments about debugging a Bash script.

## Use a unique name for your script

Do **not** name your script `test`, for example! *Why?* `test` is the
name of a UNIX(r)-command, and <u>most likely built into your shell</u>
(it's a built-in in Bash) - so you won't be able to run a script with
the name `test` in a normal way.

**Don't laugh!** This is a classic mistake :-)

## Read the error messages

Many people come into IRC and ask something like *"Why does my script
fail? I get an error!"*. And when you ask them what the error message
is, they don't even know. Beautiful.

Reading and interpreting error messages is 50% of your job as debugger!
Error messages actually **mean** something. At the very least, they can
give you hints as to where to start debugging. **READ YOUR ERROR
MESSAGES!**

You may ask yourself why is this mentioned as debugging tip? Well,
<u>you would be surprised how many shell users ignore the text of error
messages!</u> When I find some time, I'll paste 2 or 3 IRC log-snips
here, just to show you that annoying fact.

## Use a good editor

Your choice of editor is a matter of personal preference, but one with
**Bash syntax highlighting** is highly recommended! Syntax highlighting
helps you see (you guessed it) syntax errors, such as unclosed quotes
and braces, typos, etc.

From my personal experience, I can suggest `vim` or `GNU emacs`.

## Write logfiles

For more complex scripts, it's useful to write to a log file, or to the
system log. Nobody can debug your script without knowing what actually
happened and what went wrong.

An available syslog interface is `logger` ([online
manpage](http://unixhelp.ed.ac.uk/CGI/man-cgi?logger+1)).

## Inject debugging code

Insert **echos** everywhere you can, and print to `stderr`:

    echo &quot;DEBUG: current i=$i&quot; >&2

If you read input from **anywhere**, such as a file or [command
substitution](/syntax/expansion/cmdsubst), print the debug output with
literal quotes, to see leading and trailing spaces!

    pid=$(< fooservice.pid)
    echo &quot;DEBUG: read from file: pid=\&quot;$pid\&quot;&quot; >&2

Bash's [printf](/commands/builtin/printf) command has the `%q` format,
which is handy for verifying whether strings are what they appear to be.

    foo=$(< inputfile)
    printf &quot;DEBUG: foo is |%q|\n&quot; &quot;$foo&quot; >&2
    # exposes whitespace (such as CRs, see below) and non-printing characters

## Use shell debug output

There are two useful debug outputs for that task (both are written to
`stderr`):

- `set -v` mode (`set -o verbose`)
  - print commands to be executed to `stderr` as if they were read from
    input (script file or keyboard)
  - print everything **before** any ([substitution and
    expansion](/syntax/expansion/intro), ...) is applied
- `set -x` mode (`set -o xtrace`)
  - print everything as if it were executed, after [substitution and
    expansion](/syntax/expansion/intro) is applied
  - indicate the depth-level of the subshell (by default by prefixing a
    `+` (plus) sign to the displayed command)
  - indicate the recognized words after [word
    splitting](/syntax/expansion/wordsplit) by marking them like `'x y'`
  - in shell version 4.1, this debug output can be printed to a
    configurable file descriptor, rather than sdtout by setting the
    [BASH_XTRACEFD](/syntax/shellvars#BASH_XTRACEFD) variable.

**<u>Hint:</u>** These modes can be entered when calling Bash:

- from commandline: `bash -vx ./myscript`
- from shebang (OS dependant): `#!/bin/bash -vx`

### Simple example of how to interpret xtrace output

Here's a simple command (a string comparison using the [classic test
command](/commands/classictest)) executed while in `set -x` mode:

    set -x
    foo=&quot;bar baz&quot;
    [ $foo = test ]

That fails. Why? Let's see the `xtrace` output:

    + '[' bar baz = test ']'

And now you see that it's ("bar" and "baz") recognized as two separate
words (which you would have realized if you READ THE ERROR MESSAGES ;)
). Let's check it...

    # next try
    [ &quot;$foo&quot; = test ]

`xtrace` now gives

    + '[' 'bar baz' = test ']'
          ^       ^
        word markers!

### Making xtrace more useful

(by AnMaster)

`xtrace` output would be more useful if it contained source file and
line number. Add this assignment [PS4](/syntax/shellvars#PS4) at the
beginning of your script to enable the inclusion of that information:

    export PS4='+(${BASH_SOURCE}:${LINENO}): ${FUNCNAME[0]:+${FUNCNAME[0]}(): }'

**Be sure to use single quotes here!**

The output would look like this when you trace code *outside a
function*:

    +(somefile.bash:412): echo 'Hello world'

...and like this when you trace code *inside a function*:

    +(somefile.bash:412): myfunc(): echo 'Hello world'

That helps a lot when the script is long, or when the main script
sources many other files.

#### Set flag variables with descriptive words

If you test variables that flag the state of options, such as with
`if [[ -n $option ]];`, consider using descriptive words rather than
short codes, such as 0, 1, Y, N, because xtrace will show
`[[ -n word ]]` rather than `[[ -n 1 ]]` when the option is set.

## Debugging commands depending on a set variable

For general debugging purposes you can also define a function and a
variable to use:

    debugme() {
     [[ $script_debug = 1 ]] && &quot;$@&quot; || :
     # be sure to append || : or || true here or use return 0, since the return code
     # of this function should always be 0 to not influence anything else with an unwanted
     # &quot;false&quot; return code (for example the script's exit code if this function is used
     # as the very last command in the script)
    }

This function does nothing when `script_debug` is unset or empty, but it
executes the given parameters as commands when `script_debug` is set.
Use it like this:

    script_debug=1
    # to turn it off, set script_debug=0

    debugme logger &quot;Sorting the database&quot;
    database_sort
    debugme logger &quot;Finished sorting the database, exit code $?&quot;

Of course this can be used to execute something other than echo during
debugging:

    debugme set -x
    # ... some code ...
    debugme set +x

## Dry-run STDIN driven commands

Imagine you have a script that runs FTP commands using the standard FTP
client:

``` bash
ftp user@host <<FTP
cd /data
get current.log
dele current.log
FTP
```

A method to dry-run this with debug output is:

``` bash
if [[ $DRY_RUN = yes ]]; then
  sed 's/^/DRY_RUN FTP: /'
else
  ftp user@host
fi <<FTP
cd /data
get current.log
dele current.log
FTP
```

This can be wrapped in a shell function for more readable code.

## Common error messages

### Unexpected end of file

    script.sh: line 100: syntax error: unexpected end of file

Usually indicates exactly what it says: An unexpected end of file. It's
unexpected because Bash waits for the closing of a [compound
command](/syntax/ccmd/intro):

- did you close your `do` with a `done`?
- did you close your `if` with a `fi`?
- did you close your `case` with a `esac`?
- did you close your `{` with a `}`?
- did you close your `(` with a `)`?

**<u>Note:</u>** It seems that here-documents (tested on versions
`1.14.7`, `2.05b`, `3.1.17` and `4.0`) are correctly terminated when
there is an EOF before the end-of-here-document tag (see
[redirection](/syntax/redirection)). The reason is unknown, but it seems
to be deliberate. Bash 4.0 added an extra message for this:
`` warning: here-document at line <N> delimited by end-of-file (wanted `<MARKER>') ``

### Unexpected end of file while looking for matching ...

    script.sh: line 50: unexpected EOF while looking for matching `&quot;'
    script.sh: line 100: syntax error: unexpected end of file

This one indicates the double-quote opened in line 50 does not have a
matching closing quote.

These *unmatched errors* occur with:

- double-quote pairs
- single-quote pairs (also `$'string'`!)
- missing a closing `}` with [parameter expansion syntax](/syntax/pe)

### Too many arguments

    bash: test: too many arguments

You most likely forgot to quote a variable expansion somewhere. See the
example for `xtrace` output from above. External commands may display
such an error message though in our example, it was the **internal**
test-command that yielded the error.

### !": event not found

    $ echo &quot;Hello world!&quot;
    bash: !&quot;: event not found

This is not an error per se. It happens in interactive shells, when the
C-Shell-styled history expansion ("`!searchword`") is enabled. This is
the default. Disable it like this:

    set +H
    # or
    set +o histexpand

### syntax error near unexpected token \`('

When this happens during a script **function definition** or on the
commandline, e.g.

    $ foo () { echo &quot;Hello world&quot;; }
    bash: syntax error near unexpected token `('

you most likely have an alias defined with the same name as the function
(here: `foo`). Alias expansion happens before the real language
interpretion, thus the alias is expanded and makes your function
definition invalid.

## The CRLF issue

### What is the CRLF issue?

There's a big difference in the way that UNIX(r) and Microsoft(r) (and
possibly others) handle the **line endings** of plain text files. The
difference lies in the use of the CR (Carriage Return) and LF (Line
Feed) characters.

- MSDOS uses: `\r\n` (ASCII `CR` \#13 `^M`, ASCII LF \#10)
- UNIX(r) uses: `\n` (ASCII `LF` \#10)

Keep in mind your script is a **plain text file**, and the `CR`
character means nothing special to UNIX(r) - it is treated like any
other character. If it's printed to your terminal, a carriage return
will effectively place the cursor at the beginning of the *current*
line. This can cause much confusion and many headaches, since lines
containing CRs are not what they appear to be when printed. In summary,
CRs are a pain.

### How did a CR end up in my file?

Some possible sources of CRs:

- a DOS/Windows text editor
- a UNIX(r) text editor that is "too smart" when determining the file
  content type (and thinks "*it's a DOS text file*")
- a direct copy and paste from certain webpages (some pastebins are
  known for this)

### Why do CRs hurt?

CRs can be a nuisance in various ways. They are especially bad when
present in the shebang/interpreter specified with `#!` in the very first
line of a script. Consider the following script, written with a
Windows(r) text editor (`^M` is a symbolic representation of the `CR`
carriage return character!):

    #!/bin/bash^M
    ^M
    echo &quot;Hello world&quot;^M
    ...

Here's what happens because of the `#!/bin/bash^M` in our shebang:

- the file `/bin/bash^M` doesn't exist (hopefully)
- So Bash prints an error message which (depending on the terminal, the
  Bash version, or custom patches!) may or may not expose the problem.
- the script can't be executed

The error message can vary. If you're lucky, you'll get:

    bash: ./testing.sh: /bin/bash^M: bad interpreter: No such file or directory

which alerts you to the CR. But you may also get the following:

    : bad interpreter: No such file or directory

Why? Because when printed literally, the `^M` makes the cursor go back
to the beginning of the line. The whole error message is *printed*, but
you *see* only part of it!

\<note warning\> It's easy to imagine the `^M` is bad in other places
too. If you get weird and illogical messages from your script, rule out
the possibility that`^M` is involved. Find and eliminate it! \</note\>

### How can I find and eliminate them?

**To display** CRs (these are only a few examples)

- in VI/VIM: `:set list`
- with `cat(1)`: `cat -v FILE`

**To eliminate** them (only a few examples)

- blindly with `tr(1)`: `tr -d '\r' <FILE >FILE.new`
- controlled with `recode(1)`: `recode MSDOS..latin1 FILE`
- controlled with `dos2unix(1)`: `dos2unix FILE`

## See also

- [the set builtin command](/commands/builtin/set) (for `-v` and `-x`)

FIXME

- DEBUG trap
