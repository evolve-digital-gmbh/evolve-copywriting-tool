# Übergabebriefing: Evolve Copywriting Pipeline

Dieses Dokument gibt dir den vollständigen Kontext für das Projekt. Lies es komplett, bevor du etwas baust. Wenn etwas unklar ist, frag nach, statt zu raten.

## 1. Ziel

Wir bauen bei Evolve Digital (Performance-Marketing-Agentur für D2C-E-Commerce, ca. 25 Personen) eine Multi-Agent-Pipeline, die Advertorials erstellt. Mehrere spezialisierte Agents arbeiten nacheinander an einem Advertorial, ein Mensch gibt an festen Punkten frei.

Das Ergebnis muss so gebaut sein, dass das ganze Team immer denselben, aktuellen Stand nutzt. Ich (Lukas, Geschäftsführer) baue den Prozess, das Team nutzt ihn.

## 2. Bereits getroffene Entscheidungen

- **Ein Repo, ein Projekt.** Skills und Tests liegen zusammen im selben Repo. Keine getrennten Projekte für Entwicklung und Testing, weil Skill-Stand und Testsets sonst auseinanderlaufen.
- **Keine gemeinsame virtuelle Instanz für interaktive Nutzung.** Der Agent lebt als versionierte Dateien im Git-Repo. Jede Person nutzt ihn mit den eigenen Connectors und Zugangsdaten. Ein gehosteter Layer (n8n oder Agent SDK) kommt höchstens später als dünner Trigger, und nur bei echtem Bedarf.
- **Übergabe zwischen Agents über Dateien**, nicht über Chat-Verlauf.
- **Menschliche Freigabe-Gates** nach dem Konzept und nach dem Entwurf.
- **Bestehende Skills wiederverwenden**, nicht neu erfinden (siehe Abschnitt 5).
- **Regeln nur an einer Stelle pflegen** (Compliance, Copywriting-Grundregeln) und aus den Agents referenzieren, nie in jedem Prompt duplizieren.
- **Testen mit unabhängigem Kontext.** Wer einen Skill gebaut hat, bewertet ihn nicht selbst. Tests laufen in einer frischen Session oder einem Subagent, der nur Skill, Referenz-Briefing und Erwartungskriterien sieht.

## 3. Pipeline-Architektur

Jeder Agent hat genau eine Aufgabe, einen festen Input und einen festen Output.

| Stufe | Aufgabe | Input | Output |
|---|---|---|---|
| 1. Briefing/Research | Kundenwissen und Avatare aus dem Kundenbrain ziehen | Kunde, Produkt | `briefing.md` |
| 2. Typ und Konzept | Advertorial-Typ wählen, Abschnittsplan erstellen | `briefing.md` | `konzept.md` |
| 3. Konzept-Challenge | Plan auf Strategie, Aufbau, Aussagen und Verkauf prüfen | `konzept.md` | `konzept-review.md` (Freigabe oder Rückgabe) |
| **Gate 1** | **Mensch gibt Konzept frei** | | |
| 4. Schreiber | Advertorial abschnittsweise nach Typ-Template schreiben | freigegebenes `konzept.md`, Typ-Template | `entwurf.md` |
| 5. Compliance und Regeln | Health Claims, Preisangaben, Kennzeichnung, Copywriting-Grundregeln prüfen | `entwurf.md` | `compliance-review.md` |
| 6. Headlines/Lektorat | Anzeigentitel, Feinschliff, finaler Abgleich | `entwurf.md`, `compliance-review.md` | `final.md` |
| **Gate 2** | **Mensch gibt Entwurf frei, bevor er zum Kunden geht** | | |

Die Aufteilung ist ein Startvorschlag. Wenn sich beim Bauen zeigt, dass Stufen zusammengelegt oder geteilt werden sollten, schlag es vor und begründe es.

## 4. Repo-Struktur

```
evolve-copywriting-pipeline/
  skills/              (eine Stufe pro Skill, jeweils mit SKILL.md)
  tests/
    referenzen/        (3 bis 5 Referenz-Advertorials mit Briefing)
    erwartung/         (Kriterien, was ein gutes Ergebnis ausmacht)
    ergebnisse/        (Läufe pro Version, zum Vergleichen)
  CLAUDE.md            (Ziel, Konventionen, Übergabeformate)
  CHANGELOG.md
```

## 5. Vorhandene Bausteine

Diese Skills existieren bereits im Evolve-Skill-Setup und sollen Grundlage der Stufen sein:

