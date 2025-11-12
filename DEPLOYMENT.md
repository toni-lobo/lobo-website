# Deployment Guide - Google Cloud Storage

## Current Issue: Styling Not Showing

If your website is displaying text but no styling, this is a **GCS permissions issue**. Follow the steps below to fix it.

## Quick Fix

### 1. Confirm the Problem
Open your live website and press `F12` (or `Ctrl+Shift+I` / `Cmd+Opt+I`) to open Developer Tools.

Click the **Console** tab. You should see errors like:
- `403 (Forbidden)` - **This is a permissions issue** (most common)
- `404 (Not Found)` - This means file paths are wrong (unlikely, as our paths are correct)

### 2. Fix GCS Permissions (Required for Public Website)

Your bucket needs to allow public read access for browsers to download CSS, JavaScript, and images.

**Steps:**
1. Go to [Google Cloud Console](https://console.cloud.google.com/storage/)
2. Open your Cloud Storage bucket
3. Click the **Permissions** tab
4. Click **Grant Access** (or **Add**)
5. In **New principals**, type: `allUsers`
6. In **Select a role**, choose: **Storage Object Viewer**
7. Click **Save**
8. You'll see a warning that this makes objects publicly accessible - this is correct for a website
9. Wait 1-2 minutes, then refresh your website

**That's it!** Your styling should now work.

## File Structure

Your bucket should contain these files in the root:
```
index.html
styles.css
lobo.png
```

Your HTML correctly references these files using relative paths:
- `<link rel="stylesheet" href="styles.css">`
- `<img src="lobo.png" alt="Lobo Logo">`

## Deployment Steps

To deploy updates to your GCS bucket:

```bash
# Upload all files to your bucket
gsutil -m cp -r index.html styles.css lobo.png gs://YOUR-BUCKET-NAME/

# Set index.html as the main page (only needed once)
gsutil web set -m index.html gs://YOUR-BUCKET-NAME/

# Make sure permissions are correct (see step 2 above)
```

## Alternative: Grant Permissions via Command Line

If you prefer using the command line:

```bash
# Make all objects in the bucket publicly readable
gsutil iam ch allUsers:objectViewer gs://YOUR-BUCKET-NAME
```

## Troubleshooting

### Styling still not showing after permissions fix?

1. **Hard refresh** your browser: `Ctrl+Shift+R` (Windows/Linux) or `Cmd+Shift+R` (Mac)
2. **Check browser console** for any remaining 403/404 errors
3. **Verify file names** match exactly (case-sensitive):
   - The file must be named `styles.css` (not `style.css` or `Styles.css`)
   - The logo must be named `lobo.png` (not `Lobo.png` or `logo.png`)

### Security Concern: Making bucket public?

For a public website, this is necessary and safe. You're only granting:
- **Read access** (Storage Object Viewer role)
- Not write/delete access

If you have private files, keep them in a separate bucket.

## Next Steps: Automated Deployment

Consider setting up Cloud Build to automatically deploy from GitHub:

1. Push code to GitHub
2. Cloud Build detects the push
3. Automatically syncs files to GCS
4. Website updates instantly

Let me know if you'd like help setting this up!
