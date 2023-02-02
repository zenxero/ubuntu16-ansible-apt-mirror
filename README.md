# Archival Purposes

# WARNING - This playbook is outdated.

This an archive of an old Ansible playbook circa 2017 or so. It only targets Ubuntu 16.04.

# Deploy Apt Repo

This repo contains an Ansible playbook and associated configurations to automatically build an apt repository mirror for Ubuntu clients. This playbook will only run against an Ubuntu 16.04 host.

## Table of Contents
  * [Requirements](#requirements)
  * [Usage](#usage)
  * [Repos](#repos)
  * [Web server symlinks](#symlinks)
  * [Cronjob](#cronjob)

<a name="requirements"></a>
## Requirements

  * [Ansible Control System Requirements](https://docs.ansible.com/ansible/latest/intro_installation.html#control-machine-requirements)
  * A system with the `ansible` and `git` packages installed.
  * The ability to SSH to the target system as root with an SSH key.
  * The target system that will be the apt repo server needs to be running the Ubuntu 16.04 Linux distribution.
  * The target system should have at least 250 GB of disk space available at the `/srv/` filesystem location for the package mirrors.

<a name="usage"></a>
## Usage

This is a standalone playbook, meaning that it includes its own `ansible.cfg` file and does not need a system-level config file.

To use this playbook:

1. On your "control" system with ansible and git installed, clone this git repo.

2. In the [hosts](hosts) file, add the hostname of the system that you want to be your apt mirror server.

3. Run the playbook with the following command:

```
ansible-playbook deploy-apt-mirror.yml
```

In a few minutes, you should have a working Ubuntu apt package repo mirror that you can point clients at to download packages.

After the playbook is finished, you can access the nginx web server by navigating to:

  * `http://your-hostname.example.com`

__NOTE__: Since mirroring the entire Ubuntu repo takes a while (it has to download over 200 GB of stuff), the last task in the playbook is a "Fire and Forget" repo sync task that starts this process. The playbook will end, but the repo will not be available for use until the sync is done. This can take up to an hour for the initial sync.

<a name="repos"></a>
## Repos

This playbook configures the following repos to be mirrored locally:

  * Ubuntu 16.04
  * Ubuntu 14.04
  * Google Chrome for Ubuntu
  * The git-core mainline git package repo
  * NVIDIA CUDA for Ubuntu 16.04

<a name="symlinks"></a>
## Web server symlinks

The local apt repo is stored in the `/srv/` directory, but symlinks are made for each repo into `/var/www/` for nginx to serve out.


<a name="cronjob"></a>
## Cronjob

This playbook sets up a cronjob located in `/root/bin` called `update-apt-mirror` that runs every night at 1 AM to update the local apt mirror.