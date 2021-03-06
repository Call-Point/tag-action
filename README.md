# Tag Action

This action increments semantic versioning embedded in git tags.

Supports tags consisting of only a semantic version number or a semantic version number following a prefix. Prefixes can be any string and may or may not include a delimiter 
character before the semantic version number. This action does not support suffixes following a semantic version number.

Examples:

```
1.2.3      # semver
v1.2.3     # semver with prefix
foo-1.2.3  # semver with prefix and delimiter
```

If no prior tag is found then the action will default the semantic version to `0.0.1`.

# TL;DR

Copy this template and make revisions as necessary:

```yaml

jobs:
  my-job:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        id: artifacts-checkout
        uses: actions/checkout@v2

      - name: Tag Action
        id: tag_action
        uses: Call-Point/tag-action@main
        with:
          bump: patch
          prefix: v
          do_push_tag: true
          token: ${{ secrets.DEVOPS_ACCESS_TOKEN }}

      - name: Echo Output
        id: tag_action_output
        run: |
          echo "last_tag: ${{ steps.tag_action.outputs.last_tag }}"
          echo "next_tag: ${{ steps.tag_action.outputs.next_tag }}"
```
# Usage

```yaml
- uses: Call-Point/tag-action@main
  with:
    # The part of the semantic version number to increment. Options
    # include 'major', 'minor', and 'patch'. Defaults to 'patch'
    bump: major | minor | patch
    # Optional. Tag prefix if applicable. Include any delimiter
    # between the prefix and the semantic version number. Common
    # examples: 'v' | 'callPilot-' | 'portal-'
    prefix: ''
    # Optional. Signals whether the action should push the next tag
    # to the repo. Defaults to 'false'. If 'true' then the 'token'
    # property must also be provided. See below.
    do_push_tag: true | false
    # Optional. Personal access token (PAT) with write privileges
    # to push tags to the repo. The PAT must be a secret.
    token: ${{ secrets.DEVOPS_ACCESS_TOKEN }}
```

# Output

The action sets the following output variables:

- `last_tag` - Last tag found in the repo. Defaults to empty string if no tag is found.
- `next_tag` - Next tag with incremented semantic version number.
- `last_semver` - Last semantic version number found in the repo.
- `next_semver` - Next incremented semantic version number.


Use the expressions `${{ steps.<id>.outputs.next_tag }}` or `${{ steps.<id>.outputs.last_tag }}` in your workflow in steps following the action to read the tag output variables. Replace `<id>` with the id given to the action's step. Use the same convention to read the `last_semver` and `next_semver` output variables.

# Scenarios

## Common Usage to Increment Tag with Prefix and Push to Repo
```yaml
- uses: Call-Point/tag-action@main
  with:
    bump: minor
    prefix: v
    do_push_tag: true
    token: ${{ secrets.DEVOPS_ACCESS_TOKEN }}
```

## Read the Output Variables
```yaml
jobs:
  my-job:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        id: artifacts-checkout
        uses: actions/checkout@v2

      - name: Tag Action
        id: tag_action
        uses:  Call-Point/tag-action@main
        with:
          bump: patch
          prefix: v

      - name: Echo Output
        id: tag_action_output
        run: |
          echo "last_tag: ${{ steps.tag_action.outputs.last_tag }}"
          echo "next_tag: ${{ steps.tag_action.outputs.next_tag }}"
          echo "last_semver: ${{ steps.tag_action.outputs.last_semver }}"
          echo "next_semver: ${{ steps.tag_action.outputs.next_semver }}"
```
## Default Action to Increment Patch Version of SEMVER Tag
```yaml
- uses: Call-Point/tag-action@main
```

## Increment Major Version Number
```yaml
- uses: Call-Point/tag-action@main
  with:
    bump: major
```

## Increment Minor Version Number
```yaml
- uses: Call-Point/tag-action@main
  with:
    bump: minor
```

## Increment Patch Version Number
```yaml
- uses: Call-Point/tag-action@main
  with:
    bump: patch
```

## Increment a Tag with a Prefix
```yaml
- uses: Call-Point/tag-action@main
  with:
    prefix: v
```
## Increment a Tag and Push to Repo
```yaml
- uses: Call-Point/tag-action@main
  with:
    do_push_tag: true
    token: ${{ secrets.DEVOPS_ACCESS_TOKEN }}
```
