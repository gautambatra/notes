# awk

`awk options 'selection_criteria {action}' input.txt > output.txt`

```bash
# print all lines of file.txt
awk '{print}' file.txt

# print all lines matching pattern
awk '/patttern/ {print}' file.txt

# print last column of lines matching pattern, field separator '=' 
awk -F='/pattern/ {print $NF}' file.txt
````
