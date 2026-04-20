# Ansible Role: UFW

An Ansible role to install and configure UFW (Uncomplicated Firewall) on Debian and Ubuntu systems. It manages the default firewall policy, application profiles, IP blocking, custom rules, and logging.

The role is tested against the latest Ubuntu LTS and Debian releases but may work with other Debian-derived distributions.

**Note: SSH (port 22/tcp) is always allowed before the firewall is enabled to prevent accidental lockouts. Review all rules carefully before applying to production systems.**

## Role Variables

Available variables are listed below, along with some sample values. See `defaults/main.yaml` for a full list of values.

```yaml
ufw_default_policy: allow
```

The default UFW policy applied to incoming traffic. Common values are `allow`, `deny`, and `reject`. Defaults to `allow`.

```yaml
ufw_logging: 'off'
```

The UFW logging level. Valid values are `on`, `off`, `low`, `medium`, `high`, and `full`. Defaults to `off`.

```yaml
ufw_apps:
  - OpenSSH
  - Nginx Full
```

A list of UFW application profile names to allow. These correspond to profiles defined in `/etc/ufw/applications.d/`. Defaults to an empty list.

On Ubuntu packages often contain UFW applications profiles which can be used. On Debian, the `ufw` package itself provides various predefined application profiles which can be used. See the `/etc/ufw/applications.d/` directory for what profiles are available.

```yaml
ufw_block_cidrs:
  - 10.110.0.0/24
  - 198.51.100.0/24
```

A list of IP CIDRs to block. Each item should be a valid IP CIDR string. Deny rules for these CIDRs are inserted at the top of the rule chain so they take precedence. Defaults to an empty list.

```yaml
ufw_rules:
  - rule: allow
    direction: in
    interface: eth0
    proto: tcp
    src: any
    from_port: '80'
    dest: 10.0.0.1
    to_port: '80'
```

A list of specific UFW rules to apply. Each item supports the following fields:

| Field       | Required | Description                                  |
|-------------|----------|----------------------------------------------|
| `rule`      | yes      | `allow`, `deny`, `reject`, etc.              |
| `direction` | yes      | `in`, `out`, or `routed`                     |
| `interface` | no       | Network interface (e.g., `eth0`)             |
| `proto`     | no       | Protocol: `tcp`, `udp`, `any`, etc.          |
| `src`       | no       | Source IP or CIDR                            |
| `from_port` | no       | Source port                                  |
| `dest`      | no       | Destination IP or CIDR                       |
| `to_port`   | no       | Destination port                             |

Defaults to an empty list.

## Example Playbook

```yaml
- hosts: servers
  roles:
    - role: ufw
      vars:
        ufw_default_policy: deny
        ufw_logging: low
        ufw_apps:
          - OpenSSH
        ufw_block_cidrs:
          - 203.0.113.0/24
        ufw_rules:
          - rule: allow
            direction: in
            proto: tcp
            to_port: '443'
          - rule: allow
            direction: in
            proto: tcp
            to_port: '80'
```

# License

[MIT No Attribution](https://opensource.org/license/mit-0)

# Author

This Ansible role was created in 2025 by Eirik Nicolai Synnes and published as an Open Source project in 2026.

# Acknowledgements

This role uses Docker images created by Jeff Geerling for testing. The approach for developing this role, as well how to use GitHub Actions to manage it, is inspired by his work. You can find him on GitHub at https://github.com/geerlingguy.
