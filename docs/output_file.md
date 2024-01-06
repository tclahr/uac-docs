# Output file format

A compressed archive file is created in the destination directory when the collection is finished. The default output file name will be (without extension): ```uac-%hostname%-%os%-%timestamp%```

The compressed archive file is hashed and the value is stored in the acquisition log file.

Each artifact will be stored in its directory. Files collected by [file](collectors.md#file-collector) collector will be placed into the ```[root]``` directory.

![screenshot](img/output_file_01.png)
