# Josh's Wallpaper Scripts

[![Codacy Badge](https://app.codacy.com/project/badge/Grade/9eaa523a3f7d442796216da3209cd46b)](https://app.codacy.com/gh/joshbeard/walls.sh/dashboard?utm_source=gh&utm_medium=referral&utm_content=&utm_campaign=Badge_grade)

Scripts for managing desktop wallpapers.

<img align="right" width="256" height="256" src=".doc/image.jpg">

They're currently somewhat specific to my Linux desktop (arch btw) and depend
on:

* [gosimac](https://github.com/1995parham/gosimac) for downloading wallpapers
  from Bing and Usplash (e.g. via cron).
* on xorg: `xrandr` and [nitrogen](https://wiki.archlinux.org/title/Nitrogen)
* on wayland: hyprland and [swww](https://github.com/Horus645/swww) (hyprland's
  `hyprctl` command is used, but this will be changed to something more generic
  soon).

The result is random wallpapers across all connected displays, sourced from
thousands of random images at a regular interval that I can add to lists
whether running on Xorg or Wayland.

## Features

* Download wallpapers from Bing and Unsplash
* Randomly set a wallpaper per display
* Set wallpapers on demand, per-display
* Add wallpapers to a list and set from a list
* Track recent wallpapers and avoid setting them for a while
* Blacklist wallpapers
* Source images from remote server over SSH
* Supports Xorg and Wayland

## Install

* Install pre-requisite packages
    These are easy to change, but requires modifying the default config and
    possibly some of the wrapper code. See [`lib/x_xorg.sh`](lib/x_xorg.sh)
    and [`lib/x_wayland.sh`](lib/x_wayland.sh).

    On Arch with Xorg:

    ```shell
    yay -S nitrogen feh gosimac
    ```

    On Arch with Hyprland (Wayland):

    ```shell
    yay -S hyprland swww feh
    ```

* Clone to `$HOME/.local/share/wallpaper` (or somewhere)
* Modify the [config file](etc/wallpaper.cfg)
* Export the `bin` directory in `$PATH`

## Usage

```plain
walls.sh - Manage wallpaper images on X and Wayland

Download, set, randomize, blacklist and add wallpapers to lists.

Usage: walls.sh SUBCOMMAND [SUBCOMMAND OPTIONS]

Subcommands:
  help      - Show this help message.
  set       - Set wallpaper(s) and exit.
  start     - Start the wallpaper randomizer.
  blacklist - Blacklist the current wallpaper.
  download  - Download wallpapers.
  add       - Add a wallpaper to a list.
  list      - List wallpapers.
  view      - View the wallpaper set.
```

### Help

See help for the root command and subcommands with the `--help` argument:

```shell
walls.sh --help
walls.sh add --help
walls.sh blacklist --help
walls.sh download --help
walls.sh list --help
walls.sh set --help
walls.sh start --help
walls.sh view --help
```

### Setting Wallpaper

Randomize wallpapers across all detected displays and exit:

```shell
walls.sh set
```
Set a random wallpaper on all displays and exit:

```shell
walls.sh set --once
```

Set a wallpaper on display 2 and exit:

```shell
walls.sh set -d 2 --once
```

Set wallpapers at an interval:

```shell
walls.sh set --interval 600
```

### Using Lists

Save the current wallpaper on display 1 to a list called "nature":

```shell
walls.sh add 1 nature
```

Set a wallpaper from a list:

```shell
walls.sh set -d 2 --once --list nature
```

View lists:

```shell
walls.sh list         # view a list of lists
walls.sh list nature  # view the files in the 'nature' list
```

### Blacklisting

Since this downloads a bunch of random images from the Internet, it may be
necessary to 'blacklist' an image to prevent it from being used as a wallpaper
in the future. It won't prevent its download, but it'll be deleted immediately
and if it is downloaded again.

```shell
walls.sh blacklist 0  # blacklist the wallpaper on display 0
```

### Downloading

```shell
walls.sh download
```

### Viewing

```shell
walls.sh view 0
```

### Remote Source

Set the `remote` config option:

```shell
remote="ssh://foo.bar:/path/to/wallpapers
```

The images will get cached to `${walls_var_dir}/remote`.

To use a remote source in a crontab, make sure SSH is configured correct. E.g.

```plain
*/10 * * * * pgrep XORG && DISPLAY=:0 XDG_SESSION_TYPE=x11 SSH_AUTH_SOCK=/path/to/ssh_agent nice -n 19 walls.sh set --once
```
