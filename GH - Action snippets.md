# Github action snippets

## Download latest Sentilan message dll and extract it

```yaml
- uses: robinraju/release-downloader@v1.3
  with:
     repository: "ExmosLTD/sentilan-message-dll-v1"
     latest: true
     fileName: "message-dll.zip"
     out-file-path: "src"
     token: "${{ secrets.DOWNLOAD_DLL_TOKEN }}"
```

- `DOWNLOAD_DLL_TOKEN` is a PAT to allow acecss to the message dll repo.

## Extract a Zip file

```yaml
- name: Extract the message dll zip
  run: 7z.exe x .\src\message-dll.zip -o".\src"
```

## Debug the folder structure in the VM

```yaml
- name: Debug
  run: tree /f
```

## Push a Release

```yaml
- name: Push to Release
  uses: "marvinpinto/action-automatic-releases@latest"
  with:
     repo_token: "${{ secrets.GITHUB_TOKEN }}"
     automatic_release_tag: "${{ steps.verstr.outputs.contents }}"
     prerelease: false
     title: "${{ steps.verstr.outputs.contents }}"
     files: | 
        foo.zip
```

- `steps.verstr.outputs.contents` references a previous step that has an output called `contents`

## Create an Issue on failure

```yaml
- name: Create issue on failure.
  if: failure()
  uses: JasonEtco/create-an-issue@v2
     env:
         GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
     with:
         assignees: ${{ github.actor }}
```

## Build on push

```yaml
on:
  # Triggers the workflow on push or pull request events but only for the main branch
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:
```

Put this at the top under the `name` tag.

## Sha256 all the release files and create a hashes.txt

```yaml

- name: Checkout ExmosLTD/gha-sha256-files
  uses: actions/checkout@v2
  with:
    repository: ExmosLTD/gha-sha256-files
    ref: refs/heads/main
    token: ${{ github.token }}
    persist-credentials: false
    path: ./.github/actions/gha-sha256-files

- name: Sha256 the built exe and store it in hashes.txt
  uses: ./.github/actions/gha-sha256-files
  with:
    files: foo/
```

## Send a message to MS Teams

```yaml
- name: Send message to ms teams
  uses: dhollerbach/github-action-send-message-to-ms-teams@1.0.10
  with:
    webhook: ${{secrets.MSTEAMSWEBHOOK}}
    message: 'A new Sentilan Proxy API has been released! Version ${{steps.verstr.outputs.prop}}. Go here to get it https://github.com/ExmosLTD/sentilan-check-sap-to-apl/releases/tag/${{steps.verstr.outputs.prop}}'
```

- `secrets.MSTEAMSWEBHOOK` needs to be the webhok you used when setting up Teams
