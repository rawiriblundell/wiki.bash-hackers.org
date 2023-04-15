====== The case statement ======

===== Synopsis =====
&lt;code&gt;
case &lt;WORD&gt; in
  [(] &lt;PATTERN1&gt; ) &lt;LIST1&gt; ;; # or ;&amp; or ;;&amp; in Bash 4
  [(] &lt;PATTERN2&gt; ) &lt;LIST2&gt; ;;
  [(] &lt;PATTERN3&gt; | &lt;PATTERN4&gt; ) &lt;LIST3-4&gt; ;;
  ...
  [(] &lt;PATTERNn&gt;) &lt;LISTn&gt; [;;]
esac
&lt;/code&gt;

===== Description =====
The ''case''-statement can execute commands based on a [[syntax:pattern | pattern matching]] decision. The word ''&lt;WORD&gt;'' is matched against every pattern ''&lt;PATTERNn&gt;'' and on a match, the associated [[syntax:basicgrammar#lists | list]] ''&lt;LISTn&gt;'' is executed. Every commandlist is terminated by ''&lt;nowiki&gt;;;&lt;/nowiki&gt;''. This rule is optional for the very last commandlist (i.e., you can omit the ''&lt;nowiki&gt;;;&lt;/nowiki&gt;'' before the ''esac''). Every ''&lt;PATTERNn&gt;'' is separated from it's associated ''&lt;LISTn&gt;'' by a '')'', and is optionally preceded by a ''(''.

Bash 4 introduces two new action terminators. The classic behavior using '';;'' is to execute only the list associated with the first matching pattern, then break out of the ''case'' block. The '';&amp;'' terminator causes ''case'' to also execute the next block without testing its pattern. The '';;&amp;'' operator is like '';;'', except the case statement doesn't terminate after executing the associated list - Bash just continues testing the next pattern as though the previous pattern didn't match. Using these terminators, a ''case'' statement can be configured to test against all patterns, or to share code between blocks, for example.

The word ''&lt;WORD&gt;'' is expanded using //tilde//, //parameter// and //variable expansion//; //arithmetic//, //command// and //process substitution//; and //quote removal//. **No word splitting, brace, or pathname expansion is done**, which means you can leave expansions unquoted without problems:
&lt;code&gt;
var=&quot;test word&quot;

case $var in
  ...
esac
&lt;/code&gt;
This is similar to the behavior of the [[syntax:ccmd:conditional_expression | conditional expression command (&quot;new test command&quot;)]] (also no word splitting for expansions).

Unlike the C-case-statement, only the matching list and nothing else is executed. If more patterns match the word, only the first match is taken. (**Note** the comment about Bash v4 changes above.)

Multiple ''|''-delimited patterns can be specified for a single block. This is a POSIX-compatable equivalent to the ''@(pattern-list)'' extglob construct.

The ''case'' statement is one of the most difficult commands to indent clearly, and people frequently ask about the most &quot;correct&quot; style. Just do your best - there are many variations of indenting style for ''case'' and no real agreed-upon best practice.

===== Examples =====
Another one of my stupid examples...
&lt;code&gt;
printf '%s ' 'Which fruit do you like most?'
read -${BASH_VERSION+e}r fruit

case $fruit in
    apple)
        echo 'Mmmmh... I like those!'
        ;;
    banana)
        echo 'Hm, a bit awry, no?'
        ;;
    orange|tangerine)
        echo $'Eeeks! I don\'t like those!\nGo away!'
        exit 1
        ;;
    *)
        echo &quot;Unknown fruit - sure it isn't toxic?&quot;
esac
&lt;/code&gt;

Here's a practical example showing a common pattern involving a ''case'' statement. If the first argument is one of a valid set of alternatives, then perform some sysfs operations under Linux to control a video card's power profile. Otherwise, show a usage synopsis, and print the current power profile and GPU temperature.
&lt;code bash&gt;
# Set radeon power management
function clk {
	typeset base=/sys/class/drm/card0/device
	[[ -r ${base}/hwmon/hwmon0/temp1_input &amp;&amp; -r ${base}/power_profile ]] || return 1

	case $1 in
		low|high|default)
			printf '%s\n' &quot;temp: $(&lt;${base}/hwmon/hwmon0/temp1_input)C&quot; &quot;old profile: $(&lt;${base}/power_profile)&quot;
			echo &quot;$1&quot; &gt;${base}/power_profile
			echo &quot;new profile: $(&lt;${base}/power_profile)&quot;
			;;
		*)
			echo &quot;Usage: $FUNCNAME [ low | high | default ]&quot;
			printf '%s\n' &quot;temp: $(&lt;${base}/hwmon/hwmon0/temp1_input)C&quot; &quot;current profile: $(&lt;${base}/power_profile)&quot;
	esac
}
&lt;/code&gt;

A template for experiments with ''case'' logic, showing shared code between blocks using '';&amp;'', and the non-short-circuiting '';;&amp;'' operator:
&lt;code bash&gt;
#!/usr/bin/env bash

f() {
    local -a &quot;$@&quot;
    local x

    for x; do
        case $x in
            $1)
                local &quot;$x&quot;'+=(1)' ;;&amp;
            $2)
                local &quot;$x&quot;'+=(2)' ;&amp;
            $3)
                local &quot;$x&quot;'+=(3)' ;;
            $1|$2)
                local &quot;$x&quot;'+=(4)'
        esac
        IFS=, local -a &quot;$x&quot;'=(&quot;${x}: ${'&quot;$x&quot;'[*]}&quot;)'
    done

    for x; do
        echo &quot;${!x}&quot;
    done
}

f a b c

# output:
# a: 1,4
# b: 2,3
# c: 3
&lt;/code&gt;

===== Portability considerations =====

  * Only the '';;'' delimiter is specified by POSIX.
  * zsh and mksh use the '';|'' control operator instead of Bash's '';;&amp;''. Mksh has '';;&amp;'' for Bash compatability (undocumented).
  * ksh93 has the '';&amp;'' operator, but no '';;&amp;'' or equivalent.
  * ksh93, mksh, zsh, and posh support a historical syntax where open and close braces may be used in place of ''in'' and ''esac'': ''case word { x) ...; };''. This is similar to the alternate form Bash supports for its [[syntax/ccmd/classic_for | for loops]], but Bash doesn't support this syntax for ''case..esac''.
===== See also =====
  * [[http://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html#tag_18_09_04_05|POSIX case conditional construct]]
