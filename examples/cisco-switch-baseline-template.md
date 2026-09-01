# Cisco IOS VLAN and Port Templates

These are documentation-only examples derived from the patterns used in my lab. They contain fictional VLAN names, interface numbers, and addresses. Review and adapt all settings for the target switch, spanning-tree design, and security requirements before using them.

## VLAN definitions

```ios
vlan 10
 name MGMT
vlan 20
 name TRUSTED
vlan 30
 name IOT
vlan 40
 name GUEST
vlan 60
 name SERVICES
vlan 999
 name UNUSED
```

## Standard endpoint access port

```ios
interface GigabitEthernet1/0/10
 description TRUSTED-ENDPOINT
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
 spanning-tree bpduguard enable
```

Use this pattern only for a true endpoint port. BPDU Guard is an edge-port protection and should be reviewed before connecting infrastructure that may pass Layer 2 control traffic.

## Restricted infrastructure trunk

```ios
interface GigabitEthernet1/0/20
 description ACCESS-POINT-UPLINK
 switchport mode trunk
 switchport trunk native vlan 10
 switchport trunk allowed vlan 10,20,30,40
 no spanning-tree portfast
 no spanning-tree bpduguard enable
```

The trunk permits only the management and client VLANs required by the access point. Limiting allowed VLANs reduces the chance of unintentionally extending unrelated networks to an infrastructure device.

## Unused port

```ios
interface range GigabitEthernet1/0/1 - 9
 description UNUSED
 switchport mode access
 switchport access vlan 999
 shutdown
```
