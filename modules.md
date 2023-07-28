# Go modules within the Vault repository

There are a number of Go modules within https://github.com/hashicorp/vault.

At https://github.com/hashicorp/vault#importing-vault it states:

> This repository publishes two libraries that may be imported by other projects:
  `github.com/hashicorp/vault/api` and `github.com/hashicorp/vault/sdk`.

No official changelog is maintained for these libraries, despite them having
their own version numbers, distinct from Vault itself. The main purpose of this
document is to record what I have deduced from Git history, so that it may be
helpful to others, and I don't have to re-deduce it in the future.

## Concepts in the tables below

### "Vault timeline" column

The libraries have their own version numbers. To give context to these, the
point in the Vault development timeline at which library release took place is
included.

A new library version is usually tagged *shortly before* the creation of a new
Vault release branch. This means the tagged library will contain code similar
to, but **not necessarily the same as**, the embedded SDK code in the subsequent
Vault v1.x.0 release (as fixes are made by backporting to the release branch,
before the release occurs). These are described as "v1.x branch point".

Library versions may also be released intermittently between Vault releases, if
it seems useful to do so. These are described as "v1.x mid-development".

Rarely, library versions may be released from Vault release branches - typically
done when the release made at the branch point needs some minor fixes discovered
after branching. These are described as "v1.x release branch".

### "Branch" column

The Git branch from which the library release was made.

The notation "(detached)" is added to denote a release tag that is on no branch,
or a feature branch, containing just one small change - generally a version
increment - off a major branch.

## `github.com/hashicorp/vault/api`

The `api` package mostly provides Go client code for calling the Vault HTTP API.

It does also contain some code which is only used when writing Vault plugins,
which really ought to be in the `sdk` module - this is in `plugin_helpers.go`.

It claims to provide a stable API and for the most part succeeds.

| API    | Vault timeline        | Branch          | Notes                                                          |
|--------|-----------------------|-----------------|----------------------------------------------------------------|
| v1.9.2 | v1.14 branch point    | main            |                                                                |
| v1.9.1 | v1.14 mid-development | main            |                                                                |
| v1.9.0 | v1.13 branch point    | main            | No longer depends on `sdk` - much reduced dependency footprint |
| v1.8.3 | v1.13 mid-development | main            |                                                                |
| v1.8.2 | v1.13 mid-development | main            |                                                                |
| v1.8.1 | v1.13 mid-development | main            |                                                                |
| v1.8.0 | v1.12 branch point    | main            |                                                                |
| v1.7.2 | v1.12 mid-development | main            |                                                                |
| v1.7.1 | v1.12 mid-development | main            |                                                                |
| v1.7.0 | v1.12 mid-development | main            |                                                                |
| v1.6.0 | v1.11 branch point    | main            |                                                                |
| v1.5.0 | v1.11 mid-development | main            |                                                                |
| v1.4.1 | v1.10 branch point    | main (detached) |                                                                |
| v1.4.0 | v1.10 mid-development | main (detached) |                                                                |
| v1.3.1 | v1.10 mid-development | main            |                                                                |
| v1.3.0 | v1.9 branch point     | main (detached) |                                                                |
| v1.2.0 | v1.9 mid-development  | main            |                                                                |
| v1.1.1 | v1.8 branch point     | main            |                                                                |
| v1.1.0 | v1.7 release branch   | release/1.7.x   |                                                                |
| v1.0.4 | v1.2 mid-development  | main            |                                                                |
| v1.0.3 | v1.2 mid-development  | main            |                                                                |
| v1.0.2 | v1.2 mid-development  | main            |                                                                |
| v1.0.1 | v1.2 mid-development  | main            |                                                                |

### Incompatibilities introduced by version, as detected by Go apidiff

Most of these are truly minor, and unlikely to trouble consumers.

#### api/v1.9.1
- OutputPolicyError: old is comparable, new is not

#### api/v1.8.3
- SealStatusResponse: old is comparable, new is not

#### api/v1.8.1
- MountInput.PluginVersion: removed (but it was only added in v1.8.0)

#### api/v1.8.0
- PluginMetadataModeEnv: changed from var to const
- PluginUnwrapTokenEnv: changed from var to const

