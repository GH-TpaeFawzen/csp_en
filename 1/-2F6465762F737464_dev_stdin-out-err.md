# "/dev/std{in,out,err}" Files
Don't specify "/dev/stdin", "/dev/stdout", and "/dev/stderr" to do something for
standard input and outputs;
"Permission denied" happens under several conditions such as
using an environment that substitues execution users, such as
suEXEC on Linux.

## Reproduction of the Phenomenon
If you have Linux with both non-root and root permissions, try this out:

```
Using username "GENERAL_USER".
GENERAL_USER@your.service's password: 
Last login: Mon Jul 13 00:00:00 2015 from your.client
$ su - # switch to root
Password: 
# su -m apache # switch to different general user
bash: /root/.bashrc: Permission denied
$ echo HOGE >/dev/stderr
bash: /dev/stderr: Permission denied
$ echo HOGE >/dev/stdout
bash: /dev/stdout: Permission denied
$ 
```

### Cause
<!-- TODO -->

## Solutions
Here are solutions if you want to specify "/dev/std*".

### Specify /dev/stderr
Append "`1>&2`" to your command:

```sh
echo ERRMSG 1>&2
```

<!--TODO-->
```sh
exec 3>&1 1>&2 # duplicate stdout (1 by default) to 3
               # and stderr (2 by default) to 1
echo ERRMSG1 # these three lines
echo ERRMSG2 # are all output to
echo ERRMSG3 # stderr

exec 1>&3 3>&- # duplicate stderr (on 3) to 1 and delete 3
```

### Specify /dev/stdout
Just omit it; it's default

```sh
echo MSG # just omit ">/dev/stdout"
```

<!-- TODO -->

### Specify /dev/stdin
Just omit it, too, usually.

```sh
cat # omitting "</dev/stdin", read from stdin by default
```

<!-- TODO -->

### What to do in AWK
<!-- TODO -->
You don't need to do anything.

For some reasons,
**specifying "/dev/std*" on AWK does never cause Permission denied**.
Try this:

```
$ awk 'BEGIN{print "ERRMSG" > "/dev/stderr";}'
ERRMSG
$
```
