# Require Version Bump (Action)

Fail PR checks until `CHANGELOG.md` has an incremented version

```yml
on:
  pull_request:
    branches:
      - "main"

jobs:
  bump_version_check:
    runs-on: ubuntu-latest
    name: Bump Version Check

    env:
      # Required because the action uses `gh` cli tool to fetch the latest release
      GH_TOKEN: ${{ github.token }} 

    steps:
      - uses: actions/checkout@v4
        with:
          # Required to see data in other branches
          fetch-depth: 0 

      - name: "Check does CHANGELOG have a new version since last release"
        id: require-version-bump
        uses: brianhenryie/require-version-bump-action@main
        with:
          fail: true
```

Or don't fail, but use the result in a later step:

```yml
on:
  pull_request:
    branches:
      - "main"

jobs:
  bump_version_check:
    runs-on: ubuntu-latest
    name: Bump Version Check

    env:
      # Required because the action uses `gh` cli tool to fetch the latest release
      GH_TOKEN: ${{ github.token }} 

    steps:
      - uses: actions/checkout@v4
        with:
          # Required to see data in other branches
          fetch-depth: 0 

      - name: "Check does CHANGELOG have a new version since last release"
        id: require-version-bump
        uses: brianhenryie/require-version-bump-action@main

      - name: "Do something with the result"
        # `1` means the changelog is greater than the latest release version; `0` is equal; `-1` is less
        if: ${{ steps.require-version-bump.outputs.changelog-is-greater != 1 }} 
        run: echo "The changelog has not been incremented"
```
## The Problem

I often create a GitHub release having forgotten to increment the version number where it is hardcoded. This is intended to be a companion Action to a step which updates the version number where needed in your code. It's a minor safeguard so you don't forget – an ideal use case for CI.

## Acknowledgements

My colleague Evan, [circlecube](https://github.com/circlecube), at [Bluehost](https://github.com/bluehost/)/[Newfold](https://github.com/newfold-labs/), who highlighted a related problem.