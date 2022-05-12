# kwux_service_consul_acls

This playbook configures Consul ACLs, and stores details and configuration
within a Vault instance.

As such, it has a direct dependency (in this configuration) on a
Hashicorp Vault installation, hence, it is decoupled from the kwuxlab playbook
which handles the initial Consul installation/configuration.

## Requirements

### External

1. Consul
   1. Must be installed/configured on the target hosts/inventory.
   1. Master token (see `example_environment.yml`).
      - The generated master token can be obtained directly from a consul server
        host, by running:
        
        ```sh
        cat /var/lib/consul/config.json | grep master
        ```
    2. Consul DNS: must be configured and working properly
2. Vault
   1. Vault must be installed and bootstrapped on all primary datacenter
      server nodes.
   2. A Vault token with access to the `kv-v1` target namespace must be
      generated. In a pinch, you can use a Vault master management token.

### Internal

#### Playbook Variables

Configure Consul ACL details as shown in `vars/example_environment.yml`.

The playbook will look for a credential file in `vars/` that matches the ansible
environment, *e.g.*:

- `production_environment.yml`
- `test_environment.yml`
- etc.

One way to secure these files is to encrypt them files with `ansible-vault`:

```sh
KWUXLAB_ENV="production"; ansible-vault encrypt \
  --vault-id password_file."${KWUXLAB_ENV}".txt \
  playbooks/kwuxlab_ansible_service_consul_acls/vars/"${KWUXLAB_ENV}"_environment.yml
```

Which requires that `password_file."${KWUXLAB_ENV}".txt`
(read: `password_file.production.txt` in the above example) exists in your
current directory.