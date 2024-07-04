# Multi-line command

Commands can become long and difficult to read. To execute a multi-line command, you need to enter it within a triple-quote string.

Multi-line commands are supported in the ```command```, ```foreach``` and ```condition``` fields.

The example below...

```yaml
version: 1.0
artifacts:
  -
    description: Collect the command name associated with a process.
    supported_os: [linux]
    collector: command
    foreach: for pid in /proc/[0-9]*; do echo ${pid} | sed -e 's:/proc/::'; done
    command: cat /proc/%line%/comm
    output_directory: /live_response/process/proc/%line%
    output_file: comm.txt
```

...can also be defined as:

```yaml
version: 1.0
artifacts:
  -
    description: Collect the command name associated with a process.
    supported_os: [linux]
    collector: command
    foreach: """
      for pid in /proc/[0-9]*; do 
        echo ${pid} | sed -e 's:/proc/::';
      done
      """
    command: cat /proc/%line%/comm
    output_directory: /live_response/process/proc/%line%
    output_file: comm.txt
```