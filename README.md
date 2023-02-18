# deb-distro

> a framework used to create custom debian distributions

## Tools

grub-emu - used to test grub without rebooting

## Dependencies

This system can only be built from a Debian based operating system. While
any Debian based operating system should work, this is only tested against
the official Debian distribution on amd64.

| Name       | Install                                                                                                                                                                                                 | Url                                                                           |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| GNU Make   | `sudo apt-get install -y make`                                                                                                                                                                          | https://www.gnu.org/software/make                                             |
| Live Build | `sudo apt-get install -y live-build`                                                                                                                                                                    | https://live-team.pages.debian.net/live-manual/html/live-manual/index.en.html |
| NixOS      | `sudo true && curl -L https://nixos.org/nix/install \| sh`                                                                                                                                              | https://nixos.org/download.html                                               |
| NodeJS     | `sudo apt-get install -y nodejs`                                                                                                                                                                        | https://nodejs.org/en/                                                        |
| direnv     | `sudo apt-get install -y direnv && export SHELL_NAME=$(echo $SHELL \| grep -oE '[^/]+$') && echo "eval \"\$(direnv hook $SHELL_NAME)\"" >> $HOME/.${SHELL_NAME}rc && eval "$(direnv hook $SHELL_NAME)"` | https://direnv.net                                                            |
| jq         | `sudo apt-get install -y jq`                                                                                                                                                                            | https://stedolan.github.io/jq/                                                |
| yq         | `sudo apt-get install -y snap && sudo snap install yq`                                                                                                                                                  | https://mikefarah.gitbook.io/yq/                                              |

_If you install NixOS and direnv, you do not need to install yq, jq or GNU Make_

## Components

### Fonts

Currently only supports zip files that contain .ttf fonts

### Packages

### Repos

### Filesystem

### Hooks

To use the hooks in your overlay, you will need to create a file named overlay.py within your overlay directory. This file should contain a class named OverlayHooks that implements methods for each hook.

Each hook has two corresponding methods, one for before the stage is executed and one for after the stage is executed. There are currently three stages in the process: build, config, and prepare. This means the following hooks are available for you to use:

- `before_build()`
- `after_build()`
- `before_config()`
- `after_config()`
- `before_prepare()`
- `after_prepare()`

For example, if you wanted to run some custom code before the build stage is executed, you could implement the following in your overlay.py file:

_overlay.py_

```py
class OverlayHooks:
    def before_build(self):
        # Your custom code here
```

It's important to note that the order in which hooks are executed is determined by the order in which the overlays are specified. Make sure to take this into consideration when implementing your hooks.

### Live Build

## Overlays

### Calamares

Calamares is a distribution-independent system installer, which aims
to be easy, useful and fast. In the context of this system, Calamares
can be used as an overlay to provide a graphical installer for the target system.

### Grub

GRUB (GRand Unified Bootloader) is a boot loader package from the GNU Project. It is used to
boot Linux operating systems, as well as a number of other operating systems. In the context
of this system, Grub can be used as an overlay to customize the boot loader.

### Sway

Sway is a tiling window manager for Wayland. It provides a tiling window manager
experience for users who are looking for a modern, keyboard-driven interface. In the
context of this system, Sway can be used as an overlay to provide a tiling
window manager for the target system.

## Live Build Cheatsheet

### Definitions

- **live medium** - the ISO image and filesystem
- **live system** - the operating system booted from the live medium
- **installed system** - the operating system installed from debian installer
- **chroot stage** - stage when building the image
- **binary stage** - stage when building the live medium

### File Structure

- `config/archives/*.{list,key}.binary` - repositories added to _live system_ `/etc/apt/sources.list.d/`
- `config/archives/*.{list,key}.chroot` - repositories loaded during the _chroot stage_
- `config/includes.binary/*` - files to include in the _live medium's_ filesystem
- `config/includes.chroot/*` - files to include in the _live system's_ filesystem
- `config/includes.installer/*` - configuration for debian installer
- `config/package-lists/*.list.binary` - packages to place in the APT `pool/` repository on the _live medium_ (for offline packages)
- `config/package-lists/*.list.chroot` - packages to install in the _live system_ (which will most likely be added to the _installed system_)
- `config/package-lists/*.list.chroot_install` - packages to install in the _live system_ and _installed system_
- `config/package-lists/*.list.chroot_live` - packages to install in the _live system_ only (works by uninstalling them from _installed system_)

_I'm not sure exactly what the difference between `config/package-lists/*.list.chroot` and
`config/package-lists/*.list.chroot_install` are._

### Mounts

#### Debian Installer

- **live medium** - `/cdrom` if debian installer or `/run/live/medium` from live system
- **installed system** - `/target` if debian installer or `/tmp/calamares-root-*` if calamares

## Resources

- https://debian-live-config.readthedocs.io/en/latest/custom.html
- https://github.com/elementary/os/wiki/Building-ISO-Images
- https://live-team.pages.debian.net/live-manual/html/live-manual/index.en.html
- https://bugs.launchpad.net/subiquity/+bug/1960068
- https://discourse.ubuntu.com/t/automated-server-install-reference/16613/21
- https://help.ubuntu.com/community/InstallCDCustomization
