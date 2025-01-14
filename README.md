# Notes

## Branching Policy

- An upstream-tracking branch, named `upstream_stable`
    - Has stable history
    - Periodically tagged as `upstream_${version}`
- An unstable releasing branch, named `current`
    - Ready-to-use
    - Always exists
    - Based on upstream tags (may not be the lastest one)
    - With downstream patches applied
    - Accepting new downstream patches
- An WIP branch, named `next`
    - Based on upstream tags which is newer than the one from `current`
    - Actively picking & arranging patches from `current`
    - May be blocked by conflicts, then a related issue may be opened to tracking that
    - It will be renamed to `current` once all existing patches are picked.
      - Effectively causing an upstream version bump on `current`, with "zero-down-time"
    - Then it will disappear for some time, until the next upstream tag is being rebased

## Tagging Policy

- Upstream is tagged normally, on `upstream-stable` branch, with its' own commits as well as our downstream patches which are successfully merged into it. Not something that downstream could control.
- Downstream tags are created on `current` branch right before being overwritten by a finished `next` branch
  - But this tag is not versioned the same as the latest upstream tag
  - Instead it is versioned as the one that current `current` branch is based on
  - May introduce some kind of code freezing?

- Each downstream tag represents a snapshot that "just work".
- If a downstream tag is created and a bug is found after that
  - Assuming the buggy downstream tag is `downstream-v1.2.3`, then create a new tag with fixes, call it `downstream-v1.2.3-bugfix1`

## Rebasing Policy

Once `next` branch is created on top of the latest upstream tag, it is necessary to cherry-pick downstream patches from `current` branch, as they are still based on an older upstream tag.

It could be as simple as:

```shell
git cherry-pick upstream_v0.0.2..current
```

If there is no conflict at all, then just simply replace `current` branch with `next`.

- For remote branches, `-f` is required to update and replace its refs.
- Make sure `next` does not exist on both local and remote repo, if nothing is WIP.

If there does exist conflict that cannot be solved immediately: push `next` to remote and accept PRs here.
