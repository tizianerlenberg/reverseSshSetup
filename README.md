# reverseSshSetup

## Execute Ansible Playbook

Execute the following command and let the magic happen:

```bash
ansible-playbook -i inventory/hosts main.yml
```

## Add a Remote Server / Set the Remote Server

Only one remote server can be used at a time. However, you can prepare multiple
remote servers and quickly switch between them.

To add a new remote server, simply add it to `inventory/hosts` under `[remote]`:

```
[remote]
remote1 ansible_host=remote1.example.com ansible_port=22 ansible_user=<user>
remote2 ansible_host=remote2.example.com ansible_port=22 ansible_user=<user>

# ...
```

To specify which remote server should be used when executing the Ansible
playbook, update the `remote_host` attribute in `group_vars/locals.yml`:

```yml
---
remote_host: "remote1"
```

This will use *remote1* which can be reached at *remote1.example.com*.

## Add a Local Server

To add a new local server, simply add it to `inventory/hosts` under `[locals]`:

```
# ...

[locals]
local1 ansible_host=<ip_or_fqdn_to_local_server> ansible_port=22 ansible_user=<user>
local2 ansible_host=<ip_or_fqdn_to_local_server> ansible_port=22 ansible_user=<user>
```

... and create a new `.yml`-file in `host_vars/` containing the following
content:

```yml
---
local_config:
  - local_port: 22
    remote_port: 6022
  - local_port: 80
    remote_port: 6080
fqdn: local1.example.com

```

List all ports that should be routed unter `local_config`.

You can remove the `fqdn` attribute if the file is named after the fqdn that
links to the server (in this example *local1.example.com*).

With this configuration, the local server will connect the following ports to
the remote server via reverse ssh:

- 22 (local) -> 6022 (remote)
- 80 (local) -> 6080 (remote)

You should now be able to open *local1.example.com* in your webbrowser and see
the website hosted on the local server (if a webserver is running).

You should also be able to execute `ssh <user>@local1.example.com` to connect
to the local server (through the remote server).

*You could theoretically also access the website at remote1.example.com:6080 .*
*Same thing with ssh. Why the easy way when there is also a hard way ^^*
