Ansible role: Systemd-named-netns
=================================

Install and configure systemd-named-netns.
systemd-named-netns enables you to manage network namespaces
as systemd services. This includes naming the network
namespace as well as configuring the network settings for the
namespace. These named network namespaces can be attached to
existing systemd services to have them run inside the network
namespace. For more details, see:
https://github.com/wandnz/systemd-named-netns/

Role Variables
--------------

```
ENTRY POINT: main - Install and configure systemd-named-netns

        systemd-named-netns enables you to manage network namespaces
        as systemd services. This includes naming the network
        namespace as well as configuring the network settings for the
        namespace. These named network namespaces can be attached to
        existing systemd services to have them run inside the network
        namespace. For more details, see:
        https://github.com/wandnz/systemd-named-netns/

OPTIONS (= is mandatory):

- netns
        List of network namespaces and their configuration
        [Default: (null)]
        elements: dict
        type: list

        OPTIONS:

        = config
            List of interfaces to attach to the namespace and their
            configuration

            elements: dict
            type: list

            OPTIONS:

            - devname_inside
                Name of the interface inside the ns
                [Default: (null)]
                type: str

            - devname_outside
                Name of the interface outside the ns
                [Default: (null)]
                type: str

            - dhcpv4
                If set to 1, use DHCPv4 for assigning an IPv4 address
                and gateway
                [Default: (null)]
                type: int

            - gateway_v4
                Default IPv4 gateway
                [Default: (null)]
                type: str

            - gateway_v6
                Default IPv6 gateway
                [Default: (null)]
                type: str

            - interface
                Name of the interface or bridge to attach to the
                network namespace, required if type = bridge or raw
                [Default: (null)]
                type: str

            - ipaddr_outside
                Static IPv4 address for the interface outside the ns
                [Default: (null)]
                type: str

            - ipaddr_v4
                Static IPv4 address for the interface
                [Default: (null)]
                type: str

            - ipaddr_v6
                Static IPv6 address for the interface
                [Default: (null)]
                type: str

            - macaddr
                MAC address for the interface
                [Default: (null)]
                type: str

            = type
                Interface type
                (Choices: bridge, raw, veth)
                type: str

        - files
            List of configuration files and their contents to add to
            the namespace's /etc/ directory
            [Default: (null)]
            elements: dict
            type: list

            OPTIONS:

            = contents
                Contents for the file

                type: str

            = filename
                Filename (excluding /etc/)

                type: str

        = name
            Name for the namespace

            type: str

- netns_apt_key_fingerprint
        Fingerprint for apt signing key
        [Default: (null)]
        type: str

- wand_apt_key_fingerprint
        Fingerprint for apt signing key for old WAND bintray repo
        [Default: (null)]
        type: str
```

Installation
------------

This role can either be installed manually with the ansible-galaxy CLI tool:

    ansible-galaxy install git+https://github.com/wandansible/systemd_named_netns,main,wandansible.systemd_named_netns
     
Or, by adding the following to `requirements.yml`:

    - name: wandansible.systemd_named_netns
      src: https://github.com/wandansible/systemd_named_netns

Roles listed in `requirements.yml` can be installed with the following ansible-galaxy command:

    ansible-galaxy install -r requirements.yml

Example Playbook
----------------

    - hosts: netns_hosts
      roles:
         - role: wandansible.systemd_named_netns
           become: true
           vars:
             netns:
               - name: ipv6-only
                 config:
                   - type: raw
                     interface: eno2
                     ipaddr_v6: "2001:db8::1/64"
                     gateway_v6: "2001:db8::ffff"
                 files:
                   - filename: resolv.conf
                     contents: |
                       nameserver 2001:4860:4860::8888
                       nameserver 2001:4860:4860::8844
