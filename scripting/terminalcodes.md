====== Terminal codes (ANSI/VT100) introduction ======

{{keywords&gt;bash shell scripting colors cursor control vt100 ansi}}

Terminal (control) codes are used to issue specific commands to your terminal. This can be related to switching colors or positioning the cursor, i.e. anything that can't be done by the application itself.


===== How it technically works =====

A terminal control code is a special sequence of characters that is printed (like any other text). If the terminal understands the code, it won't display the character-sequence, but will perform some action. You can print the codes with a simple ''echo'' command.

__**Note:**__ I see codes referenced as &quot;Bash colors&quot; sometimes (several &quot;Bash tutorials&quot; etc...): That's a completely incorrect definition.

===== The tput command =====

Because there's a large number of different terminal control languages, usually a system has an intermediate communication layer. The real codes are looked up in a database **for the currently detected terminal type** and you give standardized requests to an API or (from the shell) to a command.

One of these commands is ''tput''. Tput accepts a set of acronyms called //capability names// and any parameters, if appropriate, then looks up the correct escape sequences for the detected terminal in the ''terminfo'' database and prints the correct codes (the terminal hopefully understands).

===== The codes =====

In this list I'll focus on ANSI/VT100 control codes for the most common actions - take it as quick reference. The documentation of your terminal or the ''terminfo'' database is always the preferred source when something is unclear! Also the ''tput'' acronyms are usually the ones dedicated for ANSI escapes!

I listed only the most relevant codes, of course, any ANSI terminal understands many more! But let's keep the discussion centered on common shell scripting ;-)

If I couldn't find a matching ANSI escape, you'll see a :?: as  the code. Feel free to mail me or fix it.

The ANSI codes always start with the ESC character. (ASCII 0x1B or octal 033) This isn't part of the list, but **you should avoid using the ANSI codes directly - use the ''tput'' command!**

