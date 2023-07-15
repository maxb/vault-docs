# Go modules within the Vault repository

There are a number of Go modules within https://github.com/hashicorp/vault.

At https://github.com/hashicorp/vault#importing-vault it states:

> This repository publishes two libraries that may be imported by other projects:
  `github.com/hashicorp/vault/api` and `github.com/hashicorp/vault/sdk`.

No official changelog is maintained for these libraries, despite them having their own version numbers, distinct from Vault itself. The main purpose of this document is to record what I have deduced from Git history, so that it may be helpful to others, and I don't have to re-deduce it in the future.

The above-linked documentation also states:

> Note that this repository also contains Vault (the product), and as with most Go
  projects, Vault uses Go modules to manage its dependencies. The mechanism to do
  that is the [go.mod](https://github.com/hashicorp/vault/blob/main/go.mod) file. As it happens, the presence of that file
  also makes it theoretically possible to import Vault as a dependency into other
  projects. Some other projects have made a practice of doing so in order to take
  advantage of testing tooling that was developed for testing Vault itself. This
  is not, and has never been, a supported way to use the Vault project. We aren't 
  likely to fix bugs relating to failure to import `github.com/hashicorp/vault` 
  into your project.

The reason that people often run into difficulties when doing so, is that the main Vault repository makes use of `go.mod` `replace` directives to ensure that Vault itself always uses the libraries at the version present within the Git repository at the same commit, not their released versions. The actual versions of these libraries stated in Vault's top-level `go.mod` file do not apply when building Vault itself - only when making an unsupported dependency reference to `github.com/hashicorp/vault` - and as a result are not kept technically correct at all times. It is possible to work around this with some careful selection of which versions you require, but as stated, it is an unsupported usage.

## `github.com/hashicorp/vault/api`

The `api` package mostly provides Go client code for calling the Vault HTTP API.

It does also contain some code which is only used when writing Vault plugins, which really ought to be in the `sdk` module.

It claims to provide a stable API and for the most part succeeds.

### api/v1.9.2

* Corresponds to Vault 1.14.0

### api/v1.9.1

* Corresponds to a point during Vault 1.14 development

* Very minor incompatible change:
  - OutputPolicyError: old is comparable, new is not

### api/v1.9.0

* Corresponds to very shortly before Vault 1.13.0 (the only missed change is a version increase of several `golang.org/x/...` libraries, so not really consequential)

* This is the first version of the `api` to not depend on the `sdk`, resulting a substantial reduction in unwarranted dependencies.

### api/v1.8.3

* Corresponds to a point during Vault 1.13 development

* Very minor incompatible change:
  - OutputPolicyError: old is comparable, new is not

### api/v1.8.2

* Corresponds to a point during Vault 1.13 development

### api/v1.8.1

* Corresponds to a point during Vault 1.13 development

* Incompatible change:
  - MountInput.PluginVersion: removed (but it was only added in v1.8.0)

### api/v1.8.0

* Corresponds to shortly before Vault 1.12.0 (missed changes relate to new plugin versioning support)

* Extremely minor incompatible change:
  - PluginMetadataModeEnv: changed from var to const
  - PluginUnwrapTokenEnv: changed from var to const

### api/v1.7.2, api/v1.7.1, api/v1.7.0

* Correspond to points during Vault 1.12 development

### api/v1.6.0

* Corresponds to a point late in Vault 1.11 development

* Mostly minor incompatible changes:
  - (*OutputStringError).CurlString: changed from func() string to func() (string, error)
  - (*Sys).Monitor: changed from func(context.Context, string) (chan string, error) to func(context.Context, string, string) (chan string, error)
  - AutopilotServer.Meta: removed
  - TLSConfig: old is comparable, new is not

### api/v1.5.0

* Corresponds to a point during Vault 1.11 development

### api/v1.4.1

* Corresponds to Vault 1.10.0

### api/v1.4.0, api/v1.3.1

* Correspond to points during Vault 1.10 development

### api/v1.3.0

* Corresponds to Vault 1.9.0

### api/v1.2.0

* Corresponds to a point during Vault 1.9 development

### api/v1.1.1

* Corresponds to very shortly before Vault 1.8.0 (only missed change is a `go mod tidy`)

### api/v1.1.0

* Corresponds to Vault 1.7.0

* Unusually, this `api` release was made from the `release/1.7.x` branch, instead of the `main` branch.

* Incompatible changes:
  - (*Sys).ConfigureCORS: changed from func(*CORSRequest) (*CORSResponse, error) to func(*CORSRequest) error
  - (*Sys).DisableCORS: changed from func() (*CORSResponse, error) to func() error
  - CORSRequest.AllowedOrigins: changed from string to []string
  - CORSRequest: old is comparable, new is not
  - CORSResponse.AllowedOrigins: changed from string to []string
  - CORSResponse: old is comparable, new is not

### api/v1.0.4

* Corresponds to very shortly before Vault 1.2.0 (only missed change is an increment to `sdk` version depended upon)

### api/v1.0.3, api/v1.0.2, api/v1.0.1

* Earlier releases during the Vault 1.2 development cycle.
