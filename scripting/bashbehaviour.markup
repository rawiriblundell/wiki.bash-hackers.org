====== Bash's behaviour ======

{{keywords>bash shell scripting startup files dotfiles modes POSIX}}

FIXME incomplete


===== Bash startup modes =====


==== Login shell ====


As a &quot;login shell&quot;, Bash reads and sets (executes) the user's profile from ''/etc/profile'' and one of ''~/.bash_profile'', ''~/.bash_login'', or ''~/.profile'' (in that order, using the first one that's readable!).

When a login shell exits, Bash reads and executes commands from the file ''~/.bash_logout'', if it exists.

Why an extra login shell mode? There are many actions and variable sets that only make sense for the initial user login. That's why all UNIX(r) shells have (should have) a &quot;login&quot; mode.

__**Methods to start Bash as a login shell:**__
  * the first character of ''argv[0]'' is ''-'' (a hyphen): traditional UNIX(r) shells start from the ''login'' binary
  * Bash is started with the ''-l'' option
  * Bash is started with the ''<nowiki>--login</nowiki>'' option

__**Methods to test for login shell mode:**__
  * the shell option ''[[internals:shell_options#login_shell |login_shell]]'' is set

__**Related switches:**__
  * ''<nowiki>--noprofile</nowiki>'' disables reading of all profile files





==== Interactive shell ====

When Bash starts as an interactive non-login shell, it reads and executes commands from ''~/.bashrc''. This file should contain, for example, aliases, since they need to be defined in every shell as they're not inherited from the parent shell.

The feature to have a system-wide ''/etc/bash.bashrc'' or a similar system-wide rc-file is specific to vendors and distributors that ship //their own, patched variant of Bash//. The classic way to have a system-wide rc file is to ''source /etc/bashrc'' from every user's ''~/.bashrc''.

__**Methods to test for interactive-shell mode:**__
  * the special parameter ''$-'' contains the letter ''i'' (lowercase I)

__**Related switches:**__
  * ''-i'' forces the interactive mode
  * ''<nowiki>--norc</nowiki>'' disables reading of the startup files (e.g. ''/etc/bash.bashrc'' if supported) and ''~/.bashrc''
  * ''<nowiki>--rcfile</nowiki>'' defines another startup file (instead of ''/etc/bash.bashrc'' and ''~/.bashrc'')




==== SH mode ====

When Bash starts in SH compatiblity mode, it tries to mimic the startup behaviour of historical versions of ''sh'' as closely as possible, while conforming to the POSIX(r) standard as well. The profile files read are ''/etc/profile'' and ''~/.profile'', if it's a login shell.

If it's not a login shell, the environment variable [[syntax:shellvars#ENV|ENV]] is evaluated and the resulting filename is used as the name of the startup file.

After the startup files are read, Bash enters the [[#posix_run_mode | POSIX(r) compatiblity mode (for running, not for starting!)]].

__**Bash starts in ''sh'' compatiblity mode when:**__
  * the base filename in ''argv[0]'' is ''sh'' (:!: NB: ''/bin/sh'' may be linked to ''/bin/bash'', but that doesn't mean it acts like ''/bin/bash'' :!:)





==== POSIX mode ====

When Bash is started in POSIX(r) mode, it follows the POSIX(r) standard for startup files. In this mode, **interactive shells** expand the [[syntax:shellvars#ENV|ENV]] variable and commands are read and executed from the file whose name is the expanded value.\\
No other startup files are read. Hence, a non-interactive shell doesn't read any startup files in POSIX(r) mode.

__**Bash starts in POSIX(r) mode when:**__
  * the commandline option ''<nowiki>--posix</nowiki>'' is specified
  * the environment variable [[syntax:shellvars#POSIXLY_CORRECT|POSIXLY_CORRECT]] is set




==== Quick startup file reference ====

  * Eventual system-wide rc-files are usually read when ''~/.bashrc'' would be read (at least Debian GNU/Linux behaves like that)
  * Regardless of the system-wide files in ''/etc'' which are always read, Bash usually reads the first file found, when multiple choices are given (for  user files in ''~/'')

^Mode^''/etc/profile''^''~/.bash_profile''^''~/.bash_login''^''~/.profile''^''~/.bashrc''^''${ENV}''^
|Login shell           |  X  |  X  |  X  |  X  |  -  |  -  |
|Interactive shell     |  -  |  -  |  -  |  -  |  X  |  -  |
|SH compatible login   |  X  |  -  |  -  |  X  |  -  |  -  |
|SH compatible         |  -  |  -  |  -  |  -  |  -  |  X  |
|POSIX(r) compatiblity |  -  |  -  |  -  |  -  |  -  |  X  |


===== Bash run modes =====


==== Normal Bash ====







==== POSIX run mode ====

In POSIX(r) mode, Bash follows the POSIX(r) standard regarding behaviour and parsing (excerpt from a Bash maintainer's document):
<code>
Starting Bash with the `--posix' command-line option or executing `set
-o posix' while Bash is running will cause Bash to conform more closely
to the POSIX standard by changing the behavior to match that specified
by POSIX in areas where the Bash default differs.

When invoked as `sh', Bash enters POSIX mode after reading the startup
files.

The following lists what's changed when Bash is in `POSIX mode':

  1. When a command in the hash table no longer exists, Bash will
     re-search `$PATH' to find the new location.  This is also
     available with `shopt -s checkhash'.

  2. The message printed by the job control code and builtins when a job
     exits with a non-zero status is `Done(status)'.

  3. The message printed by the job control code and builtins when a job
     is stopped is `Stopped(SIGNAME)', where SIGNAME is, for example,
     `SIGTSTP'.

  4. The `bg' builtin uses the required format to describe each job
     placed in the background, which does not include an indication of
     whether the job is the current or previous job.

  5. Reserved words appearing in a context where reserved words are
     recognized do not undergo alias expansion.

  6. The POSIX `PS1' and `PS2' expansions of `!' to the history number
     and `!!' to `!' are enabled, and parameter expansion is performed
     on the values of `PS1' and `PS2' regardless of the setting of the
     `promptvars' option.

  7. The POSIX startup files are executed (`$ENV') rather than the
     normal Bash files.

  8. Tilde expansion is only performed on assignments preceding a
     command name, rather than on all assignment statements on the line.

  9. The default history file is `~/.sh_history' (this is the default
     value of `$HISTFILE').

 10. The output of `kill -l' prints all the signal names on a single
     line, separated by spaces, without the `SIG' prefix.

 11. The `kill' builtin does not accept signal names with a `SIG'
     prefix.

 12. Non-interactive shells exit if FILENAME in `.' FILENAME is not
     found.

 13. Non-interactive shells exit if a syntax error in an arithmetic
     expansion results in an invalid expression.

 14. Redirection operators do not perform filename expansion on the word
     in the redirection unless the shell is interactive.

 15. Redirection operators do not perform word splitting on the word in
     the redirection.

 16. Function names must be valid shell names.  That is, they may not
     contain characters other than letters, digits, and underscores, and
     may not start with a digit.  Declaring a function with an invalid
     name causes a fatal syntax error in non-interactive shells.

 17. POSIX special builtins are found before shell functions during
     command lookup.

 18. If a POSIX special builtin returns an error status, a
     non-interactive shell exits.  The fatal errors are those listed in
     the POSIX standard, and include things like passing incorrect
     options, redirection errors, variable assignment errors for
     assignments preceding the command name, etc.

 19. If `CDPATH' is set, the `cd' builtin will not implicitly append
     the current directory to it.  This means that `cd' will fail if no
     valid directory name can be constructed from any of the entries in
     `$CDPATH', even if the a directory with the same name as the name
     given as an argument to `cd' exists in the current directory.

 20. A non-interactive shell exits with an error status if a variable
     assignment error occurs when no command name follows the assignment
     statements.  A variable assignment error occurs, for example, when
     trying to assign a value to a readonly variable.

 21. A non-interactive shell exits with an error status if the iteration
     variable in a `for' statement or the selection variable in a
     `select' statement is a readonly variable.

 22. Process substitution is not available.

 23. Assignment statements preceding POSIX special builtins persist in
     the shell environment after the builtin completes.

 24. Assignment statements preceding shell function calls persist in the
     shell environment after the function returns, as if a POSIX
     special builtin command had been executed.

 25. The `export' and `readonly' builtin commands display their output
     in the format required by POSIX.

 26. The `trap' builtin displays signal names without the leading `SIG'.

 27. The `trap' builtin doesn't check the first argument for a possible
     signal specification and revert the signal handling to the original
     disposition if it is, unless that argument consists solely of
     digits and is a valid signal number.  If users want to reset the
     handler for a given signal to the original disposition, they
     should use `-' as the first argument.

 28. The `.' and `source' builtins do not search the current directory
     for the filename argument if it is not found by searching `PATH'.

 29. Subshells spawned to execute command substitutions inherit the
     value of the `-e' option from the parent shell.  When not in POSIX
     mode, Bash clears the `-e' option in such subshells.

 30. Alias expansion is always enabled, even in non-interactive shells.

 31. When the `alias' builtin displays alias definitions, it does not
     display them with a leading `alias ' unless the `-p' option is
     supplied.

 32. When the `set' builtin is invoked without options, it does not
     display shell function names and definitions.

 33. When the `set' builtin is invoked without options, it displays
     variable values without quotes, unless they contain shell
     metacharacters, even if the result contains nonprinting characters.

 34. When the `cd' builtin is invoked in LOGICAL mode, and the pathname
     constructed from `$PWD' and the directory name supplied as an
     argument does not refer to an existing directory, `cd' will fail
     instead of falling back to PHYSICAL mode.

 35. When the `pwd' builtin is supplied the `-P' option, it resets
     `$PWD' to a pathname containing no symlinks.

 36. The `pwd' builtin verifies that the value it prints is the same as
     the current directory, even if it is not asked to check the file
     system with the `-P' option.

 37. When listing the history, the `fc' builtin does not include an
     indication of whether or not a history entry has been modified.

 38. The default editor used by `fc' is `ed'.

 39. The `type' and `command' builtins will not report a non-executable
     file as having been found, though the shell will attempt to
     execute such a file if it is the only so-named file found in
     `$PATH'.

 40. The `vi' editing mode will invoke the `vi' editor directly when
     the `v' command is run, instead of checking `$FCEDIT' and
     `$EDITOR'.

 41. When the `xpg_echo' option is enabled, Bash does not attempt to
     interpret any arguments to `echo' as options.  Each argument is
     displayed, after escape characters are converted.


There is other POSIX behavior that Bash does not implement by default
even when in POSIX mode.  Specifically:

  1. The `fc' builtin checks `$EDITOR' as a program to edit history
     entries if `FCEDIT' is unset, rather than defaulting directly to
     `ed'.  `fc' uses `ed' if `EDITOR' is unset.

  2. As noted above, Bash requires the `xpg_echo' option to be enabled
     for the `echo' builtin to be fully conformant.


Bash can be configured to be POSIX-conformant by default, by specifying
the `--enable-strict-posix-default' to `configure' when building.
</code>


FIXME help me to find out what breaks in POSIX(r) mode!

__**The POSIX(r) mode can be switched on by:**__
  * Bash starting as ''sh'' (the basename of ''argv[0]'' is ''sh'')
  * starting Bash with the commandline option ''<nowiki>--posix</nowiki>''
  * on startup, the environment variable [[syntax:shellvars#POSIXLY_CORRECT|POSIXLY_CORRECT]] is set
  * the command ''set -o posix''

__**Tests for the POSIX(r) mode:**__
  * the variable [[syntax:shellvars#SHELLOPTS|SHELLOPTS]] contains ''posix'' in its list

==== Restricted shell ====

In restricted mode, Bash sets up (and runs) a shell environment that's far more controlled and limited than the standard shell mode. It acts like normal Bash with the following restrictions:

  * the ''cd'' command can't be used to change directories
  * the variables [[syntax:shellvars#SHELL|SHELL]], [[syntax:shellvars#PATH|PATH]], [[syntax:shellvars#ENV|ENV]] and [[syntax:shellvars#BASH_ENV|BASH_ENV]] can't be set or unset
  * command names that contain a ''/'' (slash) can't be called (hence you're limited to ''PATH'')
  * filenames containing a ''/'' (slash) can't be specified as argument to the ''source'' or ''.'' builtin command
  * filenames containing a ''/'' (slash) can't be specified as argument to the ''-p'' option of the ''hash'' builtin command
  * function definitions are not inherited from the environment at shell startup
  * the environment variable [[syntax:shellvars#SHELLOPTS|SHELLOPTS]] is ignored at startup
  * redirecting output using the ''>'', ''>|'', ''<>'', ''>&'', ''&>'', and ''<nowiki>>></nowiki>'' redirection operators isn't allowed
  * the ''exec'' builtin command can't replace the shell with another process
  * adding or deleting builtin commands with the ''-f'' and ''-d'' options to the enable builtin command is forbidden
  * using the ''enable'' builtin command to enable disabled shell builtins doesn't work
  * the ''-p'' option to the ''command'' builtin command doesn't work
  * turning off restricted mode with ''set +r'' or ''set +o restricted'' is (of course) forbidden

The &quot;-r&quot; restrictions are turned on **after** Bash has read its startup files.

When the command that is run is a shell script, then the restrictions are **turned off** for the (sub-)shell that runs that shell script.


__**The restricted shell can be switched on by:**__
  * calling Bash as ''rbash'' (the basename of ''argv[0]'' is ''rbash'')
  * calling Bash with the ''-r'' option
  * calling Bash with the ''<nowiki>--restricted</nowiki>'' option

__**Tests for restricted mode:**__
  * the special parameter ''$-'' contains the letter ''r'' (lowercase R)
  * the shell option ''[[internals:shell_options#restricted_shell |restricted_shell]]'' is set and can be checked by the ''shopt'' builtin command
