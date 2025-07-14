# Exposed functions

Some internal functions are exposed and can be used during artifact collection.

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
