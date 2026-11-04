# Z1BR client-public

Public downloads for the Z1BR launcher. Each GitHub release here (`release-<N>`) holds the launcher installer,
`dinput8.dll` and a `manifest.json`. The source code lives in a private repository; this repo exists so anyone can
download and check what the launcher consists of.

The launcher only installs what is listed in a `manifest.json` that carries a valid signature from the owner's key
(`manifest.json.sig`, uploaded to the Z1BR website). The website serves the signed manifest; the launcher checks the
signature and the SHA-256 of every file before using it.

## Verify a download

**Compare the SHA-256.** The release notes list the SHA-256 of every file. Compare:

    certutil -hashfile "KotK.Launcher_<version>_x64-setup.exe" SHA256

**Verify the signature** (needs the signed `manifest.json` and `manifest.json.sig` from the website, byte for byte):

    rebirth-sign verify --keys <dir with primary.pub and backup.pub> manifest.json manifest.json.sig

or with [minisign](https://jedisct1.github.io/minisign/):

    minisign -Vm manifest.json -x manifest.json.sig -P <public key line below>

Then compare the hashes in the verified manifest with your files.

### Where to get manifest.json.sig

The `manifest.json` attached to a release here is the file the owner signs; the signature is made offline afterwards,
so a fresh release has no `.sig` yet. To get the signed pair:

- **From the website** (what the launcher itself uses): `GET <site>/api/launcher/release` returns
  `{"manifest": "<base64 of manifest.json>", "signature": "<text of manifest.json.sig>"}` for the current release
  (`?version=<launcher version>` for an older launcher's release). Decode without changing a byte, e.g. in PowerShell:

      $r = Invoke-RestMethod <site>/api/launcher/release
      [IO.File]::WriteAllBytes("$PWD\manifest.json", [Convert]::FromBase64String($r.manifest))
      [IO.File]::WriteAllText("$PWD\manifest.json.sig", $r.signature)

- **From the release here**, when the owner has attached `manifest.json.sig` to it after signing (optional).

Either way the signature must verify against the release's `manifest.json` byte for byte; a changed line ending or BOM
makes it fail.

## Public keys

These are **TEST keys** until the owner's key ceremony is done. Do not trust releases signed with them.

primary:

    RWR/zZlGOP+FDMWVx/bobVse6VtKN9SPfVrCTU21LjfmjPKKmt4VwA9s

backup (revocation only):

    RWS/RH4cUjBPFqmakIQHxmKlHSqIsNv3SwRaSEin3MszNaBzmS7vWdOP

The authoritative copy is `release-keys/` in the launcher source and on the website; this README is updated when the
real keys are installed.
