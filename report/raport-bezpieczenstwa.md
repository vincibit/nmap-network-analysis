\# Raport bezpieczeństwa — analiza sieci lokalnej z wykorzystaniem Nmap



\## 1. Cel projektu



Celem projektu było przeprowadzenie podstawowej analizy bezpieczeństwa sieci lokalnej z wykorzystaniem narzędzia Nmap. Projekt obejmował wykrywanie aktywnych hostów, analizę otwartych portów, identyfikację usług oraz przygotowanie rekomendacji bezpieczeństwa.



Analiza została wykonana wyłącznie w środowisku własnej sieci lokalnej.



\## 2. Środowisko testowe



Analizowana sieć lokalna działała w zakresie:



```text

192.168.1.0/24

```



Adres IP komputera wykonującego skanowanie:



```text

192.168.1.21

```



Adres bramy domyślnej, czyli routera:



```text

192.168.1.1

```



Router został rozpoznany jako:



```text

funbox.home

```



\## 3. Wykrywanie aktywnych hostów



Pierwszym etapem było wykrycie aktywnych urządzeń w sieci lokalnej. Wykorzystano do tego komendę:



```bash

nmap -sn 192.168.1.0/24 -oN scans\\01-host-discovery.txt

```



Opcja `-sn` oznacza skanowanie wykrywające aktywne hosty bez pełnego skanowania portów. Wynik został zapisany do pliku:



```text

scans\\01-host-discovery.txt

```



W wyniku skanowania wykryto 8 aktywnych hostów:



```text

192.168.1.1   funbox.home

192.168.1.10  samsung.home

192.168.1.12  procom4-pro.home

192.168.1.26

192.168.1.30  oneplus-nord-ce4-lite-5g.home

192.168.1.34  device-414.home

192.168.1.21  arshavin-1.home

192.168.1.24  arshavin.home

```



\### Analiza



Skan wykazał, że w sieci lokalnej aktywnych było 8 urządzeń. Wśród nich znajdował się router, komputer wykonujący skanowanie, telefon OnePlus oraz inne urządzenia domowe. Sam fakt wykrycia hostów nie oznacza zagrożenia, ale pozwala określić, jakie urządzenia znajdują się w sieci i które z nich warto poddać dalszej analizie.



Najważniejszym urządzeniem z punktu widzenia bezpieczeństwa jest router `192.168.1.1`, ponieważ odpowiada za komunikację pomiędzy siecią lokalną a internetem.



\## 4. Podstawowe skanowanie routera



Drugim etapem było wykonanie podstawowego skanowania portów routera:



```bash

nmap 192.168.1.1 -oN scans\\02-router-basic-scan.txt

```



Wynik skanowania:



```text

PORT     STATE   SERVICE

53/tcp   open    domain

80/tcp   open    http

113/tcp  closed  ident

135/tcp  closed  msrpc

139/tcp  open    netbios-ssn

443/tcp  open    https

445/tcp  open    microsoft-ds

631/tcp  open    ipp

```



\### Analiza



Router posiada kilka otwartych portów dostępnych w sieci lokalnej. Otwarte porty nie oznaczają automatycznie podatności, ale wskazują, że na urządzeniu działają określone usługi.



Port `53/tcp` wskazuje na usługę DNS, co jest typowe dla routera domowego. Porty `80/tcp` oraz `443/tcp` wskazują na dostępność panelu administracyjnego przez HTTP oraz HTTPS. Bezpieczniejszą opcją jest korzystanie z HTTPS, ponieważ połączenie jest szyfrowane.



Szczególnej uwagi wymagają porty `139/tcp` oraz `445/tcp`, ponieważ są związane z usługami NetBIOS/SMB, czyli udostępnianiem zasobów w sieci lokalnej. W przypadku routera może to oznaczać funkcje udostępniania plików, dysku USB lub innych zasobów. Jeśli te funkcje nie są używane, warto rozważyć ich wyłączenie w panelu administracyjnym routera.



Port `631/tcp` wskazuje na usługę IPP, związaną z drukowaniem sieciowym. Jeśli drukowanie przez router nie jest używane, również warto sprawdzić, czy tę funkcję można wyłączyć.



