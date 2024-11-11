# MikoPBX GitHub Workflows

This repository contains reusable GitHub Actions workflows for MikoPBX module development. It provides standardized processes for building, testing, and publishing MikoPBX modules.

## Features

- Automated versioning (X.YY format)
- GitHub Release creation
- Optional publishing to files.miko.ru and releases.mikopbx.com
- Docker-based build environment
- Composer dependencies handling
- Changelog generation

## Usage

### 1. Basic Setup

Create `.github/workflows/build.yml` in your module repository:

```yaml
name: Build and Publish

on:
  push:
    branches:
      - master
  workflow_dispatch:

jobs:
  build:
    uses: mikopbx/.github-workflows/.github/workflows/extension-publish.yml@master
    with:
      initial_version: "1.50"  # Set your module's initial version
    secrets:
      OWNCLOUD_AUTH: ${{ secrets.OWNCLOUD_AUTH }}
      MIKO_LIC_REST_VENDOR_ID: ${{ secrets.MIKO_LIC_REST_VENDOR_ID }}
      MIKO_LIC_REST_API_KEY: ${{ secrets.MIKO_LIC_REST_API_KEY }}
      MIKO_LIC_HOSTNAME: ${{ secrets.MIKO_LIC_HOSTNAME }}
      WEBDAV_ROOT: ${{ secrets.WEBDAV_ROOT }}
      SHARE_API_URL: ${{ secrets.SHARE_API_URL }}
```

### 2. Module Configuration

Create or update `module.json` in your module's src directory:

```json
{
  "name": "Your Module Name",
  "version": "1.50",
  "min_pbx_version": "2024.1.22",
  "build_settings": {
    "use_composer": false,     // Enable/disable Composer dependencies installation
  },
  "release_settings": {
    "publish_release": true,   // Enable/disable publishing to files.miko.ru and releases.mikopbx.com
    "changelog_enabled": true, // Enable/disable changelog generation
    "create_github_release": true  // Enable/disable GitHub release creation
  }
}
```

### 3. Organization Secrets

Set up the following secrets in your GitHub organization settings:

- `OWNCLOUD_AUTH`: Authentication for files.miko.ru
- `MIKO_LIC_REST_VENDOR_ID`: Vendor ID for releases.mikopbx.com
- `MIKO_LIC_REST_API_KEY`: API key for releases.mikopbx.com
- `MIKO_LIC_HOSTNAME`: MikoPBX release server hostname
- `WEBDAV_ROOT`: WebDAV root URL for files.miko.ru
- `SHARE_API_URL`: Share API URL for files.miko.ru

## How It Works

1. **Versioning**:
   - Uses X.YY format (e.g., 1.50, 1.51, 1.52)
   - Automatically increments minor version
   - Initial version is set in workflow file

2. **Build Process**:
   - Uses Docker container for consistent build environment
   - Handles Composer dependencies if enabled in module.json
   - Updates version in module.json

3. **Release Process**:
   - Creates GitHub release (if enabled)
   - If `publish_release` is true:
     - Uploads to files.miko.ru
     - Publishes to releases.mikopbx.com
   - Generates changelog (if enabled)

## Configuration Options

### Module-level Configuration (module.json)

```json
{
  "build_settings": {
    "use_composer": false       // Whether to run composer install during build
  },
  "release_settings": {
    "publish_release": true,    // Enable/disable publishing to MikoPBX platforms
    "changelog_enabled": true,  // Enable/disable changelog generation
    "create_github_release": true  // Enable/disable GitHub release creation
  }
}
```

## Development

### Adding New Features

1. Fork the repository
2. Create a feature branch
3. Submit a pull request

### Testing

Test your changes by referencing your branch in the module's workflow:

```yaml
jobs:
  build:
    uses: mikopbx/.github-workflows/.github/workflows/extension-publish.yml@your-branch
```

## Troubleshooting

Common issues and solutions:

1. **Version not incrementing**:
   - Check initial_version in workflow file
   - Verify previous releases on GitHub

2. **Upload failures**:
   - Verify organization secrets
   - Check module.json settings
   - Review action logs for specific errors

3. **Composer issues**:
   - Verify `build_settings.use_composer` is set correctly in module.json
   - Check composer.json is valid
   - Review composer install logs in action output

## Contributing

1. Create issues for bugs and feature requests
2. Submit pull requests with improvements
3. Follow existing code style and conventions

## License

This project is licensed under the MIT License - see the LICENSE file for details.
