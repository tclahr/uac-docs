# Output formats

UAC currently supports collecting and storing data in three formats: `none`, `tar`, and `zip`. The default output format is `tar`.

You can specify the desired format using the `--output-format` command-line option.

## none

Collected data will not be archived or compressed. Instead, it will be copied directly into the specified output directory.

This format is useful when no archiving tools are available on the target system.

```shell
./uac -p ir_triage /tmp --output-format none
```

## tar

Collected data will be archived and compressed into a `.tar` file. This is the default format used by UAC. Compression will be enabled if `gzip` is available in the target system.

```shell
./uac -p ir_triage /tmp
```

## zip

Collected data will be archived and compressed into a `.zip` file.

Optionally, UAC can encrypt the ZIP archive using a password provided via the `--output-password` option.

```shell
./uac -p ir_triage /tmp --output-format zip
```
