Role Name
=========

A role to download, install and run Blockchain Node software for Nerd United Brands.

The node software can be downloaded from a provided URL or uploaded from the `files` directory next to the playbook calling this role. For large numbers of hosts, this can speed things up.

The default values will run one (1) node on each host. There is a maximum or 750 nodes per host supported by this role, because each node is run under a unique user _service_ account and past a certain number you run into problems getting a UID or GID that isn't already taken by the system. This is a good limit to make sure you stay under that value.

This role can be installed using a `requirements` file and the following command.

`requirements.yml`
```yaml
---
roles:
  - name: blockchain_node
    src: https://github.com/cloudcodger/ansible_blockchain_node
```

```bash
ansible-galaxy role install -r requirements.yml
```

Requirements
------------

A computer running a Linux system capable of running the software.

Role Variables
--------------

These are **required** to be set and have no default value. Either a Firebase ID or both email and password. If all three are provided, the Firebase ID will be used to configure the node, overriding the email and password.

- `blockchain_node_company`
  - The company or brand for the blockchain.
- `blockchain_node_email`
  - The User Email to authenticate the node.
- `blockchain_node_firebase_id`
  - The Firebase ID for the User.
- `blockchain_node_password`
  - The User Password to authenticate the node.

One of these two is **Required** to be set and have no default value. The `blockchain_node_file` will be used if both are provided.

- `blockchain_node_file`
  - The file name to install on each host (must exist).
- `blockchain_node_url`
  - The full URL of the binary file to install on each host.

These have reasonable default values but can be changed when needed.

- `blockchain_node_bypass_start`
  - Set to `true` in order to bypass the enable and start of the services.
  - Default: `false`.
- `blockchain_node_config_pause`
  - The number of seconds to Pause between configuring nodes.
  - Default: `8`.
- `blockchain_node_count`
  - The number of nodes to run on the host.
  - Default: `1`.
  - Max value: `750`.
- `blockchain_node_cleanup`
  - Clean up or remove any nodes from `blockchain_node_count` to this value.
  - Default: `1`.
- `blockchain_node_local_keys_dir`
  - The directory on the control host (`localhost`) to contain config key files.
  - This directory should not be included in source control repositories.
  - Default: `files/keys`.
- `blockchain_node_name`
  - The name used for the binary and the "dot" file in the home directories.
  - Default: `"{{ blockchain_node_company }}-node"`.
- `blockchain_node_service_log_level`
  - The log level for the nodes.
  - Default `debug`.
- `blockchain_node_service_reboot_on_failure`
  - When true, adds `FailureAction=reboot` to the service.
  - Default `true`.
- `blockchain_node_service_restart_sec`
  - Number of seconds between restarting the service.
  - Default `90`.
- `blockchain_node_service_start_burst`
  - Number of times to restart the service within the limit interval before setting the service to `failed`.
  - Default `10`.
 - `blockchain_node_service_start_limit_interval`
  - The interval (in seconds) in which the burst restarts will cause the the service to become `failed`.
  - Default `1000`.
- `blockchain_node_user`
  - The Linux User prefix where the nodes will run.
  - Default `blockchain`.

Dependencies
------------

None.

Example Playbook
----------------

This example is for a blockchain node for the Element brand.

```yaml
---
- name: Configure a blockchain node for 'hyper' with ficticious email address.
  become: true
  hosts: hyper

  roles:
    - role: blockchain_node
      blockchain_node_company: hyper
      blockchain_node_email: node_runner@nerdunited.com
      blockchain_node_password: "{{ lookup('file', '~/.secrets/hyper.passwd') }}"
      blockchain_node_url: "https://download.nerdunited.com/node-binaries/v2.6.5/hyper-v2.6.5_linux-amd64"

- name: Configure a blockchain node for 'hyper' with ficticious email address, using a file.
  become: true
  hosts: hyper

  roles:
    - role: blockchain_node
      blockchain_node_company: hyper
      blockchain_node_email: node_runner@nerdunited.com
      blockchain_node_file: "hyper-v2.6.5_linux-amd64"
      blockchain_node_password: "{{ lookup('file', '~/.secrets/hyper.passwd') }}"
```

Example LXC Container Setup
---------------------------

Assuming the use of Proxmox that has been configured to use the `cloudcodger.proxmox_client.lxc` role, these two playbooks can be used together to create a container and install a `hyper` blockchain node inside it (using default values in the role). Because the container is only using the `root` user, it will be dedicated to only run this node software and nothing else. The `become: true` is not needed. See the `cloudcodger.ubuntu.initial_apt_update` role for details on what it does.

```yaml
---
- name: Blockchain Node Server Container.
  connection: local
  gather_facts: false
  hosts: localhost

  roles:
    - role: cloudcodger.proxmox_client.lxc
      lxc_pm_api_host: "192.168.1.251"
      lxc_pm_api_user: "devops@pve"
      lxc_pm_api_token_file: devops-pve-ansible.token

      lxc_cores: 2
      lxc_disk_size: 14
      lxc_nameservers: ["1.1.1.1", "8.8.4.4"]
      lxc_network_gw: "192.168.1.1"
      lxc_pm_host: pve1
      lxc_searchdomains: [example.com]
      lxc_sshkeys: | # These are not real keys. Use correct keys or be locked out of the container.
        - ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIN+K0KwgKOeeyW519YavKQodVgwWcRUIucZkOfplsKMl devops-guy-mbp
        - ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIJk/hSmxsyznAhhsD5cO9wcTgOs+/xz09kZ5woSUUQAY devops-gal-mbp
      lxc_tags: ["node", "ubuntu_22.04"]
      lxc_cts:
        - name: node51
          ip: "192.168.1.51/24"
          pm_host: pve1
          tags: ["crypto"]
          vmid: "151"
```

```yaml
---
- name: Configure a 'hyper' blockchain node inside above container tagged with 'crypto'.
  hosts: crypto

  roles:
    - role: cloudcodger.ubuntu.initial_apt_update
    - role: blockchain_node
      blockchain_node_company: hyper
      blockchain_node_email: node_runner@nerdunited.com
      blockchain_node_password: "{{ lookup('file', '~/.secrets/hyper.passwd') }}"
      blockchain_node_url: "https://download.nerdunited.com/node-binaries/v2.6.5/hyper-v2.6.5_linux-amd64"
```
