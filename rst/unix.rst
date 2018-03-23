Basic Unix shell scripting
--------------------------

Basic Unix shell commands are quite useful in day to day software development.

Let's assume you have the file with 10,000 lines defining the functions as discussed in "Generating input data using Python". In order to inspect this file, you could open it in the text editor, but there are also other, typically easier, ways to achieve this.

In the following, the character '$' means the shell prompt, i.e. you typically type in the shell whatever comes after '$'.

If you wanted to take a quick look browsing a file, you could use the utility 'less':

.. code-block:: bash

    $ less test.txt

This will open the file test.txt in 'less', which allows you to read and browse the file. Less has a few useful commands:

* 'q' exits less
* '/' allows you to search in the file - type '/' followed by the string you want to search for, followed by 'return'
* 'n' repeats the previous search
* 'N' repeats the previous search but searches backwards

There are a few more, and you can look up the reference for 'less' to see all of them (by running "man less"). These same commands are to some degree shared across several other Unix tools. For example vim has the same commands for searching.

Let's say you only wanted to see the beginning of the file. You can achieve this using 'head':

.. code-block:: bash

    $ head test.txt
    7.269553 3.427526 6.633603
    1.980206 -3.655827 -2.629755
    -8.687820 -6.930905 -8.731439
    -0.608791 -8.126272 -8.652504
    [...]

This will print the first ten lines of the file. You can adjust the number of lines output by using the "-n" switch. For example, the command "head -n5 test.txt" would only output the first five lines.

The command 'tail' can be used to output the last lines of a file, by default ten:

.. code-block:: bash

    $ tail test.txt
    [...]
    -7.058864 -5.461025 -8.337095
    -0.197971 -1.485949 -0.748672
    -3.051099 -9.215679 3.597125
    0.868340 -2.444818 -3.173135

If you want to search for a string in the file, you can use 'grep':

.. code-block:: bash

    $ grep "1234" test.txt
    6.133025 -4.612349 4.969612
    -0.904910 -5.976920 8.821234
    -6.123416 7.494195 4.350390
    -1.123468 0.327963 1.291879

