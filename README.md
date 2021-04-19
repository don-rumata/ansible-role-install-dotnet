# Ansible Role: Install .NET

[![License][license-image]][license-url] [![Ansible Galaxy][ansible-galaxy-image]][ansible-galaxy-url]

Install [.NET](https://dotnet.microsoft.com/download) for Linux and Windows.

## Work on

### Ansible Galaxy style

```yaml
  platforms:
    - name: Fedora
      versions:
        - 33
    - name: Ubuntu
      versions:
        - trusty
        - xenial
        - bionic
        - cosmic
        - disco
        - eoan
        - focal
    - name: Debian
      version:
        - jessie
        - stretch
        - buster
    # https://github.com/ansible/galaxy/issues/2072
    - name: EL
      versions:
        - 7
        - 8
    - name: opensuse
      vesrion:
        - tumbleweed
        - 15.2
    - name: Windows
      version:
        - 2008x64 (7 64bit)
        - 2019 (10 64bit)
```

## Requirements

[min_ansible_version: 2.8]

## Role Variables

```yaml
#--- Release section ---#
# https://dotnet.microsoft.com/download/dotnet-core

dotnet_support_phase: current
# dotnet_support_phase: lts
# dotnet_support_phase: preview
# # dotnet_support_phase: eol

dotnet_default_component: runtime
# dotnet_default_component: sdk
# dotnet_default_component: hosting
# dotnet_default_component: aspnetcore-runtime

# dotnet_current_releases_json: https://dotnetcli.blob.core.windows.net/dotnet/release-metadata/5.0/releases.json
# dotnet_current_channel_version: 5.0

# For install custom version for Linux. sudo apt-get install dotnet-runtime-5.0=5.0.3-1
# dotnet_current_version: 5.0.3-1

# If you have a copy on your local network
# dotnet_runtime_or_sdk_win_x64_exe_url: http://10.10.10.10/soft/dotnet/dotnet-{{ dotnet_default_component }}-latest-win-amd64.exe

#--- Method install section ---#

dotnet_method_install: package-manager

# https://en.wikipedia.org/wiki/Snappy_(package_manager)
# https://snapcraft.io/search?q=dotnetcore
# dotnet_method_install: snap

#--- Urls section ---#
# https://docs.microsoft.com/en-us/dotnet/core/install/linux

dotnet_releases_index_json_url: https://raw.githubusercontent.com/dotnet/core/master/release-notes/releases-index.json

#--- Repo section ---#

dotnet_repo_deb_key: https://packages.microsoft.com/keys/microsoft.asc

dotnet_repo_rpm_key: https://packages.microsoft.com/keys/microsoft.asc

# If you *NOT* use apt-cacher-ng or other caching proxy - select "https".
http_or_https: http
# http_or_https: https
```

## Dependencies

### If you want deploy to Windows 7

Download and install [Windows Management Framework 5.1](https://www.microsoft.com/en-us/download/details.aspx?id=54616)

## HowTo

### How to install role

Over `ansible-galaxy`:

```bash
ansible-galaxy install don_rumata.ansible_role_install_dotnet
```

Over `bash+git`:

```bash
mkdir -p "$HOME/.ansible/roles"
cd "$HOME/.ansible/roles"
git clone https://github.com/don-rumata/ansible-role-install-dotnet don_rumata.ansible_role_install_dotnet
```

### Quick config WinRM for Windows

<https://ru.stackoverflow.com/a/949971/191416>

## Example Playbooks

### I

Install latest current dotnet on Windows or Linux over package manager of you distro:

`install-dotnet.yml`:

```yaml
- name: Install dotnet
  hosts: all
  strategy: free
  serial:
    - "100%"
  roles:
    - don_rumata.ansible_role_install_dotnet
  tasks:
```

### II

Install version `2.1`:

`install-dotnet.yml`:

```yaml
- name: Install dotnet
  hosts: all
  strategy: free
  serial:
    - "100%"
  roles:
    - role: ansible-role-install-dotnet
      dotnet_current_channel_version: 2.1
  tasks:
```

### III

Install different versions for different hosts:

`install-dotnet.yml`:

```yaml
- name: Install dotnet
  hosts: all
  strategy: free
  serial:
    - "100%"
  roles:
    - role: ansible-role-install-dotnet
  tasks:
```

`dotnet-inventory.ini`:

```ini
[linux-hosts]
debian-test-01.local
ubuntu-test-01.local dotnet_method_install=snap
centos-7-test-01.local ansible_python_interpreter=/usr/bin/python2
centos-8-test-01.local
opensuse-leap-test-01.local dotnet_default_component=sdk
opensuse-tumbleweed-test-01.local
fedora-33-test-01.local dotnet_support_phase=lts

[windows-hosts]
10.10.10.20
10.10.10.21
10.10.10.22
10.10.10.23
win7-64 dotnet_runtime_or_sdk_win_x64_exe_url=\\10.10.10.10\soft\dotnet\dotnet-{{ dotnet_default_component }}-latest-win-amd64.exe dotnet_default_component=hosting
win10-64 dotnet_runtime_or_sdk_win_x64_exe_url=http://10.10.10.10/soft/dotnet/dotnet-{{ dotnet_default_component }}-latest-win-amd64.exe dotnet_support_phase=preview dotnet_default_component=sdk
```

```bash
ansible-playbook -i ./dotnet-inventory.ini ./install-dotnet.yml
```

## License

Apache License, Version 2.0

## Author Information

[don Rumata](https://github.com/don-rumata)

## TODO

- Add tests.

[license-image]: https://img.shields.io/github/license/don-rumata/ansible-role-install-dotnet.svg
[license-url]: https://opensource.org/licenses/Apache-2.0

[ansible-galaxy-image]: https://img.shields.io/badge/ansible_galaxy-don__rumata.ansible__role__install__dotnet-blue.svg
[ansible-galaxy-url]: https://galaxy.ansible.com/don_rumata/ansible_role_install_dotnet
