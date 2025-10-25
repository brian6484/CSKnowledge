$? is special variable that stores *exit status of last command*.

`$? =0` means last command succeeded. But if the value is non-zero like 1,69, last command failed. Just doing `$?` alone wont print anything but `$?` just stores the value and not display it.
```
ls /home
$?
```

With echo, it prints the value
```
ls /home
echo $?
0

# or in scripts/conditionals
if [ $? -eq 0 ]; then
  echo "Success"
fi
```
