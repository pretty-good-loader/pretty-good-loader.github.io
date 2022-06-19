---
layout: page
title: Project
permalink: /project
nav_order: 20
---

# Project
A PGL project as stated in the `project.yml` file in the root of PGL cofig, defines configurations for a collection of pipelines. These pipelines do not have to be related, they could be an entire oragnization's collection of pipelines. The pipelines.yml file has both global settings and pipeline defaults that should be set for a project.

## Global Configurations
Global settings are at the root level of the project file, and remain constant across all Pipelines. Below are all the global settings of a pipeline file

```
secret_mode: 
file_storage_mode:
file_storage_bucket:
file_storage_directory:
```

### secret_mode
`secret_mode` only has the one below option, but more are to be added soon. If `secret_mode` is not defined, PGL will pick a default of `environment_variable`. 
- environment_variable

### file_storage_mode
`file_storage_mode` allows you to pick where the files downloaded from your orginal source are stored. If `file_storage_mode` is not defined, PGL will pick a default of `s3`.
- s3
- azure_blob
- local

### file_storage_bucket
`file_storage_bucket` allows you to pick a specific bucket in cloud storage to store the original files downloaded from source. Note that this is an optional configuration, and does not apply if `file_storage_mode` is set to `local`. If `file_storage_bucket` is not defined, PGL will pick a default of `file-storage-bucket` for the bucket name.

### file_storage_directory
`file_storage_directory` allows you to pick a directory to store files. If you are using cloud storage such as S3 or Azure Blob, this will be a prefix in the bucket you specified. If you are using local file storage, this will be a directory locally on the machine you are running PGL on.

## Pipeline Configurations
The pipelines section allows for setting defaults across all or a subset of pipelines. To set a default across all pipelines, put a configuration at the root of the pipelines dictionary. That configuration needs to have a `+` character before the configuration is stated, which implies it is a configuration that applies to downstream objects. For example if you wanted to say that all sources should default to have a source type of ftp, you could put a configuration of `+source_type: ftp` under the pipelines dictionary. 

Additionally, you can set defaults for a subset of pipelines based on their folder structure in the pipelines directory. See below for an example of this behavior. Any [settings available at the pipeline level](/pipelines) are eligible to be defaulted in the project.yml file under the pipelines dictionary.


```yaml{% raw %}
secret_mode: environment_variable
file_storage_mode: local
file_storage_directory: extracted_files

# global pipeline configuration
pipelines:
  # defaults - can be overwriten at pipeline level
  +source_type: ftp

  weather:
    ncdc:
      +host: ftp.ncei.noaa.gov"{% endraw %}
```
<br /><br /><br />[Next up: Pipelines  ->](/pipelines)