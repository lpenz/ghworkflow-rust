[![CI](https://github.com/lpenz/ghworkflow-rust/actions/workflows/ci.yml/badge.svg)](https://github.com/lpenz/ghworkflow-rust/actions/workflows/ci.yml)


# ghworkflow-rust

This repository provides a reusable github workflow for rust
projects. The workflow runs the following jobs:
- *[cargo-build-release]*: does a `cargo build --release` and makes the `target`
  directory available for other jobs.
- *[cargo-check]*
- *[cargo-test]*: runs `cargo test` with coverage and uploads results
  to [coveralls.io] and/or [codecov.io].
- *[rustfmt]*
- *[clippy]*
- *[cargo-audit]*
- *[cargo-deb]*: installs and run `cargo-deb`; copies manual to crate
  directory, if present.
  (optional)
- *publish-cratesio*: uses [publish-crate] to publish the crate
  to [crates.io] when the repository is tagged with a version.
  Requires the `CARGO_REGISTRY_TOKEN` secret.
  (optional)
- *publish-packagecloud*: uses [packagecloud] to upload
  the Debian package built by `cargo-deb` job to
  [packagecloud.io] when the repository is tagged with a
  version. Requires the `PACKAGECLOUD_TOKEN` secret and the
  `deb` input to be `true`.
  (optional)
- *publish-github-release*: uses
  [action-automatic-releases] to publish a [github release]
  when the repository is tagged with a version.
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
    uses: lpenz/ghworkflow-rust/.github/workflows/rust.yml@v0.10.0
    with:
      coveralls: true
      codecov: true
      deb: true
      packagecloud: true
      publish_packagecloud_repository: |
        ["debian/debian/bullseye", "ubuntu/ubuntu/focal"]
    secrets:
      CARGO_REGISTRY_TOKEN: ${{ secrets.CARGO_REGISTRY_TOKEN }}
      PACKAGECLOUD_TOKEN: ${{ secrets.PACKAGECLOUD_TOKEN }}
```

You may have to enable public reusable workflow usage in your
organization. See [reusing-workflows] for more information.


### Inputs

- `coveralls`: makes *cargo-test* upload test coverage data to
  [coveralls.io] when `true`.
- `codecov`: makes *cargo-test* upload test coverage data to [codecov.io]
  when `true`.
- `deb`: enables *cargo-deb* when `true`.
- `dependencies_debian`: dependencies as Debian packages to install;
   used in the appropriate actions if defined
- `publish_cratesio`: enables the *publish-cratesio* job.
- `publish_github_release`: enables the *publish-github-release* job.
- `publish_github_release_files`: files to publish in the github
  release.
- `publish_packagecloud`: enables the *publish-packagecloud* job.
- `publish_packagecloud_repository`: json list with packagecloud
  repositories to publish .deb.


[cargo-build-release]: https://doc.rust-lang.org/cargo/commands/cargo-build.html
[cargo-check]: https://doc.rust-lang.org/cargo/commands/cargo-check.html
[cargo-test]: https://doc.rust-lang.org/cargo/commands/cargo-test.html
[rustfmt]: https://crates.io/crates/rustfmt-nightly
[clippy]: https://github.com/actions-rs/clippy-check
[cargo-audit]: https://crates.io/crates/cargo-audit
[cargo-deb]: https://crates.io/crates/cargo-deb
[publish-crate]: https://github.com/marketplace/actions/publish-crates
[packagecloud]: https://github.com/marketplace/actions/deploy-to-packagecloud-io
[action-automatic-releases]: https://github.com/marketplace/actions/automatic-releases
[github release]: https://docs.github.com/en/repositories/releasing-projects-on-github/managing-releases-in-a-repository
[crates.io]: https://crates.io/
[packagecloud.io]: https://packagecloud.io/
[reusing-workflows]: https://docs.github.com/en/actions/using-workflows/reusing-workflows
[coveralls.io]: https://coveralls.io/
[codecov.io]: https://codecov.io/
