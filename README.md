# Home Assistant Configuration

## Overview

This repo contains the configuration synced with my Home Assistant home automation
system running in Kubernetes.

## Working on configuration

Run `devspace dev` to sync `./config` with `/config` in the Home Assistant
container. Most configuration uses `preferNewest` at startup: a file that exists
on only one side is copied to the other, and the newer copy wins when both
exist. A deletion made while DevSpace is stopped can therefore be undone by the
next initial sync.

Bubble Card modules and themes are exceptions. `config/bubble_card/` and
`config/themes/` use separate `mirrorRemote` syncs. At startup, the container
wins: files removed through Home Assistant or its UI are also removed locally,
and remote edits overwrite local copies. While DevSpace is running, changes to
these directories sync in both directions. The local copies are useful for
inspecting installed community modules, but are not the authoring source.

Keep modules you develop in `src/bubble_card/modules/` and, when needed, custom
themes in `src/themes/`. To deploy one, start `devspace dev` and wait for its
initial sync, then copy the source file or theme directory into the matching
path under `config/`. For example:

```sh
cp src/bubble_card/modules/state_line_plus.yaml config/bubble_card/modules/
```

DevSpace then uploads the copy to Home Assistant. Do not stage a new or edited
module only under `config/` before starting DevSpace: `mirrorRemote` can remove
or overwrite it. If you edit an authored module or theme through the Home
Assistant UI, copy the resulting file back to `src/` if you want to keep that
change as source. Review changes under the installed directories before
committing; these paths are still tracked by Git.

## Access

Home assistant is reachable at <https://ha.clark.zone>.
User access and accounts are managed by Authentik at <https://auth.clark.zone>.

> [!NOTE]
> Home Assistant is only reachable from the private network or via VPN.
