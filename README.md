[![CI](https://github.com/lpenz/ghworkflow-rust/actions/workflows/ci.yml/badge.svg)](https://github.com/lpenz/ghworkflow-rust/actions/workflows/ci.yml)


# ghworkflow-rust

This repository provides a reusable github workflow for rust
projects. The workflow runs the following jobs:
- [cargo-check]
- [rustfmt]
- [clippy]
- [cargo-test]: runs `cargo test` with coverage and uploads results
  to coveralls and/or codecov.
- [publish-crate]: uploads crate to [crates.io]; requires
  `CARGO_REGISTRY_TOKEN` to be passed as a secret.
- [cargo-deb]: installs and run `cargo-deb`; copies manual to crate
  directory, if present.
  (optional)
- [packagecloud](packagecloud-action): uploads the cargo-deb debian package to
  [packagecloud]; requires `PACKAGECLOUD_TOKEN` to be passed as a
  secret.
  (optional)


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
      codecov: true
      deb: true
      packagecloud: true
    secrets:
      CARGO_REGISTRY_TOKEN: ${{ secrets.CARGO_REGISTRY_TOKEN }}
      PACKAGECLOUD_TOKEN: ${{ secrets.PACKAGECLOUD_TOKEN }}
```

You may have to enable public reusable workflow usage in your
organization. See [reusing-workflows] for more information.


### Inputs

- coveralls: makes `cargo-test` upload test coverage data to
  [coveralls] when `true`.
- codecov: makes `cargo-test` upload test coverage data to [codecov]
  when `true`.
- deb: enables `cargo-deb` when `true`.
- packagecloud: enables the `packagecloud` job that uploads the Debian
  package built by `cargo-deb` job to [packagecloud]. Requires the
  `PACKAGECLOUD_TOKEN` secret. Also requires the `deb` input to be
  `true`.


[cargo-check]: https://doc.rust-lang.org/cargo/commands/cargo-check.html
[rustfmt]: https://crates.io/crates/rustfmt-nightly
[clippy]: https://github.com/actions-rs/clippy-check
[cargo-test]: https://doc.rust-lang.org/cargo/commands/cargo-test.html
[publish-crate]: https://github.com/marketplace/actions/publish-crates
[cargo-deb]: https://crates.io/crates/cargo-deb
[packagecloud-action]: https://github.com/marketplace/actions/deploy-to-packagecloud-io
[crates.io]: https://crates.io/
[packagecloud]: https://packagecloud.io/
[reusing-workflows]: https://docs.github.com/en/actions/using-workflows/reusing-workflows
[coveralls]: https://coveralls.io/
[codecov]: https://codecov.io/