Porty `113/tcp` oraz `135/tcp` były zamknięte, dlatego nie zostały uznane za istotne ryzyko w tej analizie.



\## 5. Wykrywanie wersji usług routera



Następnie wykonano skanowanie z wykrywaniem wersji usług:



```bash

nmap -sV 192.168.1.1 -oN scans\\03-router-service-version.txt

```



Wynik wskazał między innymi:



```text

53/tcp   open    domain      dnsmasq 2.89

80/tcp   open    http?

139/tcp  open    netbios-ssn Samba smbd 3.X - 4.X

443/tcp  open    ssl/https?

445/tcp  open    netbios-ssn Samba smbd 3.X - 4.X

631/tcp  open    ipp?

```



\### Analiza



Skanowanie z opcją `-sV` pozwoliło dokładniej rozpoznać część usług działających na routerze. Na porcie `53/tcp` wykryto usługę `dnsmasq 2.89`, która jest często wykorzystywana w routerach do obsługi lokalnego DNS.



Porty `139/tcp` oraz `445/tcp` zostały rozpoznane jako usługi Samba/SMB. Oznacza to, że router może udostępniać zasoby w sieci lokalnej. Tego typu usługi powinny być włączone tylko wtedy, gdy są rzeczywiście potrzebne.



Nmap nie rozpoznał jednoznacznie usług na portach `80/tcp`, `443/tcp` i `631/tcp`, dlatego oznaczył je znakami zapytania. Nie jest to błąd. W przypadku routerów domowych panele administracyjne często zwracają niestandardowe odpowiedzi, których Nmap nie potrafi dokładnie przypisać do konkretnej wersji usługi.



\## 6. Ocena ryzyka



| Element                          | Ocena          | Uzasadnienie                                               |

| -------------------------------- | -------------- | ---------------------------------------------------------- |

| DNS na porcie 53                 | Niskie         | Typowa usługa routera w sieci lokalnej                     |

| HTTP na porcie 80                | Średnie        | Panel administracyjny przez nieszyfrowane HTTP             |

| HTTPS na porcie 443              | Niskie         | Szyfrowany dostęp do panelu administracyjnego              |

| SMB/NetBIOS na portach 139 i 445 | Średnie        | Usługi udostępniania zasobów zwiększają powierzchnię ataku |

| IPP na porcie 631                | Niskie/średnie | Usługa drukowania może być zbędna, jeśli nie jest używana  |



\## 7. Rekomendacje



Na podstawie przeprowadzonej analizy zaleca się:



1\. korzystanie z panelu routera przez HTTPS zamiast HTTP,

2\. sprawdzenie, czy funkcje udostępniania plików, USB lub dysku sieciowego są potrzebne,

3\. wyłączenie usług SMB/Samba, jeśli nie są używane,

4\. sprawdzenie, czy funkcja drukowania sieciowego jest potrzebna,

5\. zmianę domyślnego hasła administratora routera,

6\. regularną aktualizację oprogramowania routera,

7\. okresowe wykonywanie skanów kontrolnych sieci lokalnej.



\## 8. Wnioski częściowe



Dotychczasowa analiza wykazała, że router udostępnia kilka usług w sieci lokalnej. Większość z nich może być typowa dla routera domowego, jednak usługi SMB/Samba oraz IPP powinny zostać sprawdzone pod kątem rzeczywistej potrzeby ich działania.



Na tym etapie nie stwierdzono jednoznacznej podatności, ale wskazano elementy zwiększające powierzchnię ataku. Dalsza analiza powinna objąć skanowanie komputera użytkownika oraz porównanie liczby otwartych usług między routerem a innymi urządzeniami w sieci.



\## 9. Analiza komputera użytkownika



Kolejnym etapem było wykonanie podstawowego skanowania komputera użytkownika znajdującego się pod adresem `192.168.1.21`. Host został rozpoznany jako `arshavin-1.home`.



Użyta komenda:



```bash

nmap 192.168.1.21 -oN scans\\04-pc-basic-scan.txt

```



Wynik skanowania:



