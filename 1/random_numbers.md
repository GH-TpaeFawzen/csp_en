# Random Numbers
If you want to get random numbers, it's off-topic that you use `RANDOM` -- bash built-in variable.
You may instead think of using rand and srand functions of AWK --- but hold on!

In FreeBSD try the following command; you see it is practiceless.

```
$ for n in 1 2 3 4 5; do awk 'BEGIN{srand();print rand();}'; sleep 1; done
0.0205896
0.0205974
0.0206052
0.020613
0.0206209
$ 
```

What I would like to say is that some environment generates random numbers in
quite bad quality. The reason why it happens is that
FreeBSD leaves rand() and srand() from OS library, which AWK internally adopt,
original low-quality version and it serves other new high-quality function
random() (Linux, on the other hand, merges rand() and srand() with random()).

## `/dev/urandom` is Realistic
So, what should you do?
`/dev/urandom`, though it's not defined by POSIX, is realistic for seed ---
here is the way to generate a random number in range of 0--424967925.

```
$ od -A n -t u4 -N 4 /dev/urandom | tr -d '\t '
```

## In case You'd Rather Not Adopt `/dev/urandom`
Though it generates random numbers less quality than `/dev/urandom`,
you have another method;
ps command always outputs different results whenever you run it, so
let it be the seed.

In paticular, you should use list of process ID, execution time, CPU usage rate, and memor usage amount
as they vary by time.
You should also use current time and pass all of them in range of <2^32 to srand() of AWK:

```sh
LF=$(printf '\\\n_');LF=${LF%_}     # to replace to LF with sed
(ps -Ao pid,etime,pcpu,vsz; date) | # seeds
od -t d4 -A n -v                  | # convert to integers
sed 's/[^0-9]\{1,\}/'"$LF"'/g'    |
grep '[0-9]'                      |
tail -n 42                        | # prepare up to 42 integers
sed 's/.*\(.\{8\}\)$/\1/g'        | # that are <=100,000,000
awk 'BEGIN{a=-2147483648;}        # # sum up them
     {a+=$1;}                     #   to make a signed long value
     END{srand(a);print rand();}' # # ('a' should be <2^32)
```
