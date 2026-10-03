# The agent computer

Same tailnet. Tailscale 1.20 or later. This selection is this computer only.

```shell
tailscale version
sudo tailscale up
tailscale status
```

Copy the home machine's Tailscale IP from that list. It is inside `100.64.0.0/10`. A public IP is wrong. A LAN IP is wrong. If the home machine is not in the list, it is not on this tailnet.

```shell
sudo tailscale set --exit-node=<tailscale-ip>
```

Replace `<tailscale-ip>` with the address you copied. The client stores it until you clear it. It does nothing before the client is running, and nothing after the client is stopped.

Leave local-network access off unless the task needs this computer's own LAN. Public traffic uses the exit node either way.

```shell
sudo tailscale set --exit-node=<tailscale-ip> --exit-node-allow-lan-access=true
```

That flag is this computer's LAN. It is not the home LAN, and it is not a path around the exit node for public sites.

macOS, App Store or Standalone: Tailscale menu, Exit Nodes, the home machine's name. Before v1.60.0 the menu says Use exit node. Allow Local Network Access only when this computer's LAN is required.

Windows: system tray, Use exit node, the home machine's name. Same checkbox, same rule. You do not type the Tailscale IP on these clients.

## Proxies off

In the environment that actually runs the agent, including a service manager or a desktop launcher:

```shell
unset HTTP_PROXY HTTPS_PROXY ALL_PROXY http_proxy https_proxy all_proxy
```

Clear the product's proxy field. Empty means empty. A clean shell and an agent process that still has the variables are two environments. The one that runs the agent is the one that counts. Do this before the address check.

## Stop

```shell
sudo tailscale set --exit-node=
```

The value after `=` is empty.

Windows: None, from the exit-node menu. macOS: Exit Nodes (Use exit node before v1.60.0), None.

After you clear it, this computer is on its own uplink. Do not start work.

Next: [Prove the address](03-prove-the-address.md).
