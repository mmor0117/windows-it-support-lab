[001-network-baseline.md](https://github.com/user-attachments/files/33632632/001-network-baseline.md)
# Ticket 001 — Windows Network Connectivity Baseline

**Category:** IT Support / Networking  
**Environment:** Windows PC, PowerShell, wired Ethernet connection  
**Status:** Baseline checks completed — no fault identified during these tests  
**Test date:** October 18, 2026  
**Scope:** Personal practice lab; no user-reported outage was reproduced

## Objective

Verify basic network configuration, external IP connectivity, DNS resolution, and access to a web service on TCP port 443. Record the commands used and interpret the output as a help-desk technician would.

## Tools and commands

Run in Windows PowerShell:

```powershell
ipconfig
ping 1.1.1.1
nslookup google.com
Test-NetConnection google.com -Port 443
```

All four commands are diagnostic checks; none was intended to change the PC's network settings.

## Test results

| Test | Observed evidence | Result | Interpretation |
|---|---|---|---|
| Network configuration (`ipconfig`) | Ethernet adapter had an assigned private IPv4 address and a default gateway. Radmin VPN was also present; Wi-Fi adapters were disconnected. | PASS | Ethernet had a plausible local network configuration. This alone does **not** prove internet connectivity. |
| IP connectivity (`ping 1.1.1.1`) | 4 packets sent, 4 received, 0% loss; 12–15 ms response time, 13 ms average. | PASS | The PC could reach an external IPv4 address using ICMP at the time of the test. |
| DNS resolution (`nslookup google.com`) | A configured DNS resolver answered with IPv4 and IPv6 addresses for `google.com`. | PASS | DNS resolution succeeded for the tested hostname at the time of the test. |
| HTTPS port (`Test-NetConnection google.com -Port 443`) | `TcpTestSucceeded : True`; connection used the Ethernet interface and an IPv6 destination. | PASS | A TCP connection to Google's port 443 succeeded over IPv6. This does not by itself verify browser rendering or a full HTTPS request. |

## Evidence — selected, sanitized excerpts

### External IPv4 reachability

```text
Ping statistics for 1.1.1.1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
Approximate round trip times in milli-seconds:
    Minimum = 12ms, Maximum = 15ms, Average = 13ms
```

### DNS resolution

```text
nslookup google.com
Non-authoritative answer:
Name: google.com
Addresses: [multiple IPv4 and IPv6 results returned]
```

### HTTPS TCP connectivity

```text
Test-NetConnection google.com -Port 443
ComputerName     : google.com
RemoteAddress    : [redacted IPv6 address]
RemotePort       : 443
InterfaceAlias   : Ethernet 2
SourceAddress    : [redacted IPv6 address]
TcpTestSucceeded : True
```

> Privacy note: Local configuration included a Radmin VPN interface. IP addresses, interface identifiers, device names, and other potentially identifying network details have been omitted from this public report. Full, unredacted output was not uploaded.

## Findings and conclusion

The tested PC had an Ethernet configuration and passed three connectivity checks: external IPv4 ping, DNS resolution, and an IPv6 TCP connection to port 443. No network fault was demonstrated by these baseline tests.

**Limitations:** These tests do not verify every website, all network protocols, browser behavior, or connection stability over time. The ping and TCP tests used different IP families (IPv4 and IPv6).

## Next steps for a simulated support ticket

1. Create an **isolated virtual machine** and intentionally introduce one safe, reversible network problem.
2. Record the symptoms, expected versus actual results, and relevant diagnostic output.
3. Apply one troubleshooting change and repeat the same tests to verify recovery.
4. Document the root cause and fix. Do not deliberately disrupt the primary home PC or router.

## What I learned

- `ipconfig` displays local network interface settings, including assigned addresses and gateways.
- `ping` can test ICMP reachability to a target IP address, but a failed ping does not always mean a service is unavailable.
- `nslookup` checks DNS resolution independently of a web browser.
- `Test-NetConnection -Port 443` checks TCP port connectivity, not whether a web page actually loads.
- Clear support documentation separates **observations**, **interpretations**, and **confirmed fixes**.
