[![CI](https://github.com/lpenz/ghworkflow-rust/actions/workflows/ci.yml/badge.svg)](https://github.com/lpenz/ghworkflow-rust/actions/workflows/ci.yml)
[![github](https://img.shields.io/github/v/release/lpenz/ghworkflow-rust?logo=github)](https://github.com/lpenz/ghworkflow-rust/releases)


# ghworkflow-rust

This repository provides reusable github workflows for rust
projects. The *rust* workflow is a wrapper that runs the *rust-test*
workflow (all checks and tests) followed by the *rust-deploy*
workflow (release build, packages and publishing). The workflows run
the following jobs:

*rust-test*:
- *[cargo-check]*
- *[cargo-doc]*
- *[cargo-test]*: runs `cargo test` with coverage and uploads results
  to [coveralls.io] and/or [codecov.io].
- *[rustfmt]*
- *[clippy]*
- *[cargo-audit]*
- *[cargo-machete]*: detects unused dependencies.
- *msrv*: checks that the project compiles with the minimum supported
  Rust version (from `rust-version` in `Cargo.toml`). Skips if not set.
- *rust-misc*: misc checks; for now it checks if the Cargo.lock
  version matches the one in Cargo.toml.
- *[cargo-semver-checks]*: checks semver violations before
  publishing.
- *deb*: runs *cargo-deb* on a single architecture to check that
  the Debian packaging works.
  (optional, enabled by the `deb` input)
- *rpm*: runs *cargo-generate-rpm* on a single architecture to
  check that the RPM packaging works.
  (optional, enabled by the `rpm` input)

*rust-deploy*:
- *release*: release build. Optionally creates .tar.gz with the files
  specified in `release_files`.
- *deb*: installs and runs [cargo-deb]; copies manual to the
  crate directory, if present.
  (optional)
- *rpm*: installs and runs [cargo-generate-rpm]; copies manual
  to the crate directory, if present.
  (optional)
- *publish-cratesio*: uses [publish-crate] to publish the crate
  to [crates.io] when the repository is tagged with a version.
  Requires the `CARGO_REGISTRY_TOKEN` secret.
  (optional)
- *publish-packagecloud-deb*: uses [packagecloud] to upload
  the Debian package built by the `deb` job to
  [packagecloud.io] when the repository is tagged with a
  version. Requires the `PACKAGECLOUD_TOKEN` secret and the
  `deb` input to be `true`.
  (optional)
- *publish-packagecloud-rpm*: uses [packagecloud] to upload
  the RPM package built by the `rpm` job to
  [packagecloud.io] when the repository is tagged with a
  version. Requires the `PACKAGECLOUD_TOKEN` secret and the
  `rpm` input to be `true`.
  (optional)
- *publish-github-release*: uses
  [action-automatic-releases] to publish a [github release]
  when the repository is tagged with a version.
  (optional)


## Usage

To use the *rust* workflow, with both packagecloud and crates.io
uploads enabled, use the following in your
`.github/workflows/ci.yml`:

```yml
---
name: CI
on: [ push, pull_request, workflow_dispatch ]
jobs:
  rust:
    uses: lpenz/ghworkflow-rust/.github/workflows/rust.yml@v0.30.0
    with:
      coveralls: true
      codecov: true
      deb: true
      packagecloud: true
      publish_packagecloud_repository_deb: |
        ["debian/debian/bookworm", "ubuntu/ubuntu/jammy"]
    secrets:
      CARGO_REGISTRY_TOKEN: ${{ secrets.CARGO_REGISTRY_TOKEN }}
      PACKAGECLOUD_TOKEN: ${{ secrets.PACKAGECLOUD_TOKEN }}
```

The *rust* workflow forwards the inputs and secrets to the
*rust-test* and *rust-deploy* workflows. If you don't want to use the
wrapper, you can call the two workflows directly:

```yml
---
name: CI
on: [ push, pull_request, workflow_dispatch ]
jobs:
  test:
    uses: lpenz/ghworkflow-rust/.github/workflows/rust-test.yml@v0.30.0
    with:
      coveralls: true
      codecov: true
  deploy:
    needs: [test]
    uses: lpenz/ghworkflow-rust/.github/workflows/rust-deploy.yml@v0.30.0
    with:
      deb: true
      rpm: true
      release_files: mycrate
    secrets:
      CARGO_REGISTRY_TOKEN: ${{ secrets.CARGO_REGISTRY_TOKEN }}
      PACKAGECLOUD_TOKEN: ${{ secrets.PACKAGECLOUD_TOKEN }}
```

You may have to enable public reusable workflow usage in your
organization. See [reusing-workflows] for more information.


### Inputs

- `enable_cargo-semver-checks`: enables cargo-semver-checks - default
  is `true`.
- `test_features`: JSON list of test-runs to execute in the
  *cargo-test* job. Each element is the full set of cargo arguments
  for one test run, e.g. `["--all-features"]` (the default) or
  `["--no-default-features", "--features std"]`. An empty string
  element means to test with no features, e.g. `[""]`. Tests are run
  for each element one by one, and coverage data is accumulated across all
  runs.
- `coveralls`: makes *cargo-test* upload test coverage data to
  [coveralls.io] when `true`.
- `codecov`: makes *cargo-test* upload test coverage data to [codecov.io]
  when `true`.
- `deb`: when `true`, enables the *deb* job in *rust-test* and
  the *deb* job in *rust-deploy*.
- `rpm`: when `true`, enables the *rpm* job in *rust-test* and
  the *rpm* job in *rust-deploy*.
- `dependencies_debian`: dependencies as Debian packages to install;
   used in the appropriate actions if defined
- `release_files`: files to publish in the github release .tar.gz.
- `publish_cratesio`: enables the *publish-cratesio* job.
- `publish_github_release`: enables the *publish-github-release* job.
- `publish_packagecloud_repository_deb`: json list with packagecloud
  repositories to publish .deb. When defined, it enables the
  *publish-packagecloud-deb* job.
- `publish_packagecloud_repository_rpm`: json list with packagecloud
  repositories to publish .rpm. When defined, it enables the
  *publish-packagecloud-rpm* job.


[cargo-build-release]: https://doc.rust-lang.org/cargo/commands/cargo-build.html
[cargo-check]: https://doc.rust-lang.org/cargo/commands/cargo-check.html
[cargo-doc]: https://doc.rust-lang.org/cargo/commands/cargo-doc.html
[cargo-test]: https://doc.rust-lang.org/cargo/commands/cargo-test.html
[rustfmt]: https://crates.io/crates/rustfmt-nightly
[clippy]: https://crates.io/crates/clippy
[cargo-audit]: https://crates.io/crates/cargo-audit
[cargo-machete]: https://crates.io/crates/cargo-machete
[cargo-deb]: https://crates.io/crates/cargo-deb
[cargo-generate-rpm]: https://crates.io/crates/cargo-generate-rpm
[publish-crate]: https://github.com/marketplace/actions/publish-crates
[packagecloud]: https://github.com/marketplace/actions/deploy-to-packagecloud-io
[action-automatic-releases]: https://github.com/marketplace/actions/automatic-releases
[github release]: https://docs.github.com/en/repositories/releasing-projects-on-github/managing-releases-in-a-repository
[crates.io]: https://crates.io/
[packagecloud.io]: https://packagecloud.io/
[reusing-workflows]: https://docs.github.com/en/actions/using-workflows/reusing-workflows
[coveralls.io]: https://coveralls.io/
[codecov.io]: https://codecov.io/
[cargo-semver-checks]: https://github.com/obi1kenobi/cargo-semver-checks-action
