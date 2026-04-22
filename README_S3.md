# Amazon S3

## S3 CLI

`s3://prombot-ai/release/`

```bash
# List artifacts
aws s3 ls s3://prombot-ai/release/ --recursive

# Download one file
aws s3 cp s3://prombot-ai/release/<path/to/file> .

# Download everything
aws s3 sync s3://prombot-ai/release/ ./artifacts
```

## S3 Key

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

## S3 Access

By default, S3 objects are private. To allow **public (anonymous) download** for files under `s3://prombot-ai/release/`:

1. AWS Console → **S3** → bucket `prombot-ai` → **Permissions**
2. **Block public access (bucket settings)** → Edit
   - Uncheck the options that **block public bucket policies** (so the bucket policy below can take effect)
3. **Bucket policy** → add an allow rule for public read on the `release/` prefix:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadReleasePrefix",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::prombot-ai/release/*"
    }
  ]
}
```

Optional: if you want public **listing** for `release/` (not required for direct download), also allow `s3:ListBucket` with a prefix condition:

```json
{
  "Sid": "PublicListReleasePrefix",
  "Effect": "Allow",
  "Principal": "*",
  "Action": "s3:ListBucket",
  "Resource": "arn:aws:s3:::prombot-ai",
  "Condition": {
    "StringLike": { "s3:prefix": ["release/*"] }
  }
}
```
