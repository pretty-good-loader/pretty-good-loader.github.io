---
layout: page
title: none
permalink: /none
nav_order: 999
nav_exclude: true
---

## Do something with the below

A PGL project is composed of a profile.yml file containing project level configuration, a connections.yml containing configuration relevent to destination database connections and any number of modular "pipelines", short yaml snippets that define a source and destination for data to be extracted from and loaded to. These pipeliens can be executed via a CLI in a number of ways to make scheduling and executing jobs easy and intuitive.

Before getting started make sure you are familiar with contributing to a Git Repo and understand environment variables and secret management. These are all important to have a good handle on because data extraction and loading involves sensetive credentials. Understanding cloud services is a considerable advantage as well, although pgl can run on local files if that is not available.


## Project configuration

Project.yml files are used to set up project level configuration as well as to set some global pipeline congiurations if applicable.  

Top level configurations in a project.yml file for setting up a pgl ingestion platform are:  

`file_storage_mode`  
`file_storage_bucket`  
`file_storage_directory`  
`secret_mode`  

`file_storage_mode` defines the type of storage platform that is to be used for the sourcefile repository. It can be s3 to store all the files ingested in a specific s3 bucket or local to store all ingested files in an accessable file system. blob storage should be added.

`file_storage_bucket` defines the bucket that all the files that are ingested are stored in. Depending on the infrastructure setup you are working with, it could be a good idea to pass this in as an environment variable if using a different one for development vs. production.

`file_storage_directory` defines the parent directory in the `file_storage_bucket` to store the files. Files will be given a prefix denoting where they came from and what type of file they are, but sometimes we would like to define a parent directory and not use the entire bucket just for the `file_repository`.

`secret_mode` defines the method that secrets are stored in. Default is to store secrets in environment variables but this will be extended to use common secret stores.


## Pipeline Configuration

## pipeline

A pipeline represents a file that gets pulled in from some source and gets dumped to a specific table. They can send many versions of that file perhaps daily or monthly that all follow the same structure/format of the given source_file.

A pipeline definition can be organized into any file / directory in the `pipelines` directory of the git repo. Note that if a series of pipelines share configuration settings it can be set in the project.yml file to avoid having to duplicate configuration settings. See the example for how to go about that.

Configuration tags are still in flux but the required ones are below. Note that these can come from global settings in profile.yml so if a lot of sources share a `load_type`, it can be set in project.yml and the actual pipeline yml snippet does not need to specify the load type.

`source_type`  
`source_path`  
`format_definition`  
`destination_table`  
`source_regex_matches`  
`load_type`  
`delete_after_copy`  
`do_not_redownload`

`source_type` defines the type of source connection that the file lives on. s3, some local file storage system or an external sftp.

`source_path` defines what path in the source connection system to look for the file, whether it be an s3 prefix or a filepath.

`format_definition` defines the format for a given file. The value shold be a path within the `format_definitions` directory excluding the trailing `.yml`. More on the config of this file will come later.

`load_type` defines the method with which to load the file into the databse

`delete_after_copy` is a boolean that tells quafl if we want to delete the file on the source system after downloading it to our internal `sourcefile_repository`

`do_not_redownload` is a boolean that tells quafl to ignore file names that have already been downloaded. For example if we do not `delete_after_copy` we want to have `do_not_redownload` be `true` to avoid downloading duplicate files from the source system

`destination_table` is the table name that this file will get loaded to. This is optional. The table will be created using the `name` as the table name if not specified.

`file_regex_pattern` is a list of regex patterns that will be used to match filenames found in the directory to this particular sourcefile. for example `filename_\d{6}.txt` will match `filename_202101.txt` and `filename_202202.txt`. so that we could load a file every month when the filenames have the date in them.

## format_definitions

A `format_definition` is a configuration that specifies a specific format that a file comes in as. It should incldue the type of file (xlsx, delimited, etc.) as well as the schema (column names) and some additional optional params specifying things like "delimiter" or "quotechar" (quoting character)

Each source_file is linked to a format_definition. These format_definitions can be custom to a source_file or can be some industry standard format that can be shared between different source_files.

Specific configurations are still tbd

## Env Vars and Secrets

Configuration files are made in yaml with the ability to pass in secrets and environment variables for most settings via jinja templating

`"{{ env_var('ENV_VAR_NAME') }}"`
`"{{ secret('SECRET_NAME') }}"`
or if the secret is a json and we want a key from it like aws secret manager does
`"{{ secret('SECRET_NAME', 'secret_key') }}"`

values can also be passed in from the `profile.yml` in the `~/.pgl/` directory. with

`"{{ profile('VAR_NAME') }}"`


# Example

## profile.yml
```
# this means that all secrets are stored as environment variables
secret_mode: environment_variable
sourcefile_repository_mode: s3
# This comes from an environment variable
sourcefile_bucket: env::SOURCEFILE_REPO_BUCKET
# This comes from a secret
sourcefile_download_directory: secret::SOURCEFILE_REPO_DIRECTORY

# pipeline configuration
# pipeline yaml files are placed in any given directory in the pipeline directory
# they can be run at a directory level or file level

# the leading + means that we will add this tag to everything in the directory
# note that these tags can be overriden in the actual pipelines
pipelines:
  # defaults
  +do_not_redownload: false
  +delete_after_copy: false
  +load_type: "python_native"

  # use copy into for all weather pipelines
  # note that things specified here will overwrite the above configuration tags
  # so all the weather directory pipelines will default to "copy into" where as all
  # the other pipeliens will default to "python native"
  weather:
    +load_type: "copy_into"
    +source_type: "ftp"
```

## pipeline

The name of the pipeline.yml file can be any valid filename and can hold however many pipelines as you would like. It is mainly an organizational preference. In the example they are grouped by cadence so it's easy to see all the daily and all the monthly pipelines together.

Pipelines are placed as a list under the `pipelines` tag. Below are 2 pipelines. Note that per the above project.yml, we are defaulting `load_type` and `source_type` so they dont need to be configured here. You can see that the "storm_events" has a `load_type` tag of `python_native` which means it will override the defaults set at the `profile.yml` file

```
pipelines:
  - name: storm_events
    description: >
      National Centers for Environmental Information Storm data - events
    format_definition: ncdc/storm_details

    # overriding the global settings
    load_type: python_native

    # already set at profile.yml
    # source_type: ftp

    host: ftp.ncei.noaa.gov
    source_path: /pub/data/swdi/stormevents/csvfiles/
    destination_table: src_storm_events
    file_regex_patterns:
      - StormEvents_details-ftp_v1.0_d1976_c20210803.csv.gz
  - name: storm_fatalities
    description: >
      National Centers for Environmental Information Storm data - fatalities
    format_definition: ncdc/storm_fatalities

    # already set at profile.yml
    # load_type: copy_into
    # source_type: ftp

    host: ftp.ncei.noaa.gov
    source_path: /pub/data/swdi/stormevents/csvfiles/
    destination_table: src_storm_fatalities
    file_regex_patterns:
      - StormEvents_fatalities-ftp_v1.0_d2011_c20220107.csv.gz
```

## Execution

The below 4 commands can be used to kick off pipeline ingestion executions

`pgl run` runs all pipelines  
`pgl run --pipelines weather` runs weather pipelines  
`pgl run --pipelines weather.ncdc_daily` runs daily weather pipelines  
`pgl run --pipelines weather.ndcc_daily.storm_events` runs only the storm event pipeline  
