# Campus-network-design
A Cisco Packet Tracer campus network project covering VLANs, inter-VLAN routing, DHCP, trunking, ACLs and troubleshooting.

# Campus Network Design and Implementation

## Project Overview

I designed and implemented a simulated campus network in Cisco Packet Tracer
to practise VLAN configuration, IP addressing, inter-VLAN routing, DHCP and
Access Control Lists (ACLs).

My goal was to create a network that separates Engineering, Administration
and Student devices into different logical networks while allowing
controlled communication between them.

This project helped me understand how a campus network can be organized,
configured and tested, as well as how to troubleshoot connectivity problems.

## Objectives

The objectives of this project were to:

- Design a campus network topology.
- Separate Engineering, Administration and Student devices using VLANs.
- Assign a separate IP subnet to each VLAN.
- Configure trunk links where required.
- Enable inter-VLAN routing.
- Configure DHCP to assign IP addresses automatically.
- Implement an ACL to restrict Student-to-Administration traffic.
- Test connectivity between devices and network gateways.
- Document the configuration process and troubleshooting experience.

## Network Design

The network was designed around a core multilayer switch connected to
the campus VLANs, with an edge connection representing the upstream network.

The three departmental VLANs were:

| Department | VLAN ID | Network Address | Default Gateway |
|-------------|---------|-----------------|-----------------|
| Engineering | 10 | 192.168.10.0/24 | 192.168.10.1 |
| Administration | 20 | 192.168.20.0/24 | 192.168.20.1 |
| Students | 30 | 192.168.30.0/24 | 192.168.30.1 |

Each VLAN uses a /24 subnet with a subnet mask of 255.255.255.0.

The core switch provides the VLAN gateway interfaces used for inter-VLAN
routing.

The upstream connection uses the 10.0.0.0/30 network, with a default route
configured toward 10.0.0.1.

## Technologies and Concepts

- Cisco Packet Tracer
- Virtual LANs (VLANs)
- IEEE 802.1Q trunking
- IPv4 addressing and subnetting
- Inter-VLAN routing
- DHCP
- Extended Access Control Lists
- ICMP and ping testing
- Basic network troubleshooting

## Implementation

### 1. VLAN Configuration

I created three VLANs to separate Engineering, Administration and Student
traffic.

- VLAN 10: Engineering
- VLAN 20: Administration
- VLAN 30: Students

I assigned the relevant switch access ports to their intended VLANs.

This separation creates distinct broadcast domains and provides a foundation
for controlling communication between departments.

### 2. IP Addressing and Gateway Interfaces

I configured the following gateway interfaces on the core switch:

- VLAN 10: 192.168.10.1/24
- VLAN 20: 192.168.20.1/24
- VLAN 30: 192.168.30.1/24

These interfaces provide the default gateways for devices in their
respective subnets.

### 3. Trunking

I configured trunking where required to carry traffic for multiple VLANs
over a shared link.

This allows the network to maintain VLAN separation while transporting
traffic between the relevant network devices.

### 4. Inter-VLAN Routing

I configured the core switch to route traffic between the VLAN subnets.

This allowed devices in different VLANs to communicate through their
default gateways, subject to the configured access-control rules.

### 5. DHCP

I configured DHCP pools so that end devices could receive IP addresses
automatically.

I checked the DHCP bindings on the core switch and confirmed that leases
were being assigned in all three subnets.

Observed DHCP addresses included:

- Engineering: 192.168.10.10 and 192.168.10.11
- Administration: 192.168.20.10 and 192.168.20.11
- Students: 192.168.30.10 and 192.168.30.11

### 6. Access Control List

I configured extended ACL 101 to restrict traffic from the Student subnet
to the Administration subnet.

The intended rules were:

    access-list 101 deny ip 192.168.30.0 0.0.0.255 192.168.20.0 0.0.0.255
    access-list 101 permit ip any any

The first rule denies IP traffic originating in the Student subnet and
destined for the Administration subnet.

The second rule permits other IP traffic evaluated by this ACL.

I applied the ACL inbound on VLAN 30 so that it filters traffic entering
the core switch from the Student VLAN.

This was intended to block Student-to-Administration communication while
allowing other traffic, including Administration-to-Student traffic.

## Testing and Validation

I used ping tests, DHCP bindings, interface information and ACL counters
to investigate network behaviour.

