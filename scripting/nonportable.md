====== Portability talk ======

{{keywords>bash shell scripting portability POSIX portable}}

The script programming language of BASH is based on the Bourne Shell syntax, with some extensions and derivations.

If scripts need to be portable, some of the BASH-specific syntax elements should be avoided. Others should be avoided for all scripts, e.g. if there is a corresponding POSIX(r)-compatible syntax (see [[scripting:obsolete]]).

Some syntax elements have a BASH-specific, and a portable((&quot;portable&quot; doesn't necessarily mean it's POSIX, it can also mean it's &quot;widely used and accepted&quot;, and thus maybe more portable than POSIX(r))) pendant. In these cases the portable syntax should be preferred.

^  construct  ^  portable equivalent  ^  Description  ^  Portability  ^
|''source\ FILE''|''. FILE''|include a script file|Bourne shell (bash, ksh, POSIX(r), zsh, ...)|
|''declare''\ keyword|''typeset'' keyword|define local variables (or variables with special attributes)|ksh, zsh, ..., **not POSIX!**|
|''command\ <nowiki><<<</nowiki>\ WORD''|''command <nowiki><<</nowiki>MARKER\\ WORD\\ MARKER''|a here-string, a special form of the here-document, avoid it in portable scripts!|POSIX(r)|
|''export VAR=VALUE''|''VAR=VALUE\\ export VAR''|Though POSIX(r) allows it, some shells don't want the assignment and the exporting in one command|POSIX(r), zsh, ksh, ...|
|''<nowiki>(( MATH ))</nowiki>''|''<nowiki>: $(( MATH ))</nowiki>''|POSIX(r) does't define an arithmetic compund command, many shells don't know it. Using the pseudo-command '':'' and the arithmetic expansion ''<nowiki>$(( ))</nowiki>'' is a kind of workaround here. **Attention:** Not all shell support assignment like ''<nowiki>$(( a = 1 + 1 ))</nowiki>''! Also see below for a probably more portable solution.|all POSIX(r) compatible shells|
|''<nowiki>[[</nowiki>\ EXPRESSION\ <nowiki>]]</nowiki>''|''[ EXPRESSION ]''\\ or \\ ''test EXPRESSION''|The Bashish test keyword is reserved by POSIX(r), but not defined. Use the old fashioned way with the ''test'' command. See [[commands:classictest | the classic test command]]|POSIX(r) and others|
|''COMMAND\ <\ <(\ ...INPUTCOMMANDS...\ )''|''INPUTCOMMANDS\ >\ TEMPFILE\\ COMMAND\ <\ TEMPFILE''|Process substitution (here used with redirection); use the old fashioned way (tempfiles)|POSIX(r) and others|
|''<nowiki>((echo X);(echo Y))</nowiki>''  |''( (echo X); (echo Y) )''  | Nested subshells (separate the inner ''()'' from the outer ''()'' by spaces, to not confuse the shell regarding arithmetic control operators)  |POSIX(r) and others  |

===== Portability rationale =====

Here is some assorted portability information. Take it as a small guide to make your scripts a bit more portable. It's not complete (it never will be!) and it's not very detailed (e.g. you won't find information about how which shell technically forks off which subshell). It's just an assorted small set of portability guidelines. //-Thebonsai//

FIXME UNIX shell gurus out there, please be patient with a newbie like me and give comments and hints instead of flames.

==== Environment (exported) variables ====

When a new value is assigned to an **existing environment variable**, there are two possibilities:

The //new value// is seen by subsequent programs
  * without any special action (e.g. Bash)
  * only after an explicit export with ''export VARIABLE'' (e.g. Sun's ''/bin/sh'')

Since an extra ''export'' doesn't hurt, the safest and most portable way is to always (re-)export a changed variable if you want it to be seen by subsequent processes.

==== Arithmetics ====

Bash has a special compound command to do arithmetic without expansion. However, POSIX has no such command. In the table at the top, there's the ''<nowiki>: $((MATH))</nowiki>'' construct mentioned as possible alternative. Regarding the exit code, a 100% equivalent construct would be:
<code>
# Bash (or others) compound command
if ((MATH)); then
...

# portable equivalent command
if [ &quot;$((MATH))&quot; -ne 0 ]; then
...
</code>

Quotes around the arithmetic expansion ''<nowiki>$((MATH))</nowiki>'' should not be necessary as per POSIX, but Bash and AT&T-KSH perform word-splitting on aritrhmetic expansions, so the most portable is //with quotes//.

==== echo command ====

The overall problem with ''echo'' is, that there are 2 (maybe more) mainstream flavours around. The only case where you may safely use an ''echo'' on all systems is: Echoing non-variable arguments that don't start with a ''-'' (dash) and don't contain a ''\'' (backslash).

Why? (list of known behaviours)
  * may or may not automatically interpret backslash escpape codes in the strings
  * may or may not automatically interpret switches (like ''-n'')
  * may or may not ignore &quot;end of options&quot; tag (''<nowiki>--</nowiki>'')
  * ''echo -n'' and ''echo -e'' are neither portable nor standard (**even within the same shell**, depending on the version or environment variables or the build options, especially KSH93 and Bash)

For these, and possibly other, reasons, POSIX (SUS) standardized the existance of [[commands:builtin:printf | the ''printf'' command]].


==== Parameter expansions ====

  * ''${var:x:x}'' is KSH93/Bash specific
  * ''${var/../..}'' and ''${var<nowiki>//</nowiki>../..}'' are KSH93/Bash specific
  * ''var=$*'' and ''var=$@'' are not handled the same in all shells if the first char of IFS is not &quot; &quot; (space). ''var=&quot;$*&quot;'' should work (except the Bourne shell always joins the expansions with space)

==== Special variables ====

=== PWD ===

[[syntax:shellvars#PWD|PWD]] is POSIX but not Bourne. Most shells are //not POSIX// in that they don't ignore the value of the ''PWD'' environment variable. Workaround to fix the value of ''PWD'' at the start of your script:
<code>
pwd -P > dev/null
</code>

=== RANDOM ===

[[syntax:shellvars#RANDOM|RANDOM]] is Bash/KSH/ZSH specific variable that will give you a random number up to 32767 (2^15-1). Among many other available external options, you can use awk to generate a random number.  There are multiple implementations of awk and which version your system uses will depend.  Most modern systems will call 'gawk' (i.e. GNU awk) or 'nawk'.  'oawk' (i.e. Original/Old awk) does not have the rand() or srand() functions, so is best avoided.

<code>
# 'gawk' can produce random numbers using srand().  In this example, 10 integers between 1 and 500:
randpm=$(gawk -v min=1 -v max=500 -v nNum=10 'BEGIN { srand(systime() + PROCINFO[&quot;pid&quot;]); for (i = 0; i < nNum; ++i) {print int(min + rand() * (max - min)} }')

# 'nawk' and 'mawk' does the same, but needs a seed to be provided for its rand() function.  In this example we use $(date)
randpm=$(mawk -v min=1 -v max=500 -v nNum=10 -v seed=&quot;$(date +%Y%M%d%H%M%S)&quot; 'BEGIN { srand(seed); for (i = 0; i < nNum; ++i) {print int(min + rand() * (max - min)} }')
</code>
//Yes, I'm not an ''awk'' expert, so please correct it, rather than complaining about possible stupid code!//

<code>
# Well, seeing how this //is// BASH-hackers.org I kinda missed the bash way of doing the above ;-) 
# print a number between 0 and 500 :-)
 printf $((  500 *  RANDOM  / 32767   ))

# Or print 30 random  numbers between 0 and 10 ;)
 X=0; while (( X++ < 30 )); do echo $((  10 *  RANDOM  / 32767   )); done
</code>


=== SECONDS ===

[[syntax:shellvars#SECONDS|SECONDS]] is KSH/ZSH/Bash specific. Avoid it. Find another method.



==== Check for a command in PATH ====

The [[syntax:shellvars#PATH|PATH]] variable is a colon-delimited list of directory names, so it's basically possible to run a loop and check every ''PATH'' component for the command you're looking for and for executability.

However, this method doesn't look nice. There are other ways of doing this, using commands that are //not directly// related to this task.

=== hash ===
The ''hash'' command is used to make the shell store the full pathname of a command in a lookup-table (to avoid re-scanning the ''PATH'' on every command execution attempt). Since it has to do a ''PATH'' search, it can be used for this check.

For example, to check if the command ''ls'' is available in a location accessible by ''PATH'':
<code>
if hash ls >/dev/null 2>&1; then
  echo &quot;ls is available&quot;
fi
</code>

Somewhat of a mass-check:
<code>
for name in ls grep sed awk; do
  if ! hash &quot;$name&quot; >/dev/null 2>&1; then
    echo &quot;FAIL: Missing command '$name'&quot;
    exit 1
  fi
done
</code>

Here (bash 3), ''hash'' also respects builtin commands. I don't know if this works everywhere, but it seems logical.

=== command ===
The ''command'' command is used to explicitly call an external command, rather than a builtin with the same name. For exactly this reason, it has to do a ''PATH'' search, and can be used for this check.

For example, to check if the command ''sed'' is available in a location accessible by ''PATH'':
<code>
if command -v sed >/dev/null 2>&1; then
  echo &quot;sed is available&quot;
fi
</code>
