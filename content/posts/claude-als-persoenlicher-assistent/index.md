---
author: ["Chrischi"]
title: "Claude als persönlicher Assistent: Setup, MCPs und Personalisierung"
slug: "claude-als-persoenlicher-assistent"
date: "2026-04-07"
draft: false
description: "Wie ich Claude Code per Docker auf dem NAS betreibe, mit MCP-Servern ausstatte und durch Personas und CLAUDE.md zu einem persönlichen KI-Assistenten mache."
summary: "Claude Code lebt bei mir in einem Docker-Container auf dem NAS — mit Zugriff auf GitHub, Strava, meinen Unifi-Controller und aktuelle Entwicklerdoku. Über CLAUDE.md, Personas und Agents weiß es, wer ich bin und wie wir zusammenarbeiten. Und dank tmux und Remote Control bin ich von MacBook, iPad und iPhone aus dabei — ohne Terminal, ohne Kompromiss."
ShowToc: true
TocOpen: false
tags: ["Claude Code", "KI", "Docker", "MCP", "tmux"]
categories: ["Tech Tipps", "Tutorials", "Ugreen NAS"]
cover:
  image: featured-image.webp
  caption: "Erstellt von Nanobanana/Gemini"
---

KI ist aktuell das Thema, das mich am meisten fasziniert. Ich verfolge die Entwicklungen ganz aktiv, die neusten Features von Anthropic, Google und OpenAI. Tools, wie [OpenClaw](https://openclaw.ai/), die den Markt erschüttern und eine noch nie zuvor gesehene Popularität erlangen (wenn man [GitHub Stars](https://github.com/openclaw/openclaw) als Maßstab nimmt). Alle sagen, es würde unser Leben verändern, aber ich _sehe_, wie dies bereits geschieht. Wie KI im Beruf integriert wird und Berufsfelder sich gerade neu justieren müssen. Nicht jeder kann oder will mitgehen. Super spannende Zeit.

Ich habe das Glück, dass ich meinen beruflichen Arbeitsablauf stark durch KI unterstützen lassen kann. Das macht mir zum einen total Spaß, nimmt mir aber auch viele "Zeitfresser" ab. Natürlich nutze ich hier kein OpenClaw, aber privat reizte es mich immer wieder, das Tool irgendwo zum Leben zu erwecken.
Docker selbst zeigt Möglichkeiten für das Setup in einem [_sicheren_ Container](https://www.docker.com/blog/run-openclaw-securely-in-docker-sandboxes/). Aber welchem Mehrwert böte das? Ich hätte dem Tool niemals Zugriff auf meinen Kalender oder meine Emails gegeben. Trotzdem reizt mich die Idee von Claude als persönlichen Assistenten. _Eine KI, die mich kennt._ 

Ich machte also, was die meisten Nerds tun würden: Ich bastelte an einer eigenen Lösung, die _perfekt auf mich_ zugeschnitten wäre. Genau dieses Setup will ich hier besprechen. Es ist eine Art "Tutorial", aber nicht klassisch. Es ergäbe sicher keinen Sinn, meinem Setup eins zu eins zu folgen. Denn dann hättet ihr ein Setup, das auf mich zugeschnitten ist. Aber trotzdem kann mein Setup angepasst und individualisiert werden.

## Voraussetzungen für dieses Setup

Dieser Post beschreibt _mein_ Setup und auch, wenn ich hoffe, dass du viel Inspiration mitnimmst, so wird (und soll!) das Gesamtsetup bei dir anders aussehen. Auf _dich_ zugeschnitten. Trotzdem will ich dir nicht vorenthalten, was ich benutzt habe, um mein Setup hinzubekommen. Der Stack ist also:
- ... ein Ugreen DXP 4800 Plus NAS
- ... ein bezahltes Claude Pro Abo (für das CLI Tool brauchst du ein Abo oder einen API Key - sonst geht auch eine andere KI
- ... ein GitHub Account (optional)
- ... Docker auf dem NAS installiert

Das NAS kann bei dir auch etwas anderes sein, ein VPS, Raspberry Pi, dein Laptop. Du solltest das nur beachten, wenn ich über "SSH aufs NAS" und "dem Container auf dem NAS" spreche.

## Zielarchitektur für die KI, die mich kennt

Meine zentrale KI wird [Claude Code](https://code.claude.com/docs/de/quickstart), das CLI Tool von [Claude](https://claude.ai/). Um Claude als Konsolentool zu nutzen, brauchst du zwingend mindestens ein _Pro_-Abo oder einen abrechnungsfähigen API Key. Mit dem [Gemini CLI](https://geminicli.com/), dem [Mistral CLI](https://mistral.ai/pricing) oder der [Ollama Cloud](https://ollama.com/) gibt es sehr gute Alternativen, die wirklich großzügige Gratis-Tiers oder erschwingliche Monatspreise besitzt. Mein Tutorial basiert auf "Claude", das Konzept ist aber mit beiden KIs umsetzbar. Die Befehle wären ein wenig angepasst - aber nichts, was eine KI für dich umbiegen könnte 😉.

Wie kann ich Claude _personalisieren_? Wie mache ich aus Claude eine KI, die mich kennt? Als erstes muss ich Claude bestimmte _Fähigkeiten ermöglichen_. Es wäre wirklich cool, wenn Claude ...
- ... auf meine lokalen Dateien zugreifen kann (klar, ein No-Brainer).
- ... Zugriff auf meinen GitHub Account hätte.
- ... Zugriff auf meinen Unifi Controller (und somit auf die Administration meines Heimnetzwerkes) hätte.
- ... mein Smart Home einsehen kann.
- ... meinen Kalender kennt
- ... meine sportliche Leistung einschätzen kann.
- ... von überall mit diesem Wissen erreichbar wäre

Das wäre doch schon ein starker Anfang, um Claude für persönliche Dinge zu nutzen. Trainingspläne erstellen, Rolling Firmware Updates meiner Unifi-Geräte, GitHub Issues ansehen und den Code dazu prüfen. Das ist schon deutlich mehr, als einfach das generische Web-Interface im Browser zu nutzen und jedes Mal den Kontext neu schaffen zu müssen.

Das Setup ist mehrstufig, einiges kann über _MCP Server_ bereitgestellt werden, also fangen wir damit an.

## MCP in 30 Sekunden

Was ist ein _MCP Server_? _MCP_ steht für _Model Context Protocol_ und ist ein mittlerweile standardisierter Weg, einer KI neue Fähigkeiten zu ermöglichen. Oft kommt der Vergleich mit _USB-C_: USB-C ist ein Stecker-Standard über welchen du alles Mögliche an deinen Rechner anschließen und dessen Funktionsumfang erweitern kannst. Drucker über USB-C geben deinem Rechner die Fähigkeit zu drucken. Eine Tastatur ermöglicht das Tippen, eine Kamera die Foto- und Videoaufnahme.

Über das _Model Context Protocol_ können wir der KI ebenfalls Tools an die Hand geben, um Dinge zu tun. Über einen _Unifi MCP Server_ könnte eine KI _lernen_ (oder direkt wissen), wie die heimischen Unifi-Geräte auszulesen und zu administrieren sind. So könnte man fragen, _"Hey, wie viele Access Points habe ich?"_ und die KI nutzt den per MCP angebundenen _Unifi MCP Server_, der das richtige Tool beinhaltet, um diese Frage zu beantworten.

So erhält Claude Zugriff auf das Heimnetz. Ich mag mich als Nerd outen, aber ich finde das ziemlich cool!

Über MCP können wir auf sehr vieles zugreifen. So werden auch unsere weiteren Fähigkeiten angebunden.

## Es geht los: Lokales Docker-Setup

Mein Setup umfasst zwei Ebenen: Die lokale Ebene und die Service-Ebene. Die lokale Ebene beinhaltet die Tools, die wir lokal bereitstellen müssen, während die Service-Ebene ebendie Tools umfasst, die wir über das Internet anbinden. Tools, die wir nicht selbst hosten müssen.

Lokal brauchen wir einen Host, auf dem Claude installiert und der Unifi MCP Server betrieben werden kann. Da unser Netzwerk typischerweise lokal ist, brauchen wir auch den MCP-Server lokal. Glücklicherweise können wir beides über [_Docker_](https://www.docker.com/) bereitstellen. Es lebe Docker!

Für Claude gibt es kein eigenes Image, aber wir können einen schlanken [Node Container](https://hub.docker.com/_/node) nutzen. Für unsere angebundene Heimnetz-Fähigkeit gibt es glücklicherweise bereits einen [Unifi MCP Server als Communityprojekt](https://github.com/sirkirby/unifi-network-mcp).

Beide Container lasse ich auf meinem NAS im selben Netzwerk laufen, das ich `ai-net` nenne.

Allerdings möchte ich unseren Claude Container so vorbereiten, dass wir gewisse Pakete (beispielsweise `git`, `vim`, `tmux`) und Claude nicht immer manuell nach jedem Container-Neustart installieren müssen. Deshalb will ich ein eigenes `Dockerfile` nutzen. Hierfür legen wir eine Datei namens `Dockerfile` (ohne Dateiendung) an.

### UID und GID ermitteln

Claude soll später Dateien in gewissen Ordnern auf meinem NAS anlegen, editieren und löschen können. Um dies zu gewährleisten, müssen wir die UNIX-Dateiberechtigung beachten. User in Unix haben eine _User-ID_ und sind Mitglied einer Gruppe, welche auch eine _Group-ID_ hat. Rechte auf Dateien werden a) für den User, b) für die Gruppe des Users und c) für alle anderen Vergeben. Wichtig für uns ist nun, dass unser lokaler Nutzer auf dem NAS dieselbe _User-ID_ hat, wie der Nutzer `node` im Container. Außerdem soll bestenfalls auch die ID der Gruppe des NAS-Nutzers mit der ID des Nutzers `node` im Container übereinstimmen. So hat der Nutzer `node` im Container immer dieselben Rechte auf alle Dateien, wie unser lokaler NAS-Nutzer - und natürlich anders herum.  Da Claude im Container unter dem Nutzer `node` laufen wird, hat auch Claude implizit diese Berechtigungen. Das macht uns das Leben später leichter.

Der Nutzer `node` ist übrigens in dem Container, den wir nutzen werden, bereits vorhanden. Allerdings vermutlich mit einer anderen "UID" (User ID) und "GID (Gruppen ID), also werden wir diese anpassen. Dies wird eine Konfiguration im Dockerfile. Doch vorweg ermitteln wir erstmal unsere eigenen IDs.

Via SSH auf dem NAS (bei mir ein Ugreen DXP 4800 Plus):
```bash
ssh <USERNAME>@<NAS-IP>
id <USERNAME>
```

Die ausgegebenen IDs notieren wir uns.

### Das Dockerfile

Mit einem eigenen `Dockerfile` nutzen wir ein vorhandenes Docker Image, aber können weiteres Setup direkt einbauen. So werden eigenen Konfigurationen bei jedem Container-Neustart erneut gesetzt. Wenn du eben schon ein `Dockerfile` (ohne Dateiendung) angelegt hast, kannst du es nach folgendem Schema befüllen. Passe es gern an deine Bedürfnisse an. Hast du die Datei noch nicht, natürlich erst anlegen.

Claude selbst wird **nicht** direkt ins Image installiert — das passiert einmalig hinterher im persistenten Volume. Da unser Home-Verzeichnis als Volume gemountet wird, überlebt auch Claude einen Neustart. So bleibt das Image schlank und rebuild-sicher.

```dockerfile
FROM node:24-slim

# Das Setup modifizieren wir (im Container) als root User
USER root

# Hinterlege hier deine IDs als Variablen
ARG UID=<DEINE-USER-ID>
ARG GID=<DEINE-GROUP-ID>

# System-Pakete
# Ich installiere curl und bash. Das brauchst du für die Claude-Installation
# Außerdem möchte ich git, nano, vim-nox (für headless Systeme) und tmux installieren.
# Auf tmux komme ich später zu sprechen
RUN apt-get update && apt-get install -y \
    curl bash git nano vim-nox tmux \
    && rm -rf /var/lib/apt/lists/*

# GID-Kollision im Container auflösen, dann node-User anpassen
# Der node User wird nun auf unsere IDs gebogen. Für diesen Container, in dem wir nur im Home-Verzeichnis mit
# Claude arbeiten, ist dies vertretbar.
RUN if getent group ${GID} > /dev/null 2>&1; then \
        groupmod -g 9999 $(getent group ${GID} | cut -d: -f1); \
    fi && \
    usermod -u ${UID} node && groupmod -g ${GID} node

# Wechseln zum User `node`
USER node

# Für nano setzen wir die Darstellungsoption und wir konfigurieren das Startverzeichnis, wenn wir uns in den Container bringen
ENV TERM=xterm-256color
WORKDIR /home/node/

# Node Container können "einschlafen" und brauchen einen dauerhaft laufenden System-Prozess.
CMD ["sleep", "infinity"]
```

### Das Docker Compose

Sobald wir unser angepasstes `Dockerfile` haben, kann das `docker-compose.yaml` angelegt werden. Beachte auch hier bitte die Inline-Kommentare. Ein paar Anpassungen müssten vorgenommen werden. Gerade beim Username und Passwort empfehle ich stark, einen zusätzlichen, lokalen Admin im Unifi Controller anzulegen, der ausschließlich hierfür verwendet wird. Denn es darf keine Zweifaktor-Authentifizierung eingeschaltet sein. Den Rest gern mit eigenem Setup anpassen. Die Credentials hinterlege ich in einer separaten `.env` Datei.

Das `docker-compose.yaml` besteht aus zwei Services im selben Netzwerk, so kann Claude problemlos auf meinen Unifi MCP zugreifen. Dieser ist schnell konfiguriert. Spannender wird es beim `claude-code` Container. Dieser hat bei mir eine dreischichtige Volume-Strategie:
1. `claude-code-home:/home/node` für Dotfiles (Claude Kontext, erläutere ich später) und Claude-Installation => persistent, lokal
2. Zwei NAS-Mounts für `development` und `Documents` => überdecken gezielt Unterordner im Home-Volume. Hier darf Claude Dateien editieren
3. Read-only Mounts für weiteren NAS-Zugriff => Nur, um Dateien zu lesen und zu analysieren.

```yaml
networks:
  ai-net:
    driver: bridge

services:

  # ---------------------------------------------------------------------------
  # UniFi Network MCP – direkter MCP-Server für UniFi Controller
  # Intern erreichbar: http://unifi-mcp:3000/sse
  # Im LAN erreichbar:  http://<NAS-IP>:3000/sse => Port kann unten geändert werden
  # ---------------------------------------------------------------------------
  unifi-mcp:
    image: ghcr.io/sirkirby/unifi-network-mcp:latest
    container_name: unifi-mcp
    restart: always
    ports:
      - "3000:3000"                                       # Siehe Beschreibung oben. Mapping nur notwendig, wenn der MCP im gesamten LAN erreichbar sein soll
    environment:
      - UNIFI_HOST=192.168.X.XXX                          # IP deines UniFi Controllers
      - UNIFI_PORT=443                                    # Port deines Controllers
      - UNIFI_USERNAME=${UNIFI_USERNAME}                  # via ".env" Datei
      - UNIFI_PASSWORD=${UNIFI_PASSWORD}                  # via ".env" Datei
      - UNIFI_SITE=default
      - UNIFI_VERIFY_SSL=false
      - UNIFI_MCP_HTTP_ENABLED=true
      - UNIFI_MCP_HTTP_TRANSPORT=sse
      - UNIFI_MCP_ENABLE_DNS_REBINDING_PROTECTION=false
    networks:
      - ai-net

  # ---------------------------------------------------------------------------
  # Claude Code – CLI Container
  # Zugriff: docker exec -it claude-code bash
  # ---------------------------------------------------------------------------
  claude-code:
    build:
      context: .
      dockerfile: Dockerfile
      args:
        UID: <Deine UID>      # Deine UID
        GID: <Deine GID>      # Deine GID
    container_name: claude-code
    restart: unless-stopped
    stdin_open: true
    tty: true
    working_dir: /home/node/development
    volumes:
      # Persistenter Home-Ordner (Dotfiles, .claude, .local/bin/claude, .bashrc)
      - ./claude-code-home:/home/node
      # NAS-Arbeitsverzeichnisse
      - /volume1/@home/Christian/claude-workspace/development:/home/node/development
      - /volume1/@home/Christian/claude-workspace/Documents:/home/node/Documents
      # NAS-Zugriff (read-only)
      - /volume1/shared:/shared:ro
      - /volume1/@home/Christian:/personal:ro
    depends_on:
      - unifi-mcp
    networks:
      - ai-net
```

Die zugehörige `.env` Datei sieht in etwas so aus (mit deinen Werten):
```yaml
UNIFI_USERNAME=<dein-unifi-user>
UNIFI_PASSWORD=<passwort-des-unifi-users>
```

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
Nun kann in der Docker App auf dem NAS unter _Projekte_ ein neues Projekt angelegt werden. Als Name wähle ich `ai-net` und als Speicherort den gleichnamigen Ordner. Ich importiere die `docker-compose.yaml` (das sollte schon vorgeschlagen werden) und schon werden die beiden Container und das Netzwerk erstellt. Die referenzierten Ordner `claude-workspace/development` und `claude-workspace/Documents` müssen existieren.

## Claude zum Leben erwecken

Das Container Setup steht! 🎉 Nun muss Claude zum Leben erweckt und einmalig konfiguriert werden.

```bash
# 1. Per SSH aufs NAS
ssh <User>@<NAS>

# 2. In den Container wechseln
docker exec -it claude-code bash

# 3. Claude installieren
curl -fsSL https://claude.ai/install.sh | bash

# 4. Shell konfigurieren
# Diese Konfiguration ist "meinungsstark". Ich ändere auch meine PS1-Variable, was die Prompt-Zeile umkonfiguriert. Ist natürlich optional
cat >> ~/.bashrc << 'EOF'
export PATH="$HOME/.local/bin:$PATH"
export TERM=xterm-256color
export PS1="\[\e[36m\][CLAUDE]\[\e[0m\] \[\e[32m\]\u\[\e[0m\]:\[\e[34m\]\w\[\e[0m\] $ "
EOF
source ~/.bashrc

# 5. Testen
claude --version
```

Das war's. Claude lebt auf deinem NAS. Ab jetzt überlebt alles in `/home/node` jeden Restart und jeden Rebuild — solange du `./claude-code-home/` nicht löschst!

## Claude wird zum persönlichen Assistenten

Claude existiert, nun ermächtigen wir es, für uns wichtige Daten einzusehen und ggf. zu manipulieren. Man kann Claude (oder auch Gemini und anderen KIs) diese Fähigkeiten entweder _projektbasiert_ oder als _user scope_ geben. Projektbasiert existieren die Fähigkeiten nur in dem Ordner, in dem sie hinzugefügt werden. Im _user scope_ kann die KI überall darauf zugreifen, egal, in welchem Ordner ich mich aktuell befinde. Das ist, was ich für meinen Container möchte.

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

### Strava

Der Strava MCP ist etwas weniger trivial, aber auch kein Hexenwerk. Es gibt dieses [coole Github-Projekt](https://github.com/r-huijts/strava-mcp), welches dich durch das Setup führt. Du musst via [strava.com/settings/api](https://strava.com/settings/api) eine Strava App anlegen und diese auf deinen Account berechtigen. Notiere dir Client Key und Secret. Dann fügst du auch diesen MCP Server hinzu:
```bash
claude mcp add --transport stdio strava -- npx @r-huijts/strava-mcp-server
```
Wenn du in Claude später "Verbinde meinen Strava Account" schreibst, führt dich Claude durch den Login-Prozess, für den du Key und Secret brauchst. Folge am besten der [Anleitung vom Entwickler](https://github.com/r-huijts/strava-mcp?tab=readme-ov-file#step-1-alternative-add-to-claude-code).

### Cloudflare

Ich habe meine Domains zwar bei INWX erworben, aber manche davon administriere ich über Cloudflare. Also auch deren MCP möchte ich hinzufügen. Hier brauchen wir keinen API Token, die Berechtigung wird im OAuth Flow erteilt, wenn der MCP erstmals genutzt wird oder wir später per /mcp den Authentication Flow manuell starten.

```bash
claude mcp add --scope user -t http cloudflare-api https://mcp.cloudflare.com/mcp
```

### claude.ai MCPs

Da ich ein Claude Abo habe, kann ich bestimmte MCPs systemübergreifend installieren. Google Calendar beispielsweise ist in claude.ai konfiguriert. Sobald ich mich im CLI mit meinem Abo anmelde, wird dieser Zugriff auch hier existieren. Deshalb will ich mich erstmal authentifizieren und dann den Status prüfen.

### Die Anmeldung
Der nächste sinnvolle Schritt ist also die Anmeldung:
```bash
claude
```

Du wirst durch ein kleines Setup geleitet: Welches Theme bevorzugst du und auch die Anmeldung findet statt: OAuth oder API Key - ich wähle OAuth. Der Flow funktioniert übrigens coolerweise auch headless! Claude bietet dir einen OAuth-Link, den du in einen Browser kopieren kannst, um dich zu authentifizieren.

Nach dem Setup habe ich meinen Strava-Account verbunden und danach claude per `/exit` erstmal wieder verlassen.

### Alles verbunden?

```bash
claude mcp list
```

```bash
Checking MCP server health...

claude.ai Context7: https://mcp.context7.com/mcp - ✓ Connected
claude.ai Gmail: https://gmail.mcp.claude.com/mcp - ! Needs authentication
claude.ai Google Calendar: https://gcal.mcp.claude.com/mcp - ✓ Connected
unifi: http://unifi-mcp:3000/sse (SSE) - ✓ Connected
strava: npx -y @r-huijts/strava-mcp-server - ✓ Connected
github: https://api.githubcopilot.com/mcp (HTTP) - ✓ Connected
cloudflare: https://mcp.cloudflare.com/mcp (HTTP) - ✓ Connected
```

Dies ist mein Output. Die mit `claude.ai` gekennzeichneten MCP Server kommen daher, dass ich diese im Web-Interface bereits konfiguriert habe. Die unteren vier sind die neu hinzugefügten!

## Quality of Life

Um nun von unserem Endgerät bis zu Claude zu kommen, sind es einige Schritte:
- SSH aufs NAS
- `docker exec -it claude-code bash` um in den Container zu kommen
- Dann erst der Start von `claude`

Das ist mir zu viel. Ich nutze ein MacBook, die Einrichtung der Aliasse kann auf verschiedenen Systemen unterschiedlich sein. Aber claude wird dir helfen 😉. Da ich per SSH Key aufs NAS komme, hinterlege ich mein Auth-Setup auf meinem MacBook unter `~/.ssh/config` (bitte deine Daten ohne "<>" eintragen):
```
Host nas
  Hostname <Meine NAS IP>
  User <Mein NAS Username>
  IdentityFile ~/.ssh/<Mein Private Key File>
```

So kann ich nun mit folgendem Shortcut aufs NAS kommen:
```bash
ssh nas
```

Viel besser. Auf dem NAS lege ich nun meinen Alias an:
```bash
echo "alias claude='docker exec -it claude-code bash'" >> ~/.bashrc
source ~/.bashrc
```

Nun habe ich auf meinem NAS den Alias `claude`, der mich in den Claude Container bringt. Vom MacBook geht's nun also per `ssh nas` und dann `claude` in den Container. Ein erneutes `claude` und mein KI-Assistent startet. Da mein NAS via [Twingate](https://www.twingate.com/) von überall aus erreichbar ist, funktioniert der Zugriff auch remote.

Natürlich kann ich auch mit Apps, wie [Secure Shellfish](https://secureshellfish.app/) von meinem iPhone und iPad auf Claude zugreifen. Das ist schon sehr _nett_, aber geht noch besser.

## Pause, Zeit für eine Zwischenbilanz

Puh, erstmal Zeit für einen Kaffee ☕️

Was haben wir bis hierher erreicht? Claude (oder die KI deiner Wahl) lebt in einem dauerhaft laufendem Container, bei mir ist dieser auf dem NAS. Außerdem hat Claude Zugriff auf für mich wichtige Tools. Tools, die ich im Alltag für verschiedene Bereiche brauche. Claude ist außerdem von überall per SSH erreichbar. Ich realisiere dies über Twingate, aber es gibt diverse Methoden, deinen Endpunkt übers Internet erreichbar zu machen.

Aber wir haben noch nicht viel gewonnen. Ja, Tools sind verfügbar, aber dafür ist die Usability deutlich runter gegangen. Wir brauchen einen SSH Client und müssen uns einloggen. Es dauert sehr viel länger, bis wir mit Claude sprechen können und Claude weiß dann ehrlicherweise noch immer nicht viel mehr über uns, als in der Weboberfläche. Diese beiden Themen möchte ich also noch angehen.

## KI, die mich kennt

Nur, weil Claude auf dem NAS lebt und Zugriff auf gewisse Tools hat, ist Claude nicht direkt _personalisierter_. Bevor wir also der Technik einen Feinschliff geben, lasst uns Claude zu einem _persönlichen, digitalen Assistenten_ machen.

Ich kann hier einfach nicht all zu tief ins Detail gehen, wie eine KI sich Dinge "merkt" oder "Wissen behält". Aber anfangs erwähnte ich, dass es ein Set an Markdown-Dateien ist, die das _Leben_ der KI ausmachen. Hier eine kurze (für uns relevante, aber unvollständige) Übersicht:

| Datei            | Bedeutung |
| ---------------- | --------- |
| `CLAUDE.md`      | Die zentrale Konfigurationsdatei — hier steht, wie Claude sich verhalten soll. Projekt-spezifisch (im Repo) oder global (`~/.claude/CLAUDE.md`). Wird bei jeder Session automatisch geladen. |
| `personas/*.md`  | Definieren eine Rolle mit spezifischem Verhalten — z.B. „Senior Developer" oder „Personal Trainer". Claude übernimmt die beschriebene Perspektive, Tonalität und Expertise. Wird je nach Kontext in CLAUDE.md eingebunden (`@personas/developer.md`). |
| `agents/*.md`    | Spezialisierte Sub-Agenten für wiederkehrende, abgegrenzte Aufgaben — z.B. ein React-Native-Agent oder ein Firebase-Agent. Ähnlich wie Personas, aber enger gefasst: ein Agent hat ein klares Einsatzgebiet und kann von Claude als eigenständiger Subprozess gestartet werden. |
| `memory/*.md`    | Persistentes Gedächtnis über Sessions hinweg — was Claude über dich, deine Präferenzen und laufende Projekte wissen soll. Im Gegensatz zu CLAUDE.md wächst dieses Verzeichnis dynamisch durch Claude selbst. |
| `commands/*.md`  | Eigene Slash-Commands — z.B. `/review-post` oder `/commit`. Jede Datei beschreibt einen Befehl, den Claude beim Aufruf ausführt. Ermöglichen wiederholbare Workflows ohne Copy-Paste. |
| `skills/*.md`    | Ähnlich wie Commands, aber als erweiterte, aufrufbare Fähigkeiten — können Parameter entgegennehmen und komplexere Abläufe steuern. |
| Hooks (Settings) | Automatische Shell-Befehle, die Claude bei bestimmten Ereignissen ausführt — z.B. vor/nach einem Tool-Aufruf oder nach einer Dateiänderung. Konfiguriert in `settings.json`, nicht als Markdown. |

### CLAUDE.md

Claudes "Hauptgedächtnis" liegt also in `~/.claude/` und hier wollte ich ansetzen. Ich habe eine Datei erstellt, in der _ich_ vorgestellt werde. Wer bin ich, was mache ich, was sind meine Hobbies, was ist mein Tech Stack, an welchen Projekten arbeite ich gerade. Das soll Claude _immer_ wissen. Ich habe die Art der Zusammenarbeit definiert:
- Ich will keinen "Ja"-Sager
- "Weiß ich nicht" ist erlaubt, besser, als Halluzination
- Kein übertriebenes Lob
- Logiklücken meinerseits sollen erkannt und aufgedeckt werden
- Auch eine Persönlichkeit habe ich erstellt.

Claude wurde _persönlich_! Außerdem habe ich die Referenzen gegeben: Welche Tools stehen zur Verfügung und was machen diese und welche Personas existieren und wann werden diese geladen.

### Personas

Das ist das Herzstück. Dann habe ich Personas erstellt:
- Einen _Personal Trainer_, der mit mir mein Lauftraining bewertet, Trainingspläne erstellt und natürlich Zugriff auf Strava hat
- Einen _Developer_, der mich beim Programmieren unterstützt. Dieser hält sich an Coding Guidelines, beachtet _Separation of Concerns_, kennt den Context7 MCP, übergibt nur Code, der kompiliert und erklärt seine Anpassungen
- Einen _HomeLabber_, der mich bei meinem privaten Tech-Themen unterstützt. Auch bei der Ausarbeitung dieses Setups
... die Liste ist ganz individuell! Wen wünschst du, hättest du an deiner Seite?

Diese Dateien liegen also in `~/.claude/personas/` und geben spezifisches _Fachwissen_. Die Dateien werden aber nicht automatisch geladen. Noch liegen diese nur da.

### Agents

Auch Agents habe ich definiert. Diese geben für ein Themengebiet sehr spezifische Vorgaben. Mein `type-script` Agent weiß, dass kein `any` und kein `as unknown as X` erlaubt ist. Er weiß, dass ich für Bottom-Sheets immer `@gorhom/bottom-sheet` nutzen möchte. Dass `zustand` das State Management der Wahl ist. Solche Dinge.

### Commands

Auch wiederkehrende Commands habe ich. Ich mache einen `weekly-checkin` beim Sport, um standardisiert meine letzte Woche zu analysieren und die kommende Woche zu planen. Am Ende einer Session mit Claude nutze ich `session-wrapup`, um neue Erkenntnisse zu persistieren.

Und hier wird's interessant. Claude schaut sich so die Session an und passt - sofern notwendig - auch sein CLAUDE.md. Aber er legt auf jeden Fall einen _session-log_ an, in welchem beschrieben ist, was getan wurde und was noch offen ist. Dies wird bei der nächsten Session geladen und so _weiß_ Claude, was der aktuelle Stand ist.

### Sub-Kontexte

Und nun kommen wir zur Eigenkreation. Claude liest pro Session nicht nur das "Hauptgedächtnis", sondern auch das "Projektgedächtnis", also ein `CLAUDE.md`, welches im aktuellen Ordner liegt. Öffne ich Claude im Container in `~/development`, dann lädt er `./CLAUDE.md` und `~/.claude/CLAUDE.md`. Ich kann also projektspezifischen Kontext anlegen. Und das habe ich gemacht. Ich habe meine Projektordner definiert:
- ~/development
- ~/Documents/Chat Sessions
- ~/Documents/Sport
- ~/Documents/HomeLab
- ...

und pro Projekt ein CLAUDE.md angelegt. Beim Sport kann hier endlich die richtige Persona geladen werden. Außerdem gibt es Infos zu meinen sportlichen Zielen, den nächsten Wettkämpfen, den Daten meiner Leistungsanalyse und meinen Trainingstagen. Alles, was für den Sport wichtig ist. Das habe ich für all meine Projekte gemacht.

Nun kommt aber der Clou: Ich habe auch diese Dateien in `~/.claude/` gespeichert, unter `~/.claude/contexts/sport.md`, `~/.claude/contexts/homelab.md`, `~/.claude/contexts/development.md`, etc... Von dort erstelle ich nur einen _Symlink_ in den entsprechenden Ordner. Ausnahme sind sehr projektspezifische Kontexte. Ein spezielles Entwicklungsprojekt, dessen Kontext niemals für etwas anderes verwendet werden kann. Ein solcher Kontext muss natürlich im Projektordner liegen.

Für die allgemeineren Kontexte hat mein Setup den Vorteil, dass _alles an Claudes Wissen_ in einem Ordner liegt. Ich kann also in `~/.claude/` ein git Repo erstellen und folgendes versionieren:
```
~/.claude/
├── CLAUDE.md                     # Global entry point — always loaded
├── setup.sh                      # Create symlinks (run on new devices)
├── .gitignore
│
├── personas/                     # Personality & working style
│   ├── developer.md              # Senior Engineer mindset & principles
│   ├── personal-trainer.md       # Training coach — data-driven, no excuses
│   ├── lektor.md                 # Editor — dramaturgical, no false praise
│   └── homelab.md                # HomeLab & network architect — pragmatic, security-minded
│
├── agents/                       # Domain expertise & stack knowledge
│   ├── react-native.md           # Expo · RN · Zustand · Navigation · Sheets
│   └── firebase.md               # Firestore · Auth · Storage · Functions
│
├── contexts/                     # CLAUDE.md content for directory symlinks
│   ├── development.md            # → ~/development/CLAUDE.md
│   ├── chat-sessions.md          # → ~/Documents/Chat Sessions/CLAUDE.md
│   ├── sport.md                  # → ~/Documents/Sport/CLAUDE.md
│   ├── schreiben.md              # → ~/Documents/Schreiben/CLAUDE.md
│   └── homelab.md                # → ~/Documents/HomeLab/CLAUDE.md
│
└── commands/                     # Slash commands
    ├── session-wrapup.md         # /session-wrapup — structured session close
    └── weekly-checkin.md         # /weekly-checkin — Strava analysis & week planning
```

Bei neuen Maschinen, (MacBooks, NAS, etc...) kann ich dieses Repo klonen und via `setup.sh` mein Projektsetup aufbauen. Unter `~/Documents` werden meine Ordner angelegt und ein Symlink für CLAUDE.md erstellt, unter `~/development` landet meine Development-CLAUDE.md und so kann ich mein Setup überall reproduzieren.

Als Referenz für dein Setup, meine `setup.sh` sieht so aus:
```bash
#!/bin/bash

mkdir -p "$HOME/development/session-log"
mkdir -p "$HOME/Documents/Chat Sessions/session-log"
mkdir -p "$HOME/Documents/Sport/session-log"
mkdir -p "$HOME/Documents/HomeLab/session-log"

ln -sf "$HOME/.claude/contexts/development.md"   "$HOME/development/CLAUDE.md"
ln -sf "$HOME/.claude/contexts/chat-sessions.md" "$HOME/Documents/Chat Sessions/CLAUDE.md"
ln -sf "$HOME/.claude/contexts/sport.md"         "$HOME/Documents/Sport/CLAUDE.md"
ln -sf "$HOME/.claude/contexts/homelab.md"       "$HOME/Documents/HomeLab/CLAUDE.md"

echo "Done."
```

Der Ordner `session-log` beinhaltet dann die weiter oben erwähnten Logs nach dem Command `/session-wrapup`.

So ergibt es hoffentlich auch Sinn, wieso ich im Container auf dem NAS den Umweg über `~/Documents` gegangen bin. Das wäre dort natürlich nicht notwendig, aber so ist das Setup auch auf einem MacBook reproduzierbar und erstellt eine schöne Ordnerstruktur.

Mit diesem Setup ist meine KI plötzlich ganz stark personalisiert und durch die `session-wrapups` werden die Wissensdateien aktualisiert!

Jetzt ist zwar die _User Experience_ mit SSH noch nicht so schön, aber der Mehrwert ist lohnenswert! Aber auch die UX soll Spaß machen. Kümmern wir uns nun also darum.

## Immer erreichbar — tmux und Remote Control

Claude via SSH zu bedienen ist am Rechner vielleicht noch okay. Aber schön geht anders und Claude unterstützt dieses "anders". Es gibt den nativen Command `/remote-control`, mit welchem man Sessions aus dem Claude CLI an die Web App übergeben kann. In der Desktop App oder der mobilen App. Das Problem ist nur, sobald ich das Terminal schließe, endet die SSH-Session und damit auch die Möglichkeit des `remote-control`.

Deshalb haben wir anfangs aber `tmux` mit installiert.

### tmux im Container

tmux ist ein Terminal-Multiplexer. Klingt komplizierter als es ist: Du startest eine Session, Claude läuft darin, du trennst dich — und die Session läuft im Hintergrund weiter. In tmux kann man Sessions also _weiterlaufen lassen_.

Die wichtigsten Befehle:

```bash
tmux new-session -s <tmux-session-name>         # Neue tmux Session starten
claude --remote-control "<claude-session-name>" # Claude darin inkl. remote-control starten

# Ctrl+B, dann D                                # Detachen — Session läuft weiter

tmux attach -t <claude-session-name>            # Später wieder dranhängen
tmux ls                                         # Alle Sessions auflisten
tmux kill-session -t <claude-session-name>      # Session beenden
```

Es gibt hier eine kleine Falle, denn in tmux wird der Befehl `claude` nicht funktionieren. Im Container hat `tmux` eine neue Login-Shell, die `~/.bashrc` nicht automatisch liest. Claude liegt unter `~/.local/bin/` — und landet damit nicht im PATH. Der Fix:
```bash
# ~/.bash_profile anlegen, das .bashrc sourcet
echo 'source ~/.bashrc' > ~/.bash_profile
```

Auch diese Datei wird dank Volume-Mapping in Docker persistiert. Das musste nur einmalig gemacht werden.

Über tmux können wir jetzt also eine Claude session mit remote-control starten und uns dann via `<Ctrl>+B`, danach `D` detachen. Das Terminal-Fenster kann geschlossen werden, die Session läuft weiter und ist in der Claude Desktop und Mobile App bedienbar!

> Anmerkung: Es kann sein, dass du die Session noch _nicht_ siehst. Dann fragt Claude gerade danach, ob du ihn in dem Ordner wirklich ausführen möchtest. Ein `tmux attach -t <name>`, um in die Session zu kommen und eine Bestätigung helfen dir hier. Das wird später nicht mehr passieren, da Claude später deine Ordner kennt (entweder du bestätigst jeden Ordner einmal oder legst CLAUDE.md Dateien an, wie wir es weiter oben gemacht habe. Eventuell war in deinem Ordner noch keine und das war ein neuer Ordner).

So macht Claude schon viel mehr Spaß und diese Sessions _kennen unseren Kontext_ und _haben unsere Tools_!

## Schnellzugriffe einrichten

Die technischen Hürden sind genommen. Jetzt möchte ich noch den schnellstmöglichen Zugriff auf diese Sessions haben. Ich möchte ohne großen Aufwand sessions starten und beenden können.

Was macht ein Entwickler bei gleichen, wiederkehrenden Aufgaben? Richtig, eine Funktion schreiben! Auf dem NAS lege ich also zwei Dateien an, `start-claude.sh` und `stop-claude.sh`. Nicht vergessen, diese Dateien per `chmod +x *-claude.sh` ausführbar zu machen.

`start-claude.sh` soll die Möglichkeit haben, für jedes meiner Projekte eine Claude Session _im entsprechenden Ordner_ zu haben:
```bash
#!/bin/bash
NAME=${1:-generic}
case $NAME in
  homelab)    DIR="/home/node/Documents/HomeLab" ;;
  sport)      DIR="/home/node/Documents/Sport" ;;
  *)          NAME="general"; DIR="/home/node/Documents/Chat\ Sessions" ;;
esac
docker exec claude-code bash -c \
  "tmux new-session -d -s $NAME 2>/dev/null; \
   tmux send-keys -t $NAME 'cd $DIR && claude --remote-control $NAME' Enter"
```

Auf meinem NAS kann dieses Skript also mit `start-claude.sh sport` (oder irgendeinem anderen Parameter) aufgerufen werden und es würde dann in den claude-container springen, dort eine tmux-session starten und _im entsprechendem Claude-Projektordner_ Claude aufrufen. Die Session finde ich dann wieder in den entsprechenden Apps.

Das Stop-Skript sieht so aus:
```bash
#!/bin/bash
NAME=${1:-general}

if [ "$NAME" = "alle" ]; then
  SESSIONS=$(docker exec claude-code tmux ls 2>/dev/null | awk -F: '{print $1}')
  if [ -z "$SESSIONS" ]; then
    echo "Keine aktiven Sessions."
  else
    echo "$SESSIONS" | xargs -I{} docker exec claude-code tmux kill-session -t {} 2>/dev/null
    echo "Alle Sessions beendet."
  fi
else
  docker exec claude-code tmux kill-session -t "$NAME" 2>/dev/null && \
    echo "Session '$NAME' beendet." || \
    echo "Keine Session '$NAME' gefunden."
fi
```

Aufzurufen via `stop-claude.sh homelab` (oder jedem anderen Parameter) stoppt es die entsprechende Session mit dem Namen.

Das ist gut, aber noch nicht alles. Selber Tippen ist fehleranfällig. Auf dem Mac richte ich also Shell-Funktionen in `~/.zshrc` ein, die mir den Start einer solchen Session ganz leicht ermöglichen:
```bash
# >>> FUNCTIONS >>>
# Startet `claude --remote-control` mit entsprechendem Kontext in tmux via Skript
# Ist dann in der Claude App einsehbar
claude-general() { ssh nas '~/claude-workspace/start-claude.sh general'; }
claude-homelab() { ssh nas '~/claude-workspace/start-claude.sh homelab'; }
claude-sport()   { ssh nas '~/claude-workspace/start-claude.sh sport'; }

# Beendet die jeweilige tmux-Session
stop-claude()          { ssh nas "~/claude-workspace/stop-claude.sh ${1:-general}"; }
stop-claude-homelab()  { ssh nas '~/claude-workspace/stop-claude.sh homelab'; }
stop-claude-sport()    { ssh nas '~/claude-workspace/stop-claude.sh sport'; }
stop-claude-general()  { ssh nas '~/claude-workspace/stop-claude.sh general'; }
# <<< FUNCTIONS <<<
```

Die erste "Stop"-Funktion nimmt einen freien Parameter entgegen, die drei weiteren sind "convenience"-Funktionen, um Schreibfehler zu vermeiden.

Auf meinem MacBook kann ich nun also ganz simpel via `claude-sport` eine Session in tmux auf dem NAS öffnen. Terminal geht wieder zu und ich wechsle in die angenehme UI der Desktop oder mobilen App.

### Auf dem iPhone/iPad

Wer mich kennt weiß, ich liebe mein iPad und ich schreibe auch mit Claude sehr gern vom iPhone aus. Aber dauerhaft Sessions offen lassen, damit diese dauerhaft in der App verfügbar sind? Wenn ich viel tippe, läuft der Kontext auch voll. Es ist best practice, eine Session irgendwann auch zu beenden und frisch zu starten - und das wollte ich auch vom Mobilgerät.

Glücklicherweise gibt es in Apple Kurzbefehle-App ein SSH-Modul! Wir können also zwei Kurzbefehle bauen, die uns eine der Sessions startet bzw. auch wieder beendet 😎. Aber auch hier fiel ich über einen Stolperstein und das Auflisten der tmux Sessions funktionierte nicht zuverlässig. Das brauchen wir aber, um aktive Sessions zu sehen und beenden zu können. Also noch ein schnelles Skript `list-tmux-sessions.sh` (`chmod +x list-tmux-sessions.sh` nicht vergessen):
```bash
#!/bin/bash

docker exec claude-code tmux ls 2>/dev/null | awk -F: '{print $1}'
```

Damit kann es losgehen. Legen wir die Kurzbefehle an.

**Kurzbefehl "Claude starten":**

| Schritt | Aktion | Einstellung |
|---------|--------|-------------|
| 1 | Liste | `generell`, `sport`, `homelab`|
| 2 | Aus Liste auswählen | Aus `Liste` wählen |
| 3 | Skript über SSH ausführen | `~/claude-workspace/start-claude.sh [Ausgewähltes Objekt]` |
| 4 | Mitteilung anzeigen | Ergebnis von Schritt 3 |

**Kurzbefehl "Claude stoppen":**

| Schritt | Aktion | Einstellung |
|---------|--------|-------------|
| 1 | Skript über SSH ausführen | `~/claude-workspace/list-tmux-sessions.sh` |
| 2a | Wenn | `Shell-Skripteingabe` (als Text) `hat keinen Wert` |
| 3 | Mitteilung | `Es existieren keine aktiven tmux Sessions im Claude Container` |
| 2b | Sonst | |
| 3 | Text teilen | Ergebnis Schritt 1 · Trennzeichen: Neue Zeile |
| 4 | Variable setzen | Name: `Sessions` · Wert: Ergebnis Schritt 3 (b) |
| 5 | Liste | `alle` |
| 6 | Zu Variable hinzufügen | Wert: `Liste` · Variable: `Sessions` |
| 7 | Aus Liste auswählen | Variable `Sessions` |
| 8 | Skript über SSH ausführen | `~/claude-workspace/stop-claude.sh [Ausgewähltes Objekt]` |
| 9 | Mitteilung anzeigen | Ergebnis Schritt 8 |

Diesen beiden Kurzbefehlen gebe ich noch einen schönen Namen und ein Icon und lege sie mir als Widget in den Widgetbereich.

Ich kann nun per Tap aufs Widget eine tmux-Session starten und direkt in die Claude App springen, um zu chatten. Kein Sprung mehr ins Terminal. Dank iCloud ist das Widget auch auf dem iPad und dem MacBook vorhanden, sodass ich diese überall nutzen kann. Mit dem "stop"-Widget kann ich die Sessions beenden.

Für mich ist das ziemlich cool! Ausschließlich ausgehende HTTPS-Verbindungen, kein offener Port, kein Tunnel, kein Thema mit CGNAT. Die Claude App auf dem iPhone sieht die Session mit einem grünen Dot. Man öffnet sie, tippt, und die Antwort kommt — während Claude im Container auf dem NAS rechnet, mit Zugriff auf alle MCPs, alle Dateien, den ganzen Kontext.

Sportanalysen von überall. Die gewohnte UI von Anthropic. Kein Terminal mehr und trotzdem eine KI, die mich kennt!

## Fazit

Ich weiß, das war ein echt langer Beitrag und es ist auch die zweite Version. Ich wollte das erst als Serie bauen, aber ganz ehrlich, ich kam einfach zwischen meinen ganzen Spielereien nicht zum Schreiben. Das Thema begeistert mich wirklich und ich habe so viele Iterationen gedreht, bis ich zu diesem Setup kam, dass meine vorigen Posts (wie der, den ich nun überarbeitet habe) alle bereits veraltet gewesen wären. Ich weiß, dies ist kein typisches Tutorial, aber die Auflistung aller einzelnen Befehle hätte den Rahmen gesprengt. Ich hoffe, mein Setup gibt dir Ideen und du findest dein ganz persönliches Setup für eine KI, die _dich_ kennt - viel Spaß!