All codes that can be used with ''tput'' can be found in terminfo(5). (on OpenBSD at least)
See [[http://www.openbsd.org/cgi-bin/man.cgi?query=terminfo&amp;apropos=0&amp;sektion=5&amp;manpath=OpenBSD+Current&amp;arch=i386&amp;format=html|OpenBSD's terminfo(5)]] under the __Capabilities__ section.  The //cap-name// is the code to use with tput. A description of each code is also provided.

==== General useful ASCII codes ====

The **Ctrl-Key** representation is simply associating the non-printable characters from ASCII code 1 with the printable (letter) characters from ASCII code 65 (&quot;A&quot;). ASCII code 1 would be ''^A'' (Ctrl-A), while ASCII code 7 (BEL) would be ''^G'' (Ctrl-G). This is a common representation (and input method) and historically comes from one of the VT series of terminals.

^Name^decimal^octal^hex^C-escape^Ctrl-Key^Description^
|''BEL''|7|007|0x07|''\a''|''^G''|Terminal bell|
|''BS''|8|010|0x08|''\b''|''^H''|Backspace|
|''HT''|9|011|0x09|''\t''|''^I''|Horizontal TAB|
|''LF''|10|012|0x0A|''\n''|''^J''|Linefeed (newline)|
|''VT''|11|013|0x0B|''\v''|''^K''|Vertical TAB|
|''FF''|12|014|0x0C|''\f''|''^L''|Formfeed (also: New page ''NP'')|
|''CR''|13|015|0x0D|''\r''|''^M''|Carriage return|
|''ESC''|27|033|0x1B|''&lt;none&gt;''|''^[''|Escape character|
|''DEL''|127|177|0x7F|''&lt;none&gt;''|''&lt;none&gt;''|Delete character|

==== Cursor handling ====

^ANSI^terminfo equivalent^Description^
|''[ &lt;X&gt; ; &lt;Y&gt; H''\\ ''[ &lt;X&gt; ; &lt;Y&gt; f''|''cup &lt;X&gt; &lt;Y&gt;''|Home-positioning to ''X'' and ''Y'' coordinates\\ :!: it seems that ANSI uses 1-1 as home while ''tput'' uses 0-0|
|''[ H''|''home''|Move cursor to home position (0-0)|
|''7''|''sc''|Save current cursor position|
|''8''|''rc''|Restore saved cursor position|
|:?: most likely a normal code like ''\b''|''cub1''|move left one space (backspace)|
|VT100 ''[ ? 25 l''|''civis''|make cursor invisible|
|VT100 ''[ ? 25 h''|''cvvis''|make cursor visible|

==== Erasing text ====

^ ANSI                 ^ terminfo equivalent  ^ Description                                                     ^
| ''[ K''\\ ''[ 0 K''  | ''el''               | **Clear line** from current cursor position **to end** of line  |
| ''[ 1 K''            | ''el1''              | **Clear line from beginning** to current cursor position        |
| ''[ 2 K''            | ''el2'':?:           | **Clear whole line** (cursor position unchanged)                |

==== General text attributes ====

^ANSI^terminfo equivalent^Description^
|''[ 0 m''|''sgr0''|Reset all attributes|
|''[ 1 m''|''bold''|Set &quot;bright&quot; attribute|
|''[ 2 m''|''dim''|Set &quot;dim&quot; attribute|
|''[ 3 m''|''smso''|Set &quot;standout&quot; attribute|
|''[ 4 m''|set ''smul'' unset ''rmul'' :?:|Set &quot;underscore&quot; (underlined text) attribute|
|''[ 5 m''|''blink''|Set &quot;blink&quot; attribute|
|''[ 7 m''|''rev''|Set &quot;reverse&quot; attribute|
|''[ 8 m''|''invis''|Set &quot;hidden&quot; attribute|

==== Foreground coloring ====

^ ANSI         ^ terminfo equivalent  ^ Description                                   ^
| ''[ 3 0 m''  | ''setaf 0''          | Set **foreground** to color #0 - **black**    |
| ''[ 3 1 m''  | ''setaf 1''          | Set **foreground** to color #1 - **red**      |
| ''[ 3 2 m''  | ''setaf 2''          | Set **foreground** to color #2 - **green**    |
| ''[ 3 3 m''  | ''setaf 3''          | Set **foreground** to color #3 - **yellow**   |
| ''[ 3 4 m''  | ''setaf 4''          | Set **foreground** to color #4 - **blue**     |
| ''[ 3 5 m''  | ''setaf 5''          | Set **foreground** to color #5 - **magenta**  |
| ''[ 3 6 m''  | ''setaf 6''          | Set **foreground** to color #6 - **cyan**     |
| ''[ 3 7 m''  | ''setaf 7''          | Set **foreground** to color #7 - **white**    |
| ''[ 3 9 m''  | ''setaf 9''          | Set **default** color as foreground color     |

==== Background coloring ====

^ ANSI         ^ terminfo equivalent  ^ Description                                   ^
| ''[ 4 0 m''  | ''setab 0''          | Set **background** to color #0 - **black**    |
| ''[ 4 1 m''  | ''setab 1''          | Set **background** to color #1 - **red**      |
| ''[ 4 2 m''  | ''setab 2''          | Set **background** to color #2 - **green**    |
| ''[ 4 3 m''  | ''setab 3''          | Set **background** to color #3 - **yellow**   |
| ''[ 4 4 m''  | ''setab 4''          | Set **background** to color #4 - **blue**     |
| ''[ 4 5 m''  | ''setab 5''          | Set **background** to color #5 - **magenta**  |
| ''[ 4 6 m''  | ''setab 6''          | Set **background** to color #6 - **cyan**     |
| ''[ 4 7 m''  | ''setab 7''          | Set **background** to color #7 - **white**    |
| ''[ 4 9 m''  | ''setab 9''          | Set **default** color as background color     |

==== Misc codes ====

=== Save/restore screen ===

Used capabilities: ''smcup'', ''rmcup''

You've undoubtedly already encountered programs that restore the terminal contents after they do their work (like ''vim''). This can be done by the following commands:
&lt;code&gt;
# save, clear screen
tput smcup
clear

# example &quot;application&quot; follows...
read -n1 -p &quot;Press any key to continue...&quot;
# example &quot;application&quot; ends here

# restore
tput rmcup
&lt;/code&gt;

These features require that certain capabilities exist in your termcap/terminfo.  While ''xterm'' and most of its clones (''rxvt'', ''urxvt'', etc) will support the instructions, your operating system may not include references to them in its default xterm profile.  (FreeBSD, in particular, falls into this category.)  If `tput smcup` appears to do nothing for you, and you don't want to modify your system termcap/terminfo data, and you KNOW that you are using a compatible xterm application, the following may work for you:

&lt;code&gt;
echo -e '\033[?47h' # save screen
echo -e '\033[?47l' # restore screen
&lt;/code&gt;

Certain software uses these codes (via their termcap capabilities) as well. You may have seen the screen save/restore in ''less'', ''vim'', ''top'', ''screen'' and others.  Some of these applications may also provide configuration options to *disable* this behaviour.  For example, ''less'' has a ''-X'' option for this, which can also be set in an environment variable:

&lt;code&gt;
export LESS=X
less /path/to/file
&lt;/code&gt;

Similarly, ''vim'' can be configured not to &quot;restore&quot; the screen by adding the following to your ''~/.vimrc'':

&lt;code&gt;
set t_ti= t_te=
&lt;/code&gt;



=== Additional colors ===

Some terminal emulators support additional colors. The most common extension used by xterm-compatible terminals supports 256 colors. These can be generated by ''tput'' with ''seta{f,b} [0-255]'' when the ''TERM'' value has a ''-256color'' suffix.
[[https://gist.github.com/XVilka/8346728#now-supporting-truecolour|Some terminals]] also support full 24-bit colors, and any X11 color code can be written directly into a special escape sequence. ([[https://gist.github.com/XVilka/8346728|More infos]]) Only a few programs make use of anything beyond 256 colors, and tput doesn't know about them. Colors beyond 16 usually only apply to modern terminal emulators running in graphical environments.

The Virtual Terminal implemented in the Linux kernel supports only 16 colors, and the usual default terminfo entry for ''TERM=linux'' defines only 8. There is sometimes an alternate &quot;linux-16color&quot; that you can switch to, to get the other 8 colors.

===== Bash examples =====

==== Hardcoded colors ====

&lt;code&gt;
printf '%b\n' 'It is \033[31mnot\033[39m intelligent to use \033[32mhardcoded ANSI\033[39m codes!'
&lt;/code&gt;

==== Colors using tput ====

__Directly inside the echo:__
&lt;code&gt;
echo &quot;TPUT is a $(tput setaf 2)nice$(tput setaf 9) and $(tput setaf 5)user friendly$(tput setaf 9) terminal capability database.&quot;
&lt;/code&gt;

__With preset variables:__
&lt;code&gt;
COL_NORM=&quot;$(tput setaf 9)&quot;
COL_RED=&quot;$(tput setaf 1)&quot;
COL_GREEN=&quot;$(tput setaf 2)&quot;
echo &quot;It's ${COL_RED}red${COL_NORM} and ${COL_GREEN}green${COL_NORM} - have you seen?&quot;
&lt;/code&gt;

==== Misc ====

__HOME function__
&lt;code&gt;
home() {
  # yes, actually not much shorter ;-)
  tput home
}
&lt;/code&gt;

==== Silly but nice effect ====

&lt;code&gt;
#!/bin/bash

DATA[0]=&quot;     _/  _/    _/                            _/    &quot;
DATA[1]=&quot;  _/_/_/_/_/  _/_/_/      _/_/_/    _/_/_/  _/_/_/ &quot;
DATA[2]=&quot;   _/  _/    _/    _/  _/    _/  _/_/      _/    _/&quot;
DATA[3]=&quot;_/_/_/_/_/  _/    _/  _/    _/      _/_/  _/    _/ &quot;
DATA[4]=&quot; _/  _/    _/_/_/      _/_/_/  _/_/_/    _/    _/  &quot;

# virtual coordinate system is X*Y ${#DATA} * 5

REAL_OFFSET_X=0
REAL_OFFSET_Y=0

draw_char() {
  V_COORD_X=$1
  V_COORD_Y=$2

  tput cup $((REAL_OFFSET_Y + V_COORD_Y)) $((REAL_OFFSET_X + V_COORD_X))

  printf %c ${DATA[V_COORD_Y]:V_COORD_X:1}
}


trap 'exit 1' INT TERM
trap 'tput setaf 9; tput cvvis; clear' EXIT

tput civis
clear

while :; do

for ((c=1; c &lt;= 7; c++)); do
  tput setaf $c
  for ((x=0; x&lt;${#DATA[0]}; x++)); do
    for ((y=0; y&lt;=4; y++)); do
      draw_char $x $y
    done
  done
done

done
&lt;/code&gt;

==== Mandelbrot set ====

This is a slightly modified version of Charles Cooke's colorful Mandelbrot plot scripts ([[http://earth.gkhs.net/ccooke/shell.html | original w/ screenshot]]) -- ungolfed, optimized a bit, and without hard-coded terminal escapes. The ''colorBox'' function is [[http://en.wikipedia.org/wiki/Memoization | memoized]] to collect ''tput'' output only when required and output a new escape only when a color change is needed. This limits the number of ''tput'' calls to at most 16, and reduces raw output by more than half. The ''doBash'' function uses integer arithmetic, but is still ksh93-compatible (run as e.g. ''bash ./mandelbrot'' to use it). The ksh93-only floating-point ''doKsh'' is almost 10x faster than ''doBash'' (thus the ksh shebang by default), but uses only features that don't make the Bash parser crash.

&lt;code&gt;
#!/usr/bin/env ksh

# Charles Cooke's 16-color Mandelbrot
# http://earth.gkhs.net/ccooke/shell.html
# Combined Bash/ksh93 flavors by Dan Douglas (ormaaj)

function doBash {
	typeset P Q X Y a b c i v x y 
	for ((P=10**8,Q=P/100,X=320*Q/cols,Y=210*Q/lines,y=-105*Q,v=-220*Q,x=v;y&lt;105*Q;x=v,y+=Y)); do
		for ((;x&lt;P;a=b=i=c=0,x+=X)); do
			for ((;a**2+b**2&lt;4*P**2&amp;&amp;i++&lt;99;a=((c=a)**2-b**2)/P+x,b=2*c*b/P+y)); do :
			done
			colorBox $((i&lt;99?i%16:0))
		done
		echo
	done
}

function doKsh {
	integer i
	float a b c x=2.2 y=-1.05 X=3.2/cols Y=2.1/lines 
	while
		for ((a=b=i=0;(c=a)**2+b**2&lt;=2&amp;&amp;i++&lt;99&amp;&amp;(a=a**2-b**2+x,b=2*c*b+y);)); do :
		done
		. colorBox $((i&lt;99?i%16:0))
		if ((x&lt;1?!(x+=X):(y+=Y,x=-2.2))); then
			print
			((y&lt;1.05)) 
		fi
		do :
	done
}

function colorBox {
	(($1==lastclr)) || printf %s &quot;${colrs[lastclr=$1]:=$(tput setaf &quot;$1&quot;)}&quot;
	printf '\u2588'
}

unset -v lastclr
((cols=$(tput cols)-1, lines=$(tput lines)))
typeset -a colrs
trap 'tput sgr0; echo' EXIT
${KSH_VERSION+. doKsh} ${BASH_VERSION+doBash}
&lt;/code&gt;

