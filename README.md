# findscript
# *Filenames* can contain *any* character except only NUL (`$'\0'`) and slash
# (`/`); therefore, *paths* can contain any character except NUL. Here are some
# guidelines to handle them:
#
# Strings containing variables should always be double-quoted to avoid
# accidental word splitting. The only exception from this is the
# `[[ "var" =~ regex ]]` construct, where the `regex` string must not be quoted.
#
# `$'...'` can be used to create human readable strings with escape sequences.
# The complicated file name below is used to test that the code is able to
# handle several common filename issues.
#
# ` -- ` in commands is necessary to separate arguments from filenames, since
# filenames can start with `--`, and would therefore be handled as parameters.
# To handle parameters properly (like most *nix tools) use GNU `getopt` or the
# Bash built-in (but more limited) `getopts`.
#
# GNU `find` doesn't support the ` -- ` separator. This is only a problem if the
# search path is a relative path which *doesn't* start with `./` (since absolute
# paths by definition start with a slash, they are not a problem). If the
# application should support *any* filename, such paths have to be either
# converted into absolute paths by using readlink or by adding `./` to the start
# of relative paths.
#
# Variables inside `$()` have to be quoted just like outside this construct. No
# additional quoting or escaping is necessary.
#
# `read` usually strips characters in `$IFS` from the start and end of the
# resulting variable. Therefore `$IFS` must be emptied when reading a line.
#
# `read`'s `-r` argument avoids interpreting backslash in filenames specially.
#
# `read`'s `-d ''` argument splits the input by the null character.
#
# The `$()` and <code>\`\`</code> constructs *remove trailing newlines*. Getting
# the original string printed by the commands inside it is a simple matter of
# adding a single printable character after the last command and then remove it
# on the next line without using another `$()`.
#
# `basename` adds a newline to its output, so it has to be stripped in addition
# to the trailing character.
#
# `printf %q` is a way to print a string so that the result can be copied and
# pasted into a script, giving the same meaning as the original input string.
# This can be very useful to see what a variable *really* contains, including
# special characters like backspace.
#
# Using process substitution with `<(` instead of pipes avoids a broken pipe in
# case a process inside the loop exits.
#
# `find`'s `-exec printf '%s\0'` argument separates the output by null characters.
#
# Use a file descriptor between 3 and 9 for data passing instead of standard
# input to avoid greedy commands like `cat` or `ssh` slurping all of it.
