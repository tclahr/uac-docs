# uac.conf

UAC configuration file is located in the config directory.

## exclude_path_pattern

Directory/path patterns will be excluded from find, file, hash and stat collectors. As find tool is used to search for files and directories, the path patterns below need to be compatible with -path option. Please check find man pages for more information.

Example:

```ini
exclude_path_pattern: ["/etc", "/usr/*/local", "*/log"]
```

## exclude_name_pattern

File name patterns will be excluded from find, file, hash and stat collectors. As find tool is used to search for files and directories, the file name patterns below need to be compatible with -name option. Please check find man pages for more information.

Example:

```ini
exclude_name_pattern: ["/etc/passwd", "*.txt", "*.gz.*", "*.[Ll][Oo][Gg]"]
```

## exclude_file_system

File systems that will be excluded from find, file, hash and stat collectors. UAC will retrieve the list of existing mount-points (paths) and add them to the exclusion list automatically. The file system types that are supported depend on the target computer's running kernel.

Example:

```ini
exclude_file_system: [9p, afs, nfs, nfs4, rpc_pipefs, smbfs, sysfs]
```

## hash_algorithm

Hash algorithms will be used by the hash collector.

Accepted values: md5, sha1 and sha256

Example:

```ini
hash_algorithm: [md5, sha1]
```

## max_depth

The find tool is used by UAC to search for files and directories within a directory hierarchy. The -maxdepth parameter is used to limit the depth of the search within the directory tree. By default, when you run the find command without specifying -maxdepth, it searches recursively through all subdirectories of the specified directory.

When you specify the -maxdepth parameter followed by a number, it restricts the depth of the search to that number of levels below the starting point in the directory hierarchy. This option is used by all find-based UAC collectors such as find, file, hash and stat. Performance Consideration: Specifying a value here can improve performance by limiting the scope of the search.

Accepted values: a positive integer or 0 for disabling it (making UAC search recursively through all subdirectories of the specified directory).

Example:

```ini
max_depth: 10
```

## enable_find_mtime, enable_find_atime and enable_find_ctime

Limit data collection based on the date range provided. UAC uses find's -mtime, -atime and -ctime options to limit the data
collection based on the file/directory last accessed, last modified and last status changed dates.

Accepted values: true or false

Examples:

To collect only files in which data was last modified OR status last changed within the given date range, please set enable_find_mtime and enable_find_ctime to true and enable_find_atime to false.

```ini
enable_find_mtime: true
enable_find_atime: false
enable_find_ctime: true
```

To collect only files whose last status was changed within the date range, please set enable_find_ctime to true, and enable_find_atime and enable_find_mtime to false.

```ini
enable_find_mtime: false
enable_find_atime: false
enable_find_ctime: true
```
