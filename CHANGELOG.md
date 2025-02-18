# Change log

# version 4.0.0

## Breaking changes
- Changed `blockchain_node_count` to `blockchain_node_last`.
- Changed names of `service` related variables.

## Non-breaking changes
- Added ability to store/use the configure generated key files (located under `files`) and upload an existing one instead of generating a new one.
- Added `blockchain_node_bypass_start`.
- Added `blockchain_node_first`.
- Moved adding nodes to an included tasks to change processing order.
- Changed to using `loop` instead of `with_sequence`.

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
