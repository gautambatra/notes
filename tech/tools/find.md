# find

```bash

# find an grep files that have multiple patterns (AND)
find . -type f -exec grep -l 'pat1' {}\; | xargs grep -l 'pat2'
```
