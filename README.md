# GitHub Actions

This is a repository which provides everything needed to get the GitHub Actions
remote runner under Arch Linux.

This repository is a fork of [ioquatix/github-actions-bin](https://github.com/ioquatix/github-actions-bin)
which maintains the [github-actions-bin AUR package](https://aur.archlinux.org/packages/github-actions-bin/) which is - as of January 24th 2021 - still flagged as out of date.

## Motivation behind this fork

At [@Ultimate-Hosts-Blacklist](https://github.com/Ultimate-Hosts-Blacklist) we
wanted to try to use the GitHub Actions
runner under Arch Linux, but unfortunately, the package was not up to date.

Therefore, we forked the upstream repository here, so that we can have our own
PKGBUILD which we are going to try to keep up to date.

## Update frequency

This repository will check and process updates every night at 00:00 UTC.

## Installation

First, clone this repository:

    git clone https://github.com/Ultimate-Hosts-Blacklist/arch-linux-gh-actions-runner.git

The build and install it:

    cd rch-linux-gh-actions-runner
    makepkg -sCcfi

Configure the daemon:

    sudo -u github-actions -s bash
    cd /var/lib/github-actions
    bin/Runner.Listener configure --token YOUR_RUNNER_TOKEN
    exit

Then start it up:

    sudo systemctl enable --now github-actions

## Usage

You might want to combine this with a container. To create a container (including some suggested packages):

    pacstrap -c github-actions base ruby clang pkg-build vim

Then, import it:

    machinectl import-fs github-actions

Configure it:

    $ cat /etc/systemd/nspawn/github-actions.nspawn
    [Network]
    Private=no
    VirtualEthernet=no

    [Files]
    TemporaryFileSystem=/tmp

Boot it:

    sudo systemctl enable --now systemd-nspawn@github-actions.service

Attach to the container and install the github-actions package:

    machinectl shell github-actions

    cd /tmp
    sudo -u nobody git clone https://aur.archlinux.org/github-actions.git
    cd github-actions
    sudo -u nobody makepkg -fc
    pacman -U github-actions*.pkg.tar*

### Limits

Limit the memory consumption of your container to 2 GiB:

    systemctl set-property systemd-nspawn@myContainer.service MemoryMax=2G

Limit the CPU time usage to roughly the equivalent of 2 cores:

    systemctl set-property systemd-nspawn@myContainer.service CPUQuota=200%

#### ZFS Quotas

To ensure that your disk is not consumed by badly behaving test or malicious code:

    zfs create -o mountpoint=/var/lib/machines/github-actions -o quota=8G system/machines/github-actions

### Attaching Shell

    sudo machinectl shell github-actions

### Exposing Nvidia GPU

Add the following to the `github-actions.nspawn` configuration:

    [Files]
    # Expose GPU:
    Bind=/dev/nvidia0
    Bind=/dev/nvidiactl

Then, ensure the container unit can see the required devices:

    sudo systemctl set-property systemd-nspawn@github-actions.service "DeviceAllow=/dev/nvidia0 rwm" "DeviceAllow=/dev/nvidiactl rwm"

Finally, ensure the driver is a requirement of the container:

    sudo systemctl add-requires systemd-nspawn@github-actions.service nvidia-persistenced.service

You also may need the container to be running the same Linux kernel and drivers.
For this, I use `linux-lts` and `nvidia-dkms` on both the host and container.

You can test this setup using `nvidia-smi` which should show the same output
in both the host and container.

## Removing runner

    sudo systemctl stop github-actions
    sudo systemctl disable github-actions
    sudo -u github-actions -s bash
    cd /var/lib/github-actions
    bin/Runner.Listener remove --token YOUR_RUNNER_TOKEN
    # prune configuration
    # rm -rf .config/GitHub* _diag _work .env .path svc.sh
    exit

## License

```
MIT License

Copyright (c) 2019 Samuel Williams
Copyright (c) 2021 Ultimate-Hosts-Blacklist
Copyright (c) 2021 Nissar Chababy


Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```
