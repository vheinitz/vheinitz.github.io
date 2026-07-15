---
layout: post
title: "Drei Tage autonomer KI-Loop: Wie weit kommt eine KI ohne mich?"
date: 2026-07-15
categories: [ki, automatisierung]
---

Ich wollte eine KI-Pause einlegen und den Computer ausnahmsweise nicht mit in den Urlaub nehmen. Die Claude-Max-Subscription sollte aber trotzdem etwas tun. Also schrieb ich – wiederum mit Hilfe von Pi + Claude – einen KI-Loop und startete ein Experiment: Wie weit kommt KI eigentlich selbständig?

Die Aufgabe: selbständige Weiterentwicklung bzw. Portierung einer realen Anwendung mit ca. 100 kLOC.

Der Loop sollte mit N Agenten zunächst die zehn nächsten TODOs festlegen. Entscheidungsgrundlage waren der Code der alten Software, eine Spezifikation, alte Dokumente, die bestehende Code-Basis und die Historie der KI-Sessions. Sollten die TODOs realen Code beitragen, schrieben M Agenten Unit-Tests und X Agenten den Code – anschließend wurde die beste Lösung ausgewählt.

Die Auswahl der besten Lösung – sei es für TODOs, Unit-Tests, Doku oder Code – läuft so: Nachdem die N Agenten mit ihrer Arbeit fertig sind, begutachten andere M Agenten die Lösungen und vergeben Noten samt Begründung. Danach liest ein unabhängiger Agent die M Beurteilungen und trifft die Entscheidung. Alle parallelen Arbeiten laufen mit Hilfe von Gits Work-Trees.

Einen Tag vor dem Urlaub war ich noch damit beschäftigt, den Loop zu optimieren. Und siehe da: Claudes Max-Subscription reicht bei solchen Experimenten hinten und vorne nicht aus. Ich hatte die Anzahl der parallelen Agenten auf jeweils drei konfiguriert – nach zwei bis drei Loops war das Max-Limit erreicht.

Also musste wieder Pi mit dem DeepSeek-Modell her. Jetzt trifft Claude nur noch die Entscheidungen, welche Lösung besser ist, und wählt aus N Vorschlägen nicht nur den besten aus, sondern versucht, alle guten Ansätze in den besten Vorschlag zu integrieren. Die Lösungsvorschläge macht Pi mit DeepSeek.

Zwei Tage lief mein Loop durch. Ich konnte ihn nur übers Handy anhand des Tokenverbrauchs beobachten, aber nicht eingreifen. Dann kam die Nachbarin die Fische füttern, wunderte sich, warum ein Laptop so viel Lärm macht – und zog den Stecker.

Gestern, nach dem Urlaub, startete ich den Loop wieder und ließ ihn die Nacht durchlaufen.

## Die Bilanz der rund drei Tage

- **260 Loop-Invocations** (`runner.py`-Runs)
- **187 abgeschlossene Tasks** – über die Pipelines `implement`, `write-tests`, `write` (docs), `design`, `fix-docs` und `decide`. Insgesamt liegen 260 Tasks in `docs/plan/done/` (73 davon stammen aus der Zeit davor). Neun liegen aktuell noch in `todo/`.
- **62 Runs hatten einen Stage-Failure** (Build bzw. Tests eines Workers fehlgeschlagen oder Worker-Timeout) – alle haben sich selbst erholt, gemäß dem vorgesehenen Pfad „kein bestandener Worker → Lock freigeben, Task zum Retry in `todo/` belassen". Kein einziger manueller Eingriff nötig.
- **148 neue `.cpp`/`.hpp`/`.h`-Dateien**
- **Diff `.cpp`/`.hpp`/`.h`**: 235 Dateien geändert, +56.178 / −986 Zeilen
- **Alle Dateien** (inkl. Docs, Tests, CMake usw.): 868 Dateien geändert, +103.759 / −11.923 Zeilen

Saubere LOC ohne die umfangreichen Doxygen-Kommentare (insbesondere die Datenmodelle wurden extensiv kommentiert): ca. 35.000. In den drei Tagen entstanden über 500 Commits.

Einzelne Codestellen und Unit-Tests habe ich mir angeschaut – die ergeben Sinn. Nur: Wer soll diese Menge an Code noch verstehen und beurteilen können?

Eine interessante Erfahrung habe ich aus dem Experiment auf jeden Fall mitgenommen: Der erzeugte Code kann beim gleichen Modell mit dem gleichen Prompt sehr unterschiedlich ausfallen. Hat man genug Tokens, lohnt es sich in jedem Fall, die Aufgabe mehrfach parallel bearbeiten zu lassen und die beste Lösung auszuwählen – oder sogar das Beste aus allen Ansätzen zu kombinieren.
