# Bash

`$BASH_SOURCE`: array of  current source paths. more reliable than `$0`. simply printing $BASH_SOURCE gives current script path as it's the first element.

`$FUNCNAME`: Name of current function

`pushd/popd`: push/pop a directory to the directory stack without doing `cd`
```bash
pushd somdir
# do stuff in somedir
popd # return to original directory
```

## Special variables
`$0`: name of the current script
`$#`: number of arguments to the script
`$*`: string containing all command line arguments
`$@`: all arguments as an array
`$1-$9`: first 9 command line arguments
`$?`: status of last command/process
`$!`: pid of last process
`$$`: pid of current shell 
`$-`: current shell options


## Shell options
`set -o`: see currently set variables/options
`set -o <variableName>`: set variable
`set +o <variableName>`: unset variable
`shopt` or `shopt -p`: show current shopt options
`shopt -s optionName`: set optionName
`shopt -u optionName`: unset optionName

## set command
`set -x`: print each executed line
`set -n`: read each line but don't execute (good for checking for syntax errors)
`set -e`: exit on first error
`set -u` : treat unset variables as errors

## Useful options
`set -o vi` : enable vi editing mode
    in command mode: hit v to edit current command in text editor
    [https://catonmat.net/bash-vi-editing-mode-cheat-sheet](cheet sheet)


## Substrings
```bash
# get substring of a from index 12 of length 5
b=${a:12:5}

# remove prefix ending in "_"
tmp=${a#*_}

# remove suffix starting with "_"
tmp=${a%_*}
```

## Arrays
```bash
# length of array
len=${#myarr[@]}

# print full array in 1 line
echo "${myarr[*]}"

# print each element in a separate line
printf '%s\n' "${myarr[@]}"

# multiline variable to array
SAVEIFS=$IFS # save current IFS (Internal Field Separator)
IFS=$'\n' # change IFS to newline
names=($names) # split names string into an array with the same name
IFS=$SAVEIFS # restore IFS

# deleting single element from array
arr=(one two three)
delete=two
echo ${arr[@]/$delete}

array=("${arr[@]/$delete}") # quotes when working with strings

# deleting more than one element
delete=(two three)
for del in ${delete[@]}; do
    array=("${arr[@]/$del}")
done

```

## Comparison
```bash
# numbers
if [[ $a -gt $b ]]; then 
# ...
fi

# strings
if [[ "$a" == "$b" ]]; then
# ...
fi

```


## Test Command Flags
-z : string has zero length
-n : string has non-zero length
-f : file exists
-e : file exists and is a regular file
-d : directory exists
-s : file exists and has non-zero size


## Log redirection
```bash

# Writing stdout and stderr to screen and file
exec > >(tee -a ${logFile})
exec 2> >(tee -a ${logFile})

# Writing only to file
exec >$logfile 2>&1
```


## strace
