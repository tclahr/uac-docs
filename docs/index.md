# Welcome to the Unix-like Artifacts Collector documentation

UAC is a Live Response collection script for Incident Response that makes use of native binaries and tools to automate the collection of AIX, Android, ESXi, FreeBSD, Linux, macOS, NetBSD, NetScaler, OpenBSD and Solaris systems artifacts. It was created to facilitate and speed up data collection, and depend less on remote support during incident response engagements.

UAC reads artifacts files on the fly and, based on their contents, collects relevant artifacts. This makes UAC very customizable and extensible.

The source code is available from the [project page](https://github.com/tclahr/uac).

[![uac_collection](img/uac_collection.gif)](#)

## Main features

- Runs everywhere with no dependencies (no installation required).
- Customizable and extensible collections and artifacts.
- Respects the order of volatility during artifacts collection.
- Collects information from processes running without a binary on disk.
- Extracts information from files and directories to create a bodyfile (including enhanced file attributes for ext4).
- Hashes running processes and executable files.
- Collects user and system configuration files and logs.
- Collects artifacts from applications.
- Acquires volatile memory from Linux systems using Microsoft's [avml](https://github.com/microsoft/avml) tool.

## Supported operating systems

UAC runs on any Unix-like system (regardless of the processor architecture). All UAC needs is shell :)

[![AIX](https://img.shields.io/static/v1?label=&message=AIX&color=brightgreen&style=for-the-badge)](#)
[![Android](https://img.shields.io/static/v1?label=&message=Android&color=green&style=for-the-badge)](#)
[![ESXi](https://img.shields.io/static/v1?label=&message=ESXi&color=blue&style=for-the-badge)](#)
[![FreeBSD](https://img.shields.io/static/v1?label=&message=FreeBSD&color=red&style=for-the-badge)](#)
[![Linux](https://img.shields.io/static/v1?label=&message=Linux&color=lightgray&style=for-the-badge)](#)
[![macOS](https://img.shields.io/static/v1?label=&message=macOS&color=blueviolet&style=for-the-badge)](#)
[![NetBSD](https://img.shields.io/static/v1?label=&message=NetBSD&color=orange&style=for-the-badge)](#)

[![NetScaler](https://img.shields.io/static/v1?label=&message=NetScaler&color=blue&style=for-the-badge)](#)
[![OpenBSD](https://img.shields.io/static/v1?label=&message=OpenBSD&color=yellow&style=for-the-badge)](#)
[![Solaris](https://img.shields.io/static/v1?label=&message=Solaris&color=lightblue&style=for-the-badge)](#)

*Note that UAC even runs on systems like Network Attached Storage (NAS) devices, Network devices such as OpenWrt, and IoT devices.*

## Command line options

UAC is a command line tool, and as such, has several options that can be used to interact with it. It is recommended to run UAC from an external device/usb/network share to avoid overwriting evidence.

<pre class="code">
Usage: ./uac [-h] [-V] [--debug] {-p PROFILE | -a ARTIFACTS} DESTINATION
             [-m MOUNT_POINT] [-s OPERATING_SYSTEM] [-u] [--temp_dir PATH]
             [--date-range-start YYYY-MM-DD] [--date-range-start YYYY-MM-DD]
             [--case-number CASE_NUMBER] [--description DESCRIPTION]
             [--evidence-number EVIDENCE_NUMBER] [--examiner EXAMINER]
             [--notes NOTES] [--hostname HOSTNAME] [--stfp SERVER] 
             [--sftp-port PORT] [--sftp-identity-file FILE]
             [--s3-presigned-url URL] [--s3-presigned-url-log-file URL]
             [--azure-storage-sas-url URL] [--azure-storage-sas-url-log-file URL]
             [--ibm-cos-url URL] [--ibm-cos-url-log-file URL]
             [--ibm-cloud-api-key KEY]
             [--delete-local-on-successful-transfer] [--debug]
   or: $0 --validate-artifacts-file FILE

Optional Arguments:
  -h, --help        Display this help and exit.
  -V, --version     Output version information and exit.
      --debug       Enable debug mode.

Profiling Arguments:
  -p, --profile PROFILE
                    Specify the collection profile name. Use '--profile list'
                    to list available profiles.
  -a, --artifacts ARTIFACTS
                    Specify the artifacts to be collected during the collection.
                    The expression is a comma separated string where each element
                    is an artifact file. Each element can be prepended with an 
                    exclamation mark to exclude the artifact.
                    Special characters such as ! and * must be escaped with a
                    backslash.
                    Examples: --artifacts files/logs/\*,\!files/logs/var_log.yaml
                    Use '--artifacts list' to list available artifacts.

Positional Arguments:
  DESTINATION       Specify the directory the output file should be copied to.

Collection Arguments:
  -m, --mount-point MOUNT_POINT
                    Specify the mount point (default: /).
  -s, --operating-system OPERATING_SYSTEM
                    Specify the operating system.
                    Options: aix, android, esxi, freebsd, linux, macos, netbsd
                             netscaler, openbsd, solaris
  -u, --run-as-non-root
                    Disable root user check.
                    Note that data collection may be limited.
      --hostname HOSTNAME
                    Specify the target system hostname.
      --temp-dir PATH   
                    Write all temporary data to this directory.

Filter Arguments:
      --date-range-start YYYY-MM-DD
                    Only collects files that were last modified/accessed/changed
                    after the given date.
      --date-range-end YYYY-MM-DD
                    Only collects files that were last modified/accessed/changed
                    before the given date.

Informational Arguments:
      --case-number CASE_NUMBER
                    Specify the case number.
      --description DESCRIPTION
                    Specify the description.
      --evidence-number EVIDENCE_NUMBER
                    Specify the evidence number.
      --examiner EXAMINER
                    Specify the examiner name.
      --notes NOTES
                    Specify the notes.

Remote Transfer Arguments:
      --sftp SERVER
                    Transfer output file to remote SFTP server.
                    SERVER must be specified in the form [user@]host:[path]
      --sftp-port PORT
                    Remote SFTP server port (default: 22).
      --sftp-identity-file FILE
                    File from which the identity (private key) for public-key
                    authentication is read.
      --s3-presigned-url URL
                    Transfer output file to AWS S3 using a pre-signed URL.
      --s3-presigned-url-log-file URL
                    Transfer log file to AWS S3 using a pre-signed URL.
      --azure-storage-sas-url URL
                    Transfer output file to Azure Storage using a SAS URL.
      --azure-storage-sas-url-log-file URL
                    Transfer log file to Azure Storage using a SAS URL.
      --ibm-cos-url URL
                    Transfer output file to IBM Cloud Object Storage.
      --ibm-cos-url-log-file URL
                    Transfer log file to IBM Cloud Object Storage.
      --ibm-cloud-api-key KEY
                    IBM Cloud API key / Bearer token.
      --delete-local-on-successful-transfer
                    Delete local output and log files on successful transfer.

Validation Arguments:
      --validate-artifacts-file FILE
                    Validate artifacts file.
</pre>

## Using UAC

UAC does not need to be installed on the target system. You only need to download the latest version from the [releases page](https://github.com/tclahr/uac/releases), uncompress and run it. As simple as that!

A [profile](profile_file.md) name and/or a list of [artifacts](artifacts_file.md), and the destination directory need to be provided to run a collection. The remaining parameters are optional.

You can use as many --artifacts (-a) and --profile (-p) as you want to build an even more customized collection. Artifacts will be collected in the order they were provided in the command line. Note that duplicated artifacts will be collected only once.

> **Full Disk Access** permission is a privacy feature introduced in macOS Mojave (10.14) that prevents some applications from accessing important data, such as Mail, Messages, and Safari files. So it is strongly recommended that you manually [grant permission for Terminal application](https://support.apple.com/guide/mac-help/allow-access-to-system-configuration-files-mchlccb25729/mac) before running UAC from the terminal, or [grant permission for remote users](https://support.apple.com/guide/mac-help/allow-a-remote-computer-to-access-your-mac-mchlp1066/mac) before running UAC via ssh.

### Command line options

**-p, --profile**

Specify the collection profile name. Profiles are used to define the list of artifacts that will be collected during the execution. They are YAML files located in the ```profiles``` directory.

Use '--profile list' to list available profiles.

Examples:

```shell
-p ir_triage
```

```shell
-p ir_triage -p my_custom_profile
```

**-a, --artifacts**

Specify the artifacts to be collected during the collection. Artifacts are used to define parameters that will be used by a [collector](collectors.md) to collect data. They are YAML files located in the ```artifacts``` directory.

You can specify multiple artifacts at once by separating them with a comma (no spaces). Each element can be prepended with an exclamation mark to exclude the artifact. Special characters such as ! and * must be escaped with a backslash.

Use '--artifacts list' to list available artifacts.

Examples:

```shell
-a files/logs/\*,\!files/logs/var_log.yaml
```

```shell
-a artifacts/live_response/\* -a ./artifacts/files/logs/\* -a \!artifacts/files/logs/var_log.yaml
```

**DESTINATION**

The directory where the output and acquisition log files should be copied to.

### Collection options

**-m, --mount-point**

The mount point where the files will be collected from. Default is /.

**-s, --operating-system**

This option can be used to force UAC to collect artifacts for a given operating system. By default, UAC will always try to identify the target operating system automatically.

Accepted values: aix, android, esxi, freebsd, linux, macos, netbsd, netscaler, openbsd, solaris

**-u, --run-as-non-root**

Disable root user check. Note that artifacts collection may be limited.

**--hostname**

The target system hostname to be used as part of the output file name. By default, UAC will always try to identify the target system's hostname.

**--temp-dir**

The directory UAC will use to store temporary data.

### Filter options

**--date-range-start**

Only collects files that were last modified/accessed/changed after the given date. Value needs to be in YYYY-MM-DD format.

**--date-range-end**

Only collects files that were last modified/accessed/changed before the given date. Value needs to be in YYYY-MM-DD format.

### Case information options

**--case-number**

The case number.

**--description**

The case description.

**--evidence-number**

The evidence number.

**--examiner**

The examiner's name.

**--notes**

The case notes.

### SFTP options

**--sftp**

SFTP server host/IP for transferring the output and acquisition log files. It must be specified in the form [user@]host:[path]

**--sftp-port**

SFTP server port. Default is 22.

**--sftp-identity-file**

File from which the identity (private key) for public-key authentication is read.

### S3 options

**--s3-presigned-url**

This allows for using a pre-signed URL to upload the output file to S3 (if curl is available). Make sure you generate a PUT URL for this to work. It is strongly recommended to use single quotes to enclose the URL.

Example:

```https://uac-test.s3.amazonaws.com/uac-output.tar.gz?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=ALIATVL26RGHMZ23AUV6%2F20220924%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20220924T135105Z&X-Amz-Expires=360000&X-Amz-SignedHeaders=host&X-Amz-Signature=74328833a872951ed54dc0dd57180b814a808ad53efc97b361487c10a87bc795```

Please refer to [AWS documentation page](https://docs.aws.amazon.com/AmazonS3/latest/userguide/ShareObjectPreSignedURL.html) for more information.

**--s3-presigned-url-log-file**

This allows for using a pre-signed URL to upload the acquisition log file to S3 (if curl is available). Make sure you generate a PUT URL for this to work. It is strongly recommended to use single quotes to enclose the URL.

Example:

```https://uac-test.s3.amazonaws.com/uac-output.log?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=ALIATVL26RGHMZ23AUV6%2F20220924%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20220924T135105Z&X-Amz-Expires=360000&X-Amz-SignedHeaders=host&X-Amz-Signature=74328833a872951ed54dc0dd57180b814a808ad53efc97b361487c10a87bc795```

### Azure Storage options

**--azure-storage-sas-url**

This allows for using a shared access signature (SAS) URL to upload the output file to Azure Storage (if curl is available). It is strongly recommended to use single quotes to enclose the URL.

Example:

```https://uac-test.blob.core.windows.net/uac-container/uac-output.tar.gz?sp=racwdl&st=2022-09-20T11:20:49Z&se=2022-09-21T19:20:49Z&spr=https&sv=2021-06-08&sr=c&sig=LmNQLedzYBXKSlGGGA0D6x1qSCek1OHELZDiD13BxKk%3D```

Please refer to [Azure documentation page](https://learn.microsoft.com/en-us/azure/storage/common/storage-sas-overview) for more information.

**--azure-storage-sas-url-log-file**

This allows for using a shared access signature (SAS) URL to upload the acquisition log file to Azure Storage (if curl is available). It is strongly recommended to use single quotes to enclose the URL.

Example:

```https://uac-test.blob.core.windows.net/uac-container/uac-output.log?sp=racwdl&st=2022-09-20T11:20:49Z&se=2022-09-21T19:20:49Z&spr=https&sv=2021-06-08&sr=c&sig=LmNQLedzYBXKSlGGGA0D6x1qSCek1OHELZDiD13BxKk%3D```

### IBM Cloud Object Storage options

**--ibm-cos-url**

This allows uploading the output file to IBM Cloud Object Storage (if curl is available). It is strongly recommended to use single quotes to enclose the URL.

Example:

```https://s3.us-south.cloud-object-storage.appdomain.cloud/uac-test/uac-output.tar.gz```

Please refer to [IBM Cloud documentation page](https://cloud.ibm.com/docs/cloud-object-storage?topic=cloud-object-storage-curl) for more information.

**--ibm-cos-url-log-file**

This allows uploading the acquisition log file to IBM Cloud Object Storage (if curl is available). It is strongly recommended to use single quotes to enclose the URL.

Example:

```https://s3.us-south.cloud-object-storage.appdomain.cloud/uac-test/uac-output.log```

**--ibm-cloud-api-key**

IBM Cloud API key / Bearer token required to access the object storage.

Example:

```eyJraWQiOiIyMDIyMDkxMzA4MjciLCJhbGciOiJSUzI1NiJ9.eyJpYW1faWQiOiJJQk1pZC0wNjAwMDFLVVJDIiwiaWQiOiJJQk1pZC0wNjAwMDFLVVJDIiwicmVhbG1pZCI6IklCTWlkIiwianRpIjoiYWVhMDE0MGYtZGM2Ni00MzE0LWJhMjMtZTIxOTAzZmQ3Y2ZhIiwiaWRlbnRpZmllciI6IjA2MDAwMUtVUkMiLCJnaXZlbl9uYW1lIjoiVGhpYWdvIiwiZmFtaWx5X25hbWUiOiJDYW5ve``` (truncated)

### Diagnostic options

**--debug**

Enable debug mode. This will result in more details as to what UAC is doing in the background as it runs. The messages will be stored in the ```uac.log.stderr``` file.

### Other options

**--delete-local-on-successful-transfer**

Delete the local output and acquisition log files if they were successfully transferred to a remote destination such as an SFTP server or S3.

### Examples

Common usage scenarios may include the following:

**Collect all artifacts based on the ```full``` profile, and create the output file in ```/tmp```.**

```shell
./uac -p full /tmp
```

**Collect all ```live_response```, and the ```bodyfile/bodyfile.yaml``` artifact, and create the output file in the current directory.**

```shell
./uac -a live_response/\*,bodyfile/bodyfile.yaml .
```

**Collect all artifacts based on the ```full``` profile, but excludes the ```bodyfile/bodyfile.yaml``` artifact, and create the output file in ```/tmp```.**

```shell
./uac -p full -a \!bodyfile/bodyfile.yaml /tmp
```

**Collect the memory dump, then all artifacts based on the ```full``` profile.**

```shell
./uac -a artifacts/memory_dump/avml.yaml -p full /tmp
```

**Collect the memory dump, then all artifacts based on the ```ir_triage``` profile excluding the ```bodyfile/bodyfile.yaml``` artifact.**

```shell
./uac -a ./artifacts/memory_dump/avml.yaml -p ir_triage -a \!artifacts/bodyfile/bodyfile.yaml /tmp
```

**Collect all artifacts based on the ```full``` profile, but limit the data collection based on the date range provided.**

```shell
./uac -p full /tmp --date-range-start 2021-05-01 --date-range-end 2021-08-31
```

**Collect all but live response artifacts from a Linux disk image mounted in ```/mnt/ewf```.**

```shell
./uac -p full -a \!live_response/\* /tmp --mount-point /mnt/ewf --operating-system linux
```

## Validating custom artifacts files

It is recommended that you validate your custom artifacts files before running a collection. You can do it using the ```--validate-artifacts-file``` option as shown below.

```shell
./uac --validate-artifacts-file ./artifacts/files/my_custom_artifact.yaml
```

## Using your binary files

Place your validated binary files in the ```[uac_directory]\bin``` directory if you want them to be executed instead of the built-in ones provided by the target operating system.

UAC will detect and run CPU architecture-specific executable files, so they need to be placed within the following directory structure: ```[uac_directory]\bin\[operating_system]\[architecture]```.

Operating system must be one of the following options (in lowercase): android, aix, freebsd, linux, macos, netbsd, netscaler, openbsd or solaris.

Architecture is the kernel architecture (in lowercase). It can be retrieved using ```uname``` tool.

For example:

- if you have a ```ss``` binary for Linux x86_64, it needs to be placed in the ```[uac_directory]\bin\linux\x86_64``` directory.
- if you have a ```lsof``` binary for AIX powerpc, it needs to be placed in the ```[uac_directory]\bin\aix\powerpc``` directory.
- if you have a ```netstat``` binary for Android aarch64, it needs to be placed in the ```[uac_directory]\bin\android\aarch64``` directory.

## Troubleshooting and support

### Debug mode

Debug mode ```--debug``` may be useful if you are trying to find the source for an error. When debug mode is on, UAC will use ```set -x``` to generate debugging messages, and store them in ```uac.log.stderr``` file.

### File an issue on Github

If you are a user and you find a bug, please submit an [issue](https://github.com/tclahr/uac/issues). Please try to provide sufficient information for someone else to reproduce the issue. One of the project's [maintainers](https://github.com/tclahr/uac/blob/master/MAINTAINERS.md) should respond to your issue soon.

Please search within our [already reported bugs](https://github.com/tclahr/uac/issues) before raising a new one to make sure you're not raising a duplicate.

See our [Contributing Guide](https://github.com/tclahr/uac/blob/master/CONTRIBUTING.md) for more information.

### Community support

For additional help, you can use one of the channels to ask a question:

- [Discord](https://discord.com/invite/digitalforensics) (For live discussion with the community and UAC team)
- [GitHub](https://github.com/tclahr/uac/issues) (Bug reports and contributions)
- [Twitter](https://twitter.com/tclahr) (Get the news fast)

## Contributing

Have you created any artifact files? Please share them with us!

You can contribute with new artifacts, profiles, bug fixes or even propose new features. Please read our [Contributing Guide](https://github.com/tclahr/uac/blob/master/CONTRIBUTING.md) before submitting a Pull Request to the project.

## License

The UAC project uses the [Apache License Version 2.0](https://www.apache.org/licenses/LICENSE-2.0) software license.