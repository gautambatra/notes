# sed

## Quick Reference
```bash
# replace hello with world in input.txt and write to output.txt
sed 's/hello/world/' input.txt > output.txt
cat input.txt | sed 's/hello/world/' > output.txt

# replace in place (-i) in input.txt
sed -i 's/hello/world/' input.txt 

# suppress stdout (-n) and print line 45
sed -n '45p' input.txt

# print 1st line of one.txt, last line of three.txt
sed -n '1p;$p' one.txt two.txt three.txt

# use -e / -f to specify a script/script file respectively
# following are equivalent
    sed 's/hello/world/' input.txt > output.txt

    sed -e 's/hello/world/' input.txt > output.txt

    sed --expression='s/hello/world/' input.txt > output.ext

    echo 's/hello/world/' > myscript.sed
    sed -f myscript.sed input.txt > output.txt
    sed --file=myscript.sed input.txt > output.txt

# command syntax: [addr]
# delete lines 30-35
sed '30,35d' input.txt > output.txt

# print all lines till line starting with foo is found. If found, quit with status 42, else exit with 0
sed '/^foo/q42' input.txt > output.txt

# Delete lines starting with foo and replace hello with world
# following are equivalent
    sed '/^foo/d; s/hello/world/' input.txt > output.txt
    
    sed -e '/^foo/d' -e 's/hello/world/' input.txt > output.txt
    
    echo '/^foo/d' > script.sed
    echo 's/hello/world/' >> script.sed
    sed -f script.sed input.txt > output.txt
```

`-E` : extended regex (like egrep)


