# Install Compos on Linux

This guide installs the Linux x86_64 release of Compos.

The installer downloads a released executable. It does not build Compos from source.
It checks the archive and executable with SHA-256 checksums before installation.
It does not start the server.

## 1. Check the requirements

Use Linux on an x86_64 processor.
Use glibc 2.39 or later.
The release is built on Ubuntu 24.04.
Alpine Linux, ARM Linux, macOS, and Windows are not supported by this installer.

Use Bash, curl, tar, and GNU coreutils.
A C compiler, Elixir, Erlang, Rust, Zig, and the GitHub CLI are not required for this installation.

If the required commands are missing, install them with your system package manager.

For Ubuntu 24.04 or later, run:

```bash
sudo apt-get update
sudo apt-get install -y bash curl tar coreutils libc-bin libtinfo6 libstdc++6
```

For Fedora, run:

```bash
sudo dnf install -y bash curl tar coreutils glibc ncurses-libs libstdc++
```

## 2. Select the release repository

The installer uses `svs/compos` when `COMPOS_REPO` is not set.
It does not select another repository automatically.

Set `COMPOS_REPO` to use a mirror.
For example, `COMPOS_REPO=harsh098/compos` selects this fork.

The selected repository must have a published release marked Latest.
That release must contain these assets:

- `compos-linux-x86_64.tar.gz`
- `compos-linux-x86_64.tar.gz.sha256`

**Current availability:** `svs/compos` has no published Linux release.
Use `harsh098/compos` for installation now.

## 3. Install the available Linux release

Copy this complete block into a terminal:

```bash
(
  set -eu
  installer="$(mktemp)"
  trap 'rm -f "$installer"' EXIT
  curl --fail --silent --show-error --location \
    https://raw.githubusercontent.com/harsh098/compos/main/bin/install-linux.sh \
    --output "$installer"
  COMPOS_REPO=harsh098/compos bash "$installer"
)
```

The block downloads the [Linux installer](bin/install-linux.sh) from this fork.
The environment variable selects this fork's release assets.

The installer performs these operations:

1. Check the operating system, processor, glibc version, and required commands.
2. Find the latest release in the selected repository.
3. Download the archive and its checksum from that release.
4. Check the archive checksum.
5. Extract the executable.
6. Check the executable checksum.
7. Install the executable in `$HOME/.local/bin/compos`.
8. Remove the temporary download files.

The installer stops if a command or checksum check fails.
It does not require administrator access for the default installation directory.

After a successful installation, the terminal shows the executable path and release URL.

### Use the upstream default

When upstream publishes compatible Linux assets, run the downloaded script without `COMPOS_REPO`:

```bash
bash bin/install-linux.sh
```

Run this command from a checkout that contains the installer.
If `COMPOS_REPO` is already set in your terminal, remove it first:

```bash
unset COMPOS_REPO
bash bin/install-linux.sh
```

### Use another mirror or installation directory

Set `COMPOS_REPO` to select a release repository.
Set `COMPOS_BIN_DIR` to select the executable directory.

For example, run this command from the checkout:

```bash
COMPOS_REPO=harsh098/compos \
COMPOS_BIN_DIR="$HOME/apps/compos/bin" \
bash bin/install-linux.sh
```

The installer prints the correct start command for the selected directory.

## 4. Start Compos

For the default installation directory, run:

```bash
COMPOS_BIND=127.0.0.1 COMPOS_PORT=4004 "$HOME/.local/bin/compos"
```

Keep the terminal open while you use Compos.
The first start extracts the bundled runtime into the user data directory.

Wait for the terminal to show `http://localhost:4004`.
Then open <http://localhost:4004> in a browser.

To stop Compos, press `Ctrl+C` in the server terminal.
To start Compos again, run the same start command.

## 5. Find the installed files

| Item | Default location |
| --- | --- |
| Executable | `$HOME/.local/bin/compos` |
| Settings and saved data | `$HOME/.compos` |
| Extracted runtime | `$HOME/.local/share/.burrito` |

The runtime location can change when `XDG_DATA_HOME` is set.
Set `COMPOS_HOME` to select another directory for Compos settings and saved data.

The installer changes the executable.
It keeps your settings and saved data.

## 6. Install an update

Stop Compos before you install an update.
Run the installation block in section 3 again.
Then run the start command in section 4.

The installer replaces the executable after both checksum checks pass.

## 7. Remove the executable

Stop Compos.
Then run:

```bash
rm -f "$HOME/.local/bin/compos"
```

If you selected another installation directory, remove `compos` from that directory.
Your settings and saved data remain in place.

## Troubleshooting

### No published release exists

Check `COMPOS_REPO`.
The default is `svs/compos`.
Set `COMPOS_REPO=harsh098/compos` to use the available Linux release.

### A Linux asset is unavailable

Check the selected release on GitHub.
It must contain both asset names from section 2.
The installer does not use prereleases through the Latest release link.

### A checksum check fails

Do not start the downloaded executable.
Run the installer again to download a new copy.
If the check fails again, report the release URL and error message.

### The server cannot use port 4004

Stop the other Compos process, or select different ports:

```bash
COMPOS_BIND=127.0.0.1 COMPOS_PORT=4014 COMPOS_APP_PORT=4015 \
  "$HOME/.local/bin/compos"
```

Then open <http://localhost:4014>.

### The browser cannot connect

Keep the server terminal open.
Check that Compos has started without a fatal error.
Use the URL for the port selected in the start command.
