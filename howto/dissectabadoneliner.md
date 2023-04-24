# Dissect a bad oneliner

``` bash
$ ls *.zip | while read i; do j=`echo $i | sed 's/.zip//g'`; mkdir $j; cd $j; unzip ../$i; cd ..; done
```

This is an actual one-liner someone asked about in `#bash`. **There are
several things wrong with it. Let's break it down!**

``` bash
$ ls *.zip | while read i; do ...; done
```

(Please read <http://mywiki.wooledge.org/ParsingLs>.) This command
executes `ls` on the expansion of `*.zip`. Assuming there are filenames
in the current directory that end in '.zip', ls will give a
human-readable list of those names. The output of ls is not for parsing.
But in sh and bash alike, we can loop safely over the glob itself:

``` bash
$ for i in *.zip; do j=`echo $i | sed 's/.zip//g'`; mkdir $j; cd $j; unzip ../$i; cd ..; done
```

Let's break it down some more!

``` bash
j=`echo $i | sed 's/.zip//g'` # where $i is some name ending in '.zip'
```

The goal here seems to be get the filename without its `.zip` extension.
In fact, there is a POSIX(r)-compliant command to do this: `basename`
The implementation here is suboptimal in several ways, but the only
thing that's genuinely error-prone with this is "`echo $i`". Echoing an
*unquoted* variable means [wordsplitting](/syntax/expansion/wordsplit.md)
will take place, so any whitespace in `$i` will essentially be
normalized. In `sh` it is necessary to use an external command and a
subshell to achieve the goal, but we can eliminate the pipe (subshells,
external commands, and pipes carry extra overhead when they launch, so
they can really hurt performance in a loop). Just for good measure,
let's use the more readable, [modern](/syntax/expansion/cmdsubst.md) `$()`
construct instead of the old style backticks:

``` bash
sh $ for i in *.zip; do j=$(basename "$i" ".zip"); mkdir $j; cd $j; unzip ../$i; cd ..; done
```

In Bash we don't need the subshell or the external basename command. See
[Substring removal with parameter
expansion](/syntax/pe.md#substring_removal):

``` bash
bash $ for i in *.zip; do j="${i%.zip}"; mkdir $j; cd $j; unzip ../$i; cd ..; done
```

Let's keep going:

``` bash
$ mkdir $j; cd $j; ...; cd ..
```

As a programmer, you **never** know the situation under which your
program will run. Even if you do, the following best practice will never
hurt: When a following command depends on the success of a previous
command(s), check for success! You can do this with the "`&&`"
conjunction, that way, if the previous command fails, bash will not try
to execute the following command(s). It's fully POSIX(r). Oh, and
remember what I said about [wordsplitting](/syntax/expansion/wordsplit.md)
in the previous step? Well, if you don't quote `$j`, wordsplitting can
happen again.

``` bash
$ mkdir "$j" && cd "$j" && ... && cd ..
```

That's almost right, but there's one problem -- what happens if `$j`
contains a slash? Then `cd ..` will not return to the original
directory. That's wrong! `cd -` causes cd to return to the previous
working directory, so it's a much better choice:

``` bash
$ mkdir "$j" && cd "$j" && ... && cd -
```

(If it occurred to you that I forgot to check for success after cd -,
good job! You could do this with `{ cd - || break; }`, but I'm going to
leave that out because it's verbose and I think it's likely that we will
be able to get back to our original working directory without a
problem.)

So now we have:

``` bash
sh $ for i in *.zip; do j=$(basename "$i" ".zip"); mkdir "$j" && cd "$j" && unzip ../$i && cd -; done
```

``` bash
bash $ for i in *.zip; do j="${i%.zip}"; mkdir "$j" && cd "$j" && unzip ../$i && cd -; done
```

Let's throw the `unzip` command back in the mix:

``` bash
mkdir "$j" && cd "$j" && unzip ../$i && cd -
```

Well, besides word splitting, there's nothing terribly wrong with this.
Still, did it occur to you that unzip might already be able to target a
directory? There isn't a standard for the `unzip` command, but all the
implementations I've seen can do it with the -d flag. So we can drop the
cd commands entirely:

``` bash
$ mkdir "$j" && unzip -d "$j" "$i"
```

``` bash
sh $ for i in *.zip; do j=$(basename "$i" ".zip"); mkdir "$j" && unzip -d "$j" "$i"; done
```

``` bash
bash $ for i in *.zip; do j="${i%.zip}"; mkdir "$j" && unzip -d "$j" "$i"; done
```

There! That's as good as it gets.
