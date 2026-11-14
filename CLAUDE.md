# CLAUDE.md – swiss-road-accident-severity

Gruppenprojekt im Modul **Data Analytics** (3 Studierende). Dieses Dokument gibt Claude den Auftrag, die Bewertungslogik und die Arbeitsregeln für dieses Repository vor. Sprache im Team: Deutsch. Code, Variablen- und Spaltennamen: Englisch.

## 1. Auftrag

Forschungsfrage: **Welche Faktoren stehen mit der Schwere von Verkehrsunfällen mit Personenschaden in der Schweiz in Zusammenhang, insbesondere Stadt/Land und Tag/Nacht?**

- **H1:** Auf dem Land ist der Anteil schwerer bzw. tödlicher Unfallfolgen höher als in der Stadt.
- **H2:** In den Nachtstunden ist der Anteil schwerer bzw. tödlicher Unfallfolgen höher als am Tag.
- **Keine H3.** `accident_type` und `road_type` sind nur Kontrollvariablen.
- Zielvariable: `severe` = 1 (schwerverletzt/getötet), 0 (leichtverletzt).
- Es gibt keinen schriftlichen Bericht. Abgegeben werden: Video (mp4), ZIP mit Daten und Notebooks, Präsentation als PDF, Moodle-Upload durch eine Person mit Gruppennummer.

## 2. Bewertungsraster (daran misst sich jede Entscheidung)

Mindestanforderungen (9 Punkte):
1. Eigene Datenerhebung per Web-API/Scraping (`requests`). Keine fertigen Kaggle-Datensätze als Hauptquelle, keine manuell heruntergeladene CSV.
2. Mindestens 2 Quellen, Join dokumentiert (Schlüssel, Matching-Quote, verlorene Datensätze).
3. SQLite plus SQL-Abfragen aus Python.
4. Umfangreiche EDA (nicht grafisch und grafisch).
5. Klassifikation oder Regression mit Forschungsfrage über reine Vorhersage hinaus (hier: Gruppenvergleich).
6. Modellevaluation (Accuracy, Precision, Recall, F1, ROC-AUC).
7. Korrekte Interpretation der Ergebnisse und Gütemasse.
8. Dokumentation der KI-Nutzung inkl. Vor-/Nachteilen und Fehlschlägen.
9. Material auf Moodle.

Zusatzpunkte (max. 6): Kreativität, PostgreSQL (nicht SQLite), Geodaten, Chi-Quadrat/ANOVA/Korrelation mit p-Wert, k-means, öffentliches GitHub.
Bewusst eingeplant: Geodaten, Chi-Quadrat, öffentliches GitHub. Bewusst **nicht** geplant: PostgreSQL, k-means, Regression Tree. Nichts davon nur wegen Zusatzpunkten einbauen, bevor die Pflichtpunkte stehen.

Präsentation: Länge = **Anzahl Studierende × 5 Min. = 15 Min.** (wird bewertet). Folien basieren auf der PDF-Vorlage des Moduls. Frei sprechen, Code nur zeigen, wenn er Methoden oder Resultate erklärt. Im Anhang der PDF steht, wofür die Gruppe Punkte erwartet (Screenshots, Code-Snippets).

## 3. Definitionen (verbindlich, vor dem Coden festhalten)

| Variable | Definition |
|---|---|
| `severe` | 1 bei Schwerverletzten/Getöteten, 0 bei Leichtverletzten |
| `night` | Nachtstunden z. B. 22:00–05:59. Sensitivitätsanalyse mit saisonaler Dunkelheit (Monat und Uhrzeit) erwünscht |
| `rural` | Stadt/Land nach BFS-Typologie. Intermediäre Gemeinden gesondert behandeln oder begründet ausschliessen |
| `accident_type`, `road_type` | Kontrollvariablen (kategorial, One-Hot) |

Gemeinsames Schema: `accident_id, year, severe, rural, night, accident_type, road_type, municipality_id`. Eine Zeile = ein Unfall. Rohspalten dürfen anders heissen. Es gibt genau einen zentralen bereinigten Datensatz.

## 4. Datenquellen (Status prüfen, nicht annehmen)

