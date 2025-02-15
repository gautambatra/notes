# vim

## Scrolling
```
# scroll down
ctrl + e 

# scroll up
ctrl + y

# half page down
ctrl + d

# half page up
ctrl + u

# page down
ctrl + f

# page up
ctrl + b
```


## Splits
* :help splits

```
# max out height of current split
ctrl + w + _ 

# max out width of current split
ctrl + w + | 

# normalise all split sizes
ctrl + w + - 

# swap top/bottom or left/right split
ctrl + w + R

# break out current split to a new tab
ctrl + w + T

# close all splits except current on
ctrl + w + o
```

## Jumps
```
# go to previous jump
ctrl + o

# go to next jump
ctrl + I
```

## Misc
```
# source current file
:source %

# configure diagnostic messages
:help vim.diagnostics

# substitute text with line number
:$s/x/\=printf("%d", line('.'))

# delete around paragraph
dap

# de-indent around paragraph
<ap

# list all normal mode keymaps
:nmap

# list all normal mode keymaps containing leader key
:nmap <leader><CR>

# also show form which file the keymaps were last set
:verbose nmap leader><CR>

# substitue only on some lines
1. select the lines
2. :s/...


# start diff mode while in vim
:vertical diffsplit <file to diff current file with>

# update highlighting after making changes in diff mode
:diffupdate

# Put extra text from current window to other window that doesn't have it
dp :diffput

# opposite of diff put (other window to this window)
do
```
