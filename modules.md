# Go modules within the Vault repository

There are a number of Go modules within https://github.com/hashicorp/vault.

At https://github.com/hashicorp/vault#importing-vault it states:

> This repository publishes two libraries that may be imported by other projects:
  `github.com/hashicorp/vault/api` and `github.com/hashicorp/vault/sdk`.

No official changelog is maintained for these libraries, despite them having
their own version numbers, distinct from Vault itself. The main purpose of this
document is to record what I have deduced from Git history, so that it may be
helpful to others, and I don't have to re-deduce it in the future.

The above-linked documentation also states:

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
which versions you require, but as stated, it is an unsupported usage.

## `github.com/hashicorp/vault/api`

The `api` package mostly provides Go client code for calling the Vault HTTP API.

It does also contain some code which is only used when writing Vault plugins,
which really ought to be in the `sdk` module - this is in `plugin_helpers.go`.

It claims to provide a stable API and for the most part succeeds.

### api/v1.9.2

Corresponds to Vault 1.14.0

### api/v1.9.1

Corresponds to a point during Vault 1.14 development

Very minor incompatible change:
- OutputPolicyError: old is comparable, new is not

### api/v1.9.0

Corresponds to very shortly before Vault 1.13.0 (the only missed change is
a version increase of several `golang.org/x/...` libraries, so not really
consequential)

This is the first version of the `api` to not depend on the `sdk`, resulting
a substantial reduction in unwarranted dependencies.

### api/v1.8.3

Corresponds to a point during Vault 1.13 development

Very minor incompatible change:
- OutputPolicyError: old is comparable, new is not

### api/v1.8.2

Corresponds to a point during Vault 1.13 development

### api/v1.8.1

Corresponds to a point during Vault 1.13 development

Incompatible change:
- MountInput.PluginVersion: removed (but it was only added in v1.8.0)

### api/v1.8.0

Corresponds to shortly before Vault 1.12.0 (missed changes relate to new plugin
versioning support)

Extremely minor incompatible change:
- PluginMetadataModeEnv: changed from var to const
- PluginUnwrapTokenEnv: changed from var to const

### api/v1.7.2, api/v1.7.1, api/v1.7.0

Correspond to points during Vault 1.12 development

### api/v1.6.0

Corresponds to a point late in Vault 1.11 development

Mostly minor incompatible changes:
- (*OutputStringError).CurlString: changed from func() string to func() (string, error)
- (*Sys).Monitor: changed from func(context.Context, string) (chan string, error) to func(context.Context, string, string) (chan string, error)
- AutopilotServer.Meta: removed
- TLSConfig: old is comparable, new is not

### api/v1.5.0

Corresponds to a point during Vault 1.11 development

### api/v1.4.1

Corresponds to Vault 1.10.0

### api/v1.4.0, api/v1.3.1

Correspond to points during Vault 1.10 development

### api/v1.3.0

Corresponds to Vault 1.9.0

### api/v1.2.0

Corresponds to a point during Vault 1.9 development

### api/v1.1.1

Corresponds to very shortly before Vault 1.8.0 (only missed change is a `go mod
tidy`)

### api/v1.1.0

Corresponds to Vault 1.7.0

Unusually, this `api` release was made from the `release/1.7.x` branch, instead
of the `main` branch.

Incompatible changes:
- (*Sys).ConfigureCORS: changed from func(*CORSRequest) (*CORSResponse, error) to func(*CORSRequest) error
- (*Sys).DisableCORS: changed from func() (*CORSResponse, error) to func() error
- CORSRequest.AllowedOrigins: changed from string to []string
- CORSRequest: old is comparable, new is not
- CORSResponse.AllowedOrigins: changed from string to []string
- CORSResponse: old is comparable, new is not

### api/v1.0.4

Corresponds to very shortly before Vault 1.2.0 (only missed change is an
increment to `sdk` version depended upon)

### api/v1.0.3, api/v1.0.2, api/v1.0.1

Earlier releases during the Vault 1.2 development cycle.

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

To give some context to the version numbers, the point in the Vault development
timeline at which each `api/auth/` release was made, is also included. These
are described as:

* "branch point" - the `api/auth/` release was made as part of a cluster of
  version number incrementing, just prior to creating the release branch for
  a new release series of Vault itself.

* "mid-development" - the `api/auth/` release was made for its own reasons,
  outside the cadence of releases of Vault itself.

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

A new SDK version is usually tagged *shortly before* the creation of a new
Vault release branch. This means the tagged SDK will contain code similar to,
but a bit older than, the embedded SDK code in the subsequent Vault v1.x.0
release. These SDK releases are identified with a Vault version in the table
below.

SDK versions may also be released intermittently between Vault releases, if it
seems useful to do so, or rarely, from Vault release branches.

The notation 'main (detached)' means a release tag that is on no branch, or
a feature branch, containing just one small change - generally a version
increment - off the main branch.

| SDK     | Vault | Branch          | Notes                                                                                  |
|---------|-------|-----------------|----------------------------------------------------------------------------------------|
| v0.9.1  | v1.14 | main            |                                                                                        |
| v0.9.0  |       | main            |                                                                                        |
| v0.8.1  |       | release/1.13.x  |                                                                                        |
| v0.8.0  | v1.13 | main            | `sdk` now depends on `api`, rather than the other way around                           |
| v0.7.0  |       | main            |                                                                                        |
| v0.6.2  |       | main            |                                                                                        |
| v0.6.1  |       | main            |                                                                                        |
| v0.6.0  | v1.12 | main            | Support for go-plugin AutoMTLS, fixing the dependency on `api_addr` for plugin startup |
| v0.5.3  |       | main            |                                                                                        |
| v0.5.2  |       | release/1.11.x  | Created from branch divergent to v0.5.1 and v0.5.3 !                                   |
| v0.5.1  |       | main            |                                                                                        |
| v0.5.0  | v1.11 | main            |                                                                                        |
| v0.4.1  | v1.10 | main            |                                                                                        |
| v0.4.0  |       | main (detached) |                                                                                        |
| v0.3.0  | v1.9  | main            |                                                                                        |
| v0.2.1  | v1.8  | main            |                                                                                        |
| v0.2.0  |       | release/1.7.x   |                                                                                        |
| v0.1.13 | v1.2  | main            |                                                                                        |
| v0.1.12 |       | main            |                                                                                        |
| v0.1.11 |       | main            |                                                                                        |
| v0.1.10 |       | main            |                                                                                        |
| v0.1.9  |       | main            |                                                                                        |
| v0.1.8  |       | main            |                                                                                        |
