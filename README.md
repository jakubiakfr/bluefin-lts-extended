# bluefin-lts-extended
[![bluebuild build badge](https://github.com/jakubiakfr/bluefin-lts-extended/actions/workflows/build.yml/badge.svg)](https://github.com/jakubiakfr/bluefin-lts-extended/actions/workflows/build.yml)

A fork of [Bluefin LTS](https://github.com/ublue-os/bluefin-lts) with **OpenVPN** and **FUSE** support added. This image builds on the stable Bluefin base, extending it for users who need encrypted VPN connectivity and filesystem in userspace (FUSE) capabilities.

> **Note:** The `nm-openvpn` system user is _not_ created by default. If you plan to use OpenVPN via NetworkManager, you must create this user and group manually (see below).

---

## Overview

- **Base:** Bluefin LTS (Fedora Atomic OSTree template)
- **Added packages:**
  - OpenVPN (via NetworkManager plugin)
  - FUSE (Filesystem in Userspace)

## Creating the OpenVPN User

NetworkManager’s OpenVPN integration runs under a dedicated system account. To set it up:

```bash
sudo groupadd --system nm-openvpn
sudo useradd --system \
    --home-dir / \
    --shell /usr/sbin/nologin \
    --comment "NetworkManager OpenVPN user" \
    --gid nm-openvpn \
    nm-openvpn
```

Without this user, OpenVPN connections managed by NetworkManager will fail to start.

---

## Installation

To rebase an existing Atomic CentOS installation onto this extended image:

1. **Rebase to the unsigned image** to obtain signing keys and policies:
   ```bash
   rpm-ostree rebase ostree-unverified-registry:ghcr.io/jakubiakfr/bluefin-lts:latest
   ```
2. **Reboot** to apply the change:
   ```bash
   systemctl reboot
   ```
3. **Rebase to the signed image**:
   ```bash
   rpm-ostree rebase ostree-image-signed:docker://ghcr.io/jakubiakfr/bluefin-lts:latest
   ```
4. **Reboot** again:
   ```bash
   systemctl reboot
   ```

The `latest` tag always points to the newest build but uses the centos version defined in `recipe.yml`, preventing unintended major upgrades.

---

## Verification

All images are signed with [Sigstore](https://www.sigstore.dev/) using [cosign](https://github.com/sigstore/cosign). To verify:

```bash
cosign verify --key cosign.pub ghcr.io/jakubiakfr/bluefin-lts-extended
```

Download the `cosign.pub` key from this repository before running the verification.

