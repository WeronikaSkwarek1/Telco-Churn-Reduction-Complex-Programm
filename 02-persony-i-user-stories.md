# 👥 Persony i user stories: Telenova Polska

> [!NOTE]
> Persony są **fikcyjne**. Opisują role w projekcie redukcji churnu z 2,6% do ok. 2,0%. Imiona poza Weroniką i Edytą możesz dopisać.

**Platforma:** całość na **GCP** (Cloud Storage, BigQuery, Vertex AI, Cloud Run). **Raporty:** **Power BI** na danych z BigQuery.

## Spis treści

1. [Jak role współpracują](#1-jak-role-współpracują)
2. [Edyta, kierowniczka zespołu sprzedaży](#2-edyta-kierowniczka-zespołu-sprzedaży)
3. [Inżynier danych (GCP)](#3-inżynier-danych-gcp)
4. [Data Scientist](#4-data-scientist)
5. [Analityk Power BI](#5-analityk-power-bi)
6. [Inżynier AI](#6-inżynier-ai)
7. [Pozostałe role](#7-pozostałe-role)
8. [User stories](#8-user-stories)

---

## 1. Jak role współpracują

```
Inżynier danych  →  Data Scientist  →  Analityk Power BI  →  Edyta  →  Konsultanci
(porządkuje dane)   (churn, oferty)    (raporty)             (rozmowy)  (klienci)
                          ↑                                     │
                  Inżynier AI (analiza notatek, agent)          │
                          ↑                                     │
                          └──────────── feedback ───────────────┘
```

Dane płyną od inżyniera danych do raportów i do Edyty. Feedback wraca w drugą stronę: Edyta zbiera problemy od konsultantów i przekazuje je zespołowi.

---

## 2. Edyta, kierowniczka zespołu sprzedaży

| | |
|---|---|
| **Kim jest** | Kierowniczka zespołu konsultantów retencji. Ogarnia sprzedawców i na co dzień z nimi rozmawia. |
| **Cel** | Zatrzymać jak najwięcej klientów i podnieść akceptację ofert z ok. 15% do ponad 25%. |
| **Z czego korzysta** | Raport churnu i raport rekomendacji ofert (Power BI), lista klientów zagrożonych. |

**Co robi na co dzień**
- Patrzy na raport churnu i rekomendacji.
- Rozmawia z konsultantami i zbiera ich problemy: które oferty nie działają, co mówią klienci, czego brakuje w narzędziach.
- Przekazuje wnioski Weronice, analitykowi i data scientistowi.

**Problemy dziś**
- Nie wie z góry, kogo ratować w pierwszej kolejności. Lista odejść powstaje po fakcie.
- Oferty są rozproszone i dobierane „na oko", część rabatów ma ujemną marżę.
- Feedback od konsultantów zostaje w rozmowach i nigdzie się nie zapisuje.

**Czego oczekuje od rozwiązania**
- Codziennej listy klientów o najwyższym ryzyku.
- Rekomendacji ofert z krótkim uzasadnieniem.
- Raportu zrozumiałego bez znajomości SQL.
- Prostego sposobu na przekazanie feedbacku zespołowi danych.

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
- Buduje raporty w Power BI: churn, przyczyny, regiony i jakość sieci, lista klientów zagrożonych, skuteczność akcji (test vs kontrola), widok zespołu.
- Tworzy model semantyczny i miary, żeby liczby w raportach się zgadzały.
- Pilnuje, kto widzi co (dostęp według roli i regionu).

**Problemy dziś**
- Raporty to ręczne arkusze, raz w miesiącu.
- Brak jednej definicji miar, więc każdy liczy churn po swojemu.

**Czego oczekuje od rozwiązania**
- Stabilnych, opisanych widoków i miar w BigQuery od inżyniera danych.
- Gotowego scoringu od data scientista.
- Potrzeb raportowych od Edyty i Weroniki.

---

## 6. Inżynier AI

| | |
|---|---|
| **Kim jest** | Osoba od wszystkiego, co robi model językowy (LLM): analiza notatek i agent. |
| **Cel** | Wyciągnąć wiedzę z notatek i rozmów oraz dać konsultantowi agenta, któremu można ufać. |
| **Narzędzia** | Vertex AI, Gemini, Agent Development Kit, Cloud Run. |

**Co robi na co dzień**
- Analizuje notatki i transkrypcje rozmów: powody odejść, sentyment, wspomniani konkurenci.
- Buduje **raporty AI** z tych analiz (powody w czasie, skuteczne argumenty), które trafiają do Power BI.
- Buduje agenta wspierającego konsultanta i pilnuje jego bezpieczeństwa: bez zmyślania, bez ujawniania danych osobowych.

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
| **Konsultant retencji** | Rozmawia z klientami, używa raportów i agenta, daje feedback Edycie. |

---

## 8. User stories

> [!NOTE]
> Do uzupełnienia w następnym kroku. Format: **Jako [rola] chcę [co], żeby [po co].** Pod każdą historyjką kryteria akceptacji.

### Szablon

**US-01: [tytuł]**
Jako **[rola]** chcę **[co]**, żeby **[po co]**.

Kryteria akceptacji:
- [ ] ...
- [ ] ...