This command would print all lines which have the string "1234" in them. The command 'grep' also has a lot of useful switches, for example including lines before or after the line that includes the search string, or inverse search (only print lines that don't have a match).

*Exercise*: Find out which grep switch makes grep only select non-matching lines.

If you only wanted to print one column of the file, you could achieve this using 'awk':

.. code-block:: bash

    $ awk '{print $1}' test.txt
    7.269553
    1.980206
    -8.687820
    -0.608791
    [...]

This example only prints the first column in the file. ('awk' by default assumed the space is the delimiter character.)

'awk' is actually a programming language capable of much more, but much of the daily use is simple one-liners. Let's say we want to calculate the average of all values in the first column. This can be achieved using 'awk':

.. code-block:: bash

    $ awk '{sum += $1; n++} END {print sum / n}' test.txt
    0.0478027

What this line means is:

* For each line, take the value of the number in the first column, and add it in the variable called "sum" (automatically initialised to 0). Increment the variable "n" by one (also automatically initialised to 0).
* After the file has been processed, print "sum" divided by "n", i.e. the sum divided by the number of lines.

Another useful command is 'wc', or "word count", which can be used to count the words in a file:

.. code-block:: bash

    $ wc test.txt
    10000  30000 284890 test.txt

The output includes the number of lines, words, characters and the file name. By adding the switch '-l', only the number of lines and the file name are output:

.. code-block:: bash

   $ wc -l test.txt
   10000 test.txt

The final command introduced here is 'sed', or "stream editor", which modified the input using a given expression. For example we can use 'sed' to change all space characters in the file to commas:

.. code-block:: bash

   $ sed 's/ /,/g' test.txt
   7.269553,3.427526,6.633603
   1.980206,-3.655827,-2.629755
   -8.687820,-6.930905,-8.731439
   -0.608791,-8.126272,-8.652504
   [...]

Let's break down this expression 's/ /,/g':

* The character '/' is the delimiter; the expression consists of four parts: 's', ' ', ',' and 'g':
* 's': initial command: search (and replace)
* ' ': search for the space character
* ',': replace with the comma
* 'g': do this globally, i.e. not only once per line (the default), but for all occurrences in the file.

Another example is removing all '-' characters from the file:

.. code-block:: bash

   $ sed 's/-//g' test.txt
   7.269553 3.427526 6.633603
   1.980206 3.655827 2.629755
   8.687820 6.930905 8.731439
   0.608791 8.126272 8.652504
   [...]

We can also pass multiple expressions to 'sed' by using the '-e' switch, for example to replace spaces with commas and remove the dashes:

.. code-block:: bash

    $ sed -e 's/ /,/g' -e 's/-//g' test.txt
    7.269553,3.427526,6.633603
    1.980206,3.655827,2.629755
    8.687820,6.930905,8.731439
    0.608791,8.126272,8.652504
    [...]

Unix pipelines
==============

The 'awk' command above lists the first column for all 10,000 lines which might not be what you want. Let's say you only wanted to see the first ten lines, i.e. apply the 'head' command to the output of the 'awk' command. You can achieve this using pipelines:

.. code-block:: bash

    $ awk '{print $1}' test.txt | head
    7.269553
    1.980206
    -8.687820
    -0.608791
    [...]

In this case, head doesn't take a file name as input, but instead reads from standard input, i.e. the output from 'awk'. This is the typical behaviour for Unix commands.

In general, the commands can be combined in any way, giving a lot of power to the user.

*Exercise*: Find out how many lines in the file have no '0' character in them.

Further Unix shell tips and tricks
==================================

man
~~~

The command "man" (short for "manual") allows you to browse the documentation of different tools. For example, running "man grep" will display the documentation for grep. The documentation is opened using "less", such that you can browse the text freely and exit with 'q'.

There can be multiple man pages for a single command. For example, "man signal" can mean either looking up the signal() C standard library functions or the general overview of signals. The man pages are categorised by type, such that for example category 1 means commands that can be run, 3 means C standard library functions and 7 means miscellaneous documentation. You can specify which category you mean by including it in your command, for example:

.. code-block:: bash

    $ man 7 signal

...will look up the man page on signal in category 7, providing the reader with an overview of signals in Unix.

*Exercise*: Look up the man page for the command "man".

*Exercise*: Look up the man page for the C function call "printf".

sort
~~~~

The tool "sort" sorts its input line by line. By default it sorts alphabetically, but by passing the switch "-n" it will interpret its input data numerically. It by default sorts based on the beginning of each line but this can be changed:

.. code-block:: bash

    $ sort -n test.txt | head
    -9.995596 8.887278 2.325502
    -9.995454 -0.339710 4.518171
    -9.993047 -9.059912 -0.660508
    -9.990530 -5.503126 -8.374026
    [...]

(If your sort command output seems nonsensical, it might be due to the locale set on your shell such that the decimal point is defined as ',' instead of '.', confusing sort. You should be able to fix this by running "export LC_ALL=C".)

The above sorted the data based on the first column. If we wanted to sort by the second column instead, we can use:

.. code-block:: bash

    $ sort -n -k 2 test.txt | head
    0.649875 -9.998834 2.834749
    -3.819303 -9.998413 -7.295722
    0.985071 -9.997176 1.182082
    -6.991833 -9.995815 -7.523136

"sort" also allows redefining the delimiter character using the "-t" switch. For more information, run "man sort".

Variables
~~~~~~~~~

Variable support is typically something that Unix shells have built in. That is, defining variables isn't executing a program per se, but rather using a feature of the shell itself.

Terminology wise, there are different Unix shells (called for example bash, tcsh, zsh), each with different characteristics and quirks, but each one typically implements the same core functionality, namely being compatible with the original Unix shell (sh) and conforming to the POSIX shell specification.

This example defines a variable in bash:

.. code-block:: bash

    $ MY_FILE=test.txt
    $ head -n1 $MY_FILE
    7.269553 3.427526 6.633603

In other words, defining a variable is trivial, and you can use the variable by prefixing it with a dollar sign.

Sometimes you might want to combine the variable with other bits. In those cases it's typically safe to enclose the variable with curly brackets ({ and }). This will make it clear when the variable name starts and ends. For example, if we wanted to combine two variables in one file name:

.. code-block:: bash

    $ MY_FILE_START=test
    $ MY_FILE_SUFFIX=txt
    $ echo ${MY_FILE_START}.${MY_FILE_SUFFIX}
    test.txt

echo and cat
~~~~~~~~~~~~

The command "echo" simply prints its input. For example:

.. code-block:: bash

    $ echo "hello"
    hello
    $ echo $MY_FILE
    test.txt
    $ echo "abc,def,ghi" | sed -e 's/,/ /g'
    abc def ghi

The command "cat" concatenates files. It can also be used to display the contents of a file:

.. code-block:: bash

    $ cat test.txt
    7.269553 3.427526 6.633603
    1.980206 -3.655827 -2.629755
    -8.687820 -6.930905 -8.731439
    -0.608791 -8.126272 -8.652504
    [...]

Exit codes
~~~~~~~~~~

Whenever you've finished running a program in Unix, it will return an exit code. The convention is that the exit code 0 means success while non-0 means failure. You can typically see the conditions under which a program returns success or failure by looking at the documentation. For example, grep returns exit code 1 if the search string was not found at all. You can use the special built-in variable $? to access the exit code:

.. code-block:: bash

    $ grep 2345 test.txt
    5.145898 3.219212 3.234599
    3.323714 3.883829 -4.722345
    6.142345 -4.611688 0.817618
    -7.761082 9.886385 -5.742345
    $ echo $?
    0
    $ grep 23456 test.txt
    $ echo $?
    1

Multiple commands
~~~~~~~~~~~~~~~~~

You can run multiple commands in series in one line. The following runs "head", followed by "tail":

.. code-block:: bash

    $ head -n 1 test.txt ; tail -n 1 test.txt
    7.269553 3.427526 6.633603
    0.868340 -2.444818 -3.173135

You can also run multiple commands depending on the exit code of the previous execution. The shorthand "&&" means "run the following command only if the previous command succeeded, i.e. returned an exit code 0". The shorthand "||" means "run the following command only if the previous command failed". You can also group commands using parentheses. For example:

.. code-block:: bash

    $ (grep 2345 test.txt && echo "found") || echo "not found"
    5.145898 3.219212 3.234599
    3.323714 3.883829 -4.722345
    6.142345 -4.611688 0.817618
    -7.761082 9.886385 -5.742345
    found
    $ (grep 23456 test.txt && echo "found") || echo "not found"
    not found

Globbing
~~~~~~~~

Globbing refers to using special characters to match multiple files. An example is "\*.py" which means "all files with the extension .py in the current directory". For example, to find out the number of lines in Python files:

.. code-block:: bash

    $ wc -l *.py
     156 conf.py
       8 gen.py
       4 rand.py
       3 with.py
     171 total

Seq
~~~

The command "seq" simply outputs a sequence of numbers:

.. code-block:: bash

    $ seq 1 5
    1
    2
    3
    4
    5

This might not be very useful by itself but can be handy when combined with other tools.

Find
~~~~

Find is useful for finding files, and optionally performing operations on them.

For example, let's assume you have a directory with subdirectories, with the directory and subdirectories having lots of Python files. Let's further assume you had used the Python "with" statement in some of the files and would like to see how, but you can't remember which files exactly use "with". Find and grep to the rescue:

.. code-block:: bash

    $ find . -name '*.py' -exec grep with {} +
    ./conf.py:# extensions coming with Sphinx (named 'sphinx.ext.*') or your custom
    ./with.py:with open('test.txt', 'w') as f:

Let's go through this point by point:

* We execute find with several parameters
* The first parameter is '.', i.e. search in the current working directory (as well as subdirectories)
* Search for files with the extension '.py'
* For each found file, run "grep with $filename". The notation {} means the found file name will be used here, and the final '+' means the grep command will be run for once with all the files as parameters. For example, if the find command found three Python files, ./a.py, subdir/b.py and subdir2/c.py, it would execute "grep with ./a.py subdir/b.py subdir2/c.py".

The output has two lines: one with grep matching in conf.py, where a comment using the word "with", and another in with.py where the Python with statement was used.

If we only wanted to find the files with the Python extension without grepping, we simply leave out the -exec part:

.. code-block:: bash

    $ find . -name '*.py'
    ./tmp/config.py
    ./conf.py
    ./my_project/hello.py
    ./guess/guess.py
    ./with.py
    ./rand.py

Redirecting
===========

You can always write the output of any command to a file by redirecting, i.e. using the '>' character:

.. code-block:: bash

    $ awk '{print $1}' test.txt > output.txt

This will create a file called output.txt, overwriting any previous contents, with the output of the 'awk' command.

You can also append to the end of an existing file, by using '>>':

.. code-block:: bash

    $ awk '{print $1}' test.txt >> output.txt

Redirecting will allow us to simplify writing our own software. For example, it might not be necessary to open a file for writing in Python, so instead of this:

.. literalinclude:: ../material/py/with.py
   :language: python

...we can simply write this in Python:

.. code-block:: python

    for i in xrange(5):
        print "%f %f\n" % (0.5, 0.2)

...and then run it like this:

.. code-block:: bash

    $ python my_file.py > test.txt

This has the added flexibility that we don't have to hard code the output file name.

If necessary, you can also discard the output by redirecting it to the special file /dev/null, which has the sole purpose of consuming and discarding all its input:

.. code-block:: bash

    $ python my_file.py > /dev/null

*Exercise*: rewrite your program that generates the 10,000 functions file to write to standard output.

If you have a program that reads from standard input (stdin), you can have it read a file with a redirection:

.. code-block:: bash

    $ python script.py < myfile.txt

Shell scripts
=============

Simple for loops
~~~~~~~~~~~~~~~~

In most Unix shells including bash you can define for loops. For example, let's say you wanted to run your number generation program three times, and store the output to different files:

.. code-block:: bash

    $ for i in 1 2 3; do python gen.py > functions_${i}.txt ; done

This will generate three files named functions_1.txt, functions_2.txt and functions_3.txt.

If we wanted to generate a hundred files, typing in the number would get tedious. We can use "seq" instead:

.. code-block:: bash

    $ for i in $(seq 1 100); do python gen.py > functions_${i}.txt ; done

This will generate a hundred files. The notation $(...) allows capturing the output of a command within another command. By using curly braces ({}) around the 'i' variable we ensure the variable name gets correctly understood by the shell and not confused with the file name template.

if and branches
~~~~~~~~~~~~~~~

The shell built-in command "if" allows branching. There are other built-ins for comparing values against each other. For example:

.. code-block:: bash

    $ grep -q 2345 test.txt ; return_value=$?; if [ $return_value -eq 0 ]; then echo "found"; else echo "not found"; fi

This command will output "found" if the string was found in the input file, and "not found" otherwise. (The "-q" switch to grep suppresses output.)

Storing scripts in a file
~~~~~~~~~~~~~~~~~~~~~~~~~

It's often practical to store scripts to a file. Here's the previous example stored in a file:

.. code-block:: bash

    #!/bin/bash

    grep -q 2345 test.txt
    return_value=$?
    if [ $return_value -eq 0 ]; then
        echo "found"
    else
        echo "not found"
    fi

The script behaves similarly as the one-liner, but the script has one more line: it starts with "#!/bin/bash". This is also called shebang, a character sequence at the beginning of a script indicating which tool should be used to interpret the script (in this case bash).

You can store the above in a file, and then execute it by passing it to bash explicitly:

.. code-block:: bash

    $ bash ./test.sh
    found

Or you can simply make it executable, in which case it can be directly interpreted using bash:

.. code-block:: bash

    $ chmod +x test.sh
    $ ./test.sh

(The command "chmod" modifies the access rights to a file, and passing it "+x" means the file is allowed to be executed.)

Process handling
================

Process handling refers to managing how and when programs (processes) start and end.

Typically, unless overridden by the program, hitting ctrl+c sends SIGTERM (signal to terminate) to the current program. SIGTERM is a signal that tells a program it should terminate, and by default will kill the program. However programs are allowed to install a *signal handler* for SIGTERM so they can perform any necessary cleanup before terminating, for example closing and saving any files that have been modified.

Another useful key combination is ctrl+z which stops the program execution and puts it in the background. This means that you can start a process, for example "less test.txt", then exit the program but keep it running by pressing ctrl+z which will drop you back to the shell. Let's go through an example usage:

.. code-block:: bash
    :linenos:

    $ cp -i big_file.tgz /mnt
    ^Z
    [1]+  Stopped                 cp -i big_file.tgz /mnt
    $ bg
    [1]+ cp -i big_file.tgz /mnt &
    $ ls
    Makefile  dep.rst   ex_js2.rst
    $
    [1]+  Done                    cp -i big_file.tgz /mnt

Let's assume we have a very large file we want to copy somewhere. Copying large files can take time, and cp by default doesn't output anything while copying. We start the copy on line 1.

After waiting for some time, we get bored and want to do something else so we press ctrl+z. This is shown in the terminal with ^Z (line 2). Pressing this key combination will tell us that the copy command has been stopped (line 3), and drops us back in our shell (line 4).

We then issue the command "bg" which means the previous command should run in the *background*, meaning it can run but should not prevent us from using the shell. Issuing this command tells us on line 5 that the copy command continues to run again, pointed out by the & sign at the end of the command.

On line 6 we can then do what we want, e.g. run "ls".

On line 7, we have the prompt again and wait for some time, until we expect the copy command to have finished. We can check if this is indeed the case by hitting enter (doing nothing). Bash uses this opportunity to tell us that the copy operation indeed has finished (line 9).

If you're running a command and you want to start it on the background right away, you can add the & sign to the end of the command:

.. code-block:: bash

    $ cp -i big_file.tgz /mnt &
    [1] 23904
    $

If you have a command running in the background but want to run it on the foreground again, run "fg":

.. code-block:: bash

    $ cp -i big_file.tgz /mnt &
    [1] 23904
    $ fg
    cp -i big_file.tgz /mnt
    $

If you want to see which commands you have running in the background in the current terminal, run "ps":

.. code-block:: bash

    $ ps
      PID TTY          TIME CMD
     5898 pts/8    00:00:01 bash
    23904 pts/8    00:00:00 cp
    23905 pts/8    00:00:00 ps

If you want to send a SIGTERM signal to another process than what's currently running in the foreground, you can use the "kill" command with the PID (process ID) of the process you want to kill, for example:

.. code-block:: bash

    $ cp -i big_file.tgz /mnt &
    [1] 23904
    $ ps
      PID TTY          TIME CMD
     5898 pts/8    00:00:01 bash
    23904 pts/8    00:00:00 cp
    23905 pts/8    00:00:00 ps
    $ kill 23904
    [1]+  Terminated              cp -i big_file.tgz /mnt
    $

Note that bash tells you the PID of the newly started process when you start it on the background using &.

Because a program can install a signal handler for SIGTERM, it's possible that sending SIGTERM to a program won't kill it. To force kill a program you need to send the signal SIGKILL (signal 9 as can be seen from the signal man page at "man 7 signal"):

.. code-block:: bash

    $ cp -i big_file.tgz /mnt &
    [1] 23937
    $ kill -9 23937
    $
    [1]+  Killed                  cp -i big_file.tgz /mnt
    $

*Exercise*: Start "less" to browse a file, hit ctrl+z to move it to the background, then run "fg" to bring it to foreground again.