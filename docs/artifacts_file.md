# Artifacts file definition

Artifacts are used to define parameters that will be used by a [collector](collectors.md) to collect data. They are YAML files located in the ```artifacts``` directory.

Artifacts files should always be created for artifacts that can be grouped together. For instance, artifacts related to ```ps``` command should be put all in the same file. This makes artifacts collection to be more granular depending on the case you are working on.

UAC reads artifacts files on the fly and, based on their contents, collects relevant artifacts using one of the 5 available [collectors](collectors.md).

The example below contains 2 set of rules, which the first one uses the [hash](collectors.md#hash-collector) collector, and the second one uses the [command](collectors.md#command-collector) collector to collect the artifacts.

```yaml
version: 1.0
artifacts:
  -
    description: Hash running processes.
    supported_os: [aix]
    collector: hash
    path: /proc/[0-9]*/object/a.out
    output_file: hash_running_processes
  -
    description: Collect running processes executable path.
    supported_os: [freebsd, macos, openbsd]
    collector: command
    command: ps -eo args | grep ^/ | awk '{print $1}' | sort -u
    output_file: hash_running_processes_full_paths.txt
```

An artifact file has one required field at the top of the file [version](#version), followed by a set of rules [artifacts](#artifacts) that will be used by one of the [collectors](collectors.md) to collect the desired data.

## version
<span class="required">Required</span>

The artifact file version. It needs to be updated everytime a new artifact is added or changed.

## artifacts
<span class="required">Required</span>

Set of rules (sequence of mappings) that defines what artifact will be collected. 

An artifact rule has three mandatory fields [description](#description), [supported_os](#supported_os) and [collector](#collector), followed by additional fields depending on the [collector](collectors.md) chosen.

```yaml
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

### description
<span class="required">Required</span>

One line description of what will be collected. No like breaks supported.

### supported_os
<span class="required">Required</span>

List of operating systems that the artifact applies to. Use ```all``` if the artifact applies to all operating systems.

The currently suppored operating systems are (case sensitive):

| Value | Description |
| ----- | ----------- |
| aix | Artifact applies to AIX systems. |
| android | Artifact applies to Android systems. |
| freebsd | Artifact applies to FreeBSD systems. |
| linux | Artifact applies to Linux systems. |
| macos | Artifact applies to macOS systems. |
| netbsd | Artifact applies to NetBSD systems. |
| netscaler | Artifact applies to NetScaler systems. |
| openbsd | Artifact applies to OpenBSD systems. |
| solaris | Artifact applies to Solaris systems. |

Examples:

Artifact applies to all supported operating systems:

```yaml
artifacts:
  -
    description: Artifact description.
    supported_os: [all]
    collector: hash
    ... # additional options depending on the collector
```

Artifact applies to Aix, FreeBSD and Solaris systems only:

```yaml
artifacts:
  -
    description: Artifact description.
    supported_os: [aix, freebsd, solaris]
    collector: stat
    ... # additional options depending on the collector
```

### collector
<span class="required">Required</span>

UAC uses collectors to collect artifacts. Each collector is used for a specific function and requires its own options. Please check the [collectors](collectors.md) page for more details.

| Value | Description |
| ----- | ----------- |
| command | Use this collector to run commands and store the output into an output file. |
| find | Use this collector to find files and directories using either find or find.pl tool, and store the output into a text file. |
| hash | Use this collector to hash files and store the output into a text file. |
| stat | Use this collector to extract information from files and directories using either stat or stat.pl tool to create a body file. |
| file | Use this collector to collect files and directories. |
