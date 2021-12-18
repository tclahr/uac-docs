# Welcome to the Unix-like Artifacts Collector documentation

UAC is a Live Response collection tool for Incident Response that makes use of built-in tools to automate the collection of Unix-like systems artifacts. It was created to facilitate and speed up data collection, and depend less on remote support during incident response engagements.

UAC reads YAML files on the fly and, based on their contents, collects relevant artifacts using 5 different collectors: command, find, hash, stat and file. This makes UAC very customizable and extensible.

The source code is available from the [project page](https://github.com/tclahr/uac).

## Main features

- Fast and continuously tested.
- Runs everywhere with no dependencies (no installation required).
- Customizable and extensible collections and artifacts.
- Respects the order of volatility during artifacts collection.
- Collects information from processes running without a binary on disk.
- Extracts information from files and directories to create a bodyfile (including enhanced file attributes for ext4).
- Hashes running processes and executable files.
- Acquires volatile memory from Linux systems using Microsoft's [avml](https://github.com/microsoft/avml) tool.

## Supported operating systems

UAC runs on any Unix-like system (regardless the processor architecture). All UAC needs is shell.

- AIX
- Android
- FreeBSD
- Linux
- macOS
- NetBSD
- NetScaler
- OpenBSD
- Solaris

## Command line options

UAC is a command line tool, and as such, has several options that can be used to interact with it.

<pre class="code">
Usage: ./uac [-h] [-V] [--debug] {-p PROFILE | -a ARTIFACTS} DESTINATION
             [-m MOUNT_POINT] [-o OPERATING_SYSTEM] [-u] [--temp_dir PATH]
             [--date-range-start YYYY-MM-DD] [--date-range-start YYYY-MM-DD]
             [--case-number CASE_NUMBER] [--description DESCRIPTION]
             [--evidence-number EVIDENCE_NUMBER] [--examiner EXAMINER]
             [--notes NOTES] [--hostname HOSTNAME] [--stfp SERVER] 
             [--sftp-port PORT] [--sftp-identity-file FILE]
             [--sftp-delete-local-on-success] [--debug]
   or: ./uac --validate-artifacts-file FILE

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
  DESTINATION       Specify the directory the output file will be created in.

Collection Arguments:
  -m, --mount-point MOUNT_POINT
                    Specify the mount point (default: /).
  -o, --operating-system OPERATING_SYSTEM
                    Specify the operating system.
                    Options: aix, android, freebsd, linux, macos, netbsd
                             netscaler, openbsd, solaris
  -u, --run-as-non-root
                    Disable root user check.
                    Note that data collection may be limited.
      --temp-dir PATH   
                    Write all temporary data to this directory.

Filter Arguments:
      --date-range-start YYYY-MM-DD
                    Only collects files that were last modified/accessed/changed
                    after given date.
      --date-range-end YYYY-MM-DD
                    Only collects files that were last modified/accessed/changed
                    before given date.

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
      --hostname HOSTNAME
                    Specify the target system hostname.

Remote Transfer Arguments:
      --sftp SERVER
                    Transfer output file to remote SFTP server.
                    SERVER must be specified in the form [user@]host:[path]
      --sftp-port PORT
                    Remote SFTP server port (default: 22).
      --sftp-identity-file FILE
                    File from which the identity (private key) for public key
                    authentication is read.
      --sftp-delete-local-on-success
                    Delete local output file on successful transfer.

Validation Arguments:
      --validate-artifacts-file FILE
                    Validate artifacts file.
</pre>

## Using UAC

A [profile](profile_file.md) name or a list of [artifacts](artifacts_file.md), and the destination directory need to be provided in order to run a collection. The remaining parameters are optional.

Common usage scenarios may include the following:

- Collect all artifacts based on the ```full``` profile, and create the output file in ```/tmp```.

```shell
./uac -p full /tmp
```

- Collect all ```live_response```, and the ```bodyfile/bodyfile.yaml``` artifact, and create the output file in the current directory.

```shell
./uac -a live_response/\*,bodyfile/bodyfile.yaml .
```

- Collect all artifacts based on the ```full``` profile, but excludes the ```bodyfile/bodyfile.yaml``` artifact, and create the output file in ```/tmp```. 

```shell
./uac -p full -a \!bodyfile/bodyfile.yaml /tmp
```

- Note that when a profile and a list of artifacts are provided, the artifacts from the profile will always be collected first, even if the parameter ```-a``` was provided before ```-p``` in the command line. In the example below, the ```memory_dump/avml.yaml``` artifact will only be collected after all artifacts from ```full``` profile were collected.

```shell
./uac -a memory_dump/avml.yaml -p full /tmp
```

- Collect all artifacts based on the ```full``` profile, but limiting the data collection based on the date range provided.

```shell
./uac -p full /tmp --date-range-start 2021-05-01 --date-range-end 2021-08-31
```

- Collect all ```logs``` from a Linux disk image mounted in ```/mnt/ewf```.

```shell
./uac -a files/logs/\* /tmp --mount-point /mnt/ewf --operating-system linux
```

## Validating custom artifacts files

It is recommended that you validate your custom artifacts files before running a collection. You can do it using the ```--validate-artifacts-file``` option as shown below.

```shell
./uac --validate-artifacts-file ./artifacts/files/my_custom_artifact.yaml
```

## Using your own binary files

Place your validated binary files in the ```[uac_directory]\bin``` directory if you want them to be executed instead of the built-in ones provided by the target operating system.

UAC will detect and run CPU architecture specific executable files, so they need to be placed within the following directory structure: ```[uac_directory]\bin\[operating_system]\[architecture]```.

Operating system must be one of the following options (in lowercase): android, aix, freebsd, linux, macos, netbsd, netscaler, openbsd or solaris.

Architecture is the kernel architecture (in lowercase). It can be retrieved using ```uname``` tool.

For example:

- if you have a ```ss``` binary for Linux x86_64, it needs to be placed in the ```[uac_directory]\bin\linux\x86_64``` directory.
- if you have a ```lsof``` binary for AIX powerpc, it needs to be placed in the ```[uac_directory]\bin\aix\powerpc``` directory.
- if you have a ```netstat``` binary for Android aarch64, it needs to be placed in the ```[uac_directory]\bin\android\aarch64``` directory.

## Troubleshooting and support

### Debug mode

Debug mode ```--debug``` may be useful if you are trying to find the source for an error. When debug mode is on, UAC will use ```set -x``` to generate debugging messages, and store them into ```uac.log.stderr``` file.

### File an issue on Github

If you are experiencing an issue with UAC, you can create a New Issue at the [project page](https://github.com/tclahr/uac). See the Github [guide on creating an issue](https://docs.github.com/en/issues/tracking-your-work-with-issues/creating-an-issue) for more information.

## License

The UAC project uses the [Apache License Version 2.0](https://www.apache.org/licenses/LICENSE-2.0) software license.