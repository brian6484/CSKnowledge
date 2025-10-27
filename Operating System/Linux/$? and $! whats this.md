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

$! holds the **PID of last background process that was started**

for example inside a scheduelr code,
```
/opt/webapp/run-job.sh > "$LOGFILE" 2>&1 &   # Start job in background
JOB_PID=$!                                    # Save its PID to variable
wait $JOB_PID                                 # Wait for that specific PID to finish
```

so wait $PID means wait for that specific child process . If that wait isnt in the scehduler, then parent never reads the child's exit status via **wait()** so child becomes a zombie.
