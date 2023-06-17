# `molecule-qemu`

The development of the Molecule `QEMU` driver was motivated by the author's choice of the Apple M1 as their primary development machine. He wanted to test Ansible roles on the Apple M1 but preferred not to rely on `Docker` for testing due to challenges with Docker's `systemd` support. Author never liked to install supplementary software such as `Vagrant` and `VirtualBox` on development machine. Author is aware of `libvirt` and `virt-manager` but their complexity and the lack of support was frustrating.

Supported platforms:
* MacOS 13.x (arm64)
* Ubuntu 22.04 LTS (amd64) (tested on GitHub Actions)

Support guest OS:
* Debian 11 (arm64, amd64)
* Ubuntu 20.04 LTS (arm64, amd64)
* Ubuntu 22.04 LTS (arm64, amd64)

Support of other platforms and guest OS is possible, but not tested. Please, open an issue if you want to add support for other platforms.

Supported network modes:
* `user` - QEMU's user networking mode
* `vmnet-shared` - QEMU's `vmnet-shared` networking mode (MacOS only)

## Usage

```bash
pip install molecule-qemu
```

Install QEMU and CDRTools on macOS:

```bash
brew install qemu cdrtools
```

Install QEMU on Ubuntu:

```bash
apt-get install mkisofs qemu-system-x86 qemu-utils
```

## Network modes

### `user` network mode

This is the default network mode. It uses QEMU's user networking mode.

Mode is selected by setting `vm_network: user` in `molecule.yml`.

### `vmnet-shared` network mode

This mode uses QEMU's `vmnet-shared` networking mode. It requires `vmnet.framework` to be installed on the host. This mode is only supported on MacOS. It requires *passwordless* `sudo` access for current user.

Mode is selected by setting `vm_network: vmnet-shared` in `molecule.yml`.

# Examples

See [tests](https://github.com/andreygubarev/molecule-qemu/tree/main/tests/molecule) for more examples.

## Example scenario
```bash
molecule init scenario default --driver-name molecule-qemu --verifier-name testinfra
```

## Example `molecule.yml` for `user` network mode

```yaml
---
dependency:
  name: galaxy
driver:
  name: molecule-qemu
platforms:
  - name: debian-bullseye-arm64
    image: https://cloud.debian.org/images/cloud/bullseye/latest/debian-11-genericcloud-arm64.qcow2
    image_checksum: sha512:https://cloud.debian.org/images/cloud/bullseye/latest/SHA512SUMS
    image_arch: aarch64
    ssh_port: 10000
  - name: debian-bullseye-amd64
    image: https://cloud.debian.org/images/cloud/bullseye/latest/debian-11-genericcloud-amd64.qcow2
    image_checksum: sha512:https://cloud.debian.org/images/cloud/bullseye/latest/SHA512SUMS
    image_arch: x86_64
    ssh_port: 10001
  - name: ubuntu-focal-arm64
    image: https://cloud-images.ubuntu.com/focal/current/focal-server-cloudimg-arm64.img
    image_checksum: sha256:https://cloud-images.ubuntu.com/focal/current/SHA256SUMS
    image_arch: aarch64
    ssh_port: 10002
  - name: ubuntu-focal-amd64
    image: https://cloud-images.ubuntu.com/focal/current/focal-server-cloudimg-amd64.img
    image_checksum: sha256:https://cloud-images.ubuntu.com/focal/current/SHA256SUMS
    image_arch: x86_64
    ssh_port: 10003
  - name: ubuntu-jammy-arm64
    image: https://cloud-images.ubuntu.com/jammy/current/jammy-server-cloudimg-arm64.img
    image_checksum: sha256:https://cloud-images.ubuntu.com/jammy/current/SHA256SUMS
    image_arch: aarch64
    ssh_port: 10004
  - name: ubuntu-jammy-amd64
    image: https://cloud-images.ubuntu.com/jammy/current/jammy-server-cloudimg-amd64.img
    image_checksum: sha256:https://cloud-images.ubuntu.com/jammy/current/SHA256SUMS
    image_arch: x86_64
    ssh_port: 10005
provisioner:
  name: ansible
verifier:
  name: ansible
```

## Example `molecule.yml` for `vmnet-shared` network mode

```yaml
---
dependency:
  name: galaxy
driver:
  name: molecule-qemu
platforms:
  - name: ubuntu-1
    image: https://cloud-images.ubuntu.com/focal/current/focal-server-cloudimg-arm64.img
    image_checksum: sha256:https://cloud-images.ubuntu.com/focal/current/SHA256SUMS
    image_arch: aarch64
    ssh_user: ubuntu
    vm_network: vmnet-shared
  - name: ubuntu-2
    image: https://cloud-images.ubuntu.com/focal/current/focal-server-cloudimg-amd64.img
    image_checksum: sha256:https://cloud-images.ubuntu.com/focal/current/SHA256SUMS
    image_arch: x86_64  # default
    ssh_user: ubuntu
    vm_network: vmnet-shared
provisioner:
  name: ansible
verifier:
  name: ansible
```

# Cloud Images URLs

For convenience, here are the URLs for the cloud images used in the examples above.

## [Debian](https://cloud.debian.org/images/cloud/)
* https://cloud.debian.org/images/cloud/bullseye/latest/SHA512SUMS
  * https://cloud.debian.org/images/cloud/bullseye/latest/debian-11-genericcloud-amd64.qcow2
  * https://cloud.debian.org/images/cloud/bullseye/latest/debian-11-genericcloud-arm64.qcow2

## [Ubuntu](https://cloud-images.ubuntu.com/)
* https://cloud-images.ubuntu.com/focal/current/SHA256SUMS
  * https://cloud-images.ubuntu.com/focal/current/focal-server-cloudimg-arm64.img
  * https://cloud-images.ubuntu.com/focal/current/focal-server-cloudimg-amd64.img
* https://cloud-images.ubuntu.com/jammy/current/SHA256SUMS
  * https://cloud-images.ubuntu.com/jammy/current/jammy-server-cloudimg-arm64.img
  * https://cloud-images.ubuntu.com/jammy/current/jammy-server-cloudimg-amd64.img



# Reference

* [Ansible](https://www.ansible.com/)
* [Molecule](https://molecule.readthedocs.io/en/latest/)
* [QEMU](https://www.qemu.org/)
* [QEMU BIOS](https://packages.debian.org/bullseye/qemu-efi-aarch64)

## QEMU vmnet-shared networking

* [vmnet.framework modes](https://lore.kernel.org/all/20220315230741.21578-7-Vladislav.Yaroshchuk@jetbrains.com/T/)

