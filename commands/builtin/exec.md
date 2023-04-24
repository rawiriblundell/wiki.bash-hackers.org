# The exec builtin command

## Synopsis

    exec [-a NAME] [-cl] [COMMAND] [ARG...] [REDIRECTION...]

## Description

The `exec` builtin command is used to

- **replace** the shell with a given program (executing it, **not as new
  process**)
- set redirections for the program to execute or for the current shell

If only redirections are given, the redirections affect the current
shell without executing any program.

### Options

| Option    | Description                                                                                                          |
|-----------|----------------------------------------------------------------------------------------------------------------------|
| `-a NAME` | Passes `NAME` as zeroth argument for the program to be executed                                                      |
| `-c`      | Execute the program with an empty (cleared) environment                                                              |
| `-l`      | Prepends a dash (`-`) to the zeroth argument of the program to be executed, similar to what the `login` program does |

### Exit status

- on redirection errors it returns 1, otherwise 0
- on exec failures:
  - a non-interactive shell terminates; if the [shell option
    execfail](/internals/shell_options.md#execfail) is set `exec` returns
    failure
  - in an interactive shell, `exec` returns failure

## Examples

### Wrapper around a program

``` bash
myprog=/bin/ls
echo "This is the wrapper script, it will exec $myprog"

# do some vodoo here, probably change the arguments etc.
# well, stuff a wrapper is there for

exec "$myprog" "$@"
```

### Open a file as input for the script

``` bash
# open it
exec 3< input.txt

# for example: read one line from the file(-descriptor)
read -u 3 LINE
# or
read LINE <&3

# finally, close it
exec 3<&-
```

### Overall script logfile

To redirect the whole `stdout` and `stderr` of the shell or shellscript
to a file, you can use the `exec` builtin command:

``` bash
exec >/var/adm/my.log 2>&1

# script continues here...
```

## Portability considerations

    *POSIX(r) specifies error code ranges:
      * if ''exec'' can't find the program to execute, the error code shall be 126
      * on a redirection error, the error code shall be between 1 and 125
    * the ''-a NAME'' option appeared in Bash 4.2-alpha
    * POSIX(r) does **not** specify any options for ''exec'' (like ''-c'', ''-l'', ''-a NAME'').

## See also

- [redirection](/syntax/redirection.md)
