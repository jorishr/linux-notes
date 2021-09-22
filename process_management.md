# Process management

## Process status
Each software program runs as a process. Each process has a unique process number identifier. The process status can be seen by using the `ps` command. By itself this merely shows the process that are running in the current shell session (usually only bash). But most processes on a server run outside of the current shell session and across the rest of the system.
```
ps -e   
//-> show all processes running across the entire system with process id (PID)

ps -ef  
//-> shows under which user the process is running and the parent process id (PPID), that is the parent process responsible for initiating a process.

ps -ejHf
//-> tree view

ps -e | grep bash
//-> get the process id of a process (here: bash)
```

## Signals
Processes can respond to standard linux signals. These signals are codified with numbers. Use the command `kill -l´ to list these signals and there corresponding numbers. The most used is SIGKILL (9).
```
kill -9 <process id>
```

## Resource management
Monitor the finite resources: cpu, memory and storage usage using `top` or `htop`. The dashboard view of `htop` is more advanced. Use `?` to bring up the help instructions. Use the `,` to sort the processes by various metrics.

The check simple memory overview use `free -h` for human readable format. 

Use `df -h` for storage monitoring and `df -h /` to see just the root file system storage. To find large files: `sudo find / -size +500M`


## services
Services are more advanced ways for dealing with processes. They add extra functionality so that you don't have to deal with each process individually. Webservers, database servers or ssh servers can deal with requests that are handled by various processes. Use `systemctl` to list all services and devices that are being tracked.
```
systemctl status <name.service>

# Example
systemctl status rsyslog.service
systemctl start rsyslog.service
systemctl stop rsyslog.service
systemctl enable rsyslog.service    //-> on boot
systemctl disable rsyslog.service
```
