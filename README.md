# swayrot8

A simple auto-rotation daemon for tablets and 2-in-1 laptops using [SwayWM](https://swaywm.org/).

`swayrot8` integrates with `monitor-sensor` (`iio-sensor-proxy`) which presents sensor data as an event stream over a simple text protocol.

## Features

Provides efficient rotation support for tablets and 2-in-1 laptops:

- `swayrot8` only parses accelerometer events (using `--accel` flag),
- `swayrot8` runs only when there's a relevant event to process, and
- `swayrot8` doesn't suffer from timing issues related to sleep delays.

A user-level `systemd` unit, `sway-rot8.service`, is included for reliable operation, and easy integration with Sway.

## Installation

`sway-rot8` depends on:

- SwayWM
- iio-sensor-proxy

### Fedora Install

Pre-built packages are available for Fedora-based systems:

```bash
sudo dnf copr enable -y ehsanj/coprstones
sudo dnf install -y swayrot8
```

### Manual Install

TBD

## Usage

Invoke `sway-rot8.service` from your `Sway` config:

```config
exec systemctl --user restart sway-rot8.service
```

Although, you can independently enable `sway-rot8.service`, the recommended approach is to integrate it with `Sway` (as shown above).

### Default Settings

The service defines the following environment variables to control output and transforms:

| Env. variable    | Default |
| ---------------- | ------- |
| OUTPUT_NAME      | eDP-1   |
| TRANSFORM_NORMAL | 0       |
| TRANSFORM_BOTTOM | 180     |
| TRANSFORM_RIGHT  | 90      |
| TRANSFORM_LEFT   | 270     |

### Customization

Since sensor locations vary widely between laptops, one laptop's notion of _right_ could very well be another's _left_. That is to say, you might find yourself needing to adjust the transform values. Here's how to do it, assuming you need to swap left and right transforms.

Start by editing the service unit:

```bash
systemctl --user edit sway-rot8.service
```

Above the `### Edits below this comment will be discarded` line, provide new values for the relevant environment variables:

```systemd
Environment=TRANSFORM_RIGHT=270
Environment=TRANSFORM_LEFT=90
```

Save the file, and exit. Restart the service (or your system) for the changes to take effect:

```bash
systemctl --user daemon-reload
systemctl --user restart sway-rot8.service
```
