# Log files

## uac.log

The `uac.log` file contains timestamped messages that include a log level and a corresponding message. This is the primary log used to track the actions performed by UAC during its execution. The level of detail in this log is affected by the `--debug` option. When data collection is complete, this log is also included in the final output archive.

| Level | Description |
|-------|-------------|
| DBG   | Debug messages |
| INF   | Informational messages |
| ERR   | Error messages |
| CMD   | Executed commands and their stderr output |

At the beginning of the file, UAC logs important information such as the command-line options used, operating system, system architecture, mount point, loaded configuration (from `uac.conf`), available tools, and other environment details. These entries are logged at the `INF` level.

Example:

```text
2024-05-23 18:07:12 -0300 INF Unix-like Artifacts Collector 
2024-05-23 18:07:12 -0300 INF UAC directory: /tmp/uac
2024-05-23 18:07:12 -0300 INF Command line: ./uac -p ir_triage -u -v /tmp
2024-05-23 18:07:12 -0300 INF Operating system: linux
2024-05-23 18:07:12 -0300 INF System architecture: x86_64
2024-05-23 18:07:12 -0300 INF Hostname: uac-suse-tumbleweed
2024-05-23 18:07:12 -0300 INF Mount point: /
2024-05-23 18:07:12 -0300 INF Running as: uac
2024-05-23 18:07:12 -0300 INF Temp Directory: /tmp/uac/uac-data.tmp
2024-05-23 18:07:12 -0300 INF Output format: tar
2024-05-23 18:07:12 -0300 INF Current PID: 6377
2024-05-23 18:07:12 -0300 INF PATH: /tmp/uac/tools/date_to_epoch_pl:/tmp/uac/tools/find_pl:/tmp/uac/tools/stat_pl:/tmp/uac/tools/statx/linux/x86_64:/tmp/uac/tools/zip/esxi_linux/x86_64:/tmp/uac/bin/linux/x86_64:/tmp/uac/bin/linux:/tmp/uac/bin:/usr/xpg4/bin:/usr/xpg6/bin:/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:/usr/ucb:/usr/ccs/bin:/opt/bin:/opt/sbin:/opt/local/bin:/snap/bin:/netscaler:/opt/homebrew/bin:/usr/xpg4/bin:/usr/xpg6/bin:/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:/usr/ucb:/usr/ccs/bin:/opt/bin:/opt/sbin:/opt/local/bin:/snap/bin:/netscaler
2024-05-23 18:07:12 -0300 INF Loading uac.conf settings
2024-05-23 18:07:12 -0300 INF Exclude path pattern: 
2024-05-23 18:07:12 -0300 INF Exclude name pattern: 
2024-05-23 18:07:12 -0300 INF Exclude file system: 9p|afs|autofs|cifs|davfs|fuse|kernfs|nfs|nfs4|rpc_pipefs|smbfs|sysfs
2024-05-23 18:07:12 -0300 INF Hash algorithm: md5|sha1
2024-05-23 18:07:12 -0300 INF Max depth: 0
2024-05-23 18:07:12 -0300 INF Enable find mtime: true
2024-05-23 18:07:12 -0300 INF Enable find atime: false
2024-05-23 18:07:12 -0300 INF Enable find ctime: true
2024-05-23 18:07:12 -0300 INF Setting up tools and parameters
2024-05-23 18:07:12 -0300 INF find operators support: true
2024-05-23 18:07:12 -0300 INF find -path support: true
2024-05-23 18:07:12 -0300 INF find -size support: true
2024-05-23 18:07:12 -0300 INF find -maxdepth support: true
2024-05-23 18:07:12 -0300 INF find -perm support: true
2024-05-23 18:07:12 -0300 INF find -type support: true
2024-05-23 18:07:12 -0300 INF find -mtime support: true
2024-05-23 18:07:12 -0300 INF find -atime support: true
2024-05-23 18:07:12 -0300 INF find -ctime support: true
2024-05-23 18:07:12 -0300 INF find -print0 support: true
```

Once the collection begins, UAC logs each executed command on the target system using the `CMD` level.

```text
2024-05-23 18:07:12 -0300 INF Artifacts collection started
2024-05-23 18:07:12 -0300 INF Parsing live_response/process/ps.yaml
2024-05-23 18:07:12 -0300 CMD ps
2024-05-23 18:07:12 -0300 CMD ps auxwww
2024-05-23 18:07:12 -0300 CMD ps auxwwwf
2024-05-23 18:07:12 -0300 CMD ps -deaf
2024-05-23 18:07:12 -0300 CMD ps -ef
2024-05-23 18:07:12 -0300 CMD ps -efl
2024-05-23 18:07:12 -0300 CMD ps -eo pid,user,etime,args
2024-05-23 18:07:12 -0300 CMD ps -eo pid,user,lstart,args
2024-05-23 18:07:12 -0300 CMD ps -eo pid,user,cgroup
```

If a command produces an error (stderr), it will be logged on the same line, following the `2>` identifier.

```text
2024-05-23 18:07:44 -0300 CMD uptime -s 2> uptime: invalid option -- 's'/nTry 'uptime --help' for more information.
2024-05-23 18:08:04 -0300 CMD fdisk -l 2> fdisk: cannot open /dev/vda: Permission denied
```

## Acquisition log

After UAC completes execution, it generates a log file with detailed information about the acquisition process. This includes the case metadata and calculated cryptographic hashes (e.g., MD5 and SHA-1) for the output archive to verify data integrity.

Example:

```text
Created by UAC (Unix-like Artifacts Collector)

[Case Information]
Case Number: 42
Evidence Number: 1
Description: Web server quick triage
Examiner: Duncan
Notes: 

[System Information]
Operating System: linux
System Architecture: x86_64
Hostname: webserver01

[Acquisition Information]
Mount Point: /
Acquisition Started: Mon May 26 20:29:36 2024 -0300
Acquisition Finished: Mon May 26 20:35:56 2024 -0300

[Output Information]
File: uac-webserver01-linux-20240526202936.tar.gz
Format: tar

[Computed Hashes]
md5 checksum: 5c5305c81a13efdbb394b588da84951d
sha1 checksum: ab46c30b259bcbaa7ee9cdb64cae660d27ab98d2
```
