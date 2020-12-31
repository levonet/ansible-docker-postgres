PostgreSQL Role
===============
[![Build Status](https://travis-ci.org/levonet/ansible-docker-postgres.svg?branch=master)](https://travis-ci.org/levonet/ansible-docker-postgres)

Ansible role to manage and run the PostgreSQL docker container.
[More information](https://hub.docker.com/r/library/postgres/) about using docker image.

Role Variables
--------------

- `docker_postgres__name` (default: postgres): This name used in name of home folder path and in name of container.
- `docker_postgres__image` (default: postgres:latest)
- `docker_postgres__port` (default: 5432)
- `docker_postgres__superuser_name` (default: postgres): This variable will create the specified user with superuser power and a database with the same name.
- `docker_postgres__superuser_password` (default: postgres): This environment variable sets the superuser password for PostgreSQL.
- `docker_postgres__file_volumes` (optional): Mount files into docker.
- `docker_postgres__env` (optional): Environment Variables.
- `docker_postgres__network_mode` (default: host): Connect the container to a network.
- `docker_postgres__networks` (default: []): List of networks the container belongs to.
- `docker_postgres__purge_networks`(default: no): Enforce the removal of the default network (and all other networks not explicitly mentioned in networks).
- `docker_postgres__log_driver` (default: json-file): Specify the logging driver.
- `docker_postgres__log_options` (optional): Dictionary of options specific to the chosen log_driver. See [Configure logging drivers](https://docs.docker.com/engine/admin/logging/overview/) for details.
- `docker_postgres__remove_existing_home_dir` (default: no): Remove home directory of instance with data and old configs before setup postgres. It's needed for CI.
- `docker_postgres__remove_existing_container` (default: yes)
- `docker_postgres__pull_image` (default: yes)
- `docker_postgres__wait_for_start` (default: no): Waiting for startup postgres container.
- `docker_postgres__pg_hba_conf`: This variable contains content of `pg_hba.conf`.
- `docker_postgres__postgresql_conf`: This variable contains content of `postgresql.conf`.

Replication settings:

- `docker_postgres__slave` (default: no): Make a base backup from master and run postgres container as slave.
- `docker_postgres__master_host` (default: localhost): Host of master database.
- `docker_postgres__master_port` (default: 5432): Port of master database.
- `docker_postgres__recovery_conf`: This variable contains content of `recovery.conf`.

Dependencies
------------

- `docker`

Example Playbook
----------------

```yaml
- hosts: all
  become: yes
  become_method: sudo
  roles:
  - role: levonet.docker_postgres
    docker_postgres__name: postgres-11
    docker_postgres__image: postgres:11.1-alpine
    docker_postgres__port: 5678
    docker_postgres__superuser_name: superuser
    docker_postgres__superuser_password: supersecret
    docker_postgres__env:
      POSTGRES_DB: my_db
    docker_postgres__log_driver: syslog
    docker_postgres__log_options:
      syslog-facility: local0
      tag: "{{ docker_postgres__name }}"
```

An example of running a PostgreSQL cluster on a single server
------------------------------------------------------------

Change the IP address `10.0.0.1` to a valid server address.

```yaml
- hosts: all
  become: yes
  become_method: sudo
  vars:
    docker_postgres__superuser_password: supersecret
    docker_postgres__master_host: 10.0.0.1
    docker_postgres__master_port: 5550
  pre_tasks:
  - set_fact:
      docker_postgres__postgresql_conf: |
        {{ docker_postgres__postgresql_conf }}
        max_wal_senders = 10
        wal_keep_segments = 64
        hot_standby = on
        synchronous_standby_names = 'ANY 1 (*)'
  roles:
  - role: levonet.docker_postgres
    docker_postgres__name: postgres-master
    docker_postgres__port: "{{ docker_postgres__master_port }}"
  - role: levonet.docker_postgres
    docker_postgres__name: postgres-salve-1
    docker_postgres__port: 5551
    docker_postgres__slave: yes
  - role: levonet.docker_postgres
    docker_postgres__name: postgres-salve-2
    docker_postgres__port: 5552
    docker_postgres__slave: yes
```

License
-------

[MIT](https://opensource.org/licenses/MIT)

Author Information
------------------

This role was created by [Pavlo Bashynskyi](https://github.com/levonet)
