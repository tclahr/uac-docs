# Exposed functions

Some internal functions are exposed and can be used during artifact collection.

## astrings

Print the sequences of printable characters in files. This is a shell implementation of the strings command.

Synopsis:

```yaml
astrings "file"
```

Example:

```yaml
version: 1.0
artifacts:
  -
    description: Extract strings from running processes.
    supported_os: [aix]
    collector: command
    condition: ! command_exists "strings"
    foreach: for pid in /proc/[0-9]*; do echo ${pid} | sed -e 's:/proc/::'; done
    command: astrings /proc/%line%/object/a.out
    output_directory: /live_response/process/proc/%line%
    output_file: strings.txt
    compress_output_file: true
```

## command_exists

Return true if the *command* exists in the target system. The *command* must be enclosed in quotes.

Synopsis:

```yaml
command_exists "command"
```

Example:

```yaml
version: 1.0
condition: command_exists "lsblk"
output_directory: /live_response/storage
artifacts:
  -
    description: List block devices.
    supported_os: [linux]
    collector: command
    command: lsblk -l
    output_file: lsblk_-l.txt
```
