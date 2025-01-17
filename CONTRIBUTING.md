# Contributing

## Updating libgit2

The following steps can be used to update libgit2:

1. Update the submodule.
   There are several ways to go about this.
   One way is to go to the `libgit2-sys/libgit2` directory and run `git fetch origin` to download the latest updates, and then check out a specific tag (such as `git checkout v1.4.1`).
2. Update all the references to the version:
    * Update [`libgit2-sys/build.rs`](https://github.com/rust-lang/git2-rs/blob/master/libgit2-sys/build.rs).
      There is a version probe (search for `cfg.range_version`) which should be updated.
    * Update the version in
      [`libgit2-sys/Cargo.toml`](https://github.com/rust-lang/git2-rs/blob/master/libgit2-sys/Cargo.toml).
      Update the metadata portion (the part after the `+`) to match libgit2.
      Also bump the Cargo version (the part before the `+`), keeping in mind
      if this will be a SemVer breaking change or not.
    * Update the dependency version in [`Cargo.toml`](https://github.com/rust-lang/git2-rs/blob/master/Cargo.toml) to match the version in the last step (do not include the `+` metadata).
      Also update the version of the `git2` crate itself so it will pick up the change to `libgit2-sys` (also keeping in mind if it is a SemVer breaking release).
    * Update the version in [`README.md`](https://github.com/rust-lang/git2-rs/blob/master/README.md) if needed.
    * If there was a SemVer-breaking version bump for either library, also update the `html_root_url` attribute in the `lib.rs` of each library.
3. Run tests.
   `cargo test -p git2 -p git2-curl` is a good starting point.
4. Run `systest`.
   This will validate for any C-level API problems.
   Unfortunately `systest` does not work on nightly, so you'll need to use stable.

   `cargo +stable run -p systest`

   The changelog at <https://github.com/libgit2/libgit2/blob/main/docs/changelog.md>
   can be helpful for seeing what has changed.
   The project has recently started labeling API and ABI breaking changes with labels:
   <https://github.com/libgit2/libgit2/pulls?q=is%3Apr+label%3A%22api+breaking%22%2C%22abi+breaking%22+is%3Aclosed>
4. Once you have everything functional, publish a PR with the updates.

## Release process

Checklist for preparing for a release:

- Make sure the versions have been bumped and are pointing at what is expected.
    - Version of `libgit2-sys`
    - Version of `git2`
    - Version of `git2-curl`
    - `git2`'s dependency on `libgit2-sys`
    - `git2-curl`'s dependency on `git2`
    - The libgit2 version probe in `libgit2-sys/build.rs`
    - Update the version in `README.md`
    - Check the `html_root_url` values in the source code.
- Update the change logs:
    - [`CHANGELOG.md`](https://github.com/rust-lang/git2-rs/blob/master/CHANGELOG.md)
    - [`libgit2-sys/CHANGELOG.md`](https://github.com/rust-lang/git2-rs/blob/master/libgit2-sys/CHANGELOG.md)
    - [`git2-curl/CHANGELOG.md`](https://github.com/rust-lang/git2-rs/blob/master/git2-curl/CHANGELOG.md)

To publish the new release:

- In a fresh clone, make sure you run `git submodule update`.
- For each updated package, run `cargo publish` (`libgit2-sys` then `git2` then `git2-curl`).
- Set tags for each package that was update:
    - `git tag 0.16.1`
    - `git tag libgit2-sys-0.14.2+1.5.1`
    - `git tag git2-curl-0.17.0`
- Push the tags (substitute the "origin" remote name if you are using a different name):
    - `git push origin 0.16.1`
    - `git push origin libgit2-sys-0.14.2+1.5.1`
    - `git push origin git2-curl-0.17.0`
