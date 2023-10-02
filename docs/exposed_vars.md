# Exposed variables

Some variables can be used and will be replaced by UAC at runtime:

|Variable|Replacement|
|---|---|
|%uac_directory%|Full path to where uac was run from|
|%destination_directory%|Full path to the destination directory along with the path to the artifact appended to it|
|%mount_point%|Full path of the target mount point|
|%start_date%|Date specified by --date-range-start|
|%start_date_epoch%|Epoch converted date specified by --date-range-start|
|%end_date%|Date specified by --date-range-end|
|%end_date_epoch%|Epoch converted date specified by --date-range-end|

The following two variables will trigger a loop in which UAC will run the command across all users.

|Variable|Replacement|
|---|---|
|%user%|Username of each user|
|%user_home%|User's home directory path|

Examples:

```yaml
artifacts:
  -
    description: Hash all files based on a file list located in the UAC directory.
    supported_os: [all]
    collector: hash
    path: /%uac_directory%/my_file_list.txt
    is_file_list: true
    output_file: my_hash_list.txt
```

```yaml
artifacts:
  -
    description: Capture a RAM dump using AVML tool and store the acquired data into avml.raw file.
    supported_os: [all]
    collector: command
    command: avml %output_file%
    output_file: avml.raw
```

```yaml
artifacts:
  -
    description: Collect shell history files.
    supported_os: [all]
    collector: file
    path: /%user_home%/
    name_pattern: [".*_history", ".*history", ".lesshst", ".zhistory"]
```