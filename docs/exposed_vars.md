# Exposed variables

Some variables can be used and will be replaced by UAC at runtime:

|Variable|Replacement|
|---|---|
|%uac_directory%|Full path to where uac was run from.|
|%mount_point%|Full path of the target mount point.|
|%temp_directory%|Full path to the temp directory used by UAC to store temp data. Data in this directory will be discarded and not be part of the output file.|
|%start_date%|Date specified by --start-date command line option.|
|%start_date_epoch%|Epoch converted date specified by --start-date.|
|%end_date%|Date specified by --end-date command line option.|
|%end_date_epoch%|Epoch converted date specified by --end-date.|

The following two variables will initiate a loop, causing UAC will run the command for all users.

|Variable|Replacement|
|---|---|
|%user%|Username of each user.|
|%user_home%|User's home directory path.|

Examples:

%uac_directory% is useful when you need to add custom files to use during the collection.

```yaml
version: 1.0
artifacts:
  -
    description: Hash all files based on a file list located in the UAC directory.
    supported_os: [all]
    collector: hash
    path: /%uac_directory%/my_file_list.txt
    is_file_list: true
    output_directory: /live_response/system
    output_file: my_hash_list.txt
```

```yaml
version: 1.0
output_directory: /live_response/packages
artifacts:
  -
    description: Display all applications located at /Applications directory.
    supported_os: [macos]
    collector: command
    command: ls -l -a /%mount_point%/Applications
    output_file: ls_-l_-a_Applications.txt
```

In the example below, the file ```hash_running_processes_full_paths.txt``` will not be part of the output file as it was stored in the ```%temp_directory%```.

```yaml
version: 1.0
artifacts:
  -
    description: Collect running processes executable path.
    supported_os: [esxi]
    collector: command
    command: ps -c | awk '{print $4}' | sort -u | grep "^/"
    output_directory: /%temp_directory%/process
    output_file: hash_running_processes_full_paths.txt
  -
    description: Hash running processes.
    supported_os: [esxi, freebsd, macos, openbsd]
    collector: hash
    path: /%temp_directory%/process/hash_running_processes_full_paths.txt
    is_file_list: true
    output_directory: /live_response/hash_executables
    output_file: hash_running_processes
```

In the example below, UAC will collect the file patterns specified in name_pattern from any user's home folder.

```yaml
version: 1.0
artifacts:
  -
    description: Collect shell history files.
    supported_os: [all]
    collector: file
    path: /%user_home%/
    name_pattern: [".*_history", ".*history", ".lesshst", ".zhistory"]
```
