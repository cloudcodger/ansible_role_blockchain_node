Role Name
=========

A role to download and install Blockchain Node software for Nerd United Brands.

This role can be installed using a `requirements` file and the following command.

`requirements.yml`
```yaml
---
roles:
  - name: nerd_blockchain_node
    src: https://github.com/cloudcodger/ansible_nerd_blockchain_node
```

```bash
ansible-galaxy role install -r requirements.yml
```

Requirements
------------

A computer running a Linux system capable of running the software.
A file under the `nerd_blockchain_node_secrets_dir` directory that contains the password for the blockchain node account.

Role Variables
--------------

Either set these or make sure they are correct.
`nerd_blockchain_node_company`: The company or brand for the blockchain (default `hyper`).
`nerd_blockchain_node_url_prefix`: The URL prefix from which to download the binary
  (default `https://download.nerdunited.com/node-binaries` used for `hyper`).
`nerd_blockchain_node_user_id`: The User ID to authenticate the node (NO default value).

These have reasonable default values but can be changed when needed.
`nerd_blockchain_node_arch`: The host architecture (default `amd64`).
`nerd_blockchain_node_file`: The file name to download
  (the default is constructed from the company, version and arch values).
`nerd_blockchain_node_reboot_on_failure`: When true, adds `FailureAction=reboot` to the service (default `true`).
`nerd_blockchain_node_restart_sec`: Number of seconds between restarting the service (default `90`).
`nerd_blockchain_node_secrets_dir`: The directory holding the password file (default `~/.secrets`).
`nerd_blockchain_node_secret_file`: The full path to file inside the secrets directory holding the password
  (default is `"{{ nerd_blockchain_node_secrets_dir }}/{{ nerd_blockchain_node_company }}.passwd"` ).
`nerd_blockchain_node_start_burst`: Number of times to restart the service within the limit interval
  before setting the service to `failed` (default `10`).
`nerd_blockchain_node_start_limit_interval`: The interval (in seconds) in which the burst restarts will cause the
  the service to become `failed` (default `1000`).
`nerd_blockchain_node_url`: The full URL of the binary file to download
  (the default is constructed from the url prefix, version and node file vaules).
`nerd_blockchain_node_version`: The version of the node software to download (default `v2.6.1-b`).

Dependencies
------------

None.

Example Playbook
----------------

This example is for a blockchain node for the Element brand.

```yaml
---
- name: Configure a blockchain node for 'element' with ficticious email address.
  become: true
  hosts: element

  roles:
    - role: nerd_blockchain_node
      nerd_blockchain_node_company: element
      nerd_blockchain_node_url_prefix: "https://download.elementunited.com/node-binaries"
      nerd_blockchain_node_user_id: node_runner@nerdunited.com
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
    - role: nerd_blockchain_node
      nerd_blockchain_node_user_id: node_runner@nerdunited.com
```
