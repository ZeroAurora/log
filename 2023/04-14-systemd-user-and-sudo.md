# Don't use `systemd --user` with sudo...

because it simply won't work.

## Instead, use...

```sh
# install systemd-container (in Debian) or similar
sudo machinectl shell --uid [WHOEVER]
```

## refs

- https://unix.stackexchange.com/q/346841
- https://unix.stackexchange.com/q/423632
- https://github.com/systemd/systemd/issues/7451#issuecomment-346787237
