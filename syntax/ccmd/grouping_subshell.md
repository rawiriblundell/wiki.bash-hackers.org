# Grouping commands in a subshell

## Synopsis

    ( <LIST> )

## Description

The [list](/syntax/basicgrammar#lists) `<LIST>` is executed in a
separate shell - a subprocess. No changes to the environment (variables
etc...) are reflected in the "main shell".

## Examples

Execute a command in a different directory.

``` bash
echo &quot;$PWD&quot;
( cd /usr; echo &quot;$PWD&quot; )
echo &quot;$PWD&quot; # Still in the original directory.
```

## Portability considerations

- The subshell compound command is specified by POSIX.
- Avoid ambiguous syntax.

``` bash
(((1+1))) # Equivalent to: (( (1+1) ))
```

## See also

- [grouping commands](/syntax/ccmd/grouping_plain)
- [Subshells on Greycat's wiki](http://mywiki.wooledge.org/SubShell)
