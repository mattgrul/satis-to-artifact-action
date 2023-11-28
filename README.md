# Composer Satis Repository Generator

| Version | Latest Commit                                                                                                                                                                                                                               | Nightly Build                                                                                                                                                                                                                                             |
|---------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `main`  | [![Latest Test](https://github.com/mattgrul/satis-to-artifact-action/actions/workflows/event-test-action.yml/badge.svg?branch=main)](https://github.com/mattgrul/satis-to-artifact-action/actions/workflows/event-test-action.yml)          | [![Nightly Main Test](https://github.com/mattgrul/satis-to-artifact-action/actions/workflows/nightly-main-test-action.yml/badge.svg?branch=main)](https://github.com/mattgrul/satis-to-artifact-action/actions/workflows/nightly-main-test-action.yml)    |
| `v1`    | [![Latest Test](https://github.com/mattgrul/satis-to-artifact-action/actions/workflows/event-test-action.yml/badge.svg?branch=releases%2Fv1)](https://github.com/mattgrul/satis-to-artifact-action/actions/workflows/event-test-action.yml) | [![Nightly v1 Test](https://github.com/mattgrul/satis-to-artifact-action/actions/workflows/nightly-v1-test-action.yml/badge.svg?branch=releases%2Fv1)](https://github.com/mattgrul/satis-to-artifact-action/actions/workflows/nightly-v1-test-action.yml) |

Build a Composer Satis repository and output the final build to a GitHub artifact.

### Private Repositories

For private GitHub repositories hosted on the same user account or organization the default `github` context is used
within the action like
so `${{ github.token }}`. This sets the composer `github-oauth` token within the runner and allows private repositories
to be added to the Satis config file.

## Usage

You can use the minimal example below within your own workflow to get up and running.

```yaml
jobs:
  build-satis-repo:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - id: generate-satis-repo
        name: 'Run Generate Satis Repository Action'
        uses: masheto-org/satis-to-artifact-action@v1
        env:
          SATIS_PATH: ${{ github.workspace }}/satis
```

For a more configurable use case of using the action with all the available inputs see the example below.

```yaml
jobs:
  build-satis-repo:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - id: generate-satis-repo
        name: 'Run Generate Satis Repository Action'
        uses: masheto-org/satis-to-artifact-action@v1
        with:
          composer-version: 2.2 # Composer version 2.2
          php-version: 8.1 # PHP version 8.1
          satis-version: fafc1c2eca6394235f12f8a3ee4da7fc7c9fc874 # Satis from the commit SHA fafc1c2eca6394235f12f8a3ee4da7fc7c9fc874
          satis-config: build # The Satis config file from the `build` directory
          cache-composer: false # Disable caching of the Composer cache directory
          artifact-name: my-composer-repo #Output the final build to an artifact named `my-composer-repo`
          retention-days: 30 # Keep the artifact for 30 days
        env:
          SATIS_PATH: ${{ github.workspace }}/satis
```

## Inputs

There are 7 input values that can be configured for the action which are as follows:

| Name                                        | Required | Default                  | Description                                   |
|---------------------------------------------|----------|--------------------------|-----------------------------------------------|
| [composer-version](#php--composer-versions) | No       | 2.6                      | The Composer version to use                   |
| [php-version](#php--composer-versions)      | No       | 8.3                      | The PHP version to use                        |
| [satis-version](#satis-version)             | No       | main                     | The version of Satis to use                   |
| [satis-config](#satis-config)               | No       | `${{github.workspace}}`  | The path to the Satis config file             |
| [cache-composer](#cache-composer)           | No       | true                     | Whether or not to cache the composer files    |
| [artifact-name](#artifact-name)             | No       | modules-repository-build | The name of the final artifact                |
| [retention-days](#retention-days)           | No       | 90                       | The duration to keep the final build artifact |

### PHP & Composer Versions

This action uses `shivammathur/setup-php` to control PHP and Composer versions.

For supported versions of PHP please see
the [Setup PHP in GitHub Actions](https://github.com/shivammathur/setup-php#tada-php-support).

The latest stable version of composer is set up by default. You can set up the required composer version by specifying
the major version v1 or v2, or the version in major.minor or semver format. Additionally, for composer snapshot a
preview can also be specified to set up the respective releases.

### Cache Composer

Caching of the Composer cache directory is enabled by default, which speeds up the action by reusing the Composer files
between runs. This behavior can be toggled with the cache-composer input.

### Artifact Name

The name of the final artifact is suffixed with `${{ github.run_id }}` to ensure each workflow keeps its original copy
of the repository build. For example, modules-repository-build-1234567890.

### Retention Days

By default, GitHub will keep artifacts for 90 days. This can be changed by setting the `retention-days` input value to
your specified time e.g. `30` for 30 days.

### Satis Version

The latest stable version of Satis is used by default using the branch `main`.

If you need a specific version of Satis to be compatible with an older version of Composer for example, you can set up
the required Satis version by specifying the commit SHA from the Satis repository using the `satis-version` input value.

```yaml
with:
  satis-version: e4982dc3037fdc14402c3e080556a172d276463a
```

### Satis Config

An example Satis config file can be found below. The default location for the config file is `satis.json` in the root of
the repository.

```json
{
  "name": "My Repository",
  "homepage": "http://packages.example.org",
  "repositories": [
    {
      "type": "vcs",
      "url": "https://github.com/mycompany/privaterepo"
    }
  ],
  "require-all": true
}
```

The location of this config file can be changed by adding the `satis-config` input value relative to the root, for
example if you
placed the config file in a `build` directory within the repository root you can use `satis-config: build` as an input.

```yaml
with:
  satis-config: build
```

## Outputs

There is 1 output for the action.

| Name                              | Description                               |
|-----------------------------------|-------------------------------------------|
| [satis-artifact](#satis-artifact) | The name of the Satis Repository artifact |

If the `upload-artifact` step does not find a file to upload, an error is returned and the action will fail without
setting the output. This allows you to use the `${{ success() }}` expression on subsequent steps or the `needs`
condition on subsequent jobs.

### Satis Artifact

The `satis-artifact` output can be useful if you want to deploy the artifact to a remote server for example.

For example if you had the below `generate-satis-repo` step in your workflow you could use the `satis-artifact` output
to download the artifact in a subsequent step like so.

```yaml
- id: generate-satis-repo
  name: 'Run Generate Satis Repository Action'
  uses: masheto-org/satis-to-artifact-action@v1
  env:
    SATIS_PATH: ${{ github.workspace }}/satis

- id: download-artifact
  if: ${{ success() }}
  uses: actions/download-artifact@v3
  with:
    name: ${{ steps.generate-satis-repo.outputs.satis-artifact }}
```

## Env

There is one environment variable that can be configured for the action which is the location of the Satis install on
the job's runner:

```yaml
env:
  SATIS_PATH: ${{ github.workspace }}/satis
```

## Releases

This action follows [Semantic Versioning](https://semver.org/) for releases and will be associated with a matching tag.

It is recommended to use dependabot with semantic versioning to keep the actions in your workflows up to date.

### Stable

Depending on your preference for receiving updates, you can use any of the following tags. Note that major and minor
version tags, such as v1 and v1.1, are rolling tags and synced with the latest minor and patch releases.

- `masheto-org/satis-to-artifact-action@v1` - Suitable for receiving all updates in the `v1` range (up to `v1.x.x`).
- `masheto-org/satis-to-artifact-action@v1.0` - Suitable for receiving all updates in the `v1.0.x` range.
- `masheto-org/satis-to-artifact-action@v1.1.1` - Pins to the specific v1.1.1 patch version.

If you would like to pin to a specific commit for compatibility reasons you can use a specific commit SHA which are
immutable.

- `masheto-org/satis-to-artifact-action@e4982dc3037fdc14402c3e080556a172d276463a` - Will use a specific commit version.

### Latest

Although it is not guaranteed to be stable and should be used with caution, you can get the latest version by using the
branch `main` such as `masheto-org/satis-to-artifact-action@main`.

## Dependencies

This action depends on the following actions:

- [shivammathur/setup-php@v2](https://github.com/shivammathur/setup-php) - This action is used to set up PHP and
  Composer versions.
- [actions/cache@v3](https://github.com/actions/cache) - This action is used to cache the Satis install directory
  between runs.

## Contributing

All contributions are welcome! Please see the [Contributing Guide](.github/CONTRIBUTING.md) for more details.
