[![CI](https://github.com/lpenz/ghworkflow-rust/actions/workflows/ci.yml/badge.svg)](https://github.com/lpenz/ghworkflow-rust/actions/workflows/ci.yml)


# ghworkflow-rust

This repository provides a reusable github workflow for rust
projects. The workflow runs the following jobs:
- cargo-check
- rustfmt
- clippy
- test-coverage: runs `cargo test` with coverage and uploads results
  to coveralls.
- cargo-deb: copies manual to crate directory, if present
- publish-crate: uploads crate to [crates.io]; requires
  `CARGO_REGISTRY_TOKEN` to be passed as a secret.
- packagecloud: uploads the cargo-deb debian package to
  [packagecloud]; requires `PACKAGECLOUD_TOKEN` to be passed as a
  secret.


## Usage

To use this worflow, with both packagecloud and crates.io uploads
enabled, use the following in your `.github/workflows/ci.yml`:

```.yml
---
name: CI
on: [ push, pull_request ]
jobs:
  rust:
    uses: lpenz/ghworkflow-rust/.github/workflows/rust.yml@v0.2
    with:
      coveralls: true
    secrets:
      CARGO_REGISTRY_TOKEN: ${{ secrets.CARGO_REGISTRY_TOKEN }}
      PACKAGECLOUD_TOKEN: ${{ secrets.PACKAGECLOUD_TOKEN }}
```

You may have to enable public reusable workflow usage in your
organization. See [reusing-workflows] for more information.


[crates.io]: https://crates.io/
[packagecloud]: https://packagecloud.io/
[reusing-workflows]: https://docs.github.com/en/actions/using-workflows/reusing-workflows
