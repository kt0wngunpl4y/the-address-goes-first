# The Address Goes First

## Idea

A site often sorts a request by network address before it reads the request. A computer in a datacenter shows a datacenter address. We have been putting that computer's default route on a Linux machine at home, so **the public address is the house**.

## Problem

Datacenter ranges are a class. The task fails. The agent treats that as a bad fetch and runs the plan again. **The bill tracks the attempts.** Any agent on a cloud computer presents that class. Changing the vendor does not change it.

## Solution

A Linux box on the home network, left awake, with forwarding on in the kernel, advertises a Tailscale exit node. An admin allows it unless the tailnet already auto-approves.

- The agent computer selects that box by the Tailscale address `tailscale status` shows.
- Public traffic leaves on `0.0.0.0/0` and `::/0`.
- Proxy variables stay unset. The product proxy field stays empty.
- **If the public address does not match the house, do not start.**

## Theory

An exit node is the computer's default route, not a setting in one program. A proxy, a per-app tunnel, a subnet router, and an app connector only cover what they name.

- Linux forwards in the kernel.
- macOS, Windows, and Android forward in userspace and drop the route when they sleep.
- A commercial VPN exit, including Mullvad, and a suggested or automatic exit node, show someone else's address.
- While an exit node stays selected, Tailscale's design is a blackhole when that node is down, not a fall back onto the local uplink. **That is the design, not a promise.**
- Before the client starts, after it stops, and after the selection is cleared, the computer uses its own uplink.

## Practice

The commands are in the docs.

1. [The home Linux machine](docs/01-home-linux.md)
2. [The agent computer](docs/02-agent-computer.md)
3. [Prove the address](docs/03-prove-the-address.md)
4. [The access-policy grant](docs/04-access-policy.md)
5. [What this is not](docs/05-what-this-is-not.md)

The route belongs on the computer: [ADR 0001](docs/adr/0001-computer-default-route.md).

Commands follow [KB 1103](https://tailscale.com/kb/1103/exit-nodes), last validated 15 December 2025. **Where they disagree, the Tailscale document wins.**

The commands are a working demonstration of the idea, not a frozen setup. They are a practice that has been holding up. As the client and the agents change, the commands will date. The check will not: the public address matches the house, or the task does not start.
