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


## strace
