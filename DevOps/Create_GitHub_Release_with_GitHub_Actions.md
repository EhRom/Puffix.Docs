# GitHub CLI in Github Actions with GitHub Actions

A GitHub Release can be created within GitHub Actions in two steps:

1. Create one or more packages,
1. Create the release and upload packages.

## Create packages

The build output are packaged on archive files (on the example below, Zip and 7z files). Files are then copied to a directory:

```yaml
env:
  PACKAGE_VERSION: <package version, e.g. 1.0.0>
  APP_NAME: <package name>
  
  # ...

    - name: Package
      working-directory: './out/'
      run: |
        mkdir "../${{ env.APP_NAME }}-${{ env.PACKAGE_VERSION }}/"

        mkdir "../${{ env.APP_NAME }}-${{ env.PACKAGE_VERSION }}/linux-x64/"
        7z a ${{ env.APP_NAME }}-linux-x64.7z ./linux-x64/*
        zip -r ${{ env.APP_NAME }}-linux-x64.zip ./linux-x64/*

        cp -a ./${{ env.APP_NAME }}-linux-x64.* ../${{ env.APP_NAME }}-${{ env.PACKAGE_VERSION }}/

        mkdir "../${{ env.APP_NAME }}-${{ env.PACKAGE_VERSION }}/win-x64/"
        7z a ${{ env.APP_NAME }}-win-x64.7z ./win-x64/*
        zip -r ${{ env.APP_NAME }}-win-x64.zip ./win-x64/*

        cp -a ./${{ env.APP_NAME }}-win-x64.* ../${{ env.APP_NAME }}-${{ env.PACKAGE_VERSION }}/
```

### (option) Publish packages as artefact

Optionally, the directory to which the archives are copied can be published as an artifact:

```yaml
env:
  PACKAGE_VERSION: <package version, e.g. 1.0.0>
  APP_NAME: <package name>
  
  # ...

    - name: Upload
      uses: actions/upload-artifact@v4
      with: 
        name: ${{ env.APP_NAME }}-${{ env.PACKAGE_VERSION }}
        path: '${{ env.APP_NAME }}-${{ env.PACKAGE_VERSION }}' 
```

## Create release and upload packages

To create a version from a GitHub action, the GitHub CLI commands are used and require a GitHub token (cf. [GitHub CLI in Github Actions](./GitHub_CLI_in_GitHub_Actions.md)). The token is stored in a step environment variable named `GH_TOKEN`.

In the following sample, two commands are used:

1. [`gh release create`](https://cli.github.com/manual/gh_release_create): to create the release.
1. [`gh release upload`](https://cli.github.com/manual/gh_release_upload): to reference files in the release.

```yaml
env:
  PACKAGE_VERSION: <package version, e.g. 1.0.0>
  APP_NAME: <package name>
  
  # ...

    - name: Create release
      env:
        GH_TOKEN: ${{ secrets.PFX_IMAGES_GH_CLI_TOKEN }}
      run: |
        gh release create v${{ env.PACKAGE_VERSION }}-$GITHUB_RUN_ID --target main --generate-notes 
        
        gh release upload v${{ env.PACKAGE_VERSION }}-$GITHUB_RUN_ID ${{ env.APP_NAME }}-${{ env.PACKAGE_VERSION }}/${{ env.APP_NAME }}-linux-x64.zip
        gh release upload v${{ env.PACKAGE_VERSION }}-$GITHUB_RUN_ID ${{ env.APP_NAME }}-${{ env.PACKAGE_VERSION }}/${{ env.APP_NAME }}-linux-x64.7z
        gh release upload v${{ env.PACKAGE_VERSION }}-$GITHUB_RUN_ID ${{ env.APP_NAME }}-${{ env.PACKAGE_VERSION }}/${{ env.APP_NAME }}-win-x64.zip
        gh release upload v${{ env.PACKAGE_VERSION }}-$GITHUB_RUN_ID ${{ env.APP_NAME }}-${{ env.PACKAGE_VERSION }}/${{ env.APP_NAME }}-win-x64.7z

```

---

[Back](../README.md)
