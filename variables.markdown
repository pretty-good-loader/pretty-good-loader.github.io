---
layout: page
title: Variables
permalink: /variables
nav_order: 10
---

# Variables
It is strongly reccommended that when configuring connections, you should use variables for information that does not belong in source control. This would minimally be passwords, but it is suggested that hostnames, users, and passwords all are configured with variables. 

## Environment Variables
Environment variables are the simplest way to achieve variable substitution for sensitive values. When developing locally, you can create a file named `.env` in the root of your PGL project. This below is an example of what that file could look like.
```
export POSTGRES_DB_HOST=example.your_db_host.com
export POSTGRES_DB_USER=your_user
export POSTGRES_DB_PASSWORD=your_password
```
Throughout PGL config, you can use environment variables with the following syntax `{% raw %}{{ env_var('ENV_VAR_NAME') }}{% endraw %}`.
<br /><br /><br /><br />
[Next up: Connections  ->](/connections)