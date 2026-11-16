# Notification Hub releases

This directory contains the canonical files for the separate public binary
release repository `denor81/tg_inform_hub_releases`.

This file is also the canonical public-repository README. Copy
`attest-release.yml` to `.github/workflows/attest-release.yml`, copy
`install.sh` to the repository root, and copy this file to `README.md`. The
public repository must not receive this private source repository's Git
history.

The public release channel contains only its minimal README and bootstrap,
protected workflow, immutable release artifacts, strict manifests, and GitHub
Artifact Attestation bundles. It must not contain Hub configuration,
credentials, databases, logs, notification content, or private source files.

## Install a verified public release

The bootstrap needs no GitHub account or server-side token. Download it over
HTTPS from the protected public default branch, inspect it if desired, and run
it separately rather than piping network content directly into a root shell:

```bash
curl --fail --silent --show-error --location \
  --output /tmp/install-notification-hub.sh \
  https://raw.githubusercontent.com/denor81/tg_inform_hub_releases/main/install.sh
chmod 0755 /tmp/install-notification-hub.sh
/tmp/install-notification-hub.sh --list
sudo /tmp/install-notification-hub.sh
```

The default is the latest stable release. To select an exact stable or approved
release candidate:

```bash
sudo /tmp/install-notification-hub.sh --version v0.3.0
sudo /tmp/install-notification-hub.sh --channel rc
sudo /tmp/install-notification-hub.sh --version v0.3.0-rc.1
```

The bootstrap downloads a checksum-pinned GitHub CLI verifier, verifies the
manifest's GitHub Artifact Attestation before trusting its digest, verifies the
artifact digest and attestation, checks every archive entry against the strict
manifest, and only then invokes the packaged installer. Existing A/B installs
must update with `sudo notify-hub update apply`; the bootstrap never bypasses
the installed transaction engine.
