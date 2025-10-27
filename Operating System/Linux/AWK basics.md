## awk
{ } = This is the action block - it runs for every line
```
awk 'pattern { action }' filename
```

## getting unique ip addresses
mjust sort first! 
```
cat /var/log/app.log | awk '{print $1}' | sort | uniq
#                                          ↑ MUST sort first!

# With counts
cat /var/log/app.log | awk '{print $1}' | sort | uniq -c
```

without sort, uniq wont work
```
# Without sort
echo -e "10.0.0.1\n10.0.0.2\n10.0.0.1" | uniq
# Output:
# 10.0.0.1
# 10.0.0.2
# 10.0.0.1  ← Still appears! (not consecutive)

# With sort
echo -e "10.0.0.1\n10.0.0.2\n10.0.0.1" | sort | uniq
# Output:
# 10.0.0.1
# 10.0.0.2  ← Only unique IPs!
```

## Example file
```
John 25 Engineer
Mary 30 Doctor
Bob 22 Student
Alice 28 Teacher
```

## NR - Line Number (Number of Records)
For the line `John 25 Engineer`:

| Variable | Contains | Description |
|----------|----------|-------------|
| `$0` | `"John 25 Engineer"` | Whole line |
| `$1` | `"John"` | Field 1 |
| `$2` | `"25"` | Field 2 |
| `$3` | `"Engineer"` | Field 3 |
| `$4` | `""` (empty) | Field 4 (doesn't exist) |
| `NF` | `3` | Total number of fields |
```
admin@ip-10-1-13-61:~$ awk '{print NR, $0}' people.txt
1 John 25 Engineer
2 Mary 30 Doctor
3 Bob 22 Student
4 Alice 28 Teacher
```

## print
```
awk '{ print }' people.txt
# Output: (same as original file)
```

## $1, $2, $3... - Individual Fields (Columns)
```
# Print first column only
awk '{ print $1 }' file.txt

# Print first and third columns
awk '{ print $1, $3 }' file.txt
```

## NF
```
awk '{ print NF }' file.txt   # Print number of columns per line
awk '{ print $NF }' file.txt  # Print content of last column
```

# Pattern matching
## match specific values
```
# Find people who are 25 years old
awk '$2 == 25 { print }' people.txt
# Output: John 25 Engineer

# Find Engineers
awk '$3 == "Engineer" { print }' people.txt
# Output: John 25 Engineer
```

## numeric comparison
```
# People over 25
awk '$2 > 25 { print }' people.txt
# Output:
# Mary 30 Doctor
# Alice 28 Teacher

# People 25 or younger
awk '$2 <= 25 { print }' people.txt
# Output:
# John 25 Engineer
# Bob 22 Student
```

## string match
1. awk '/o/ { print }' people.txt
/o/: This is the regular expression pattern. The slashes / are the delimiters that tell awk to treat the content between them as a regex.

o: This simply matches the character "o" anywhere within a line.

This command searches every line in people.txt for the letter "o" and prints the entire line if a match is found.

2. awk '$1 ~ /^A/ { print }' people.txt
$1: This refers to the first field on each line.

~: This is the match operator. It checks if the field on its left ($1) matches the regular expression on its right.

/^A/: This is the regular expression pattern.

^: This is an anchor that means "the beginning of the line or string."

A: This matches the literal character "A".


## begin and end
its like print statements
```
# BEGIN runs before reading any lines
# END runs after reading all lines

awk 'BEGIN { print "=== PEOPLE LIST ===" } 
     { print $1, $2 } 
     END { print "=== TOTAL:", NR, "people ===" }' people.txt

# Output:
# === PEOPLE LIST ===
# John 25
# Mary 30  
# Bob 22
# Alice 28
# === TOTAL: 4 people ===
```

## variable and math (impt)
so delcaring total variable first and adding ages in people.txt
```
# Count total age
awk 'BEGIN { total = 0 } 
     { total += $2 } 
     END { print "Total age:", total }' people.txt
# Output: Total age: 105

# Calculate average age
awk 'BEGIN { total = 0 } 
     { total += $2 } 
     END { print "Average age:", total/NR }' people.txt
# Output: Average age: 26.25
```

## field separators
awk by default splits on spaces/tabls but u can change like split(',')

```
# Use comma as separator
awk -F',' '{ print $1, $2 }' data.csv
# Or:
awk 'BEGIN { FS="," } { print $1, $2 }' data.csv

# Output:
# John 25
# Mary 30
# Bob 22
```
