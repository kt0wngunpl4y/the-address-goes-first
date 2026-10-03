# The home Linux machine

Linux. Awake. On the residential network. Its public address is the one the agent computer has to match.

Both sides need Tailscale 1.20 or later.

```shell
tailscale version
```

## Forwarding

```shell
echo 'net.ipv4.ip_forward = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
echo 'net.ipv6.conf.all.forwarding = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
sudo sysctl -p /etc/sysctl.d/99-tailscale.conf
```

If `/etc/sysctl.d` does not exist, append those two lines to `/etc/sysctl.conf` and run `sudo sysctl -p /etc/sysctl.d/99-tailscale.conf`.

Do not append again if both keys are already `1`.

```shell
sysctl net.ipv4.ip_forward
sysctl net.ipv6.conf.all.forwarding
```

Each line prints `= 1`. Set IPv6 even when the uplink has none.

Leave the firewall denying forward by default. `ufw` and `firewalld` already do. Do not open forward in general.

On firewalld only:

```shell
sudo firewall-cmd --permanent --add-masquerade
sudo firewall-cmd --reload
```

## Advertise

```shell
sudo tailscale set --advertise-exit-node
sudo tailscale up
```

Do not pass `--advertise-routes`. Do not make this box an app connector. This machine does not select an exit node. Its own traffic stays on the residential uplink.

## Allow it

Admin console, Machines, this device. Filter `property:exit-node`. Device menu, Exit route settings, enable Use as exit node, save.

Owner or Admin, any plan. Network admin on Standard, Premium, and Enterprise. An IT admin cannot.

Skip the console only when `autoApprovers.exitNode` already covers this machine.

## Before you leave

- Both sysctls are `1`.
- The firewall still denies forward by default.
- The machine stays awake. Closing a laptop sleeps it, and sleep drops the route.
- `tailscale status` shows it on the tailnet.
- The console shows it allowed, or `autoApprovers` already covered the advertisement.
- This machine has not selected an exit node.

Next: [The agent computer](02-agent-computer.md).
