# ccdc.vagrant-base-box

[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-ccdc.vagrant-base-box-blue.svg)](https://galaxy.ansible.com/ccdc/debloat_windows/)
[![Ansible Lint](https://github.com/ccdc-opensource/ansible-role-vagrant-base-box/actions/workflows/lint-ansible-role.yml/badge.svg)](https://github.com/ccdc-opensource/ansible-role-debloat-windows/actions/workflows/lint-ansible-role.yml)
[![Common CCDC PR Checks](https://github.com/ccdc-opensource/ansible-role-vagrant-base-box/actions/workflows/common_ccdc_status_checks.yml/badge.svg)](https://github.com/ccdc-opensource/ansible-role-debloat-windows/actions/workflows/common_ccdc_status_checks.yml)

An Ansible role to perform some provisioning on virtual machines for exporting as Vagrant base boxes.

## What does this role do?

- [Windows] Rearrange drives so hard drives are clustered from C: onwards and optical drives after them
- [Windows] Install all available system updates
- [Windows] Precompile .NET assemblies
- [Windows] Set PowerShell execution policy to `RemoteSigned`
- [Windows] Disable password expiration for `vagrant` user
- [Windows] Set all network connections to be `Private` by default
- [Windows] Enable access to the VM via RDP
- [Windows] Enable access to the VM via SSH (Windows Server only)
- [Windows] Enable User Account Control
- [Windows] Set power management plan to High Performance

## Requirements

For provisioning Windows VMs, this role requires the
`[community.windows](https://galaxy.ansible.com/community/windows)` collection.

## Role Variables

Most of the variables for this role are simple booleans to determine which debloating features should be run.

### Rearrange hard drives

This task will rearrange all hard drives as per a given specification and then sort all optical drives to after the final hard drive.

| Variable              |  Default  | Comments                                                    |
|-----------------------|-----------|-------------------------------------------------------------|
| `rearrange_drives`    | `true`    | Whether or not to rearrange hard drives                     |
| `drive_configuration` | see below | A list of mappings of drive labels to their desired letters |

#### Default values for `drive_configuration`

At the CCDC, Windows build machines have two drives in addition to the system drive. These are assigned
drive letters as per this specification:

```
drive_configuration:
  - letter: d
    label: x_mirror
  - letter: e
    label: builds
```

Where `builds` is a drive for build spaces, and `x_mirror` a drive holding local copies of compiled
libraries, data etc. required to build our software. You can similarly assign specific drive letters
to any drives you've specified in your VM configuration and formatted using the `<DiskConfiguration>`
section in your Windows' `autounattend.xml` file.

### Update operating system

| Variable         | Default | Comments                                                          |
|------------------|---------|-------------------------------------------------------------------|
| `update_windows` | `true`  | Whether or not to install any available operating system updates. |

### Precompile .NET assemblies

| Variable            | Default | Comments                                      |
|---------------------|---------|-----------------------------------------------|
| `precompile_dotnet` | `true`  | Whether or not to precompile .NET assemblies. |

Doing this can save some time when using .NET on the finished build machine.

### Set PowerShell execution policy

| Variable                  | Default        | Comments                                                  |
|---------------------------|----------------|-----------------------------------------------------------|
| `set_execution_policy`    | `true`         | Whether to set PowerShell execution policy (system-wide). |
| `target_execution_policy` | `RemoteSigned` | The execution policy to set.                              |

### Disable `vagrant` user password expiration

| Variable                          | Default | Comments                                                       |
|-----------------------------------|---------|----------------------------------------------------------------|
| `disable_vagrant_password_expiry` | `true`  | Whether to disable password expiration for the `vagrant` user. |

### Set all network connections as `private` by default

| Variable                          | Default | Comments                                                                    |
|-----------------------------------|---------|-----------------------------------------------------------------------------|
| `disable_network_location_prompt` | `true`  | Whether to disable the prompt for network types on new network connections. |
| `set_all_networks_private`        | `true`  | Whether to set all current network connections to be "Private"              |

### Enable RDP, SSH and User Account control

| Variable                 | Default | Comments                                                                                                    |
|--------------------------|---------|-------------------------------------------------------------------------------------------------------------|
| `enable_rdp`             | `true`  | Whether to set the system up to allow access via RDP.                                                       |
| `enable_ssh`             | `true`  | Whether to set the system up to allow access via SSH.                                                       |
| `add_vagrant_ssh_pubkey` | `true`  | Whether to add the default insecure Vagrant SSH public key to the `authorized_keys` for the `vagrant` user. |
| `enable_uac`             | `true`  | Whether to enable User Account Control.                                                                     |

### Set Power Management plan

| Variable     | Default            | Comments                                                            |
|--------------|--------------------|---------------------------------------------------------------------|
| `power_plan` | `High performance` | The name of the Windows power management plan to set on the system. |