#### api/v1.6.0
- (*OutputStringError).CurlString: changed from func() string to func() (string, error)
- (*Sys).Monitor: changed from func(context.Context, string) (chan string, error) to func(context.Context, string, string) (chan string, error)
- AutopilotServer.Meta: removed
- TLSConfig: old is comparable, new is not

#### api/v1.1.0
- (*Sys).ConfigureCORS: changed from func(*CORSRequest) (*CORSResponse, error) to func(*CORSRequest) error
- (*Sys).DisableCORS: changed from func() (*CORSResponse, error) to func() error
- CORSRequest.AllowedOrigins: changed from string to []string
- CORSRequest: old is comparable, new is not
- CORSResponse.AllowedOrigins: changed from string to []string
- CORSResponse: old is comparable, new is not

## `github.com/hashicorp/vault/api/auth/*`

In addition to the main `api` module, there are also separate modules
containing the client side code necessary to authenticate with particular auth
methods. Each is a separate module so that applications that do not need them
all, do not need to take a dependency on specific, sometimes large, client
libraries for particular products or cloud providers.

There are 7 of them:
- `approle`
- `aws`
- `azure`
- `gcp`
- `kubernetes`
- `ldap`
- `userpass`

The version numbers of these 7 libraries are mostly but not always managed in
sync with each other.

| api/auth/ libraries                | Version | `api` version | Vault timeline        | Contents                     |
|------------------------------------|---------|---------------|-----------------------|------------------------------|
| *                                  | v0.4.1  | v1.9.2        | v1.14 branch point    | No material changes          |
| *                                  | v0.4.0  | v1.9.0        | v1.13 branch point    | No material changes          |
| *                                  | v0.3.0  | v1.8.0        | v1.12 branch point    | No material changes          |
| *                                  | v0.2.0  | v1.7.2        | v1.12 mid-development | Some fixes                   |
| ldap                               | v0.1.0  | v1.3.1        | v1.10 mid-development | Initial version (2022-02-04) |
| approle                            | v0.1.1  | v1.3.0        | v1.10 mid-development | Some fixes                   |
| azure, gcp                         | v0.1.0  | v1.3.0        | v1.10 mid-development | Initial version (2021-11-12) |
| approle, aws, kubernetes, userpass | v0.1.0  | v1.3.0        | v1.10 mid-development | Initial version (2021-11-09) |

Each `api/auth/` library depends on the base `api` library. The versions are
unimportant, as thanks to how Go modules work, you can combine any `api/auth/`
library with any (compatible) newer version of `api`, without needing the
`api/auth/` library to be re-released. Even so, the `api/auth/` libraries do
get re-released anyway, even just to increment the default version of `api`
that they suggest - those strictly unnecessary releases are the ones noted as
"No material changes" above.

## `github.com/hashicorp/vault/sdk`

The code in the `sdk` module is intended only to be used within Vault plugins.

Unlike the `api` libraries, the `sdk` package deliberately maintains a `v0.x.x`
version number forever, and explicitly documents that it does *NOT* provide API
stability across versions - see
https://github.com/hashicorp/vault/blob/main/sdk/README.md. It frequently
exercises its reserved right to change APIs, though most of the time in small
ways that will not bother most plugin authors.

Generally, a Vault plugin author should just use the latest available SDK
version at the time of writing their plugin, and pull in updates when
available.

