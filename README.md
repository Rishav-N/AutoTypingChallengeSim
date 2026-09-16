# URC autonomous-typing challenge simulator

A ROS 2 (Jazzy) simulator of the URC autonomous-typing task. A five-joint,
velocity-controlled arm with a forearm camera must find a keyboard on an
ArUco-marked panel and type a launch key. The simulator publishes camera
images, joint states and TF; you publish joint velocities and presses; a
browser dashboard lets a person watch the run. It runs in Docker on an
Ubuntu 24.04 machine.

## Requirements

- Ubuntu 24.04 on **arm64** — see "Check your architecture" below
- Docker, with the Compose plugin (`docker compose`)
- nothing else. ROS 2 Jazzy is inside the container images; a ROS 2 install on
  the machine itself is neither required nor used, and the simulator is
  invisible to it

## Clone the repository

Everything below runs from the clone, and the docs assume it lives at
`~/AutoTypingChallengeSim`. The clone URL is on this repository's page, under
the green **Code** button:

```bash
git clone <url> ~/AutoTypingChallengeSim
cd ~/AutoTypingChallengeSim
```

## Check your architecture

```bash
uname -m
```

`aarch64` or `arm64` is what you want. **arm64 is the only architecture
published today.** If `uname -m` says `x86_64`, stop here and open an issue (or
ask in the team chat): the tarballs are built by the maintainer from a checkout
that carries the site configuration, so an amd64 build cannot be produced from
this repository alone. Loading the arm64 tarball on an x86_64 machine fails
with `exec format error`, and emulation is far too slow for a 50 Hz loop.

## Get the image tarball

The container images ship as **release assets** on this repository's Releases
page, not inside the repository: each tarball is about 320 MB, and GitHub
rejects files over 100 MB in git.

From the clone, with the GitHub CLI:

```bash
gh release download --pattern 'urc-autotype-arm64.tar.gz*'
```

Without `gh`, download both files from the Releases page in a browser into this
directory, or use `curl` (the first line reads the owner and repository name
off the clone, so there is nothing to substitute by hand):

```bash
SLUG=$(git remote get-url origin | sed -E 's#(git@[^:]+:|https?://[^/]+/)##; s#\.git$##')
curl -L -O "https://github.com/$SLUG/releases/latest/download/urc-autotype-arm64.tar.gz"
curl -L -O "https://github.com/$SLUG/releases/latest/download/urc-autotype-arm64.tar.gz.sha256"
```

Check it against the published checksum, then load it:

```bash
sha256sum -c urc-autotype-arm64.tar.gz.sha256
docker load -i urc-autotype-arm64.tar.gz
docker image ls urc-autotype
```

One tarball restores **two** images. `docker image ls` must show a row for
`urc-autotype:sim` and a row for `urc-autotype:dev`, in either order; if one is
missing, the load failed — rerun it and read the output.

## Quick start

From the root of this repository, once the images are loaded:

```bash
mkdir -p member_ws/src
docker compose up -d
```

Then open the dashboard: **http://localhost:8080** on the machine running it,
or **http://\<that machine's IP\>:8080** from another machine on the network.

## Documentation

Two documents, and they do not overlap:

- **[docs/INFO.md](docs/INFO.md) — start here.** Full setup, everyday use,
  troubleshooting, and a suggested order to build things in.
- **[docs/INTERFACES.md](docs/INTERFACES.md) — the reference.** Every topic,
  message, number and rule.
