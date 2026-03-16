---
author: ["Chrischi"]
title: "KI, die mich kennt - Teil 1: Claude auf dem NAS einrichten"
slug: "ki-die-mich-kennt-claud-auf-nas"
date: "2026-03-16"
draft: true
description: ""
summary: ""
ShowToc: true
TocOpen: false
tags: ["Claude Code", "KI", "Docker", "MCP"]
categories: ["Tech Tipps", "Tutorials"]
cover:
  image: featured-image.webp
  caption: ""
---

Wie nutzt du KI? Du ließt einen Tech Blog, von daher nutzt du vielleicht nicht nur das Web-Interface. Aber die große Mehrheit öffnet einfach den Browser und fängt an zu tippen. Aber das ist bei Weitem nicht die produktivste Art, einen KI-Assistenten in die täglichen Aufgaben zu integrieren. In jeder Session musst du erneut definieren, was genau du möchtest, wer du bist und auch andere Datenquellen können nur eingeschränkt genutzt werden. Deshalb wollte ich eine KI, die mich kennt. Immer verfügbar und mit mit spezifischen Wissen für meine Bedürfnisse. Dies ist Teil 1 der _"KI, die mich kennt"_-Reihe. Lasst uns [Claude](https://code.claude.com/docs/de/quickstart) auf dem NAS einrichten. Du kannst auch [Gemini](https://geminicli.com/) nutzen, dann ändern sich für dich am Ende einige Befehle, da ich in diesem Tutorial Claude nutze. Das Prinzip bleibt aber gleich.

_Disclaimer:_ Wenn du Claude nutzen möchtest, benötigst du entweder mindestens ein kostenpflichtiges Pro-Abo oder einen abrechnungsfähigen API Key.

## Zielarchitektur für die KI, die mich kennt

Mein Ziel ist eine zentrale Claude Anlaufstelle. Ein zentraler Workspace, in welchem ich diverse Themenbereiche (Sport, mein Blog, Home Lab, Recherche, etc...) bearbeiten und mich dabei von einem persönlich eingerichteten KI-Assistenten unterstützen lassen kann.

Was passt da also besser, als mein Ugreen NAS?! Das kann ich via Tailscale von überall erreichen. Meine Vorstellung ist ein dedizierter Ordner in meinem User-Ordner, der als _Workspace_ dient, z.B. "claude-workspace". Darin soll eine KI leben, die alle für mich wichtigen Daten anzapfen kann und jeweils einen dedizierten und vor allem _persistieren_ Kontext zu all meinen Themen hat. Bin ich im Unterordner "HomeLab" soll sie als Experte hierfür agieren, im Unterordner "Blog" als Lektor (keine Angst, ich schreibe alles selbst, lasse aber gegenlesen) und im Unterordner "Sport" als Personal Trainer. Ich will nicht jedes Mal alles wieder neu erklären müssen.

Das ist eigentlich nicht schwierig, aber ich habe viele zu komplexe Wege eingeschlagen und bin gescheitert, bis ich am Ende bei meinem jetzigen Setup landete. Aber dazu gleich mehr. Erstmal die Frage, wie muss die KI eingerichtet sein, um _mich_ beraten zu können? Sie soll ...
- ... einige meiner lokalen Ordner lesen können
- ... Zugriff auf meinen GitHub Account haben
- ... immer die neuste Dokumentation in Entwicklerthemen haben
- ... mein Unifi-Netzwerk administrieren können
- ... mein Smart Home einsehen
- ... meine sportlichen Aktivitäten kennen
- ... vielleicht auch meinen Kalender einsehen?!

Mit einem solchen Setup hätte ich tatsächlich eine KI, die mich kennt. Klingt gut? Angsteinflößend und gut? Dann lasst uns anfangen.

## Disclaimer: KI, die _mich_ kennt!

Dieser Blog Post wird sehr meinungsstark. Ich richte die KI so ein, wie es für _mich_ passt. Das muss für dich überhaupt nicht dasselbe Setup sein. Aber ich hoffe, ich kann dir hier auch zeigen, _wie_ diese Einrichtung aussehen kann, sodass du mein Setup für dich justieren kannst.

## Es lebe Docker! ... oder: Unsere KI lebt in Docker?!

Zu allererst einmal müssen wir uns überlegen, was wir _lokal_ brauchen und was wir von extern aus anbinden. Hier bin ich anfangs falsch abgebogen und wollte unbedingt das [_Docker MCP Gateway_](https://github.com/docker/mcp-gateway) nutzen, aber das beruht zu sehr auf dem Secret Store, den Docker Desktop mitbringt und so verwarf ich meine erste Setup-Idee. Im Nachhinein nicht schlecht, da auf dem NAS auch das Starten und Stoppen von Docker Containern aus anderen Containern heraus ein Schmerz ist. Aber ich schweife ab.

Wir brauchen einen Container, in welchem Claude selbst läuft und dann brauchen wir lokal einen Container als Unifi MCP Server. Denn mein Unifi Controller ist lokal im Heimnetz und so muss auch der entsprechende MCP Server lokal sein.

Glücklicherweise gibt es bereits ein Communityprojekt zu einem [dockerbasierten Unifi MCP Server](https://github.com/sirkirby/unifi-network-mcp) und claude selbst kann in einem [node Container](https://hub.docker.com/_/node) laufen. Beide Container können im selben Netzwerk sein, welches wir ebenfalls einrichten.

Hier also das `docker-compose.yaml`:
```
networks:
  ai-net:
    driver: bridge

services:

  # ---------------------------------------------------------------------------
  # UniFi Network MCP – direkter MCP-Server für UniFi Controller
  # Intern erreichbar: http://unifi-mcp:3000/sse
  # Im LAN erreichbar:  http://<NAS-IP>:3000/sse
  # Die IP-Adresse deines Unifi Controllers, sowie den Port (wenn nicht 443)
  # musst du anpassen.
  # ---------------------------------------------------------------------------
  unifi-mcp:
    image: ghcr.io/sirkirby/unifi-network-mcp:latest
    container_name: unifi-mcp
    restart: always
    ports:
      - "3000:3000"       # Dein interner Port 3000 kann geändert werden, wenn bereits belegt
    environment:
      - UNIFI_HOST=192.168.X.XXX                          # IP deines UniFi Controllers
      - UNIFI_PORT=XXXX                                   # Port meines Controllers
      - UNIFI_USERNAME=${UNIFI_USERNAME}                  # UniFi-Benutzername via .env
      - UNIFI_PASSWORD=${UNIFI_PASSWORD}                  # UniFi-Passwort via .env
      - UNIFI_SITE=default                                # Site-Name (meist "default")
      - UNIFI_VERIFY_SSL=false                            # false bei self-signed oder keinem Zertifikat
      - UNIFI_MCP_HTTP_ENABLED=true                       # HTTP aktivieren
      - UNIFI_MCP_HTTP_TRANSPORT=sse                      # SSE Transport
      - UNIFI_MCP_ENABLE_DNS_REBINDING_PROTECTION=false   # Erlaube alle eingehenden Verbindungen (nur im lokalen Netz nutzen!)
    networks:
      - ai-net

  # ---------------------------------------------------------------------------
  # Claude Code – CLI, erreichbar via: docker exec -it claude-code bash
  # Working Directory ist der Obsidian Vault auf dem NAS.
  # MCP-Verbindungen werden einmalig nach Installation konfiguriert (siehe README).
  # ---------------------------------------------------------------------------
  claude-code:
    image: node:24-slim    # Node 24 ist LTS
    container_name: claude-code
    restart: unless-stopped
    stdin_open: true
    tty: true
    working_dir: /claude-workspace  # So heißt mein Arbeitsverzeichnis bei den Volumes
    command: sleep infinity
    volumes:
      - ./claude-code-home:/root                                  # Persistente Claude-Konfiguration
      - /volume1/@home/{USER}/claude-workspace:/claude-workspace  # Arbeitsverzeichnis für claude. Hier liegen alle Projekte
    depends_on:
      - unifi-mcp
    networks:
      - ai-net
```

Wir nutzen ein eigenes Bridge-Netzwerk und setzen die beiden Container auf. Beim Unifi MCP müsstest du deine Daten in den `environment` Variablen anpassen. Username und Passwort kannst du entweder direkt eintragen oder in eine .env auslagern.

Wichtig ist, es wird kein Account mit einer Zweifaktor-Authentifizierung unterstützt. Lege am besten einen weiteren lokalen Admin in deinem Unifi Controller an und dessen Credentials nutzt du hier. Dein persönlicher Account ist vermutlich synchronisiert mit deinem Unifi-Account. Deshalb deaktiviere nicht 2FA!

Im `claude-code` Container kannst du natürlich weitere Ordner in deinen `volumes` angeben, auf die Claude Zugriff haben soll. Hänge für den Anfang gern `:ro` an das Mapping, um diese Zugriffe erstmal auf `read-only` zu beschränken. So kann claude nichts löschen.

## Was ist MCP?

Nun haben wir bereits das `docker-compose.yaml` und darin ein MCP Server Image hinterlegt. Doch was ist ein MCP Server? _MCP_ steht für _Model Context Protocol_ und ist ein mittlerweile standardisierter Weg, einer KI neue Fähigkeiten bereitzustellen. Oft kommt der Vergleich mit _USB-C_: USB-C ist ein Stecker-Standard über welchen du alles mögliche an deinen Rechner anschließen und dessen Funktionsumfang erweitern kannst. Drucker über USB-C geben deinem Rechner die Fähigkeit zu drucken. Eine Tastatur ermöglicht das Tippen, eine Kamera die Foto- und Videoaufnahme.

Über das _Model Context Protocol_ können wir der KI also Tools an die Hand geben, um beispielweise ein Unifi-Netzwerk zu lesen und zu administrieren. Genau dies macht der Unifi MCP Server. Der Server abstrahiert ganz viele Fähigkeiten und stellt diese per MCP für unsere KI bereit. So können wir die KI fragen _"Hey, wie viele Access Points habe ich?"_ und die KI nutzt MCP, um das richtige Tool im Unifi MCP Server zu finden, welches diese Frage beantwortet.

Claude kennt von Haus aus weder mein noch dein Heimnetzwerk. Mit MCP Server können wir unsere KI aber ermächtigen, Infos aus unserem Unifi Controller zu bekommen - und auch Dinge dort zu tun. Die KI kann auch die Firmware rollierend aktualisieren, wenn gewünscht. Das ist ebenfalls ein verfügbares Tool.

Cool, oder? Über MCP können wir auf sehr vieles zugreifen. So werden auch unsere weitern Fähigkeiten angebunden.

## Docker Projekt bauen

Ich nutze ein Ugreen NAS als zentralen Server. Du kannst das compose file aber überall starten, wo du es möchtest. Ich zeige hier weiterhin _meinen_ Weg. Also, logge dich im Web-Interface deines NAS ein. Du musst natürlich Docker installiert haben, das überspringe ich hier kurz. Du findest es im App Center.

Erst einmal lege ich `/volume1/docker/ai-net` an, denn darin will ich mein Projekt legen. Darin brauchen wir einen Unterordner `/volume1/docker/ai-net/claude-code-home`. Hier werden die Konfigurationsdateien des Containers liegen, sodass diese persistiert werden. Unter anderem werden so auch die hinzugefügten MCP Server gespeichert.

Außerdem muss im _Persönlichen Ordner_ ein Unterordner `claude-workspace` (`/volume1/@home/{USER}/claude-workspace`) angelegt werden. Dies wird der Arbeitsbereich. Bei Ugreen müssen die referenzierten Ordner existieren, wenn Container starten.

Nun kannst du die Docker App starten.