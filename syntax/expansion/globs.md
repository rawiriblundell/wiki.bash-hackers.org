====== Pathname expansion (globbing) ======

===== General =====

Unlike on other platforms you may have seen, on UNIX(r), the shell is responsible for interpreting and expanding globs (&quot;filename wildcards&quot;). A called program will never see the glob itself; it will only see the expanded filenames as its arguments (here, all filenames matching ''*.log''):
<code>
grep &quot;changes:&quot; *.log
</code>

The base syntax for the pathname expansion is the [[syntax:pattern | pattern matching]] syntax. The pattern you describe is matched against all existing filenames and the matching ones are substituted. Since this substitution happens **after [[syntax:expansion:wordsplit | word splitting]]**, all resulting filenames are literal and treated as separate words, no matter how many spaces or other ''IFS''-characters they contain.


===== Normal behaviour =====

  * with [[commands:builtin:set | the set command]] (''-f'', ''noglob'') you can entirely disable pathname expansion
  * when matching a pathname, the slash-character (''/'') always needs to be matched explicitly
  * the dot at the beginning of a filename must be matched explicitly (also one following a ''/'' in the glob)
  * a glob that doesn't match a filename is unchanged and remains what it is




===== Customization =====

  * when the shell option ''[[internals:shell_options#nullglob | nullglob]]'' is set, non-matching globs are removed, rather than preserved
  * when the shell option ''[[internals:shell_options#failglob | failglob]]'' is set, non-matching globs produce an error message and the current command is not executed
  * when the shell option ''[[internals:shell_options#nocaseglob | nocaseglob]]'' is set, the match is performed case-insensitive
  * when the shell option ''[[internals:shell_options#dotglob | dotglob]]'' is set, wildcard-characters can match a dot at the beginning of a filename
  * when the shell option ''[[internals:shell_options#dirspell | dirspell]]'' is set, Bash performs spelling corrections when matching directory names
  * when the shell option ''[[internals:shell_options#globstar | globstar]]'' is set, the glob ''<nowiki>**</nowiki>'' will recursively match all files and directories. This glob isn't &quot;configurable&quot;, i.e. you **can't** do something like ''<nowiki>**</nowiki>.c'' to recursively get all ''*.c'' filenames.
  * when the shell option ''[[internals:shell_options#globasciiranges | globasciiranges]]'' is set, the bracket-range globs (e.g. ''[A-Z]'') use C locale order rather than the configured locale's order (i.e. ''ABC...abc...'' instead of e.g. ''AaBbCc...'') - since 4.3-alpha
  * the variable [[syntax:shellvars#GLOBIGNORE|GLOBIGNORE]] can be set to a colon-separated list of patterns to be removed from the list before it is returned

==== nullglob ====

Normally, when no glob specified matches an existing filename, no pathname expansion is performed, and the globs are __**not**__ removed:
<code>
$ echo &quot;Textfiles here:&quot; *.txt
Textfiles here: *.txt
</code>
In this example, no files matched the pattern, so the glob was left intact (a literal asterisk, followed by dot-txt).

This can be very annoying, for example when you drive a [[syntax:ccmd:classic_for | for-loop]] using the pathname expansion:
<code>
for filename in *.txt; do
  echo &quot;=== BEGIN: $filename ===&quot;
  cat &quot;$filename&quot;
  echo &quot;=== END: $filename ===&quot;
done
</code>
When no file name matches the glob, the loop will not only output stupid text (&quot;''BEGIN: *.txt''&quot;), but also will make the ''cat''-command fail with an error, since no file named ''*.txt'' exists.

Now, when the shell option ''[[internals:shell_options#nullglob | nullglob]]'' is set, Bash will remove the entire glob from the command line. In case of the for-loop here, not even one iteration will be done. It just won't run.

So in our first example:
<code>
$ shopt -s nullglob
$ echo &quot;Textfiles here:&quot; *.txt
Textfiles here:
</code>

and the glob is gone.


====Glob characters=====

  * * - means 'match any number of characters'.  '/' is not matched (and depending on your settings, things like '.' may or may not be matched, see above)
  * ? - means 'match any single character'
  * [abc] - match any of the characters listed. This syntax also supports ranges, like [0-9]




For example, to match something beginning with either 'S' or 'K' followed by two numbers, followed by at least 3 more characters:

<code>[SK][0-9][0-9]???*</code>




===== See also =====
  * [[syntax:expansion:intro | Introduction to expansion and substitution]]
  * [[syntax:pattern | pattern matching syntax]]
  * [[commands:builtin:set | the set builtin command]]
  * [[commands:builtin:shopt | the shopt builtin command]]
  * [[internals:shell_options | list of shell options]]
