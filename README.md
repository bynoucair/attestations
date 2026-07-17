# Système de Vérification d'Attestations — Allthinkers x Geny's World

A complete, free certificate-verification system that runs entirely on **GitHub Pages** — no server, no database, no cost.

## How it works

1. The **generator** (`generateur-attestation.html`) prints each certificate with a unique ID (e.g. `AT-2026-9481`) and a QR code pointing to your GitHub Pages site.
2. Anyone scanning the QR code lands on the **verification page** (`verify/`), which checks the ID against **`certificates.json`** — your registry of issued certificates.
3. The page shows **"Certificat Authentique"** with the graduate's full details, or **"Certificat Non Reconnu"** if the ID is unknown.

## File structure

```
├── index.html                    → landing page with manual ID search
├── verify/
│   └── index.html                → verification page (QR codes land here)
├── certificates.json             → YOUR REGISTRY — edit this for each graduate
├── generateur-attestation.html   → the certificate generator (updated)
└── .nojekyll                     → tells GitHub to serve files as-is
```

## Setup on GitHub (about 5 minutes)

### 1. Create the repository
- Go to [github.com/new](https://github.com/new)
- Name it e.g. `attestations` — **Public** (required for free GitHub Pages)
- Click **Create repository**

### 2. Upload the files
- On the repo page, click **"uploading an existing file"**
- Drag in **all the files and the `verify/` folder** from this package
- Commit the changes

### 3. Enable GitHub Pages
- Go to **Settings → Pages**
- Under *Source*: choose **Deploy from a branch**
- Branch: **main**, folder: **/ (root)** → **Save**
- Wait 1–2 minutes. Your site is live at:
  `https://YOUR-USERNAME.github.io/attestations/`

### 4. Point the generator at your new site
Open `generateur-attestation.html` and change **one line** at the top of the script:

```js
const VERIFY_BASE_URL = 'https://YOUR-USERNAME.github.io/attestations/verify/';
```

That's it — every QR code you generate now points to your live verification page.

## Issuing a new certificate (each graduate)

**Step 1** — Give them an ID in the generator (e.g. `AT-2026-9483`) and download the PDF.

**Step 2** — Register them by adding an entry to `certificates.json` on GitHub
(pencil icon → edit → Commit changes):

```json
"AT-2026-9483": {
  "status": "valid",
  "name": "Madame Exemple Nom",
  "date": "Le 20 Juillet 2026",
  "program": "Accompagnement aux outils numériques professionnels — …",
  "modules": [
    "Module 0 : Kit de Survie",
    "Session 1.1 : Word & Excel",
    "Session 1.3 : Canva & Retouche IA"
  ],
  "director": "Madame Nihad El Idrissi, Directrice de l'école",
  "trainer": "Monsieur Noucair B",
  "trainerTitle": "Consultant en Innovation Technologique & Sociale | Allthinkers CEO"
}
```

> ⚠️ JSON is picky: entries are separated by commas, and every quote matters.
> Validate the file at [jsonlint.com](https://jsonlint.com) before committing if unsure.

**To revoke a certificate**, change its `"status"` to anything other than `"valid"`
(e.g. `"revoked"`) — the verification page will immediately show it as unrecognized.

## Testing

Once deployed, open:
- `https://YOUR-USERNAME.github.io/attestations/verify/?id=AT-2026-9481` → should show **Certificat Authentique**
- `https://YOUR-USERNAME.github.io/attestations/verify/?id=FAKE-123` → should show **Certificat Non Reconnu**

(Two sample IDs, `AT-2026-9481` and `AT-2026-9482`, are included in `certificates.json` — replace them with your real graduates.)

## Honest limitations

- `certificates.json` is public (like everything on GitHub Pages), so the registry of names is viewable by anyone who looks for the file. For a training organization this is usually acceptable — it is the same information printed on the certificates themselves.
- This system proves an ID is *registered*; it does not stop someone from photo-editing a name onto a genuine certificate image. The registered-ID check is the standard level of verification for participation certificates.
- If you later need private data, one-click revoking via API, or analytics on scans, you would need a small backend (e.g. Supabase/Firebase) — the verification page is structured so it can be upgraded without changing the QR codes.
