# Unix Utilities
`loginctl` : query/control the systemd login manager
`loginctl show-session <SESSION_ID> -p Type` : show windowing system

## System Info
`ps -o 'cmd=' -p $(ps -o 'ppid=' -p $$)` : print terminal emulator process name
`echo $TERM` : Terminal 
`echo $COLORTERM`
`ls -l /proc/$$/exe` : current shell
`echo $SHELL`: current shell
`neofetch` display system info summary (need to install neofetch)
`echo $BASH_VERSION`
`uname` print system information
    -a : all
    -r : kernel only
`lsb_release` Print distro info
