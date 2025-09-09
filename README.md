# Setting up RiverWM in Debian 13 (Trixie)

> [!NOTE]
> This is a work in progress. Changes will occur very often until completely done.
>
> This only covers the installation of River. To configure it, refer to [river/wiki](https://codeberg.org/river/wiki).
>
> Installation has been done through 'Expert Install' on '[netinst](https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/debian-13.1.0-amd64-netinst.iso)' ISO.

(To enable non-free, and some other configurations that cannot be done on standard install.)

After install, you're booted to a TTY Session. We will install a set of packages before installing River.

As per [river's](https://github.com/riverwm/river) instruction on `Building`, here's the packages we need:
```
- zig 0.13
- wayland
- wayland-protocols
- wlroots 0.18
- xkbcommon
- libevdev
- pixman
- pkg-config
- scdoc
```

> [!NOTE]
> Optional, but it's best to perform a quick `apt update`.

### Wayland Dependencies
```
libwayland-bin
libwayland-client0
libwayland-cursor0
libwayland-dev
libwayland-egl-backend-dev
libwayland-egl1
libwayland-server0
wayland-protocols
libwlroots-0.18
libwlroots-0.18-dev
xwayland - optional if you're using X11 programs.
```

### xkbcommon
```
libxkbcommon-dev

From Debian Packages, these were also part of 'libxkbcommon', I installed it but they might not be needed.
Best to leave it here just in case the build fails.

libxkbcommon-tools
libxkbcommon-x11-0
libxkbcommon-x11-dev
libxkbcommon0
libxkbregistry-dev
libxkbregistry0
```

## evdev
```
libevdev-dev

Same as above.

libevdev-tools
libevdev2
```

## pixman
```
libpixman-1-dev

(not sure if needed)
libpixman-1-0
```

## Core Packages - Required to install or else we'll get some errors while building.
```
pkg-config
g++ 
xserver-xorg
build-essential
seatd
pkexec
foot - River's config file uses this terminal by default, nothing to do with building, but is a neccessity.
```

# Installing Zig

Zig isn't packaged on Debian. So we'll install its binary from their page.

We'll use `wget` to download the tarball.

As per River's Requirements, we need Zig 0.13.0

`wget https://ziglang.org/download/0.13.0/zig-linux-x86_64-0.13.0.tar.xz`

Extract the tarball

`tar -xf zig-linux-x86_64-0.13.0.tar.xz` - Optional, you can use `-xvf` to enable verbose output.

## Move the contents *inside* `/usr/local/bin/`

`sudo mv zig-linux-x86_64-0.13.0/* /usr/local/bin` 

> (notice the `*` after the zig folder. This indicates that it will only copy what's inside the folder, not including the folder itself.)

# Source `PATH` 
`export PATH="$PATH:/usr/local/bin/"`

> (notice the double-quotation marks, they are *included*.)
> 
> check if zig works by doing `zig version`

# Cloning the Repository

Before doing so, install `git`.

Then do `git clone https://riverwm/river`

# To compile:

## *Without* `xwayland`

`zig build -Doptimize=ReleaseSafe --prefix ~/.local install`

## *With* `xwayland`

`zig build -Dxwayland -Doptimize=ReleaseSafe --prefix ~/.local install`

Assuming you have installed the packages needed, this should work just fine. If it fails, try to include the packages provided below each categories.

## After a successful build, do ***NOT*** go into `river` yet. You have no configuration file yet and you'd be forced to shut it down.

Assuming you did *not* remove the cloned river's repository, we will copy the [init](https://github.com/riverwm/river/blob/master/example/init) file for it, so you can have the starting block for river and you can use it.

Create a directory named "river" on `.config` directory

`mkdir -p ~/.config/river/`

Then move the `init` file from the `river` repository

`cp river/example/init $HOME/.config/river/`

> Afterwards, this should be good to go.
>

# To use river, just type `river`.

## Sources used:

- [Debian Packages](https://www.debian.org/distrib/packages)

- [riverwm/river](https://github.com/riverwm/river)

### To-Do List:
- [ ] Cleaner Look of this guide
- [ ] Add Ly Display Manager as an option to install and detect River
- [ ] Complete the guide


