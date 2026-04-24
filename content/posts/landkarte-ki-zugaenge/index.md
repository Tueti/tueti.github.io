---
author: ["Chrischi"]
title: "Web, CLI oder als Tool - Eine Landkarte der KI-Zugänge"
slug: "landkarte-der-ki-zugaenge"
date: "2026-04-24"
draft: false
description: "Web-Interface, CLI, Plugins - jede KI lässt sich auf verschiedene Arten nutzen. Welcher Zugang wofür taugt und warum das wichtiger ist, als die Modellwahl."
summary: "Die Diskussion darum, welche KI die beste ist, verfehlt meist den eigentlichen Hebel: den Zugang. Web-Interface, CLI und Integrationen in Drittanbieter-Tools sind drei fundamental unterschiedliche Arten, dieselbe KI zu nutzen - mit großen Unterschieden bei Kontext-Tiefe, Tool-Zugriff und Automatisierbarkeit. Eine Landkarte mit Entscheidungshilfe für vier typische Alltagsszenarien."
ShowToc: true
TocOpen: false
tags: ["Web App", "KI", "CLI", "Cursor", "ChatGPT", "Claude", "Gemini"]
categories: ["Tech Tipps"]
cover:
  image: featured-image.webp
  caption: "Erstellt von Nanobanana/Gemini"
sitemap:
  priority: 0.7
---

Die Frage, welche KI die beste ist, beantwortet sich alle paar Wochen neu und man kann sie super nutzen, um regelmäßig Content mit hohen Klickzahlen zu generieren. Die interessantere Frage stellt jedoch kaum jemand.

## Die falsche Frage

Viel spannender ist die Frage, *über welchen Zugang die KI genutzt wird*. Und genau über die will ich in diesem Beitrag sprechen.

Kleine Vorbemerkung: Ich halte den Beitrag weitgehend *anbieteragnostisch*. Die Zugänge, um die es geht, bieten im Grunde alle großen Player in ähnlicher Form an - ChatGPT, Claude, Gemini, Mistral und Co. unterscheiden sich in Nuancen. Es gibt Differenzen, welche Tools nun genau in wessen Web-Interface verfügbar sind und wie mächtig die jeweilige CLI ist, aber die *Struktur* ist überall gleich.

## Die drei KI-Zugänge

Grob lassen sich drei Zugangsarten unterscheiden:

1. **Web-Interface** - der klassische Zugang, den sicherlich die allermeisten benutzen, ist das Chat-Fenster. Entweder im Browser oder als Desktop- bzw. Mobile-App. Hier gibt es reine Chats und mittlerweile auch "Chat-Sammlungen", die man als Projekt nutzen kann.
2. **CLI / Terminal-Tools** - Claude Code, Gemini CLI, Codex und auch lokale Tools wie Ollama oder LMStudio. Alles, was wir im Terminal aufrufen und das dann direkten Zugriff aufs Dateisystem unseres Rechners hat.
3. **Integrationen in Drittanbieter-Tools** - IDE-Plugins (VS Code Copilot, Cursor, Antigravity), Automatisierungs-Tools (n8n, Zapier, Make) und alles andere, was die KI über eine API eingebunden in fremder Software nutzt.

Das sind drei fundamental unterschiedliche Arten, mit derselben KI zu arbeiten. Dabei will ich nicht bewerten, was "der beste" oder "richtige" Weg ist. Jeder dieser Wege hat seine Vor- und Nachteile und je nach Szenario passt das eine besser als das andere. Ich möchte aber, dass wir alle verstehen, welche Art für welches Szenario sinnvoll wäre.

In [meinem letzten Blogpost](/claude-als-persoenlicher-assistent/) habe ich mein Claude-Code-Setup beschrieben und konnte hoffentlich darstellen, wie anders sich eine KI anfühlen kann, wenn man einen anderen Weg der Nutzung wählt. In diesem Beitrag will ich einen Schritt zurückgehen und die Landkarte der groben Nutzungsmöglichkeiten einmal erklären. Denn es ergibt einen Unterschied, ob man die KI via Web-Oberfläche, in einem Command Line Interface (CLI) oder per Integration in einem Tool nutzt.

## Zwei Achsen: Client und Kontext-Tiefe

Um die drei Zugänge sinnvoll einzuordnen, hilft es, sie auf zwei Achsen zu legen.

