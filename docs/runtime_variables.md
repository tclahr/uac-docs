
# Runtime Variables

UAC supports a set of special runtime variables that are dynamically replaced during execution.

## Command Line Variables

The following variables can be used in conjunction with these command-line options:

- [--output-base-name](index.md#-o-output-base-name)
- [--aws-s3-presigned-url](index.md#-aws-s3-presigned-url)
- [--aws-s3-presigned-url-log-file](index.md#-aws-s3-presigned-url-log-file)
- [--azure-storage-sas-url](index.md#--azure-storage-sas-url)
- [--azure-storage-sas-url-log-file](index.md#-azure-storage-sas-url-log-file)

| Variable        | Description |
|-----------------|-------------|
| `%hostname%`    | The hostname of the target system. |
| `%os%`          | The operating system of the target system. |
| `%timestamp%`   | The Unix timestamp indicating when the data collection started. |

Examples:

Use variables to create custom output file names:

```shell
./uac -a files/browsers/\* --output-base-name "uac-%hostname%-%os%-browsers-only-%timestamp%" /tmp
```

Use variables to upload custom file names during presigned URL transfers:

```shell
./uac -a files/browsers/\* --output-base-name "uac-%hostname%-%os%-browsers-only-%timestamp%" --azure-storage-sas-url 'https://uac-test.blob.core.windows.net/uac-container/uac-%hostname%-%os%-browsers-only-%timestamp%.tar.gz?sp=racwdl&st=2022-09-20T11:20:49Z&se=2022-09-21T19:20:49Z&spr=https&sv=2021-06-08&sr=c&sig=LmNQLedzYBXKSlGGGA0D6x1qSCek1OHELZDiD13BxKk%3D' /tmp
```

## General Variables

These variables can be used within artifact definitions to make your collection scripts more flexible and adaptable.

| Variable               | Description |
|------------------------|-------------|
| `%uac_directory%`      | Full path to the directory where UAC was executed. |
| `%mount_point%`        | Full path to the target mount point. |
| `%temp_directory%`     | Full path to the temporary directory used by UAC to store transient data. Files in this directory are **not** included in the final output archive. |
| `%non_local_mount_points%` | Pipe-separated list of non-local mount points, based on the `exclude_file_system` setting in the `uac.conf` file. |
| `%start_date%`         | Date provided via the `--start-date` command-line option. |
| `%start_date_epoch%`   | The Unix timestamp corresponding to the `--start-date` value. |
| `%end_date%`           | Date provided via the `--end-date` command-line option. |
| `%end_date_epoch%`     | The Unix timestamp corresponding to the `--end-date` value. |

Examples:

Use `%uac_directory%` to reference internal files. This example hashes files listed in a custom file located in the UAC directory:

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

Use `%mount_point%` to access files on the target system. This example lists all files in the `/Applications` directory of the target macOS system:

```yaml
version: 1.0
output_directory: /live_response/packages
artifacts:
  -
    description: Display all applications located in the /Applications directory.
    supported_os: [macos]
    collector: command
    command: ls -l -a /%mount_point%/Applications
    output_file: ls_-l_-a_Applications.txt
```

Use `%temp_directory%` for temporary data. Files stored in the `%temp_directory%` are **excluded** from the final output archive. This is useful for temporary or intermediate data:

```yaml
version: 1.0
artifacts:
  -
    description: Collect executable paths of running processes.
    supported_os: [esxi]
    collector: command
    command: ps -c | awk '{print $4}' | sort -u | grep "^/"
    output_directory: /%temp_directory%/process
    output_file: hash_running_processes_full_paths.txt
  -
    description: Hash running process executables.
    supported_os: [esxi, freebsd, macos, openbsd]
    collector: hash
    path: /%temp_directory%/process/hash_running_processes_full_paths.txt
    is_file_list: true
    output_directory: /live_response/hash_executables
    output_file: hash_running_processes
```

## User-Based Variables

These variables trigger a loop in which UAC runs the command once for **each user** detected on the system.

| Variable       | Description |
|----------------|-------------|
| `%user%`       | Username of the current user in the loop. |
| `%user_home%`  | Full path to the current user's home directory. |

Example:

Use `%user_home%` to collect files for each user. This example collects shell history and related files from each user's home directory:

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
