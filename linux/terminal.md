

## Various

`tail -f /proc/<pid>/fd/1` - display stdout(1) or stderr(2) from <pid> process.
`readlink <link>` - shows file of target link

## Networking

`dig example.com` - return ip in answer
`dig NS example.com` - return nameservers of website
`dig example.com +trace` - trance without using cache
`sudo lsof -i:PORT - list all services using PORT`

## Dolar sign

`$1, $2, $3,` - are the positional parameters,
`"$@"` - is an array-like construct of all positional parameters 
`"$*"` - is the IFS expansion of all positional parameters
`$#` - is the number of positional parameters
`$-` - current options set for the shell.
`$$` - pid of the current shell (not subshell).
`$_` - most recent parameter (or the abs path of the command to start the current shell immediately after startup).
`$IFS` - is the (input) field separator.
`$?` - is the most recent foreground pipeline exit status.
`$!` - is the PID of the most recent background command.
`$0` - is the name of the shell or shell script.

## Shortcuts

`ctrl+a` - move to the start of the line.
`ctrl+e` - move to the end of the line.
`ctrl+u` - delete from the cursor to the start of the line.
`ctrl+k` - delete from the cursor to the end of the line.
`ctrl+w` - delete the word before the cursor.
`ctrl+l` - clear the terminal screen.
`ctrl+c` - stop the current process/command.
`ctrl+d` - log out or exit the terminal.
`ctrl+z` - pause the current process (can be resumed).
`ctrl+r` - search command history (backward search).
`ctrl+r` - (again): navigate through the matching commands.
`ctrl+o` - send the command back to your terminal or select enter to execute the command from the search mode.
`ctrl+g` - leave the history search mode without running a command.
`!!` repeat the last command.
`!n` repeat the nth command from history.
`ctrl+shift+c` - copy the selected text or command.
`ctrl+shift+v` - paste copied text or command.
`ctrl+shift+n` - open a new terminal window.
`ctrl+shift+t` - open a new tab in the terminal.
`ctrl+taborctrl+pagedown` - switch between terminal tabs.
