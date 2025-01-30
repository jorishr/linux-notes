# Terminal multiplexer

When connecting remotely via ssh the connection can be interrupted. By itself this is not a big issue. But when a shell is terminated all programs that were running in that shell are also interrupted. When doing important updates remotely an interruption can thus leave the system in a broken state.

To mitigate this risk a terminal multiplexer is used via the command `tmux`. This opens up a virtual session on the server machine to which you can connect and disconnect at will. Thus when the connection is lost, the session continues and you can re-attach to the existing session after re-connecting using the command `tmux attach`.

Tmux is also useful to allow you to disconnect when you know a process will take a lot of time to complete.

Screen is a similar application that allows you to do multitasking via the command line.

## Commands

```bash
tmux # start new session
tmux new -s name-your-session
tmux -ls #show existing sessions
tmux attach # re-connect to existing session(s)
tmux attach -t your-named-session
```

### Managing multiple windows or panes

Inside a session press `CTRL + b` (prefix) followed by the actual command. For example, `d` detaches you from the current session. The command `c` creates a new window. And switch to the next window by using `n` or `p` for previous window or use the arrow keys.

To create split screens for multiple windows use `"` for horizontal split and `%` for a vertical split. To change the zoom level to full screen use prefix and `z`.
