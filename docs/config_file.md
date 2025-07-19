# Config files

UAC configuration files are located in the `config` directory.

The default configuration file is `uac.conf`, which will be automatically loaded by UAC during execution.

You can create files named after the operating system in the `config` directory to **override** the default configuration for a specific operating system. For example: `aix.conf` for AIX, `linux.conf` for Linux, `macos.conf` for macOS, etc.

You can also load an external configuration file using the `-c/--config` option to **override** the default and the operating system specific configuration.

## exclude\_path\_pattern

Exclude specific paths using shell-style patterns.

```ini
exclude_path_pattern: ["/etc", "/usr/*/local", "*/log"]
```

## exclude\_name\_pattern

Exclude files by name pattern. No slashes allowed in patterns.

```ini
exclude_name_pattern: ["*.txt", "*.gz.*", "*.[Ll][Oo][Gg]"]
```

## exclude\_file\_system

Exclude file systems by type. UAC retrieves the list of existing mountpoints (paths) at runtime and excludes them from the collection.

```ini
exclude_file_system: [9p, afs, nfs, nfs4, rpc_pipefs, smbfs, sysfs]
```

## hash_algorithm

Specifies the hash algorithms to be used by the `hash` collector.

Accepted values: `md5`, `sha1`, and `sha256`

```ini
hash_algorithm: [md5, sha1]
```

## max\_depth

Limit recursion depth in directories.

Accepted values: A positive integer or `0` to disable depth limiting.

**Performance consideration**: Specifying a value here can improve performance by reducing the search scope.

```ini
max_depth: 10
```

## enable\_find\_mtime / enable\_find\_atime / enable\_find\_ctime

Enable or disable filters based on file timestamps using find options:

-mtime: last modified

-atime: last accessed

-ctime: last status change

These options can be used to restrict collection to specific date ranges.

Accepted values: true, false

To collect only files whose data was last modified **or** whose status was last changed within the specified date range, set `enable_find_mtime` and `enable_find_ctime` to `true`, and `enable_find_atime` to `false`:

```ini
enable_find_mtime: true
enable_find_atime: false
enable_find_ctime: true
```

To collect only files whose status was last changed within the specified date range, set only `enable_find_ctime` to `true`:

```ini
enable_find_mtime: false
enable_find_atime: false
enable_find_ctime: true
```
