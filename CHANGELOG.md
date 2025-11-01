# Changelog

## v1.0.0 - 2025-10-31

- Initial release.
- OS-specific root filesystem expansion:
  - RedHat family: run `rootfs-expand` if present.
  - Raspberry Pi OS: use `raspi-config nonint do_expand_rootfs` with legacy fallback `raspi-config --expand-rootfs`.
- Introduce `extendfs_reboot` variable to optionally reboot Raspberry Pi OS after expansion.
- Document features, requirements, usage examples, testing, and troubleshooting in README.