# EWC Ansible Role EUMETSAT S3 Mount

This repository contains a configuration template 
(i.e. an [Ansible Role](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_reuse_roles.html)) 
to customize your environment in the
[European Weather Cloud (EWC)](https://europeanweather.cloud/).

The template is designed to provide:
* A secure, idempotent, and production-grade way to mount read-only EUMETSAT data buckets as `FUSE` filesystems on EWC compute instances running RockyLinux 9 or Ubuntu 24.

## Copyright and License
Copyright © EUMETSAT 2026.

The provided code and instructions are licensed under the [MIT license](./LICENSE).
They are intended to automate the setup of an environment that includes 
third-party software components.
The usage and distribution terms of the resulting environment are 
subject to the individual licenses of those third-party libraries.

Users are responsible for reviewing and complying with the licenses of
all third-party components included in the environment.

Contact [EUMETSAT](http://www.eumetsat.int) for details on the usage and distribution terms.

## Features

- Self-service access to EUMETSAT data publicly available within the EWC (no credentials required)
- User-defined mounting location and configurable idle timeout
- Strict file permissions for mounted directories
- Boot-safe design; does not block boot on `S3` outage
- Advanced per-bucket cache/refresh tuning (via tweaking of included defaults)

## Usage

The step-by-step described below assume your local file system follows the example structure below, with `ewc-ansible-role-eumetsat-s3-mount` being a clone of this repository:

```
.
├── roles
│   └── ewc-ansible-role-eumetsat-s3-mount
├── inventory.yml
└── playbook.yml
```

### 1. Specify the target host and SSH credentials
Create an inventory file to specify address/credentials that Ansible should use
to reach the virtual machine you wish to configure:
```yaml
# inventory.yml
---
ewcloud:
  hosts:
    ewc_host:
      ansible_python_interpreter: /usr/bin/python3
      ansible_host: <add the IPV4 address of the target host>
      ansible_ssh_private_key_file: <add the path to local SSH private key file>
      ansible_user: <add the username which owns the SSH private key >
```

### 2. Customize the template

Edit input values for the template [variables](./vars/main.yml) as needed (see
[Inputs](#inputs) section for details).
Then, proceed to create an Ansible Playbook file to load your customizations: 

```yaml
# playbook.yml
---
- name: Mount EUMETSAT S3 buckets
  hosts: ewc_host
  become: true
  become_user: root
  become_method: ansible.builtin.sudo

  roles:
    - ewc-ansible-role-eumetsat-s3-mount
```

### 3. Apply the template


You can apply changes on the target host by running:
```bash
ansible-playbook -i inventory.yml playbook.yml
```

## Inputs

| Name |  Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| allow_other | Allow non-root access to the mounted directories. Only `yes` will be accepted to approve. Example: `yes` | `string`| n/a | yes |
| vfs_cache_mode | Cache mode. Setting as `minimal` is recommended for read-only EO data. Example: `minimal` | `string` | n/a | yes |
| vfs_cache_max_size | Max cache size. Example: `512Mi` | `string` | n/a | yes |


## SW Bill of Materials (SBoM)

Third-party components used in the resulting environment.

### RockyLinux Environment

The following components will be included in the resulting environment:

| Component | Version | License | Home URL |
|------|---------|---------|--------------|
| curl | 7.76 | MIT | https://curl.se/ |
| unzip | 6.0 | BSD | http://www.info-zip.org/UnZip.html |
| fuse3 | 3.10  | GPL+ | http://fuse.sf.net |
| fuse-overlayfs | 1.16 | GPLv3+ | https://github.com/containers/fuse-overlayfs |
| rclone | 1.73 | MIT |	https://github.com/rclone/rclone |

### Ubuntu Environment

The following components will be included in the resulting environment:

| Component | Version | License | Home URL |
|------|---------|---------|--------------|
| curl | 8.5 | MIT | https://curl.se/  |
| unzip | 6.0  | BSD | http://www.info-zip.org/UnZip.html |
| fuse3 | 3.14  | GPLv2+ | https://github.com/libfuse/libfuse/wiki |
| fuse-overlayfs | 1.13 | GPLv3+ | https://github.com/containers/fuse-overlayfs  |
| rclone | 1.73 | MIT |	https://github.com/rclone/rclone |

## Changelog
All notable changes (i.e. fixes, features and breaking changes) are documented 
in the [CHANGELOG.md](./CHANGELOG.md).


## Contributing

Thanks for taking the time to join our community and start contributing!
Please make sure to:
* Familiarize yourself with our [Code of Conduct](./CODE_OF_CONDUCT.md) before 
contributing.
* See [CONTRIBUTING.md](./CONTRIBUTING.md) for instructions on how to request 
or submit changes.

## Authors

[European Weather Cloud](http://support.europeanweather.cloud/) 
<[support@europeanweather.cloud](mailto:support@europeanweather.cloud)>
