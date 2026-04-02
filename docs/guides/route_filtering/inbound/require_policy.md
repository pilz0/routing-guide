---
tags:
  - BIRD missing
  - Cisco missing
  - Huawei VRP missing
  - Junos missing
  - Mikrotik missing
  - OpenBGPD missing
  - RtBrick RBFS missing
---

# Require policy to start a BGP session

Some routers process configuration commands  in real time as you type them. Others require you to *commit* your configuration when you are ready, before the router will process the configuration changes you have entered.

If you start to enter the configuration commands to setup a BGP session and the router processes the configuration commands in real time, the session will be established as soon as the minimum required configuration is entered, regardless of whether you have entered *all* the intended configuration or not.

This can be dangerous because as soon as the minimum required configuration to establish a BGP session is entered, the session might establish. If the minimum required configuration does not include any configuration relating to filtering, you will accept all prefixes the neighbor sends to you. Vice versa, you will announce every prefix in your own BGP table to that neighbor. Most of the time, this is undesirable because you might accept invalid prefixes from the neighbor or announce prefixes which should remain internal to your network.

[RFC8212](https://www.rfc-editor.org/rfc/rfc8212.html) defines a requirement for routers, that the operator *must* configure an import and an export policy on any external BGP session, before the router will establish the session.

The compliance of BGP implementations to RFC8212 is tracked in [this repository](https://github.com/bgp/RFC8212).

Configuration examples:

=== "Cisco IOS XR"
    No configuration necessary, RFC8212 is supported by default.

=== "FRRouting"
    This is enabled by default for traditional configuration.
    ```
    router bgp 64500
      bgp ebgp-requires-policy
    ```

=== "Nokia SR OS classic CLI"
    ```
    /configure router "Base" bgp
            ebgp-default-reject-policy import export
    ```

=== "VyOS (>= 1.4)"
    VyOS has two modes (operational and configuration mode). Enter configuration mode with
    `configure` to make changes. Use `commit` to apply them and `save` to keep them after reboot.

    ```
    set protocols bgp parameters ebgp-requires-policy
    ```

=== "Arista EOS legacy"
    ```
    router bgp 64500
       address-family ipv4
          bgp missing-policy direction in action deny
          bgp missing-policy direction out action deny
       !
       address-family ipv6
          bgp missing-policy direction in action deny
          bgp missing-policy direction out action deny
    ```

=== "Arista EOS RCF"
    ```
    router bgp 64500
       address-family ipv4
          bgp missing-policy direction in action deny
          bgp missing-policy direction out action deny
       !
       address-family ipv6
          bgp missing-policy direction in action deny
          bgp missing-policy direction out action deny
    ```
