# Public Shared Repository

This repository contains shared GitHub Actions and other resources.

## Release Management with Release-Please

This repository uses [Release-Please](https://github.com/googleapis/release-please) to automate versioning and changelog generation for the actions contained within it.

### How Release-Please Works with Example-Action

The `example-action` in this repository is managed through Release-Please, which automates the release process including versioning and changelog generation.

#### Configuration

The release process is configured through the `release-please-config.json` file:

```json
{
  "separate-pull-requests": true,
  "always-update": true,
  "include-component-in-tag": true,
  "date-format": "%Y-%m-%d",
  "release-type": "simple",
  "commit-search-depth": 20,
  "tag-separator": "-",
  "packages": {
    ".github/actions/example-action": {
      "package-name": "example-action",
      "release-type": "simple"
    }
  }
}
```

Key configuration aspects:
- `separate-pull-requests`: Creates individual PRs for each package/action.
- `include-component-in-tag`: Makes tags include the component name (e.g., `example-action-v1.2.3`).
- `tag-separator`: Sets the separator between component name and version to be `-`.
- The `example-action` is defined as a package with the "simple" release type.

#### Tag Creation Process

When changes are pushed to the `main` branch that affect the example-action, the following process occurs:

1. The GitHub workflow `.github/workflows/release-action.yaml` is triggered.
2. Release-Please scans commit messages to determine version changes.
3. It follows the [Conventional Commits](https://www.conventionalcommits.org/) specification:
   - `fix:` commits trigger PATCH version bumps (e.g., 1.0.0 → 1.0.1)
   - `feat:` commits trigger MINOR version bumps (e.g., 1.0.0 → 1.1.0)
   - `feat!:` or commits with `BREAKING CHANGE:` in the footer trigger MAJOR version bumps (e.g., 1.0.0 → 2.0.0)
4. A Pull Request is created with version updates and CHANGELOG modifications.
5. When the PR is merged, Release-Please automatically:
   - Creates a new Git tag with the format `example-action-vX.Y.Z`
   - Generates a GitHub release with release notes from the CHANGELOG

#### Making Changes to Example-Action

To trigger a new release and tag for the example-action:

1. Make your changes to files in the `.github/actions/example-action/` directory.
2. Commit your changes using the Conventional Commits format:
   ```
   feat: add new capability to example-action
   
   This adds XYZ capability to the action.
   ```
3. Push your changes to the `main` branch.
4. The Release-Please workflow will automatically create a release PR.
5. Merge the PR to create a new tag and release.

#### Benefits

- **Automated Versioning**: No need to manually update version numbers.
- **Consistent Changelog**: Automatically generated from commit messages.
- **Conventional Commits**: Provides a clear history and semantic versioning.
- **Specialized Tags**: Tags include the component name, making it easy to use specific versions in workflows.

### Referencing Tagged Versions

To use a specific version of the example-action in a workflow:

```yaml
steps:
  - name: Use Example Action
    uses: your-org/public-shared/.github/actions/example-action@example-action-v1.0.0
```

Or to always use the latest version:

```yaml
steps:
  - name: Use Example Action
    uses: your-org/public-shared/.github/actions/example-action@main
```