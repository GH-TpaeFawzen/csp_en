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
ps command --- outputs 
