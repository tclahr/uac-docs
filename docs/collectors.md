# Collectors

## command collector

This collector is used to run commands and store the output into an output file.

```yaml
artifacts:
  -
    description: Report a snapshot of the current processes.
    supported_os: [all]
    collector: command
    command: ps
    output_file: ps.txt
  -
    description: Report a snapshot of the current processes.
    supported_os: [aix, freebsd, linux, macos, netbsd, openbsd, solaris]
    collector: command
    command: ps auxwww
    output_file: ps_auxwww.txt
  -
    description: Extract strings from running processes.
    supported_os: [aix]
    collector: command
    loop_command: for pid in /proc/[0-9]*; do echo ${pid} | sed -e 's:/proc/::'; done
    command: strings -a /proc/%line%/object/a.out
    output_directory: proc/%line%
    output_file: strings.txt
    compress_output_file: true
```

### command
<span class="required">Required</span>

The command that will be run in the target system and the output will be collected from.

The path to the executable is not required. Only the name of the executable and the parameters should be given.

UAC can also run executables located in the ```bin``` directory. Even in this case the path to the executable is not required. The ```bin``` directory is always added to PATH when UAC starts. Please refer to ```bin/README.md``` file for more information.

The example below shows how to collect the output from ```ps -ef``` command:

```yaml
artifacts:
  -
    description: Report a snapshot of the current processes.
    supported_os: [all]
    collector: command
    command: ps -ef
    output_file: ps_-ef.txt
```

### loop_command
<span class="optional">Optional</span>

The command that will be run and the output lines will be used as the input for the [command](#command) option.

The logic behind it is:

```
for (each line returned by loop_command); do
  command
done
```

There is one variable that can be used that will be replaced by UAC at runtime:

|VARIABLE NAME|DESCRIPTION|
|---|---|
|%line%|Replaced with each line returned by the execution of the loop_command command|

Let's suppose you need to collect containers logs, and you don't know the containers IDs. First you need to retrieve all the IDs: ```docker container ps -all | sed 1d | awk '{print $1}'```

The ```%line%``` variable will be replaced by each output line resulted from that command (which are containers IDs in the example above).

This means if you have 10 containers, the command ```docker container logs %line%``` will be run 10 times, one for each container ID. Example:

```yaml
artifacts:
  -
    description: Fetch the logs of all containers.
    supported_os: [linux]
    collector: command
    loop_command: docker container ps -all | sed 1d | awk '{print $1}'
    command: docker container logs %line%
    output_file: docker_container_logs_%line%.txt
```

### output_file
<span class="required">Required</span>

Output file name where collected data will be stored in. UAC never overwrites output files. Data will be appended to file if the same file name is set for a different artifact rule within the same artifact directory.

Both ```ps``` and ```ps auxwww``` outputs will be stored into the same ```ps.txt``` filein the example below.

```yaml
artifacts:
  -
    description: Report a snapshot of the current processes.
    supported_os: [all]
    collector: command
    command: ps
    output_file: ps.txt
  -
    description: Report a snapshot of the current processes.
    supported_os: [all]
    collector: command
    command: ps auxwww
    output_file: ps.txt
```

### output_directory
<span class="optional">Optional</span>

Defines the subdirectory name the output file will be stored in.

By default, collected artifacts will always be stored into a directory which its path follows the same structure defined in the artifacts directory.

The following artifact output file will be stored into ```live_response/process/my_custom_dir``` directory in the example below. *Snippet retrieved from ```live_response/process/ps.yaml```*

```yaml
artifacts:
  -
    description: Report a snapshot of the current processes.
    supported_os: [all]
    collector: command
    command: ps -ef
    output_directory: my_custom_dir
    output_file: ps_-ef.txt
```

### compressed_output_file
<span class="optional">Optional</span>

The output file will be compressed by gzip if this option is set to ```true```.

The following artifact will have its output stored into the compressed output file ```ps_-ef.txt.gz``` in the example below.

```yaml
artifacts:
  -
    description: Report a snapshot of the current processes.
    supported_os: [all]
    collector: command
    command: ps -ef
    output_file: ps_-ef.txt
    compress_output_file: true
```

## find collector

This collector is used to find files and directories.

```yaml
artifacts:
  -
    description: Find files that contain at least +x flag set for other.
    supported_os: [all]
    collector: find
    path: /
    exclude_file_system: [proc, procfs]
    file_type: f
    max_depth: 4
    max_file_size: 3072000
    permissions: -001
    output_file: list_of_executable_files.txt

```

## hash collector

hash collector

## stat collector

stat collector

## file collector

file collector