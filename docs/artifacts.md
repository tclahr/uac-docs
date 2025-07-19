<!-- markdownlint-disable MD036 -->
# Artifacts definition

Artifacts define the parameters used by UAC collectors to gather data from the target system.

UAC dynamically reads YAML artifact definition files and determines which of the five available collectors to use: [`command`](#command), [`file`](#file), [`find`](#find), [`hash`](#hash), or [`stat`](#stat). Based on the contents of each artifact, UAC collects the appropriate data and saves it to the specified output locations.

Each artifact file must include a `version` field and a list of artifact rules. Every rule defines how data is collected, which platforms it supports, and what collector it uses. Here's an example containing two rules, each using a different collector:

```yaml
version: 1.0
artifacts:
  -
    description: Hash running processes.
    supported_os: [aix]
    collector: hash
    path: /proc/[0-9]*/object/a.out
    output_directory: /live_response/process
    output_file: hash_running_processes
  -
    description: Report a snapshot of the current processes.
    supported_os: [aix, freebsd, linux, macos, netbsd, openbsd, solaris]
    collector: command
    command: ps auxwww
    output_directory: /live_response/process
    output_file: ps_auxwww.txt
```

It's a common practice to group artifacts by related topics within the same YAML file. This structure allows for more precise and efficient artifact collection tailored to your specific needs.

## YAML File Structure Overview

### version

**Required**

Indicates the version of the artifact file. Increment this value whenever you modify the file, such as by updating rules or adding new artifacts.

```yaml
version: 1.0
```

### artifacts

**Required**

A list of rules specifying what to collect. Each rule must contain:

- `description`
- `supported_os`
- `collector`

Other fields depend on the selected collector.

```yaml
version: 1.0
artifacts:
  -
    description: Report a snapshot of the current processes.
    supported_os: [all]
    collector: command
    ... # additional options depending on the collector
  -
    description: Hash files that contain at least +x flag set for owner.
    supported_os: [all]
    collector: hash
    ... # additional options depending on the collector
  -
    description: Collect /var/log logs.
    supported_os: [all]
    collector: file
    ... # additional options depending on the collector
```

## Collectors Overview

Each collector serves a specific function and comes with its own set of required and optional fields. The following sections provide a complete explanation of each collector along with practical examples.

### command

Use the `command` collector to execute system commands and save their output to a specified file. UAC uses `eval` to run the command and captures standard output (stdout) into the defined file. Standard error (stderr) messages are written to `uac.log`, unless redirected explicitly.

**Required fields:**

- `command`
- `output_directory`

**Optional fields:**

- `compress_output_file`
- `condition`
- `exclude_nologin_users`
- `foreach`
- `output_file`
- `redirect_stderr_to_stdout`

**Example:**

```yaml
version: 1.0
artifacts:
  -
    description: Report a snapshot of the current processes.
    supported_os: [all]
    collector: command
    command: ps auxwww
    output_directory: /live_response/process
    output_file: ps_auxwww.txt
```

### find-based collectors

These collectors rely on the `find` tool to locate files and directories. UAC builds the appropriate `find` command dynamically at runtime based on the platform’s supported options. If unsupported options are detected or `find` is not available, UAC can use [find.pl](https://github.com/tclahr/find.pl) as a fallback.

**Example:**

```yaml
version: 1.0
artifacts:
  -
    description: Collect nginx logs.
    supported_os: [aix, freebsd, linux, macos, netbsd, netscaler, openbsd, solaris]
    collector: file
    path: /var/log
    exclude_path_pattern: ["/sys", "/proc"]
    name_pattern: ["*access_log*", "*access.log*", "*error_log*", "*error.log*"]
    max_depth: 5
    file_type: [f]
    max_file_size: 1073741824 # 1GB
```

Resulting command with standard `find`:

```shell
find /var/log -maxdepth 5 \( -path "/sys" -o -path "/proc" \) -prune -o -size -1073741824c  \( -name "*access_log*" -o -name "*access.log*" -o -name "*error_log*" -o -name "*error.log*" \) -print
```

Fallback using Perl implementation:

```shell
find.pl /var/log -maxdepth 5 \( -path "/sys" -o -path "/proc" \) -prune -o -size -1073741824c  \( -name "*access_log*" -o -name "*access.log*" -o -name "*error_log*" -o -name "*error.log*" \) -print
```

#### find

Search for files and directories and save their paths to a text file.

**Required fields:**

- `path`
- `output_directory`
- `output_file`

**Optional fields:** Refer to the [field reference section](#field-reference-and-examples) for a full list (e.g., `exclude_path_pattern`, `name_pattern`, etc.)

#### hash

Generate hash values of matching files and save the results to a text file. The hash algorithm is defined in the UAC configuration file (`uac.conf`).

**Required fields:**

- `path`
- `output_directory`
- `output_file`

**Optional fields:** Refer to the [field reference section](#field-reference-and-examples) for a full list (e.g., `exclude_path_pattern`, `name_pattern`, etc.)

#### stat

Collect file and directory metadata to generate a body file, compatible with The Sleuth Kit (TSK). If the native `stat` tool is unavailable, UAC will use a Perl fallback [stat.pl](https://github.com/tclahr/stat.pl).

**Required fields:**

- `path`
- `output_directory`
- `output_file`

**Optional fields:** Refer to the [field reference section](#field-reference-and-examples) for a full list (e.g., `exclude_path_pattern`, `name_pattern`, etc.)

#### file

Copy raw files and directories to the output archive. Files are stored in the `[root]` directory inside the output file structure.

**Required fields:**

- `path`

**Optional fields:** Refer to the [field reference section](#field-reference-and-examples) for a full list (e.g., `exclude_path_pattern`, `name_pattern`, etc.)

Example output layout:

```text
[root]
└── var
    └── log
        └── access.log
```

## Field Reference and Examples

Each field used in artifact rules is described below, along with relevant examples.

### command

**Required by: command**

Defines the shell command to execute on the target system. The command is run using `eval`, and its stdout is captured in the output file. stderr is logged to `uac.log` unless redirected.

```yaml
version: 1.0
artifacts:
  -
    description: Report a snapshot of the current processes.
    supported_os: [all]
    collector: command
    command: ps -ef
    output_directory: /live_response/process
    output_file: ps_-ef.txt
```

Multi-line commands should be enclosed in triple quotes:

```yaml
command: """
  for pid in /proc/[0-9]*; do 
    echo ${pid} | sed -e 's:/proc/::';
  done
"""
```

Use with timeout:

```yaml
command: timeout.sh 2 ps -ef
```

### compress\_output\_file

**Optional for: command**

If `true`, compresses the output file using `gzip`.

```yaml
version: 1.0
output_directory: /live_response/process
artifacts:
  -
    description: Report a snapshot of the current processes.
    supported_os: [all]
    collector: command
    command: ps -ef
    output_file: ps_-ef.txt
    compress_output_file: true
```

### condition

**Optional for: all collectors**

Shell condition that must return success (exit 0) for the artifact to run.

```yaml
version: 1.0
output_directory: /live_response/process
artifacts:
  -
    description: Collect running processes executable path.
    supported_os: [freebsd]
    condition: ls /proc/$$
    collector: command
    command: ls -l /proc/[0-9]*/file
    output_file: running_processes_full_paths.txt
```

Negation with `!`:

```yaml
condition: ! ls /proc/$$
```

Global condition example. In this case, the collection will only proceed if the global condition returns true.

```yaml
condition: ls /proc/$$
artifacts:
  - description: Hash running processes
    supported_os: [linux]
    ...
```

Multi-line example:

```yaml
condition: """
  if ls /proc/$$ && ps; then
    true
  else
    false
  fi
"""
```

### description

**Required**

Short text describing what the artifact does. Should not contain line breaks.

```yaml
description: List current processes
```

### exclude\_file\_system

**Optional for: file, find, hash, stat**

Exclude file systems by type. UAC retrieves the list of existing mountpoints (paths) at runtime and excludes them from the collection.

```yaml
version: 1.0
output_directory: /live_response/system
artifacts:
  -
    description: Search all files excluding any files located in procfs, nfs and devfs file systems.
    supported_os: [all]
    collector: find
    path: /
    exclude_file_system: [procfs, nfs, devfs]
    output_file: exclude_procfs_nfs_devfs.txt
```

_Note that exclude_file_system will be ignored when path_pattern is used._

### exclude\_name\_pattern

**Optional**

Exclude files by name pattern. No slashes allowed in patterns.

```yaml
version: 1.0
output_directory: /live_response/system
artifacts:
  -
    description: Search /etc excluding passwd and shadow* files.
    supported_os: [all]
    collector: find
    path: /etc
    exclude_name_pattern: ["passwd", "shadow*"]
    output_file: etc_excluding_passwd_shadow.txt
```

### exclude\_nologin\_users

**Optional**

When `true`, artifacts are only collected for users with a valid login shell.

```yaml
version: 1.0
artifacts:
  -
    description: Run 'ls -lRa' on all home directories for users that have a valid shell only.
    supported_os: [all]
    collector: command
    command: ls -lRa /%user_home%/
    exclude_nologin_users: true
    output_directory: /live_response/%user%
    output_file: ls_-lRa_%user%.txt
```

### exclude\_path\_pattern

**Optional**

Exclude specific paths using shell-style patterns.

```yaml
version: 1.0
output_directory: /live_response/system
artifacts:
  -
    description: Search all excluding /etc and /var.
    supported_os: [all]
    collector: find
    path: /
    exclude_path_pattern: ["/dev", "/var"]
    output_file: all_excluding_etc_var.txt
```

### file\_type

**Required when [max\_file\_size](#max_file_size) or [min\_file\_size](#min_file_size) is specified**

Match files by type.

| Value | Description        |
| ----- | ------------------ |
| `f`   | Regular file       |
| `d`   | Directory          |
| `l`   | Symbolic link      |
| `p`   | Named pipe (FIFO)  |
| `s`   | Socket             |
| `b`   | Block special file |
| `c`   | Character special  |

```yaml
version: 1.0
output_directory: /live_response/system
artifacts:
  -
    description: Search files only.
    supported_os: [all]
    collector: find
    path: /
    file_type: [f]
    output_file: files_only.txt
  -
    description: Search directories only.
    supported_os: [all]
    collector: find
    path: /
    file_type: [f, d, l]
    output_file: directories_only.txt
```

### foreach

**Optional for: command**

Run a command for each result returned by another command. Use `%line%` to substitute the line output.

The logic behind it is:

```sh
for (each line returned by foreach); do
  command
done
```

```yaml
version: 1.0
artifacts:
  -
    description: Fetch the logs of all containers.
    supported_os: [linux]
    collector: command
    foreach: docker container ps -all | sed 1d | awk '{print $1}'
    command: docker container logs %line%
    output_directory: /live_response/containers/%line%
    output_file: docker_container_logs_%line%.txt
```

Multi-line:

```yaml
foreach: """
  for pid in /proc/[0-9]*; do
    echo ${pid} | sed -e 's:/proc/::'
  done
"""
```

### ignore\_date\_range

**Optional**

Ignore `--start-date` and `--end-date` arguments if `true`.

```yaml
version: 1.0
output_directory: /live_response/system
artifacts:
  -
    description: Search /etc regardless date range set by --start-date and --end-date.
    supported_os: [all]
    collector: find
    path: /etc
    ignore_date_range: true
    output_file: ignore_date_range.txt
```

### is\_file\_list

**Optional for: file, hash, stat**

Interpret `path` as a file that contains a list of file paths.

```yaml
artifacts:
  -
    description: Hash files based on a file list located in /%uac_directory%/my_file_list.txt.
    supported_os: [all]
    collector: hash
    path: /%uac_directory%/my_file_list.txt
    is_file_list: true
    output_file: hash_my_file_list.txt
```

### max\_depth

**Optional**

Limit recursion depth in directories.

```yaml
version: 1.0
output_directory: /live_response/system
artifacts:
  -
    description: Descend at most 5 levels of directories below /.
    supported_os: [all]
    collector: find
    path: /
    max_depth: 5
    output_file: max_5_levels.txt
```

### max\_file\_size

**Optional**

Only include files within the specified size limits (in bytes).

```yaml
version: 1.0
output_directory: /live_response/system
artifacts:
  -
    description: Search all files smaller than 1048576 bytes.
    supported_os: [all]
    collector: find
    path: /
    max_file_size: 1048576
    output_file: smaller_than.txt
```

### min\_file\_size

**Optional**

Only include files within the specified size limits (in bytes).

```yaml
version: 1.0
output_directory: /live_response/system
artifacts:
  -
    description: Search all files bigger than 1048576 bytes.
    supported_os: [all]
    collector: find
    path: /
    min_file_size: 1048576
    output_file: bigger_than.txt
```

### modifier

**Optional (global)**

Mark artifact as a modifier (may alter system state). Must be enabled via `--enable-modifiers`.

```yaml
version: 1.0
modifier: true
output_directory: /live_response/modifiers
artifacts:
  -
    description: List all PIDs with a directory in /proc but hidden for ps command.
    supported_os: [linux]
    collector: command
    foreach: for pid in /proc/[0-9]*; do echo ${pid} | sed -e 's:/proc/::'; done
    command: if ps ax | awk '{print $1}' | grep -q %line%; then true; else echo %line%; fi
    output_file: hidden_pids_for_ps_command.txt
  -
    description: Umount all bind mounted directories to /proc/PID.
    supported_os: [linux]
    collector: command
    foreach: mount | awk 'BEGIN { FS=" on "; } { print $2; }' | grep "/proc/[0-9]" | awk '{print $1}'
    command: umount "%line%"
    output_file: umount_%line%.txt
```

### name\_pattern

**Optional**

Match files by name. Wildcards and regex supported.

```yaml
version: 1.0
output_directory: /live_response/system
artifacts:
  -
    description: Search all wtmp and utmp files.
    supported_os: [all]
    collector: find
    path: /var
    name_pattern: ["wtmp", "btmp"]
    output_file: wtmp_btmp.txt
```

### no\_group

**Optional**

Match files with unknown GID.

```yaml
version: 1.0
output_directory: /live_response/system
artifacts:
  -
    description: List files under /bin directory with an unknown group ID name.
    supported_os: [aix, freebsd, linux, macos, netbsd, netscaler, openbsd, solaris]
    collector: find
    path: /bin
    file_type: [f]
    no_group: true
    output_file: group_name_unknown_files.txt
```

### no\_user

**Optional**

Match files with unknown UID.

```yaml
version: 1.0
output_directory: /live_response/system
artifacts:
  -
    description: List files under /bin directory with an unknown user ID name.
    supported_os: [aix, freebsd, linux, macos, netbsd, netscaler, openbsd, solaris]
    collector: find
    path: /bin
    file_type: [f]
    no_user: true
    output_file: user_name_unknown_files.txt
```

### output\_directory

**Required for: command, find, hash, stat**

Specifies the internal output path in the output file.

```yaml
version: 1.0
artifacts:
  -
    description: My first artifact.
    supported_os: [all]
    collector: command
    command: ls /tmp
    output_directory: /my_custom_artifacts
    output_file: ls_tmp.txt
```

Output file contents:

![screenshot](img/artifacts_md_output_directory.png)

Can be defined globally:

```yaml
version: 1.0
output_directory: /my_custom_artifacts
artifacts:
  -
    description: Report a snapshot of the current processes.
    supported_os: [all]
    collector: command
    command: ps
    output_directory: /live_response/process
    output_file: ps.txt
  -
    description: Report a snapshot of the current processes.
    supported_os: [all]
    collector: command
    command: ps auxwww
    output_file: ps_auxwww.txt
```

### output\_file

**Optional**

Defines the file name inside `output_directory`. UAC appends to this file.

```yaml
version: 1.0
artifacts:
  -
    description: Report a snapshot of the current processes.
    supported_os: [all]
    collector: command
    command: ps
    output_directory: /live_response/process
    output_file: ps.txt
```

If no `output_file` is specified, the output of the `command` will be stored in the `output_directory`.

In the example below, `avml.raw` will be placed in the `memory_dump` directory.

```yaml
version: 1.0
output_directory: /memory_dump
artifacts:
  -
    description: Capture a memory image.
    supported_os: [linux]
    collector: command
    command: avml avml.raw
```

### path

**Required for: find, file, hash, stat**

Specifies the starting point for searching or collecting files. The mount point set by `--mount-point` is automatically prepended to the path.

```yaml
version: 1.0
output_directory: /live_response/system
artifacts:
  -
    description: Search all cmdline files within /proc/*/
    supported_os: [all]
    collector: find
    path: /proc/*/cmdline
    output_file: list_of_cmdline_files.txt
```

Multiple paths example:

```yaml
version: 1.0
output_directory: /system
artifacts:
  -
    description: List files under user's home directory (no recursion, top-level only) with an unknown user ID name.
    supported_os: [aix, freebsd, linux, netbsd, netscaler, openbsd]
    collector: find
    path: /home /export/home /Users /%user_home%
    max_depth: 1
    file_type: [f]
    no_user: true
    output_file: user_name_unknown_files.txt
```

Use quotation marks when specifying paths with spaces or special characters.

```yaml
version: 1.0
output_directory: /live_response/system
artifacts:
  -
    description: Search for TCC.db file.
    supported_os: [all]
    collector: find
    path: /Library/"Application Support"/com.apple.TCC/TCC.db
    output_file: path_with_spaces.txt
```

### path\_pattern

**Optional**

Match full paths with patterns.

```yaml
version: 1.0
output_directory: /live_response/system
artifacts:
  -
    description: Find Discord cache files.
    supported_os: [all]
    collector: find
    path: /%user_home%
    path_pattern: ["*/discord/Cache/*"]
    output_file: discord_cache.txt
```

### permissions

**Optional**

Filter files by permission bits. Symbolic mode is not supported (e.g: -g=w).

```yaml
version: 1.0
output_directory: /live_response/system
artifacts:
  -
    description: Search files that have permissions set to 755 or 644.
    supported_os: [all]
    collector: find
    path: /
    file_type: [f]
    permissions: [755, -4000]
    output_file: all_755_and_644_permissions.txt
```

### redirect\_stderr\_to\_stdout

**Optional for: command**

Redirect `stderr` to the output file.

```yaml
version: 1.0
output_directory: /live_response/process
artifacts:
  -
    description: Collect the list open files.
    supported_os: [all]
    collector: command
    command: lsof -nPl
    output_file: lsof_-nPl.txt
    redirect_stderr_to_stdout: true
```

### supported\_os

**Required**

Specifies supported operating systems.

| Value       | Description            |
| ----------- | ---------------------- |
| `all`       | Applies to all systems |
| `aix`       | IBM AIX                |
| `esxi`      | VMware ESXi            |
| `freebsd`   | FreeBSD                |
| `linux`     | Linux                  |
| `macos`     | macOS                  |
| `netbsd`    | NetBSD                 |
| `netscaler` | NetScaler              |
| `openbsd`   | OpenBSD                |
| `solaris`   | Solaris                |

```yaml
version: 1.0
artifacts:
  -
    description: Artifact description.
    supported_os: [all]
    collector: hash
    ... # additional options depending on the collector
  -
    description: Artifact description.
    supported_os: [aix, freebsd, solaris]
    collector: stat
    ... # additional options depending on the collector
```

## Additional Recommendations

- Enclose paths with spaces or special characters in quotes.
- Use triple-quoted strings (`"""`) for multi-line commands or conditions.
- Apply runtime variables like `%user%`, `%line%`, and `%uac_directory%` where applicable.
- Maintain consistent indentation and formatting in your YAML.
