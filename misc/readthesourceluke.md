Comments extracted from the bash source and therefore Copyright (C)
1987-2004 Free Software Foundation, Inc. under the terms of the GNU
General Public License etc..

from `mailcheck.c`:

``` C
/* check_mail () is useful for more than just checking mail.  Since it has
   the paranoids dream ability of telling you when someone has read your
   mail, it can just as easily be used to tell you when someones .profile
   file has been read, thus letting one know when someone else has logged
   in.  Pretty good, huh? */
```

From `builtins/read.def`:

``` C
 /* If there are no variables, save the text of the line read to the
     variable $REPLY.  ksh93 strips leading and trailing IFS whitespace,
     so that `read x ; echo "$x"' and `read ; echo "$REPLY"' behave the
     same way, but I believe that the difference in behaviors is useful
     enough to not do it.  Without the bash behavior, there is no way
     to read a line completely without interpretation or modification
     unless you mess with $IFS (e.g., setting it to the empty string).
     If you disagree, change the occurrences of `#if 0' to `#if 1' below. */
```

from `variables.c`:

``` C
     /*
      * 24 October 2001
      *
      * I'm tired of the arguing and bug reports.  Bash now leaves SSH_CLIENT
      * and SSH2_CLIENT alone.  I'm going to rely on the shell_level check in
      * isnetconn() to avoid running the startup files more often than wanted.
      * That will, of course, only work if the user's login shell is bash, so
      * I've made that behavior conditional on SSH_SOURCE_BASHRC being defined
      * in config-top.h.
      */
```

From `shell.h`:

``` C
/* Values that can be returned by execute_command (). */
#define EXECUTION_FAILURE 1
#define EXECUTION_SUCCESS 0

/* Usage messages by builtins result in a return status of 2. */
#define EX_BADUSAGE     2

/* Special exit statuses used by the shell, internally and externally. */
#define EX_RETRYFAIL    124
#define EX_WEXPCOMSUB   125
#define EX_BINARY_FILE  126
#define EX_NOEXEC       126
#define EX_NOINPUT      126
#define EX_NOTFOUND     127

#define EX_SHERRBASE    256     /* all special error values are > this. */

#define EX_BADSYNTAX    257     /* shell syntax error */
#define EX_USAGE        258     /* syntax error in usage */
#define EX_REDIRFAIL    259     /* redirection failed */
#define EX_BADASSIGN    260     /* variable assignment error */
#define EX_EXPFAIL      261     /* word expansion failed */
```
