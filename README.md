# Aktywny Senior — landing programu

Strona programu PZSQ „Aktywny Senior” (bezpłatne zajęcia squasha dla osób 55+): **https://squashaktywnysenior.pl/**

- `index.html` — cała strona (HTML + CSS + JS w jednym pliku)
- `img/` — obrazy (wcześniej osadzone w HTML jako base64)

## Hosting
Strona stoi na serwerze VPS (Apache) — tym samym środowisku co RMP. **Nie** na GitHub Pages
(workflow `static.yml` publikuje tylko kopię pod github.io). Do 11.11.2026 zmiany były wgrywane ręcznie na serwer;
docelowo deploy po merge do `main` przez GitHub Actions (SSH + rsync), jak w repo RMP.

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
