# 📉 Plan redukcji churnu: Telenova Polska

> [!NOTE]
> Firma, ludzie i dane w tym projekcie są **fikcyjne** (dane syntetyczne). Scenariusz jest wzorowany na dużych operatorach mobilnych i służy jako case study platformy danych, raportowania i agentów AI.

| | |
|---|---|
| **Program** | Redukcja churnu z 2,6% do ok. 2,0% w 3 miesiące |
| **Lider programu** | **Weronika, Team Leader** |
| **Sponsor** | Dyrektor ds. klienta |
| **Zespół** | 6–7 osób w rdzeniu + 8 konsultantów w pilotażu |
| **Platformy** | **AWS**: zbieranie i porządkowanie danych · **GCP**: analityka, modele, agent, raporty |
| **Dane** | Syntetyczne (symulacja skali) |

## Spis treści

1. [Sytuacja](#1-sytuacja)
2. [Cel i miary sukcesu](#2-cel-i-miary-sukcesu)
3. [Strategia](#3-strategia)
4. [Co się zmieni](#4-co-się-zmieni)
5. [Dane i porządkowanie](#5-dane-i-porządkowanie)
6. [Churn: typy, źródła i diagnoza](#6-churn-typy-źródła-i-diagnoza)
7. [Modele: churn i rekomendacje ofert](#7-modele-churn-i-rekomendacje-ofert)
8. [Notatki konsultantów i analiza LLM](#8-notatki-konsultantów-i-analiza-llm)
9. [Agent AI](#9-agent-ai)
10. [Dzień pracy konsultanta](#10-dzień-pracy-konsultanta)
11. [Raporty](#11-raporty)
12. [Zespół i odpowiedzialności](#12-zespół-i-odpowiedzialności)
13. [Plan w krokach](#13-plan-w-krokach)
14. [Ryzyka](#14-ryzyka)
15. [Jak raportuje lider](#15-jak-raportuje-lider)
16. [Dokumenty w repozytorium](#16-dokumenty-w-repozytorium)

---

## 1. Sytuacja

Telenova Polska to operator mobilny. Przez ponad rok miesięczny churn wynosił **1,8%**. Dwa zdarzenia to zmieniły:

- 📈 **Styczeń:** podwyżka cen abonamentów średnio o 8%.
- 📡 **Luty:** 5-dniowa awaria sieci w regionie południowo-wschodnim.

Do kwietnia churn wzrósł do **2,6%**.

| Fakt | Wartość |
|---|---|
| Klienci abonamentowi | 500 000 |
| ARPU | 60 zł / miesiąc |
| Churn przed kryzysem | 1,8% (ok. 9 000 odejść miesięcznie) |
| Churn w kwietniu | 2,6% (ok. 13 000 odejść miesięcznie) |
| Dodatkowe odejścia | ok. 4 000 miesięcznie |
| Utracony przychód | ok. 2,9 mln zł rocznie za każdy miesiąc z takim wzrostem (4 000 × 60 zł × 12) |
| Dział retencji | 24 konsultantów w 3 zespołach po 8 osób |

### Dlaczego firma słabo reaguje

- Konsultant przygotowuje się do jednej rozmowy ok. **8 minut**: sprawdza CRM, billing (Oracle), system zgłoszeń i dane o jakości sieci.
- Oferty są rozproszone w kilku dokumentach i dobierane „na oko". Część rabatów ma ujemną marżę.
- Akceptacja ofert retencyjnych to ok. **15%**.
- Notatki z rozmów zostają w różnych miejscach i nikt ich nie analizuje.
- Nikt nie wie z góry, **kogo ratować w pierwszej kolejności**. Lista odejść powstaje po fakcie.

---

## 2. Cel i miary sukcesu

> [!IMPORTANT]
> **Cel główny (3 miesiące od startu):** sprowadzić churn z 2,6% do ok. **2,0%**, nie obniżając marży ofert retencyjnych.

| Obszar | Miara | Dziś | Cel | Odpowiada |
|---|---|---|---|---|
| 💼 Biznes | Churn miesięczny | 2,6% | ok. 2,0% | Sponsor |
| 💼 Biznes | Akceptacja oferty retencyjnej | ok. 15% | powyżej 25% | Sponsor |
| 💼 Biznes | Oferty z dodatnią marżą po rabacie | nieznane | 100% | Sponsor |
| ⚙️ Operacje | Czas przygotowania do rozmowy | ok. 8 min | ok. 3 min | Weronika |
| ⚙️ Operacje | Zadania retencyjne zamknięte w 48 h | brak pomiaru | co najmniej 90% | Weronika |
| ⚙️ Operacje | Adopcja agenta (rozmowy z jego użyciem) | 0% | co najmniej 80% w pilotażu | Weronika |
| ⚙️ Operacje | Rozmowy z uzupełnioną notatką | brak pomiaru | co najmniej 95% | Weronika |
| 🗄️ Dane | Reguły jakości danych zaliczone | brak pomiaru | co najmniej 98% | Inżynier danych |
| 🗄️ Dane | Dane gotowe do 7:00 | brak pomiaru | co najmniej 99% dni | Inżynier danych |
| 📊 Modele | Trafność modelu churnu (recall@10%) | brak | co najmniej 40% (lift co najmniej 4) | Data Scientist |
| 📊 Modele | Akceptacja oferty z rekomendacji vs wybranej ręcznie | brak | o co najmniej 5 p.p. wyższa | Data Scientist |
| 🤖 AI | Zgodność odpowiedzi agenta z polityką | brak | co najmniej 95% | Tester |
| 🤖 AI | Zgodność klasyfikacji powodu przez LLM z oceną człowieka | brak | co najmniej 85% | Inżynier AI |
| 🤖 AI | Koszt jednej odpowiedzi agenta | brak | do 0,10 zł | DevOps |

Progi są założeniami. Zweryfikuję je w pilotażu.

**Zasada pomiaru:** efekt zawsze porównuję z grupą kontrolną, żeby nie przypisać agentowi tego, co zrobił sam rynek.

---

## 3. Strategia

Pięć ruchów. Każdy ma właściciela, miarę i raport.

| # | Ruch | Efekt | Raporty | Odpowiada |
|---|---|---|---|---|
| 1 | **Uporządkować dane** | Jedno źródło prawdy, zebrane na AWS, analizowane na GCP | R10 | Inżynier danych |
| 2 | **Zrozumieć churn** | Wiemy, ile jest odejść każdego typu i skąd się biorą | R1, R2, R3, R4 | Analityk biznesowy |
| 3 | **Przewidywać i rekomendować** | Wiemy, kogo ratować (model churnu) i co mu zaproponować (model rekomendacji ofert) | R5, R6 | Data Scientist |
| 4 | **Wesprzeć konsultanta i uczyć się z rozmów** | Agent daje rekomendację w minutę, a notatki z rozmów są analizowane przez LLM | R8 | Inżynier AI |
| 5 | **Mierzyć i raportować codziennie** | System 10 raportów: od zarządu po jakość danych | R1–R10 | Analityk BI, Weronika |

### Pętla, która napędza program

```
raport  →  decyzja  →  zmiana (oferta, proces, model)  →  pomiar efektu  →  raport
```

Przykład: raport z analizy notatek pokazuje, że klienci odchodzą przez nieczytelne faktury. Decyzja: zmiana wzoru faktury i nowy argument w ofercie. Pomiar: spadek odejść z tej przyczyny w kolejnym tygodniu.

---

## 4. Co się zmieni

| Obszar | Dziś | Po wdrożeniu |
|---|---|---|
| Dane | Oracle, pliki CSV i zgłoszenia w osobnych miejscach | Jedno źródło prawdy z kontrolą jakości |
| Wiedza o churnie | „Churn wzrósł" | Podział na typy i źródła, ranking przyczyn, wpływ awarii i cennika |
| Przygotowanie do rozmowy | 8 min, cztery systemy | ok. 3 min, jedno miejsce (agent) |
| Wybór klientów do kontaktu | Reakcja na zgłoszenie rezygnacji | Codzienna lista klientów o najwyższym ryzyku |
| Dobór oferty | Kilka dokumentów, „na oko" | Model rekomendacji wskazuje 3 oferty z uzasadnieniem |
| Rabaty | Brak kontroli marży | Agent liczy marżę, rabat powyżej progu wymaga zatwierdzenia |
| Notatki z rozmów | Rozproszone, nieanalizowane | Jedna baza, automatyczna analiza LLM, raport powodów odejść |
| Zadania po rozmowie | Notatki w różnych miejscach | Zadanie tworzy się automatycznie z terminem 48 h |
| Raportowanie | Ręczne arkusze, raz w miesiącu | 10 raportów odświeżanych codziennie lub tygodniowo |

---

## 5. Dane i porządkowanie

### 5.1 Źródła danych

| Źródło | Co zawiera | Gdzie leży dziś | Do czego służy |
|---|---|---|---|
| Baza klientów i rozliczeń | Klienci, karty SIM, taryfy, faktury, płatności | Oracle (legacy) | Cechy modeli, historia klienta |
| Zgłoszenia | Kontakty z call center, tematy, czas rozwiązania | System zgłoszeń | Cechy modeli, diagnoza przyczyn |
| Jakość sieci | Awarie, wskaźniki jakości według regionu i komórki | Pliki CSV | Wpływ awarii na churn |
| Odejścia | Data, typ odejścia, port-out (dokąd), powód formalny | Oracle | Etykieta churnu, typy churnu |
| Notatki konsultantów | Wynik rozmowy, powód, notatka tekstowa | Nowa tabela w bazie | Analiza LLM, cechy modeli |
| Historia ofert | Oferta, rabat, wynik (przyjęta / odrzucona) | Zadania retencyjne | Model rekomendacji, skuteczność |
| Katalog ofert i polityka | Oferty, warunki, koszty, progi rabatów | Dokumenty | Baza wiedzy agenta, kalkulator marży |
| Oferty konkurencji | Cenniki i promocje konkurentów | Tabela prowadzona ręcznie | Analiza przyczyny „oferta konkurencji" |
| Transkrypcje rozmów (opcjonalnie) | Zapis rozmów retencyjnych | Systemy call center | Dodatkowa analiza LLM |

### 5.2 Architektura: AWS zbiera i porządkuje, GCP analizuje

```
ŹRÓDŁA                      AWS (dane)                                       GCP (analityka i AI)
------                      ----------                                       --------------------
Oracle (legacy) --DMS-->+
Pliki CSV (sieć) ------>+--> S3 raw --Glue--> S3 clean --Glue--> S3 curated --transfer--> BigQuery
Zgłoszenia ------------>+                                                                   |
Notatki konsultantów -->+                                                                   +--> Model churnu
                                                                                            +--> Model rekomendacji ofert
Zarządzanie: Lake Formation, Glue Catalog, KMS, CloudWatch                                 +--> Analiza notatek (LLM)
                                                                                            +--> Agent AI (Cloud Run)
                                                                                            +--> Raporty (Looker Studio)
```

**Dlaczego dwie chmury:** w tej historii Telenova ma już na AWS magazyn danych, a zespół analityczno-AI pracuje na GCP. Zamiast przenosić wszystko, wyznaczamy jedną, jasną granicę.

> [!IMPORTANT]
> **Granica:** AWS odpowiada za dane od źródła do warstwy *curated*. GCP odpowiada za wszystko, co z danymi robimy: analizy, modele, agent i raporty. Dane płyną w jedną stronę (AWS → GCP).

### 5.3 Kto za co odpowiada

| Zadanie | AWS | GCP |
|---|---|---|
| Replikacja Oracle (pełne ładowanie i zmiany na bieżąco) | AWS DMS | |
| Magazyn danych (raw, clean, curated) | S3 (format Parquet) | |
| Porządkowanie i transformacje | Glue (ETL), Glue Data Catalog | |
| Zapytania ad hoc do danych | Athena | |
| Uprawnienia do danych | Lake Formation | |
| Szyfrowanie i sekrety | KMS, Secrets Manager | Secret Manager |
| Harmonogramy i monitoring | Step Functions / EventBridge, CloudWatch | Cloud Monitoring |
| Przeniesienie curated do analityki | | BigQuery Data Transfer Service (z S3) lub eksport Parquet |
| Hurtownia analityczna | | BigQuery |
| Modele (churn, rekomendacje) | | BigQuery ML, Vertex AI |
| Analiza notatek (LLM) i agent | | Gemini (Vertex AI), Cloud Run |
| Raporty | | Looker Studio |

Nazwy i dostępność usług sprawdź w aktualnej dokumentacji AWS i Google Cloud, w tym koszty transferu między chmurami. Wszystkie zasoby w regionach UE.

### 5.4 Warstwy danych

| Warstwa | Co zawiera | Kto z niej korzysta |
|---|---|---|
| **Raw** (surowa) | Dane dokładnie takie jak w źródle, bez zmian, z datą załadunku | Tylko inżynier danych |
| **Clean** (oczyszczona) | Ujednolicone typy i nazwy, bez duplikatów, z zamaskowanymi danymi wrażliwymi | Inżynier danych, analityk |
| **Curated** (gotowa) | Tabela klienta i tabele zdarzeń gotowe do analizy i modeli | Analityk, Data Scientist, BI |

### 5.5 Porządkowanie danych: 8 kroków

| # | Krok | Co dokładnie | Odpowiada |
|---|---|---|---|
| 1 | Inwentaryzacja | Lista źródeł, właściciel każdego zbioru, słownik danych | Analityk biznesowy |
| 2 | Klucze | Ustalenie identyfikatorów (klient, SIM, numer) i zasad łączenia zbiorów | Inżynier danych |
| 3 | Standaryzacja | Typy, formaty dat i stref czasowych, kodowanie znaków, nazwy kolumn, waluty | Inżynier danych |
| 4 | Duplikaty i braki | Reguły usuwania duplikatów i uzupełniania lub oznaczania braków | Inżynier danych |
| 5 | Kontrola jakości | Liczba wierszy źródło vs cel, unikalność kluczy, zakresy wartości, spójność relacji | Inżynier danych, Tester |
| 6 | Maskowanie | Hashowanie numerów, e-maili i adresów przed warstwą clean | Specjalista ds. bezpieczeństwa |
| 7 | Modelowanie | Tabela klienta (jeden wiersz na klienta i dzień) oraz tabele zdarzeń: faktury, zgłoszenia, awarie, oferty, notatki | Inżynier danych |
| 8 | Dokumentacja | Opis tabel, pochodzenie danych, właściciele, zasady odświeżania | Inżynier danych, Weronika (zatwierdza) |

### 5.6 Zasady

- Każde ładowanie ma kontrolę jakości i zapisuje wynik w raporcie R10.
- Dane wrażliwe są maskowane, a dostęp zależy od roli i regionu.
- Retencja: dane osobowe przechowujemy tylko tak długo, jak wymaga cel analizy.
- Budżety i alerty kosztów w obu chmurach od pierwszego dnia.
- W projekcie portfolio wszystkie dane są **syntetyczne**, a skalę produkcyjną (dziesiątki TB) szacuję na podstawie testów przepustowości, nie deklaruję jej jako wykonanej.

---

## 6. Churn: typy, źródła i diagnoza

### 6.1 Typy churnu (co rozumiemy przez „odejście")

| Typ | Opis | Jak rozpoznajemy | Co robimy |
|---|---|---|---|
| **Dobrowolny, port-out** | Klient przenosi numer do konkurenta | Zlecenie przeniesienia numeru | Retencja przed przeniesieniem, analiza konkurenta |
| **Dobrowolny, rezygnacja** | Klient rezygnuje bez przeniesienia numeru | Wypowiedzenie umowy | Retencja, analiza powodu |
| **Niedobrowolny, płatności** | Rozwiązanie umowy z powodu zaległości | Zaległości powyżej progu, rozwiązanie przez operatora | Wczesne przypomnienia, plany spłat |
| **Niedobrowolny, nadużycia** | Rozwiązanie z powodu nadużyć | Decyzja operatora | Poza zakresem retencji, tylko monitoring |
| **Cichy** | Klient przestaje używać usług, ale nadal płaci | Brak użycia przez 30 dni lub więcej | Kontakt proaktywny, zanim odejdzie |
| **Wczesny** | Odejście do 90 dni od aktywacji | Staż poniżej 90 dni | Analiza sprzedaży i pierwszego doświadczenia |
| **Koniec umowy** | Brak przedłużenia po okresie zobowiązania | Zbliżający się koniec umowy | Oferta przedłużenia z wyprzedzeniem |
| **Miękki** | Obniżenie taryfy (downgrade) | Zmiana na tańszą taryfę | Oferta utrzymania wartości |

**Metryka:** churn ogółem rozkładam na typy w raporcie R2. Retencja dotyczy przede wszystkim typów dobrowolnych, cichych i końca umowy.

### 6.2 Źródła churnu (dlaczego odchodzą)

| Przyczyna | Przykłady | Jak wykryjemy w danych |
|---|---|---|
| **Cena i podwyżka** | Wyższy rachunek po zmianie cennika | Zmiana rachunku, taryfa, notatki |
| **Oferta konkurencji** | Tańsza promocja u konkurenta | Port-out, notatki, tabela konkurencji |
| **Jakość sieci i awarie** | Przerwy, wolny internet | Awarie w regionie, wskaźniki jakości, zgłoszenia |
| **Obsługa klienta** | Długie rozwiązywanie zgłoszeń, brak odpowiedzi | Liczba zgłoszeń, czas rozwiązania, powtórne kontakty |
| **Rozliczenia** | Błędne lub niezrozumiałe faktury, korekty | Korekty faktur, zgłoszenia o fakturach |
| **Zasięg i miejsce** | Przeprowadzka, słaby zasięg w domu | Zmiana adresu, jakość w lokalizacji, notatki |
| **Dopasowanie oferty** | Źle dobrana taryfa, niewykorzystane usługi | Niskie użycie, przekroczenia pakietu |
| **Doświadczenie sprzedaży** | Obietnice niezgodne z umową | Wczesny churn, notatki |
| **Sytuacja klienta** | Problemy finansowe, wyjazd, zmiana pracy | Zaległości, notatki |
| **Urządzenie i usługi** | Problem z telefonem, brak potrzebnej usługi | Zgłoszenia, notatki |

### 6.3 Jak badamy churn (diagnoza)

**Pytanie:** dlaczego churn wzrósł z 1,8% do 2,6%?

1. Liczę churn w czasie i dzielę go według typu, regionu, taryfy, stażu i kanału sprzedaży.
2. Porównuję **kohorty**: klienci z regionu awarii vs reszta, starsze taryfy (większa podwyżka) vs nowsze.
3. Porównuję okresy przed i po zdarzeniu z grupą porównawczą, żeby oddzielić wpływ awarii od wpływu podwyżki.
4. Rozkładam wzrost churnu na przyczyny z 6.2 i sprawdzam, które rosną najszybciej.
5. Łączę dane z **analizą notatek konsultantów** (sekcja 8), żeby zobaczyć powody, których nie widać w liczbach.
6. Robię analizę 5 Why dla głównych przyczyn.
7. Sprawdzam istotność statystyczną wyników.

**Wynik:** notatka „Diagnoza" na 1–2 strony z rankingiem przyczyn, rozkładem wzrostu churnu na przyczyny i rekomendacją działań. Prezentuję ją sponsorowi i zapisuję decyzje.

---

## 7. Modele: churn i rekomendacje ofert

### 7.1 Model churnu: kogo ratować

**Co przewidujemy:** prawdopodobieństwo odejścia w ciągu **30 dni**. Wynik liczony **raz dziennie**, gotowy do 7:00.

**Cechy:**
- trend rachunków i zmiana ceny abonamentu,
- liczba zgłoszeń w ostatnich 90 dniach i czas ich rozwiązania,
- awarie i jakość sieci w regionie klienta,
- spadek użycia danych i połączeń,
- zaległości w płatnościach,
- staż, koniec umowy, typ taryfy,
- wcześniejsze kontakty retencyjne i ich wynik,
- powód i sentyment z notatek (z analizy LLM).

| Etap | Model | Po co |
|---|---|---|
| 1 | Regresja logistyczna | Prosty punkt odniesienia |
| 2 | Drzewa gradientowe (np. XGBoost) | Docelowy model dla danych tabelarycznych |

**Ocena:** podział danych po czasie, metryki recall@10%, lift i PR-AUC, kalibracja prawdopodobieństw. Dla każdego klienta podaję trzy główne przyczyny ryzyka (np. SHAP).

**Wynik dla konsultanta:** poziom ryzyka (wysoki / średni / niski) + trzy przyczyny. Top 10% klientów trafia na codzienną listę priorytetową.

**Utrzymanie:** co tydzień sprawdzam drift, co miesiąc ponownie trenuję model.

### 7.2 Model rekomendacji ofert: co klientowi zaproponować

**Cel:** dla klienta zagrożonego wskazać **3 oferty** o najwyższej oczekiwanej wartości i uzasadnić wybór.

**Jak działa (4 kroki):**

1. **Filtr uprawnień (reguły).** Zostają tylko oferty dostępne dla taryfy, regionu i stażu klienta, w granicach limitów rabatu.
2. **Model prawdopodobieństwa akceptacji.** Dla każdej pary klient–oferta model (drzewa gradientowe) liczy szansę, że klient ofertę przyjmie. Uczy się na historii ofert i ich wyników.
3. **Ranking po wartości.** Wartość oczekiwana = szansa akceptacji × marża po rabacie przez 12 miesięcy.
4. **Uzasadnienie.** Top 3 oferty z jednozdaniowym powodem, np. „klient odchodzi przez cenę i ma 24 miesiące stażu: rabat na abonament na 12 miesięcy".

**Dane:** historia ofert i wyników, cechy klienta, główna przyczyna ryzyka (z modelu churnu i notatek), taryfa i ARPU, koszty i marże ofert z katalogu.

**Etap 2 (po pilotażu):** modelowanie *uplift*, czyli kto zostaje **dzięki** ofercie, a kto zostałby i bez niej. Wymaga grupy kontrolnej, którą zbieramy w pilotażu.

**Ocena:**
- czy zaakceptowana oferta była w top 3 rekomendacji,
- akceptacja oferty rekomendowanej vs wybranej ręcznie (cel: o co najmniej 5 p.p. wyższa),
- marża po rabacie,
- test porównawczy w pilotażu.

**Raport:** R6 (rekomendacje ofert).

---

## 8. Notatki konsultantów i analiza LLM

### 8.1 Przepływ

```
rozmowa → krótki formularz po rozmowie → zapis w bazie → maskowanie danych osobowych
        → analiza LLM → tabela wyników → raporty, cechy modeli, poprawki ofert
```

**Formularz po rozmowie** (ok. 1 minuta): wynik (przyjął / odrzucił / odroczył), przedstawiona oferta, powód wskazany przez klienta (lista z 6.2) i krótka notatka (2–5 zdań). Agent może przygotować szkic notatki, który konsultant zatwierdza.

### 8.2 Co LLM wyciąga z notatki

| Pole | Przykład |
|---|---|
| Kategoria powodu (z listy źródeł churnu) | Cena i podwyżka |
| Podkategoria | Rachunek wyższy niż zapowiedziano |
| Sentyment (od −2 do +2) | −1 |
| Wspomniany konkurent | Tak, nazwa konkurenta |
| Argument konsultanta | Rabat na 12 miesięcy |
| Reakcja klienta | Rozważa, prosi o czas |
| Flaga ryzyka | Obietnica spoza polityki (tak / nie) |
| Pilność | Wysoka / średnia / niska |
| Podsumowanie | Jedno zdanie |

### 8.3 Zasady jakości

- Model zwraca wynik w ustalonym formacie (lista kategorii), a prompt ma numer wersji.
- Przed uruchomieniem sprawdzam go na **100 ręcznie ocenionych notatkach** (cel: zgodność co najmniej 85%).
- Co tydzień analityk ręcznie sprawdza **5%** notatek, a wyniki o niskiej pewności trafiają do przeglądu.
- Do modelu nie trafiają dane osobowe (maskowanie przed analizą).
- Analizę wykorzystuję do **coachingu i poprawy ofert**, nie do karania konsultantów.

### 8.4 Co z tego wynika

- Tygodniowy przegląd powodów odejść (raport R8).
- Aktualizacja katalogu ofert i argumentów.
- Nowe cechy dla modeli (powód, sentyment).
- Wykrywanie obietnic spoza polityki zanim staną się reklamacją.

---

## 9. Agent AI

**Cel:** skrócić przygotowanie do rozmowy z 8 do ok. 3 minut i ujednolicić oferty.

**Kto korzysta:** konsultant retencji (przed rozmową i w jej trakcie) oraz team leader (poranne podsumowanie, zatwierdzanie rabatów).

### Jak działa

Konsultant pyta: *„Klient 48213 chce zrezygnować. Co mu zaproponować?"*

Agent:

1. pobiera ryzyko klienta i przyczyny z modelu churnu,
2. pobiera historię klienta (zgłoszenia, faktury, użycie, awarie w regionie),
3. pobiera **3 rekomendowane oferty** z modelu rekomendacji,
4. sprawdza zasady w bazie wiedzy i podaje źródło,
5. liczy marżę każdej opcji,
6. odpowiada krótko: dlaczego klient jest zagrożony, dwie najlepsze oferty z argumentami, czego nie wolno obiecywać,
7. po zatwierdzeniu zakłada zadanie follow-up i przygotowuje szkic notatki.

### Narzędzia agenta

| Narzędzie | Co robi |
|---|---|
| Ryzyko klienta | Zwraca wynik modelu churnu i przyczyny |
| Historia klienta | Zwraca zgłoszenia, faktury, użycie, awarie |
| Rekomendacje ofert | Zwraca top 3 oferty z modelu rekomendacji |
| Polityka i oferty | Szuka w katalogu i zasadach, podaje źródło |
| Kalkulator marży | Liczy opłacalność oferty po rabacie |
| Zadanie i notatka | Zakłada zadanie z terminem 48 h i szkic notatki |

### Czego agent nie robi

- Nie rozmawia z klientem. Wspiera konsultanta.
- Nie zatwierdza rabatów powyżej progu. Decyduje człowiek.
- Nie obiecuje niczego spoza polityki ofert.
- Nie pokazuje danych osobowych poza tym, co potrzebne do rozmowy.

### Zabezpieczenia

- Filtry bezpieczeństwa i maskowanie danych osobowych w odpowiedziach.
- Zatwierdzanie rabatów powyżej progu przez team leadera.
- Zestaw 50 przypadków testowych sprawdzany przed każdą zmianą (zgodność z polityką, brak zmyślonych ofert, odporność na próby obejścia zasad).
- Logowanie rozmów i kosztu każdej odpowiedzi.

---

## 10. Dzień pracy konsultanta

1. **Rano:** konsultant otwiera listę priorytetową (klienci o najwyższym ryzyku, przyczyna, sugerowana oferta).
2. **Rozmowa:** wpisuje identyfikator klienta do agenta.
3. **Rekomendacja (ok. 1 minuta):** dostaje ryzyko, przyczyny, dwie oferty i argumenty.
4. **Oferta:** przedstawia ją klientowi. Jeśli rabat przekracza próg, prosi Weronikę o zatwierdzenie w aplikacji.
5. **Notatka:** po rozmowie zatwierdza szkic notatki i zapisuje decyzję klienta oraz powód.
6. **Follow-up:** zadanie tworzy się automatycznie z terminem 48 h.

### Wdrożenie

- Szkolenie 2 godziny dla każdego zespołu.
- **Pilotaż:** jeden zespół (8 osób) pracuje z agentem, drugi (8 osób) po staremu jako grupa kontrolna. Zespoły przydzielam losowo i dostają podobne listy klientów.
- Cotygodniowy feedback od konsultantów i poprawki agenta.
- Opcjonalnie później: ta sama rekomendacja w punktach sprzedaży.

---

## 11. Raporty

**Narzędzie:** Looker Studio na danych z BigQuery. Wszystkie raporty korzystają z jednej warstwy miar (widoki w BigQuery), więc liczby są takie same w każdym raporcie. Dostęp zależy od roli i regionu.

### Przegląd

| # | Raport | Odbiorca | Odświeżanie | Właściciel |
|---|---|---|---|---|
| R1 | War room churnu | Dyrektor, Weronika | Codziennie 7:00 | Analityk BI |
| R2 | Typy i źródła churnu | Dyrektor, analityk | Tygodniowo | Analityk biznesowy |
| R3 | Sieć i regiony | Dyrektor, Weronika | Codziennie | Analityk BI |
| R4 | Kohorty i retencja | Analityk, Data Scientist | Tygodniowo | Analityk biznesowy |
| R5 | Klienci zagrożeni | Konsultanci, Weronika | Codziennie rano | Data Scientist |
| R6 | Rekomendacje ofert | Konsultanci, Weronika, sponsor | Codziennie / tygodniowo | Data Scientist |
| R7 | Skuteczność akcji i ROI | Sponsor, Weronika | Tygodniowo | Analityk biznesowy |
| R8 | Notatki z rozmów (analiza LLM) | Weronika, analityk, sponsor | Tygodniowo | Inżynier AI |
| R9 | Zespół | Weronika | Codziennie | Analityk BI |
| R10 | Jakość danych, modeli i koszty | Inżynier danych, Data Scientist, DevOps | Codziennie | Inżynier danych |

### R1. War room churnu

- **Pytania:** czy zbliżamy się do celu 2,0%? gdzie churn rośnie?
- **Metryki:** churn miesięczny i dzienny (średnia 7-dniowa), liczba odejść, utracony przychód, churn dobrowolny vs niedobrowolny, odległość od celu.
- **Filtry:** region, taryfa, segment, staż, kanał sprzedaży, okres.
- **Wizualizacje:** karty KPI (churn, odejścia, utracony przychód), wykres liniowy churnu z linią celu, słupki regionów posortowane malejąco, tabela taryf z kolorowaniem warunkowym.

### R2. Typy i źródła churnu

- **Pytania:** jakiego typu są odejścia? dlaczego odchodzą? które przyczyny rosną?
- **Metryki:** liczba i udział odejść według typu (6.1) i przyczyny (6.2), zmiana udziału przyczyn miesiąc do miesiąca, wpływ awarii (region awarii vs reszta), wpływ podwyżki (taryfy przed i po).
- **Filtry:** typ churnu, przyczyna, region, taryfa, okres.
- **Wizualizacje:** słupki skumulowane typów churnu w czasie, ranking przyczyn (słupki poziome), wykres przed/po z grupą porównawczą, wykres kaskadowy (od 1,8% do 2,6%, wkład przyczyn), tabela krzyżowa typ × przyczyna.
- **Źródło:** dane o odejściach, zgłoszenia, jakość sieci, analiza notatek.

### R3. Sieć i regiony

- **Pytania:** jak jakość sieci wiąże się z churnem? które regiony wymagają działań?
- **Metryki:** churn w regionie, wskaźniki jakości sieci, liczba i czas trwania awarii, liczba klientów dotkniętych awarią, zgłoszenia na 1000 klientów.
- **Filtry:** region, komórka, okres, typ awarii.
- **Wizualizacje:** mapa regionów (kolor = churn), oś czasu awarii nałożona na wykres churnu, wykres punktowy (jakość sieci vs churn według regionu), tabela regionów z rankingiem.

### R4. Kohorty i retencja

- **Pytania:** kiedy klienci odchodzą? które grupy są najbardziej narażone?
- **Metryki:** odsetek klientów pozostających po 1, 3, 6, 12 miesiącach, churn wczesny (do 90 dni), churn przy końcu umowy.
- **Filtry:** miesiąc aktywacji, taryfa, kanał sprzedaży, region.
- **Wizualizacje:** tabela kohort jako mapa cieplna (miesiąc aktywacji × miesiące życia), krzywe utrzymania klientów według taryfy, słupki churnu wczesnego według kanału sprzedaży.

### R5. Klienci zagrożeni

- **Pytania:** do kogo zadzwonić w pierwszej kolejności i dlaczego?
- **Kolumny:** identyfikator klienta, poziom ryzyka, prawdopodobieństwo, trzy przyczyny, sugerowana oferta (z R6), wartość zagrożona (prawdopodobieństwo × ARPU × 12), data ostatniego kontaktu, status zadania.
- **Filtry:** region, taryfa, poziom ryzyka, przyczyna, przypisany konsultant.
- **Wizualizacje:** tabela z sortowaniem po wartości zagrożonej, karty (liczba klientów wysokiego ryzyka, wartość zagrożona), słupki rozkładu ryzyka.
- **Dostęp:** konsultant widzi tylko swój zespół i region.

### R6. Rekomendacje ofert

- **Pytania:** co komu proponować? czy rekomendacje działają?
- **Metryki:** akceptacja oferty rekomendowanej vs wybranej ręcznie, marża po rabacie, zatrzymany przychód, odsetek rekomendacji zgodnych z wyborem konsultanta, czy zaakceptowana oferta była w top 3.
- **Filtry:** segment, przyczyna ryzyka, oferta, zespół, okres.
- **Wizualizacje:** mapa cieplna segment × oferta (% akceptacji), ranking ofert (słupki: akceptacja i marża), tabela rekomendacji dla klienta (top 3 z oczekiwaną wartością i uzasadnieniem), wykres „rekomendowana vs wybrana ręcznie".

### R7. Skuteczność akcji i ROI

- **Pytania:** czy pilotaż działa? czy się opłaca?
- **Metryki:** churn grupy testowej vs kontrolnej, akceptacja ofert, marża, czas przygotowania do rozmowy, koszt rabatów, koszt platformy, zatrzymany przychód, ROI i czas zwrotu.
- **Filtry:** grupa (test / kontrola), tydzień, zespół, typ oferty.
- **Wizualizacje:** słupki porównawcze test vs kontrola z przedziałami niepewności, wykres narastającej liczby uratowanych klientów, zestawienie kosztów i zysków, karty KPI z celami.

### R8. Notatki z rozmów (analiza LLM)

- **Pytania:** dlaczego klienci mówią, że odchodzą? jakie argumenty działają?
- **Metryki:** udział powodów w czasie, wspomniani konkurenci, sentyment, skuteczność argumentów (udział akceptacji po danym argumencie), liczba flag „obietnica spoza polityki", odsetek uzupełnionych notatek.
- **Filtry:** powód, konkurent, zespół, tydzień, wynik rozmowy.
- **Wizualizacje:** słupki powodów w czasie, ranking konkurentów, wykres sentymentu, tabela tematów rosnących w ostatnich tygodniach (zanonimizowane fragmenty), tabela skuteczności argumentów.

### R9. Zespół

- **Pytania:** czy zadania są w terminie? jak wygląda obciążenie? czy zespół używa agenta?
- **Metryki:** zadania w terminie 48 h, liczba przeterminowanych, obciążenie na konsultanta, adopcja agenta, akceptacja ofert na konsultanta, odsetek uzupełnionych notatek.
- **Filtry:** zespół, konsultant, tydzień.
- **Wizualizacje:** tabela zadań z kolorowaniem terminów, słupki obciążenia, wykres adopcji agenta w czasie, lista przeterminowanych zadań.
- **Uwaga:** raport służy do wsparcia i coachingu.

### R10. Jakość danych, modeli i koszty

- **Pytania:** czy dane są świeże i poprawne? czy modele działają? ile to kosztuje?
- **Metryki:** czas gotowości danych, reguły jakości zaliczone, błędy ładowania, trafność modeli, drift, koszt AWS i GCP, koszt jednej odpowiedzi agenta, jakość klasyfikacji LLM.
- **Wizualizacje:** wykres liniowy gotowości danych o 7:00, tabela reguł jakości, wykres trafności i driftu w czasie, słupki kosztów według usług.

---

## 12. Zespół i odpowiedzialności

Zespół jest mały. W symulacji jedna osoba może pełnić kilka ról, ale każda rola ma swoje zadania i swojego „właściciela" w backlogu. Imiona możesz dopisać.

| Osoba | Odpowiada za | Dostarcza | Zatwierdza ją |
|---|---|---|---|
| **Weronika, Team Leader** | Priorytety, backlog, ryzyka, decyzje, rabaty powyżej progu, raporty dla zarządu | Plan, rytm pracy, rejestr decyzji i ryzyk, raport statusu | Sponsor |
| **Sponsor** (Dyrektor ds. klienta) | Cele, KPI, budżet, akceptacja efektów | Zatwierdzone KPI i decyzja o skalowaniu | Zarząd |
| **Analityk biznesowy** | Wymagania, diagnoza, typy i źródła churnu, plan pilotażu, kategorie powodów | Notatka „Diagnoza", R2, R4, R7 | Weronika |
| **Inżynier danych (AWS)** | Replikacja Oracle, S3, Glue, jakość, porządkowanie danych | Warstwy raw, clean, curated, R10 | Weronika |
| **Inżynier danych (GCP)** | Przeniesienie do BigQuery, tabela klienta, widoki miar | Dane i miary w BigQuery | Weronika |
| **Data Scientist** | Model churnu, model rekomendacji ofert | Dzienny scoring, rekomendacje, R5, R6 | Weronika |
| **Inżynier AI** | Agent, analiza notatek przez LLM, zabezpieczenia | Działający agent, R8 | Weronika |
| **Analityk BI** | Raporty i warstwa miar | R1, R3, R9 | Sponsor |
| **DevOps / Cloud** | Środowiska AWS i GCP, wdrożenia, monitoring, koszty | Stabilne środowiska, kontrola kosztów | Weronika |
| **Specjalista ds. bezpieczeństwa i RODO** | Dostępy, maskowanie, zgodność | Zatwierdzony model dostępu | Sponsor |
| **Tester** | Jakość danych, testy agenta i LLM | Raport z testów przed wdrożeniem | Weronika |
| **Konsultanci retencji** (8 w pilotażu) | Praca z agentem, notatki, feedback | Dane o adopcji i skuteczności | Weronika |

### Kto odpowiada za co w kluczowych rezultatach

| Rezultat | Wykonuje | Zatwierdza | Informowani |
|---|---|---|---|
| Dane w warstwie curated | Inżynier danych (AWS) | Weronika | Analityk, Data Scientist |
| Diagnoza przyczyn | Analityk biznesowy | Sponsor | Zespół |
| Model churnu | Data Scientist | Weronika | Inżynier AI, BI |
| Model rekomendacji ofert | Data Scientist | Weronika i Sponsor | Inżynier AI, konsultanci |
| Analiza notatek LLM | Inżynier AI | Weronika | Analityk biznesowy |
| Agent AI | Inżynier AI | Weronika i Sponsor | Konsultanci |
| Raporty R1–R10 | Właściciele z sekcji 11 | Weronika | Odbiorcy raportów |
| Pilotaż i ROI | Analityk biznesowy | Sponsor | Zarząd |

### Co robię jako Weronika

- **Co tydzień:** poniedziałek (planowanie i priorytety), codziennie daily 15 minut, środa (przegląd ryzyk), piątek (status na jednej stronie dla sponsora).
- **Co dwa tygodnie:** sprint review z liczbami z raportów i retro zespołu.
- **Decyzje:** rejestr decyzji (co, dlaczego, jakie alternatywy), zatwierdzanie rabatów powyżej progu, decyzje „idziemy dalej / zatrzymujemy" na końcu każdego kroku z sekcji 13.
- **Odpowiedzialność:** macierz odpowiedzialności z tej sekcji i backlog z jedną tablicą, jasnymi priorytetami i właścicielami.
- **Eskalacje:** blokery powyżej 2 dni trafiają do sponsora.

---

## 13. Plan w krokach

Tygodnie dotyczą fikcyjnego programu w firmie (3 miesiące). Realny czas pracy nad repozytorium zależy od liczby godzin tygodniowo.

| Krok | Tydzień | Co robimy | Odpowiada | Wynik |
|---|---|---|---|---|
| 1 | 1 | Cele, KPI, zespół, backlog, rytm pracy | Weronika, Sponsor | Ten dokument, tablica projektu |
| 2 | 1–2 | Środowiska AWS i GCP, dostępy, budżety z alertami | DevOps / Cloud | Działające środowiska |
| 3 | 2–3 | Zbieranie danych: Oracle (replikacja), CSV, zgłoszenia, notatki do S3 raw | Inżynier danych (AWS) | Dane surowe w jednym miejscu |
| 4 | 3–5 | Porządkowanie danych (8 kroków z 5.5), warstwy clean i curated | Inżynier danych (AWS), Analityk | Dane gotowe do analizy |
| 5 | 4–5 | Przeniesienie do BigQuery, tabela klienta, widoki miar | Inżynier danych (GCP) | Warstwa analityczna |
| 6 | 4–6 | Diagnoza: typy i źródła churnu, raporty R2–R4 | Analityk biznesowy, Data Scientist | Notatka „Diagnoza", decyzje |
| 7 | 6–8 | Model churnu i dzienny scoring, raport R5 | Data Scientist | Wynik ryzyka z przyczynami |
| 8 | 6–9 | Raporty R1, R3, R9, R10 | Analityk BI, Inżynier danych | Dashboardy |
| 9 | 7–10 | Notatki w bazie, analiza LLM, model rekomendacji ofert, raporty R6 i R8 | Inżynier AI, Data Scientist | Analiza notatek i rekomendacje |
| 10 | 8–11 | Agent: baza ofert, narzędzia, zabezpieczenia, testy | Inżynier AI, Tester | Działający agent |
| 11 | 11–12 | Pilotaż z grupą kontrolną i pomiar, raport R7 | Analityk biznesowy, Weronika | Wyniki testu |
| 12 | 12 | Raport końcowy i rekomendacja dla zarządu | Weronika | Raport z ROI, decyzja o skalowaniu |

> [!IMPORTANT]
> **Kryterium decyzji o skalowaniu (założenie):** churn w grupie pilotażowej niższy niż w kontrolnej o co najmniej 0,3 p.p., akceptacja ofert powyżej 25% i marża po rabacie dodatnia dla wszystkich zatwierdzonych ofert.

---

## 14. Ryzyka

| Ryzyko | Co robimy | Odpowiada |
|---|---|---|
| Dane niekompletne lub błędne | Kontrola jakości przy każdym ładowaniu, porównanie liczby wierszy źródło vs cel | Inżynier danych |
| Rozjazd danych między AWS a GCP | Przepływ w jedną stronę, kontrola liczby wierszy po transferze, jeden właściciel każdej tabeli | Inżynier danych (GCP) |
| Rosnące koszty dwóch chmur | Budżety z alertami w obu chmurach, limity zapytań, przegląd kosztów co sprint (R10) | DevOps |
| Model churnu za słaby | Zaczynamy od prostego punktu odniesienia, próg minimalnej jakości, człowiek decyduje o kontakcie | Data Scientist |
| Rekomendacje ofert nietrafne lub nieopłacalne | Filtr reguł i limity rabatów, test w pilotażu, kontrola marży | Data Scientist |
| LLM błędnie klasyfikuje notatki | Test na 100 ręcznie ocenionych notatkach, tygodniowa kontrola 5% próbki, flagi niskiej pewności | Inżynier AI |
| Agent zmyśla lub obiecuje za dużo | Odpowiedzi tylko na bazie polityki ze źródłem, zabezpieczenia, zatwierdzanie rabatów, testy przed każdą zmianą | Inżynier AI, Tester |
| Konsultanci nie używają agenta lub nie piszą notatek | Szkolenie, wybrany „ambasador" w zespole, pomiar adopcji (R9), cotygodniowy feedback | Weronika |
| Naruszenie RODO | Minimalizacja danych, maskowanie przed analizą i LLM, dostęp według roli i regionu | Specjalista ds. bezpieczeństwa |
| Opóźnienia | Mały zakres na start, rejestr ryzyk, szybka eskalacja do sponsora | Weronika |

---

## 15. Jak raportuje lider

- **Codziennie:** daily 15 minut (co zrobione, co dziś, co blokuje).
- **Co tydzień:** status na jednej stronie dla sponsora (zrobione, ryzyka, potrzebne decyzje) z liczbami z R1 i R9.
- **Co 2 tygodnie:** review z sekcją „co pokazują raporty" i retro zespołu.
- **Na koniec:** raport końcowy z diagnozą, wynikami pilotażu (R7), ROI i rekomendacją.

---

## 16. Dokumenty w repozytorium

| Dokument | Zawartość |
|---|---|
| `docs/01-plan-redukcji-churnu.md` | Ten dokument |
| `docs/02-persony-i-user-stories.md` | Persony i historyjki użytkowników |
| `docs/03-raci-i-backlog.md` | Role, odpowiedzialności, backlog |
| `docs/04-slownik-danych.md` | Słownik danych i właściciele zbiorów |
| `docs/decisions/` | Rejestr decyzji |
| `docs/architektura.md` | Architektura rozwiązania (AWS + GCP) |
