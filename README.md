# Ansible Role `jm1.kvm_nested_virtualization`

This role enables KVM nested virtualization for Intel and AMD CPUs.
It is inspired by [Lukas Bednar's](https://github.com/lukas-bednar)
[`lukas-bednar.nested_virtualization`](https://github.com/lukas-bednar/ansible-role-nested-virtualization) role.

*Details*
* Add or remove `options kvm_* nested=y` for kernel modules `kvm_intel` and `kvm_amd` in modprobe config file.
* Reload current kvm kernel module with Ansible's `modprobe` module if configuration has changed

**Tested OS images**
- Cloud image of [`Debian 10 (Buster)` \[`amd64`\]](https://cdimage.debian.org/images/cloud/buster/daily/)
- Cloud image of [`Debian 11 (Bullseye)` \[`amd64`\]](https://cdimage.debian.org/images/cloud/bullseye/daily/)
- Cloud image of [`Debian 12 (Bookworm)` \[`amd64`\]](https://cdimage.debian.org/images/cloud/bookworm/daily/)
- Generic cloud image of [`CentOS 7 (Core)` \[`amd64`\]](https://cloud.centos.org/centos/7/images/)
- Generic cloud image of [`CentOS 8 (Core)` \[`amd64`\]](https://cloud.centos.org/centos/8/x86_64/images/)
- Generic cloud image of [`CentOS 9 (Stream)` \[`amd64`\]](https://cloud.centos.org/centos/9-stream/x86_64/images/)
- Ubuntu cloud image of [`Ubuntu 18.04 LTS (Bionic Beaver)` \[`amd64`\]](https://cloud-images.ubuntu.com/bionic/current/)
- Ubuntu cloud image of [`Ubuntu 20.04 LTS (Focal Fossa)` \[`amd64`\]](https://cloud-images.ubuntu.com/focal/)
- Ubuntu cloud image of [`Ubuntu 22.04 LTS (Jammy Jellyfish)` \[`amd64`\]](https://cloud-images.ubuntu.com/jammy/)

Available on Ansible Galaxy: [jm1.kvm_nested_virtualization](https://galaxy.ansible.com/jm1/kvm_nested_virtualization)

## Requirements

This role uses module(s) from collection [`community.general`][galaxy-community-general]. You can fetch this collection
from Ansible Galaxy using the provided [`requirements.yml`](requirements.yml):

```sh
ansible-galaxy collection install --requirements-file requirements.yml
```

[galaxy-community-general]: https://galaxy.ansible.com/community/general

## Variables

| Name                 | Default value                                    | Required | Description                                                                                                                                     |
| -------------------- | ------------------------------------------------ | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| `modprobe_conf_path` | `/etc/modprobe.d/kvm-nested-virtualization.conf` | no       | Path to modprobe config file. If this file already exists, then it will be overwritten. If `state` is `absent`, then this file will be removed. |
| `reload_module`      | `no`                                             | no       | Should the current kernel module be reloaded if configuration has changed. Beware, the module must not be in use, e.g. no VMs must be running   |
| `state`              | `present`                                        | no       | Should KVM nested virtualization be `present` or `absent`                                                                                       |

## Dependencies

None.

## Example Playbook

```yml
- hosts: all
  roles:
    - name: Enable KVM nested virtualization for Intel and AMD CPUs
      role: jm1.kvm_nested_virtualization
      # Optional: Pass variables to role
      vars:
        modprobe_conf_path: '/etc/modprobe.d/kvm-nested-virtualization.conf'
        reload_module: yes
        state: present
```

For instructions on how to run Ansible playbooks have look at Ansible's
[Getting Started Guide](https://docs.ansible.com/ansible/latest/network/getting_started/first_playbook.html).

## License

GNU General Public License v3.0 or later

See [LICENSE.md](LICENSE.md) to see the full text.

## Author

Jakob Meng
@jm1 ([github](https://github.com/jm1), [galaxy](https://galaxy.ansible.com/jm1), [web](http://www.jakobmeng.de))
