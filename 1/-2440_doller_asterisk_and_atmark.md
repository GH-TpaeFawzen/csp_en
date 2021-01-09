# "`$*`" and "`$@`" Variables
You have to take care of handling two built-in variables "`$*`" and "`$@`",
who has command arguments.
When `set -u` is set on shell (to make it error when it tries to refer undefined variable),
those variables can also cause an error if no arguments are given.
The section <!-- TODO --> explains how to deal with it.
