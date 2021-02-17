# nerdctl directory layout

## Data
### `<DATAROOT>`
e.g. `/var/lib/nerdctl`

Can be overriden with `nerdctl --data-root=<DATAROOT>` flag.

The directory is solely managed by nerdctl, not by containerd.
The directory has nothing to do with containerd data root `/var/lib/containerd`.

### `<DATAROOT>/<ADDRHASH>`
e.g. `/var/lib/nerdctl/1935db59`

`1935db9` is from `$(echo -n "/run/containerd/containerd.sock" | sha256sum | cut -c1-8)`

This directory is also called "data store" in the implementation.

### `<DATAROOT>/<ADDRHASH>/containers/<NAMESPACE>/<CID>`
e.g. `/var/lib/nerdctl/1935db59/containers/default/c4ed811cc361d26faffdee8d696ddbc45a9d93c571b5b3c54d3da01cb29caeb1`

Files:
- `resolv.conf`: mounted to the container as `/etc/resolv.conf`
- `<CID>-json.log`: used by `nerdctl logs`
- `oci-hook.*.log`: logs of the OCI hook

### `<DATAROOT>/<ADDRHASH>/names/<NAMESPACE>`
e.g. `/var/lib/nerdctl/1935db59/names/default`

Files:
- `<NAME>`: contains the container ID (CID). Represents that the name is taken by that container. 

Files must be operated with a `LOCK_EX` lock against the `<DATAROOT>/<ADDRHASH>/names/<NAMESPACE>` directory.

### `<DATAROOT>/<ADDRHASH>/etchosts/<NAMESPACE>/<CID>`
e.g. `/var/lib/nerdctl/1935db59/etchosts/default/c4ed811cc361d26faffdee8d696ddbc45a9d93c571b5b3c54d3da01cb29caeb1`

Files:
- `hosts`: mounted to the container as `/etc/hosts`
- `meta.json`: metadata

Files must be operated with a `LOCK_EX` lock against the `<DATAROOT>/<ADDRHASH>/etchosts` directory.

## CNI

### `<NETCONFPATH>`
e.g. `/etc/cni/net.d`

Can be overriden with `nerdctl --cni-netconfpath=<NETCONFPATH>` flag and environment variable `$NETCONFPATH`.

Files:
- `nerdctl-<NWNAME>.conflist`: CNI conf list created by nerdctl