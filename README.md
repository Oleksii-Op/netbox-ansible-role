NetBox Ansible Role
====================

This project provides an Ansible role, `netbox`, and an example playbook to deploy the NetBox IPAM/DCIM application on a Debian 13 host. It automates installation of system packages, PostgreSQL, Redis, NetBox itself, systemd services, and an HTTPS-ready nginx frontend using opinionated but configurable defaults.

Features
--------
- Installs required packages (PostgreSQL 17, Redis, nginx, Python tooling, build dependencies, Git).
- Clones and installs NetBox (default version: `v4.5.4`) into `/opt/netbox` using the provided upgrade script and virtual environment.
- Configures NetBox via `configuration.py.j2` with options for authentication, CORS, logging, plugins, metrics, sessions, and more (see `netbox/defaults/configuration.yml`).
- Sets up PostgreSQL roles, database, and privileges based on the `postgres.yaml` variables.
- Configures Redis for tasks and caching (`redis.yaml`).
- Deploys Gunicorn configuration and systemd units for `netbox` and `netbox-rq`.
- Configures nginx as an HTTPS reverse proxy, including TLS certificate paths and static file serving.

Requirements
------------
- Tested on **Debian GNU/Linux 13 (trixie)**.
- Controller side: **Python ≥ 3.12** and **Ansible ≥ 13.4.0**.
- Target host with systemd, PostgreSQL, Redis, and nginx support (the role installs these packages if needed).

Important Variables
-------------------
Several variables **must be provided** (for example via inventory, group/host vars, or extra vars):
- Database (edit `netbox/defaults/postgres.yaml`): `netbox_db_name`, `netbox_db_user`, `netbox_db_user_password`, `netbox_sql_db_host`, `netbox_sql_db_port`.
- NetBox application security and access (edit `netbox/defaults/superuser.yaml`):
  - `netbox_secret_key`
  - `netbox_api_token_peppers`
  - `netbox_superuser.username`, `netbox_superuser.password`, `netbox_superuser.email`
- TLS and nginx (edit `netbox/defaults/nginx.yaml`):
  - TLS certificate and key files deployed to `/etc/nginx/ssl` (paths configurable via `netbox_ssl_cert` and `netbox_ssl_key`).
- Email settings (edit `netbox/defaults/email_server.yaml`): `netbox_email_*` variables.

Reasonable defaults for application behavior (allowed hosts, authentication behavior, CORS, logging, plugins, time zone, etc.) are defined in `netbox/defaults/configuration.yml` and can be overridden as needed.

Usage
-----
An example playbook is provided in `playbook.yaml`, which:
- Updates the apt cache.
- Loads default variable files from `netbox/defaults/`.
- Applies the `netbox` role to all hosts.

Run it with:

```bash
ansible-playbook -i your_inventory playbook.yaml
```

