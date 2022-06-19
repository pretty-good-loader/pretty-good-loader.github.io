---
layout: page
title: Pipelines
permalink: /pipelines
nav_order: 25
---

# Pipelines
Pipelines are the core function of PGL. A PGL pipeline is defined as a single source file loading data into as single destination table.

## Configuration options

### conn
Destination database for loading file data into. Value for this configuration should be a database connection defined in the [connections file](/connections)

### do_not_redownload
This is a boolean configuration which defaults to `fase` if not specified. When `true`, specifies NOT to copy and load a file from a source connection if that file has been received before (determined by name of file).

### delete_after_copy
This is a boolean configuration which defaults to `false` if not specified. When `true`, removes file from the source connection after copying to the file store.

### load_type
The technical method used for loading data into the database.
- `python_native` -
  <br />This load type essentially creates line by line insert statements for loading data into the database. This is the most inefficient method of loading data, however it is the most cross compatible and is useful when other options are not possible, such as when a cloud storage connection is not available.
- `copy_into_local` -
  <br />This load type runs a copy into statement from cloud storage into a database. This is the most efficient method for loading data
- `psql` - 
  <br / >Fill this in later.

### source_type
- `ftp`
- `s3`
- `azure_blob`
- `local`
- `internal_compressed_pipeline`

### source_path
Path on the source where files can be found.

### host
This configuration only applies if ftp is the chosen `source_type`. This is the address of the ftp server where source data will be downloaded from.

### user
This configuration only applies if ftp is the chosen `source_type`. This is the user for the ftp server where source data will be downloaded from.

### password
This configuration only applies if ftp is the chosen `source_type`. This is the password for the ftp server where source data will be downloaded from.

### name
This is the identifier for the pipeline that can be referenced for specific targeted loads, and in parent configurations for inheritance.

### description
This is space for more information used for documentation purposes.

### format_definition
Need a whole thing for this.

### file_regex_patterns
A list of regex expressions for determining if a file should be downloaded or not. If a file name matches a regex expression it will be downloaded, and if it does not, it wil be left on the source.

### destination_table
Name of the table in the destination database that data will be loaded into.
