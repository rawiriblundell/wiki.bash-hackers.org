====== User selections ======

===== Synopsis =====
&lt;code&gt;
select &lt;NAME&gt;; do
  &lt;LIST&gt;
done
&lt;/code&gt;

&lt;code&gt;
select &lt;NAME&gt; in &lt;WORDS&gt;; do
  &lt;LIST&gt;
done
&lt;/code&gt;

&lt;code&gt;
# alternative, historical and undocumented syntax

select &lt;NAME&gt;
{
  &lt;LIST&gt;
}

select &lt;NAME&gt; in &lt;WORDS&gt;
{
  &lt;LIST&gt;
}
&lt;/code&gt;

===== Description =====
This compound command provides a kind of menu. The user is prompted with a //numbered list// of the given words, and is asked to input the index number of the word. If a word was selected, the variable ''&lt;NAME&gt;'' is set to this word, and the [[syntax:basicgrammar#lists | list]] ''&lt;LIST&gt;'' is executed.

If no ''in &lt;WORDS&gt;'' is given, then the positional parameters are taken as words (as if ''in &quot;$@&quot;'' was written).

Regardless of the functionality, the //number// the user entered is saved in the variable ''REPLY''.

Bash knows an alternative syntax for the ''select'' command, enclosing the loop body in ''{&lt;nowiki&gt;...&lt;/nowiki&gt;}'' instead of ''do &lt;nowiki&gt;...&lt;/nowiki&gt; done'':
&lt;code&gt;
select x in 1 2 3
{
  echo $x
}
&lt;/code&gt;
This syntax is **not documented** and should not be used. I found the parser definitions for it in 1.x code, and in modern 4.x code. My guess is that it's there for compatiblity reasons. This syntax is not specified by POSIX(R).

===== Examples =====

===== Portability considerations =====

===== See also =====

