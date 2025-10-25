## seeing file permission
-a = all cuz without this, it might hide some files like .gitignore, .config, .profile.
```
$ ls -la ~/.ssh/prod_key

-rw------- 1 you you 2602 Sep 15 14:23 /home/you/.ssh/prod_key
```

so the first character (- in this case) is d for directory, - for a regular file, l for a symbolic link, etc.

Then theres 9 characters - first 3 being the owner permission, the next 3 being the group permission and the last 3 being others' permission.

Owner has read and write permission so 600 permission for this file. This means me (user) is the only one that can read/write. Which is good.

## change file permission via chmod
if pem file is anything other than 600, SSH refuses it for security reasons.
```
chmod 600 company-ssh-key.pem

## others
chmod 600 file.pem    # Set specific permissions
chmod +x script.sh    # Add execute permission
chmod -w file.txt     # Remove write permission
```

## change via **symbolic mode**
In symbolic mode, you use letters like **u (not o)** for the user (owner), g for the group, and o for others. You can add permissions with a +, remove them with a -, and set them exactly with an =. For instance, chmod u+r adds the read permission for the owner, and chmod g-w removes the write permission for the group.

tricky example is 
chmod o=r-x dir/. I thought this adds read but delets executer permission for others. 

The **characters immediately following the equals sign** (r-x) are treated as the list of permissions to set. So this r-x is chained and this hyphen acts as a "separator", not removing. But more common way is to not include the hyphen so like chmod o=rx dir/

## just rechecking file's content
if u just need to see the first line of that file

head: The command to output the beginning of a file.
-n number: show the top n lines of that file

both comamnds work fine
```
$ head -n 1 ~/.ssh/prod_key
$ head 1 ~/.ssh/prod_key
```
