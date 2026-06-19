# Analiza sieci lokalnej z wykorzystaniem Nmap

Projekt przedstawia podstawową analizę bezpieczeństwa sieci lokalnej z użyciem narzędzia Nmap. Celem było wykrycie aktywnych hostów, sprawdzenie otwartych portów, identyfikacja usług oraz przygotowanie raportu z rekomendacjami bezpieczeństwa.

## Cel projektu

Celem projektu było pokazanie praktycznego procesu analizy sieci lokalnej:

- wykrywanie aktywnych urządzeń,
- skanowanie otwartych portów,
- identyfikacja usług,
- interpretacja wyników,
- ocena ryzyka,
- przygotowanie rekomendacji bezpieczeństwa.

## Użyte narzędzia

- Nmap
- Windows CMD
- Markdown
- GitHub

## Zakres analizy

Analiza została wykonana w prywatnej sieci lokalnej `192.168.1.0/24`.

Badaniu poddano:

- router domowy,
- komputer użytkownika,
- inne aktywne urządzenia wykryte w sieci.

## Struktura projektu

```text
nmap-network-analysis/
├── scans/
│   ├── 01-host-discovery.txt
│   ├── 02-router-basic-scan.txt
│   ├── 03-router-service-version.txt
│   ├── 04-pc-basic-scan.txt
│   └── 05-pc-service-version.txt
├── report/
│   └── raport-bezpieczenstwa.md
├── screenshots/
└── README.md