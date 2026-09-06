# Windows Server Infrastructure Lab

## Objective
Build a small virtualized business network from scratch: deploy a Windows Server and two Windows client VMs, configure them to a business-ready standard, then stand up core infrastructure services (Active Directory, DHCP, DNS) and demonstrate they work correctly. This will be my first ever personal networking project

## Environment & Tools
- Oracle VirtualBox
- Windows Server 2022 (infrastructure server)
- Windows 10/11 (x2 clients)
- Internal/host-only virtual network

## Part 1: Virtual Environment & Internal Network

Set up three VMs (`Blinds-Trellis-Server`, `Blinds-Trellis-Client1`, `Blinds-Trellis-Client2`) on a shared internal network adapter so they could communicate without touching the host network.

![VirtualBox VM overview](images/01-vm-overview.png)

Server was assigned `192.168.10.10`, with Client1 on `.11` and Client2 on `.12`. Verified connectivity with `ping` between both clients and the server — 0% packet loss on all tests.
![Ping test from Server](images/02-ping-server.png)
~Server ping sent 
![Ping test from Server](images/03-ping-client1.png)
~Client1 ping sent 
![Ping test from Client2](images/04-ping-client2.png)
~Client2 ping sent 

All VM NIC's were configured so the communication between them is internal/host-only and I provided proof they ping each other.

## Part 2: Client Configuration

Configured both clients to a business-ready standard:
- Partitioned the disk into a 1/3 C: (system) / 2/3 D: (Business Data) split
- Enabled System Protection and created a restore point on each client
- Set the desktop background to the organisation logo


![Disk partitioning](images/05-disk-partition.png)

~The disks are partitioned

![Restore point created](images/06-restore-point.png)

~System protection enabled and restore points created 

![Desktop Background Change](images/07-restore-point.png)

~Wallpaper changed!

## Part 3: Server Roles — Active Directory, DHCP, Group Policy, DNS

- **Active Directory Domain Services**: Promoted the server to a domain controller, created the domain, and joined at least one client.
- **DHCP**: Installed the DHCP role and configured an active scope (`192.168.10.0/24`), then confirmed both clients received leases automatically.
- **DNS break/fix**: Deliberately set an incorrect DNS value, demonstrated the resulting failure, then corrected it and confirmed resolution worked again.

![Active DDS server promotion confirmation](images/08-active-DDS.png)
~Joining Confirmation pop up
![DDS persisted](images/09-active-DDS.png)

~This shows that the join persisted 

![DHCP role installation](images/10-dhcp-role.png)

~DHCP Scope showing the address pool 

![DHCP lease confirmation](images/11-dhcp-lease.png)

~Address leases showing BT-Client1 with a lease


![DNS Break](images/12-dhcp-role.png)

Purposely entering failure state: nslookup repeatedly timing out against 192.168.10.99

![DNS fix](images/13-dhcp-role.png)

nslookup succeeding, resolving to 192.168.10.10, success state

##Challenges

Challenge: After correcting the DNS setting back to the right server, nslookup still failed at first — the client had cached the broken DNS server address locally, so simply fixing the setting wasn't enough. Running ipconfig /flushdns to clear the resolver cache was the extra step needed before resolution actually succeeded.

## What I learned

What I learned: DNS troubleshooting isn't just about the server-side configuration — the client's local resolver cache can hold onto stale (or broken) records even after the DNS setting itself has been corrected. nslookup combined with ipconfig /flushdns became a much more reliable way to verify a real fix, rather than trusting the config screen alone.
