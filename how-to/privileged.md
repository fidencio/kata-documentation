# Privileged Kata Containers

Kata Containers supports creation of containers that are "privileged" (i.e. have additional capabilities and access
that is not normally granted).

* [Warnings](#warnings)
    * [Host Devices](#host-devices)
        * [Containerd and CRI](#containerd-and-cri)

## Warnings

**Warning:** Whilst this functionality is supported, it can decrease the security of Kata Containers if not configured 
correctly.

### Host Devices

By default, when privileged is enabled for a container, all the `/dev/*` block devices from the host are mounted
into the guest. This will allow the privileged container inside the Kata guest to gain access to mount any block device 
from the host, a potentially undesirable side-effect that decreases the security of Kata.

The following sections document how to configure this behavior in different container runtimes.

#### Containerd and CRI

The Containerd CRI allows configuring the privileged host devices behavior for each runtime in the CRI config. This is
done with the `privileged_without_host_devices` option. Setting this to `true` will disable hot plugging of the host 
devices into the guest, even when privileged is enabled.

See below example config:

```toml
[plugins]
  [plugins.cri]
    [plugins.cri.containerd]
       [plugins.cri.containerd.runtimes.runc]
         runtime_type = "io.containerd.runc.v1"
         privileged_without_host_devices = false
       [plugins.cri.containerd.runtimes.kata]
         runtime_type = "io.containerd.kata.v2"
         privileged_without_host_devices = true
         [plugins.cri.containerd.runtimes.kata.options]
           ConfigPath = "/opt/kata/share/defaults/kata-containers/configuration.toml"
```

 - [Kata Containers with Containerd and CRI documentation](how-to-use-k8s-with-cri-containerd-and-kata.md)
 - [Containerd CRI config documentation](https://github.com/containerd/cri/blob/master/docs/config.md)