```text

PORT     STATE  SERVICE

135/tcp  open   msrpc

139/tcp  open   netbios-ssn

445/tcp  open   microsoft-ds

```



\### Analiza



Skanowanie wykazało, że na komputerze użytkownika otwarte są trzy porty TCP: `135`, `139` oraz `445`. Są to porty typowe dla systemów Windows i są związane głównie z usługami RPC, NetBIOS oraz SMB.



Port `135/tcp` jest powiązany z usługą MSRPC, wykorzystywaną przez system Windows do komunikacji między procesami i usługami sieciowymi. Porty `139/tcp` oraz `445/tcp` są związane z mechanizmami udostępniania plików i drukarek w sieci lokalnej.



Największej uwagi wymaga port `445/tcp`, ponieważ wskazuje na dostępność usługi SMB. Sama obecność tego portu w sieci lokalnej nie oznacza podatności, jednak usługa SMB powinna być dostępna tylko w zaufanych sieciach. Jeżeli użytkownik nie korzysta z udostępniania plików lub drukarek, warto rozważyć wyłączenie tej funkcji albo ograniczenie jej za pomocą zapory systemu Windows.



Wynik `997 closed ports` oznacza, że większość sprawdzanych portów była zamknięta. Jest to pozytywna informacja, ponieważ komputer nie udostępnia dużej liczby usług w sieci lokalnej.



\### Ocena ryzyka



Ryzyko oceniono jako niskie do średniego. Otwarte porty są typowe dla systemu Windows, jednak usługi SMB/NetBIOS zwiększają powierzchnię ataku, szczególnie w przypadku pracy w niezaufanych sieciach.



\### Rekomendacje



Zaleca się:



1\. sprawdzenie, czy udostępnianie plików i drukarek jest potrzebne,

2\. wyłączenie udostępniania, jeśli nie jest używane,

3\. pozostawienie włączonej zapory systemu Windows,

4\. ustawienie sieci domowej jako prywatnej tylko wtedy, gdy jest to zaufana sieć,

5\. unikanie udostępniania zasobów w sieciach publicznych,

6\. regularne aktualizowanie systemu Windows.



\### Wniosek



Komputer użytkownika udostępnia podstawowe usługi charakterystyczne dla systemu Windows. Nie wykryto dużej liczby otwartych portów, co jest korzystne z punktu widzenia bezpieczeństwa. Głównym elementem wymagającym kontroli jest usługa SMB dostępna na porcie `445/tcp`.



\## 10. Identyfikacja usług komputera użytkownika



Po wykonaniu podstawowego skanowania komputera użytkownika przeprowadzono dodatkowe skanowanie z wykrywaniem wersji usług.



Użyta komenda:



```bash

nmap -sV 192.168.1.21 -oN scans\\05-pc-service-version.txt

```



Wynik skanowania:



```text

PORT     STATE  SERVICE       VERSION

135/tcp  open   msrpc         Microsoft Windows RPC

139/tcp  open   netbios-ssn   Microsoft Windows netbios-ssn

445/tcp  open   microsoft-ds?

Service Info: OS: Windows

```



\### Analiza



Skanowanie z użyciem opcji `-sV` potwierdziło, że na komputerze użytkownika działają usługi charakterystyczne dla systemu Windows. Na porcie `135/tcp` wykryto usługę Microsoft Windows RPC, która jest używana przez system Windows do komunikacji pomiędzy usługami.



Port `139/tcp` wskazuje na działanie usługi NetBIOS Session Service, związanej ze starszymi mechanizmami komunikacji i udostępniania zasobów w sieci lokalnej. Port `445/tcp` wskazuje na usługę Microsoft-DS/SMB, która odpowiada za udostępnianie plików i drukarek w środowisku Windows.



Wynik `Service Info: OS: Windows` oznacza, że Nmap rozpoznał analizowane urządzenie jako system Windows.



\### Ocena bezpieczeństwa



Wykryte porty są typowe dla komputera z systemem Windows działającego w sieci lokalnej. Nie oznaczają one automatycznie podatności ani infekcji. Jednocześnie usługi RPC, NetBIOS oraz SMB zwiększają powierzchnię ataku, dlatego powinny być dostępne wyłącznie w zaufanej sieci lokalnej.



