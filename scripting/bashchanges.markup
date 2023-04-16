====== Bash changes ======

This article is an **incomplete overview** of changes to Bash over time. Not all changes are listed, just the ones most likely to be useful for normal scripting. The overviews are categorized by topic and ordered by version.

A useful starting point is the [[https://github.com/bminor/bash/blob/master/NEWS|NEWS file]] in bash sources. If you have more detailed information, or historical information about Bash versions earlier than V2, feel free to mail me, or use the discussion below.

Status: 5.1 (alpha)

===== Shell options =====
Note that the ''shopt'' builtin command first appeared in Bash 2.0.

For this topic, see also
  * [[internals:shell_options]]
  * [[commands:builtin:set]]

^ Feature or change description      ^ Appeared in Bash version  ^ See also/remarks                                                                 ^
| ''posix'' (for ''set -o'')         | 1.14.0                    |                                                                                  |
| ''hostcomplete''                   | 2.0-alpha3                |                                                                                  |
| ''expand_aliases''                 | 2.0                       |                                                                                  |
| ''huponexit''                      | 2.02-alpha1               |                                                                                  |
| ''nocaseglob''                     | 2.02-alpha1               |                                                                                  |
| ''extglob''                        | 2.02-alpha1               | together with extended globbing, KSH88                                           |
| ''restricted_shell''               | 2.03-alpha                |                                                                                  |
| ''xpg_echo''                       | 2.04-beta1                |                                                                                  |
| ''progcomp''                       | 2.04-alpha1               |                                                                                  |
| ''no_empty_command_completion''    | 2.04                      |                                                                                  |
| ''login_shell''                    | 2.05a-alpha1              |                                                                                  |
| ''nolog'' (for ''set -o'')         | 2.05a                     |                                                                                  |
| ''gnu_errfmt''                     | 3.0-alpha                 |                                                                                  |
| ''force_fignore''                  | 3.0-alpha                 |                                                                                  |
| ''failglob''                       | 3.0-alpha                 |                                                                                  |
| ''extquote''                       | 3.0-alpha                 | unsure -- verify!                                                                |
| ''extdebug''                       | 3.0-alpha                 |                                                                                  |
| ''pipefail'' (for ''set -o'')      | 3.0                       |                                                                                  |
| ''functrace'' (for ''set -o'')     | 3.0                       |                                                                                  |
| ''errtrace'' (for ''set -o'')      | 3.0                       |                                                                                  |
| ''nocasematch''                    | 3.1-alpha1                |                                                                                  |
| ''dirspell''                       | 4.0-alpha                 |                                                                                  |
| ''globstar''                       | 4.0-alpha                 |                                                                                  |
| ''checkjobs''                      | 4.0-alpha                 |                                                                                  |
| ''autocd''                         | 4.0-alpha                 |                                                                                  |
| ''set -e'' effects more intuitive  | 4.0                       | not directly specified by POSIX, but in consensus with POSIX WG                  |
| ''compat40''                       | 4.1-beta                  |                                                                                  |
| ''lastpipe''                       | 4.2-alpha                 | only works with job control disabled                                             |
| ''compat41''                       | 4.2-alpha                 |                                                                                  |
| ''globasciiranges''                | 4.3-alpha                 | enable &quot;character range globbing&quot; to always act as if in ''C'' locale            |
| ''compat42''                       | 4.3-alpha                 |                                                                                  |
| ''compat43''                       | 4.4-alpha                 |                                                                                  |
| ''compat44''                       | 5.0-alpha                 |                                                                                  |
| ''localvar_inherit''               | 5.0-alpha                 | local variables inherit preceeding scope values if they have the same name       |
| ''syslog_history''                 | 5.0-alpha                 | send history lines to syslog (undocumented, default off) if syslog is supported  |
| ''assoc_expand_once''              | 5.0-alpha                 | expand associative array subscripts only one                                     |
| ''globasciiranges''                | 5.0-beta                  | New default: on (default may be configured at compile time)                      |
| ''localvar_inherit''               | 5.0-beta                  | guard code against inheriting from an incompatible data type                     |
| ''checkwinsize''                   | 5.0-beta2                 | New default: on                                                                  |
| ''shift_verbose''                  | 5.0-beta2                 | Default on when in POSIX mode                                                    |

==== General (all/many builtins) ====
^Feature or change description  ^Appeared in Bash version  ^See also/remarks  ^
|generally return 2 on usage error  |2.0  |  |
|generally accept ''<nowiki>--</nowiki>'' (end of options)  |2.0  |  |
|(where applicable) implement a ''-p'' option to produce reusable output  |2.0  |''shopt'' and ''umask'' builtins were fixed to support that in 2.02  |

==== printf ====

For this topic, see also
  * [[commands:builtin:printf]]

^ Feature or change description                                  ^ Appeared in Bash version  ^ See also/remarks                              ^
| new ''printf'' command                                         | 2.02-alpha1               |                                               |
| respects ''0..'' and ''0x..'' prefixed numbers                 | 2.04-beta1                | consistency with arithmetic                   |
| POSIX(r) length specifiers ''j'', ''t'' and ''z''              | 2.05a-alpha1              | ISO C99                                       |
| POSIX(r) flag ''%%'%%''                                        | 2.05a-alpha1              |                                               |
| conversion ''a'' and ''A''                                     | 2.05a-rc1                 | if provided by the underlying printf(3)       |
| conversion ''F''                                               | 2.05a-rc1                 |                                               |
| conversion ''n''                                               | 2.05a-rc1                 |                                               |
| new option ''-v''                                              | 3.1-alpha1                |                                               |
| escape sequences ''\&quot;'' and ''\?''                             | 3.0-beta1                 |                                               |
| modified option ''-v'' to assign to individual array elements  | 4.1-alpha                 |                                               |
| conversion ''(...)T''                                          | 4.2-alpha                 | support stftime(3) date/time format; uses current time |
| ''\uNNNN'' and ''\UNNNNNNNN'' escape sequences                 | 4.2-alpha                 | for: ''printf'', ''echo -e'', ''%%$'...'%%''  |





==== Conditional expressions and test command ====

For this topic, see also
  * [[syntax:ccmd:conditional_expression]]
  * [[commands:classictest]]

^ Feature or change description                                               ^ Appeared in Bash version  ^ See also/remarks                                                                     ^
| ''test'': ''-o'', ''=='', ''<'' and ''>''                                   | 2.0                       |                                                                                      |
| ''test'': ''-N''                                                            | 2.02                      |                                                                                      |
| ''%%[[...]]%%'': new                                                        | 2.02-alpha1               | KSH93                                                                                |
| ''%%[[...]]%%'': regex support (''=~'')                                     | 3.0-alpha                 |                                                                                      |
| ''%%[[...]]%%'': quotable right-hand-side of ''=~'' forces string matching  | 3.2-alpha                 | for consistency with pattern matching                                                |
| ''%%[[...]]%%'': ''<'' and ''>'' operators respect locale                   | 4.1-alpha                 | for consistency, since 4.1-beta: ensure you have set compatiblity to >4.0 (default)  |
| ''test''/''%%[%%''/''%%[[%%'': ''-v''                                       | 4.2-alpha                 | check if a variable is set                                                           |
| ''test''/''%%[%%''/''%%[[%%'': ''-v''                                       | 4.2-alpha                 | support array syntax to check for elements                                           |
| ''test''/''%%[%%''/''%%[[%%'': ''-N'' accepts nanoseconds                   | 5.1-alpha                 |                                                                                      |
| ''test''/''%%[%%''/''%%[[%%'': ''-v'' accepts positional parameters         | 5.1-alpha                 |                                                                                      |

==== Other builtins and keywords ====
^ Builtin                      ^ Feature or change description                                                                                                                     ^ Appeared in Bash version  ^ See also/remarks                                                                                     ^
| ''bashbug''                  | new                                                                                                                                               | 1.14.0                    |                                                                                                      |
| ''select''                   | new                                                                                                                                               | 1.14.0                    |                                                                                                      |
| ''disown''                   | new                                                                                                                                               | 2.0                       |                                                                                                      |
| ''shopt''                    | new                                                                                                                                               | 2.0                       | [[commands:builtin:shopt]]                                                                           |
| ''declare''                  | new options ''-a'' and ''-F''                                                                                                                     | 2.0                       |                                                                                                      |
| ''enable''                   | builtin has basic plugin support (dlopen)                                                                                                         | 2.0                       |                                                                                                      |
| ''exec''                     | options ''-l'', ''-c'' and ''-a''                                                                                                                 | 2.0                       |                                                                                                      |
| ''read''                     | options ''-p'', ''-e'' and ''-a''                                                                                                                 | 2.0                       | [[commands:builtin:read]]                                                                            |
| ''readonly''                 | option ''-a''                                                                                                                                     | 2.0                       | [[syntax:arrays]]                                                                                    |
| ''time''                     | new keyword                                                                                                                                       | 2.0                       |                                                                                                      |
| ''shopt''                    | ''-p'' (reusable output)                                                                                                                          | 2.02                      |                                                                                                      |
| ''umask''                    | ''-p'' (reusable output)                                                                                                                          | 2.02                      |                                                                                                      |
| ''complete''                 | new                                                                                                                                               | 2.04-devel                | for and together with support for programmable completion                                            |
| ''compgen''                  | new                                                                                                                                               | 2.04-devel                | for and together with support for programmable completion                                            |
| ''read''                     | options ''-t'', ''-n'', ''-d'', ''-s''                                                                                                            | 2.04-devel                | [[commands:builtin:read]]                                                                            |
| ''%%for ((...;...;...))%%''  | new                                                                                                                                               | 2.04-devel                | KSH93                                                                                                |
| ''set''                      | print shell functions in a format reusable as input                                                                                               | 2.05-beta1                |                                                                                                      |
| ''for''                      | allow an empty word list                                                                                                                          | 2.05a-alpha1              |                                                                                                      |
| ''read''                     | new option ''-u''                                                                                                                                 | 2.05b-alpha1              | [[commands:builtin:read]]                                                                            |
| ''caller''                   | new                                                                                                                                               | 3.0                       | [[commands:builtin:caller]]                                                                          |
| ''coproc''                   | new                                                                                                                                               | 4.0-alpha                 |                                                                                                      |
| ''declare''                  | new options ''-l'' and ''-u''                                                                                                                     | 4.0-alpha                 | together with case-changing expansion forms                                                          |
| ''case''                     | new action list terminators ''%%;;&%% and ''%%;&%%                                                                                                | 4.0-alpha                 | ksh93: only '';&''. zsh and mksh: '';|''. mksh: all 4, ('';;&'' is undocumented Bash compatibility)  |
| ''read''                     | changed ''-t'' (fractional seconds)                                                                                                               | 4.0-alpha                 |                                                                                                      |
| ''mapfile''                  | new                                                                                                                                               | 4.0-alpha                 |                                                                                                      |
| ''read''                     | new option ''-i''                                                                                                                                 | 4.0-alpha                 |                                                                                                      |
| ''compopt''                  | new                                                                                                                                               | 4.0-alpha                 |                                                                                                      |
| ''read''                     | modified option ''-t'' to test for data                                                                                                           | 4.0-beta                  |                                                                                                      |
| ''read''                     | new option ''-N''                                                                                                                                 | 4.1-alpha                 |                                                                                                      |
| ''mapfile''                  | changed behaviour regarding history spamming                                                                                                      | 4.1-alpha                 |                                                                                                      |
| ''declare''                  | new option ''-g''                                                                                                                                 | 4.2-alpha                 |                                                                                                      |
| ''mapfile''                  | calls the callback with an additional argument: The line (data)                                                                                   | 4.2-alpha                 |                                                                                                      |
| ''cd''                       | new option ''-e''                                                                                                                                 | 4.2-alpha                 |                                                                                                      |
| ''echo''                     | ''\uNNNN'' and ''\UNNNNNNNN'' escape sequences                                                                                                    | 4.2-alpha                 | for: ''printf'', ''echo -e'', ''%%$'...'%%''                                                         |
| ''exec''                     | option ''-a'' to give a ''argv[0]'' string                                                                                                        | 4.2-alpha                 |                                                                                                      |
| ''time''                     | allowed as a command by itself to display timing values of the shell and its children                                                             | 4.2-alpha                 | POSIX change                                                                                         |
| ''help''                     | ''help'' now searches exact topic-strings (i.e. ''help read'' won't find ''readonly'' anymore)                                                    | 4.3-alpha                 |                                                                                                      |
| ''return''                   | accept negative values as return value (e.g. ''return -1'' will show as (8 bit) 255 in the caller)                                                | 4.3-alpha                 |                                                                                                      |
| ''exit''                     | accept negative values as return value (e.g. ''return -1'' will show as (8 bit) 255 in the caller)                                                | 4.3-alpha                 |                                                                                                      |
| ''read''                     | ''read'' skips ''NUL'' (ASCII Code 0) in input                                                                                                    | 4.3-alpha                 |                                                                                                      |
| ''declare''                  | new option ''-n''/''+n'' to support nameref variable type                                                                                         | 4.3-alpha                 |                                                                                                      |
| ''wait''                     | new option ''-n'' to wait for the next background job to finish, returning its exit status.                                                       | 4.3-alpha                 |                                                                                                      |
| ''read''                     | ''read'' checks first variable argument for validity before trying to read inout                                                                  | 4.3-beta                  |                                                                                                      |
| ''help''                     | attempts substring matching (as it did through bash-4.2) if exact string matching fails                                                           | 4.3-beta2                 |                                                                                                      |
| ''fc''                       | interprets option ''-0'' (zero) as the current command line                                                                                       | 4.3-beta2                 |                                                                                                      |
| ''cd''                       | new option ''-@'' to browse a file's extended attributes (on systems that support ''O_XATTR'')                                                    | 4.3-rc1                   |                                                                                                      |
| ''kill''                     | new option ''-L'' (upper case ell) to list signals like the normal lowercase option ''-l'' (compatiblity with some standalone ''kill'' commands)  | 4.4-beta                  |                                                                                                      |
| ''mapfile''                  | new option ''-d''                                                                                                                                 | 4.4-alpha                 |                                                                                                      |
| ''wait''                     | new option ''-f''                                                                                                                                 | 5.0-alpha                 |                                                                                                      |
| ''history''                  | option ''-d'' allows negative numbers to index from the end of the history list                                                                   | 5.0-alpha                 |                                                                                                      |
| ''umask''                    | allows modes greater than octal 777                                                                                                               | 5.0-alpha                 |                                                                                                      |
| ''times''                    | honors current locale settings when printing decimal points                                                                                       | 5.0-alpha                 |                                                                                                      |
| ''kill''                     | New options ''-n SIGNUMBER'' and ''-s SIGNAME''                                                                                                   | 5.0-beta2                 | [[commands:builtin:kill]]                                                                            |
| ''select''                   | Support for an empty wordlist following ''in''                                                                                                    | 5.0-beta2                 |                                                                                                      |
| ''read''                     | Option ''-e'' (use ReadLine to obtain input) now works with arbitrary file descriptors (given by ''-u'' option)                                   | 5.1-alpha                 |                                                                                                      |
| ''trap''                     | ''-p'' option prints signals with SIG_DFL/SIG_IGN on shell start (POSIX mode)                                                                     | 5.1-alpha                 |                                                                                                      |
| ''unset''                    | automatically tries to unset a function if the given name is an invalid variable name                                                             | 5.1-aplha                 |                                                                                                      |
| ''wait''                     | option ''-n'' now accepts a list of jobs                                                                                                          | 5.1-alpha                 |                                                                                                      |
| ''wait''                     | new option ''-p NAME'' to store PID/JobID (useful when waiting for a list of jobs)                                                                | 5.1-alpha                 |                                                                                                      |
| ''local''                    | new option ''-p'' to print local variables in the current scope                                                                                   | 5.1-alpha                 |                                                                                                      |
| ''ulimit''                   | new option ''-R'' to get/set ''RLIMIT_RTTIME'' resource                                                                                           | 5.1-alpha                 |                                                                                                      |


===== Builtin variables =====
^ Feature or change description                           ^ Appeared in Bash version  ^ See also                                                                            ^
| ''HISTCMD''                                             | 1.14.0                    | interactive usage                                                                   |
| ''PS1'', ''PS2'', ''PATH'', and ''IFS'' are unsettable  | 2.0                       |                                                                                     |
| ''DIRSTACK'' array variable                             | 2.0                       |                                                                                     |
| ''PIPESTATUS'' array variable                           | 2.0                       |                                                                                     |
| ''BASH_VERSINFO'' array variable                        | 2.0                       |                                                                                     |
| ''HOSTNAME''                                            | 2.0                       |                                                                                     |
| ''SHELLOPTS''                                           | 2.0                       |                                                                                     |
| ''MACHTYPE''                                            | 2.0                       |                                                                                     |
| ''GLOBIGNORE''                                          | 2.0                       |                                                                                     |
| ''HISTIGNORE''                                          | 2.0                       |                                                                                     |
| respect ''LC_ALL''                                      | 2.0                       |                                                                                     |
| respect ''LC_MESSAGES''                                 | 2.0                       |                                                                                     |
| respect ''LC_CTYPE''                                    | 2.0                       |                                                                                     |
| respect ''LC_COLLATE''                                  | 2.0                       |                                                                                     |
| respect ''LANG''                                        | 2.0                       |                                                                                     |
| ''GROUPS'' array variable                               | 2.01                      |                                                                                     |
| ''GROUPS'' unsettable/takes (discarded) assignments     | 2.04                      |                                                                                     |
| ''FUNCNAME''                                            | 2.04                      |                                                                                     |
| respect ''LC_NUMERIC''                                  | 2.04                      |                                                                                     |
| ''TMOUT''                                               | 2.05b                     |                                                                                     |
| ''BASH_REMATCH''                                        | 3.0                       | together with regex support in ''%%[[...]]%%''                                      |
| ''BASH_ARGC''                                           | 3.0                       | debugger support                                                                    |
| ''BASH_ARGV''                                           | 3.0                       | debugger support                                                                    |
| ''BASH_SOURCE''                                         | 3.0                       | debugger support                                                                    |
| ''BASH_LINENO''                                         | 3.0                       | debugger support                                                                    |
| ''BASH_SUBSHELL''                                       | 3.0                       | debugger support                                                                    |
| ''BASH_EXECUTION_STRING''                               | 3.0                       | debugger support                                                                    |
| ''BASH_COMMAND''                                        | 3.0                       | debugger support                                                                    |
| ''HISTTIMEFORMAT''                                      | 3.0                       |                                                                                     |
| ''COMP_WORDBREAKS''                                     | 3.0                       |                                                                                     |
| respect ''LC_TIME''                                     | 3.1                       |                                                                                     |
| ''BASHPID''                                             | 4.0-alpha                 | Added to mksh R41.                                                                  |
| ''PROMPT_DIRTRIM''                                      | 4.0                       |                                                                                     |
| ''BASH_XTRACEFD''                                       | 4.1-alpha                 |                                                                                     |
| ''BASHOPTS''                                            | 4.1-alpha                 |                                                                                     |
| ''FUNCNEST''                                            | 4.2-alpha                 |                                                                                     |
| ''HISTSIZE''                                            | 4.3-alpha                 | can be set to negative values for unlimited history length                          |
| ''HISTFILESIZE''                                        | 4.3-alpha                 | can be set to negative values for unlimit history file size                         |
| ''CHILD_MAX''                                           | 4.3-alpha                 | max. number of exit status of children the shell remembers                          |
| ''BASH_COMPAT''                                         | 4.3-alpha                 | set shell compatiblity levels                                                       |
| ''EPOCHSECONDS''                                        | 5.0-alpha                 | expands to the time in seconds since Unix epoch                                     |
| ''EPOCHREALTIME''                                       | 5.0-alpha                 | expands to the time in seconds since Unix epoch with microsecond granularity        |
| ''BASH_ARGV0''                                          | 5.0-alpha                 | get/set ''$0''                                                                      |
| ''PATH''                                                | 5.0-alpha                 | Possibility to set a static path for use in a restricted shell (at compile time)    |
| ''HISTSIZE''                                            | 5.0-beta                  | Default can now be set at runtime                                                   |
| ''SRANDOM''                                             | 5.1-alpha                 | New random generator for 32bit numbers (using various methods in the backend)       |
| ''ARGV0''                                               | 5.1-alpha                 | Respected when set in initial shell environment, then initially used to set ''$0''  |
| ''BASH_REMATCH''                                        | 5.1-alpha                 | Not readonly anymore                                                                |
| ''PROMPT_COMMANDS''                                     | 5.1-alpha                 | New array variable. List of commands to be executed like ''PROMPT_COMMAND''         |
| ''SECONDS''                                             | 5.1-alpha                 | Assignment using arithmetic expressions (is nominally an integer variabnle)         |
| ''RANDOM''                                              | 5.1-alpha                 | Assignment using arithmetic expressions (is nominally an integer variabnle)         |
| ''LINENO''                                              | 5.1-alpha                 | Not an integer variabe                                                              |

===== Quoting, expansions, substitutions and related =====
For this topic, see also
  * [[syntax:pe]].

^ Feature or change description                                                                                 ^ Appeared in Bash version  ^ Remarks                                                                                                          ^
| Support for integer-indexed arrays                                                                            | 2.0                       | relevant builtins also got array support                                                                         |
| ''%%${PARAMETER//PATTERN/REPLACEMENT}%%''                                                                     | 2.0                       |                                                                                                                  |
| ''%%${PARAMETER:OFFSET:LENGTH}%%''                                                                            | 2.0                       |                                                                                                                  |
| ''%%${!PARAMETER}%%'' (indirection)                                                                           | 2.0                       |                                                                                                                  |
| ''%%$&quot;...&quot;%%'' (localized strings)                                                                            | 2.0                       |                                                                                                                  |
| ''%%$'...'%%'' (ANSI-C-like strings)                                                                          | 2.0                       |                                                                                                                  |
| ''%%\xNNN%%'' in ''%%$'...'%%'' (and ''echo -e'')                                                             | 2.02-alpha1               |                                                                                                                  |
| ''%%$(< FILENAME)%%'' (file content)                                                                          | 2.02-alpha1               |                                                                                                                  |
| globbing (''fnmatch()'') capable of POSIX(r) character classes etc.                                           | 2.02-alpha1               |                                                                                                                  |
| extended globbing                                                                                             | 2.02-alpha1               | KSH88                                                                                                            |
| globbing inside array mass-assignment: ''ARRAY=(*.txt)''                                                      | 2.03-alpha                |                                                                                                                  |
| ''%%$'...\'...'%%'' escaped single quote inside ANSI-C-like strings                                           | 2.04-devel                | KSH93                                                                                                            |
| ''%%${!PREFIX*}%%'' (parameter name expansion)                                                                | 2.04                      | KSH93                                                                                                            |
| ''%%$'...'%%'' expands ''\cx'' (Control-x)                                                                    | 2.05b                     |                                                                                                                  |
| ''[:class:]'' syntax for pattern matching                                                                     | 2.05b                     | KSH93                                                                                                            |
| ''${!ARRAY[@]}'' (array index expansion)                                                                      | 3.0-alpha                 | KSH93                                                                                                            |
| ''{x..y}'' (range brace expansion)                                                                            | 3.0-alpha                 |                                                                                                                  |
| ''%%$'...'%%'' expands ''\xNNN'' (Hexdigits)                                                                  | 3.0                       |                                                                                                                  |
| ''+='' operator for arrays and strings                                                                        | 3.1-alpha1                |                                                                                                                  |
| ''%%${PARAMETER//PATTERN/REPLACEMENT}%%'' behaviour changed                                                   | 3.2-alpha                 | anchoring for global substitution is no longer allowed, changes the way old syntax may work                      |
| ''${@:0:x}'' includes ''$0''                                                                                  | 4.0-alpha                 |                                                                                                                  |
| Support for associative arrays                                                                                | 4.0-alpha                 | relevant builtins also got associative array support                                                             |
| case modification operators for expansions                                                                    | 4.0-alpha                 |                                                                                                                  |
| ''{0x..0y}'' (zeropadding brace expansion)                                                                    | 4.0-alpha                 |                                                                                                                  |
| numerically indexed arrays can be accessed (on expansion time) from the end using negative indexes            | 4.1-alpha                 |                                                                                                                  |
| ''%%\uNNNN%%'' and ''%%\uNNNNNNNN%%'' in ''%%$'...'%%''                                                       | 4.2-alpha                 | for: ''printf'', ''echo -e'', ''%%$'...'%%''                                                                     |
| ''%%${PARAMETER:OFFSET:LENGTH}%%'': Negative ''LENGTH'' values are used as offset from the end of the string  | 4.2-alpha                 | Substrings only for Bash and ksh93. Works also for argument expansions in zsh. ksh93 can use ''${arr[n..-m]}''.  |
| Word expansions like ''${foo##bar}'' understand indirect variable references                                  | 4.3-beta                  |                                                                                                                  |
| Transformations                                                                                               | 4.4                       |                                                                                                                  |
| Process substitution now works in POSIX mode                                                                  | 5.1-alpha                 |                                                                                                                  |
| New transformations: ''U'', ''u'', ''L''                                                                      | 5.1-alpha                 | Case-transformation                                                                                              |
| New transformation: ''K''                                                                                     | 5.1-alpha                 | Display associative arrays as key/value pairs                                                                    |




===== Arithmetic =====
For this topic, see also
  * [[syntax:arith_expr]]
  * [[syntax:expansion:arith]]

^Feature or change description  ^Appeared in Bash version  ^Remarks  ^
|''<nowiki>((...))</nowiki>''  |2.0-beta2  |KSH93  |
|ternary operator  |2.0  |  |
|base 64 integer constants  |2.0  |the max. base before is unknown. Anybody?  |
|deprecated ''$[...]'' in favor of ''<nowiki>$((...))</nowiki>''  |2.0  |  |
|exponentiaition operator (''<nowiki>**</nowiki>'')  |2.02-alpha1  |  |
|comma operator ''EXPR,EXPR''  |2.04-devel  |  |
|pre- and postfix operators  |2.04-devel  |  |


===== Redirection and related =====
For this topic, see also
  * [[syntax:redirection]]

^ Feature or change description                                                     ^ Appeared in Bash version  ^ Remarks  ^
| socket redirection (''/dev/tcp/'', ''/dev/udp/'')                                 | 2.04-devel                |          |
| OS/filesystem-independent support for ''/dev/std(in|out|err)'' and ''/dev/fd/*''  | 2.04                      |          |
| socket redirection accepts service names                                          | 2.05                      |          |
| ''[n]<&word-'' and ''[n]>&word-'' FD-duplicate/closing                            | 2.05b-alpha1              | KSH93    |
| Here strings: ''%%<<< WORD%%''                                                    | 2.05b-alpha1              |          |
| ''|&'' (synonym for ''2>&1 |'')                                                   | 4.0-alpha                 |          |
| ''%%&>>%%'' (equiv. to ''%%>>FILE 2>&1%%'')                                       | 4.0-alpha                 |          |
| ''<nowiki>{varname}</nowiki>'' style automatic file descriptor allocation         | 4.1-alpha                 | ksh93    |
| ''<nowiki>{varname[idx]}</nowiki>'' fd allocation accepts array subscripts and special-meaning variables       | 4.3-alpha                 | ksh93    |


===== Misc =====

^ Feature or change description                                                                                    ^ Appeared in Bash version  ^ See also/remarks                                                                                                                           ^
| ''DEBUG'' trap                                                                                                   | 2.0                       |                                                                                                                                            |
| ''ERR'' trap                                                                                                     | 2.05a                     | KSH93                                                                                                                                      |
| Support for multibyte characters: Unicode / UTF8                                                                 | 2.05b                     |                                                                                                                                            |
| ''RETURN'' trap                                                                                                  | 3.0                       | ksh93 ''EXIT'' trap evaluates in caller scope (for ''function name {''). Bash ''RETURN'' in same scope.                                    |
| ''command_not_found_handle'' handler function                                                                    | 4.0-alpha                 |                                                                                                                                            |
| official introduction of switchable &quot;compatiblity levels&quot;                                                        | 4.0-alpha                 | ''compat31'' was introduced in a 3.2 version, mainly because of the incompatibilities that were introduced by the changed ''=~'' operator  |
| ''%%[[...]]%%'' and ''%%((...))%%'' conditional commands are subject to the ''ERR'' trap and ''set -e'' feature  | 4.1-alpha                 |                                                                                                                                            |
| ACL support for file status checks                                                                               | 4.1-alpha                 |                                                                                                                                            |
| Assignment to negative array indices                                                                             | 4.3-alpha                 | ksh93, zsh                                                                                                                                 |
| ''declare''/''typeset -n''                                                                                       | 4.3-alpha                 | Support for nameref variable type, a variable referencing another one by name                                                              |
| shells started to run process substitutions now run any trap set on ''EXIT''                                     | 4.3-beta                  |                                                                                                                                            |
| process substitution does not inherit the ''v'' flag                                                             | 5.0-alpha                 |                                                                                                                                            |
| ''ERR'' trap                                                                                                     | 5.0-alpha                 | Reports more reliable line numbers                                                                                                         |
| Variable assignment                                                                                              | 5.0-beta                  | Assignments preceeding a special builtin that chages variable attributes are not propagated back unless compatiblity mode is 44 or lower   |
