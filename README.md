[![CI](https://github.com/lpenz/ghworkflow-rust/actions/workflows/ci.yml/badge.svg)](https://github.com/lpenz/ghworkflow-rust/actions/workflows/ci.yml)


# ghworkflow-rust

This repository provides a reusable github workflow for rust
projects. The workflow runs the following jobs:
- [cargo-check]
- [rustfmt]
- [clippy]
- [cargo-test]: runs `cargo test` with coverage and uploads results
  to coveralls and/or codecov.
- [cargo-audit]
- [publish-crate]: uploads crate to [crates.io]; requires
  `CARGO_REGISTRY_TOKEN` to be passed as a secret.
- [cargo-deb]: installs and run `cargo-deb`; copies manual to crate
  directory, if present.
  (optional)
- [packagecloud]: uploads the cargo-deb debian package to
  [packagecloud.io]; requires `PACKAGECLOUD_TOKEN` to be passed as a
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
    uses: lpenz/ghworkflow-rust/.github/workflows/rust.yml@v0.5
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

- `coveralls`: makes `cargo-test` upload test coverage data to
  [coveralls.io] when `true`.
- `codecov`: makes `cargo-test` upload test coverage data to [codecov.io]
  when `true`.
- `deb`: enables `cargo-deb` when `true`.
- `publish_cratesio`: uses [publish-crate] to publish the crate
  to [crates.io] when the repository is tagged with a version.
- `publish_github_release`: uses
  [action-automatic-releases] to publish a github release
  when the repository is tagged with a version.
- `publish_github_release_files`: files to publish in the github release.
- `publish_packagecloud`: uses [packagecloud] to upload
  the Debian package built by `cargo-deb` job to
  [packagecloud.io] when the repository is tagged with a
  version. Requires the `PACKAGECLOUD_TOKEN` secret and the
  `deb` input to be `true`.
- `publish_packagecloud_repository`: packagecloud repository to
  publish .deb.


[cargo-check]: https://doc.rust-lang.org/cargo/commands/cargo-check.html
[rustfmt]: https://crates.io/crates/rustfmt-nightly
[clippy]: https://github.com/actions-rs/clippy-check
[cargo-test]: https://doc.rust-lang.org/cargo/commands/cargo-test.html
[cargo-audit]: https://crates.io/crates/cargo-audit
[publish-crate]: https://github.com/marketplace/actions/publish-crates
[cargo-deb]: https://crates.io/crates/cargo-deb
[packagecloud]: https://github.com/marketplace/actions/deploy-to-packagecloud-io
[action-automatic-releases]: https://github.com/marketplace/actions/automatic-releases
[crates.io]: https://crates.io/
[packagecloud.io]: https://packagecloud.io/
[reusing-workflows]: https://docs.github.com/en/actions/using-workflows/reusing-workflows
[coveralls.io]: https://coveralls.io/
[codecov.io]: https://codecov.io/
