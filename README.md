# Install Quarkus Snapshots Action

A GitHub Action that downloads and installs Quarkus Maven snapshot artifacts from the latest release into your local Maven repository (`~/.m2/repository`).

## How it works

This action:

1. Searches for the latest snapshot release matching the specified branch in the configured repository.
2. Validates that the release is not older than 36 hours (to ensure freshness).
3. Downloads the `maven-repo.tar.gz` artifact from the release.
4. Extracts it into `~/.m2/repository`.

## Usage

```yaml
steps:
  - uses: quarkusio/install-quarkus-snapshots-action@main
```

### With custom parameters

```yaml
steps:
  - uses: quarkusio/install-quarkus-snapshots-action@main
    with:
      branch: 3.17
      repository: quarkusio/quarkus-ecosystem-ci
```

## Inputs

| Input | Description | Required | Default |
|---|---|---|---|
| `branch` | The Quarkus branch to install snapshots for | Yes | `main` |
| `repository` | The repository containing the Maven snapshot releases | Yes | `quarkusio/quarkus-ecosystem-ci` |

## Permissions

This action requires the following permissions on the calling workflow:

| Permission | Access | Reason |
|---|---|---|
| `contents` | `read` | Required to list, view, and download releases via the `gh` CLI |

Example workflow with explicit permissions:

```yaml
permissions:
  contents: read

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: quarkusio/install-quarkus-snapshots-action@main
```

## Requirements

- The `gh` CLI must be available in the runner (included by default in GitHub-hosted runners).
- The `GITHUB_TOKEN` must have read access to the releases in the target repository. The action uses `github.token` automatically.

## License

Apache License 2.0 - see [LICENSE](LICENSE) for details.
