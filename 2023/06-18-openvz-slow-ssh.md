# Using a OpenVZ VPS and experiencing slowed down SSH first response?

## TLDR

1. Disable UsePAM in `sshd_config` (recommended, since OpenVZ VPS is not designed to be used with cgroups), or
2. Install `libpam-cgfs`

## Why

```
Jun 18 10:21:06 systemd[652]: Failed to create /user.slice/user-1000.slice/user@1000.service/init.scope control group: Permission denied
Jun 18 10:21:06 systemd[652]: Failed to allocate manager object: Permission denied
```

Perhaps libpam can't access cgfs in a container-like environment. However, `libpam-cgfs` can make this work.

## Or better?

Use a KVM machine. Yes.
