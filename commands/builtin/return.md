# The return builtin command

## Synopsis

    return [N]

## Description

The `return` command returns from a shell function.

If `N` is given, the return code to the caller is set to `N`. If not,
the returned status the the status of the most recently executed command
(i.e. `$?`).

### Options

There are no options.

### Exit status

If everything is okay, the `return` command doesn't come back. If it
comes back, there was a problem in doing the return.

| Status | Reason                                                                  |
|:-------|:------------------------------------------------------------------------|
| 1      | `return` was called while not being in a shell function or sourced file |

## Examples

## Portability considerations

## See also

- [The exit builtin command](/commands/builtin/exit)
- [The exit status](/dict/terms/exit_status)
