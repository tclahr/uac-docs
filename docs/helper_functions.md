# Helper functions

Some helper functions are available for use during artifact collection. These functions can enhance your collection logic by introducing conditional behavior based on the target system's state.

## command_exists

Checks whether a given command is available on the target system. Returns `true` if the command exists.

- **Parameter:** The command name must be provided as a string (enclosed in quotes).
- **Returns:** Boolean (`true` if the command exists)

Example:

The following artifact will only collect block device information if the `lsblk` command is available:

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