- `advertorial-typ-template`: Typ-Templates (GitHub `evolve-digital-gmbh/evolve-digital`, Ordner `templates/advertorial-formate`, gespiegelt in Notion)
- `advertorial-template-abgleich`: Konzept-Challenge und Abgleich realer Advertorials gegen Template und Master
- `evolve-regeln`: kundenübergreifende Regeln zu Compliance, Copywriting und Conversion
- `kundenbrain-checker` und `evolve-kunden-wissen`: Kundenwissen in Notion lesen, prüfen und einsortieren
- `advertorial-swipe-eintrag`: Swipefile in Notion
- `pull-advertorial-keyword-recherche`: Keyword-Recherche für Pull-Advertorials
- `evolve-content-publishing`: fertige Markdown-Dateien für Advertorials im Content-Repo

Prüfe zuerst, was davon als Stufe direkt nutzbar ist, was angepasst werden muss und was fehlt. Bevor du eine Stufe von null baust, schau, ob einer dieser Skills sie schon abdeckt.

## 6. Qualitätssicherung und Testing

- Vor dem Bauen der Skills werden **Erwartungskriterien** und **3 bis 5 Referenz-Advertorials** festgelegt. Daran messen wir Qualität.
- Vor jedem Merge läuft die Pipeline über die Referenzen. Ergebnisse landen in `tests/ergebnisse/` pro Version.
- Die Test-Session schreibt ihre Findings in eine Datei. Die Dev-Session arbeitet sie ab.
- Jeder Release bekommt eine Versionsnummer und einen kurzen Eintrag im `CHANGELOG.md`, damit klar ist, mit welcher Version ein Advertorial entstanden ist.

## 7. Phasen

- **Phase 1 (jetzt):** Stufen als Skills sauber definieren (Input, Output, Qualitätskriterien) und manuell nacheinander in Claude Code durchlaufen. Übergabedateien prüfen, mit Referenzen testen, nachschärfen. Am Ende die Kette einmal im Claude-Chat mit den fertigen Skills gegenprüfen, weil Copywriter dort arbeiten könnten (Dateizugriff und Connectors verhalten sich dort anders).
- **Phase 2:** Orchestrator in Claude Code mit Subagents, der die Skills verkettet (Section-Writer bei Bedarf parallel). Verteilung ans Team als Plugin über einen Marketplace.
- **Phase 3:** Nur bei echtem Bedarf ein gehosteter Trigger (Formular oder Slack-Befehl), der dieselben Skills aus demselben Repo nutzt.

## 8. Arbeitsregeln für dich

- Arbeite in Branches, nicht direkt auf main. Änderungen gehen über Pull Request.
- Secrets nie ins Repo. Zugriff auf Notion, Slack und Google läuft über Connectors mit dem Login der jeweiligen Person.
- Jede Stufe einzeln bis zu einem akzeptablen Ergebnis bringen, dann die nächste anhängen und die Übergabe testen. Nicht alle sechs gleichzeitig bauen.
- Fang mit der Stufe an, die den größten Hebel hat (vermutlich Konzept oder Schreiber), und begründe die Wahl.
- Nutze für Texte keine Gedankenstriche (Halbgeviertstriche). Das gilt für alle Dokumente und Prompts, die du schreibst.
- Wenn du eine Annahme treffen musst, schreib sie in `CLAUDE.md` unter "Annahmen", damit ich sie prüfen kann.

## 9. Erste Aufgaben

1. Lies dieses Briefing und lege `CLAUDE.md` mit Ziel, Konventionen und Übergabeformaten an.
2. Erstelle die Repo-Struktur aus Abschnitt 4.
3. Inventarisiere die vorhandenen Skills aus Abschnitt 5 und ordne sie den Stufen zu. Liste Lücken und Anpassungsbedarf auf.
4. Schlage mir Erwartungskriterien für ein gutes Advertorial vor und frage mich nach geeigneten Referenz-Advertorials.
5. Schlage vor, mit welcher Stufe wir starten, und warum.

Zeig mir die Ergebnisse von 3 bis 5, bevor du mit dem Bauen der ersten Stufe beginnst.

## 10. Offene Fragen an mich

- Wer bedient die Pipeline im Alltag: Copywriter im Chat oder Personen mit Claude Code? (Entscheidet, ob Phase 1 reicht oder Phase 2 früher kommt.)
- Welche Advertorial-Typen sollen in der ersten Version abgedeckt sein?
- Welche Kunden und Produkte eignen sich als Referenz?
