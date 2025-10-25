## env variable
* **Environment Variables:** These are named values stored within the operating system that affect how processes (running programs) will run. They are a way for a program to get
* configuration settings or information about its operating environment *without* needing them hard-coded. Examples are `$PATH` ,`$HOME`, `$USER`.

## path variable
it is a type of environment variable that tells the shell (like Bash or Zsh) **where to look** for executable programs when you type a command. It contains a list of directories, 
separated by colons (`:`). When you type a command like `ls` or `chmod`, the shell searches each directory listed in the **PATH** until it finds the corresponding program.

## viewing or adding env variable
to view, use `printenv` or `env` in the terminal.

when u wanna set a **temporary env variable**, use `export VAR_NAME (like PATH)= value`. This env var will exist until u shut down the terminal. 

if u want permanent way, u have to edit the config file, which in linux is at `~/.bashrc`. Then using sudo vim, like for example u wanna add ur script path, then
```
# Add my custom scripts directory to the PATH
export PATH="$PATH:/home/user/my_scripts"

## and then pply the Changes
source ~/.bashrc
```
