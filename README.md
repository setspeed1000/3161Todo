# 3161todo

An editable to-do site that runs entirely on GitHub Pages. No backend: the page commits changes to `data.json` through the GitHub API.

## How it works

- Everyone reads `data.json` (through the API for freshness, falling back to the Pages copy).
- **Unlock editing** asks for the editor password (default `test123`). The password decrypts a GitHub token stored encrypted in `secret.json` (AES-GCM, PBKDF2 key). The site then checks the token has push access before enabling edits.
- **Save** commits the new `data.json`. If someone else saved first, the page reloads their version instead of overwriting it.
- Pages redeploys after each commit (about a minute) for anyone not signed in.

## Setup

1. Push these three files to a new repo (`main` branch, root).
2. Settings → Pages → Source: *Deploy from a branch* → `main` / `/ (root)`.
3. Make an editor token: Settings → Developer settings → Fine-grained tokens → Generate.
   - Repository access: **Only select repositories** → this repo
   - Permissions: **Contents → Read and write**
   - Set an expiry.
4. Open `https://setspeed1000.github.io/3161todo/setup.html`, paste the token, keep or change the password, and download `secret.json`.
5. Commit `secret.json` to the repo root. Never commit the raw token: GitHub's secret scanning revokes tokens pushed to public repos.
6. Open the main page, press **Unlock editing**, enter the password.

To change the password, rerun `setup.html` and replace `secret.json`.

## Limits

- `secret.json` is public, so anyone can try passwords against it offline. `test123` falls instantly to a wordlist. Use a long passphrase for anything real.
- Whoever unlocks can change this repo's files, so scope the token to this repo only and set an expiry.
- Anonymous API reads are capped at 60 per hour per IP. On a shared school network the page falls back to the Pages copy when that runs out.
- Every save is a commit, so the repo history doubles as a change log.
