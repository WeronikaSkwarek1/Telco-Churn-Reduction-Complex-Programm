# 👥 Persony i user stories: Telenova Polska

> [!NOTE]
> Firma, ludzie i dane w tym projekcie są **fikcyjne** (dane syntetyczne). Persony opisują role w programie redukcji churnu z 2,6% do ok. 2,0% w 3 miesiące.

| | |
|---|---|
| **Platforma** | Całość na **GCP** (Cloud Storage, BigQuery, Vertex AI, Cloud Run) |
| **Raporty** | **Power BI** na danych z BigQuery |
| **Powiązany dokument** | [`01-plan-redukcji-churnu.md`](01-plan-redukcji-churnu.md) |

## Spis treści

1. [Jak role współpracują](#1-jak-role-współpracują)
2. [Lider Sprzedawców (Edyta)](#2-lider-sprzedawców-edyta)
3. [Inżynier danych (GCP)](#3-inżynier-danych-gcp)
4. [Data Scientist](#4-data-scientist)
5. [Analityk Power BI](#5-analityk-power-bi)
6. [Inżynier AI](#6-inżynier-ai)
7. [Pozostałe role](#7-pozostałe-role)
8. [User stories](#8-user-stories)
9. [Uwagi na kolejne kroki](#9-uwagi-na-kolejne-kroki)

---

## 1. Jak role współpracują

```
Inżynier danych  →  Data Scientist  →  Analityk Power BI  →  Lider Sprzedawców  →  Sprzedawcy
(porządkuje dane)   (churn, oferty)    (raporty)             (Edyta)               (klienci)
                          ↑                                         │
                  Inżynier AI (analiza notatek, agent)              │
                          ↑                                         │
                          └──────────────── feedback ───────────────┘
```

Dane płyną od inżyniera danych do raportów i do Edyty. Feedback wraca w drugą stronę: Edyta zbiera problemy od sprzedawców i przekazuje je zespołowi.

---

## 2. Lider Sprzedawców (Edyta)

| | |
|---|---|
| **Kim jest** | Lider zespołu sprzedawców retencji. Ogarnia sprzedawców i na co dzień z nimi rozmawia. |
| **Cel** | Zatrzymać jak najwięcej klientów i podnieść akceptację ofert z ok. 15% do ponad 25%. |
| **Z czego korzysta** | Raport churnu, raport rekomendacji i lista klientów zagrożonych (Power BI). |

**Co robi na co dzień**
- Patrzy na raport churnu i raport rekomendacji.
- Rozmawia ze sprzedawcami i zbiera ich problemy: które oferty nie działają, co mówią klienci, czego brakuje w narzędziach.
- Przekazuje wnioski Weronice, analitykowi i data scientistowi.

**Problemy dziś**
- Nie wie z góry, kogo ratować w pierwszej kolejności. Lista odejść powstaje po fakcie.
- Oferty są rozproszone i dobierane „na oko", część rabatów ma ujemną marżę.
- Nie widzi, jakie rekomendacje dostają klienci, kto je przedstawia i co się z nimi dzieje.

**Czego oczekuje od rozwiązania**
- Codziennej listy klientów o najwyższym ryzyku.
- Codziennego raportu rekomendacji: kto dostał jaką ofertę, od jakiego sprzedawcy, z jakiej kampanii i z jakim wynikiem.
- Raportu zrozumiałego bez znajomości SQL.

---

## 3. Inżynier danych (GCP)

| | |
|---|---|
| **Kim jest** | Osoba od fundamentu: baza danych, przenoszenie i porządkowanie danych. |
| **Cel** | Dane gotowe codziennie rano, kompletne i poprawne, w jednym miejscu. |
| **Narzędzia** | Cloud Storage, BigQuery, Datastream, Dataform, Cloud Composer. |

**Co robi na co dzień**
- Przenosi dane z Oracle, plików CSV i systemu zgłoszeń do GCP.
- Porządkuje dane: usuwa duplikaty, poprawia kodowanie, obsługuje zmiany schematu.
- Układa dane w warstwy (bronze, silver, gold) i buduje tabelę klienta oraz miary.
- Sprawdza jakość: zgodność liczby wierszy źródło vs cel, reguły jakości.

**Problemy dziś**
- Dane leżą w Oracle, plikach CSV i systemie zgłoszeń, bez wspólnych definicji.
- Brak słownika danych i jasnych właścicieli tabel.
- Dane bywają niekompletne albo błędne.

**Czego oczekuje od rozwiązania**
- Słownika danych z właścicielami zbiorów.
- Jasnych definicji (np. co to jest churn), uzgodnionych z analitykiem.
- Informacji zwrotnej od odbiorców, gdy dane wyglądają podejrzanie.

---

## 4. Data Scientist

| | |
|---|---|
| **Kim jest** | Osoba, która na podstawie danych tworzy predykcje i rekomendacje. |
| **Cel** | Wskazać, kto odejdzie i jaką ofertę mu zaproponować. Trafność: recall@10% co najmniej 40%. |
| **Narzędzia** | BigQuery (SQL, BigQuery ML), Python, Vertex AI. |

**Co robi na co dzień**
- Buduje **model churnu**: ryzyko odejścia każdego klienta, z przyczynami.
- Buduje **model rekomendacji ofert**: 3 oferty z uzasadnieniem, z kontrolą marży.
- Dostarcza codzienny scoring, który zasila raporty.

**Problemy dziś**
- Nikt nie przewiduje odejść z wyprzedzeniem.
- Brak cech do modelu w jednym miejscu (rachunki, zgłoszenia, awarie, rozmowy).
- Ryzyko, że model będzie słaby albo „podejrzy przyszłość" (data leakage).

**Czego oczekuje od rozwiązania**
- Czystych, spójnych danych od inżyniera danych.
- Feedbacku od Edyty, czy rekomendacje mają sens w rozmowach.
- Grupy kontrolnej, żeby uczciwie zmierzyć efekt.

---

## 5. Analityk Power BI

| | |
|---|---|
| **Kim jest** | Osoba, która zamienia wyniki modeli i dane na raporty zrozumiałe dla biznesu. |
| **Cel** | Raporty, którym ufają Edyta, Weronika i sponsor, odświeżane codziennie lub tygodniowo. |
| **Narzędzia** | Power BI (Desktop i Service) połączony z BigQuery. |

**Co robi na co dzień**
- Buduje raporty w Power BI: churn, przyczyny, regiony i jakość sieci, lista klientów zagrożonych, raport rekomendacji, skuteczność akcji (test vs kontrola).
- Tworzy model semantyczny i miary, żeby liczby w raportach się zgadzały.
- Pilnuje, kto widzi co (dostęp według roli i regionu).

**Problemy dziś**
- Raporty to ręczne arkusze, raz w miesiącu.
- Brak jednej definicji miar, więc każdy liczy churn po swojemu.

**Czego oczekuje od rozwiązania**
- Stabilnych, opisanych widoków i miar w BigQuery od inżyniera danych.
- Gotowego scoringu i rekomendacji od data scientista.
- Potrzeb raportowych od Edyty i Weroniki.

---

## 6. Inżynier AI

| | |
|---|---|
| **Kim jest** | Osoba od wszystkiego, co robi model językowy (LLM): analiza notatek i agent. |
| **Cel** | Wyciągnąć wiedzę z notatek i rozmów oraz dać sprzedawcy agenta, któremu można ufać. |
| **Narzędzia** | Vertex AI, Gemini, Agent Development Kit, Cloud Run. |

**Co robi na co dzień**
- Analizuje notatki i transkrypcje rozmów: powody odejść, sentyment, wspomniani konkurenci.
- Buduje **raporty AI** z tych analiz (powody w czasie, skuteczne argumenty), które trafiają do Power BI.
- Buduje agenta wspierającego sprzedawcę i pilnuje jego bezpieczeństwa: bez zmyślania, bez ujawniania danych osobowych.

**Problemy dziś**
- Notatki z rozmów są rozproszone i nikt ich nie analizuje.
- Ryzyko, że LLM błędnie sklasyfikuje powód albo obieca za dużo.

**Czego oczekuje od rozwiązania**
- Notatek zebranych w jednym miejscu.
- Opisanej polityki ofert i retencji, na której agent może się opierać.
- Ręcznie ocenionej próbki notatek do sprawdzania jakości (cel: zgodność co najmniej 85%).

---

## 7. Pozostałe role

| Rola | Krótko |
|---|---|
| **Weronika, Team Leader** | Liderka programu: priorytety, backlog, ryzyka, decyzje, raporty dla sponsora. |
| **Sponsor (Dyrektor ds. klienta)** | Zatwierdza cele, budżet i decyzję o skalowaniu. |

---

## 8. User stories

Format: **Jako [rola] chcę [co], żeby [po co].** Pod każdą historyjką są kryteria akceptacji, czyli warunki, które da się sprawdzić na tak albo nie.

### Przegląd

| ID | Rola | Historyjka |
|---|---|---|
| US-01 | Lider Sprzedawców | Lista klientów zagrożonych |
| US-02 | Lider Sprzedawców | Raport rekomendacji |
| US-03 | Inżynier danych | Dane gotowe rano i sprawdzone |
| US-04 | Data Scientist | Codzienny scoring churnu |
| US-05 | Data Scientist | Rekomendacja ofert z kontrolą marży |
| US-06 | Analityk Power BI | Jedna definicja churnu i miar |
| US-07 | Analityk Power BI | Dostęp do raportów według roli |
| US-08 | Inżynier AI | Analiza notatek z rozmów |
| US-09 | Inżynier AI | Agent wspierający sprzedawcę |

---

### Lider Sprzedawców (Edyta)

#### US-01: Lista klientów zagrożonych

Jako **Lider Sprzedawców** chcę **codziennie widzieć listę klientów o najwyższym ryzyku odejścia**, żeby **wiedzieć, kogo sprzedawcy mają ratować w pierwszej kolejności**.

Kryteria akceptacji:
- [ ] Lista jest w raporcie Power BI i odświeża się codziennie do 7:00.
- [ ] Każdy klient ma wynik ryzyka i główną przyczynę.
- [ ] Listę można filtrować po regionie i zespole.
- [ ] Klienci są posortowani od najwyższego ryzyka.

#### US-02: Raport rekomendacji

Jako **Lider Sprzedawców** chcę **codziennie widzieć odświeżany raport rekomendacji, w którym każdy klient ma przeliczoną rekomendację i wiadomo, co się z nią stało**, żeby **sprawdzać, jakie oferty są proponowane, kto je przedstawia i które kampanie działają**.

Kryteria akceptacji:
- [ ] Raport w Power BI odświeża się codziennie do 7:00.
- [ ] Dla każdego klienta widać: przeliczoną rekomendację (nazwa oferty), sprzedawcę, któremu została przypisana, oraz nazwę kampanii, z której pochodzi.
- [ ] Każda rekomendacja ma status: **wyświetlona**, **zaakceptowana** albo **odrzucona**.
- [ ] Raport można filtrować po statusie, sprzedawcy, kampanii, regionie i zespole.
- [ ] Na górze są podsumowania: liczba rekomendacji, udział zaakceptowanych i odrzuconych oraz akceptacja według kampanii.
- [ ] Suma rekomendacji w raporcie zgadza się z liczbą w BigQuery.

---

### Inżynier danych (GCP)

#### US-03: Dane gotowe rano i sprawdzone

Jako **Inżynier danych** chcę **żeby dane z Oracle, plików CSV i zgłoszeń lądowały codziennie w BigQuery i były sprawdzane**, żeby **wszyscy pracowali na kompletnych, poprawnych danych**.

Kryteria akceptacji:
- [ ] Dane są gotowe do 7:00 w co najmniej 99% dni.
- [ ] Liczba wierszy w źródle zgadza się z liczbą w BigQuery.
- [ ] Co najmniej 98% reguł jakości danych jest zaliczonych.
- [ ] Przy błędzie ładowania ktoś dostaje alert.
- [ ] Każda tabela ma opisanego właściciela w słowniku danych.

---

### Data Scientist

#### US-04: Codzienny scoring churnu

Jako **Data Scientist** chcę **codziennie wyliczać ryzyko odejścia dla każdego klienta z przyczynami**, żeby **Lider Sprzedawców i sprzedawcy wiedzieli, kogo i dlaczego ratować**.

Kryteria akceptacji:
- [ ] Model łapie co najmniej 40% odchodzących klientów w grupie 10% najbardziej zagrożonych (recall@10% co najmniej 40%).
- [ ] Wynik jest zapisany w BigQuery do 7:00.
- [ ] Przy każdym wyniku jest główna przyczyna (np. awaria, podwyżka, rachunki).
- [ ] Model uczy się na danych z przeszłości i nie „podgląda" przyszłości.

#### US-05: Rekomendacja ofert z kontrolą marży

Jako **Data Scientist** chcę **żeby model wskazywał 3 oferty dla klienta z uzasadnieniem**, żeby **sprzedawca nie dobierał ofert „na oko" i nie dawał rabatów poniżej marży**.

Kryteria akceptacji:
- [ ] Każda rekomendowana oferta ma dodatnią marżę po rabacie (100% ofert).
- [ ] Przy każdej ofercie jest krótkie uzasadnienie.
- [ ] Każda rekomendacja jest zapisana w BigQuery z nazwą kampanii, z której pochodzi.
- [ ] W pilotażu akceptacja ofert z rekomendacji jest o co najmniej 5 p.p. wyższa niż z wyboru ręcznego.

---

### Analityk Power BI

#### US-06: Jedna definicja churnu i miar

Jako **Analityk Power BI** chcę **mieć jedną, opisaną definicję churnu i innych miar**, żeby **liczby w każdym raporcie były takie same**.

Kryteria akceptacji:
- [ ] Definicja churnu jest zapisana w dokumencie i uzgodniona z Inżynierem danych.
- [ ] Miary są policzone w jednym miejscu (widoki w BigQuery), a nie osobno w każdym raporcie.
- [ ] Churn miesięczny w Power BI zgadza się z wynikiem zapytania SQL.

#### US-07: Dostęp do raportów według roli

Jako **Analityk Power BI** chcę **żeby każdy widział tylko to, co mu wolno**, żeby **dane osobowe i dane innych zespołów były chronione**.

Kryteria akceptacji:
- [ ] Lider Sprzedawców widzi tylko klientów i sprzedawców ze swojego zespołu.
- [ ] Dane osobowe (np. numer telefonu) są zamaskowane tam, gdzie nie są potrzebne.
- [ ] Sponsor widzi dane zbiorcze bez list klientów.
- [ ] Test z kontami o różnych rolach potwierdza powyższe.

---

### Inżynier AI

#### US-08: Analiza notatek z rozmów

Jako **Inżynier AI** chcę **żeby model językowy wyciągał z notatek powód odejścia, sentyment i wspomnianych konkurentów**, żeby **firma wiedziała, dlaczego klienci naprawdę odchodzą**.

Kryteria akceptacji:
- [ ] Zgodność klasyfikacji powodu z oceną człowieka wynosi co najmniej 85% (test na 100 ręcznie ocenionych notatkach).
- [ ] Wyniki trafiają do BigQuery i są widoczne w raporcie Power BI (powody w czasie).
- [ ] Dane osobowe są zamaskowane, zanim notatka trafi do modelu.
- [ ] Odpowiedzi o niskiej pewności są oznaczone do sprawdzenia przez człowieka.

#### US-09: Agent wspierający sprzedawcę

Jako **Inżynier AI** chcę **zbudować agenta, który odpowiada sprzedawcy na podstawie polityki ofert**, żeby **sprzedawca dostawał rzetelną podpowiedź i nie obiecywał klientowi rzeczy spoza polityki**.

Kryteria akceptacji:
- [ ] Zgodność odpowiedzi z polityką wynosi co najmniej 95% (zestaw 50 przypadków testowych).
- [ ] Przy każdej odpowiedzi o ofercie jest źródło z dokumentu.
- [ ] Rabat powyżej ustalonego progu wymaga zatwierdzenia przez człowieka.
- [ ] Koszt jednej odpowiedzi to najwyżej 0,10 zł.
- [ ] Agent nie ujawnia danych osobowych i oprze się prostym próbom wymuszenia zakazanych odpowiedzi.

---

## 9. Uwagi na kolejne kroki

- **Progi to założenia.** Wartości takie jak 40%, 85%, 95% czy 0,10 zł pochodzą z planu i zostaną zweryfikowane w pilotażu.
- **Dane dla raportu rekomendacji (US-02).** Żeby raport pokazywał status rekomendacji, sprzedawcę i kampanię, w danych syntetycznych (Faza 2) trzeba przewidzieć tabelę rekomendacji z polami: klient, oferta, sprzedawca, kampania, status (wyświetlona, zaakceptowana, odrzucona) i data.
- **Decyzje do zapisania w rejestrze (ADR).** Całość na GCP (bez AWS) oraz Power BI jako główne narzędzie raportowe.
- **Poprawka w planie.** W `01-plan-redukcji-churnu.md` zamienić AWS na GCP i dopisać Power BI.
