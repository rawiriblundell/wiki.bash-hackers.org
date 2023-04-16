# The exit builtin command

## Synopsis

    exit [N]

## Description

The `exit` command terminates the current shell (or script).

If `N` is given, the return code to the parent process is set to `N`. If
not, the returned status the the status of the most recently executed
command (i.e. `$?`).

A [trap](/commands/builtin/trap) on `EXIT` is executed before the shell
exits, except the executed `exit` command is part of an already running
trap.

### Options

There are no options.

### Exit status

Naturally, you can't ask for the exit status from within the shell that
executed the `exit` command, because the shell exits.

| Status | Reason                                                                     |
|:-------|:---------------------------------------------------------------------------|
| 255    | invalid (e.g. non-numeric) argument - this staus is returned to the parent |

## Examples

### Exit the shell and explicitely set its exit status

    exit 3

## Portability considerations

- if `N` is specified, but its value is not between 0 and 255
  inclusively, the exit status is undefined.

## See also

- [The trap builtin command](/commands/builtin/trap)
- [The exit status](/dict/terms/exit_status)
