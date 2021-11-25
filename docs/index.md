# Welcome to the Unix-like Artifacts Collector documentation

UAC is a Live Response collection tool for Incident Response that makes use of built-in tools to automate the collection of Unix-like systems artifacts. It was created to facilitate and speed up data collection, and depend less on remote support during incident response engagements.

UAC reads YAML files on the fly and, based on their contents, collects relevant artifacts using 5 different collectors: command, find, hash, stat and file. This makes UAC very customizable and extensible.

The source code is available from the [project page](https://github.com/tclahr/uac).

## Supported Operating Systems

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