**Achse 1: Wo läuft die KI?**
Das ist eine der drei eben beschriebenen Zugangsarten. Im Browser oder als App, im Terminal oder eingebettet in Drittsoftware. Das entscheidet maßgeblich, wie du die KI im Alltag erreichst, auf welchem Gerät du arbeitest und wie schnell du eine Session startest.

**Achse 2: Kontext-Tiefe - Wie viel weiß die KI, bevor du tippst?**
Das ist die spannende Achse. Sie reicht von _"nichts, jede Session ist ein weißes Blatt"_ über _"ein bisschen Account-Memory, aber wir wissen es nicht genau"_ und _"Projektdateien, die ich manuell gepflegt habe"_ bis zu _"voller Zugriff auf mein Dateisystem plus alle konfigurierten Tools"_.

Die Zugänge:

```
                    Kontext-Tiefe
                         ▲
                         │            ┌─────┐
    Filesystem + Tools   │            │ CLI │
                         │            └─────┘
    Projektdateien       │  ┌────────────┐
                         │  │ Projekte   │        ┌──────────────┐
                         │  │ (im Web)   │        │ Integration  │
    Account-Memory       │  └────────────┘        │ (n8n, IDE)   │
                         │  ┌──────┐              └──────────────┘
    Nur Session          │  │ Web  │
                         │  └──────┘
                         └───────────────────────────────────▶ KI-Zugang
                            Browser    Terminal    Drittsoftware
                                        Client

-----
ASCII Art erstellt mit Hilfe von Claude Code
```

Die Zugänge setzen also unterschiedliche Fokuspunkte. Eine leicht zugängliche KI via Web/App hat wenig Kontext-Tiefe. Will oder braucht man diesen persistierten und vor allem _kontrollierten_ Kontext, wird der Zugang komplexer.

## Die Faktoren, die wirklich unterscheiden

Wie können wir nun entscheiden, was der für uns richtige Zugang ist? Die spannende Achse war die **Kontext-Tiefe**, das _Wissen_, das die KI bereits über uns hat, bevor wir tippen. Das klingt wie eine kleine Nebeninfo, ist aber ein essentieller Unterschied. Du musst nicht jedes Mal bei null starten und alles erklären - verschiedene Chats zum selben Thema können auf derselben Basis loslegen. Du musst so nicht jedes Mal wieder erklären, welchen Sport du betreibst und was deine körperliche Verfassung ist, bevor du dir Trainingstipps holst.

Im Web-Interface hat die KI standardmäßig _gar keinen_ Kontext bei neu gestarteten Chats. Allerdings kann man mittlerweile in den Account-Einstellungen einen Systemprompt hinterlegen. Dieser sollte allerdings maximal generisch sein, denn hinterlegst du dort Spezifika zu bestimmten Themen, füllst du das "Kontextfenster" (also was die KI sich pro Session merken kann) für jeden neuen Chat und so "vergisst" die KI schneller die geschriebenen Details. Also so ein Systemprompt sollte nicht als Roman genutzt werden.

Zusätzlich dazu haben manche KIs _Account-Memory_. Sie speichern gewisse Dinge über Chats hinweg ab, wobei wir nicht erfahren, was genau sich die KI dann merkt und als "wertvoll genug zum Merken" einstuft.

Es gibt in den Web-Interfaces mittlerweile auch _Projekte_. Diese adressieren genau das Thema des _spezifischen Wissens_. Du kannst in diesen Projekten zusätzliches, persistiertes Wissen definieren und alle Chats, die dann im Kontext des Projektes gestartet werden, haben zusätzlich zum System-Kontext noch den Projekt-Kontext.

Als CLI hingegen hat die KI _Dateizugriff_. Du kannst der KI also Zugriff auf deine Dateien geben, der Inhalt kann automatisch in Sessions geladen werden und zum Abschluss einer Session können neue Erkenntnisse ganz bewusst persistiert werden, sodass die KI sich beim nächsten Mal "erinnert". Ein solches Setup beschreibe ich in meinem Beitrag zu [Claude als persönlichen Assistenten](/claude-als-persoenlicher-assistent/). Auch in Projekten kann man (manuell) den Kontext regelmäßig aktualisieren. Dürfte man nur nicht vergessen. Aber inhaltlich muss sich die Kontext-Tiefe der beiden Zugänge nicht zwingend unterscheiden.

