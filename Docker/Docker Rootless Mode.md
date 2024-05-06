# Run the Docker daemon as a non-root user on Ubuntu
## Prerequisites

Create a dedicated user with sudo privileges.

Install Docker Engine using the docker apt repository

## Install

Installation de `newuidmap` et `newgidmap`

```
sudo apt update
sudo apt install uidmap
```

```
newuidmap --version
newgidmap --version
```

Ensure that the user has 65536 UID/GID

```shell
cat /etc/subuid
omdocker:165536:65536

cat /etc/subgid
omdocker:165536:65536
```

Install `dbus-user-session` package if not installed

```shell
apt-get install docker-ce-rootless-extras
```
>  The AppArmor profile for `rootlesskit` is already bundled with the `apparmor` deb package.

If the system-wide Docker daemon is already running, consider disabling it:

```shell
sudo systemctl disable --now docker.service docker.socket
```

Run `dockerd-rootless-setuptool.sh install` as a non-root user to set up the daemon :

```shell
dockerd-rootless-setuptool.sh install
```

```shell
...
Created symlink /home/omdocker/.config/systemd/user/default.target.wants/docker.service → /home/omdocker/.config/systemd/user/docker.service.
[INFO] Installed docker.service successfully.
[INFO] To control docker.service, run: `systemctl --user (start|stop|restart) docker.service`
[INFO] To run docker.service on system startup, run: `sudo loginctl enable-linger omdocker`

[INFO] Creating CLI context "rootless"
Successfully created context "rootless"
[INFO] Using CLI context "rootless"
Current context is now "rootless"

[INFO] Make sure the following environment variable(s) are set (or add them to ~/.bashrc):
export PATH=/usr/bin:$PATH

[INFO] Some applications may require the following environment variable too:
export DOCKER_HOST=unix:///run/user/1001/docker.sock
```

## Usage 

The systemd unit file is installed as `~/.config/systemd/user/docker.service`.

Use `systemctl --user` to manage the lifecycle of the daemon:

```shell
systemctl --user start docker
```

To launch the daemon on system startup, enable the systemd service and lingering:

```shell
systemctl --user enable docker
sudo loginctl enable-linger $(whoami)
```

> Starting Rootless Docker as a systemd-wide service (`/etc/systemd/system/docker.service`) is not supported, even with the `User=` directive.


Remarks about directory paths:

- The socket path is set to `$XDG_RUNTIME_DIR/docker.sock` by default. `$XDG_RUNTIME_DIR` is typically set to `/run/user/$UID`.
- The data dir is set to `~/.local/share/docker` by default. The data dir should not be on NFS.
- The daemon config dir is set to `~/.config/docker` by default. This directory is different from `~/.docker` that is used by the client.

## Unlocking limitations

Exposing privileged port:

To expose privileged ports (< 1024), set `CAP_NET_BIND_SERVICE` on `rootlesskit` binary and restart the daemon.

```shell
sudo setcap cap_net_bind_service=ep $(which rootlesskit)
systemctl --user restart docker
```

