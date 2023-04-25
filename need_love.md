# Bash Hackers Wiki needs love

There are things to do. Always.

## Article optics and structure

Target: A more or less unique structure (done)

- [intro](/syntax/ccmd/intro.md) - bring all mentioned compound command
  description pages into a format
  - SYNOPSIS
  - DESCRIPTION
    - (with as many sub-topics as needed)
  - EXAMPLES
  - PORTABILITY CONSIDERATIONS
  - SEE ALSO
- do the same with command descriptions (printf, read, ...)

## Article content and correctness

In general all pages need review, all the time. If you find anything
incorrect, stupid, unclear, just edit the page or write a mail. It's
also possible to use the integrated discussion option (below every
article).

Specific needs:

- [tutoriallist](/scripting/tutoriallist.md) - new tutorials? opinions?
  etc.

## Article code examples

- [getopts_tutorial](/howto/getopts_tutorial.md) - lacks some complex or
  more sophisticated examples

## Article completeness

- [newbie_traps](/scripting/newbie_traps).md - add your personal top newbie
  traps
- [shell_options](/internals/shell_options.md) - are these all options?
- [printf](/commands/builtin/printf.md) - can printf do more?
- [shellvars](/syntax/shellvars.md) - needs complete shell variables

## Greg's BashFAQ mirror

The code rewrite to mirror Greg's BashFAQ was a bad idea, it doesn't
work good. A fix to not lose the links would be to invent some nifty
mod_rewrite rules to forward any requests to the namespace to the real
BashFAQ wiki.

**Update:** Just hacked the rewrite rules - the mirror is gone and the
mirror URLs redirect to Greg's BashFAQ.

**Future:** Mirror it as real MoinMoinWiki instance. This won't happen
here on the webspace, more on a dedicated server.

## Getopts tutorial

- more examples
- workaround to use long options: translate long option to short option
  and replace positional parameters before using getopts
