## 📌 Introduction

**AutoBackupGayHub** is a simple tool that harnesses **GitHub Actions** to automatically back up your GitHub repositories to **Dropbox**, ensuring that your code is always safe and accessible. Utilizing GitHub Actions, it automatically triggers a backup process each time you push to your repository, packing your repository contents and uploading them to your Dropbox space to safeguard your code against loss.

## 🚀 Key Features

- 🔄 **Automatic Backup**: Initiates a backup process after every `push` action.
- 🛡️ **Secure Storage**: Directly backs up repository content to Dropbox, protecting against data loss.
- 📥 **Convenient Access**: Easily retrieve your repository backups from Dropbox.

## ⚙️ Quick Start

1. Create a new workflow file in your GitHub repository: `.github/workflows/AutoBackupGayHub.yml`.
2. Copy and paste the following YAML content:

```
name: 备份至Dropbox

on: push

jobs:
  backup:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2

    - name: 打包并上传至Dropbox
      run: |
        sudo apt-get update && sudo apt-get install -y zip curl jq
        UTC_TS=$(date --utc +'%Y%m%d%H%M%S')
        CN_TS=$(date -d "$UTC_TS UTC" -d '+8 hours' +'%Y%m%d%H%M%S')
        ARCHIVE_NAME="$CN_TS.zip"
        zip -r $ARCHIVE_NAME .
        UPLOAD_PATH="/backups/$ARCHIVE_NAME"
        API_ARG="{\"path\": \"$UPLOAD_PATH\",\"mode\": \"add\",\"autorename\": true,\"mute\": false,\"strict_conflict\": false}"
        curl -X POST https://content.dropboxapi.com/2/files/upload \
          --header "Authorization: Bearer ${{ secrets.TOKEN }}" \
          --header "Dropbox-API-Arg: $API_ARG" \
          --header "Content-Type: application/octet-stream" \
          --data-binary @$ARCHIVE_NAME
```

## 🛠 Configuration

Before utilizing this tool, you need to configure the following in GitHub Secrets to allow the workflow access to your Dropbox account:

- `TOKEN`: Your Dropbox access token.

Add this secret in your GitHub repository under "Settings" -> "Secrets and variables" -> "Actions" -> "Secrets".

## 🗝 Obtaining Dropbox Token

There are two methods to acquire a Dropbox Token:

### Method 1: One-click Acquisition

1. Visit our provided authorization website: [AutoBackupGayHub](https://autobackupgayhub.pages.dev/) (hosted on Cloudflare Page, safe to use).

2. Click to authorize and grant permissions to the AutoBackupGayHub application on the new page.

3. Copy the generated Token and save it to GitHub Secrets.

   ⚠️ **Note**: Be sure to securely store your Token information. Should it be lost, you will need to generate a new Token.

### Method 2: Manual Acquisition

1. Visit [Dropbox Developers App Console](https://www.dropbox.com/developers/apps).

2. Create a new App and assign appropriate permissions (e.g., file and folder access).

3. Generate and copy the Access Token.

4. Save the generated Token to GitHub Secrets.

   For reference, here’s a tutorial: [Creating and Obtaining Dropbox Access Token](https://preventdirectaccess.com/docs/create-app-key-access-token-for-dropbox-account/).
