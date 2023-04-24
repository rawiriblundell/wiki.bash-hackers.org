# User selections

## Synopsis

    select <NAME>; do
      <LIST>
    done

    select <NAME> in <WORDS>; do
      <LIST>
    done

    # alternative, historical and undocumented syntax

    select <NAME>
    {
      <LIST>
    }

    select <NAME> in <WORDS>
    {
      <LIST>
    }

## Description

This compound command provides a kind of menu. The user is prompted with
a *numbered list* of the given words, and is asked to input the index
number of the word. If a word was selected, the variable `<NAME>` is set
to this word, and the [list](/syntax/basicgrammar.md#lists) `<LIST>` is
executed.

If no `in <WORDS>` is given, then the positional parameters are taken as
words (as if `in "$@"` was written).

Regardless of the functionality, the *number* the user entered is saved
in the variable `REPLY`.

Bash knows an alternative syntax for the `select` command, enclosing the
loop body in `{...}` instead of `do ... done`:

    select x in 1 2 3
    {
      echo $x
    }

This syntax is **not documented** and should not be used. I found the
parser definitions for it in 1.x code, and in modern 4.x code. My guess
is that it's there for compatiblity reasons. This syntax is not
specified by POSIX(R).

## Examples

## Portability considerations

## See also
