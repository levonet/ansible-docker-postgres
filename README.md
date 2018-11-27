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
- `docker_postgres__log_driver` (default: json-file): Specify the logging driver.
- `docker_postgres__log_options` (optional): Dictionary of options specific to the chosen log_driver. See [Configure logging drivers](https://docs.docker.com/engine/admin/logging/overview/) for details.
- `docker_postgres__remove_existing_container` (default: yes)
- `docker_postgres__pull_image` (default: yes)

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

License
-------

[MIT](https://opensource.org/licenses/MIT)

Author Information
------------------

This role was created by [Pavlo Bashynskyi](https://github.com/levonet)
