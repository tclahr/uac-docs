# Profiles definition

Profiles are used to define the list of artifacts that will be collected during the execution.

A profile has two mandatory fields at the top of the file, followed by a set of artifacts that will be used during the collection.

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

The profile name. This is the name UAC uses to find the correct profile during the script execution.

## description
<span class="required">Required</span>

Describes the purpose of the profile as a whole. It should contain all the details about what kind of collection will be performed. No line breaks are supported.

## artifacts
<span class="required">Required</span>

This is the set of artifacts that will be used during the collection. All available artifacts are located in the ```artifacts``` directory.

Artifacts will be executed in the order they are listed in the profile file.

The example below shows a profile that will collect the ```live_response/process/ps.yaml``` artifact first, followed by the ```live_response/process/lsof.yaml``` artifact.

```yaml
name: myprofile
description: My profile.
artifacts:
  - live_response/process/ps.yaml
  - live_response/process/lsof.yaml
```

Wildcards and regex patterns (globbing) are also supported for referring to artifacts. The example below shows a profile that will collect all artifacts located in the ```live_response/process``` directory (including subdirectories). In this case, the artifacts will be processed in alphabetical order.

```yaml
name: myprofile
description: My profile.
artifacts:
  - live_response/process/*
```

Artifacts can be excluded from the collection by prefixing the artifact path with an exclamation mark (!). The example below shows a profile that will collect all artifacts in the ```files``` directory (including subdirectories), except for ```files/logs/additional_logs.yaml``` and all artifacts in the ```files/applications``` directory.

```yaml
name: myprofile
description: My profile.
artifacts:
  - files/*
  - !files/logs/additional_logs.yaml
  - !files/applications/*
```

Artifacts located outside the artifacts directory can also be used by specifying their full path. The example below shows a profile that will first collect all artifacts in the ```live_response/process``` directory (including subdirectories), followed by a custom artifact located in the ```/my_artifacts_dir``` directory. Remember to use the full path for artifacts outside the ```artifacts``` directory.

```yaml
name: myprofile
description: My profile.
artifacts:
  - live_response/process/*
  - /my_artifacts_dir/my_artifact.yaml
```
