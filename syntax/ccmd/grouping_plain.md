# Grouping commands

## Synopsis

    { <LIST>; }

    {
    <LIST>
    }

## Description

The [list](/syntax/basicgrammar#lists) `<LIST>` is simply executed in
the **current** shell environment. The list must be terminated with a
**newline** or **semicolon**. For parsing reasons, the curly braces must
be separated from `<LIST>` by a **semicolon** and **blanks** if they're
in the same line! [^1][^2]

This is known as a **group command**. The return status is the [exit
status (exit code)](/scripting/basics#exit_codes) of the list.

The input and output **filedescriptors** are cumulative:

    {
      echo &quot;PASSWD follows&quot;
      cat /etc/passwd
      echo
      echo &quot;GROUPS follows&quot;
      cat /etc/group
    } >output.txt

This compound command also usually is the body of a [function
definition](/syntax/basicgrammar#shell_function_definitions), though not
the only compound command that's valid there:

    print_help() {
      echo &quot;Options:&quot;
      echo &quot;-h           This help text&quot;
      echo &quot;-f FILE      Use config file FILE&quot;
      echo &quot;-u USER      Run as user USER&quot;
    }

## Examples

### A Try-Catch block

      try_catch() {
          { # Try-block:
              eval &quot;$@&quot;
          } ||
          { # Catch-block:
              echo &quot;An error occurred&quot;
              return -1
          }
      }

## Portability considerations

## See also

     * [[syntax:ccmd:grouping_subshell | grouping commands in a subshell]]

[^1]: Actually any properly terminated compound command will work
    without extra separator (also in some other shells), **example**:
    `{ while sleep 1; do echo ZzZzzZ; done }` is valid. But this is not
    documented, infact the documentation explicitly says that a
    semicolon or a newline must separate the enclosed list. -- thanks
    `geirha` at Freenode

[^2]: The main reason is the fact that in shell grammar, the curly
    braces are not control operators but reserved words -- TheBonsai
