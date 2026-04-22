# Install Quarkus Snapshots Action

A GitHub Action that downloads and installs Quarkus Maven snapshot artifacts from the latest release into your local Maven repository (`~/.m2/repository`).

## How it works

This action:

1. Searches for the latest snapshot release matching the specified branch in the configured repository.
2. Downloads the `maven-repo.tar.gz` artifact from the release.
3. Extracts it into `~/.m2/repository`.

If no usable snapshot is available, the action automatically falls back to cloning and building Quarkus from source for the requested branch. Set `fail-on-missing: true` to fail the build instead.

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

### Fail instead of building from source

```yaml
steps:
  - uses: quarkusio/install-quarkus-snapshots-action@main
    with:
      fail-on-missing: true
```

## Inputs

| Input | Description | Required | Default |
|---|---|---|---|
| `branch` | The Quarkus branch to install snapshots for | Yes | `main` |
| `repository` | The repository containing the Maven snapshot releases | Yes | `quarkusio/quarkus-ecosystem-ci` |
| `fail-on-missing` | Fail the build instead of building Quarkus from source when no snapshot is available | No | `false` |

## Outputs

| Output | Description |
|---|---|
| `quarkus-version` | The Quarkus version that was installed |
| `quarkus-sha` | The Quarkus commit SHA |
| `built-from-source` | `true` if Quarkus was built from source, `false` if installed from a snapshot |

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

## Cleanup

To remove the snapshot artifacts after your build, use the cleanup action with `if: always()` to ensure it runs even if the build fails:

```yaml
steps:
  - uses: quarkusio/install-quarkus-snapshots-action@main
  - run: mvn verify
  - uses: quarkusio/install-quarkus-snapshots-action/cleanup@main
    if: always()
```

> [!NOTE]
> The cleanup action only removes artifacts installed from snapshot releases. Artifacts built from source are installed via Maven and are not tracked for cleanup.

## Requirements

- The `gh` CLI must be available in the runner (included by default in GitHub-hosted runners).
- The `GITHUB_TOKEN` must have read access to the releases in the target repository. The action uses `github.token` automatically.
- When building from source, `mvn` and a JDK must be available on the runner.

## License

Apache License 2.0 - see [LICENSE](LICENSE) for details.
