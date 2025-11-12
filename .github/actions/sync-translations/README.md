# Sync Translations Action

This GitHub Action synchronizes translation files from the MikoPBX Core repository to Language Pack modules during the build process.

## Purpose

Language Pack modules need to include the latest translations from Weblate, which are committed to the `mikopbx/Core` repository. This action automates the synchronization process, ensuring that Language Pack releases always contain the most up-to-date translations without manual copying.

## Configuration

Add a `translation_sync` section to your module's `module.json`:

```json
{
  "translation_sync": {
    "enabled": true,
    "source_repo": "mikopbx/Core",
    "source_branch": "develop",
    "language_code": "ja",
    "exclude_files": ["ModuleJapaneseLanguagePack.php"]
  }
}
```

### Configuration Options

- **enabled** (boolean, required): Set to `true` to enable translation sync, `false` to disable
- **source_repo** (string, required): Source repository in format "owner/repo" (e.g., "mikopbx/Core")
- **source_branch** (string, required): Branch to sync from (e.g., "develop", "master")
- **language_code** (string, required): Language code used in the source repository (e.g., "ja", "ru", "de")
- **exclude_files** (array, optional): List of files to preserve in the target directory (e.g., module-specific translation files)

## How It Works

1. Reads `module.json` and checks if `translation_sync.enabled` is `true`
2. If disabled, skips the sync process
3. If enabled, extracts sync configuration from `module.json`
4. Checks out the source repository at the specified branch
5. Copies all PHP files from `src/Common/Messages/{language_code}/` to module's `Messages/{language_code}/`
6. Preserves files specified in `exclude_files` array
7. Logs the sync results and any updated files

## Integration

This action is automatically called in the `extension-publish.yml` workflow before creating the module package:

```yaml
- name: Sync translations from Core
  uses: ./.github-workflows/.github/actions/sync-translations
```

## Example Output

```
Translation sync settings:
  Source repository: mikopbx/Core
  Source branch: develop
  Language code: ja
  Exclude files: ModuleJapaneseLanguagePack.php

Syncing translations from core-repo/src/Common/Messages/ja to module/Messages/ja
  Copying: Common.php
  Copying: Extensions.php
  Copying: GeneralSettings.php
  ...
  Skipping excluded file: ModuleJapaneseLanguagePack.php

Translation sync completed successfully

Updated files:
 M Messages/ja/Common.php
 M Messages/ja/Extensions.php
```

## Use Cases

This action is designed for Language Pack modules that:
- Need to include translations from MikoPBX Core
- Want to automatically sync Weblate translations
- Maintain some module-specific translation files
- Build releases on GitHub Actions

## Notes

- The action only runs during GitHub Actions builds
- If sync is disabled, the build process continues without errors
- Files are overwritten unless specified in `exclude_files`
- The source directory must exist in the source repository
