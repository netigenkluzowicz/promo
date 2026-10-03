# Cross-promocja — plik `feed.json` (FIX-034, F17)

Appka pokazuje jedną kartę innej appki Netigenu: w Ustawieniach (sekcja „Może Ci się przydać” pod „O aplikacji”)
i na „Trening ukończony” (tylko bez Premium). Treść jest sterowana centralnie plikiem JSON pod `AppConfig.promoFeedUrl`
— ten plik: `https://netigenkluzowicz.github.io/promo/feed.json` (GitHub Pages z gałęzi `main`).

**Zmiana promowanej appki = edycja `feed.json` i commit na `main`.** Appki pobierają plik najwyżej raz na 12 h,
więc zmiana dociera do użytkowników w ciągu doby, bez wydawania aktualizacji. Plik jest publiczny — tylko treść karty, bez danych.

## Format

```json
{"version": 1, "apps": [
  {"id": "perfect-metronome",
   "android": "pl.netigen.perfect.metronome",
   "ios": "1234567890",
   "icon": "https://…/ikona.png",
   "name": {"en": "Perfect Simple Metronome", "pl": "…"},
   "text": {"en": "Steady rhythm for jump rope and warm-ups.", "pl": "Rytm do skakanki i rozgrzewki."},
   "weight": 1,
   "exclude": ["pl.netigen.inna.appka"]}
]}
```

| Pole | Wymagane | Znaczenie |
|---|---|---|
| `version` | tak | `1`; inna wartość = plik ignorowany |
| `id` | tak | identyfikator wpisu (dowolny, unikalny) |
| `android` / `ios` | co najmniej jedno | pakiet Google Play / numer App Store (z `id` lub bez); brak dla platformy = wpis tam niewidoczny |
| `icon` | nie | URL ikony (≥ 96 px); brak albo błąd = ikona zastępcza |
| `name`, `text` | tak, z `en` | po jednym krótkim zdaniu na język (`en`, `pl`, `de`, `pt_BR` …); brak języka → `en` |
| `weight` | nie (1) | waga losowania; `0` = wpis wyłączony |
| `exclude` | nie | pakiety appek, w których wpisu nie pokazujemy |

## Zasady w appce

- Pomijane: wpis z własnym pakietem appki, wpis bez identyfikatora dla bieżącej platformy, wpis z `exclude` zawierającym
  własny pakiet, wpis niepełny (reszta pliku działa). Pusta lista `apps` = brak karty.
- Wybór: losowanie ważone `weight`, stałe w obrębie dnia (ta sama karta przez cały dzień).
- Pobranie: w tle po starcie, limit 4 s, najwyżej raz na 12 h (także po błędzie), zwykły GET bez parametrów i danych
  użytkownika. Kolejność źródeł: sieć → ostatnia dobra treść z pamięci → treść wbudowana `assets/promo/feed.json`.
- „Zobacz” otwiera sklep (Play: `market://details?id=` → WWW; App Store: `itms-apps://` → WWW) przez `openExternalLink`
  powłoki, więc powrót ze sklepu nie pokazuje App Open.

Pierwsza appka z kartą: Boxing Timer (`netigenkluzowicz/boxing_round_timer`, kod w `lib/shell/promo/`, kandydat do szablonu — LISTA-PRAC F17).
