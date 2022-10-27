# Ansible Role `jm1.kvm_nested_virtualization`

This role enables KVM nested virtualization for Intel and AMD CPUs.

It adds or removes `options kvm_* nested=y` for kernel modules `kvm_intel` and `kvm_amd` in modprobe config file
`/etc/modprobe.d/kvm-nested-virtualization.conf` (defined with variable `modprobe_conf_path`). When kernel module
options have been changed and `reload_module` is true, then it will reload the current kvm kernel module with Ansible
module `community.general.modprobe`.

:warning: **WARNING:**
This role will remove and (re)load the `kvm_intel` and `kvm_amd` modules from the Linux kernel to apply changes when
variable `reload_module` is set to true. Before executing this role ensure that no virtual machines or other processes
depending on these modules are running.
:warning:

With `state: present`, this role runs tasks similar to the following shell commands:

```sh
# Reloading kernel modules and changing their options requires root rights
sudo -s

# Identify kvm support
if ! grep -E 'vmx|svm' -q /proc/cpuinfo; then
    echo "No virtualization support has been detected"
else
    if grep -E 'vmx' -q /proc/cpuinfo; then
        # Detected Intel virtualization
        kvm_kernel_module="kvm_intel"
    else
        # Detected AMD virtualization
        kvm_kernel_module="kvm_amd"
    fi

    # Add module options to enable nested virtualization
    cat << ____EOF > /etc/modprobe.d/kvm-nested-virtualization.conf
# 2020-2022 Jakob Meng, <jakobmeng@web.de>
# Enable KVM nested virtualization for Intel and AMD CPUs
# Ref.: https://galaxy.ansible.com/jm1/kvm_nested_virtualization
options kvm_intel nested=y
options kvm_amd nested=1
____EOF

    # Ensure kernel module is available and loaded
    modprobe "$kvm_kernel_module"

    # Reload kernel module to apply changes
    if grep -E '^N|0$' -q "/sys/module/${kvm_kernel_module}/parameters/nested"; then
        rmmod "$kvm_kernel_module"
        modprobe "$kvm_kernel_module"
    fi
fi
```

With `state: absent`, this role runs tasks similar to the following shell commands:

```sh
# Reloading kernel modules and changing their options requires root rights
sudo -s

# Identify kvm support
if ! grep -E 'vmx|svm' -q /proc/cpuinfo; then
    echo "No virtualization support has been detected"
else
    if grep -E 'vmx' -q /proc/cpuinfo; then
        # Detected Intel virtualization
        kvm_kernel_module="kvm_intel"
    else
        # Detected AMD virtualization
        kvm_kernel_module="kvm_amd"
    fi

    # Remove module options to enable nested virtualization
    rm /etc/modprobe.d/kvm-nested-virtualization.conf

    # Ensure kernel module is available and loaded
    modprobe "$kvm_kernel_module"

    # Reload kernel module to apply changes
    if grep -E '^Y|1$' -q "/sys/module/${kvm_kernel_module}/parameters/nested"; then
        rmmod "$kvm_kernel_module"
        modprobe "$kvm_kernel_module"
    fi
fi
```

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

This role is inspired by [Lukas Bednar's](https://github.com/lukas-bednar)
[`lukas-bednar.nested_virtualization`](https://github.com/lukas-bednar/ansible-role-nested-virtualization) role.

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
| `reload_module`      | `yes`                                            | no       | Should the current kernel module be reloaded if configuration has changed. Beware, the module must not be in use, e.g. no VMs must be running   |
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
