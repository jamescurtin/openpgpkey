# openpgpkey — WKD for jamescurtin.com

Serves the OpenPGP public key for `james@jamescurtin.com` via
[Web Key Directory (WKD)](https://wiki.gnupg.org/WKD), hosted on GitHub Pages at
`https://openpgpkey.jamescurtin.com` (advanced WKD method).

## Layout
- `.well-known/openpgpkey/jamescurtin.com/hu/<hash>` — binary public key (minimal, one UID)
- `.well-known/openpgpkey/jamescurtin.com/policy` — empty policy file (required by WKD)
- `.nojekyll` — makes GitHub Pages serve the dotted `.well-known/` path
- `CNAME` — custom domain `openpgpkey.jamescurtin.com`
- `.github/workflows/pages.yml` — builds and deploys to GitHub Pages via Actions

The `<hash>` is the WKD hash of the local-part `james`: `e7f4c665fnqgra7upz6aw69pp1n5y1kd`.

## Updating the key
Expiry renewals do NOT require regenerating this file. Regenerate only when the key
material or UID set changes:
```sh
gpg --no-armor --export --export-options export-minimal \
  --export-filter 'keep-uid=mbox=james@jamescurtin.com' 0xCC082447FE25D271 \
  > .well-known/openpgpkey/jamescurtin.com/hu/e7f4c665fnqgra7upz6aw69pp1n5y1kd
git commit -am "update WKD key" && git push
```

## Verify
```sh
gpg-wks-client --check james@jamescurtin.com
curl -sI https://openpgpkey.jamescurtin.com/.well-known/openpgpkey/jamescurtin.com/hu/e7f4c665fnqgra7upz6aw69pp1n5y1kd
gpg --auto-key-locate clear,wkd --locate-keys james@jamescurtin.com
```

## Caveats (GitHub Pages)
- No custom headers → no CORS; the gpg CLI works, browser clients (Mailvelope) may not.
- Extensionless `hu/<hash>` is served as `application/octet-stream`, which WKD expects.
