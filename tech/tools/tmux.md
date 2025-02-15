# tmux

## Quick Reference
```
##########################
#### From local terminal
##########################

# new session
tmux new -s <session name>

# rename session
# if -t <session name> is not specified, last session is renamed
tmux rename-session [-t <session name>] <new session name>

# kill all sessions
tmux kill-server

# kill session
tmux kill-session -t <session name>

# attach session
tmux attach [ -t <session name> ]

# show global options
tmux show-options -s

# reload config
tmux source-file ~/.tmux.conf

# list sessions
tmux ls


##########################
#### From tmux 
##########################

# enter copy mode
Prefix + [

# enter tmux command
Prefix + : <command>

# sessions switcher
Prefix + s

# detach session
Prefix + d

# create window
Prefix + c

# kill current window
Prefix + &

# rename current window
Prefix + ,

# rename current session
Prefix + $

# select window
Prefix + <window id>

# split pane vertically
Prefix + %

# split pane horizontally
Prefix + "

# close current pane
Prefix + x

# toggle between panes
Prefix + <arrow keys>

# enable mouse
set-window-option -g mode-mouse on

# scroll mode
Prefix + <arrow keys to scroll, q to quit>

# toggle pane full screen (zoom)
Prefix + z

# display pane numbers
Prefix + q

# window switcher
Prefix + w

# list key bindings
Prefix + ?

# info on single key binding
Prefix + /

# resize pane
Prefix + ctrl + <arrow keys>

# redraw
Prefix + r

# binding keys
bind-key or bind command
bind -n: don't need prefix
```


## Copy/Paste
1. Prefix + [ to enter copy mode 
2. Spacebar/visual mode to start selecting
3. Enter to copy and exit copy mode
4. prefix + ] to paste

OR

Hold shift while left clicking and selecting text, ctrl+shift+c, ctrl+shift+v


## Options
* 3 distinct classes of options: server, session, window
    * Each option belongs to only on of these classes
* Session and window options each use a two-level hierarchy
    * Global session/window options provide default values for the session/window options that are unset
    * Session and window options are all initially unset, so new sessions/windows will automatically use the global option values
    * When a session/window option is set (set-option), the newly set value will override the global value
    * Pane options are inherited from window options
    * To go back to the global value, unset the option (set-option -u)
* To see what options are set:
    set-option / show-options [-gpsw] <options> 
    * g: global, p: pane, s: server, w: window
    * if p,s, or w is not specified, it implies session options
    * set-option -u <optio> to unset
    * -s/-w not required for standard options (it can be inferred from the option name)
* set is an alias of set-option


## Plugins
* Plugin manager: https://github.com/tmux-plugins/tpm
* Plugin list: https://github.com/tmux-plugins/list
* Install plugins: Prefix + I
* Tmux resurrect: save the tmux environment using Prefix + ctrl + s
* Use tmux continuum to continually save the sessions


## vi mode
https://blog.sanctum.geek.nz/vi-mode-in-tmux/

## tips and tricks
* To ensure bashrc gets sourced for new tmux windows, add the following to ~/.bash_profile: . ~/bashrc

## vim-tmux Navigataion

1. Install the vim-tmux-navigator Vim plugin from Chris Toomey.
    Provides the following mappings (no tmux prefix required):
    <ctrl-h> => Left
    <ctrl-j> => Down
    <ctrl-k> => Up
    <ctrl-l> => Right
    <ctrl-\> => Previous split

 

2. Add following to .tmux.conf:
```
# Smart pane switching with awareness of Vim splits.
# See: https://github.com/christoomey/vim-tmux-navigator
is_vim="ps -o state= -o comm= -t '#{pane_tty}' \
    | grep -iqE '^[^TXZ ]+ +(\\S+\\/)?g?(view|n?vim?x?)(diff)?$'"
bind-key -n 'C-h' if-shell "$is_vim" 'send-keys C-h'  'select-pane -L'
bind-key -n 'C-j' if-shell "$is_vim" 'send-keys C-j'  'select-pane -D'
bind-key -n 'C-k' if-shell "$is_vim" 'send-keys C-k'  'select-pane -U'
bind-key -n 'C-l' if-shell "$is_vim" 'send-keys C-l'  'select-pane -R'
tmux_version='$(tmux -V | sed -En "s/^tmux ([0-9]+(.[0-9]+)?).*/\1/p")'
if-shell -b '[ "$(echo "$tmux_version < 3.0" | bc)" = 1 ]' \
    "bind-key -n 'C-\\' if-shell \"$is_vim\" 'send-keys C-\\'  'select-pane -l'"
if-shell -b '[ "$(echo "$tmux_version >= 3.0" | bc)" = 1 ]' \
    "bind-key -n 'C-\\' if-shell \"$is_vim\" 'send-keys C-\\\\'  'select-pane -l'"

bind-key -T copy-mode-vi 'C-h' select-pane -L
bind-key -T copy-mode-vi 'C-j' select-pane -D
bind-key -T copy-mode-vi 'C-k' select-pane -U
bind-key -T copy-mode-vi 'C-l' select-pane -R
bind-key -T copy-mode-vi 'C-\' select-pane -l

```
 
3. For more info: https://www.bugsnag.com/blog/tmux-and-vim


## References
* https://github.com/tmux/tmux/wiki
* https://github.com/tmux/tmux/wiki/Getting-Started
* https://github.com/rothgar/awesome-tmux