Integrationen liegen irgendwo dazwischen - der Systemprompt wird meist vom genutzten Tool vorgegeben, dynamischer Kontext kommt aus dem laufenden Workflow. Bei solchen Tools geht es meist nicht um persönliche Kontext-Tiefe, sondern um das Verständnis der zu erledigenden Aufgabe.

**Tool-Zugriff** ist der nächste wichtige Faktor auf der Achse. Das bezieht sich auf die Frage, was die KI eigentlich tun kann. Im Web-Interface beschränkt sich das auf die Websuche, möglicherweise Code-Ausführung in der Sandbox und Tool-Nutzung per Remote-MCPs über HTTP. Lokale Tools gehen _nicht_! Im CLI kommen Shell-Zugriff (also die Möglichkeit der Systemadministration), lokale MCP-Tools und direktes Editieren von Dateien dazu - das ist qualitativ ein anderes Level. Integrationen haben wiederum nur die Tools, die das Programm selbst bereitstellt: n8n-Knoten, IDE-Funktionen, API-Aufrufe aus dem Workflow heraus.

Hier ist der ganz wichtige Unterschied: Einem Web-Interface kann ich (ohne großen Aufwand) nicht einfach Zugriff auf mein Heimnetzwerk geben oder Zugriff auf meine lokalen Dateien auf dem Rechner. Die oben eingebettete ASCII Art hat Claude Code direkt in diese Datei geschrieben - kein Copy/Paste. Der KI via CLI kann ich also genau diesen tiefen Zugriff ermöglichen. Durch diesen Dateizugriff kann auch die automatische Aktualisierung des Kontexts ermöglicht werden.

**Automatisierbarkeit.** Kann ich das Ganze ohne menschliches Eingreifen laufen lassen? Chat per Web-Interface: nein, das ist per Definition ein Chat. CLI: teilweise, per Script, Cron-Job und Headless-Modus. Integration: ja, das ist oft genau der Punkt. Auch hier wird der Nutzungsunterschied klar.

**Mobilität und Multi-Device.** Komme ich vom iPhone dran? Web-Interface: absolut, alles synchronisiert cloudseitig. CLI: nur mit Aufwand, siehe mein [letzter Beitrag](/claude-als-persoenlicher-assistent/). Integrationen: je nach Tool - n8n-Workflows laufen unabhängig vom Gerät, IDE-Plugins sind ans Entwicklungsgerät gebunden.

**Datenschutz und Datenfluss.** Den Punkt nehme ich noch mit auf, denn ich möchte betonen, dass auch bei der lokalen Ausführung vieler KIs (CLI, Automatisierungstools) trotzdem Infos beim Hersteller der KI landen. Beim Web-Interface ist es klar, es liegt alles beim Anbieter der KI. Beim CLI ist die Datenbewegung zumindest lokaler - die Dateien selbst bleiben auf deiner Maschine, nur das, was im Prompt landet, wird gesendet. *Lokal installiert* heißt **nicht** *lokal berechnet*. Das Modell läuft trotzdem in der Cloud beim Anbieter, solange du kein explizit lokales Modell heruntergeladen hast (beispielsweise mit Ollama).

**Setup-Aufwand.** Web-Interface: keiner, einfach Account anlegen. Projekte: einmal anlegen, Dateien hochladen, fertig. Nicht vergessen, dynamischen Kontext hin und wieder zu aktualisieren. CLI: je nach Ambition zwischen einer Stunde und einem langen Wochenende mit Docker, NAS und Custom-CLAUDE.md - ich verlinke meinen vorigen Post nicht noch mal. Integrationen: stark abhängig vom Tool - ein VS-Code-Plugin ist in drei Klicks drin, ein sauberer n8n-Workflow mit mehreren Knoten kann einen halben Abend dauern.

## Die Vergleichs-Matrix

Zusammengefasst:

