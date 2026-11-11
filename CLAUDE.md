# Aktywny Senior — landing programu

Strona programu PZSQ „Aktywny Senior” (bezpłatne zajęcia squasha dla osób 55+): **https://squashaktywnysenior.pl/**

- `index.html` — cała strona (HTML + CSS + JS w jednym pliku)
- `img/` — obrazy (wcześniej osadzone w HTML jako base64)

## Hosting i deploy
Strona stoi na serwerze VPS (Apache, `57.128.237.169`) w katalogu `/var/www/aktywnysenior` — tym samym serwerze co RMP.
**Deploy jest automatyczny po merge do `main`** (`.github/workflows/deploy.yml`):
kopia zapasowa obecnej wersji do `~/backups/sites/` (10 ostatnich) → rsync `index.html` + `img/` → sprawdzenie,
że https://squashaktywnysenior.pl/version.txt pokazuje nowy commit. Ponowny deploy: Actions → „Deploy strony” → Run workflow.
GitHub Secrets: `SSH_PRIVATE_KEY`, `SSH_HOST`, `SSH_USER`.

## Formularz zapisów → RMP
Zgłoszenia idą do RMP na **produkcji**: `https://rmp.polskisquash.pl`, formularz `aktywny-senior-zapis`
(stałe `RMP_BASE`, `RMP_FORM_KEY`, `MIN_AGE` w skrypcie na dole `index.html`).

**Kluby i terminy nie są wpisane w HTML** — strona pobiera je z RMP przy ładowaniu:
- kluby: `GET /api/public/forms/aktywny-senior-zapis/clubs` (wysyłane jest `Club.id`),
- terminy: definicja formularza `GET /api/public/forms/aktywny-senior-zapis` (pole `termin_spotkania_aktywnysenior`).

Żeby zmienić kluby albo terminy w formularzu — zmień je w panelu RMP. (Sekcja „Kluby” z adresami
na stronie jest nadal w HTML.)

Strona sama pokazuje „Zapisy są obecnie zamknięte”, gdy wszystkie terminy w RMP minęły albo formularz
w RMP jest wyłączony. Wiek (≥ 55 lat) jest sprawdzany na dzień wybranego spotkania.

## Zasady pracy
- Zmiany tylko przez repo: branch → PR → właściciel klika Merge. Nikt nie edytuje plików bezpośrednio na serwerze.
- Nie wklejaj obrazów jako base64 — pliki do `img/`.
- Payload formularza musi dokładnie odpowiadać polom formularza w RMP (RMP odrzuca nieznane pola — walidacja `strict`).
- Przed PR: test w przeglądarce (Playwright) — stan „zapisy zamknięte” na danych z produkcji oraz otwarty formularz
  z zamockowanymi odpowiedziami RMP (definicja formularza, `/clubs`, `/submit`).
