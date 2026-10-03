# Prove the address

After the home machine is allowed, after this computer has selected it, after proxies are off. From the environment the agent will run in. Before the task. If it fails, do not start.

1. On the home Linux machine, on its own uplink, read the public address. It is advertising an exit node and it has not selected one. Keep the address only long enough to compare. Do not put it in a repo, a ticket, or a screenshot.
2. On the agent computer, same method, same environment the agent will run in.
3. The two addresses are the same. That is the pass. Anything else is a fail.

[KB 1103](https://tailscale.com/kb/1103/exit-nodes) describes the check as an online tool that shows your public address. Use one method on both machines. When the exit node is in use, that observation is the exit node's public address.

`tailscale status` is not this check. An address in `100.64.0.0/10` is not the public address.

## When they do not match

Stop. Do not run the task once to see.

- The client on the agent computer is running, and the selection has not been cleared.
- The home machine is awake, and both `net.ipv4.ip_forward` and `net.ipv6.conf.all.forwarding` are `1`.
- The home machine was allowed in the admin console, or `autoApprovers` already approved the advertisement. Advertising alone is not the allow.
- `--exit-node` was the home machine's Tailscale IP from `tailscale status`.
- Policy still lets this user reach `autogroup:internet`. See [The access-policy grant](04-access-policy.md).
- `HTTP_PROXY`, `HTTPS_PROXY`, `ALL_PROXY`, the lowercase forms, and the product proxy field are unset in the environment you just observed.
- The selected exit node is the home machine. Mullvad or another commercial exit matches that provider's address, which is the class that gets blocked.
- The destination is not already claimed by a subnet router or an app connector. Those take their traffic first. The exit node never sees it.

Fix the cause and observe again.

## When the exit node is not there

While an exit node stays selected, Tailscale's design is a blackhole if that node is offline, missing, or not offering the service, rather than a fall back onto the local uplink. The client's exit-node preference states that case directly. Tailscale staff have called a leak in that case a bug. Users have reported leaks. Do not treat the design as a guarantee, and do not start work on the strength of it.

The design does not apply before the client starts, after the client is stopped, or after the selection is cleared with `sudo tailscale set --exit-node=` or with None. In those states the computer uses its own uplink. On a datacenter machine, that uplink is a datacenter address.

The selection stays stored until you clear it. The next time the client runs, a selection you did not clear is in force again. Before that client is running, it is not. Repeat this check before the task, including when the same computer passed it on an earlier day.

After the task, if this computer should use its own uplink, clear the selection and read the public address once more. You should now see this computer's own address.

If the home machine will be asleep, offline, or no longer offering the exit node, clear the selection before you depend on this computer's network.
