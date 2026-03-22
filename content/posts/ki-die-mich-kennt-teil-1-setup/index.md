---
author: ["Chrischi"]
title: "KI, die mich kennt - Teil 1: Claude auf dem NAS einrichten"
slug: "ki-die-mich-kennt-claude-auf-nas"
date: "2026-03-17"
draft: false
description: "Wie ich Claude Code per Docker auf meinem NAS installiere und per MCP-Servern mit Unifi, GitHub und Context7 verbinde — für eine KI, die mich wirklich kennt."
summary: "Eine persönliche KI, die Zugriff auf das Heimnetz, GitHub und aktuelle Entwicklerdokumentation hat — erreichbar von MacBook, iPad und iPhone. Dieser Post zeigt, wie man Claude Code per Docker auf dem NAS einrichtet und mit MCP-Servern ausstattet, die aus einem generischen CLI-Tool einen echten persönlichen Assistenten machen."
ShowToc: true
TocOpen: false
tags: ["Claude Code", "KI", "Docker", "MCP"]
categories: ["Tech Tipps", "Tutorials"]
cover:
  image: featured-image.webp
  caption: "Erstellt von Nanobanana/Gemini"
---

KI ist aktuell das Thema, das mich am meisten fasziniert. Ich habe das Glück, dass ich meinen beruflichen Arbeitsablauf stark durch KI unterstützen lassen kann. Das macht mir zum einen total Spaß, nimmt mir aber auch viele "Zeitfresser" ab. Privat wollte ich mir deshalb auch eine Art "persönlichen, digitalen Assistenten" aufbauen. Eine KI, die mich kennt, am besten lokal auf meinem NAS, damit ich sie von allen Geräten aus nutzen kann: MacBook, iPad, iPhone, egal. Kann ich eine SSH-Session initiieren, kann ich meine KI nutzen.

Bevor ich tiefer in den Blog Post einsteige, lass mich kurz schreiben, dass dieser Post meinungsstark ist. Ich werde ein Setup aufbauen, dass zu _mir_ passt. Das ist ganz wichtig. Auch, wenn dieser Post wie ein Tutorial wirkt, so ist ein Eins-zu-Eins-Nachbau vermutlich nur in wenigen Fällen sinnvoll. Eigentlich nur, wenn du exakt dieselben Interessen hast, wie ich. Denn mein Ziel ist eine _persönliche_ KI. Ich will dich aber ermächtigen, dir selbst eine solche KI zu bauen und dir Ideen geben, wie ein solches Setup aussehen kann und welche Möglichkeiten es gibt.

## Zielarchitektur für die KI, die mich kennt

