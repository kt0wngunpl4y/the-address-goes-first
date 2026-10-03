# The access-policy grant

Two approvals. Do not mix them.

1. Allow the device. Owner, Admin, or Network admin, on the Machines page, unless `autoApprovers` already did. That is [the home machine](01-home-linux.md). It answers: may this device offer the route?
2. Let a user send internet traffic through an exit node. That is tailnet policy. It answers: may this user use an exit node at all?

The default policy already allows (2). If you have not replaced it, do not add a grant.

Rule and grant shape: [KB 1103](https://tailscale.com/kb/1103/exit-nodes), "Grant access to use the exit node." Example: [Allow using exit nodes](https://tailscale.com/docs/reference/examples/grants). Syntax: [policy file](https://tailscale.com/docs/reference/syntax/policy-file).

## A custom policy can drop it

KB 1103's example only lets one group reach an internal tag:

```json
{
  "grants": [
    {
      "src": ["group:developers"],
      "dst": ["tag:prod"],
      "ip": ["*"]
    }
  ]
}
```

Those users can still reach `tag:prod`. They can no longer use an exit node. Nothing grants `autogroup:internet`. The address check fails. Do not start the task to confirm it.

## Listing the machine is the wrong grant

A grant whose `dst` is the exit-node machine allows connections to that machine, such as SSH. It does not let you leave through it. Adding the home machine, its name, its Tailscale IP, or its tag as `dst` does not fix the failure above.

The destination that matters is `autogroup:internet`.

## The grant

Add this into the existing `grants` array. Do not replace the file with only this object if other grants are how the tailnet reaches its own machines.

```json
{
  "src": ["autogroup:member"],
  "dst": ["autogroup:internet"],
  "ip": ["*"]
}
```

`autogroup:member` is the authenticated users. `autogroup:internet` is internet destinations outside the tailnet, through any configured exit node, not through one named machine. `ip` of `*` is all protocols. Narrow `src` if not every member should have an exit node. Do not narrow it by changing `dst` to the home machine.

This grant does not approve the advertisement, does not select the exit node, and does not restrict use to the home machine. A user covered by it may select any allowed exit node, including a commercial VPN exit. The computer's selection is what pins this route to the home machine.

Owners, Admins, and Network admins edit the policy file. In the admin console that file is Access controls. If GitOps locks the editor, the change goes into that source. Typing over a locked editor is not the procedure.

After the grant is saved, repeat [the address check](03-prove-the-address.md). The grant is in force when that check shows the home machine's public address.

## autoApprovers is the other opt-in

`autoApprovers` does not grant use. It skips the console allow for the advertisement.

```json
"autoApprovers": {
  "exitNode": ["tag:example-exit"]
}
```

The `exitNode` list may contain a user's login, a group, an autogroup, or a tag. KB 1103: if the device is authenticated by a user who can approve exit nodes in `autoApprovers`, the exit node is approved automatically. A tag in that list approves an advertisement from devices wearing that tag. The tag has to be one the policy already defines, and one this machine actually wears. This procedure does not create tags. Use the Machines page allow unless `autoApprovers` already covered the home machine.

`autoApprovers` and the `autogroup:internet` grant solve different problems. You can have either, both, or, on the default policy with a console allow, neither written by hand. You cannot substitute one for the other.
