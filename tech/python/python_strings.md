# Python Strings
* Can be surrounded in single or double quotes
* If need a quote in the string, can either escape it using `\` or use the other type of quotes around the string:
```python
a = 'doesn\'t'
b = "doesn't"
c = 'they said "yes"'
```

* Use raw strings to keep the backslashes instead of escaping the character that follows:
```python
print (r'C:\some\path') # prints the string as is
```

* Use triple quotes for multi-line strings:
```python
print("""\
Usage: thingy [OPTIONS]
     -h                        Display this usage message
     -H hostname               Hostname to connect to
""")
```

* Use negative indices to access elements from end
```python
word[-1]  # last character
word[-2]  # second-last character
```

* Use slicing for substrings
```python
word[2:5]  # characters from position 2 (included) to 5 (excluded)
word[:2]   # character from the beginning to position 2 (excluded)
word[4:]   # characters from position 4 (included) to the end
word[-2:]  # characters from the second-last (included) to the end
```

* Strings are immutable - cannot be changed
```
word[0] = 'J'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'str' object does not support item assignment
```

* String length: `len(s)`

* Return string version of an object: `str(obj)`

* String methods
    `count` : get number of occurrences of a substring
    `endswith` : check if string ends with the given suffix
    `find` : return position of first occurrence of given substring

* Modulo operator `%`
```
$> name = "World"
$> "Hello, %s!" % name
'Hello, World!'

$> name = "Jane"
$> age = 25

# Use tuple for multiple replacements
$> "Hello, %s! You're %s years old." % (name, age) 
'Hello, Jane! You're 25 years old.'

# Can use dictionary
$> "Hello, %(name)s! You're %(age)s years old." % {"name": "Jane", "age": 25}
"Hello, Jane! You're 25 years old."

# Can specify precision
$> "Balance: $%.2f" % 5425.9292
'Balance: $5425.93'

# Can specify alignment
$> print("Name: %s\nAge: %5s" % ("John", 35))
Name: John
Age:    35

```

# str.format() Method
```
# Curly braces delimit the replacement fields
$> name = "Jane"
$> age = 25

$> "Hello, {}! You're {} years old.".format(name, age)
"Hello, Jane! You're 25 years old."

# Can specify position in the curly braces
$> "Hello, {1}! You're {0} years old.".format(age, name)
"Hello, Jane! You're 25 years old."

# Can use argument names to improve readability
$> "Hello, {name}! You're {age} years old.".format(name="Jane", age=25)
"Hello, Jane! You're 25 years old."

# Can use dictionaries with the dictionary unpacking operator (**)
$> person = {"name": "Jane", "age": 25}

$> "Hello, {name}! You're {age} years old.".format(**person)
"Hello, Jane! You're 25 years old."

# Can use format specifiers
$> "Balance: ${:.2f}".format(5425.9292)
'Balance: $5425.93'
```

# F-strings (Formatted String Literals)
* Prefixed with `f` or `F`
* May contain replacement fields `{expr}`
* Not constant - evaluated at run time
```
$> name = "Jane"
$> age = 25

$> f"Hello, {name}! You're {age} years old."
'Hello, Jane! You're 25 years old.'


$> f"{2 * 21}"
'42'

$> f"Hello, {name.upper()}! You're {age} years old."
"Hello, JANE! You're 25 years old."

$> f"{[2**n for n in range(3, 9)]}"
'[8, 16, 32, 64, 128, 256]'

$> balance = 5425.9292

$> f"Balance: ${balance:.2f}"
'Balance: $5425.93'
```
