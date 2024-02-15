# DHCP Configuration Module

DHCP configuration module implements DHCP clients, servers, and relays for IPv4 and IPv6.

```eval_rst
.. contents:: Table of Contents
   :depth: 2
   :local:
   :backlinks: none
```

(dhcp-platform)=
## Platform Support

DHCP clients are supported on these platforms:

| Operating system      | IPv4 | IPv6 |
| --------------------- | :-: | :-: |
| Arista EOS            | ✅ [❗](caveats-eos) |  ❌  |
| Cisco IOSv            | ✅  | ✅  |
| Cumulus Linux         | ✅  | ✅  | 

DHCP servers are supported on these platforms: 

| Operating system      | IPv4 | IPv6 |
| --------------------- | :-: | :-: |
| Arista EOS            | ✅  | ✅  |
| Cisco IOSv            | ✅  | ✅  |
| dnsmasq               | ✅  | ✅  |

DHCP relays still need to be implemented.

(dhcp-parameters)=
## Node Parameters

The DHCP module supports the following node parameters:

* **dhcp.server** (boolean) -- the device should run a DHCP server
* **dhcp.vrf** (boolean) -- the DHCP server should be VRF-aware

## Interface Parameters

* To start a DHCP client on an interface, set **ipv4** or **ipv6** address to **dhcp**.
* To start a DHCP relay on an interface, set **dhcp.server** interface parameter to a list of DHCP servers.
* To enable inter-VRF DHCP relaying, set the **dhcp.vrf** interface parameter to the name of the destination VRF (or **global**).

## Implementation Notes

* The **dhcp** module is automatically enabled for nodes with DHCP clients. You don't have to specify it in the **module** list.
* You must enable the **dhcp** module on DHCP relays and servers.
* *dnsmasq* always uses the **dhcp** module and is configured as a DHCP server. There's no need to set the **module** or **dhcp.server** node variable.

## Examples

The following topology starts IPv4 DHCP clients on Cumulus Linux and Cisco IOSv and a DHCP server on Arista EOS:

```
nodes:
  r1:
    device: iosv
  r2:
    device: cumulus
  srv:
    device: eos
    module: [ dhcp ]
    dhcp.server: True

links:
- r1:
    ipv4: dhcp
  r2:
    ipv4: dhcp
  srv:
```

The following topology uses *dnsmasq* DHCP server and starts IPv4 and IPv6 DHCP clients on Arista EOS:

```
provider: clab

addressing.lan.ipv6: 2001:db8:cafe::/48

nodes:
  client:
    device: eos
  server:
    device: dnsmasq

links:
- client:
    ipv4: dhcp
    ipv6: dhcp
  server:
```