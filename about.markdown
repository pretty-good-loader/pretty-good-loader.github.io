---
layout: page
title: About
permalink: /
nav_order: 5
---

# What is PGL?

Pretty Good Loader (PGL) is a solution for easily moving data from file sources into database destinations. PGL solves for such complexities as change tracking, testing, and reusability that many other data loader solutions do not.

## What is a PGL project?

A PGL project is a collection of pipelines for loading data from file sources to database destinations. These pipelines can be related or unrelated. Each pipeline is independent and organization around your pipelines is completely up to you.

## What is a PGL pipeline?
A PGL pipeline is a singular source and destination for data to be extracted from and loaded to. For example, a file to be picked up off an SFTP, and loaded into a PostgreSQL database.

## How do I use PGL?
PGL is a command-line interface used to execute any or all pipelines in a project. PGL itself is _not_ a scheduler. PGL's purpose is to facilitate moving data from outside a database, into a database with as little configuration as possible. You can execute a PGL pipeline yourself in an adhoc fashion as a single command, or on a regular basis with a job scheduler of your choice.

## Why would I use PGL?
Although not incredibly complicated, the amount of code it takes to stand up a reliable data pipeline is non-trivial. Data Engineers frequently spend time coding modules to fetch files from an SFTP, unzip those files, standardize those files into something a database can utilize, and load that data into tables. And even after those modules are created, they still need to be strung together in a way that is repeatable for many files and diverse file types. PGL abstracts this work so Data Engineers can focus on what needs to happen once the data is in the database, and more quickly start to provide value for downstream stakeholders.

Additionally, PGL is not a front-end only tool. PGL is controlled through configuration documents in the form of YAML. Pipelines made with PGL are easy to source control, and support multi-environment deployments through variables. This allows for several workflows unique to PGL, not found in UI point and click style interfaces. First, Data Engineers can have testable data flows without having to define the same pipeline twice by having both production and non-production source _and_ destination connection variables. Second, Data Engineers can easily load data from multiple production sources to multiple production destinations with a singular pipeline just by defining separate environments. Lastly, pipelines can be easily migrated from one destination to another by editing the destination and not changing any other part of the pipeline definition.

## What do I need to know to get started?
Before getting started make sure you are familiar with contributing to a Git Repo and understand environment variables and secret management. These are all important to have a good handle on because data extraction and loading involves sensetive credentials. Understanding cloud services is a considerable advantage as well, although pgl can run on local files if that is not available.
<br /><br /><br /><br />
[Next up: Variables  ->](/variables)