Meine zentrale KI wird [Claude Code](https://code.claude.com/docs/de/quickstart), das CLI Tool von [Claude](https://claude.ai/). Um Claude als Konsolentool zu nutzen, brauchst du zwingend mindestens ein _Pro_-Abo oder einen abrechnungsfähigen API Key. Mit dem [Gemini CLI](https://geminicli.com/) gibt es eine sehr gute Alternative, die ein wirklich großzügiges Gratis-Tier besitzt. Mein Tutorial basiert auf "Claude", das Konzept ist aber mit beiden KIs umsetzbar. Die Befehle sind ein wenig angepasst - aber nichts, was eine KI für dich umbiegen könnte 😉.

Aber was macht Claude in meinem Setup _persönlich_, wie mache ich aus Claude eine KI, die mich kennt? Als erstes muss ich Claude bestimmte _Fähigkeiten ermöglichen_. Es wäre wirklich cool, wenn Claude ...
- ... auf meine lokalen Dateien zugreifen kann (klar, ein No-Brainer).
- ... Zugriff auf meinen GitHub Account hätte.
- ... Zugriff auf meinen Unifi Controller (und somit auf die Administration meines Heimnetzwerkes) hätte.
- ... mein Smart Home einsehen kann.
- ... meinen Kalender kennt?!
- ... meine sportliche Leistung einschätzen kann.

Das wäre doch schon ein starker Anfang, um Claude für persönliche Dinge zu nutzen. Trainingspläne erstellen, Rolling Firmware Updates meiner Unifi-Geräte, GitHub Issues ansehen und den Code dazu prüfen. Das ist schon deutlich mehr, als einfach das generische Web-Interface im Browser zu nutzen und jedes Mal wieder einen mehr oder weniger soliden Kontext einzubringen.

All diese Fähigkeiten können wir per _MCP Server_ bereitstellen.

## Was ist MCP?

Was ist ein _MCP Server_? _MCP_ steht für _Model Context Protocol_ und ist ein mittlerweile standardisierter Weg, einer KI neue Fähigkeiten zu ermöglichen. Oft kommt der Vergleich mit _USB-C_: USB-C ist ein Stecker-Standard über welchen du alles Mögliche an deinen Rechner anschließen und dessen Funktionsumfang erweitern kannst. Drucker über USB-C geben deinem Rechner die Fähigkeit zu drucken. Eine Tastatur ermöglicht das Tippen, eine Kamera die Foto- und Videoaufnahme.

Über das _Model Context Protocol_ können wir der KI ebenfalls Tools an die Hand geben, um Dinge zu tun. Über einen _Unifi MCP Server_ könnte eine KI _lernen_ (oder direkt wissen), wie die heimischen Unifi-Geräte auszulesen und zu administrieren sind. So könnte man fragen, _"Hey, wie viele Access Points habe ich?"_ und die KI nutzt den per MCP angebundenen _Unifi MCP Server_, der das richtige Tool beinhaltet, um diese Frage zu beantworten.

So erhält Claude Zugriff auf das Heimnetz. Ich mag mich als Nerd outen, aber ich finde das ziemlich cool!

Über MCP können wir auf sehr vieles zugreifen. So werden auch unsere weiteren Fähigkeiten angebunden.

## Es geht los: Lokales Docker-Setup

Mein Setup umfasst zwei Ebenen: Die lokale Ebene und die Service-Ebene. Die lokale Ebene beinhaltet die Tools, die wir lokal bereitstellen müssen, während die Service-Ebene ebendie Tools umfasst, die wir über das Internet anbinden. Tools, die wir nicht selbst hosten müssen.

Lokal brauchen wir einen Host, auf dem Claude installiert werden kann und einen Host, um den Unifi MCP Server zu betreiben. Da unser Netzwerk typischerweise lokal ist, brauchen wir auch den MCP-Server lokal. Glücklicherweise können wir beides über [_Docker_](https://www.docker.com/) bereitstellen. Es lebe Docker!

Für Claude gibt es kein eigenes Image, aber wir können einen schlanken [Node Container](https://hub.docker.com/_/node) nutzen. Für unsere angebundene Heimnetz-Fähigkeit gibt es glücklicherweise bereits einen [Unifi MCP Server als Communityprojekt](https://github.com/sirkirby/unifi-network-mcp).

Beide Container lasse ich auf meinem NAS im selben Netzwerk laufen, das ich `ai-net` nenne.

Allerdings möchte ich meinen Claude Container so vorbereiten, dass ich gewisse Pakete (`git`, `vim`, `nano`) und Claude nicht immer manuell nach jedem Neustart installieren muss. Deshalb will ich ein eigenes `Dockerfile` nutzen. Lege eine Datei namens `Dockerfile` (ohne Dateiendung) an. Teste per `id <USERNAME>` einmal, was deine UserID (UID) und deine GruppenID (GID) sind und dann befülle dein Dockerfile folgendermaßen:
```bash
FROM node:24-slim

USER root

ARG UID=<DEINE UID>
ARG GID=<DEINE GID>

# System-Pakete installieren
RUN apt-get update && apt-get install -y \
    curl bash git nano vim-nox \
    && rm -rf /var/lib/apt/lists/*

# Falls eine Gruppe mit id der GID bereits existiert, wird diese zu 9999 geändert (im Container okay)
RUN if getent group ${GID} > /dev/null 2>&1; then \
        groupmod -g 9999 $(getent group ${GID} | cut -d: -f1); \
    fi && \
    usermod -u ${UID} node && groupmod -g ${GID} node

# User `node` auf eigene UID und GID ändern
RUN usermod -u ${UID} node && groupmod -g ${GID} node

# Claude als root installieren → landet in /root/.local/bin/claude
RUN curl -fsSL https://claude.ai/install.sh | bash

# Global verfügbar machen für alle User
#RUN ln -s /root/.local/bin/claude /usr/local/bin/claude
RUN cp /root/.local/bin/claude /usr/local/bin/claude && \
    chmod 755 /usr/local/bin/claude

# Ab hier als node-User
USER node

# PATH für node-User setzen (für interaktive Shells)
RUN echo 'export PATH="$HOME/.local/bin:$PATH"' >> /home/node/.bashrc
```

Wir installieren hier als `root` unsere Pakete und auch Claude. Claude wollen wir dann aber unter dem User `nano` ausführen und da Claude später auch Dateien anlegen können soll, die wir auf dem NAS lesen wollen, braucht der lokale User `node` dieselbe UID und GID, wie unser Nutzer auf dem NAS. Denn dann werden die Dateien im Container auf diese IDs berechtigt und wir haben später keine Leseschwierigkeiten.

Danach mein `docker-compose.yaml`, beachte bitte die Inline-Kommentare. Ein paar Anpassungen müssten vorgenommen werden. Gerade beim Username und Passwort empfehle ich stark, einen zusätzlichen, lokalen Admin im Unifi Controller anzulegen, der ausschließlich hierfür verwendet wird. Denn es darf keine Zweifaktor-Authentifizierung eingeschaltet sein. Den Rest gern mit eigenem Setup anpassen.
```yaml
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
      - UNIFI_PORT=XXXX                                   # Optional Port meines Controllers, wenn nicht 443
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
    build:
      context: .
      dockerfile: Dockerfile
      args:
        UID: <DEINE UID>
        GID: <DEINE GID>
    container_name: claude-code
    restart: unless-stopped
    stdin_open: true
    tty: true
    working_dir: /claude-workspace  # In diesem Verzeichnis startet meine Session, wenn ich mich einlogge
    command: sleep infinity
    volumes:
      - ./claude-code-home:/home/node                             # Persistente Claude-Konfiguration
      - /volume1/@home/{USER}/claude-workspace:/claude-workspace  # Arbeitsverzeichnis für claude. Hier liegen alle Projekte. Ist auch das "working_dir" in Zeile 44
    depends_on:
      - unifi-mcp
    networks:
      - ai-net
```

Mit diesem Setup ziehst du zwei Container hoch. Der `claude-code` Container enthält aber noch keine Claude CLI, die müssen wir gleich nachziehen.

Du kannst natürlich außerdem im `claude-code` Container weitere Ordner in deinen `volumes` angeben, auf die Claude Zugriff haben soll. Hänge für den Anfang gern `:ro` an das Mapping, um diese Zugriffe erstmal auf `read-only` zu beschränken. So kann claude nichts löschen.

## Das NAS vorbereiten

Ich ziehe mein Setup auf meinem Ugreen NAS hoch und werde es dort als _Projekt_ in der hauseigenen Docker App starten. Einfach, damit ich es in der Docker UI sehe. Damit Docker die Container starten kann, brauche ich also im Vorfeld alle referenzierten Ordner.

Ich lege meine Dockerprojekte alle unter `/volume1/docker` ab, dieses also unter `/volume1/docker/ai-net` und hier brauche ich folgende Struktur:
```bash
/volume1/docker/ai-net/
  |-- claude-code-home/
  |-- docker-compose.yaml # Meine oben gezeigte Datei
  |-- Dockerfile          # Das Setup unserer node-Images
  |-- .env                # Hier speichere ich meine Unifi Zugangsdaten
```
Nun kann in der Docker App auf dem NAS unter _Projekte_ ein neues Projekt angelegt werden. Als Name wähle ich `ai-net` und als Speicherort den gleichnamigen Ordner. Ich importiere die `docker-compose.yaml` (das sollte schon vorgeschlagen werden) und schon werden die beiden Container und das Netzwerk erstellt.

## Claude zum Leben erwecken

In den Einstellungen wirst du vermutlich noch SSH aktivieren müssen, damit du dich per SSH auf deinem NAS einloggen kannst. Ob du SSH dauerhaft aktiviert haben möchtest, bleibt dir überlassen.

```bash
ssh <USERNAME>@<NAS-IP>           # Per SSH auf dem NAS einloggen
docker exec -it claude-code bash  # Eine Shell-Sitzung im Container starten
```

Da wir im Vorfeld per Dockerfile bereits claude installiert haben, teste das Setup gern mit `claude --version`, was dir einen sinnvollen Output geben sollte! Wenn das so ist, meinen Glückwunsch, du hast Claude auf deinem NAS! 🎉

## Claude wird zur KI, die mich kennt

Okay, Claude existiert, nun ermächtigen wir es, für uns wichtige Daten einzusehen und ggf. zu manipulieren. Man kann Claude (oder auch Gemini und anderen KIs) diese Fähigkeiten entweder _projektbasiert_ oder als _user scope_ geben. Projektbasiert existieren die Fähigkeiten nur in dem Ordner, in dem sie hinzugefügt werden. Im _user scope_ kann die KI überall darauf zugreifen, egal, in welchem Ordner ich mich aktuell befinde. Das ist, was ich für meinen Container möchte.

### Unifi MCP Server

Unser Unifi MCP Server existiert, also fügen wir diesen als erstes hinzu:
```bash
claude mcp add --scope user --transport sse unifi http://unifi-mcp:3000/sse
```

Es sollte eine kurze Info erscheinen, dass der Server hinzugefügt wurde und per `claude mcp list` kannst du erfragen, welche MCP Server konfiguriert sind. Es sollte etwas, wie:
```bash
Checking MCP Server health ...

unifi: http://unifi-mcp:3000/sse (SSE) - ✓ Connected
```
Dann hast du es geschafft, Claude hat Zugriff auf deinen Unifi Controller - sofern du die richtigen Credentials im `docker-compose.yaml` angegeben hast und der Zugriff tatsächlich funktioniert. Nur als Hinweis, falls Fragen später doch kein Ergebnis liefern sollten.

### Sequential Thinking MCP Server

Als zweites möchte ich den MCP Server für _Sequential Thinking_ hinzufügen, ein MCP Server, der bei komplexen Themen dafür sorgt, dass die KI sich erstmal eine Art "Gesamtbild" der Aufgabe verschafft, bevor eine Lösung präsentiert wird. Es verbraucht in diesen Fällen extra Tokens, aber hilft der KI, komplexe Probleme in kleine Schritte zu zerlegen und Revisionen durchzuführen, wenn diese Schritte abgearbeitet werden. Die Installation ist denkbar einfach:

```bash
claude mcp add --scope user sequential-thinking -- npx -y @modelcontextprotocol/server-sequential-thinking
```

### Context7 MCP Server

Als nächstes möchte ich den [_Context7_](https://context7.com/) MCP Server. Context7 bietet aktuelle Entwicklerdokumentation für LLMs. Da ich programmiere, ist es super, immer eine aktuelle Doku für die LLMs griffbereit zu haben und nicht hoffen zu müssen, dass veraltete Lösungen online gefunden werden oder in den Trainingsdaten vorhanden sind. Für Context7 benötigst du einen Account und einen API Key. Allerdings gibt es hier einen gratis Tier, der für Privatpersonen gut ausreicht, da man 1000 Calls pro Monat bekommt und selbst, wenn man dies erreicht, werden bis Ende des Monats 20 Calls pro Tag erlaubt, um weitere Arbeit zu gewährleisten. Ziemlich cooles Free Tier! Sobald du einen Account auf deren Webseite erstellt hast, kannst du im [Dashboard](https://context7.com/dashboard) einen API Key erstellen. Danach geht hier weiter mit:

```bash
claude mcp add --scope user context7 -- npx -y @upstash/context7-mcp --api-key DEIN_KEY
```

### GitHub MCP Server

Nun zu GitHub - auch dort brauchst du einen Access Token, den du in `Settings -> Developer Settings -> Personal Access Token -> Fine-grained tokens` (oder [hier](https://github.com/settings/personal-access-tokens) erstellen kannst). Gib dem Token die Berechtigung, die du claude ermöglichen willst. Ein sinnvoller Startpunkt könnte dies sein:

| Permission | Level |
|---|---|
| Metadata | Read (Pflicht) |
| Contents | Read & Write |
| Pull Requests | Read & Write |
| Issues | Read & Write |
| Discussions | Read |
| Commit statuses | Read & Write |
| Actions | Read & Write |
| Workflows | Read & Write |
| Pages | Read & Write |
| Environments | Read & Write |
| Deployments | Read & Write |
| Dependabot alerts | Read |
| Variables | Read & Write |
| Wikis | Read & Write |

Wenn der Access Token erstellt ist, geht's im Terminal weiter:

```bash
claude mcp add-json --scope user github '{"type":"http","url":"https://api.githubcopilot.com/mcp","headers":{"Authorization":"Bearer github_pat_DEIN_KEY"}}'
```

## Hallo Claude

Ein letzter Check mit `claude mcp list`, das hoffentlich einen solchen Output bringt:
```bash
Checking MCP server health...

context7: npx -y @upstash/context7-mcp --api-key dein_api_key - ✓ Connected
unifi: http://unifi-mcp:3000/sse (SSE) - ✓ Connected
github: https://api.githubcopilot.com/mcp (HTTP) - ✓ Connected
sequential-thinking: npx -y @modelcontextprotocol/server-sequential-thinking - ✓ Connected
```

Wenn du es bis hier geschafft hast, kommt nun der Moment, auf den wir gewartet haben:
```bash
claude
```

Damit startest du die CLI. Logge dich ein (unser NAS hat keinen Browser, aber die Claude CLI ist clever genug, uns einen kopierbaren Link zu geben, mit dem wir das OAuth mit unserem normalen Browser durchführen können) oder verbinde deinen API Key und fang an zu testen! Lass Claude deine GitHub Repos auflisten oder prüfen, wie viele Unifi Access Points du hast. Lass es prüfen, ob es "Expo v55" (die neuste API für React-Native Mobilentwicklung) kennt. Claude ist da und hat die Fähigkeiten, dich persönlich zu unterstützen!

Und du kannst die SSH Session vom Tablet oder Smartphone aus starten, wenn dein NAS remote verfügbar ist (vielleicht via VPN, Tailscale, oder Twingate), kannst du das von überall machen.

## Eine letzte Quality-of-Life Konfig und weiterer Ausblick

Wenn du per `exit` die Session beendest und dich erneut einloggen willst, musst du auf dem NAS immer `docker exec -it claude-code bash` eingeben. Ich kann mir das nicht merken. Leg dir doch auch hier einen `claude` Alias an:
```bash
echo "alias claude='docker exec -it claude-code bash'" >> ~/.bashrc
```

Und schon kannst du per `claude` die Session in deinem Container starten. Also vom Endgerät erst per `ssh <Username>@<NAS-IP>` aufs NAS und dann per `claude` in den Container - und dort lebt deine immer persönlich werdendere KI 😎.

Bis hierher erstmal viel Spaß damit! Im nächsten Teil verbinde ich Strava mit der KI, sodass diese auch als mein Personal Trainer agieren kann. Da hierzu jedoch eine Strava App angelegt werden muss, wollte ich diesen Post nicht noch länger machen. Ich werde auch noch zeigen, wie du deine SSH Session per Key-Authentifizierung starten kannst, um nicht immer das Passwort anzugeben und [_n8n_](https://n8n.io/) steht auch auf der Liste. Und natürlich widmen wir uns auch den _Skills_, _Agents_ und _Commands_, damit die KI nicht nur Fähigkeiten besitzt, sondern auch weiß, was sie wie in welcher Situation tun soll.

Coole Zeiten! Bis dahin, viel Spaß!