# What this is not

## A proxy some apps opt into

`HTTP_PROXY`, `HTTPS_PROXY`, `ALL_PROXY`, the lowercase forms, and a product proxy field pount one program at a proxy. The program connects to the proxy. The site sees the proxy. That stays true while an exit node is selected. Unset them, in the environment that runs the agent, before [the address check](03-prove-the-address.md).

## Android per-app split tunneling

The Android client can include or exclude apps. An excluded app goes to the network on its own. [App-based split tunneling](https://tailscale.com/docs/features/client/android-app-split-tunneling). The computer does not have one default route that every program uses. This procedure does not use it, and it does not use an Android device as the exit node. Android exit nodes forward in userspace. [KB 1103](https://tailscale.com/kb/1103/exit-nodes) calls that path less mature than Linux.

## A Mullvad exit node, or any commercial VPN exit node

[Mullvad exit nodes](https://tailscale.com/docs/features/exit-nodes/mullvad-exit-nodes) use Mullvad's servers. Traffic leaves through Mullvad's address. Any other commercial VPN used as an exit node leaves through that provider's address. That is the class that gets classified and blocked.

Select the home Linux machine, by the Tailscale IP from `tailscale status`. Do not pick a city name and then compare the agent computer against the provider's address. The comparison that matters is against the home machine's own uplink.

## A suggested or automatic exit node

KB 1103: the client picks from what is available using location and latency. The CLI form is `--exit-node=auto:any`, on [`tailscale up`](https://tailscale.com/docs/reference/tailscale-cli/up). That can land on a commercial exit node or on a machine that is not the home machine. This repository uses `sudo tailscale set --exit-node=<tailscale-ip>` with the home machine's address.

## A subnet router

A [subnet router](https://tailscale.com/docs/features/subnet-routers) advertises specific private subnets with `--advertise-routes`. Other devices can then reach non-Tailscale hosts on those subnets. It does not put the computer's public traffic on the home machine's address.

An exit node takes traffic that is not already claimed by a subnet router. Advertising routes from the home machine, or accepting someone else's subnet routes, hands those destinations to the subnet router. The public-address check can still pass, because that check is general egress, while a destination inside an advertised subnet never uses the exit node.

`--exit-node-allow-lan-access=true` is not a subnet router. It lets the agent computer reach its own local network while public traffic still uses the exit node. It does not expose the home LAN.

## An app connector

An [app connector](https://tailscale.com/docs/features/app-connectors) routes specific applications, matched by domain, through a chosen device, which then connects out from that device's public address. Destinations an app connector claims do not go to the exit node. If the connector is a cloud machine, those sites see the cloud machine.

## A userspace exit node as the home machine

Linux, running as root, forwards in the kernel. macOS, Windows, and Android forward exit-node traffic in userspace. KB 1103 says the macOS and Android implementations are not as mature as Linux, and that Windows exit nodes are limited to userspace and still being optimized. Those systems also lose the route if they sleep. Comparison: [Kernel vs. netstack subnet routing & exit nodes](https://tailscale.com/docs/reference/kernel-vs-userspace-routers).

They can appear in a tailnet as exit nodes. This procedure does not use them as one. The home machine is the Linux machine in [the first document](01-home-linux.md).

## A guarantee that traffic cannot leak

While an exit node stays selected, Tailscale's design is to blackhole traffic if that node is offline, missing, or not offering exit-node service, rather than fall back to the local uplink. Tailscale staff have said a leak in that case is a bug, and users have reported leaks. The design is not a guarantee.

It is also the wrong description of three states: before the client starts, after the client is stopped, and after the selection is cleared. In those states the computer uses its own uplink.

The control that decides whether work starts is the public address observed from the agent computer. It matches the home machine's public address, or the task does not start. That check is [Prove the address](03-prove-the-address.md).
