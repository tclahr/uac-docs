# Output formats

Currently, UAC can collect and store data in 4 different formats: none, tar and zip. The default output format is ```tar```. You can define the output using the [--output-format](index.md#output-arguments) command line option.

## none

Collected data will not be archived or compressed. Instead, it will be copied directly to an output directory. This is useful in cases where the system has no archiving tool available.

```shell
./uac -p ir_triage /tmp --output-format none
```

## tar

Collected data will be archived (and compressed) into a tar file. This is the default output format.

```shell
./uac -p ir_triage /tmp
```

## zip

Collected data will be archived and compressed into a zip file. UAC can also encrypt the contents of the zip archive using a password defined by the [--output-password](index.md#output-arguments) command line option.

```shell
./uac -p ir_triage /tmp --output-format zip
```
