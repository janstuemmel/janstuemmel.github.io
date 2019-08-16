A bunch of things about shell scripting i always forget. Writing it down helps me to keep that in mind.

## SH vs Bash

[Bash](https://en.wikipedia.org/wiki/Bash_(Unix_shell)) (Bourne-again shell) is basically a superset of [SH](https://en.wikipedia.org/wiki/Bourne_shell) (Bourne shell) and has more functionality (e.g. arrays). On a few systems the `sh` command is just symlink to bash. But bash behaves more [POSIX](https://en.wikipedia.org/wiki/POSIX) compliant when it's called via `sh`. Look also at this [Stackoverflow post](https://stackoverflow.com/questions/5725296/difference-between-sh-and-bash). 

## SH scripting

The sh scripting language is very simple. Almost everything in sh is a command or a symlink to a command. There are variables, conditionals, loops and functions. You can type commands directly in your terminal or put it in a file. The script can then be executed with `sh file.sh`. 

You can also put a [shebang](https://en.wikipedia.org/wiki/Shebang_(Unix)) on the first line `#!/bin/sh` and give execution rights to the file `chmod +x file.sh`. Now you can execute your script with `./file.sh`. 

### Variables

In sh you can define variables in the scope of a script like this: `FOO=1`. If you want them persist over a terminal session you can type `export FOO=1`. To return their values type `echo $FOO`.

```sh
#!/bin/sh

FOO=foo
${BAR:-bar} # default value "bar" for BAR, can be set from outside the script

echo $FOO $BAR # prints: foo bar
echo ${FOO}baz # prints foobaz
echo ${FOO} # prints string length of "foo"

${BAZ:?variable BAZ missing} # exits script if variable BAZ is missing 

echo $BAZ
```

To execute the script above prepend a `BAR=blabla` in front of `script.sh` or the script exits with message "variable BAR missing".

Type `env` or `export -p` into your terminal to see all environment variables.

### Parameters

Commandline arguments you can use inside a script. Example shows the execution of a script `foo.sh` with arguments `one two`. The output will remain the same dispite executing with `sh foo.sh one two` or `./foo.sh one two` except `$0`.

```sh
#!/bin/sh
echo Scriptname: $0
echo Number of args: $#
echo All args: $@
echo All args expanded: "$@"
echo First arg: $1
echo Second arg: $2
echo Process number: $$
echo Last command exit code: $?
sh -stfu 2> /dev/null # silent stderr
echo Last command exit code: $?
```

Output:

```
Scriptname: ./foo.sh
Number of args: 2
All args: one two
All args expanded: one two
First arg: one
Second arg: two
Process number: 15442
Last command exit code: 0
Last command exit code: 2
```

### Conditional - the `test` command

The sh `if` takes a command as parameter, e.g. `if ls; then ...; fi`. If the command exits with `0` the condition is `true`. To check a condition we have a progam called `test`. `test 2 -lt 3` would exit with code `0` because 2 is lower then 3. With this program we can check conditions in a if statement:

```sh
if test 2 -lt 3
  then echo "2<3"
fi
```

There's also the shorthand `[` for `test`. `[` is a simple symlink to `test`. When `test` gets called via `[` it expects a closing `]`. Now we can write scripts like:

```sh
if [ 2 -lt 3 ]
  then echo "2<3"
fi
```

Following example shows a few capabilities of the `test` command. For more see the [wikipedia](https://en.wikipedia.org/wiki/Test_(Unix)) article. Execute the script with `./foo.sh 5`:

```sh
#!/bin/sh

# if/else
if [ $1 -le 2 ] 
  then echo "$1 <= 2"
elif [ $1 -le 4 ]
  then echo "$1 <= 4"
else
  echo "i can't count to $1"
fi

# numbers
if [ 1 -eq "1" ]; then echo "1 == 1"; fi  # equal
if [ 1 -ne 2 ]; then echo "1 != 2"; fi    # not equal
if [ 2 -gt 1 ]; then echo "2 > 1"; fi     # greater then
if [ 2 -ge 2 ]; then echo "2 >= 2"; fi    # greater equal
if [ 1 -lt 2 ]; then echo "1 < 2"; fi     # lower then
if [ 1 -le 1 ]; then echo "1 <= 2"; fi    # lower equal

# strings
if [ "foo" = "foo" ]; then echo '"foo" == "foo"'; fi
if [ "foo" != "bar" ]; then echo '"foo" != "bar"'; fi
if [ -z "" ]; then echo '"" is empty'; fi
if [ -n "foo" ]; then echo '"foo" is not empty'; fi

# always use "$VAR" in quotes if you checking string variables
if [ "$1" = "5" ]; then echo "$1 == 5"; else echo "$1 != 5"; fi

# check folders and files
if [ -f "$0" ]; then echo "me exists"; fi
mkdir foo
if [ -d "./foo" ]; then echo "folder exists"; fi
rm -r foo
if [ ! -d "./foo" ]; then echo "folder removed"; fi
```

### Loops 

Expected! There are loops in sh. Nice, let's see how they work.

```sh
#!/bin/sh

# while loop with break condition
i=0
while [ $i -le 10 ]
do
  echo $i
  i=$(( $i + 1 ))
done

# infinite loop 
while [ : ]
do 
  echo 1
  break # we don't want you to run infinite :)
done

# for loop with list [one, two, three]
for i in one two three
do
  echo $i
done

# for loop with list from command (all files/dirs in $PWD that end with .sh)
for i in $(ls *.sh)
do 
  echo $i
done

# loop through all file names in $PWD that end with .sh
for i in *.sh
do 
 echo $i
done

# loop through all command line arguments
for i in $@
do
  echo $i
done

# same as above, but shorter
for i
do
 echo $i 
done
```

### Functions

To avoid writing repetitive tasks there are functions in sh. Function arguments are not taken via `foo(1, "two")`, instead functions work like normal sh commands, parameters are passed via `$@`, `$1`, `$2`, etc. 

```sh
#!/bin/sh

hello() {
  echo "hello $@"
}

hello world !
```

## References

* [FU Berlin - Shell Scripts sh (Bourne Shell)](http://kirste.userpage.fu-berlin.de/chemnet/general/topics/scripts_sh.html)
* [Linux-praxis.de - Das Programmieren der Shell](http://www.linux-praxis.de/linux1/shell2.html)
* [Comparison of command shells](https://en.wikipedia.org/wiki/Comparison_of_command_shells)