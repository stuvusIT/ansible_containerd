# ansible_containerd

This role installs and configures [containerd](https://github.com/containerd/containerd) on Ubuntu
or Debian. It uses the pre-built version of containerd found in the Docker apt repository.

## Requirements

[Ubuntu](https://www.ubuntu.com/) or [Debian](https://www.debian.org/) on a machine with `amd64`
architecture.

## Role Variables

| Name                | Default                     | Description                         |
| :------------------ | :-------------------------- | :---------------------------------- |
| `containerd_config` | _see `./defaults/main.yml`_ | See [Configuration](#configuration) |

## Configuration

containerd is configured through the config file `/etc/containerd/config.toml`.
This role converts the content of the `containerd_config` variable to TOML and writes it to the
config file.
In order to customize the config, copy this default config (delived with this Ansible role) from
`./defaults/main.yml` to your playbook and edit it.
For an example see [Example Playbook](#example-playbook).

## Example Playbook

The following example playbook assumes that you cloned this role to `roles/containerd`
(i.e. the name of the role is `containerd` instead of `ansible_containerd`).

```yml
- hosts: kube01
  roles:
    - role: containerd
      containerd_config:
        version: 2
        root: /var/lib/containerd
        state: /run/containerd
        plugin_dir: ""
        disabled_plugins: []
        required_plugins: []
        oom_score: 0
        grpc:
          address: /run/containerd/containerd.sock
          tcp_address: ""
          tcp_tls_cert: ""
          tcp_tls_key: ""
          uid: 0
          gid: 0
          max_recv_message_size: 16777216
          max_send_message_size: 16777216
        ttrpc:
          address: ""
          uid: 0
          gid: 0
        debug:
          address: ""
          uid: 0
          gid: 0
          level: ""
        metrics:
          address: ""
          grpc_histogram: false
        cgroup:
          path: ""
        timeouts:
          "io.containerd.timeout.shim.cleanup": 5s
          "io.containerd.timeout.shim.load": 5s
          "io.containerd.timeout.shim.shutdown": 3s
          "io.containerd.timeout.task.state": 2s
        plugins:
          "io.containerd.gc.v1.scheduler":
            pause_threshold: 0.02
            deletion_threshold: 0
            mutation_threshold: 100
            schedule_delay: 0s
            startup_delay: 100ms
          "io.containerd.grpc.v1.cri":
            disable_tcp_service: true
            stream_server_address: 127.0.0.1
            stream_server_port: "0"
            stream_idle_timeout: 4h0m0s
            enable_selinux: false
            sandbox_image: k8s.gcr.io/pause:3.1
            stats_collect_period: 10
            enable_tls_streaming: false
            max_container_log_line_size: 16384
            disable_cgroup: false
            disable_apparmor: false
            restrict_oom_score_adj: false
            max_concurrent_downloads: 3
            disable_proc_mount: false
            containerd:
              snapshotter: overlayfs
              default_runtime_name: runc
              no_pivot: false
              default_runtime:
                runtime_type: ""
                runtime_engine: ""
                runtime_root: ""
                privileged_without_host_devices: false
              untrusted_workload_runtime:
                runtime_type: ""
                runtime_engine: ""
                runtime_root: ""
                privileged_without_host_devices: false
              runtimes:
                runc:
                  runtime_type: io.containerd.runc.v1
                  runtime_engine: ""
                  runtime_root: ""
                  privileged_without_host_devices: false
                  options:
                    SystemdCgroup: true
            cni:
              bin_dir: /opt/cni/bin
              conf_dir: /etc/cni/net.d
              max_conf_num: 1
              conf_template: ""
            registry:
              mirrors:
                "docker.io":
                  endpoint:
                    - https://registry-1.docker.io
            x509_key_pair_streaming:
              tls_cert_file: ""
              tls_key_file: ""
          "io.containerd.internal.v1.opt":
            path: /opt/containerd
          "io.containerd.internal.v1.restart":
            interval: 10s
          "io.containerd.metadata.v1.bolt":
            content_sharing_policy: shared
          "io.containerd.monitor.v1.cgroups":
            no_prometheus: false
          "io.containerd.runtime.v1.linux":
            shim: containerd-shim
            runtime: runc
            runtime_root: ""
            no_shim: false
            shim_debug: false
          "io.containerd.runtime.v2.task":
            platforms:
              - linux/amd64
          "io.containerd.service.v1.diff-service":
            default:
              - walking
          "io.containerd.snapshotter.v1.devmapper":
            root_path: ""
            pool_name: ""
            base_image_size: ""
```