| Test | Result |
|------|--------|
| DHCP address assignment | Successful |
| VLAN gateway interfaces | All three VLAN interfaces reported up/up |
| Administration PC to Administration gateway | Successful |
| Administration PC to Student gateway | Successful |
| Core switch to Student PC | Successful |
| Student-to-Administration communication | Blocked as intended by the ACL |
| Administration PC to Student PC | Unresolved; repeated tests timed out |

The successful gateway and core-switch tests helped me narrow down the
remaining connectivity issue.

However, successful communication with the Student gateway and successful
pings originating from the core switch did not establish that
Administration-to-Student communication was fully working.

## Troubleshooting Experience

### 1. Invalid ACL Command Syntax

While configuring the ACL, I received an "Invalid input detected" error.

I checked the command syntax and configuration mode, then switched to a
numbered extended ACL.

I successfully configured the intended rules:

    access-list 101 deny ip 192.168.30.0 0.0.0.255 192.168.20.0 0.0.0.255
    access-list 101 permit ip any any

This taught me to check the CLI prompt and use the correct syntax for the
device and configuration mode.

### 2. Incomplete ACL Command

I initially entered:

    ip access-group 101

The switch returned an incomplete-command error.

I corrected the command by specifying the direction:

    ip access-group 101 in

This taught me that applying an ACL requires both the ACL identifier and
the direction in which it should filter traffic.

### 3. Incorrect Named ACL Rule

During troubleshooting, I discovered that my named ACL,
STUDENT-SECURITY, contained an incorrect deny rule.

I moved to numbered ACL 101, which contained the intended source and
destination subnets.

I then checked the ACL entries and their match counters to understand
which rules were processing traffic.

### 4. Administration-to-Student Ping Failure

One issue remained unresolved at the end of testing.

Administration could ping its own gateway and the Student gateway.
The core switch could also ping the Student PC.

However, pings initiated by the Administration PC toward the Student PC
continued to time out.

I checked the ACL rules and counters, reviewed the VLAN interfaces,
and confirmed that the gateway and core-switch ping tests were successful.

The ACL's deny rule matched Student-to-Administration traffic, which was
the intended restriction. The permit rule allowed other traffic evaluated
by ACL 101.

I could not conclusively identify the cause of the
Administration-to-Student timeout before documenting the project.

My next diagnostic step would be to inspect an Administration-to-Student
ICMP packet in Packet Tracer Simulation Mode to determine whether the
request reaches the Student PC and whether the reply successfully returns.

I documented this issue rather than claiming that all connectivity tests
passed.

## Lessons Learned

Through this project, I learned that:

- VLANs separate devices into different logical broadcast domains.
- Each VLAN requires appropriate IP addressing and a default gateway
  for communication with other subnets.
- Trunk links carry traffic for multiple VLANs.
- Inter-VLAN routing enables communication between separate IP subnets.
- DHCP simplifies IP address assignment.
- Extended ACLs can control traffic based on source and destination
  addresses.
- ACL direction and placement are important when controlling traffic.
- A successful gateway ping does not guarantee that end-to-end
  communication between two PCs will work.
- ACL counters can help identify which rules are matching traffic.
- Troubleshooting requires testing individual network segments and
  examining packet flow rather than repeatedly changing configurations.

## Project Files

- `packet-tracer/campus-network.pkt` — Packet Tracer simulation file.
- `images/campus-topology.png` — Network topology screenshot.

## Future Improvements

If I continue developing this project, I would:

- Resolve the remaining Administration-to-Student connectivity issue.
- Inspect ICMP traffic in Simulation Mode to identify where packets
  are dropped or replies fail.
- Verify the final ACL placement and test every permitted and denied
  traffic path.
- Improve the topology presentation and network documentation.
- Extend the design with additional network services and security rules.

## Conclusion

This project gave me practical experience designing and configuring a
segmented campus network in Cisco Packet Tracer.

I worked with VLANs, trunking, IP addressing, inter-VLAN routing, DHCP
and extended ACLs.

Although most of the intended network functions were working, I still
had an unresolved Administration-to-Student connectivity issue at the
end of testing.

Documenting that issue helped me recognize the importance of validating
the complete traffic path and using packet-level troubleshooting to
identify the actual cause of a network failure.