|                        | Web-Interface            | CLI / Terminal              | Integration (IDE, n8n, ...)       |
|------------------------|--------------------------|-----------------------------|--------------------------------|
| **Kontext-Tiefe**      | Systemprompt + Memory + Projekte (statisch)  | Kontext-Dateien + ganzer Workspace     | Vorgegeben vom Tool            |
| **Tool-Zugriff**       | Eingebaut + Remote-MCP | Lokale Tools + Shell + MCP   | Tools der Applikation, ggf. MCP    |
| **Automatisierbar**    | Nein                     | Per Script / Headless       | Ja, nativ                      |
| **Mobilität**          | Sehr gut - überall       | Nur mit Remote-Setup        | Gerätegebunden (IDE) oder egal (n8n) |
| **Datenschutz**        | Alles beim Anbieter      | Dateien lokal, Prompt zum Anbieter | Abhängig vom Wirt              |
| **Setup-Aufwand**      | Minimal                  | Hoch bis sehr hoch          | Mittel                         |
| **Ideal für**          | Spontane Fragen, kleine Projekte          | Entwicklung, Administration, tief persönliches Setup | Wiederkehrende Workflows       |

Das Muster ist klar: Web-Interface bietet einen schnellen Zugriff, aber die geringste Integration in _dein Leben_. Per CLI ist die KI deutlich weniger intuitiv zu nutzen, kann aber potentiell ein wirklich _persönlicher Assistent_ werden. In Integrationen ist es sehr spezifisch für dein explizites Vorhaben. Je weiter links, desto schneller bist du produktiv - aber die KI kann eben auch nur *reden*, nicht *machen*.

Wichtig: Keine Spalte ist die beste. Das sind drei Werkzeuge für drei Zwecke.

## Vier KI-Szenarien aus dem Alltag

Damit das Ganze greifbar wird, hier vier typische Situationen - und welcher Zugang dafür mein klarer Favorit ist.

**Schnelle Frage unterwegs.** Web-Interface in der Mobile App gewinnt. Session-Wegwerfkontext reicht völlig, ich brauche keinen Zugriff auf Dateien, ich will direkt tippen können. Alles andere wäre Overkill.

**Ein Thema, das ich regelmäßig mit denselben Unterlagen bearbeite.** Ich habe konkrete Recherche-Themen, die ein gewisses Setup voraussetzen. Beispielsweise Schach-Training, die Ressourcen können alle über das Web erreicht werden und ich brauche keine lokalen Dateien. Ich möchte aber alles auf _dieselben Referenzdokumente_ beziehen und darauf basierend arbeiten. Dann _Web-Projekte_. Einmal Dokumente hochladen, einmal einen Systemprompt schreiben, und ab da kennt die KI mich in diesem spezifischen Bereich schon ein Stück weit. Für Standard-Nutzer ist das der große Schritt nach vorn gegenüber einem isolierten Chat.

**Persönliche Projekte, wie mein HomeLab oder Sport.** Ich administriere mein HomeLab, werte Logs aus, checke Container. Das ist alles lokal und hier gewinnt das CLI deutlich. Ohne Dateisystem-Zugriff und echte Werkzeuge müsste ich wild zwischen Chat-Fenster und meinen Dateien copy-pasten. Ich müsste selbst notieren, was wir getan haben und jedes Mal dem Chat wieder darlegen, was der aktuelle Zustand ist. Das ist genau der Punkt, an dem der Aufwand für ein sauberes CLI-Setup sich wirklich auszahlt.

**Wiederkehrende Automatisierungen ohne manuellen Anstoß.** Ein RSS-Digest, der jeden Morgen genau die Nachrichtenquellen zusammenfasst, die mich interessieren und mir dies als _Morning Briefing_ in mein E-Mail-Postfach schickt. Dann gewinnt n8n mit seinem KI-Modul.

## Kombinationen statt Entweder-Oder

Ein Punkt, der in den meisten *"KI-Vergleich"*-Artikeln komplett fehlt: In der Praxis nutzt man nicht nur einen Zugang, sondern mehrere parallel. Und das ist auch richtig so.

Mein eigenes Setup sieht aktuell grob so aus:

Ich nutze _VS Code_ mit einem Claude Plugin, um eine Mobile App zu entwickeln. Ich habe _n8n_ laufen, um Automatisierungen abzubilden und ich brainstorme über meinen Sport, mein HomeLab und auch meinen Blog mit dem CLI-Tool _Claude Code_. Ich nutze aber auch ausgiebig die mobile Claude App (und somit letztlich die Web-Version) für einzelne "Quick Shot Chats" und für Projekte, die keinen lokalen Kontext benötigen.

