# Setup RiverWM in Debian 13 (Trixie)

> [!IMPORTANT]
> This guide is *NOT* meant for beginners. I assume that you have a decent knowledge with configuring stuff in TTY, or using a text editor like [vim](https://www.vim.org/) or [nano](https://www.nano-editor.org/).
> 
> This only covers the installation of River. To configure it, refer to [river/wiki](https://codeberg.org/river/wiki).
>
> For installation, I have done a "minimal" install using Debian's [netinst](https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/debian-13.1.0-amd64-netinst.iso) ISO.

> I've also included an option to install [Ly Display Manager](https://github.com/fairyglade/ly), although you'd need to do a little work with Zig, in which I will provide instructions. Since Ly requires Zig 0.15.x, and River only requires Zig 0.13.0.
> 
> I would also appreciate if you can point out any improvements for this guide.

> [!NOTE]
> Optional, but it's best to perform a quick `apt update`.

### As per [river's](https://github.com/riverwm/river) instruction, here's the packages we need:
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

## Core Packages - Required to install.
```
xserver-xorg
pkg-config
g++ 
xserver-xorg
build-essential
seatd
pkexec
foot - River's config file uses this terminal by default, nothing to do with building, but is a neccessity.
git - For cloning repositories.
```

# Installing Zig

> [!IMPORTANT]
> This will include *two* versions of Zig, just in case you want to install Ly alongside it for your login manager. This is optional.
>
> If you *only* want to install River, only follow Zig 13. Otherwise, do both.

Zig isn't packaged on Debian. So we'll install its binary from their page.

# Zig 13

Create a directory first under `/usr/local/bin/`
```
sudo mkdir -p /usr/local/bin/zig-13
```
To check:
```
ls /usr/local/bin
```

You should be able to see `zig-13` inside.

Since we're on a TTY session, we will use wget to grab the tarball.

### As per River's Requirements, we need Zig 0.13.0 ###
```
wget https://ziglang.org/download/0.13.0/zig-linux-x86_64-0.13.0.tar.xz
```
> Since I have done a minimal install, I have no directories yet.
>
> (Such as Documents, Downloads, Videos, Music, etc.)
>
> Our download directory for now is only at /home/username, which is fine.
>
> However, if you want those directories to appear, simply append `xdg-user-dirs-update`, then do `ls`, you should be able to seem them.

Extract the tarball with `tar` command.
```
tar -xf zig-linux-x86_64-0.13.0.tar.xz
```
> Optional, but you can use `-xvf` to enable verbose output.

## Move the contents *inside* `/usr/local/bin/zig-13/`
```
sudo mv zig-linux-x86_64-0.13.0/* /usr/local/bin/zig-13/
```
> (notice the `*` after the zig folder. This indicates that it will only copy what's inside the folder, not including the folder itself.)

> [!CAUTION]
> Be ***CAREFUL*** on using `*`, this is a very powerful symbol and you can mess up really bad if you used it in some unexpected ways.

# Cloning the Repository
```
git clone https://riverwm/river
```
### To compile:

There are two ways to compile River. One *without* the `xwayland` package installed, or *with* `xwayland` installed.

> Either way, the compiler will return an error if you tried to compile with `xwayland` flag but did not install `xwayland`, the simple fix is to obviously install it, or just remove the flag.

## *Without* `xwayland`
```
zig build -Doptimize=ReleaseSafe --prefix ~/.local install
```
## *With* `xwayland`
```
zig build -Dxwayland -Doptimize=ReleaseSafe --prefix ~/.local install
```
# Finishing touches
Assuming you have installed the packages needed, this should work just fine. If it fails, try to include the packages provided below each categories.

At this point, you're technically done, but we need to copy the [init](https://github.com/riverwm/river/blob/master/example/init) file first from the repository that we have cloned earlier, which is river.

Create a directory named "river" on `.config` directory
```
mkdir -p ~/.config/river/
```
Then move the `init` file from the `river` repository
```
cp river/example/init $HOME/.config/river/
```
To go onto the session, just type `river`.

# Optional - Ly with Zig 15

Before cloning the repository, we will install Zig first.

Again, with `wget`:
```
wget https://ziglang.org/download/0.15.1/zig-x86_64-linux-0.15.1.tar.xz
```
Then make a directory named `zig-15` inside `/usr/local/bin`
```
sudo mkdir -p /usr/local/bin/zig-15
```
Extract the tarball:
```
tar -xf zig-x86_64-linux-0.15.1.tar.xz
```
Move the extracted tarball inside `/usr/local/bin/zig-15/`
```
sudo mv zig-x86_64-linux-0.15.1/* /usr/local/bin/zig-15/
```
Now we can clone the repository.
```
git clone https://github.com/fairyglade/ly
```
Before we compile and install it, let us grab the dependencies needed.

As per the instructions, here's the dependencies we need:
```
- zig 0.15.x
- libc
- pam
- xcb (optional, required by default; needed for X11 support)
```

Fortunately, the repository has included the packages needed.

```
build-essential
libpam0g-dev
libxcb-xkb-dev
xauth
```

If dependencies have been installed, we can now proceed on installing Ly.

Go to the cloned repository:
```
cd ly/
```
Build with Zig (since we have two versions of Zig, we will specify the exact compiler version.)
```
/usr/local/bin/zig-15/zig build
```
Then install it:
```
sudo /usr/local/bin/zig-15/zig installexe -Dinit_system=systemd
```
Enable the service:
```
systemctl enable ly.service
```

> Without the package `pkexec`, you'll be forced to use `sudo systemctl`.

Disable `getty` on TTY 2

> As per Ly's note:
> 
> Important: Because Ly runs in a TTY, you must disable the TTY service that Ly will run on, otherwise bad things will happen. For example, to disable getty spawning on TTY 2 (the default TTY on which Ly spawns), you need to execute the following command:

```
systemctl disable getty@tty2.service
```

### Do ***NOT*** reboot yet!, Ly will ***NOT*** detect River by default, so we'll have to add it first.

### Configure Ly to detect River

Create folder first for river.desktop
```
sudo mkdir -p /usr/local/share/wayland-sessions/
```

From river/contrib - copy river.desktop
```
cd river/contrib/
sudo cp river.desktop /usr/local/share/wayland-sessions/
```

Edit `/etc/ly/config.ini`
```
sudo nano /etc/ly/config.ini
```
or if using vim:
```
sudo vim /etc/ly/config.ini
```

We need to edit Line 115, To cut off the slack, we can jump onto a line number.

- For nano : `Alt + G`, then type `115` and enter. (this jumps to line 115)

- For vim : `115gg` and enter.

Remove the comment on `custom_sessions`, then replace it with:
```
/usr/local/share/wayland-sessions/
```
To edit:

- In nano:

Navigate using arrow keys, then to save, hit `Ctrl + S` and `Ctrl + X` to exit.

- In vim:

Press the `I` key for Insert, then navigate using arrow keys. Afterwards, hit `esc`, click `:` and type `wq` to save and exit.

Now, it should look like `custom_sessions = /usr/local/share/wayland-sessions/`

And then reboot. Ly should now detect River. Enjoy.

## Sources used:

- [Zig Programming Language](https://ziglang.org/)

- [Vim](https://www.vim.org/)

- [GNU Nano Cheatsheet](https://www.nano-editor.org/dist/latest/cheatsheet.html)

- [fairyglade/ly](https://github.com/fairyglade/ly)

- [Debian Packages](https://www.debian.org/distrib/packages)

- [riverwm/river](https://github.com/riverwm/river)

### To-Do List:
- [ ] Cleaner look of this guide
- [x] Add Ly Display Manager as an option to install and detect River
- [x] Complete the guide


