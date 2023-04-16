# Arithmetic expansion

    $(( <EXPRESSION> ))

    $[ <EXPRESSION> ]

The [arithmetic expression](/syntax/arith_expr) `<EXPRESSION>` is
evaluated and expands to the result. The output of the arithmetic
expansion is guaranteed to be one word and a digit in Bash.

Please **do not use the second form `$[ ... ]`**! It's deprecated. The
preferred and standardized form is `$(( ... ))`!

Example

``` bash
function printSum {
    typeset -A args
    typeset name
    for name in first second; do
        [[ -t 0 ]] && printf 'Enter %s positive integer: ' &quot;$name&quot; >&2
        read -r ${BASH_VERSION+-e} &quot;args[$name]&quot;
        [[ ${args[$name]} == +([[:digit:]]) ]] || return 1 # Validation is extremely important whenever user input is used in arithmetic.
    done
    printf 'The sum is %d.' $((${args[first]} + ${args[second]}))
}
```

**Note** that in Bash you don't need the arithmetic expansion to check
for the boolean value of an arithmetic expression. This can be done
using the [arithmetic evaluation compound
command](/syntax/ccmd/arithmetic_eval):

``` bash
printf %s 'Enter a number: ' >&2
read -r number
if ((number == 1234)); then
    echo 'Good guess'
else
    echo 'Haha... :-P'
fi
```

**Variables** used inside the arithmetic expansion, as in all arithmetic
contexts, can be used with or without variable expansion:

``` bash
x=1

echo $((x))       # Good.
echo $(($x))      # Ok. Avoid expansions within arithmetic. Use variables directly.
echo $((&quot;$x&quot;))    # Error. There is no quote-removal in arithmetic contexts. It expands to $((&quot;1&quot;)), which is an invalid arithmetic expression.
echo $((x[0]))    # Good.
echo $((${x[0]})) # Ok. Nested expansion again.
echo $((${x[$((${x[!$x]}-$x))]})) # Same as above but more ridiculous.
echo $(($x[0]))   # Error. This expands to $((1[0])), an invalid expression.
```

## Bugs and Portability considerations

- The original Bourne shell doesn't have arithmetic expansions. You have
  to use something like `expr(1)` within backticks instead. Since `expr`
  is horrible (as are backticks), and arithmetic expansion is required
  by POSIX, you should not worry about this, and preferably fix any code
  you find that's still using `expr`.

## See also

- [arithmetic expressions](/syntax/arith_expr)
- [arithmetic evaluation compound command](/syntax/ccmd/arithmetic_eval)
- [Introduction to expansion and substitution](/syntax/expansion/intro)
- [POSIX
  definition](http://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html#tag_18_06_04)
