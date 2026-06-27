# Publishing Sentinel

Two destinations:

- **Website → Vercel** (already done): https://website-three-wheat-36.vercel.app
- **Source → GitHub** (needs two one-time auth steps on your Mac, below)

---

## 1. Push the source to a new GitHub repo

Your Mac has the GitHub CLI (`gh`) installed but **git itself needs Apple's Command Line
Tools**, and `gh` isn't logged in yet. Do these once:

```bash
# (a) Install git (a macOS dialog appears — accept it, then reopen Terminal)
xcode-select --install

# (b) Log in to GitHub
gh auth login          # choose: GitHub.com → HTTPS → login with browser
```

Then, from the repo root, create the repo and push everything in one command:

```bash
cd /Users/jongurakuqi/Projects/bitchat-vibe
gh repo create sentinel --public --source=. --remote=origin --push
```

That creates `github.com/<you>/sentinel`, wires up `origin`, and pushes. Use `--private`
instead of `--public` if you'd rather keep it closed for now.

> Prefer a clean history (no earlier commits)? Run this instead before `gh repo create`:
> ```bash
> rm -rf .git && git init && git add . && git commit -m "Sentinel: initial public release"
> ```

### What gets uploaded (and what doesn't)

`.gitignore` already excludes everything sensitive or heavy, so you won't leak anything:

- `node_modules/` (all of them), `dist/`, `.expo/`
- `sentinel/server/data/` — **the relay's accounts, password hashes, and ciphertext blobs**

Sanity-check before pushing:

```bash
git status                         # should NOT list server/data, node_modules, dist
git ls-files | grep -i data        # should print nothing from server/data
```

---

## 2. (Optional) Connect GitHub → Vercel for auto-deploys

So every push redeploys the site automatically:

1. Vercel dashboard → **Add New → Project → Import** the `sentinel` repo.
2. Set **Root Directory** to `website`.
3. Framework preset: **Other** (it's static — no build command, output `.`).
4. Deploy. Future `git push` to the default branch redeploys.

If you only ever deploy from the CLI, you can skip this and keep using
`cd website && vercel deploy --prod --yes`.

---

## 3. After publishing

- Update the GitHub URL in the site: replace `jongurakuqi/sentinel` in
  `website/index.html` with your actual repo path, then redeploy.
- Add a custom domain in Vercel (Settings → Domains), e.g. `sentinel.chat`.
- Consider a privacy policy + an in-app "delete my account" path before any app-store
  submission.
