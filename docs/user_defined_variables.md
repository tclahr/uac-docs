
# User-defined Variables

User-defined variables make artifact definitions more flexible and reusable. They are set at runtime using the `--define` (or `-D`) command-line option and are expanded dynamically during execution.

Use the syntax `%var` or `%var=default_value%` to declare a variable with an optional default value inside an artifact definition.

User-defined variables can be used within the following fields:

- `command`
- `condition`
- `foreach`
- `output_file`
- `path`

Example:

The example below limits how many iterations (frames) the top command runs before exiting:

```yaml
version: 1.0
artifacts:
  -
    description: Display current running processes.
    supported_os: [linux]
    collector: command
    command: top -b -n%top_number_of_iterations=1%
    output_file: top_-b_-n%top_number_of_iterations=1%.txt
```

Run UAC and override the default value:

```shell
./uac -p full -D top_number_of_iterations=5 DESTINATION
```

## Listing Available User-defined Variables

To list all available user-defined variables:

```shell
./uac --define list
```