Interessanterweise können bestimmte Dinge sogar zwischen den Zugängen geteilt werden. Die MCPs, die ich in meinem Claude-Code-Container aktiviert habe, liefern mir teilweise auch im Web-Interface Tools - weil ich sie über mein Abo einmal verbunden habe. Ein Google-Calendar-MCP, den ich für das CLI einrichte, taucht dann auch bei claude.ai auf. Das macht die Grenzen zwischen den Zugängen an manchen Stellen durchlässiger, als man zunächst denken würde.

KI wird also richtig sinnvoll, wenn man den _richtigen Zugang_ findet. Oft wirkt eine KI doofer, als sie wirken sollte, weil man selbst nicht das entsprechende Setup gewählt und somit der KI nicht die optimale Plattform geboten hat.

## Die unbequemen Wahrheiten

Zum Schluss noch ein ehrlicher Blick auf das, was in den meisten Artikeln weggelassen wird - weil es weniger marketingfreundlich ist.

**Projekte altern unbemerkt.** Das schönste Web-Projekt mit den besten Dateien wird innerhalb von ein paar Monaten veraltet sein, wenn du die Dokumente nicht aktiv pflegst. Und im Gegensatz zu einem Git-Repo mit `CLAUDE.md` und Versionierung gibt es hier keine History, kein Diff, keinen Commit. Du bemerkst nicht, dass deine Dateien drei Monate alt sind - bis die KI dir plötzlich seltsame Antworten gibt.

**Account-Memory ist intransparent.** Die Funktion, dass sich deine KI Dinge über dich über Chats hinweg "merkt", ist praktisch. Und gleichzeitig undurchsichtig. Was genau wird gespeichert? Wann? Kann man das nachlesen? Bei den meisten Anbietern geht das nur eingeschränkt. Das ist okay, solange man sich dessen bewusst ist und es nicht für Dinge benutzt, bei denen man einen konkreten Kontext voraussetzen muss.

**CLI-Setups brauchen Wartung.** Mein eigenes Docker-Setup auf dem NAS ist ziemlich stabil, aber es braucht Wartung. Erst kürzlich bin ich von Tailscale zu Twingate für meinen Remote-Zugriff gewechselt, da mein Tailscale-Setup nicht mehr arbeiten wollte und ich so remote nicht auf mein lokales Claude-Setup kam. Die lokalen MCP-Server müssen eingerichtet werden und Zugriffs-Token können ablaufen und müssen neu konfiguriert werden. Wer das nicht regelmäßig pflegen will, ist mit einem Web-Interface mittelfristig glücklicher.

**Integrationen können teuer werden.** Wenn ein n8n-Workflow jede Minute einen API-Call macht statt einmal pro Stunde, merkst du das erst an der Rechnung. Bei Abo-basierten Zugängen gibt es ein Limit, bei API-basierten nicht. Also immer Rate-Limits und Cost-Alerts einrichten - *besonders* bei autonomen Workflows.

**Lokal heißt nicht privat.** Ganz wichtiger Punkt: Nur weil das CLI auf deinem Rechner läuft, heißt das nicht, dass deine Daten privat bleiben. Das Modell selbst läuft bei Anthropic, Google, OpenAI - wo auch immer. Nur *wirklich* lokale Modelle (Ollama, LMStudio mit selbst geladenen Modellen) bleiben komplett bei dir. Und die sind leistungstechnisch noch nicht auf dem Niveau der großen Cloud-Modelle.

## Fazit

Kein Zugang ist dem anderen überlegen, sie lösen aber unterschiedliche Probleme. Und genau deshalb ist die Frage *"welche KI ist die beste?"* so unvollständig. Interessanter ist, *über welchen Zugang nutze ich diese KI?* Denn eine KI, die ich über das Web-Interface in der Mobile App nutze, ist nicht dieselbe wie die KI vom gleichen Anbieter im Terminal mit vollem Filesystem-Zugriff. Das Modell ist gleich, der Hebel ein komplett anderer.

Wenn du tiefer in den CLI-Pfad einsteigen willst, hab ich dazu [im letzten Beitrag](https://tueti.space/claude-als-persoenlicher-assistent/) alles zu meinem Setup beschrieben und ich hoffe, ich konnte dir die Landkarte der KI-Zugänge näherbringen. Vielleicht probierst du mal ein Web-Projekt und steigst tiefer in die Personalisierung deines KI-Modells ein. Es kann sich echt lohnen. Bis dahin, viel Spaß beim Basteln damit!