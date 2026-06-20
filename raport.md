# Raport z analizy sieci lokalnej z wykorzystaniem Nmap

## 1. Cel analizy

Celem analizy było sprawdzenie podstawowego stanu bezpieczeństwa sieci lokalnej poprzez wykrycie aktywnych urządzeń, identyfikację otwartych portów oraz rozpoznanie usług działających na wybranym hoście.

Analiza została wykonana w środowisku lokalnym, na urządzeniach należących do autora projektu.

## 2. Środowisko testowe

Analiza została przeprowadzona w sieci lokalnej. Głównym badanym adresem był adres routera/bramy domyślnej:

```text
192.168.1.1
```

Do wykonania skanów użyto narzędzia Nmap.

## 3. Wykonane działania

### 3.1 Wykrywanie hostów

Pierwszym etapem było sprawdzenie, jakie urządzenia są aktywne w sieci lokalnej.

Przykładowe polecenie:

```bash
nmap 192.168.1.0/24 -oN scans/01-host-discovery.txt
```

Celem tego etapu było uzyskanie listy aktywnych hostów, które mogą być dalej analizowane.

### 3.2 Podstawowe skanowanie routera

Następnie wykonano podstawowy skan wybranego hosta:

```bash
nmap 192.168.1.1 -oN scans/02-router-basic-scan.txt
```

Skan pozwolił sprawdzić, które porty są otwarte, zamknięte lub filtrowane.

### 3.3 Rozpoznawanie usług

Kolejnym krokiem było rozpoznanie wersji usług:

```bash
nmap -sV 192.168.1.1 -oN scans/03-service-version-scan.txt
```

Opcja `-sV` pozwala ustalić, jakie usługi działają na otwartych portach oraz, jeśli to możliwe, określić ich wersje.

## 4. Analiza wyników

Wyniki skanów pokazują, jakie usługi są dostępne w badanej sieci. Otwarte porty należy traktować jako potencjalne punkty wejścia, dlatego każdy z nich powinien zostać przeanalizowany.

Najważniejsze pytania podczas analizy:

* Czy dana usługa jest potrzebna?
* Czy usługa powinna być dostępna dla wszystkich urządzeń w sieci?
* Czy panel administracyjny jest odpowiednio zabezpieczony?
* Czy urządzenie korzysta z aktualnego oprogramowania?
* Czy wykryte usługi nie są wystawione do internetu?

## 5. Potencjalne ryzyka

Potencjalne ryzyka związane z otwartymi portami i usługami:

* dostęp do panelu administracyjnego routera przez niepowołane osoby,
* używanie słabego lub domyślnego hasła administratora,
* nieaktualne oprogramowanie urządzenia,
* działanie niepotrzebnych usług,
* możliwość rozpoznania urządzenia przez atakującego,
* większa powierzchnia ataku w sieci lokalnej.

## 6. Rekomendacje

Zalecane działania bezpieczeństwa:

1. Zmienić domyślne hasło administratora routera.
2. Wyłączyć zdalne zarządzanie routerem, jeśli nie jest potrzebne.
3. Regularnie aktualizować firmware routera.
4. Korzystać z WPA2/WPA3 dla sieci Wi-Fi.
5. Wyłączyć nieużywane usługi.
6. Okresowo sprawdzać listę urządzeń podłączonych do sieci.
7. Nie udostępniać panelu administracyjnego routera do internetu.
8. Dokumentować wyniki okresowych skanów bezpieczeństwa.

## 7. Wnioski końcowe

Projekt pozwolił przećwiczyć podstawowe czynności związane z analizą bezpieczeństwa sieci lokalnej. Wykonane skany umożliwiły identyfikację aktywnych urządzeń, otwartych portów oraz usług.

Tego typu analiza jest przydatna w pracy związanej z cyberbezpieczeństwem, ponieważ stanowi podstawę do oceny powierzchni ataku, wykrywania nieznanych usług oraz przygotowywania rekomendacji bezpieczeństwa.

Projekt może być rozwijany o dodatkowe elementy, takie jak analiza podatności, porównywanie wyników skanów w czasie, automatyzacja raportowania lub integracja z prostym dashboardem.