Największej uwagi wymaga port `445/tcp`, ponieważ usługa SMB była w przeszłości częstym celem ataków. Jeżeli użytkownik nie korzysta z udostępniania plików i drukarek, warto rozważyć ograniczenie lub wyłączenie tej funkcji.



\### Rekomendacje



Zaleca się:



1\. pozostawienie włączonej Zapory systemu Windows,

2\. wyłączenie udostępniania plików i drukarek, jeśli nie jest używane,

3\. korzystanie z profilu sieci prywatnej tylko w zaufanej sieci domowej,

4\. ustawianie sieci publicznej w przypadku korzystania z Wi-Fi poza domem,

5\. regularne aktualizowanie systemu Windows,

6\. sprawdzenie, czy nie są udostępnione niepotrzebne foldery.



\### Wniosek



Komputer użytkownika udostępnia podstawowe usługi systemu Windows w sieci lokalnej. Liczba otwartych portów jest niewielka, co jest pozytywne z punktu widzenia bezpieczeństwa. Głównym elementem wymagającym kontroli jest usługa SMB dostępna na porcie `445/tcp`.



\## 11. Porównanie wyników routera i komputera użytkownika



W ramach analizy porównano dwa najważniejsze hosty wykryte w sieci lokalnej:



```text id="yu2ayu"

192.168.1.1   funbox.home       router

192.168.1.21  arshavin-1.home   komputer użytkownika

```



\### Porównanie otwartych portów



| Host                    |              Otwarte porty | Charakter usług                            |

| ----------------------- | -------------------------: | ------------------------------------------ |

| Router `192.168.1.1`    | 53, 80, 139, 443, 445, 631 | DNS, panel administracyjny, SMB/Samba, IPP |

| Komputer `192.168.1.21` |              135, 139, 445 | RPC, NetBIOS, SMB                          |



\### Analiza porównawcza



Router posiada większą liczbę otwartych usług niż komputer użytkownika. Jest to częściowo uzasadnione jego rolą w sieci, ponieważ router odpowiada za komunikację urządzeń lokalnych, obsługę DNS oraz dostęp do panelu administracyjnego.



Na routerze szczególną uwagę zwracają porty `139/tcp` oraz `445/tcp`, które wskazują na usługi Samba/SMB. Mogą one być związane z udostępnianiem zasobów, na przykład dysku USB, plików lub innych funkcji routera. Jeżeli te funkcje nie są używane, powinny zostać wyłączone.



Komputer użytkownika posiada mniej otwartych portów. Wykryte usługi są typowe dla systemu Windows i obejmują RPC, NetBIOS oraz SMB. Najważniejszym elementem do kontroli jest port `445/tcp`, ponieważ odpowiada za udostępnianie plików i drukarek.



\### Ocena bezpieczeństwa



Z punktu widzenia bezpieczeństwa pozytywną informacją jest to, że komputer użytkownika nie udostępnia dużej liczby usług. Otwartych było tylko kilka portów charakterystycznych dla systemu Windows.



Router posiada więcej usług, co zwiększa jego powierzchnię ataku. Jest to szczególnie istotne, ponieważ router jest centralnym punktem sieci lokalnej. Nie oznacza to automatycznie podatności, ale wymaga kontroli konfiguracji.



\### Najważniejsze ryzyka



Najważniejsze potencjalne ryzyka zidentyfikowane w analizie to:



1\. dostępność usług SMB/Samba na routerze,

2\. dostępność SMB na komputerze użytkownika,

3\. możliwość korzystania z panelu routera przez HTTP,

4\. obecność usługi IPP, jeśli drukowanie sieciowe nie jest używane.



\### Rekomendacje



Na podstawie porównania zaleca się:



1\. sprawdzić w panelu routera, czy funkcje udostępniania plików, USB lub drukarki są aktywne,

2\. wyłączyć SMB/Samba na routerze, jeśli nie jest potrzebne,

3\. korzystać z panelu routera przez HTTPS zamiast HTTP,