- **ASTRA, «Strassenverkehrsunfälle mit Personenschaden»** (opendata.swiss, ab 2011). **Verifiziert (Branch `data`, 2026-11-14):** Abruf per STAC-API `https://data.geo.admin.ch/api/stac/v1/collections/ch.astra.unfaelle-personenschaeden_alle/items`; Asset `unfaelle-personenschaeden_alle_2056.csv.zip` (Gesamtdatei: 267'761 Zeilen, 36 Spalten). Enthält u. a. `AccidentUID`, `AccidentYear/Month/WeekDay/Hour`, `AccidentSeverityCategory` (as1 getötet, as2 schwerverletzt, as3 leichtverletzt; Zuordnung der Codes zu den Schweregraden gemäss Notebook), `AccidentType` (Code plus Labels `AccidentType_de`, `_fr`; für `RoadType` und `AccidentWeekDay` analoge Labelspalten prüfen), `MunicipalityCode`, `CantonCode`, LV95-Koordinaten (`AccidentLocation_CHLV95_E/N`).
- **Folge:** Die Gemeindenummer liegt direkt vor, ein Spatial Join ist für die Typologie nicht nötig. Koordinaten dienen der Karte. Lizenz weiterhin **nicht verifiziert**.
- **BFS Stadt/Land-Typologie:** `data/raw/stadt_land.csv` (Spalte «Stadt/Land-Typologie», Codierung 1/2/3 = Urban/Intermediate/Rural laut Notebook; gegen BFS-Legende prüfen). Datei liegt lokal vor, ist also **nicht** per Code abgerufen (Abruf-URL und Datum dokumentieren oder automatisieren). Gemeindestand prüfen.
- Gemeindegrenzen und Typologie müssen denselben Gemeindestand verwenden, sonst gehen Fälle durch Fusionen verloren.
- Wenn ein Endpunkt, eine Spalte oder eine Lizenz nicht bestätigt ist, so in Notebook und README schreiben. Nichts erfinden (Spaltennamen, Codes, Kategorien).

## 5. Repository-Struktur

```
notebooks/            # aktuell Tippfehler-Ordner "nootebooks/" -> mit `git mv nootebooks notebooks` umbenennen
  01_data.ipynb       # Person A: API, Cleaning, Joins, SQLite/SQL
  02_eda.ipynb        # Person B: EDA, Chi-Quadrat, Karte
  03_model.ipynb      # Person C: logistische Regression, Evaluation
data/raw/             # nicht committen (gross), Abrufanleitung im README
data/processed/       # accidents_clean.parquet, SQLite-DB (lokal)
figures/
presentation/
README.md  requirements.txt  .gitignore
```

Aktueller Stand (`main`): README.md, requirements.txt, .gitignore, 01_data.ipynb und 02_eda.ipynb sind leer; 03_model.ipynb ist praktisch leer.

Aktueller Stand (Branch `data`, 3 Commits von Person A, noch nicht nach `main` gemergt):
- `nootebooks/01_data.ipynb` ist fertig bis und mit SQLite, Parquet und Qualitäts-Checkpoint. `02_eda.ipynb` und `03_model.ipynb` existieren dort nicht mehr (auf `main` leer vorhanden), `.gitignore` und `requirements.txt` sind weiter leer.
- Ergebnisdaten: `data/processed/accidents_clean.parquet` (89'193 Zeilen), `data/processed/accidents.db` (Tabellen `accidents`, `municipalities`), Rohdaten `data/raw/` (inkl. `gemeinden.geojson`, im Notebook noch ungenutzt).
- Zeitraum 2021–2025: 267'761 Rohfälle → 89'544 im Zeitraum → 89'193 mit BFS-Zuordnung (351 verloren = 0.39 %).
- **Bekannter Fehler:** `pd.read_csv("../data/raw/stadt_land.csv", sep=";", skiprows=[0, 2])` überspringt die Kopfzeile und die Gemeinde 2 und macht Gemeinde 1 zur Kopfzeile. Dadurch fehlen die Gemeinden 1 und 2 (84 der 351 «nicht zuordenbaren» Unfälle). Fix: ohne `skiprows` einlesen und Spalten nach Namen wählen. Die übrigen 267 nicht zuordenbaren Unfälle haben Codes, die in der BFS-Datei fehlen (6513, 5394, 6831, 5395, 2239, 2056, 9073). Wahrscheinlich anderer Gemeindestand: prüfen und in der Verlusttabelle erklären.
- Code-Labels (`at0`, `rt432`, `aw404`) sind nicht dekodiert. Die `_de`-Labels aus den Rohdaten für EDA und Modell erhalten (oder Lookup-Tabelle in SQLite).

## 6. Pipeline und Verantwortung

| Person | Notebook | Ergebnis |
|---|---|---|
| A | `01_data.ipynb` | `data/processed/accidents_clean.parquet`, SQLite-Tabelle `accidents`, Join-/Verlusttabelle |
| B | `02_eda.ipynb` | Kreuztabellen, Anteile, Chi-Quadrat (H1/H2), Karte, Grafiken |
| C | `03_model.ipynb` | Logistische Regression, Odds Ratios, Confusion Matrix, Evaluation |

- Jede Person ändert nur ihr eigenes Notebook. B und C laden ausschliesslich `data/processed/accidents_clean.parquet` und bereinigen nicht erneut.
- **A:** `requests`-Abruf mit URL, Datum, Parametern dokumentiert; Koordinaten und Grenzen im selben CRS; Spatial Join; BFS-Join; Qualitäts-Checkpoint «Rohfälle → räumlich zugeordnet → BFS gematcht → final» absolut und in %; mindestens zwei SQL-SELECTs mit WHERE/GROUP BY und idealerweise ein JOIN über `sqlite3`.
- **B:** Chi-Quadrat via `scipy.stats.chi2_contingency` mit χ², p, Fallzahlen, Prüfung der erwarteten Häufigkeiten. **Zusätzlich Effektstärke** (Anteilsdifferenz/Risk Ratio mit Konfidenzintervall, Cramér's V), weil bei grossen n fast alles signifikant wird. Karte nur mit ausreichenden Fallzahlen pro Region.
- **C:** Stratified Train/Test-Split (z. B. 80/20, fixer `random_state`), Vorverarbeitung nur auf Train fitten. `statsmodels` für Koeffizienten, Odds Ratios, Konfidenzintervalle. **Clusterrobuste Standardfehler nach Gemeinde** (Unfälle sind innerhalb von Gemeinden gruppiert). Vergleich mit Mehrheitsklassen-Baseline, Train-/Testwerte, Klassenungleichgewicht beachten. Optional Interaktion `rural × night`.

## 7. Arbeitsregeln für Claude

- **Erst lesen, dann ändern.** Vor Änderungen an einem Notebook den aktuellen Stand lesen. Keine Notebook-Inhalte aus dem Gedächtnis oder aus abgeschnittenen Ausgaben neu aufbauen.
- **Nur im Notebook der jeweiligen Person arbeiten.** Fremde Notebooks nur auf ausdrückliche Anweisung ändern.
- **Nichts erfinden.** Keine Spaltennamen, API-Parameter, Kategoriecodes oder Zahlen raten. Alles gegen die echten Daten prüfen und Annahmen als Annahme markieren.
- **Reproduzierbar:** feste Seeds, relative Pfade, Notebooks laufen von oben nach unten (Restart & Run All). Keine Pfade wie `C:\Users\...`.
- **Daten:** Rohdaten und grosse Dateien nicht ins öffentliche Repo committen. Nutzungsbedingungen der Quellen prüfen, kleine Beispieldaten und Abrufanleitung im README.
- **Statistik sauber:** Assoziation ≠ Kausalität; Odds Ratios nicht als Wahrscheinlichkeitsverhältnisse bezeichnen; Grenzen nennen (nur Unfälle mit Personenschaden, kein Bezug auf Fahrleistung, zeitliche/räumliche Einflüsse, Klassenungleichgewicht).
- **Grafiken:** beschriftete Achsen, Titel, Fallzahlen, 1–2 Sätze Interpretation pro Grafik («Was ist erkennbar? Was darf man nicht sagen?»).
- **Code-Stil:** Python 3, `pandas`, `numpy`, `requests`, `geopandas`, `shapely`, `matplotlib`, `seaborn`, `scipy`, `scikit-learn`, `statsmodels`, `pyarrow`; Pakete in `requirements.txt` pflegen. Funktionen statt Copy-Paste, kurze Markdown-Erklärungen zwischen den Zellen.
- **Git:** Branches `data`, `eda`, `modeling`; vor der Arbeit `main` pullen; nur lauffähige Notebooks committen; Pull Request nach `main`. Keine Force-Pushes auf `main`. Commit-Nachrichten kurz und auf Deutsch oder Englisch, konsistent.
- **Scope:** kein H3, kein Regression Tree, kein k-means, kein PostgreSQL, solange die Pflichtpunkte nicht verlässlich erfüllt sind.

## 8. KI-Nutzung dokumentieren (wird bewertet)

Jedes Notebook enthält eine Markdown-Sektion **«AI usage log»** mit: Tool und Aufgabe (inkl. Beispielprompt), was geholfen hat, was nicht funktioniert hat (z. B. erfundene Spaltennamen, falscher Join), eigene Prüfung/Korrektur (Vorher/Nachher), was die Person selbst erklären kann. Claude ergänzt bei jeder grösseren Hilfestellung einen Logeintrag-Vorschlag, auch wenn etwas nicht geklappt hat. Aus den Logs entsteht eine Folie (Slide 8) im Video. Ziel ist Verständnis: Jede Person muss ihre Daten, Methoden und Resultate selbst erklären und begründen können.

## 9. Abgabe-Check

- [ ] Eigene API-/Scraping-Erhebung, Parameter dokumentiert
- [ ] 2 Quellen, Join-Quote und Verluste dokumentiert
- [ ] SQLite + SQL aus Python
- [ ] EDA (nicht grafisch + grafisch) inkl. Karte
- [ ] Chi-Quadrat H1/H2 mit p-Wert und Effektstärke
- [ ] Logistische Regression mit Evaluation und Interpretation
- [ ] KI-Log je Notebook und KI-Folie
- [ ] README: Datenquellen, Setup, Ausführungsreihenfolge
- [ ] Öffentlicher GitHub-Link getestet (ausgeloggt)
- [ ] Präsentation 15 Min., PDF mit Anhang «erwartete Punkte» mit Screenshots
- [ ] Moodle: `video_recording_group_XX.mp4`, `materials_group_XX.zip`, `presentation_group_XX.pdf`

## 10. Offene Entscheidungen

- Welche Jahre des ASTRA-Datensatzes (vergleichbare Datenqualität)?
- Behandlung intermediärer Gemeinden (ausschliessen oder eigene Kategorie)?
- Nacht-Definition fix oder zusätzlich saisonal (Sensitivitätsanalyse)?
- Ob `year` als Kontrollvariable ins Modell kommt?
- Gemeinden mit abweichendem Gemeindestand (267 Unfälle): ausschliessen oder per Fusionstabelle zuordnen?
- Begründung für den Zeitraum 2021–2025 festhalten (die Rohdaten reichen weiter zurück).

## 11. Code-Vorlage: Kurs-Repository

Als Vorlage für Python-Code, Notebook-Aufbau und Methoden dient das Kurs-Repo https://github.com/mario-gellrich-zhaw/data_analytics (Branch `master`). Dessen Stil und Techniken sind zu übernehmen, soweit sie zur Aufgabe passen. Inhalte nie 1:1 kopieren, sondern auf unsere Daten anpassen und selbst erklären können.

**Notebook-Konventionen (in allen Kurs-Notebooks gleich):**
- Aufbau: Markdown-Überschrift pro Schritt, Zelle «Libraries and settings» (Imports, `warnings.filterwarnings`, `print(os.getcwd())`), kurze Markdown-Interpretation nach jeder Auswertung.
- Am Ende jedes abgegebenen Notebooks steht die **Footer-Zelle** («Jupyter notebook --footer info--»: OS, Datum, Python-Version). In `01_data.ipynb` bereits vorhanden, in `02_eda` und `03_model` ergänzen.
- Train/Test 80/20 mit `random_state=42`.

**Welche Kurs-Notebooks für welche Aufgabe:**

| Aufgabe | Vorlage im Kurs-Repo |
|---|---|
| API-Abruf mit `requests` (Timeout, `raise_for_status`, JSON) und Folium-Karte | `Week_01/getting_supermarket_locations_WebAPI.ipynb` |
| SQLite anlegen, Tabelle schreiben (`to_sql`), SQL-Abfragen, Verbindung schliessen | `Week_02/apartments_database_Python.ipynb` |
| EDA, Korrelationen | `Week_04/exploratory_data_analysis_apartment_data.ipynb`, `Week_07/correlation_analysis.ipynb` |
| Chi-Quadrat (Kreuztabelle, `chi2_contingency`, erwartete Häufigkeiten, kritischer Wert, p, Interpretation) | `Week_07/analysis_of_contingency_tables.ipynb` |
| statsmodels (`sm.add_constant`, `summary()`, Interpretation von Koeffizienten und p-Werten), Train/Test | `Week_08/linear_regression.ipynb` |
| Klassifikation: `confusion_matrix`, `classification_report`, `RocCurveDisplay`, Feature-Importance | `Week_09/classification_analysis_titanic.ipynb` |
| Zusatz, falls Zeit bleibt: ANOVA, k-means | `Week_06/analysis_of_variance_ANOVA.ipynb`, `Week_10/k-means_clustering.ipynb` |

**Lücken gegenüber dem Kurs-Repo (selbst lösen):** Die logistische Regression kommt dort nicht vor (nur Entscheidungsbaum/Random Forest). Wir nutzen `statsmodels` (`sm.Logit` oder `sm.GLM` mit Binomial) mit Odds Ratios und clusterrobusten Standardfehlern nach Gemeinde, plus Evaluation nach Week 9. Auch `geopandas` und Effektstärken zum Chi-Quadrat fehlen im Kurs-Repo.

**Hinweis zu `requirements.txt`:** Nicht die Pins des Kurs-Repos übernehmen (z. B. `numpy==1.26.4`, `pandas==2.2.2` unterstützen laut meinem Wissen Python 3.13 nicht, unser Footer zeigt 3.13.5). Stattdessen aus der eigenen Umgebung erzeugen (`pip freeze` bzw. nur die direkt genutzten Pakete) und `pyarrow`, `geopandas`, `folium`, `openpyxl` ergänzen.

Die Aufgabenblätter in `Week_LC/` (z. B. `tasks_large_classes_week_08.md`) verlangen, tatsächlich erhaltene Gütemasse zu berichten und Grafiken in eigenen Worten zu beschreiben. Das gilt auch für unsere Ergebnisse: keine Zahlen aus Annahmen oder von KI schätzen lassen.
