# swayrot8

A simple auto-rotation daemon for tablets and 2-in-1 laptops using [SwayWM](https://swaywm.org/).

`swayrot8` integrates with `monitor-sensor` (`iio-sensor-proxy`) which presents sensor data as an event stream over a simple text protocol.

## Features

Provides efficient rotation support for tablets and 2-in-1 laptops:

- `swayrot8` only parses accelerometer events (using `--accel` flag),
- `swayrot8` runs only when there's a relevant event to process, and
- `swayrot8` doesn't suffer from timing issues related to sleep delays.

A user-level `systemd` unit, `sway-rot8.service`, is included for reliable operation, and easy integration with Sway.

> [!NOTE]
> Non-systemd operation is also supported out of the box.

## Requirements

- SwayWM
- `iio-sensor-proxy` (provides `monitor-sensor`)

## Installation

### Fedora Install

Pre-built packages are available for Fedora-based systems:

```bash
sudo dnf copr enable -y ehsanj/coprstones
sudo dnf install -y swayrot8
```

### Manual Install

```bash
# Clone this repo
git clone https://github.com/ehsanullahjan/swayrot8

# Set up daemon
sudo cp swayrot8/swayrot8 /usr/libexec/
sudo cp swayrot8/sway-rot8.service /usr/lib/systemd/user/
sudo chmod +x /usr/libexec/swayrot8

# Copy example config
sudo mkdir -p /etc/swayrot8
sudo cp swayrot8/config.example /etc/swayrot8/config

# Reload systemd
systemctl --user daemon-reload
```

## Usage

Add `sway-rot8.service` to your `Sway` config to start the rotation daemon on startup:

```config
exec systemctl --user restart sway-rot8.service
```

While enabling `sway-rot8.service` independently might be possible, it's strongly recommended to integrate the service with `Sway` (as shown above) to avoid potential issues accessing `$SWAYSOCK` and `$WAYLAND_DISPLAY` from `systemd`.

If you want to use `swayrot8` without `systemd`, add this instead to your `Sway` config:

```config
exec /usr/libexec/swayrot8
```

> [!WARNING]
> It's recommended to pair `swayrot8` with a process manager for fault tolerance.

The daemon will start automatically on system startup. However, after initial install while Sway is running, you'd need to launch the daemon manually to avoid restarting the system.

## Configuration

The daemon comes with defaults that should work for most systems:

| Setting Name     | Default Value |
| ---------------- | ------------- |
| OUTPUT_NAME      | eDP-1         |
| TRANSFORM_NORMAL | 0             |
| TRANSFORM_BOTTOM | 180           |
| TRANSFORM_RIGHT  | 90            |
| TRANSFORM_LEFT   | 270           |

> [!NOTE]
> Example config `/etc/swayrot8/config` can be copied to `~/.config/swayrot8/config` to override default settings.

## Customization

Since sensor locations vary widely between laptops, one laptop's notion of _right_ could very well be another's _left_. That is to say, you might find yourself needing to adjust the transform values.

Let's say you need to swap left and right transforms.

- Copy `/etc/swayrot8/config` to `~/.config/swayrot8/config`.
- Edit `~/.config/swayrot8/config` as follows:

```config
TRANSFORM_RIGHT=270
TRANSFORM_LEFT=90
```

Save the file, and restart the service (or your system) for the changes to take effect:

```bash
systemctl --user restart sway-rot8.service
```

## License

MIT - see [LICENSE](LICENSE) for details.
