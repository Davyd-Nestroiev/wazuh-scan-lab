# Troubleshooting

Issues encountered while building the lab, and how each was resolved.

## Architecture mismatch: DVWA image
- **Symptom:** original `vulnerables/web-dvwa` image failed with `exec format error`
- **Root cause:** the image is amd64-only, incompatible with Apple Silicon (arm64)
- **Fix:** switched to `cytopia/dvwa`, an arm64-compatible alternative

## Docker Compose binary naming conflict
- **Symptom:** `make start` on the `cytopia/dvwa` repo failed
- **Root cause:** the Makefile hardcodes the legacy `docker-compose` (hyphenated) binary name, which doesn't exist once the modern `docker compose` plugin is installed instead
- **Fix:** ran the `docker compose` commands directly rather than through the Makefile's `make` targets

## `docker.socket` systemd conflict
- **Symptom:** conflict during Docker install
- **Root cause:** newly installed Docker packages overwrote existing systemd units for `docker.socket`
- **Fix:** resolved with `systemctl daemon-reload` + `systemctl reset-failed` on the affected units

## Login loop bug: hardcoded cookie domain
- **Symptom:** DVWA login kept looping back to the login page with no visible error, in both browser and via curl
- **Diagnostic path:** verified DB connectivity (OK) → checked CSRF token logic (`checkToken()`) → traced to session cookies not persisting → found the `Set-Cookie` header included the port (`domain=192.168.99.130:8000`), which is invalid per cookie spec — browsers/curl silently reject it
- **Root cause:** `dvwaPage.inc.php` line 55 sets the session cookie domain directly from `$_SERVER['HTTP_HOST']`, which includes the port on a non-standard port setup (`:8000`)
- **Fix:** patched line 55 to strip the port — `'domain' => explode(':', $_SERVER['HTTP_HOST'])[0]`
- **Verification:** confirmed via curl round-trip that `PHPSESSID` now persists across requests, and the DB shows `users` and `guestbook` tables created successfully
- **Caveat:** this fix lived inside the running container's filesystem, not the source image — it would be wiped on container rebuild or `docker compose down`
- Confirmed login (`admin`/`password`) successful — full DVWA dashboard loaded