4\. sprawdzić w systemie Windows ustawienia udostępniania plików i drukarek,

5\. pozostawić włączoną zaporę systemu Windows,

6\. nie udostępniać zasobów w sieciach publicznych,

7\. regularnie aktualizować router oraz system Windows.



\### Wniosek



Porównanie wykazało, że router posiada większą powierzchnię ataku niż komputer użytkownika. Jest to częściowo naturalne ze względu na rolę routera w sieci, jednak część usług może być zbędna. Najważniejsze elementy wymagające kontroli to usługi SMB/Samba oraz ewentualne funkcje drukowania lub udostępniania plików.



\# Analiza sieci lokalnej z wykorzystaniem Nmap



Projekt przedstawia podstawową analizę bezpieczeństwa sieci lokalnej z użyciem narzędzia Nmap. Celem było wykrycie aktywnych hostów, sprawdzenie otwartych portów, identyfikacja usług oraz przygotowanie raportu z rekomendacjami bezpieczeństwa.



\## Cel projektu



Celem projektu było pokazanie praktycznego procesu analizy sieci lokalnej:



\* wykrywanie aktywnych urządzeń,

\* skanowanie otwartych portów,

\* identyfikacja usług,

\* interpretacja wyników,

\* ocena ryzyka,

\* przygotowanie rekomendacji bezpieczeństwa.



\## Użyte narzędzia



\* Nmap

\* Windows CMD

\* Markdown

\* GitHub



\## Zakres analizy



Analiza została wykonana w prywatnej sieci lokalnej `192.168.1.0/24`.



Badaniu poddano między innymi:



\* router domowy,

\* komputer użytkownika,

\* inne aktywne urządzenia wykryte w sieci.



\## Struktura projektu



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

```



\## Przykładowe komendy



Wykrywanie aktywnych hostów:



```bash

nmap -sn 192.168.1.0/24 -oN scans/01-host-discovery.txt

```



Podstawowe skanowanie routera:



```bash

nmap 192.168.1.1 -oN scans/02-router-basic-scan.txt

```



Wykrywanie wersji usług routera:



```bash

nmap -sV 192.168.1.1 -oN scans/03-router-service-version.txt

```



Podstawowe skanowanie komputera użytkownika:



```bash

nmap 192.168.1.21 -oN scans/04-pc-basic-scan.txt

```



Wykrywanie wersji usług komputera użytkownika:



```bash

nmap -sV 192.168.1.21 -oN scans/05-pc-service-version.txt

```



\## Najważniejsze wyniki



W sieci lokalnej wykryto 8 aktywnych hostów. Następnie dokładniejszej analizie poddano router oraz komputer użytkownika.



Router posiadał otwarte porty związane z usługami DNS, HTTP, HTTPS, SMB/Samba oraz IPP. Komputer użytkownika posiadał otwarte porty typowe dla systemu Windows: RPC, NetBIOS oraz SMB.



\## Wnioski



Analiza wykazała, że router posiada większą powierzchnię ataku niż komputer użytkownika, co wynika z jego roli w sieci lokalnej. Szczególnej uwagi wymagają usługi SMB/Samba oraz IPP, ponieważ mogą być zbędne, jeśli użytkownik nie korzysta z udostępniania plików, USB lub drukarki.



Projekt pokazuje praktyczne użycie Nmapa w podstawowej analizie bezpieczeństwa sieci lokalnej oraz umiejętność interpretacji wyników skanowania.



\## Rekomendacje



Na podstawie analizy zalecono:



\* wyłączenie nieużywanych usług na routerze,

\* korzystanie z panelu administracyjnego przez HTTPS,

\* sprawdzenie ustawień udostępniania plików i drukarek,

\* pozostawienie włączonej zapory systemu Windows,

\* regularną aktualizację routera i systemu operacyjnego,

\* okresowe powtarzanie skanowania kontrolnego.



\## Zastrzeżenie



Projekt został wykonany wyłącznie w prywatnej sieci lokalnej w celach edukacyjnych. Skanowanie cudzych sieci lub systemów bez zgody właściciela jest niedozwolone.







