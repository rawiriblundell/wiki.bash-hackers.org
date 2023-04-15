====== Arithmetic expressions ======

{{keywords&gt;bash shell scripting math arithmetic C calculation integer}}

Arithmetic expressions are used in several situations:
  * [[syntax:ccmd:arithmetic_eval | arithmetic evaluation command]]
  * [[syntax:expansion:arith | arithmetic expansion]]
  * [[syntax:pe#substring_expansion | substring parameter expansion]]
  * [[commands:builtin:let | the ''let'' builtin command]]
  * [[syntax:ccmd:c_for | C-style for loop]]
  * [[syntax:arrays | array indexing]]
  * [[syntax:ccmd:conditional_expression | conditional expressions]]
  * Assignment statements, and arguments to declaration commands of variables with the integer attribute.

These expressions are evaluated following some rules described below. The operators and rules of arithmetic expressions are mainly derived from the C programming language.

This article describes the theory of the used syntax and the behaviour. To get practical examples without big explanations, see [[http://mywiki.wooledge.org/BashGuide/CompoundCommands#Arithmetic_Evaluation | this page on Greg's wiki]].

===== Constants =====

Mathematical constants are simply fixed values you write: ''1'', ''3567'', or ''4326''. Bash interprets some notations specially:
  * ''0...'' (leading zero) is interpreted as an **octal** value
  * ''0x...'' is interpreted as a **hex** value
  * ''0X...'' also interpreted as a **hex**
  * ''&lt;BASE&gt;#...'' is interpreted as a number according to the **specified base** ''&lt;BASE&gt;'', e.g., ''2#00111011'' (see below)
If you have a constant set in a variable, like,
&lt;code&gt;
x=03254
&lt;/code&gt;
this is interpreted as an octal value. If you want it to be interpreted as a decimal value, you need to expand the parameter and specify base 10:
&lt;code&gt;
# this is interpreted as a decimal:
echo $(( 10#$x ))

# this is interpreted as an octal:
echo $(( x ))

# this is an invalid digit for base 10 (the &quot;x&quot;)...:
echo $(( 10#x ))
&lt;/code&gt;

===== Different bases =====

For a constant, the base can be specified using the form
&lt;code&gt;
&lt;BASE&gt;#&lt;DIGITS...&gt;
&lt;/code&gt;

Regardless of the specified base, the arithmetic expressions will, if ever displayed, be **displayed in decimal**!

When no base is specified, the base 10 (decimal) is assumed, except when the prefixes as mentioned above (octals, hexadecimals) are present. The specified base can range from 2 to 64. To represent digits in a specified base greater than 10, characters other than 0 to 9 are needed (in this order, low =&gt; high):
  * ''0 ... 9''
  * ''a ... z''
  * ''A ... Z''
  * ''@''
  * ''_''

Let's quickly invent a new number system with base 43 to show what I mean:
&lt;code&gt;
$ echo $((43#1))
1

$ echo $((43#a))
10

$echo $((43#A))
36

$ echo $((43#G))
42

$ echo $((43#H))
bash: 43#H: value too great for base (error token is &quot;43#H&quot;)
&lt;/code&gt;

If you have no clue what a base is and why there might be other bases, and what numbers are and how they are built, then you don't need different bases.

If you want to convert between the usual bases (octal, decimal, hex), use [[commands:builtin:printf | the printf command]] and its format strings.

===== Shell variables =====

Shell variables can of course be used as operands, even when the integer attribute is not turned on (by ''declare -i &lt;NAME&gt;''). If the variable is empty (null) or unset, its reference evaluates to 0. If the variable doesn't hold a value that looks like a valid expression (numbers or operations), the expression is re-used to reference, for example, the named parameters, e.g.:
&lt;code&gt;
test=string
string=3

echo $((test))
# will output &quot;3&quot;!
&lt;/code&gt;
Of course, in the end, when it finally evaluates to something that is **not** a valid arithmetic expression (newlines, ordinary text, ...) then you'll get an error.

When variables are referenced, the notation ''1 + $X'' is equivalent to the notation ''1 + X'', both are allowed.

When variables are referenced like ''$X'', the rules of [[syntax:pe | parameter expansion]] apply and are performed **before** the expression is evaluated. Thus, a construct like ''${MYSTRING:4:3}'' is valid inside an arithmetic expression.

===== Truth =====

Unlike command exit and return codes, arithmetic expressions evaluate to logical &quot;true&quot; when they are not 0. When they are 0, they evaluate to &quot;false&quot;. The [[syntax:ccmd:arithmetic_eval | arithmetic evaluation compound command]] reverses the &quot;truth&quot; of an arithmetic expression to match the &quot;truth&quot; of command exit codes:
  * if the arithmetic expression brings up a value not 0 (arithmetic true), it returns 0 (shell true)
  * if the arithmetic expression evaluates to 0 (arithmetic false), it returns 1 (shell false)
That means, the following ''if''-clause will execute the ''else''-thread:
&lt;code&gt;
if ((0)); then
  echo &quot;true&quot;
else
  echo &quot;false&quot;
fi
&lt;/code&gt;

===== Operators =====

==== Assignment ====

^Operator^Description^
|''&lt;ID&gt; = &lt;EXPR&gt;''|normal assignment|
|''&lt;ID&gt; *= &lt;EXPR&gt;''|equivalent to ''&lt;ID&gt; = &lt;ID&gt; * &lt;EXPR&gt;'', see [[syntax:arith_expr#calculations | calculation operators]]|
|''&lt;ID&gt; /= &lt;EXPR&gt;''|equivalent to ''&lt;ID&gt; = &lt;ID&gt; / &lt;EXPR&gt;'', see [[syntax:arith_expr#calculations | calculation operators]]|
|''&lt;ID&gt; %= &lt;EXPR&gt;''|equivalent to ''&lt;ID&gt; = &lt;ID&gt; % &lt;EXPR&gt;'', see [[syntax:arith_expr#calculations | calculation operators]]|
|''&lt;ID&gt; += &lt;EXPR&gt;''|equivalent to ''&lt;ID&gt; = &lt;ID&gt; + &lt;EXPR&gt;'', see [[syntax:arith_expr#calculations | calculation operators]]|
|''&lt;ID&gt; -= &lt;EXPR&gt;''|equivalent to ''&lt;ID&gt; = &lt;ID&gt; - &lt;EXPR&gt;'', see [[syntax:arith_expr#calculations | calculation operators]]|
|''&lt;ID&gt; &lt;nowiki&gt;&lt;&lt;=&lt;/nowiki&gt; &lt;NUMBER&gt;''|equivalent to ''&lt;ID&gt; = &lt;ID&gt; &lt;nowiki&gt;&lt;&lt;&lt;/nowiki&gt; &lt;NUMBER&gt;'', see [[syntax:arith_expr#bit_operations | bit operations]]|
|''&lt;ID&gt; &lt;nowiki&gt;&gt;&gt;=&lt;/nowiki&gt; &lt;NUMBER&gt;''|equivalent to ''&lt;ID&gt; = &lt;ID&gt; &lt;nowiki&gt;&gt;&gt;&lt;/nowiki&gt; &lt;NUMBER&gt;'', see [[syntax:arith_expr#bit_operations | bit operations]]|
|''&lt;ID&gt; &amp;= &lt;EXPR&gt;''|equivalent to ''&lt;ID&gt; = &lt;ID&gt; &amp; &lt;EXPR&gt;'', see [[syntax:arith_expr#bit_operations | bit operations]]|
|''&lt;ID&gt; ^= &lt;EXPR&gt;''|equivalent to ''&lt;ID&gt; = &lt;ID&gt; ^ &lt;EXPR&gt;'', see [[syntax:arith_expr#bit_operations | bit operations]]|
|''&lt;ID&gt; &lt;nowiki&gt;|=&lt;/nowiki&gt; &lt;EXPR&gt;''|equivalent to ''&lt;ID&gt; = &lt;ID&gt; &lt;nowiki&gt;|&lt;/nowiki&gt; &lt;EXPR&gt;'', see [[syntax:arith_expr#bit_operations | bit operations]]|

==== Calculations ====

^Operator^Description^
|''*''|multiplication|
|''/''|division|
|''%''|remainder (modulo)|
|''+''|addition|
|''-''|subtraction|
|''&lt;nowiki&gt;**&lt;/nowiki&gt;''|exponentiation|

==== Comparisons ====

^Operator^Description^
|''&lt;''|comparison: less than|
|''&gt;''|comparison: greater than|
|''&lt;nowiki&gt;&lt;=&lt;/nowiki&gt;''|comparison: less than or equal|
|''&lt;nowiki&gt;&gt;=&lt;/nowiki&gt;''|comparison: greater than or equal|
|''&lt;nowiki&gt;==&lt;/nowiki&gt;''|equality|
|''!=''|inequality|

==== Bit operations ====

^Operator^Description^
|''~''|bitwise negation|
|''&lt;nowiki&gt;&lt;&lt;&lt;/nowiki&gt;''|bitwise shifting (left)|
|''&lt;nowiki&gt;&gt;&gt;&lt;/nowiki&gt;''|bitwise shifting (right)|
|''&amp;''|bitwise AND|
|''^''|bitwise exclusive OR (XOR)|
|''|''|bitwise OR|

==== Logical ====

^Operator^Description^
|''!''|logical negation|
|''&lt;nowiki&gt;&amp;&amp;&lt;/nowiki&gt;''|logical AND|
|''&lt;nowiki&gt;||&lt;/nowiki&gt;''|logical OR|

==== Misc ====

^ Operator                      ^ Description                                                                           ^
| ''id++''                      | **post-increment** of the variable ''id'' (not required by POSIX(r))                  |
| ''id%%--%%''                  | **post-decrement** of the variable ''id'' (not required by POSIX(r))                  |
| ''++id''                      | **pre-increment** of the variable ''id'' (not required by POSIX(r))                   |
| ''%%--%%id''                  | **pre-decrement** of the variable ''id'' (not required by POSIX(r))                   |
| ''+''                         | unary plus                                                                            |
| ''-''                         | unary minus                                                                           |
| ''&lt;EXPR&gt; ? &lt;EXPR&gt; : &lt;EXPR&gt;''  | conditional (ternary) operator \\ &lt;condition&gt; ? &lt;result-if-true&gt; : &lt;result-if-false&gt;  |
| ''&lt;EXPR&gt; , &lt;EXPR&gt;''           | expression list                                                                       |
| ''( &lt;EXPR&gt; )''                | subexpression (to force precedence)                                                   |


===== Precedence =====

The operator precedence is as follows (highest -&gt; lowest):

  * Postfix (''id++'', ''&lt;nowiki&gt;id--&lt;/nowiki&gt;'')
  * Prefix (''++id'', ''&lt;nowiki&gt;--id&lt;/nowiki&gt;'')
  * Unary minus and plus (''-'', ''+'')
  * Logical and bitwise negation (''!'', ''~'')
  * Exponentiation (''&lt;nowiki&gt;**&lt;/nowiki&gt;'')
  * Multiplication, division, remainder (''*'', ''/'', ''%'')
  * Addition, subtraction (''+'', ''-'')
  * Bitwise shifts (''&lt;nowiki&gt;&lt;&lt;&lt;/nowiki&gt;'', ''&lt;nowiki&gt;&gt;&gt;&lt;/nowiki&gt;'')
  * Comparison (''&lt;'', ''&gt;'', ''&lt;nowiki&gt;&lt;=&lt;/nowiki&gt;'', ''&lt;nowiki&gt;&gt;=&lt;/nowiki&gt;'')
  * (In-)equality (''&lt;nowiki&gt;==&lt;/nowiki&gt;'', ''!='')
  * Bitwise AND (''&amp;'')
  * Bitwise XOR (''^'')
  * Bitwise OR (''|'')
  * Logical AND (''&lt;nowiki&gt;&amp;&amp;&lt;/nowiki&gt;'')
  * Logical OR (''&lt;nowiki&gt;||&lt;/nowiki&gt;'')
  * Ternary operator (''&lt;EXPR&gt; ? &lt;EXPR&gt; : &lt;EXPR&gt;'')
  * Assignments (''='', ''*='', ''/='', ''%='', ''+='', ''-='', ''&lt;nowiki&gt;&lt;&lt;=&lt;/nowiki&gt;'', ''&lt;nowiki&gt;&gt;&gt;=&lt;/nowiki&gt;'', ''&amp;='', ''^='', ''|='')
  * Expression list operator (''&lt;EXPR&gt; , &lt;EXPR&gt;'')

The precedence can be adjusted using subexpressions of the form ''( &lt;EXPR&gt; )'' at any time. These subexpressions are always evaluated first.

===== Arithmetic expressions and return codes =====

Bash's overall language construct is based on exit codes or return codes of commands or functions to be executed. ''if'' statements, ''while'' loops, etc., they all take the return codes of commands as conditions.

Now the problem is: The return codes (0 means &quot;TRUE&quot; or &quot;SUCCESS&quot;, not 0 means &quot;FALSE&quot; or &quot;FAILURE&quot;) don't correspond to the meaning of the result of an arithmetic expression (0 means &quot;FALSE&quot;, not 0 means &quot;TRUE&quot;).

That's why all commands and keywords that do arithmetic operations attempt to **translate** the arithmetical meaning into an equivalent return code. This simply means:
  * if the arithmetic operation evaluates to 0 (&quot;FALSE&quot;), the return code is not 0 (&quot;FAILURE&quot;)
  * if the arithmetic operation evaluates to 1 (&quot;TRUE&quot;), the return code is 0 (&quot;SUCCESS&quot;)

This way, you can easily use arithmetic expressions (along with the commands or keywords that operate them) as conditions for ''if'', ''while'' and all the others, including ''set -e'' for autoexit on error:
&lt;code bash&gt;
MY_TEST_FLAG=0

if ((MY_TEST_FLAG)); then
  echo &quot;MY_TEST_FLAG is ON&quot;
else
  echo &quot;MY_TEST_FLAG is OFF&quot;
fi
&lt;/code&gt;
&lt;WRAP center round important&gt;
Beware that ''set -e'' can change the runtime behavior of scripts.  For example,

This non-equivalence of code behavior deserves some attention.
Consider what happens if v happens to be zero in the expression below:

&lt;code bash&gt;((v += 0))
echo $?&lt;/code&gt;
1

(&quot;FAILURE&quot;)

&lt;code bash&gt;v=$((v + 0))
echo $?&lt;/code&gt;
0

(&quot;SUCCESS&quot;)

The return code behavior is not equivalent to the arithmetic behavior, as has been noted.

A workaround is to use a list operation that returns True, or use the second assignment style.
&lt;code bash&gt;((v += 0)) || :
echo $?&lt;/code&gt;
0

(&quot;SUCCESS&quot;)

This change in code behavior was discovered once the script was run under set -e.
&lt;/WRAP&gt;
===== Arithmetic expressions in Bash =====

  * [[syntax:ccmd:c_for | The C-style for-loop]]
  * [[syntax:expansion:arith | Arithmetic expansion]]
  * [[syntax:ccmd:arithmetic_eval | Arithmetic evaluation compound command]]
  * [[commands:builtin:let | The &quot;let&quot; builtin command]]