| SDK     | Vault timeline        | Branch          | Notes                                                                                  |
|---------|-----------------------|-----------------|----------------------------------------------------------------------------------------|
| v0.9.2  | v1.15 mid-development | main            |                                                                                        |
| v0.9.1  | v1.14 branch point    | main            |                                                                                        |
| v0.9.0  | v1.14 mid-development | main            |                                                                                        |
| v0.8.1  | v1.13 release branch  | release/1.13.x  |                                                                                        |
| v0.8.0  | v1.13 branch point    | main            | `sdk` now depends on `api`, rather than the other way around                           |
| v0.7.0  | v1.13 mid-development | main            |                                                                                        |
| v0.6.2  | v1.13 mid-development | main            |                                                                                        |
| v0.6.1  | v1.13 mid-development | main            |                                                                                        |
| v0.6.0  | v1.12 branch point    | main            | Support for go-plugin AutoMTLS, fixing the dependency on `api_addr` for plugin startup |
| v0.5.3  | v1.12 mid-development | main            |                                                                                        |
| v0.5.2  | v1.11 release branch  | release/1.11.x  | Created from branch divergent to v0.5.1 and v0.5.3 !                                   |
| v0.5.1  | v1.12 mid-development | main            |                                                                                        |
| v0.5.0  | v1.11 branch point    | main            |                                                                                        |
| v0.4.1  | v1.10 branch point    | main            |                                                                                        |
| v0.4.0  | v1.10 mid-development | main (detached) |                                                                                        |
| v0.3.0  | v1.9 branch point     | main            |                                                                                        |
| v0.2.1  | v1.8 branch point     | main            |                                                                                        |
| v0.2.0  | v1.7 release branch   | release/1.7.x   |                                                                                        |
| v0.1.13 | v1.2 mid-development  | main            |                                                                                        |
| v0.1.12 | v1.2 mid-development  | main            |                                                                                        |
| v0.1.11 | v1.2 mid-development  | main            |                                                                                        |
| v0.1.10 | v1.2 mid-development  | main            |                                                                                        |
| v0.1.9  | v1.2 mid-development  | main            |                                                                                        |
| v0.1.8  | v1.2 mid-development  | main            |                                                                                        |

## What about using `github.com/hashicorp/vault` itself as a Go module?

The previously-mentioned documentation at
https://github.com/hashicorp/vault#importing-vault also states:

> Note that this repository also contains Vault (the product), and as with most Go
  projects, Vault uses Go modules to manage its dependencies. The mechanism to do
  that is the [go.mod](https://github.com/hashicorp/vault/blob/main/go.mod) file.
  As it happens, the presence of that file
  also makes it theoretically possible to import Vault as a dependency into other
  projects. Some other projects have made a practice of doing so in order to take
  advantage of testing tooling that was developed for testing Vault itself. This
  is not, and has never been, a supported way to use the Vault project. We aren't
  likely to fix bugs relating to failure to import `github.com/hashicorp/vault`
  into your project.

The reason that people often run into difficulties when doing so, is that the
main Vault repository makes use of `go.mod` `replace` directives to ensure that
Vault itself always uses the libraries at the version present within the Git
repository at the same commit, not their released versions. The actual versions
of these libraries stated in Vault's top-level `go.mod` file do not apply when
building Vault itself - only when making an unsupported dependency reference to
`github.com/hashicorp/vault` - and as a result are not kept technically correct
at all times. It is possible to work around this with some careful selection of
which versions you require, but as stated, **it is an unsupported usage**.

If you really want to do it anyway, then there is **only one way** to
**ensure** you get versions of `github.com/hashicorp/vault` and
`github.com/hashicorp/vault/sdk` that are compatible with each other.

**You must use versions of both of these, from the same Git commit.**

Here is a worked example of doing so, assuming you want to use the code from
the Vault `v1.14.1` Git tag:

```
$ git ls-remote https://github.com/hashicorp/vault v1.14.1
bf23fe8636b04d554c0fa35a756c75c2f59026c0        refs/tags/v1.14.1

$ go get github.com/hashicorp/vault@bf23fe8636b04d554c0fa35a756c75c2f59026c0
... many lines mentioning other dependencies omitted for clarity...
go: added github.com/hashicorp/vault v1.14.1
... many lines mentioning other dependencies omitted for clarity...

$ go get github.com/hashicorp/vault/sdk@bf23fe8636b04d554c0fa35a756c75c2f59026c0
go: upgraded github.com/hashicorp/vault/sdk v0.9.2-0.20230530190758-08ee474850e0 => v0.9.2-0.20230721171514-bf23fe8636b0
```

You can select an sdk tag, such as `sdk/v0.9.2`, rather than a Vault tag, as
the basis for finding the Git commit if you prefer - but either way, you will
be using a pseudo-version (not an actual tag) for at least one of the modules,
as the current release engineering processes never place tags for both Vault
itself, and the sdk, on the same commit.
