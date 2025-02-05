# Change log

# version 4.0.0

## Breaking changes
- Renamed `service` related variables.

## Other changes
- Update to check if a configuration `key` file exists, and upload it.
- Download all the configuration `key` files.
  - Can be disabled with `blockchain_node_save_keys: false`.
- Added a variable to bypass starting the node services.
- Added a variable to change the name of the binary and (dot) files.
  - For instances where the brand name is used instead of the name of the blockchain.
- Created `add_nodes.yml` task file to change the order of task execution.

# version 3.0.0

## Breaking changes
- Renamed the role to `blockchain_node`.
- Also means the prefix for all variable names changed.

# version 2.1.1

- Added `sudo` because Debian doesn't include it like Ubuntu (where initial testing was done).

# version 2.1.0

- Added the ability to provide a Firebase ID instead of email and password for authentication.

# version 2.0.0

## Breaking changes
- Changed `nerd_blockchain_node_id` to `nerd_blockchain_node_email` (breaking change).
- The `nerd_blockchain_node_company` and `nerd_blockchain_node_email` are now required.
- The `nerd_blockchain_node_password` must now be provided. A file from where to read it is no longer configurable and needs to be done in the playbook that calls this role.
- `nerd_blockchain_node_file` or `nerd_blockchain_node_url` must now be provided. The `prefix` and `version` are no longer supported to compile a URL.

## New features

- Added the ability to run up to 750 nodes on one system.
- Added `user` account under which to run the nodes.
- Added assertions to make sure required values are provided.

# version 1.2.0

- Added `Environment` to systemd service template.

# version 1.1.1

- Changed `FailureAction` to `StartLimitAction`.

# version 1.1.0

- Added settings to change the restart behaviour of the service.

# version 1.0.0

- The initial release
