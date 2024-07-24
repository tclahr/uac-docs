<p align="center">
    <img src="img/uac-dark.svg" alt="logo" width="120px">
</p>

# Welcome to the Unix-like Artifacts Collector documentation

UAC is a Live Response collection script for Incident Response that makes use of native binaries and tools to automate the collection of AIX, ESXi, FreeBSD, Linux, macOS, NetBSD, NetScaler, OpenBSD and Solaris systems artifacts. It was created to facilitate and speed up data collection, and depend less on remote support during incident response engagements.

UAC reads YAML files on the fly and, based on their contents, collects relevant artifacts. This makes UAC very customizable and extensible.

The source code is available on the [project](https://github.com/tclahr/uac) page](https://github.com/tclahr/uac).

[![uac_collection](img/uac_3_collection.gif)](#)

## Main features

- Run everywhere with no dependencies (no installation required).
- Customizable and extensible collections and artifacts.
- Respect the order of volatility during artifact collection.
- Collect information about current running processes (including processes without a binary on disk).
- Hash running processes and executable files.
- Extract files and directories status to create a bodyfile.
- Collect system and user-specific data, configuration files and logs.
- Acquire volatile memory from Linux systems using different methods and tools.

## Supported operating systems

UAC runs on any Unix-like system, regardless of the processor architecture. All UAC needs is shell :)

- AIX
- ESXi
- FreeBSD
- Linux
- macOS
- NetBSD
- NetScaler
- OpenBSD
- Solaris

*Note that UAC even runs on systems like Network Attached Storage (NAS) devices, Network devices such as OpenWrt, and IoT devices.*

## Using UAC

UAC does not need to be installed on the target system. Simply download the latest version from the [releases page](https://github.com/tclahr/uac/releases), uncompress it, and launch. It's that simple!

> **Full Disk Access** permission is a privacy feature introduced in macOS Mojave (10.14) that prevents some applications from accessing important data, such as Mail, Messages, and Safari files. So it is strongly recommended that you manually [grant permission for Terminal application](https://support.apple.com/guide/mac-help/allow-access-to-system-configuration-files-mchlccb25729/mac) before running UAC from the terminal, or [grant permission for remote users](https://support.apple.com/guide/mac-help/allow-a-remote-computer-to-access-your-mac-mchlp1066/mac) before running UAC via ssh.

To execute a collection, you must supply at least a [profile](profiles.md) and/or a list of [artifacts](artifacts.md), and specify the destination directory. Any additional parameters are optional.

Examples:

Collect all artifacts based on the ir_triage profile, and save the output file to /tmp.

```shell
./uac -p ir_triage /tmp
```

Collect all artifacts located in the artifacts/live_response directory, and save the output file to /tmp.

```shell
./uac -a ./artifacts/live_response/\* /tmp
```

Collect all artifacts based on the ir_triage profile, along with all artifacts located in the /my_custom_artifacts directory, and save the output file to /mnt/sda1.

```shell
./uac -p ir_triage -a /my_custom_artifacts/\* /mnt/sda1
```

Collect a memory dump and all artifacts based on the full profile.

```shell
./uac -a ./artifacts/memory_dump/avml.yaml -p full /tmp
```

Collect all artifacts based on the ir_triage profile excluding the bodyfile.yaml artifact.

```shell
./uac -p ir_triage -a \!artifacts/bodyfile/bodyfile.yaml /tmp
```

## Command line options

UAC is a command line tool, and as such, has several options that can be used to interact with it. It is recommended to run UAC from an external device/usb/network share to avoid overwriting evidence.

<pre class="code">
Usage: ./uac [-h] [-V] [--debug] {-p PROFILE | -a ARTIFACT} DESTINATION 
             
   or: ./uac --validate-artifact FILE
   or: ./uac --validate-profile FILE

Optional Arguments:
  -h, --help        Display this help and exit.
  -v, --verbose     Increases the verbosity level.
      --debug       Enable debug mode.
      --trace       Enable trace messages.
  -V, --version     Output version information and exit.

Profiling Arguments:
  -p, --profile   PROFILE
                    Specify the collection profile name or path.
                    Use '--profile list' to list all available profiles.
  -a, --artifacts ARTIFACT
                    Specify the artifact(s) to be collected during the collection.
                    The expression is a comma separated string where each element
                    is an artifact. You can exclude individual artifacts by
                    prefixing them with an exclamation mark (!).
                    Special characters such as ! and * must be escaped with a
                    backslash.
                    Examples: --artifacts files/logs/\*,\!files/logs/var_log.yaml
                    Use '--artifacts list [OPERATING_SYSTEM]' to list available 
                    artifacts (default: all).

Positional Arguments:
  DESTINATION       Specify the directory the output file should be copied to.

Output Arguments:
  -o, --output-base-name BASENAME
                    Specify the base name of the output file (without extension).
                    Default: uac-%hostname%-%os%-%timestamp%
  -f, --output-format FORMAT
                    Specify the output format.
                    Compression will be enabled if gzip is available.
                    Options: none, tar, zip (default: tar)
  -P, --output-password PASSWORD
                    Specify the password to be used to encrypt the contents
                    of the archive file.
                    Applies to zip output format only.

Collection Arguments:
  -c, --config      FILE
                    Load the config from a specific file.
  -m, --mount-point MOUNT_POINT
                    Specify the mount point (default: /).
  -s, --operating-system OPERATING_SYSTEM
                    Specify the operating system.
                    Options: aix, esxi, freebsd, linux, macos, netbsd
                             netscaler, openbsd, solaris
  -H, --hash-collected
                    Hash all collected files.
  -t, --max-threads THREADS
                    Specify the number of files that will be processed in
                    parallel by the hash and stat collectors (default: 2).
                    Use '--max-threads list' to list the number of processing
                    units available on the system.
  -u, --run-as-non-root
                    Disable root user check.
                    Note that data collection may be limited.
      --hostname  HOSTNAME
                    Specify the target system hostname.
      --temp-dir  PATH   
                    Write all temporary data to this directory.

Filter Arguments:
      --start-date YYYY-MM-DD
                    Only collects files that were last modified/accessed/changed
                    after the given date.
      --end-date  YYYY-MM-DD
                    Only collects files that were last modified/accessed/changed
                    before the given date.

Informational Arguments:
      --case-number CASE_NUMBER
                    Specify the case number.
      --description DESCRIPTION
                    Specify the description.
      --evidence-number EVIDENCE_NUMBER
                    Specify the evidence number.
      --examiner  EXAMINER
                    Specify the examiner name.
      --notes     NOTES
                    Specify the notes.

Remote Transfer Arguments:
      --sftp SERVER
                    Transfer the output file to remote SFTP server.
                    SERVER must be specified in the form [user@]host:[path]
      --sftp-port PORT
                    Remote SFTP server port (default: 22).
      --sftp-identity-file FILE
                    File from which the identity (private key) for public key
                    authentication is read.
      --sftp-ssh-options
                    Comma separated ssh options.
      --s3-provider
                    Transfer the output and log files to S3 service.
                    Options: amazon, google, ibm
      --s3-region
                    S3 region name (default: us-east-1 [amazon], us-south [ibm]).
      --s3-bucket
                    S3 bucket/cloud object storage name.
      --s3-access-key
                    The access key for the bucket/cloud object storage.
      --s3-secret-key
                    The secret access key for the bucket/cloud object storage.
      --s3-token
                    The session/bearer token for the bucket/cloud object storage.
      --aws-s3-presigned-url URL
                    Transfer the output file to AWS S3 using a pre-signed URL.
      --aws-s3-presigned-url-log-file URL
                    Transfer the log file to AWS S3 using a pre-signed URL.
      --azure-storage-sas-url URL
                    Transfer the output file to Azure Storage using a SAS URL.
      --azure-storage-sas-url-log-file URL
                    Transfer the log file to Azure Storage using a SAS URL.
      --delete-local-on-successful-transfer
                    Delete local output and log files on successful transfer.

Validation Arguments:
      --validate-artifact FILE
                    Validate artifact.
      --validate-profile FILE
                    Validate profile.

</pre>

### Optional Arguments

**--help**

Display the help and exit.

**-v, --verbose**

Increases the verbosity level. Enabling a higher verbosity level will result in the display of all executed commands.

**--debug**

Enable debug mode. Enabling debug mode will prevent the removal of the ```uac-data.tmp``` directory in the destination directory. This directory stores collected, temporary and debugging data during execution.

**--trace**

Enable ```set -x```. Using ```set -x``` in a shell script enables command tracing by printing each command before execution, which helps in debugging and understanding the script's flow.

**-V, --version**

Output version information and exit.

### Profiling Arguments

**-p, --profile**

Specify the collection profile name. Profiles are used to define the list of artifacts that will be used during the execution. They are YAML files located in the ```profiles``` directory.

Feel free to utilize multiple profiles to craft a highly tailored collection. Profiles will be processed in the sequence they are listed in the command line. It's important to note that duplicate artifacts will be gathered just once.

Use ```--profile list``` to list available profiles.

Examples:

```shell
./uac -p ir_triage DESTINATION
```

```shell
./uac -p ir_triage -p offline DESTINATION
```

You also have the option to supply a file path to a custom profile located outside the ```profiles``` directory.

Example:

```shell
./uac -p /home/user/my_custom_profile.yaml DESTINATION_DIR
```

**-a, --artifacts**

Specify the artifacts to be used during the collection. Artifacts are used to define parameters that will be used by a [collector](artifacts.md#collector) to collect data. They are YAML files located in the ```artifacts``` directory.

Feel free to utilize multiple artifacts to craft a highly tailored collection. Artifacts will be processed in the sequence they are listed in the command line. It's important to note that duplicate artifacts will be gathered just once.

You have the option to specify multiple artifacts simultaneously by either separating them with a comma (without spaces) or by using -a/--artifacts multiple times.

You can exclude individual artifacts by prefixing them with an exclamation mark (!). Note that in many shells, special characters like ! and * require escaping with a backslash.

Use ```--artifacts list``` to list all available artifacts.

Use ```--artifacts list [OPERATING_SYSTEM]``` to list all available artifacts for a specific operating system.

Examples:

```shell
./uac -a files/logs/\*,\!files/logs/var_log.yaml DESTINATION
```

```shell
./uac -a artifacts/live_response/\* -a ./artifacts/files/logs/\* -a \!files/logs/var_log.yaml DESTINATION
```

You also have the option to supply a file path to a custom artifact file located outside the ```artifacts``` directory.

Examples:

```shell
./uac -a files/logs/\* -a /home/user/my_artifact_file.yaml DESTINATION
```

```shell
./uac -a /home/user/my_artifacts/\* DESTINATION
```

### Positional Arguments

**DESTINATION**

The directory where the output and acquisition log files should be copied to.

### Output Arguments

**-o, --output-base-name**

Specify the base name of the output file (without extension). Default is ```uac-%hostname%-%os%-%timestamp%```

Examples:

```shell
./uac -p ir_triage -o my_custom_output_file_name DESTINATION
```

```shell
./uac -a ./artifacts/files/browsers/chrome.yaml -o uac-chrome-only-%hostname%-%timestamp% DESTINATION
```

**-f, --output-format**

Specify the output format. Compression will be automatically enabled if ```gzip``` is available.

Options: none, tar, zip (Default is ```tar```)

- **none**: Collected data will not be archived or compressed. Instead, it will be copied directly to an output directory.

- **tar**: Collected data will be archived (and compressed) into a tar file.

- **zip**: Collected data will be archived and compressed into a zip file.

Examples:

```shell
./uac -p full --output-format none DESTINATION
```

**-P, --output-password**

Specify the password to be used to encrypt the contents of the archive file. Applies to zip output format only.

Examples:

```shell
./uac -p ir_triage --output-format zip --output-password "infected" DESTINATION
```

### Collection Arguments

**-c, --config**

Load the config from a specific file.

Examples:

```shell
./uac -p full -c /home/user/my_custom_uac_config.conf DESTINATION
```

**-m, --mount-point**

The mount point where the files will be collected from. Default is ```/```

Examples:

```shell
./uac -p offline -m /mnt/sda1 DESTINATION
```

**-s, --operating-system**

This option allows you to force UAC to collect artifacts for a specific operating system. By default, UAC automatically attempts to identify the target operating system.

Options: aix, esxi, freebsd, linux, macos, netbsd, netscaler, openbsd, solaris

**-H, --hash-collected**

Enabling this option will cause UAC to hash all collected files and save the results in a hash file. To accomplish this, all collected data must first be copied to the destination directory. Therefore, ensure you have twice the free space available on the system: once for the collected data and once for the output file. Additionally, note that this process will increase the running time.

**-t, --max-threads**

This option allows you to specify the number of files that will be processed in parallel by the ```hash``` and ```stat``` collectors by setting the -P/--max-procs parameter of xargs at runtime. Note that this option will be ignored on systems where xargs does not support the -P/--max-procs parameter.

Ideally, you want to set --max-threads to a number that matches or is slightly below the number of CPU threads available. This ensures you utilize reasonable processing power without overloading the system.

Use ```--max-threads list``` to list all processing units/threads available on the system.

**-u, --run-as-non-root**

Disable root user check. Note that artifact collection may be limited.

**--hostname**

Specify the hostname to be used as part of the output name when %hostname% is used in --output-base-name. By default, UAC automatically attempts to identify the target system's hostname.

**--temp-dir**

The location where the ```uac-data.tmp``` directory will be created. This directory stores collected, temporary and debugging data during execution. By default, ```uac-data.tmp``` will be created within the DESTINATION directory.

### Filter Arguments

**--start-date**

Collect only the files that were last modified, accessed, or changed after a specific date. The date must be in the format: YYYY-MM-DD.

**--end-date**

Collect only the files that were last modified, accessed, or changed before a specific date. The date must be in the format: YYYY-MM-DD.

### Informational Arguments

**--case-number**

Specify the case number.

**--description**

Specify the case description.

**--evidence-number**

Specify the evidence number.

**--examiner**

Specify the examiner's name.

**--notes**

Specify the case notes.

### Remote Transfer Arguments

**--sftp**

SFTP server host/IP for transferring the output and acquisition log files. It also transfers collected data when ```none``` is used as the output format. It must be specified in the form ```[user@]host:[path]```

Examples:

```shell
./uac -p full --sftp "user@host:/remote_dir" DESTINATION
```

**--sftp-port**

SFTP server port. Default is ```22```.

**--sftp-identity-file**

File from which the identity (private key) for public-key authentication is read.

**--sftp-ssh-options**

Comma-separated options that modify the behavior of the SSH client. These options are usually specified in the SSH client configuration file, but this argument allows you to specify them directly on the command line.

Examples:

```shell
./uac -p ir_triage --sftp "user@host:/remote_dir" --sftp-ssh-options "StrictHostKeyChecking=no,UserKnownHostsFile=/dev/null" DESTINATION
```

**--s3-provider**

Transfer the output and log file to a S3 bucket/cloud object storage.

Options: amazon, google, ibm

**--s3-region**

S3 region name.

Default: us-east-1 (amazon), us-south (ibm)

**--s3-bucket**

S3 bucket/cloud object storage name

**--s3-access-key**

The access key for the bucket/cloud object storage.

**--s3-secret-key**

The secret access key for the bucket/cloud object storage.

```shell
./uac -p ir_triage --s3-provider amazon --s3-region us-east-1 --s3-bucket uac-test --s3-access-key "AKIAXKDAGGVYYXDZKAGH1" --s3-secret-key "plJqM2mFAu2lVBhpi5UrL+X+8hL+LOYOJryhp2s2" DESTINATION
```

**--s3-token**

The session/bearer token for the bucket/cloud object storage.

Examples:

```shell
./uac -p ir_triage --s3-provider ibm --s3-region us-south --s3-bucket uac-test --s3-token "eyJraWQiOiIyMDIyMDkxMzA4MjciLCJhbGciOiJSUzI1NiJ9.eyJpYW1faWQiOiJJQk1pZC0wNjAwMDFLVVJDIiwiaWQiOiJJQk1pZC0wNjAwMDFLVVJDIiwicmVhbG1pZCI6IklCTWlkIiwianRpIjoiYWVhMDE0MGYtZGM2Ni00MzE0LWJhMjMtZTIxOTAzZmQ3Y2ZhIiwiaWRlbnRpZmllciI6IjA2MDAwMUtVUkMiLCJnaXZlbl9uYW1lIjoiVGhpYWdvIiwiZmFtaWx5X25hbWUiOiJDYW5ve" DESTINATION
```

**--aws-s3-presigned-url**

This allows for using a pre-signed URL to upload the output file to AWS S3. Make sure you generate a PUT URL for this to work. It is strongly recommended to use single quotes to enclose the URL.

Example:

```shell
./uac -p full --aws-s3-presigned-url 'https://uac-test.s3.amazonaws.com/uac-output.tar.gz?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=ALIATVL26RGHMZ23AUV6%2F20220924%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20220924T135105Z&X-Amz-Expires=360000&X-Amz-SignedHeaders=host&X-Amz-Signature=74328833a872951ed54dc0dd57180b814a808ad53efc97b361487c10a87bc795' DESTINATION
```

**--aws-s3-presigned-url-log-file**

This allows for using a pre-signed URL to upload the acquisition log file to AWS S3. Make sure you generate a PUT URL for this to work. It is strongly recommended to use single quotes to enclose the URL.

Example:

```shell
./uac -p full --aws-s3-presigned-url 'https://uac-test.s3.amazonaws.com/uac-output.tar.gz?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=ALIATVL26RGHMZ23AUV6%2F20220924%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20220924T135105Z&X-Amz-Expires=360000&X-Amz-SignedHeaders=host&X-Amz-Signature=74328833a872951ed54dc0dd57180b814a808ad53efc97b361487c10a87bc795' --aws-s3-presigned-url-log-file 'https://uac-test.s3.amazonaws.com/uac-output.log?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=ALIATVL26RGHMZ23AUV6%2F20220924%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20220924T135105Z&X-Amz-Expires=360000&X-Amz-SignedHeaders=host&X-Amz-Signature=74328833a872951ed54dc0dd57180b814a808ad53efc97b361487c10a87bc795' DESTINATION
```

**--azure-storage-sas-url**

This allows for using a shared access signature (SAS) URL to upload the output file to Azure Storage. It is strongly recommended to use single quotes to enclose the URL.

Example:

```shell
./uac -p ir_triage --azure-storage-sas-url 'https://uac-test.blob.core.windows.net/uac-container/uac-output.tar.gz?sp=racwdl&st=2022-09-20T11:20:49Z&se=2022-09-21T19:20:49Z&spr=https&sv=2021-06-08&sr=c&sig=LmNQLedzYBXKSlGGGA0D6x1qSCek1OHELZDiD13BxKk%3D' DESTINATION
```

**--azure-storage-sas-url-log-file**

This allows for using a shared access signature (SAS) URL to upload the acquisition log to Azure Storage. It is strongly recommended to use single quotes to enclose the URL.

Example:

```shell
./uac -p ir_triage --azure-storage-sas-url 'https://uac-test.blob.core.windows.net/uac-container/uac-output.tar.gz?sp=racwdl&st=2022-09-20T11:20:49Z&se=2022-09-21T19:20:49Z&spr=https&sv=2021-06-08&sr=c&sig=LmNQLedzYBXKSlGGGA0D6x1qSCek1OHELZDiD13BxKk%3D' --azure-storage-sas-url-log-file 'https://uac-test.blob.core.windows.net/uac-container/uac-output.log?sp=racwdl&st=2022-09-20T11:20:49Z&se=2022-09-21T19:20:49Z&spr=https&sv=2021-06-08&sr=c&sig=LmNQLedzYBXKSlGGGA0D6x1qSCek1OHELZDiD13BxKk%3D' DESTINATION
```

**--delete-local-on-successful-transfer**

Delete the local output and acquisition log files if they were successfully transferred to a remote destination such as an SFTP server or S3.

### Validation Arguments

**--validate-artifact**

Validate whether the artifact YAML file contains any errors.

Examples:

```shell
./uac --validate-artifact my_custom_artifact.yaml
```

**--validate-profile**

Validate whether the profile YAML file contains any errors.

Examples:

```shell
./uac --validate-profile my_custom_profile.yaml
```

## Validating custom artifacts

It is recommended that you validate your custom artifacts before running a collection. You can do it using the ```--validate-artifact``` option as shown below.

```shell
./uac --validate-artifact /path_to/my_custom_artifact.yaml
```

## Validating custom profiles

It is recommended that you validate your custom profiles before running a collection. You can do it using the ```--validate-profile``` option as shown below.

```shell
./uac --validate-profile /path_to/my_custom_profile.yaml
```

## Using your binary files

In most cases, the executables should be placed in the ```[UAC_DIRECTORY]/bin``` directory, along with any additional support files it needs to run.

For example, if you have an artifact that uses an executable named 'my_script.sh', you should place this binary in the ```[UAC_DIRECTORY]/bin``` directory.

In the case where you have executables with the same name, but for multiple operating systems, they should be placed in the ```[UAC_DIRECTORY]/bin/[OS]``` directory.

For example, if you have an artifact that uses an executable named 'lsof', but you have two binary files, one for Linux and one for FreeBSD, you should place the binaries in the ```[UAC_DIRECTORY]/bin/linux``` and ```[UAC_DIRECTORY]/bin/freebsd``` directories. Note that the operating system name must be in lowercase.

In the case where you have executables that can be run on multiple operating systems, they should be placed in the ```[UAC_DIRECTORY]/bin/[OS1_OS2_OS3]``` directory. Note that you can have multiple operating systems separated by an underscore '_'.

For example, if you have an artifact that uses an executable named 'netstat' that runs on both Linux and ESXi systems, you should place the binary either in the ```[UAC_DIRECTORY]/bin/linux_esxi``` directory or place the binary in the ```[UAC_DIRECTORY]/bin/linux``` and ```[UAC_DIRECTORY]/bin/esxi``` directories.

In the case where you have executables with the same name, but for multiple operating systems and multiple architectures, they should be placed in the ```[UAC_DIRECTORY]/bin/[OS]/[ARCH]``` directory.

For example, if you have an artifact that uses an executable named 'ss', but you have binary files for Linux arm64 and ppc64, FreeBSD i386, and Solaris x86_64 and sparc64, you should place the binary files in the ```[UAC_DIRECTORY]/bin/linux/arm64```, ```[UAC_DIRECTORY]/bin/linux/ppc64```, ```[UAC_DIRECTORY]/bin/freebsd/i386```, ```[UAC_DIRECTORY]/bin/solaris/x86_64``` and ```[UAC_DIRECTORY]/bin/solaris/sparc64``` directories.

## Troubleshooting and support

### Debug mode

Debug mode ```--debug``` may be useful if you are trying to find the source of an error. Enabling debug mode will prevent the removal of the ```uac-data.tmp``` directory in the destination directory. This directory stores collected, temporary and debugging data during execution.

### Trace messages

Using ```--trace``` enables command tracing by printing each command before execution, which helps in debugging and understanding the script's flow. It is recommended to redirect the stderr (shell tracing messages) to a text file.

Consider the following command to enable command tracing and storing them in /tmp/uac-tracing.log file.

```shell
./uac -p ir_triage --trace DESTINATION 2>/tmp/uac-tracing.log
```

### File an issue on Github

If you are a user and find a bug, please submit an [issue](https://github.com/tclahr/uac/issues) and provide enough information for others to reproduce it. One of the project's [maintainers](https://github.com/tclahr/uac/blob/master/MAINTAINERS.md) should respond to your issue soon.

Before submitting, please check our list of [already reported bugs](https://github.com/tclahr/uac/issues) to ensure you are not raising a duplicate.

See our [Contributing Guide](https://github.com/tclahr/uac/blob/master/CONTRIBUTING.md) for more information.

### Community support

For additional help, you can use one of the channels to ask a question:

- [Discord](https://discord.com/invite/digitalforensics) (For live discussion with the community and UAC team)
- [GitHub](https://github.com/tclahr/uac/issues) (Bug reports and contributions)
- [Twitter](https://twitter.com/tclahr) (Get the news fast)

## Contributing

Have you created any artifacts? Please share them with us!

You can contribute with new artifacts, profiles, bug fixes or even propose new features. Please read our [Contributing Guide](https://github.com/tclahr/uac/blob/master/CONTRIBUTING.md) before submitting a Pull Request to the project.

## License

The UAC project uses the [Apache License Version 2.0](https://www.apache.org/licenses/LICENSE-2.0) software license.