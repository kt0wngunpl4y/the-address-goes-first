# ADR 0001: The route is the computer's

## Status

Accepted


## Context

An agent running on a datacenter address is classified by that address before the request is classified. The task fails, and the agent retries the plan, the fetches, and the tool calls. The bill tracks the attempts.

A proxy configured inside one program covers the programs that opt into it. `HTTP_PROXY` and a product proxy field make the site see the proxy. Nearby Tailscale features do the same kind of partial job: Android per-app split tunneling chooses apps, an app connector chooses domains, a subnet router chooses private subnets, and a commercial VPN exit node presents that provider's address.

## Decision

The agent computer's default route is a Tailscale exit node running on a Linux machine on the residential network. Linux is the exit node because it forwards in the kernel. The home machine advertises with `tailscale set --advertise-exit-node` and stays awake. The agent computer selects that machine, by Tailscale IP, with `tailscale set --exit-node`. Proxy settings on the agent are removed. Work starts only after the public address observed from the agent computer is the home machine's public address.


## Consequences

While the selection stands and the client is running, programs on that computer use the route, including programs that have no proxy setting. The home machine has to stay awake with IP forwarding on, and an Owner, Admin, or Network admin has to allow the advertisement unless `autoApprovers` already does. A custom policy has to grant `autogroup:internet`; naming the exit node machine as `dst` allows a connection to the machine and does not grant exit-node use.

While the exit node stays selected, Tailscale's design is to blackhole traffic if the node is offline, missing, or not offering the service, rather than fall back to the local uplink. Staff have called a leak in that case a bug, and users have reported leaks, so the blackhole is not a guarantee. The design does not apply before the client starts, after it stops, or after the selection is cleared. The address check is the gate.

Traffic already claimed by a subnet router or an app connector does not use this route. A commercial VPN exit node would pass traffic through the class of address this decision rejects.
