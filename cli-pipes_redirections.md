# Linux CLI: Pipes and Redirections
1.  File descriptors
2.  Pipes
3.  Redirections

## 1.   File descriptors
When a program wants to read or write a file it needs to request access to that file from the kernel. The kernel assigns three files descriptors to the program by default:
* 0 stdin
* 1 stdout
* 2 stderr

The stdin takes in data and supplies it as input to a program. It functions as an interface so that the actual program does not need to account for where the input is generated from (keyboard, data from another program, other).

Stdout and stderr both produce output (program data or error message) into the terminal by default but can redirected into seperately into files.

## 2.   Pipes
Run program1 and send stdout to stdin of program2 with anonymous pipe:
`program1 | program2`

## 3.   Redirections
Pipes operate on the stdout of a program while redirections can be done with both input and output.

* ### Output Redirection
Run program1 and redirect stdout to log file, stderr goes to the terminal by default:
`program1 1> output.log`
`program1 > output.log`
Both commands are identical but in the first one the file descriptor is made explicit.

Run program1 and redirect stdout to output log file, stderr goes to error log file:
`program1 1> output.log 2> error.log`

Run program1 and redirect both stdout and stderr to the same output log file:
`program1 > output.log 2>&1`

To append the output data to a file instead of overwriting exisiting data use '>>':
`program1 >> output.log`

* ### Input redirection
Run program1 with input.txt into stdin, stdout and stderr to terminal by default:
`program1 < input.txt`
`cat input.txt | program1`
Both programs produces the same result but the cat command and pipe adds an additional step.

Run program1 with input.txt into stdin, stdout and stderr to seperate log files:
`program1 < input.txt > output.log 2> error.log`

* ### Named pipes
Create a named pipe, redirect stdout of program1 to that pipe and redirect the pipe to stdin from program2:
```
mkfifo mypipe
program1 > mypipe
program2 < mypipe 
```
The pipe can be found in the current directory.

* ### No output
To discard all program output stdout and stderr can be redirected to /dev/null, a kernel sewer device that makes the data disappear.
`program1 > /dev/null 2>&1`