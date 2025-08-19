## $?
$? stores the exit code of most recently executed command and stores just one exit code (the most recent)
```
$ ls /nonexistent
ls: cannot access '/nonexistent': No such file or directory

$ echo $?
2
```
