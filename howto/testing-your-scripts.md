The one of the simplest way to check your bash/sh scripts is run it and
check it output or run it and check the result. This tutorial shows
how-to use [bashtest](https://github.com/pahaz/bashtest) tool for
testing your scripts.

### Write simple util

We have a simple **stat.sh** script:

    #!/usr/bin/env bash

    if [ -z "$1" ]
    then
        DIR=./
    else
        DIR=$1
    fi

    echo "Evaluate *.py statistics"
    FILES=$(find $DIR -name '*.py' | wc -l)
    LINES=$((find $DIR -name '*.py' -print0 | xargs -0 cat) | wc -l)
    echo "PYTHON FILES: $FILES"
    echo "PYTHON LINES: $LINES"

This script evaluate the number of python files and the number of python
code lines in the files. We can use it like **./stat.sh \<dir\>**

### Create testsuit

Then make test suits for **stat.sh**. We make a directory **testsuit**
which contain test python files.

**testsuit/main.py**

    import foo
    print(foo)

**testsuit/foo.py**

    BAR = 1
    BUZ = BAR + 2

Ok! Our test suit is ready! We have 2 python files which contains 4
lines of code.

### Write bashtests

Lets write tests. We just write a shell command for testing our work.

Create file **tests.bashtest**:

    $ ./stat.sh testsuit/
    Evaluate *.py statistics
    PYTHON FILES:        2
    PYTHON LINES:        4

This is our test! This is simple. Try to run it.

    # install bashtest if required!
    $ pip install bashtest

    # run tests
    $ bashtest *.bashtest
    1 items passed all tests:
       1 tests in tests.bashtest
    1 tests in 1 items.
    1 passed and 0 failed.
    Test passed.

Thats all. We wrote one test. You can write more tests if you want.

    $ ls testsuit/
    foo.py  main.py

    $ ./stat.sh testsuit/
    Evaluate *.py statistics
    PYTHON FILES:        2
    PYTHON LINES:        4

And run tests again:

    $ bashtest *.bashtest
    1 items passed all tests:
       2 tests in tests.bashtest
    2 tests in 1 items.
    2 passed and 0 failed.
    Test passed.

You can find more **.bashtest** examples in the [bashtest github
repo](https://github.com/pahaz/bashtest). You can also write your
question or report a bug
[here](https://github.com/pahaz/bashtest/issues).

Happy testing!
