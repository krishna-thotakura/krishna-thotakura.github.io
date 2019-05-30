## Frequently used Unix commands

#### Parsing text from Log file using regular expressions
Grep in its common usage prints out the entire matching line. But if you are looking to output just the matched content from each line, use grep -o option.

Usage
```
grep -o '<regex>' file 
```
For example
```
echo 'Hello World 123' | grep -o '[0-9]*'
```
