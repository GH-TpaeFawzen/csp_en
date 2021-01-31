# Textdata Without LF-terminator in Last Line
You should not omit the LF (0x0A) on final line of shellscript;
it may cause the script to work differently on some enviroment.

For example, here is an exercise to make

```
