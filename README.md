# ansible-cfg

This repository records guest-internal configuration for non-NixOS Linux machines. It complements `pve-cfg`, which records Proxmox VM/LXC shells, and `nixos-cfg`, which rebuilds the NixOS VM.

## Current Scope

The 2026-07-07 inventory includes every LXC on the PVE host. Containers that were stopped were started one at a time, inspected with read-only commands, and stopped back to their original state.

Recorded for each LXC under `snapshots/lxc/<ctid>-<name>/`:

- PVE config copy and collection metadata.
- OS release, hostname, kernel, IP addresses, routes and resolver.
- Manual packages and full package list.
- Running services, enabled services, timers and systemd unit file paths.
- Listening sockets.
- Filesystem, mounts, fstab, cron and selected config path inventory.
- Docker containers, images and compose list when Docker is installed.
- Sanitized OpenWrt UCI output for the OpenWrt LXC.

Excluded from raw capture: private keys and token/password-like key-value fields are redacted. Full disk state and application data are still a PBS/image-backup concern.

## Inventory Groups

- `lxc_all`: all LXC snapshots.
- `lxc_running` / `lxc_stopped`: current state after inventory.
- `debian_running`: safe default Ansible target for currently running Debian LXCs.
- `debian_stopped`: Debian LXCs that have snapshots but are not currently running.
- `docker_lxc`, `bots`, `db_lxc`: convenience groups based on observed services/names.
- `vm_linux`: Linux VMs discovered outside the LXC pass, currently DockerHost.

## Usage

The current playbook is intentionally non-destructive and only targets `debian_running` by default. Convert snapshots into roles gradually, then run targeted checks such as:

```bash
ansible-inventory --list
ansible-playbook playbooks/site.yml --check --limit lxc_101_nginx
```
