# Arithmetic evaluation (command)

## Synopsis

    (( <EXPRESSION> ))

## Description

This command evaluates the [arithmetic expression](/syntax/arith_expr.md)
`<EXPRESSION>`.

If the expression evaluates to 0 then the exit code of the expression is
set to 1 (`FALSE`). If the expression evaluates to something else than
0, then the exit code of the expression is set to 0 (`TRUE`). For this
return code mapping, please see [this
section](/syntax/arith_expr.md#arithmetic_expressions_and_return_codes).

The functionality basically is equivalent to what the [`let` builtin
command](/commands/builtin/let.md) does. The arithmetic evaluation compound
command should be preferred.

## Examples

## Portability considerations

## See also

- Internal: [arithmetic expressions](/syntax/arith_expr.md)
- Internal: [arithmetic expansion](/syntax/expansion/arith.md)
- Internal: [The `let` builtin command](/commands/builtin/let.md)
