# Rocky OSS apt repository

GPG-signed apt repository for [i-rocky](https://github.com/i-rocky)'s CLI
tools, served from Cloudflare R2 at **https://apt.clapbox.net**.

Docs and the package list: **https://i-rocky.github.io/apt/**

## Use it

```sh
curl -fsSL https://apt.clapbox.net/rocky-oss.gpg \
  | sudo tee /usr/share/keyrings/rocky-oss.gpg >/dev/null
echo "deb [signed-by=/usr/share/keyrings/rocky-oss.gpg] https://apt.clapbox.net stable main" \
  | sudo tee /etc/apt/sources.list.d/rocky-oss.list
sudo apt update
```

Then `sudo apt install <package>`. Available packages are listed on the
[docs page](https://i-rocky.github.io/apt/) and in
[`dists/stable/main/binary-amd64/Packages`](https://apt.clapbox.net/dists/stable/main/binary-amd64/Packages).

Architectures: `amd64`, `arm64`. Suite: `stable`. Component: `main`.

## How it works

- Each tool's release workflow builds `.deb` packages with `cargo-deb` and
  attaches them to its GitHub release.
- [`scripts/publish-apt.sh`](scripts/publish-apt.sh) downloads the `.deb`s
  from the latest release of every repo in its `REPOS` array, regenerates
  the apt metadata (`Packages`, `Release`, `InRelease`), signs it with the
  Rocky OSS signing key, and uploads the tree to R2 via wrangler.
- The [`publish`](.github/workflows/publish.yml) workflow runs the script
  on a schedule, on manual dispatch, and on `repository_dispatch` events
  (type `publish-apt`) sent by tool releases.

## Adding a package

1. Make the tool's release workflow produce `.deb` assets (see limitbar's
   release workflow for the `cargo-deb` pattern).
2. Add the GitHub repo to the `REPOS` array in `scripts/publish-apt.sh`.
3. Add it to the package table in `docs/index.html`.
4. Run the publish workflow (or wait for the schedule).

## Signing key

Fingerprint: `C7752A90E90DC6FE1CD1699FEE6ECE90DAAFB8E6`
(`Rocky OSS APT Repository <smrockypk@gmail.com>`). The public key is served
at [`/rocky-oss.gpg`](https://apt.clapbox.net/rocky-oss.gpg) (binary) and
[`/rocky-oss.asc`](https://apt.clapbox.net/rocky-oss.asc) (armored).

## License

MIT
