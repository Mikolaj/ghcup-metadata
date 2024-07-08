# GHCup metadata

This repo is a collection of different GHCup metadata. These are mappings from tool versions (e.g. GHC 9.6.5)
to bindist URLs (e.g. https://downloads.haskell.org/~ghc/9.6.5/ghc-9.6.5-x86_64-fedora33-linux.tar.xz), depending
on architecture (e.g. X86_64), platform (e.g Linux) and possibly distro (e.g. Fedora).

## For end users

### Metadata variants (distribution channels)

* `ghcup-A.B.C.yaml`: this is the main metadata and what ghcup uses by default
* `ghcup-vanilla-A.B.C.yaml`: this is similar to `ghcup-A.B.C.yaml`, but only uses upstream bindists (no patches/fixes are applied, no missing platforms added)
* `ghcup-prereleases-A.B.C.yaml`: this contains pre-releases of all tools
* `ghcup-cross-A.B.C.yaml`: this contains experimental cross compilers. See https://www.haskell.org/ghcup/guide/#cross-support for details.

### Using the metadata

If you want access to both pre-releases and cross compilers, run:

```
ghcup config add-release-channel https://raw.githubusercontent.com/haskell/ghcup-metadata/master/ghcup-prereleases-0.0.7.yaml
ghcup config add-release-channel https://raw.githubusercontent.com/haskell/ghcup-metadata/master/ghcup-cross-0.0.8.yaml
```

If you want **only** vanilla upstream bindists and opt out of all unofficial stuff, you'd run:

```sh
ghcup config set url-source https://raw.githubusercontent.com/haskell/ghcup-metadata/master/ghcup-vanilla-0.0.8.yaml
```

Also check the [config.yaml documentation](https://github.com/haskell/ghcup-hs/blob/master/data/config.yaml).

## For contributors

Most bindists are built downstream by the GHCup developers, e.g.:

* https://github.com/stable-haskell/haskell-language-server
* https://github.com/stable-haskell/cabal
* https://github.com/stable-haskell/stack

For GHC bindists there is no automation yet and it is done manually (e.g. for FreeBSD and Alpine i386).

That makes contributions harder, because your pull requests to the metadata will be partial.

## For upstream developers

GHC, Cabal, HLS and stack developers distribute their own bindists via the `ghcup-vanilla-A.B.C.yaml` files. These
files are primarily maintained by upstream developers at their own discretion. The GHCup project will perform no QA, fixes
etc. on these bindists.

This allows a clean separation between projects. Also see
[GHCup is not an installer](https://hasufell.github.io/posts/2023-11-14-ghcup-is-not-an-installer.html).

Updates to `ghcup-A.B.C.yaml` are carried out by the GHCup project.

### Understanding tags

Tags are documented [here](https://github.com/haskell/ghcup-hs/blob/master/lib/GHCup/Types.hs). Search for `data Tag`.
Some tags are unique. Uniqueness is checked by `cabal run ghcup-gen -- check -f ghcup-<yaml-ver>.yaml`.

If you want to check prereleases, do: `cabal run ghcup-gen -- check -f ghcup-prereleases-<yaml-ver>.yaml --channel=prerelease`

### During a pull request

* For local testing see `cabal run ghcup-gen -- --help`
* make sure to run the bindist action to check tool installation on all platforms: https://github.com/haskell/ghcup-metadata/actions/workflows/bindists.yaml
  - this is a manual pipeline
  - set the appropriate parameters
* make sure to sign the yaml files you edited, e.g.: `gpg --detach-sign -u <your-email> ghcup-0.0.7.yaml` or ask a GHCup developer to sign
  - PGP pubkeys need to be cross-signed by the GHCup team
  - they need to be added to the CI: https://github.com/haskell/ghcup-metadata/blob/develop/.github/workflows/sigs
  - and need to be documented on the homepage
    * https://github.com/haskell/ghcup-hs/blob/master/docs/guide.md#gpg-verification
	* https://github.com/haskell/ghcup-hs/blob/master/docs/install.md#unix

