# MikoPBX GitHub Actions

This directory contains composite actions used in MikoPBX module workflows.

## Actions Overview

### checkout
Handles repository checkouts for both module and workflow repositories.

### validate
Validates module configuration and metadata, including:
- Required fields in module.json
- PHP version requirements
- PBX version compatibility
- Module settings

### versioning
Manages module versioning:
- Retrieves latest release version
- Calculates new version
- Updates module.json version

### composer
Manages PHP dependencies:
- Removes mikopbx/core dependency
- Installs required packages
- Supports both PHP 7.4 and 8.3

### archive
Creates module distribution archive:
- Excludes development files
- Creates versioned ZIP archive
- Validates archive creation

### changelog
Generates changelog from git commits:
- Collects commits since last release
- Formats changelog content
- Supports both development and production releases

### release
Manages GitHub releases:
- Creates production releases (master branch)
- Creates development releases (develop branch)
- Cleanup old development releases (keeps last 5)

### publish
Handles module publication:
- Creates directory structure on files.miko.ru
- Uploads module archive
- Generates and submits release information
- Publishes to releases.mikopbx.com

## Usage

Each action can be used independently or as part of the main workflow. See individual action documentation for specific inputs and outputs.

## Development

When adding new actions:
1. Create a new directory under `.github/actions/`
2. Include `action.yml` with action definition
3. Add comprehensive documentation
4. Include input/output validation
5. Add error handling
6. Update this README

## Testing

Test new actions by referencing them in a test workflow:

```yaml
steps:
  - uses: ./.github/actions/your-new-action
    with:
      required-input: value
```
