# Evolve Copywriting Pipeline

Multi-Agent-Pipeline für die Erstellung von Advertorials bei Evolve Digital. Den vollständigen Kontext findest du in `UEBERGABEBRIEFING.md`. Lies diese Datei und das Briefing zu Beginn jeder Session.

## Ziel

Mehrere spezialisierte Agents erstellen nacheinander ein Advertorial. Ein Mensch gibt nach dem Konzept und nach dem Entwurf frei. Das ganze Team nutzt immer denselben, aktuellen Stand aus diesem Repo.

## Konventionen

- Ein Repo, ein Projekt. Skills und Tests liegen zusammen.
- Arbeit in Branches, Änderungen über Pull Request, nichts direkt auf main (außer dem initialen Setup).
- Jede Stufe hat genau eine Aufgabe, einen festen Input und einen festen Output.
- Übergabe zwischen Stufen über Dateien (`briefing.md`, `konzept.md`, `entwurf.md`, `review.md`), nicht über Chat-Verlauf.
- Regeln (Compliance, Copywriting) werden nur an einer Stelle gepflegt und aus den Skills referenziert, nie dupliziert.
- Keine Secrets im Repo. Zugriff auf Notion, Slack und Google läuft über Connectors mit dem Login der jeweiligen Person.
- Texte ohne Gedankenstriche (Halbgeviertstriche) schreiben.
- Tests laufen in einer frischen Session, die nur Skill, Referenz-Briefing und Erwartungskriterien sieht.

## Übergabeformate

Noch zu definieren pro Stufe (Input, Output, Qualitätskriterien). Eintragen, sobald festgelegt.

## Entscheidungen

Hier landen getroffene Entscheidungen mit Datum, damit jede Session und jede Person dasselbe Wissen hat.

## Annahmen

Annahmen, die getroffen wurden und von Lukas geprüft werden sollen.

## Offene Fragen

- Wer bedient die Pipeline im Alltag: Copywriter im Chat oder Personen mit Claude Code?
- Welche Advertorial-Typen sollen in Version 1 abgedeckt sein?
- Welche Kunden und Produkte eignen sich als Referenz?
