====== Collapsing Functions ======

{{keywords&gt;bash shell scripting example function collapse}}

===== What is a &quot;Collapsing Function&quot;? =====
A collapsing function is a function whose behavior changes depending upon the circumstances under which it's run. Function collapsing is useful when you find yourself repeatedly checking a variable whose value never changes.

===== How do I make a function collapse? =====
Function collapsing requires some static feature in the environment. A common example is a script that gives the user the option of having &quot;verbose&quot; output.
&lt;code&gt;
#!/bin/bash

[[ $1 = -v || $1 = --verbose ]] &amp;&amp; verbose=1

chatter() {
  if [[ $verbose ]]; then
    chatter() {
      echo &quot;$@&quot;
    }
    chatter &quot;$@&quot;
  else
    chatter() {
      :
    }
  fi
}

echo &quot;Waiting for 10 seconds.&quot;
for i in {1..10}; do
  chatter &quot;$i&quot;
  sleep 1
done
&lt;/code&gt;

===== How does it work? =====
The first time you run chatter(), the function redefines itself based on the value of verbose. Thereafter, chatter doesn't check $verbose, it simply is. Further calls to the function reflect its collapsed nature. If verbose is unset, chatter will echo nothing, with no extra effort from the developer.
===== More examples =====
FIXME Add more examples!
&lt;code&gt;
# Somewhat more portable find -executable
# FIXME/UNTESTED (I don't have access to all of the different versions of find.)
# Usage: find PATH ARGS -- use find like normal, except use -executable instead of
# various versions of -perm /+ blah blah and hacks
find() {
  hash find || { echo 'find not found!'; exit 1; }
  # We can be pretty sure &quot;$0&quot; should be executable.
  if [[ $(command find &quot;$0&quot; -executable 2&gt; /dev/null) ]]; then
    unset -f find # We can just use the command find
  elif [[ $(command find &quot;$0&quot; -perm /u+x 2&gt; /dev/null) ]]; then
    find() {
      typeset arg args
      for arg do
        [[ $arg = -executable ]] &amp;&amp; args+=(-perm /u+x) || args+=(&quot;$arg&quot;)
      done
      command find &quot;${args[@]}&quot;
    }
  elif [[ $(command find &quot;$0&quot; -perm +u+x 2&gt; /dev/null) ]]; then
    find() {
      typeset arg args
      for arg do
        [[ $arg = -executable ]] &amp;&amp; args+=(-perm +u+x) || args+=(&quot;$arg&quot;)
      done
      command find &quot;${args[@]}&quot;
    }
  else # Last resort
    find() {
      typeset arg args
      for arg do
        [[ $arg = -executable ]] &amp;&amp; args+=(-exec test -x {} \; -print) || args+=(&quot;$arg&quot;)
      done
      command find &quot;${args[@]}&quot;
    }
  fi
  find &quot;$@&quot;
}
&lt;/code&gt;

&lt;code&gt;
#!/bin/bash
# Using collapsing functions to turn debug messages on/off

[ &quot;--debug&quot; = &quot;$1&quot; ] &amp;&amp; dbg=echo || dbg=:


# From now on if you use $dbg instead of echo, you can select if messages will be shown

$dbg &quot;This message will only be displayed if --debug is specified at the command line

