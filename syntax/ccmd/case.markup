====== The case statement ======

===== Synopsis =====
<code>
case <WORD> in
  [(] <PATTERN1> ) <LIST1> ;; # or ;& or ;;& in Bash 4
  [(] <PATTERN2> ) <LIST2> ;;
  [(] <PATTERN3> | <PATTERN4> ) <LIST3-4> ;;
  ...
  [(] <PATTERNn>) <LISTn> [;;]
esac
</code>

===== Description =====
The ''case''-statement can execute commands based on a [[syntax:pattern | pattern matching]] decision. The word ''<WORD>'' is matched against every pattern ''<PATTERNn>'' and on a match, the associated [[syntax:basicgrammar#lists | list]] ''<LISTn>'' is executed. Every commandlist is terminated by ''<nowiki>;;</nowiki>''. This rule is optional for the very last commandlist (i.e., you can omit the ''<nowiki>;;</nowiki>'' before the ''esac''). Every ''<PATTERNn>'' is separated from it's associated ''<LISTn>'' by a '')'', and is optionally preceded by a ''(''.

Bash 4 introduces two new action terminators. The classic behavior using '';;'' is to execute only the list associated with the first matching pattern, then break out of the ''case'' block. The '';&'' terminator causes ''case'' to also execute the next block without testing its pattern. The '';;&'' operator is like '';;'', except the case statement doesn't terminate after executing the associated list - Bash just continues testing the next pattern as though the previous pattern didn't match. Using these terminators, a ''case'' statement can be configured to test against all patterns, or to share code between blocks, for example.

The word ''<WORD>'' is expanded using //tilde//, //parameter// and //variable expansion//; //arithmetic//, //command// and //process substitution//; and //quote removal//. **No word splitting, brace, or pathname expansion is done**, which means you can leave expansions unquoted without problems:
<code>
var=&quot;test word&quot;

case $var in
  ...
esac
</code>
This is similar to the behavior of the [[syntax:ccmd:conditional_expression | conditional expression command (&quot;new test command&quot;)]] (also no word splitting for expansions).

Unlike the C-case-statement, only the matching list and nothing else is executed. If more patterns match the word, only the first match is taken. (**Note** the comment about Bash v4 changes above.)

Multiple ''|''-delimited patterns can be specified for a single block. This is a POSIX-compatable equivalent to the ''@(pattern-list)'' extglob construct.

The ''case'' statement is one of the most difficult commands to indent clearly, and people frequently ask about the most &quot;correct&quot; style. Just do your best - there are many variations of indenting style for ''case'' and no real agreed-upon best practice.

===== Examples =====
Another one of my stupid examples...
<code>
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
</code>

Here's a practical example showing a common pattern involving a ''case'' statement. If the first argument is one of a valid set of alternatives, then perform some sysfs operations under Linux to control a video card's power profile. Otherwise, show a usage synopsis, and print the current power profile and GPU temperature.
<code bash>
# Set radeon power management
function clk {
	typeset base=/sys/class/drm/card0/device
	[[ -r ${base}/hwmon/hwmon0/temp1_input && -r ${base}/power_profile ]] || return 1

	case $1 in
		low|high|default)
			printf '%s\n' &quot;temp: $(<${base}/hwmon/hwmon0/temp1_input)C&quot; &quot;old profile: $(<${base}/power_profile)&quot;
			echo &quot;$1&quot; >${base}/power_profile
			echo &quot;new profile: $(<${base}/power_profile)&quot;
			;;
		*)
			echo &quot;Usage: $FUNCNAME [ low | high | default ]&quot;
			printf '%s\n' &quot;temp: $(<${base}/hwmon/hwmon0/temp1_input)C&quot; &quot;current profile: $(<${base}/power_profile)&quot;
	esac
}
</code>

A template for experiments with ''case'' logic, showing shared code between blocks using '';&'', and the non-short-circuiting '';;&'' operator:
<code bash>
#!/usr/bin/env bash

f() {
    local -a &quot;$@&quot;
    local x

    for x; do
        case $x in
            $1)
                local &quot;$x&quot;'+=(1)' ;;&
            $2)
                local &quot;$x&quot;'+=(2)' ;&
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
</code>

===== Portability considerations =====

  * Only the '';;'' delimiter is specified by POSIX.
  * zsh and mksh use the '';|'' control operator instead of Bash's '';;&''. Mksh has '';;&'' for Bash compatability (undocumented).
  * ksh93 has the '';&'' operator, but no '';;&'' or equivalent.
  * ksh93, mksh, zsh, and posh support a historical syntax where open and close braces may be used in place of ''in'' and ''esac'': ''case word { x) ...; };''. This is similar to the alternate form Bash supports for its [[syntax/ccmd/classic_for | for loops]], but Bash doesn't support this syntax for ''case..esac''.
===== See also =====
  * [[http://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html#tag_18_09_04_05|POSIX case conditional construct]]
