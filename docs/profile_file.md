# Profile file definition

Profiles are used to define the list of artifacts that will be collected during the execution. They are YAML files located in the ```profiles``` directory.

A profile file has two mandatory fields at the top of the file, followed by a set of artifacts files that will be used during the collection.

Example:
```yaml
name: myprofile
description: My profile.
artifacts:
  - live_response/process/ps.yaml
  - live_response/process/lsof.yaml
  - bodyfile/bodyfile.yaml
  - hash_executables/hash_executables.yaml
```

## name
<span class="required">Required</span>

The profile name. This is the name UAC uses to find the correct profile file during the script execution.

## description
<span class="required">Required</span>

Describes the purpose of the profile as a whole. It should contain all the details about what kind of collection will be performed. No line breaks are supported.

## artifacts
<span class="required">Required</span>

Set of artifacts files that will be used during the collection. All available artifacts files are located in the ```artifacts``` directory.

Artifacts files will be executed in the same order they were added to the profile file.

The example below shows a profile file that will collect `ps````, and then ```lsof``` artifacts.

```yaml
name: myprofile
description: My profile.
artifacts:
  - live_response/process/ps.yaml
  - live_response/process/lsof.yaml
```

Wildcards and regex patterns (globbing) are also supported to refer to artifacts files. The example below shows a profile that will collect all artifacts located in the ```live_response/process``` directory. Note that the artifacts files will be parsed in alphabetical order in this case.

```yaml
name: myprofile
description: My profile.
artifacts:
  - live_response/process/*
```

Artifacts files can also be skipped from the collection using the exclamation mark (!) before the artifacts file path. The example below shows a profile that will collect all artifacts located in the ```files``` directory (and subdirectories), excluding ```files/logs/additional_logs.yaml``` and all artifacts files located in ```files/applications``` directory.

```yaml
name: myprofile
description: My profile.
artifacts:
  - files/*
  - !files/logs/additional_logs.yaml
  - !files/applications/*
```
