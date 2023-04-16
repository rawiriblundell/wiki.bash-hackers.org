====== User selections ======

===== Synopsis =====
<code>
select <NAME>; do
  <LIST>
done
</code>

<code>
select <NAME> in <WORDS>; do
  <LIST>
done
</code>

<code>
# alternative, historical and undocumented syntax

select <NAME>
{
  <LIST>
}

select <NAME> in <WORDS>
{
  <LIST>
}
</code>

===== Description =====
This compound command provides a kind of menu. The user is prompted with a //numbered list// of the given words, and is asked to input the index number of the word. If a word was selected, the variable ''<NAME>'' is set to this word, and the [[syntax:basicgrammar#lists | list]] ''<LIST>'' is executed.

If no ''in <WORDS>'' is given, then the positional parameters are taken as words (as if ''in &quot;$@&quot;'' was written).

Regardless of the functionality, the //number// the user entered is saved in the variable ''REPLY''.

Bash knows an alternative syntax for the ''select'' command, enclosing the loop body in ''{<nowiki>...</nowiki>}'' instead of ''do <nowiki>...</nowiki> done'':
<code>
select x in 1 2 3
{
  echo $x
}
</code>
This syntax is **not documented** and should not be used. I found the parser definitions for it in 1.x code, and in modern 4.x code. My guess is that it's there for compatiblity reasons. This syntax is not specified by POSIX(R).

===== Examples =====

===== Portability considerations =====

===== See also =====

