# The Bash Hackers Wiki

This wiki is intended to hold documentation of any kind about GNU Bash.
The main motivation was to provide *human-readable documentation* and
information so users aren't forced to read every bit of the Bash manpage - 
which can be difficult to understand. However, the docs here are **not**
meant as a newbie tutorial.

This wiki and any programs found in this wiki are free software: you can
redistribute it and/or modify it under the terms of the GNU General
Public License as published by the Free Software Foundation, either
version 3 of the License, or (at your option) any later version.

This wiki and its programs are distributed in the hope that it will be
useful, but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General
Public License for more details.

You should have received a copy of the GNU General Public License along
with this program. If not, see \<<http://www.gnu.org/licenses/>\>.

[What would YOU like to see here?](wishes.md) (outdated and locked, please
use the discussions)

**Stranger!** [Feel free to
register](http://wiki.bash-hackers.org/start&do=register) and comment or
edit the contents. There is a [Bash Hackers Wiki needs love
page](/meta/need_love) that lists some things to do. The registration is
only there to prevent SPAM.

## Scripting and general information

- [Bash v4 - a rough overview](bash4.md) (unmaintained, since Bash 4 is
  more or less standard)
- [style](/scripting/style.md.md) -- an assorted collection of style and optic
  hints
- [basics](/scripting/basics.md.md)
- [newbie_traps](/scripting/newbie_traps.md.md)
- [bashbehaviour](/scripting/bashbehaviour.md.md)
- [posparams](/scripting/posparams.md.md)
- [processtree](/scripting/processtree.md.md)
- [obsolete](/scripting/obsolete.md.md)
- [nonportable](/scripting/nonportable.md.md)
- [debuggingtips](/scripting/debuggingtips.md.md)
- [terminalcodes](/scripting/terminalcodes.md.md)
- [tutoriallist](/scripting/tutoriallist.md.md)

## Code snippets

There is a [section that holds small code snippets](/snipplets/start.md.md).

See also [some Bash source code excerpts](/misc/readthesourceluke.md.md).

## How to

[Doing specific tasks: concepts, methods, ideas](/howto/start.md.md):

- [Simple locking (against parallel run)](/howto/mutex.md.md)
- [Rudimentary config files for your scripts](/howto/conffile.md.md)
- [Editing files with ed(1)](/howto/edit-ed.md.md)
- [Collapsing Functions](/howto/collapsing_functions.md.md)
- [Illustrated Redirection Tutorial](/howto/redirection_tutorial.md.md)
- [Calculate with dc(1)](/howto/calculate-dc.md)
- [Introduction to pax - the POSIX archiver](/howto/pax.md.md)
- [getopts_tutorial](/howto/getopts_tutorial.md.md) (*under construction!*)
- [dissectabadoneliner](/howto/dissectabadoneliner.md.md) An example of a bad
  oneliner, breakdown and fix (by `kojoro`)
- [Write tests for ./your-script.sh](/howto/testing-your-scripts.md.md) by
  using bashtest util

## Bash syntax and operations

- [Bash features overview by version](/scripting/bashchanges.md.md)
- [Basic grammar rules](/syntax/basicgrammar.md.md)
- [Quoting and character escaping](/syntax/quoting.md.md)
- [Parsing and execution](/syntax/grammar/parser_exec.md.md)
- [Some words about words...](/syntax/words.md.md)
- [Patterns and pattern matching](/syntax/pattern.md.md)
- [Arithmetic expressions](/syntax/arith_expr.md.md)
- [List of shell options](/internals/shell_options.md.md)
- [Redirection](/syntax/redirection.md.md)
- [Special parameters and shell variables](/syntax/shellvars.md.md)
- [Arrays](/syntax/arrays.md.md)

## Compound commands

| **[Compound commands overview](/syntax/ccmd/intro.md.md)** |                                                                  |
|:-----------------------------------------------------|------------------------------------------------------------------|
| Grouping                                             |                                                                  |
| `{ ...; }`                                           | [command grouping](/syntax/ccmd/grouping_plain.md.md)                  |
| `( ... )`                                            | [command grouping in a subshell](/syntax/ccmd/grouping_subshell.md.md) |
| Conditionals                                         |                                                                  |
| `[[ ... ]]`                                          | [conditional expression](/syntax/ccmd/conditional_expression.md.md)    |
| `if ...; then ...; fi`                               | [conditional branching](/syntax/ccmd/if_clause.md.md)                  |
| `case ... esac`                                      | [pattern-based branching](/syntax/ccmd/case.md.md)                     |
| Loops                                                |                                                                  |
| `for word in ...; do ...; done`                      | [classic for-loop](/syntax/ccmd/classic_for.md.md)                     |
| `for ((x=1; x<=10; x++)); do ...; done`              | [C-style for-loop](/syntax/ccmd/c_for.md.md)                           |
| `while ...; do ...; done`                            | [while loop](/syntax/ccmd/while_loop.md.md)                            |
| `until ...; do ...; done`                            | [until loop](/syntax/ccmd/until_loop.md.md)                            |
| Misc                                                 |                                                                  |
| `(( ... ))`                                          | [arithmetic evaluation](/syntax/ccmd/arithmetic_eval.md.md)            |
| `select word in ...; do ...; done`                   | [user selections](/syntax/ccmd/user_select.md.md)                      |

## Expansions and substitutions

| **[Introduction to expansions and substitutions](/syntax/expansion/intro.md.md)** |                                                      |
|:----------------------------------------------------------------------------|------------------------------------------------------|
| `{A,B,C} {A..C}`                                                            | [Brace expansion](/syntax/expansion/brace.md.md)           |
| `~/ ~root/`                                                                 | [Tilde expansion](/syntax/expansion/tilde.md.md)           |
| `$FOO ${BAR%.mp3}`                                                          | [Parameter expansion](/syntax/pe.md.md)                    |
| `` `command` $(command) ``                                                  | [Command substitution](/syntax/expansion/cmdsubst.md.md)   |
| `<(command) >(command)`                                                     | [Process substitution](/syntax/expansion/proc_subst.md.md) |
| `$((1 + 2 + 3)) $[4 + 5 + 6]`                                               | [Arithmetic expansion](/syntax/expansion/arith.md.md)      |
| `Hello <---> Word!`                                                         | [Word splitting](/syntax/expansion/wordsplit.md.md)        |
| `/data/*-av/*.mp?`                                                          | [Pathname expansion](/syntax/expansion/globs.md.md)        |


## Builtin Commands

This is a selection of builtin commands and command-like keywords,
loosely arranged by their common uses. These are provided directly by
the shell, rather than invoked as standalone external commands.

<table>
<thead>
<tr class="header">
<th style="text-align: center;">Declaration commands<br />
Commands that set and query
attributes/types, and manipulate simple
datastructures.</th>
<th></th>
<th style="text-align: center;">Alt</th>
<th style="text-align: center;">Type</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: center;"><a
href="/commands/builtin/declare.md.md">declare</a></td>
<td>Display or set shell variables or functions along with
attributes.</td>
<td style="text-align: center;"><code>typeset</code></td>
<td style="text-align: center;">builtin</td>
</tr>
<tr class="even">
<td style="text-align: center;"><a
href="/commands/builtin/export">export</a></td>
<td>Display or set shell variables, also giving them the export
attribute.</td>
<td style="text-align: center;"><code>typeset -x</code></td>
<td style="text-align: center;">special builtin</td>
</tr>
<tr class="odd">
<td style="text-align: center;"><a
href="/commands/builtin/eval">eval</a></td>
<td>Evaluate arguments as shell code.</td>
<td style="text-align: center;">-</td>
<td style="text-align: center;">special builtin</td>
</tr>
<tr class="even">
<td style="text-align: center;"><a
href="/commands/builtin/local">local</a></td>
<td>Declare variables as having function local scope.</td>
<td style="text-align: center;">-</td>
<td style="text-align: center;">builtin</td>
</tr>
<tr class="odd">
<td style="text-align: center;"><a
href="/commands/builtin/read.md.mdonly">readonly</a></td>
<td>Mark variables or functions as read-only.</td>
<td style="text-align: center;"><code>typeset -r</code></td>
<td style="text-align: center;">special builtin</td>
</tr>
<tr class="even">
<td style="text-align: center;"><a
href="/commands/builtin/unset.md.md">unset</a></td>
<td>Unset variables and functions.</td>
<td style="text-align: center;">-</td>
<td style="text-align: center;">special builtin</td>
</tr>
<tr class="odd">
<td style="text-align: center;"><a
href="/commands/builtin/shift">shift</a></td>
<td>Shift positional parameters</td>
<td style="text-align: center;">-</td>
<td style="text-align: center;">special builtin</td>
</tr>
<tr class="even">
<td style="text-align: center;">I/O<br />
Commands for reading/parsing
input, or producing/formatting output of standard
streams.</td>
<td></td>
<td style="text-align: center;">Alt</td>
<td style="text-align: center;">Type</td>
</tr>
<tr class="odd">
<td style="text-align: center;"><a
href="/syntax/keywords/coproc.md.md">coproc</a></td>
<td>Co-processes: Run a command in the background with pipes for reading
/ writing its standard streams.</td>
<td style="text-align: center;">-</td>
<td style="text-align: center;">keyword</td>
</tr>
<tr class="even">
<td style="text-align: center;"><a
href="/commands/builtin/echo">echo</a></td>
<td>Create output from arguments.</td>
<td style="text-align: center;">-</td>
<td style="text-align: center;">builtin</td>
</tr>
<tr class="odd">
<td style="text-align: center;"><a
href="/commands/builtin/mapfile.md.md">mapfile</a></td>
<td>Read lines of input into an array.</td>
<td style="text-align: center;"><code>readarray</code></td>
<td style="text-align: center;">builtin</td>
</tr>
<tr class="even">
<td style="text-align: center;"><a
href="/commands/builtin/printf.md.md">printf</a></td>
<td>"advanced <code>echo</code>."</td>
<td style="text-align: center;">-</td>
<td style="text-align: center;">builtin</td>
</tr>
<tr class="odd">
<td style="text-align: center;"><a
href="/commands/builtin/read.md.md">read</a></td>
<td>Read input into variables or arrays, or split strings into fields
using delimiters.</td>
<td style="text-align: center;">-</td>
<td style="text-align: center;">builtin</td>
</tr>
<tr class="even">
<td style="text-align: center;">Configuration and Debugging<br />
Commands that modify shell behavior, change special options, assist in debugging.</td>
<td></td>
<td style="text-align: center;">Alt</td>
<td style="text-align: center;">Type</td>
</tr>
<tr class="odd">
<td style="text-align: center;"><a
href="/commands/builtin/caller.md.md">caller</a></td>
<td>Identify/print execution frames.</td>
<td style="text-align: center;">-</td>
<td style="text-align: center;">builtin</td>
</tr>
<tr class="even">
<td style="text-align: center;"><a
href="/commands/builtin/set.md.md">set</a></td>
<td>Set the positional parameters and/or set options that affect shell
behaviour.</td>
<td style="text-align: center;">-</td>
<td style="text-align: center;">special builtin</td>
</tr>
<tr class="odd">
<td style="text-align: center;"><a
href="/commands/builtin/shopt.md.md">shopt</a></td>
<td>set/get some bash-specific shell options.</td>
<td style="text-align: center;">-</td>
<td style="text-align: center;">builtin</td>
</tr>
</tbody>
</table>

<table>
<thead>
<tr class="header">
<th style="text-align: center;">Control flow and data processing<br />
Commands that operate on data and/or affect control flow.</th>
<th></th>
<th style="text-align: center;">Alt</th>
<th style="text-align: center;">Type</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: center;"><a
href="/commands/builtin/true">colon</a></td>
<td>"true" null command.</td>
<td style="text-align: center;">true</td>
<td style="text-align: center;">special builtin</td>
</tr>
<tr class="even">
<td style="text-align: center;"><a
href="/commands/builtin/source">dot</a></td>
<td>Source external files.</td>
<td style="text-align: center;">source</td>
<td style="text-align: center;">special builtin</td>
</tr>
<tr class="odd">
<td style="text-align: center;"><a
href="/commands/builtin/false">false</a></td>
<td>Fail at doing nothing.</td>
<td style="text-align: center;">-</td>
<td style="text-align: center;">builtin</td>
</tr>
<tr class="even">
<td style="text-align: center;"><a
href="/commands/builtin/continueBreak">continue / break</a></td>
<td>continue with or break out of loops.</td>
<td style="text-align: center;">-</td>
<td style="text-align: center;">special builtin</td>
</tr>
<tr class="odd">
<td style="text-align: center;"><a
href="/commands/builtin/let.md.md">let</a></td>
<td>Arithmetic evaluation simple command.</td>
<td style="text-align: center;">-</td>
<td style="text-align: center;">builtin</td>
</tr>
<tr class="even">
<td style="text-align: center;"><a
href="/commands/builtin/return">return</a></td>
<td>Return from a function with a specified exit status.</td>
<td style="text-align: center;">-</td>
<td style="text-align: center;">special builtin</td>
</tr>
<tr class="odd">
<td style="text-align: center;"><a
href="/commands/classictest.md.md">[</a></td>
<td>The classic <code>test</code> simple command.</td>
<td style="text-align: center;">test</td>
<td style="text-align: center;">builtin</td>
</tr>
<tr class="even">
<td style="text-align: center;">Process and Job control<br />
Commands related to jobs,
signals, process groups, subshells.</td>
<td></td>
<td style="text-align: center;">Alt</td>
<td style="text-align: center;">Type</td>
</tr>
<tr class="odd">
<td style="text-align: center;"><a
href="/commands/builtin/exec.md.md">exec</a></td>
<td>Replace the current shell process or set redirections.</td>
<td style="text-align: center;">-</td>
<td style="text-align: center;">special builtin</td>
</tr>
<tr class="even">
<td style="text-align: center;"><a
href="/commands/builtin/exit">exit</a></td>
<td>Exit the shell.</td>
<td style="text-align: center;">-</td>
<td style="text-align: center;">special builtin</td>
</tr>
<tr class="odd">
<td style="text-align: center;"><a
href="/commands/builtin/kill">kill</a></td>
<td>Send a signal to specified process(es)</td>
<td style="text-align: center;">-</td>
<td style="text-align: center;">builtin</td>
</tr>
<tr class="even">
<td style="text-align: center;"><a
href="/commands/builtin/trap">trap</a></td>
<td>Set signal handlers or output the current handlers.</td>
<td style="text-align: center;">-</td>
<td style="text-align: center;">special builtin</td>
</tr>
<tr class="odd">
<td style="text-align: center;"><a
href="/commands/builtin/times">times</a></td>
<td>Display process times.</td>
<td style="text-align: center;">-</td>
<td style="text-align: center;">special builtin</td>
</tr>
<tr class="even">
<td style="text-align: center;"><a
href="/commands/builtin/wait">wait</a></td>
<td>Wait for background jobs and asynchronous lists.</td>
<td style="text-align: center;">-</td>
<td style="text-align: center;">builtin</td>
</tr>
<tr class="odd">
<td style="text-align: center;"></td>
<td></td>
<td style="text-align: center;">Alt</td>
<td style="text-align: center;">Type</td>
</tr>
</tbody>
</table>

## Dictionary

| :bulb: A list of expressions, words, and their meanings is [here](/dict/index.md.md). |
| --- |

## Links

### Official Bash links

- [Chet Ramey's Bash
  page](http://tiswww.case.edu/php/chet/bash/bashtop.html) and its
  [FAQ](http://tiswww.case.edu/php/chet/bash/FAQ).
- [GNU Bash software page](http://www.gnu.org/software/bash/)
- Official Bash mailing lists:
  - **Bug reports**: <bug-bash@gnu.org>
    ([archives](http://mail.gnu.org/pipermail/bug-bash))
  - **General questions**: <help-bash@gnu.org>
    ([archives](http://mail.gnu.org/pipermail/help-bash))
- Official Bash git repository:
  - **Browse**: [cgit](http://git.savannah.gnu.org/cgit/bash.git)
  - **Clone**: //<git://> ssh://git.sv.gnu.org/srv/git/bash.git •
    //<ssh://> ssh://git.sv.gnu.org/srv/git/bash.git • //<http://>
    http://git.savannah.gnu.org/r/bash.git

### Recommended Shell resources

- [Greg's wiki](http://mywiki.wooledge.org/) - Greg Wooledge's (aka
  "greycat") wiki -- with **MASSIVE** information about Bash and UNIX(r)
  in general.
  - [BashFAQ](http://mywiki.wooledge.org/BashFAQ) •
    [BashGuide](http://mywiki.wooledge.org/BashGuide) •
    [BashPitfalls](http://mywiki.wooledge.org/BashPitfalls) •
    [BashSheet](http://mywiki.wooledge.org/BashSheet)
- [Sven Mascheck's pages](http://www.in-ulm.de/~mascheck/) - A goldmine
  of information. A must-read.
- [\#ksh channel page](https://www.mirbsd.org/ksh-chan.htm) - \#ksh
  Freenode channel page maintains a solid collection of recommended
  links.
- [The Grymoire Unix pages](http://www.grymoire.com/Unix/) - Good
  scripting information, especially read the
  [quoting](http://www.grymoire.com/Unix/Quote.html) guide.
- [Heiner's "Shell Dorado"](http://www.shelldorado.com) - Tips, tricks,
  links - for every situation.
- [The Single Unix Specification (version 4, aka
  POSIX-2008)](http://pubs.opengroup.org/onlinepubs/9699919799/)
- [The Austin Group](http://www.opengroup.org/austin/) - [List
  archives](http://dir.gmane.org/gmane.comp.standards.posix.austin.general),
  [Bug tracker](http://austingroupbugs.net/main_page.php)
- [comp.unix.shell FAQ](http://cfajohnson.com/shell/cus-faq.html)
- [Advanced Bash-Scripting
  Guide](https://tldp.org/LDP/abs/html/index.html) - last update: 10 Mar
  2014, but still very useful guide.

#### Documentation / Reference

- **Bash**: [man page](http://tiswww.case.edu/php/chet/bash/bash.html)
  [info page](http://tiswww.case.edu/php/chet/bash/bashref.html)
- **AT&T ksh**:
  [ksh88](http://www2.research.att.com/sw/download/man/man1/ksh88.html)
  [ksh93](http://www2.research.att.com/sw/download/man/man1/ksh.html)
- [mksh](https://www.mirbsd.org/htman/i386/man1/mksh.htm) (pdksh
  successor)
- [zsh](http://zsh.sourceforge.net/Doc/)
- [dash](http://man7.org/linux/man-pages/man1/dash.1.html)
- [Heirloom Bourne shell](http://heirloom.sourceforge.net/man/sh.1.html)
- [Thompson shell](http://v6shell.org/man/osh.1.html)

### Assorted interesting links

- [History and development of the traditional Bourne shell
  family](http://www.in-ulm.de/~mascheck/bourne/) - very interesting and
  nice to read!
- [Interview with Chet
  Ramey](http://www.computerworld.com.au/article/222764/-z_programming_languages_bash_bourne-again_shell)
- [Interview with Steve
  Bourne](http://www.computerworld.com.au/article/279011/a-z_programming_languages_sh)
  • [Stephen Bourne - BSDCan 2015
  keynote](https://www.youtube.com/watch?v=2kEJoWfobpA)
- [Interview with David
  Korn](http://news.slashdot.org/story/01/02/06/2030205/david-korn-tells-all)
- [Kernighan on the Unix pipeline (computerphile
  video)](https://www.youtube.com/watch?v=bKzonnwoR2I)
- Linux in general, with some shell related stuff: [nixCraft: Linux
  Tips, Hacks, Tutorials and Ideas](http://www.cyberciti.biz/)
- Linux tutorials, guides and how-tos: [RoseHosting
  Blog](https://www.rosehosting.com/blog/), [bash script for installing
  WordPress](https://www.rosehosting.com/blog/script-install-wordpress-on-a-debianubuntu-vps/)
  and some [basic shell
  commands](https://www.rosehosting.com/blog/basic-shell-commands-after-putty-ssh-logon/)
- [Bashphorism list from the Bash IRC channel on
  Freenode](/misc/bashphorisms)
- [Some more or less funny commandline stuff](/misc/shell_humor.md.md)
- [How to Enable SSH on Ubuntu
  Tutorial](https://thishosting.rocks/how-to-enable-ssh-on-ubuntu/)
- [How To Make an Awesome Custom Shell with
  ZSH](https://linuxstans.com/how-to-make-an-awesome-custom-shell-with-zsh/)

### Bash Libraries (needs review)

- [An oo-style bash library for bash
  4](http://sourceforge.net/projects/oobash/) - provides tools for rapid
  script development and huge libraries.
- [General purpose shell framework for bash
  4](https://github.com/hornos/shf3) - in development.
- [General purpose bash library for bash
  4](https://github.com/chilicuil/learn/blob/master/sh/lib) - active
  development

## Contact

Visit us in `irc.freenode.net`, channel `#bash` ;-)

If you have critiques or suggestions, please feel free to send a mail
using the contact form on the right. Note that there is a simple
discussion option below every article.

Please also see the [imprint](/user/thebonsai/imprint.md.md) if you have
problems with the site and its contents (legality, ...)!

It also would be nice to drop a line when

- it helped you
- it didn't help you (something missing / unclear)
- you like it
- you don't like it
- you found mistakes / bugs

Simply: Reader's feedback
