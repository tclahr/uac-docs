# Exposed variables

There are some variables that can be used that will be replaced by UAC at runtime:

|Variable Name|Description|
|---|---|
|%uac_directory%|Replaced with the full path to where uac was run from|
|%output_file%|Replaced with the file name set in the output_file option|
|%mount_point%|Replaced with the full path of the target mount point|
|%start_date%|Replaced with the date specified for --date-range-start|
|%start_date_epoch%|Replaced with the epoch converted date specified for --date-range-start|
|%end_date%|Replaced with the date specified for --date-range-end|
|%end_date_epoch%|Replaced with the epoch converted date specified for --date-range-end|

The following two variables will trigger a loop which UAC will run the command across all users.

|Variable Name|Description|
|---|---|
|%user%|Replaced with the username of each user|
|%user_home%|Replaced with the user's home directory path|

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