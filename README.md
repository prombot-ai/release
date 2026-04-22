# release

PromBot release distribution.

**Bundled downloads (this repo):** [Latest release](https://github.com/prombot-ai/release/releases/latest) · [v0.14.0](https://github.com/prombot-ai/release/releases/tag/v0.14.0)

---

## PromBot CLI

| Platform | Download |
|----------|----------|
| Linux | [`prombot-cli_0.10.0_linux.tar.gz`](https://github.com/prombot-ai/release/releases/latest/download/prombot-cli_0.10.0_linux.tar.gz) |
| | [`prombot-cli_0.10.0_linux_arm64.tar.gz`](https://github.com/prombot-ai/release/releases/latest/download/prombot-cli_0.10.0_linux_arm64.tar.gz) |
| macOS | [`prombot-cli_0.10.0_macos.tar.gz`](https://github.com/prombot-ai/release/releases/latest/download/prombot-cli_0.10.0_macos.tar.gz) |
| Windows | [`prombot-cli_0.10.0_windows.zip`](https://github.com/prombot-ai/release/releases/latest/download/prombot-cli_0.10.0_windows.zip) |

## PromBot Desktop

| Platform | Download |
|----------|----------|
| macOS | [`prombot-desktop_0.14.0_aarch64.dmg`](https://github.com/prombot-ai/release/releases/latest/download/prombot-desktop_0.14.0_aarch64.dmg) |
| Linux | [`prombot-desktop_0.14.0_amd64.deb`](https://github.com/prombot-ai/release/releases/latest/download/prombot-desktop_0.14.0_amd64.deb) |
| Linux | [`prombot-desktop_0.14.0_arm64.deb`](https://github.com/prombot-ai/release/releases/latest/download/prombot-desktop_0.14.0_arm64.deb) |
| Windows (portable) | [`prombot-desktop_0.14.0_x64.exe`](https://github.com/prombot-ai/release/releases/latest/download/prombot-desktop_0.14.0_x64.exe) |
| Windows (installer) | [`prombot-desktop_0.14.0_x64.msi`](https://github.com/prombot-ai/release/releases/latest/download/prombot-desktop_0.14.0_x64.msi) |

## PromBot App

| Platform | Download |
|----------|----------|
| Android | [`prombot-app-0.10.0-android.apk`](https://github.com/prombot-ai/release/releases/latest/download/prombot-app-0.10.0-android.apk) |

## PromBot S3

### Amazon S3

`s3://prombot-ai/release/`

```bash
# List artifacts
aws s3 ls s3://prombot-ai/release/ --recursive

# Download one file
aws s3 cp s3://prombot-ai/release/<path/to/file> .

# Download everything
aws s3 sync s3://prombot-ai/release/ ./artifacts
```

### Amazon Configure

Create an IAM access key, then add it to GitHub Actions Secrets.

1. AWS Console → **IAM** → **Users** → **Create user** (e.g. `github-actions-prombot-release`)
2. Attach permissions that can write to `s3://prombot-ai/release/` (avoid using the root account; prefer least privilege)

   Minimal policy example:

   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "ListBucket",
         "Effect": "Allow",
         "Action": ["s3:ListBucket"],
         "Resource": "arn:aws:s3:::prombot-ai"
       },
       {
         "Sid": "WriteReleasePrefix",
         "Effect": "Allow",
         "Action": [
           "s3:PutObject",
           "s3:AbortMultipartUpload",
           "s3:ListMultipartUploadParts"
         ],
         "Resource": "arn:aws:s3:::prombot-ai/release/*"
       }
     ]
   }
   ```

3. IAM → **Users** → your user → **Security credentials** → **Access keys** → **Create access key**
4. Save the generated values (shown only once):
   - `AWS_ACCESS_KEY_ID`
   - `AWS_SECRET_ACCESS_KEY`
5. GitHub repo → **Settings** → **Secrets and variables** → **Actions** → add:
   - `AWS_ACCESS_KEY_ID`
   - `AWS_SECRET_ACCESS_KEY`
   - `AWS_REGION` (e.g. `ap-southeast-1`)
