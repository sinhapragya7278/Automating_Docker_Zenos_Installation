# Setup Docker and Zenoss on t5group Hosts

![YAML](https://img.shields.io/badge/language-YAML-blue.svg)

This Ansible playbook sets up Docker and Zenoss on hosts in the `t5group`.

## Playbook Details

### Playbook: `setup_docker_zenoss.yml`

This playbook performs the following tasks:
1. Installs prerequisite packages for Docker.
2. Configures the Docker repository.
3. Installs Docker.
4. Starts and enables the Docker service.
5. Adds the EPEL repository.
6. Adds the Zenoss repository.
7. Installs Zenoss.
8. Enables the Zenoss service.

### Playbook Content

```yaml
---
- name: Setup Docker and Zenoss on t5group hosts
  hosts: t5group
  become: yes
  tasks:
    - name: Install Docker prerequisite packages
      yum:
        name:
          - yum-utils
          - device-mapper-persistent-data
          - lvm2
        state: latest

    - name: Configure Docker repository
      get_url:
        url: https://download.docker.com/linux/centos/docker-ce.repo
        dest: /etc/yum.repos.d/docker-ce.repo
        mode: '0644'

    - name: Install Docker
      yum:
        name: docker-ce
        state: present

    - name: Start and enable Docker service
      service:
        name: docker
        state: started
        enabled: yes

    - name: Add EPEL repository
      yum_repository:
        name: epel
        description: EPEL YUM repo
        baseurl: http://download.fedoraproject.org/pub/epel/$releasever/$basearch/
        gpgcheck: yes
        enabled: yes

    - name: Add Zenoss repository
      yum_repository:
        name: zenoss
        description: Zenoss YUM repo
        baseurl: http://get.zenoss.io/yum/zenoss-repo-1-1.x86_64.rpm
        gpgcheck: no
        enabled: yes

    - name: Install Zenoss
      yum:
        name: zenoss-core-service
        state: present

    - name: Enable Zenoss service
      service:
        name: serviced
        state: started
        enabled: yes
