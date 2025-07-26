<!-- markdownlint-disable MD036 -->
# Profile Definition

Profiles define the list of artifacts to be collected during execution.

Each profile file includes two mandatory fields at the top, followed by a set of artifacts that guide the collection process.

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

**Required**

The profile name. UAC uses this name to locate and apply the correct profile during script execution.

## description

**Required**

A brief summary describing the overall purpose of the profile. It should specify the type of collection being performed. Line breaks are not supported.

## artifacts

**Required**

The list of artifacts to be collected. All available artifacts are stored in the `artifacts` directory.

Artifacts are collected in the order they are listed.

The following example collects `live_response/process/ps.yaml` first, then `live_response/process/lsof.yaml`:

```yaml
name: myprofile
description: My profile.
artifacts:
  - live_response/process/ps.yaml
  - live_response/process/lsof.yaml
```

### Globbing and Wildcards

Wildcards and glob-style patterns are supported. The next example collects all artifacts in the `live_response/process` directory and its subdirectories, in alphabetical order:

```yaml
name: myprofile
description: My profile.
artifacts:
  - live_response/process/*
```

### Excluding Artifacts

Artifacts can be excluded using an exclamation mark (`!`) prefix. The example below collects everything from `files/`, except for `files/logs/additional_logs.yaml` and all items in `files/applications/`:

```yaml
name: myprofile
description: My profile.
artifacts:
  - files/*
  - !files/logs/additional_logs.yaml
  - !files/applications/*
```

### Using External Artifacts

Artifacts located outside the default `artifacts` directory can also be included by specifying their full path.

Example:

```yaml
name: myprofile
description: My profile.
artifacts:
  - live_response/process/*
  - /my_artifacts_dir/my_artifact.yaml
```

Ensure the full path is used when referencing external artifacts.
