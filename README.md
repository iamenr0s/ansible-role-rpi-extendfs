[![Molecule](https://github.com/iamenr0s/ansible-role-rpi-extendfs/actions/workflows/molecule.yml/badge.svg)](https://github.com/iamenr0s/ansible-role-rpi-extendfs/actions/workflows/molecule.yml) [![Release](https://github.com/iamenr0s/ansible-role-rpi-extendfs/actions/workflows/release.yml/badge.svg)](https://github.com/iamenr0s/ansible-role-rpi-extendfs/actions/workflows/release.yml) ![Ansible Role](https://img.shields.io/ansible/role/d/iamenr0s/ansible_role_upgrade) [![CodeFactor](https://www.codefactor.io/repository/github/iamenr0s/ansible-role-rpi-extendfs/badge)](https://www.codefactor.io/repository/github/iamenr0s/ansible-role-rpi-extendfs)

# Ansible Role: Expand Root Filesystem (RPi + RedHat)

This role expands the root filesystem on supported systems:
- On RedHat-family Linux (RHEL, Rocky, Alma, Fedora), it runs `rootfs-expand` if available.
- On Raspberry Pi OS (Debian-based), it uses `raspi-config` to expand the root partition.

It includes an optional reboot for Raspberry Pi OS to finalize changes.

## Features

- OS-aware expansion for RedHat-family and Raspberry Pi OS
- Non-interactive expansion using `raspi-config nonint do_expand_rootfs` when available
- Fallback to legacy `raspi-config --expand-rootfs` on older Raspberry Pi OS releases
- Optional reboot after expansion on Raspberry Pi OS
- Clear failures when prerequisites (`rootfs-expand` or `raspi-config`) are missing

## Requirements

- Ansible 2.9 or higher
- Privilege escalation (`become: true`)
- Raspberry Pi OS: `raspi-config` must be present
- RedHat family: `rootfs-expand` must be present in the image
- Facts must be gathered (`gather_facts: true`) to detect OS family

## Supported Platforms

- Ubuntu 18.04, 20.04, 22.04
- Debian 10, 11, 12
- RHEL 7, 8, 9
- Rocky Linux 8, 9
- AlmaLinux 8, 9
- Fedora 35+

## Role Variables

### Basic Configuration

- `extendfs_reboot` (bool): Reboot after expanding rootfs on Raspberry Pi OS. Default: `false`.

## How It Works

- RedHat family (`RHEL/Rocky/Alma/Fedora`): Runs `rootfs-expand` as root if available on the image.
- Raspberry Pi OS (Debian-based): Uses `raspi-config nonint do_expand_rootfs`, falling back to `raspi-config --expand-rootfs` if needed.

## Example Playbook

### Basic Usage

```yaml
- hosts: all
  gather_facts: true
  become: true
  roles:
    - role: iamenr0s.ansible_role_rpi_extendfs
```

### Custom Configuration

Enable reboot after expansion on Raspberry Pi OS:

```yaml
- hosts: rpi_hosts
  gather_facts: true
  become: true
  roles:
    - role: iamenr0s.ansible_role_rpi_extendfs
      vars:
        extendfs_reboot: true
```

## Dependencies

None. This role is self-contained.

### Advanced Security Configuration

- Requires `become: true` to manage partitions and filesystems
- Does not open network sockets; operations are local to the host
- Consider limiting execution to trusted hosts and maintenance windows

## Security Features Implemented

- Runs only local system commands (`rootfs-expand`, `raspi-config`)
- No remote code execution beyond package-provided tools

## Security Compliance

- Not applicable. Role performs administrative maintenance operations; comply with your org’s change management policies.

## Testing

This repository includes Molecule for testing.

```bash
pip install -r requirements.txt
molecule test
```

For iterative runs:

```bash
molecule converge
molecule verify
```

## Backup and Recovery

- Ensure you have console or remote management access before performing filesystem changes
- Back up critical data or snapshots if your environment supports it
- After expansion, verify with `lsblk`, `df -h`, and check logs via `dmesg`

## Troubleshooting

### Common Issues

- `raspi-config` not found on Debian: ensure host is Raspberry Pi OS, or install `raspi-config`
- `rootfs-expand` not found on RedHat: your image may not include it; consider alternative methods (growpart + resize2fs)
- Expansion appears unchanged: a reboot may be required (enable `extendfs_reboot` on Raspberry Pi OS)

### Recovery Steps

- Validate current partition and filesystem status:
  - `lsblk`
  - `df -h`
  - `sudo parted -l`
- Check system logs for disk/partition messages: `dmesg | tail -n 200`
- On non-RPi RedHat hosts, consider manual expansion:
  - `sudo growpart /dev/sda 2` (example; confirm your device numbers)
  - `sudo resize2fs /dev/sda2` or appropriate filesystem tool

## License

MIT

## Author Information

Author: iamenr0s
Galaxy: `iamenr0s.ansible_role_rpi_extendfs`

## Contributing

Contributions are welcome! Please:
1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests if applicable
5. Submit a pull request

## Changelog

See `CHANGELOG.md` for version history and release notes.
