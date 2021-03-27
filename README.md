# Ansible Role: Squid

[![ubuntu-18](https://img.shields.io/badge/ubuntu-18.x-orange?style=flat&logo=ubuntu)](https://ubuntu.com/)
[![ubuntu-20](https://img.shields.io/badge/ubuntu-20.x-orange?style=flat&logo=ubuntu)](https://ubuntu.com/)
[![debian-9](https://img.shields.io/badge/debian-9.x-orange?style=flat&logo=debian)](https://www.debian.org/)
[![debian-10](https://img.shields.io/badge/debian-10.x-orange?style=flat&logo=debian)](https://www.debian.org/)
[![centos-7](https://img.shields.io/badge/centos-7.x-orange?style=flat&logo=centos)](https://www.centos.org/)
[![centos-8](https://img.shields.io/badge/centos-8.x-orange?style=flat&logo=centos)](https://www.centos.org/)

[![License](https://img.shields.io/badge/license-MIT%20License-brightgreen.svg?style=flat)](https://opensource.org/licenses/MIT)
[![GitHub issues](https://img.shields.io/github/issues/OnkelDom/ansible-role-squid?style=flat)](https://github.com/OnkelDom/ansible-role-squid/issues)
[![GitHub tag](https://img.shields.io/github/tag/OnkelDom/ansible-role-squid.svg?style=flat)](https://github.com/OnkelDom/ansible-role-squid/tags)
[![GitHub action](https://github.com/OnkelDom/ansible-role-squid/workflows/ansible-lint/badge.svg)](https://github.com/OnkelDom/ansible-role-squid)

## Description

Install and configure an Squid Proxy on CentOS/RHEL or Debian systems using ansible.
See: [Squid Config Snippets](https://gist.github.com/OnkelDom/e5a452b87a58ede35c8318b2188e6914)

## Requirements

- Ansible >= 3
- Community Packages
- `ansible-galaxy collection install community.general`
- `ansible-galaxy collection install ansible.posix`
- `ansible-galaxy collection install onkeldom.caddyserver`

After you have installed dante socks, you van use to following tag to only change the configuration and reload the service
```
ansible-playbook <playbook>.yml --tags squid_acls
```

## Role Variables

All variables which can be overridden are stored in [defaults/main.yml](defaults/main.yml) file as well as in table below.

| Name           | Default Value | Description                        |
| -------------- | ------------- | -----------------------------------|
| `proxy_env` | {} | Set proxy environment variables | 
| `squid_port` | 3128 | Default bind Port |
| `squid_src_acls` | [] | default source acls |
| `squid_dst_acls` | [] | default destination acls |
| `squid_ssl_ports` | [443] | default ssl ports |
| `squid_safe_ports` | [80,443] | default safe ports |
| `squid_logformat_name` | squid_json | logformat as json |
| `squid_logformat` | [defaults/main.yml#L65](defaults/main.yml#L65) | logformat as json |
| `squid_logrotate_days` | 28 | logrotate days |
| `squid_log_dir` | /var/log/squid | log folder |
| `squid_allow_firewall` | false | allow access on firewall |
| `squid_config_custom` | [] | define config params (not defaults defined) |
| `squid_config_refresh_pattern` | [] | define refresh pattern per line |
| `squid_caddy_generate_html_output` | false | generate config and acls as html file |
| `squid_caddy_webserver_path` | /var/www/ | webserver path |
| `squid_caddy_user` | caddy | webserver user name |
| `squid_caddy_group` | caddy | webserver group name |
| `squid_template` | false | override original templates |
| `squid_template_file` | squid_errors.html.j2 | squid deny page template |
| `squid_template_errors` | [defaults/main.yml#L87](defaults/main.yml#L87) | defined template files |

## Example

```yaml
---
- hosts: all
  roles:
  - onkeldom.squid
  vars:
    squid_src_acls:
      - name: client_net
        comment: Client Networks
        source: |
          10.0.0.0/8
    ## OR with source file in ansible-role-squid/proxy_{{ ansible_proxy }}
        file: client_net # (filename: acl_src_client_net.lst)
    squid_dst_acls: []
      - name: blacklist
        comment: Blacklist
        access: deny
        type: dstdomain
        deny_info: custom_access_blocked # Define Squid deny_info pages defined in squid_template_errors
        source: client_net
        file: blacklist (filename: acl_dst_blacklist.lst)
      - name: whitelist_domains
        comment: Internal Domains
        access: allow
        type: dstdomain
        source: client_net
    ## With destination file
        file: whitelist_domains (filename: acl_dst_whitelist_domains.lst)
    ## OR with destination definition
        destination: |
          .onkeldom.eu
          .cloudflare.com
```

## Contributing

See [contributor guideline](CONTRIBUTING.md).

## License

This project is licensed under MIT License. See [LICENSE](/LICENSE) for more details.
