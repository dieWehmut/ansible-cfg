# ansible-cfg

This repository records and will eventually manage guest-internal configuration for non-NixOS Linux machines. It complements `pve-cfg`, which records Proxmox VM/LXC shells, and `nixos-cfg`, which rebuilds the NixOS VM.

## Current Scope

Included now:

- Running Debian LXCs discovered through PVE on 2026-07-07.
- Debian DockerHost VM discovered through qemu guest agent.
- Read-only snapshots of manual packages, running services, listening ports and Docker containers where available.

Excluded now:

- NixOS VM: managed by `nixos-cfg`.
- OpenWrt LXC: network configs are saved in `pve-cfg/openwrt/`.
- DSM VM: restore from image/PBS or DSM-native backup.
- Stopped LXCs: only their PVE shell config is recorded in `pve-cfg` until they are started and inspected.

## Layout

- `inventory/hosts.yml`: Ansible inventory generated from observed IPs.
- `inventory/host_vars/*.yml`: PVE metadata, network placement and snapshot pointers per host.
- `snapshots/<host>/`: read-only package/service/port snapshots.
- `playbooks/site.yml`: conservative entrypoint.
- `roles/`: placeholders for future idempotent roles.

## Usage

The current playbook is intentionally non-destructive. Convert snapshots into roles gradually, then run targeted checks such as:

```bash
ansible-inventory --list
ansible-playbook playbooks/site.yml --check --limit nginx
```
