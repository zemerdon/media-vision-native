# Media Vision — Native Releases

Official native Debian 13 Proxmox LXC binaries and versioned installers. Source development remains in an independent private repository.

## Permanent installer

```bash
bash -c "$(curl -fsSL https://github.com/zemerdon/media-vision-native/releases/latest/download/bootstrap-native.sh)"
```

The GitHub latest release is the supported normal/stable native baseline. Release assets are immutable versioned software distributions with SHA256 manifests. Do not install these on a host with an existing Media Vision LXC of the same product.

This is a clean new release repository, not a fallback to the old release history. No historical release archives or user data are copied here.
