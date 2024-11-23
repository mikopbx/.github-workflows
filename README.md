# MikoPBX GitHub Workflows

This repository contains reusable GitHub Actions workflows for MikoPBX module development. It provides standardized processes for building, testing, and publishing MikoPBX modules.

## Features

- Automated versioning (X.YY format)
- GitHub Release creation
- Optional publishing to files.miko.ru and releases.mikopbx.com
- Docker-based build environment
- Composer dependencies handling
- Changelog generation

## Workflow Inputs

The extension-publish workflow accepts the following inputs:

### Required Inputs

| Input | Description | Type | Default |
|-------|-------------|------|---------|
| `initial_version` | Initial version for the module (e.g., "1.70") | string | - |

### Optional Inputs

| Input | Description | Type | Default |
|-------|-------------|------|---------|
| `custom_build_steps` | Custom build steps to execute during build process, including Docker builds | string | '' |


## Usage

### Basic Usage

For modules that don't require custom build steps, use this basic configuration:


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

### Custom Build Steps

For modules requiring custom compilation or build steps, use the `custom_build_steps` parameter. This allows you to:
- Use Docker containers for isolated builds
- Compile binary components
- Run specific build tools
- Execute custom build scripts

#### Example: Simple Custom Build

```yaml
jobs:
  build:
    uses: mikopbx/.github-workflows/.github/workflows/extension-publish.yml@master
    with:
      initial_version: "1.70"
      custom_build_steps: |
      cd $GITHUB_WORKSPACE/module
      make build
      chmod +x bin/
```


#### Example: Docker-based Build

```yaml
jobs:
  build:
  uses: mikopbx/.github-workflows/.github/workflows/extension-publish.yml@master
  with:
    initial_version: "1.70"
    custom_build_steps: |
    cd $GITHUB_WORKSPACE/module
    # Build custom Docker image
    docker build -t custom-builder -f Dockerfile.build .
    # Run compilation
    docker run --rm \
    -v $GITHUB_WORKSPACE/module:/build \
    -w /build \
    custom-builder \
    bash -c "make && make install"
```

#### Example: Multiple Build Environments

```yaml
jobs:
  build:
  uses: mikopbx/.github-workflows/.github/workflows/extension-publish.yml@master
  with:
    initial_version: "1.70"
    custom_build_steps: |
    cd $GITHUB_WORKSPACE/module
    # C++ compilation
    docker run --rm \
    -v $GITHUB_WORKSPACE/module:/build \
    -w /build \
    gcc:latest \
    bash -c "g++ -o bin/app src/main.cpp"
    # Go compilation
    docker run --rm \
    -v $GITHUB_WORKSPACE/module:/build \
    -w /build \
    golang:1.21 \
    bash -c "cd cmd && go build -o ../bin/tool"
```

### 2. Module Configuration

Create or update `module.json` in your module's src directory:

```json
{
  "name": "Your Module Name",
  "version": "1.50",
  "min_pbx_version": "2024.1.22",
  "release_settings": {
    "publish_release": true,   // Enable/disable publishing to files.miko.ru and releases.mikopbx.com
    "changelog_enabled": true, // Enable/disable changelog generation
    "create_github_release": true  // Enable/disable GitHub release creation
  }
}
```

### Custom Build Requirements

When using custom build steps:

1. **File Structure**:
   - Place build-related files (Dockerfile.build, Makefile, etc.) in your module repository
   - Ensure compiled artifacts are placed in the correct location for packaging

2. **Docker Considerations**:
   - Use `$GITHUB_WORKSPACE/module` as the mount point for your module code
   - Clean up containers and images after use
   - Consider using Docker build cache for faster builds

3. **Environment Variables**:
   - Access to all GitHub Actions environment variables
   - Can pass additional variables to Docker containers

4. **Best Practices**:
   - Keep Dockerfiles in your module repository
   - Document build requirements clearly
   - Test custom build steps locally before committing

### Workflow Steps

The workflow executes in this order:
1. Repository checkout
2. Module validation
3. Version management
4. Environment setup
5. Dependency handling
6. Custom build steps (if specified)
7. Package creation
8. Publishing and release

### Set up the following secrets in your GitHub organization settings:

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


## Support

For issues or questions about:
- Workflow configuration: Open an issue in this repository
- Module-specific build problems: Open an issue in the module's repository

## Contributing

1. Create issues for bugs and feature requests
2. Submit pull requests with improvements
3. Follow existing code style and conventions

## License

This project is licensed under the MIT License - see the LICENSE file for details.
