# Install Compos on Linux

Use a Linux x86_64 computer with glibc. This release does not support Alpine Linux.

Copy this complete block into a Bash terminal. It installs Compos in `$HOME/.local/bin` and starts it.

```bash
bash <<'COMPOS_INSTALL'
set -euo pipefail

if [ "$(uname -s)" != Linux ] || [ "$(uname -m)" != x86_64 ]; then
  printf 'Compos needs Linux x86_64.\n' >&2
  exit 1
fi

for tool in curl tar sha256sum install mktemp getconf; do
  if ! command -v "$tool" >/dev/null 2>&1; then
    printf 'Install %s, then run this block again.\n' "$tool" >&2
    exit 1
  fi
done

if ! getconf GNU_LIBC_VERSION >/dev/null 2>&1; then
  printf 'Compos needs a Linux system with glibc.\n' >&2
  exit 1
fi

work_dir="$(mktemp -d)"
trap 'rm -rf "$work_dir"' EXIT
cd "$work_dir"

url='https://github.com/harsh098/compos/releases/latest/download'
curl --fail --location --retry 3 --output compos-linux-x86_64.tar.gz "$url/compos-linux-x86_64.tar.gz"
curl --fail --location --retry 3 --output compos-linux-x86_64.tar.gz.sha256 "$url/compos-linux-x86_64.tar.gz.sha256"
sha256sum --check compos-linux-x86_64.tar.gz.sha256
tar -xzf compos-linux-x86_64.tar.gz
sha256sum --check SHA256SUMS

mkdir -p "$HOME/.local/bin"
install -m 0755 compos-linux-x86_64 "$HOME/.local/bin/compos"
printf 'Installed Compos at %s/.local/bin/compos\n' "$HOME"

cd "$HOME"
COMPOS_BIND=127.0.0.1 COMPOS_PORT=4004 "$HOME/.local/bin/compos"
COMPOS_INSTALL
```

Open <http://localhost:4004> in a browser. Keep the terminal open while you use Compos.

To stop Compos, press `Ctrl+C` in that terminal.

To start Compos again, run this command:

```bash
COMPOS_BIND=127.0.0.1 COMPOS_PORT=4004 "$HOME/.local/bin/compos"
```

To install a newer release, run the first block again. The block keeps your Compos data in `$HOME/.compos`.
