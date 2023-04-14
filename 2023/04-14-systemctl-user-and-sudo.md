# Don't use `systemctl --user` with `sudo -u [WHOEVER]`...

because it simply won't work：

```sh
sudo -u somebody -i
## or
su somebody
## and do...
systemctl --user {command}
## you'll get...
# Failed to connect to bus: No medium found
```

## Instead, use...

```sh
# install systemd-container (in Debian) or similar
sudo machinectl shell --uid [WHOEVER]
# do whatever thing you want to do with `systemctl --user`
```

## refs

- https://unix.stackexchange.com/q/346841
- https://unix.stackexchange.com/q/423632
- https://github.com/systemd/systemd/issues/7451#issuecomment-346787237
