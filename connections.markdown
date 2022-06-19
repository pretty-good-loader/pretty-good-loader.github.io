---
layout: page
title: Connections
permalink: /connections
nav_order: 15
---

# Connections
The PGL `connections.yml` file is where database destinations are configured. PGL allows for as many destinations as needed, and for each destination as many environments as is needed. 

For example a postgresql database could be configured as a destination, and multiple schemas could be used for different environments. The below snippet shows this under the top level `conns` dictionary.

```yaml{% raw %}
conns:
  database1:
    env1:
      db_type: postgres
      host: "{{ env_var('POSTGRES_DB_HOST') }}"
      user: "{{ env_var('POSTGRES_DB_USER') }}"
      password: "{{ env_var('POSTGRES_DB_PASSWORD') }}"
      port: 5432
      database: d9k5gv2j90sgs8
      destination_schema: pgl_dev
    env2:
      db_type: postgres
      host: "{{ env_var('POSTGRES_DB_HOST') }}"
      user: "{{ env_var('POSTGRES_DB_USER') }}"
      password: "{{ env_var('POSTGRES_DB_PASSWORD') }}"
      port: 5432
      database: d9k5gv2j90sgs8
      destination_schema: pgl_prod{% endraw %}
```

### dbt_type
`dbt_type` can be configured as one of the below options.
- `postges`
- `snowflake`
- `mariadb`
<br /><br /><br /><br />
[Next up: Project  ->](/project)