---
author: ["Chrischi"]
title: "NAS als Private Cloud #1: Eigener Tunnel mit virtuellem privaten Server, Reverse Proxy & WireGuard"
slug: "nas als private cloud 1 eigener tunnel mit virtuellem privaten server reverse proxy und wireguard"
date: "2025-07-28"
draft: false
description: "Eine Private Cloud geht auch als DIY-Lösung mit eigenem Server. In Teil 1 starten wir das Setup."
summary: "Wenn wir das heimische Netzwerk aus dem Internet heraus erreichen, aber keine Portweiterleitung einrichten wollen, geht dies nicht nur über Cloudflare. Ein solcher Tunnel kann auch selbst eingerichtet werden: Mit virtuellem privaten Server (VPS) und einem VPN. Dabei nutzen wir heute WireGuard."
ShowToc: true
TocOpen: false
tags: ["Synology", "WireGuard", "Zoraxy", "Virtual Private Server"]
categories: ["Tutorials", "Synology NAS"]
series: ["Private Cloud mit einem Synology NAS"]
cover:
  image: featured-image.webp
  caption: Der Tunnel zur Cloud - Erstellt von ChatGPT
---

## Wieso nicht einfach Cloudflare?

In den beiden letzten Artikeln habe ich mich des Problems angenommen, das [heimische NAS via Cloudflare Tunnel aus dem Internet heraus erreichbar zu machen](/synology-nas-als-private-cloud-via-cloudflare-tunnel-ohne-portweiterleitung) und dann auch noch [abzusichern](/cloudflare-tunnel-mit-extra-authentifizierung). Das Setup funktioniert soweit, aber es hat zwei Haken:

* Den ersten Haken erwähnte ich bereits im entsprechenden Post: Der Status des Streamings ist in der Community ungewiss
* Und natürlich geht unser Traffic über Cloudflare. Jeder darf selbst entscheiden, ob und in wie weit man Cloudflare vertrauen möchte, aber ich verstehe jeden, der seinen Traffic gern vollständig in der eigenen Hand hat.

Wie im Beitrag zum Setup des Cloudflare Tunnels erwähnt, haben manche von uns einfach nicht die Möglichkeit, Portfreigaben einzurichten und müssen deshalb Kompromisse eingehen. Wenn wir keine Portfreigaben einrichten können oder wollen, dann brauchen wir eine weitere Komponente, mit der wir aus dem Internet heraus auf unsere Services im Heimnetzwerk zugreifen können.

Eine Möglichkeit, die uns sehr viel Kontrolle ermöglicht, wäre ein _eigener Zugriffstunnel mit Hilfe eines virtuellen privaten Servers, eines Reverse Proxies und WireGuard_. Ganz ohne Cloudflare und genau dieses Setup möchte ich hier starten.

## Ein Wort (bzw. Absatz) der Warnung

Ich sage so einfach, wir nutzen einen _privaten virtuellen Server_ (kurz VPS), da dieser viel Kontrolle in die eigene Hand legt. Aber Kontrolle ist Macht und _mit großer Macht kommt auch große Verantwortung (Onkel Ben)_. Ein virtueller privater Server - gerade, wenn dieser als Cloud Service irgendwo gehostet ist - ist ein vollständiger Server, der aus dem Internet heraus erreichbar ist. Ein Server, auf dem ihr vermutlich Admin-Rechte besitzt und der wahrscheinlich auch erstmal keine Firewall aktiviert hat. Kurzum, ein Server, den ihr selbst bestmöglich schützen müsst!

Klingt abschreckend, aber probiere es trotzdem aus! Meine Empfehlung für VPS Hosting Services ist [Hetzner](https://www.hetzner.com/de/cloud). Die bieten für den Anfang gute Tutorials zur [Ersteinrichtung eines Ubuntu Servers](https://community.hetzner.com/tutorials/howto-initial-setup-ubuntu/de) und der [grundlegenden Sicherheit](https://community.hetzner.com/tutorials/howto-initial-setup-ubuntu/de) bei frischen Servern. Außerdem sind die wirklich günstig. Aber Achtung, am Ende des Tutorials werden wir `nftables` nutzen, um den Datenverkehr auf unserem Server zu steuern. Mit `nftables` kann man aber auch das "Eingangstor" etwas schließen und Firewall-Regeln etablieren. Wenn ihr dieser Ersteinrichtung folgt, lasst erstmal die Finger von `iptables` und der `ufw`. Wenn ihr dies am Ende dieses Teils immer noch einrichten wollt, könnt ihr das machen.

Mit diesem Disclaimer komme ich aber auch schon zu den Voraussetzungen.

## Unser Ziel

Mein (oder vermutlich unser) Problem ist noch immer, dass ich gern Services aus meinem Heimnetzwerk erreichen möchte, wenn ich unterwegs bin und dies kriege ich aufgrund von [CGNat](https://de.wikipedia.org/wiki/Carrier-grade_NAT) nicht "einfach so" gelöst.

Das Ziel ist deshalb, einen _virtuellen privaten Server_ im Internet zu hosten (beispielsweise bei [Hetzner](https://www.hetzner.com/de/cloud/)) und diesen als Tunnel-Gateway zu meinem Heimnetz einzurichten.

```
|------------------------------|                                                |-----------------------|
|                              |                 |--------------|               |                       |
|   Meine Geräte im Internet   |  --- HTTPS -->  |   Mein VPS   |  <-- VPN -->  |   Mein Heimnetzwerk   |   
|                              |                 |--------------|               |                       |
|------------------------------|                                                |-----------------------|
```

Mein VPS soll als Art _Türsteher_ agieren und ein paar Anforderungen erfüllen:
- Eingehenden Traffic nur mit TLS verschlüsselt (HTTPS) erlauben
- Nette Endpunkte für Services bereitstellen (in etwa so: `nextcloud.meinedomain.de`, `photos.meinedomain.de`, ...)
- Manche Endpunkte offen im Internet bereitstellen (dafür aber sicher via SSO und Authentication)
- Zugang zu anderen Endpunkten nur erlauben, wenn der anfragende Client per VPN verbunden ist (nicht offen im Internet)

Wir werden einige Dinge installieren und konfigurieren müssen, um dieses Setup zu erreichen. Speziell, wenn du auch ein NAS hast, das sich beim Thema _WireGuard_ etwas querstellt. Deshalb ist dieser Beitrag in mehrere Teile aufgeteilt. Hier in **Teil 1** wollen wir erstmal den Server weitestgehend vorbereiten und die notwendigen Apps installieren.

## Welche Voraussetzungen gibt es?

Um diesem Tutorial folgen zu können, brauchst du...

* ... einen Server, den du aus dem Internet heraus erreichen kannst und auf dem du root Rechte hast
* ... eine Domain oder einen DynDNS, wie beispielsweise von [IPv64](https://ipv64.net/) oder [DuckDNS](https://www.duckdns.org/), die auf deinen Server zeigt
* ... ein NAS mit mindestens einem Kernel Version 3.10 - ansonsten wird WireGuard nicht laufen ([hier ist eine Kompatibilitätsliste](https://github.com/runfalk/synology-wireguard?tab=readme-ov-file#compatibility-list))*

*_Du brauchst natürlich kein NAS, wenn du ein anderes System, wie einen Raspberry Pi von außen erreichbar machen möchtest. Der zweite Teil dieses Tutorials befasst sich aber mit den Gegebenheiten eines NAS._

Wenn du also all diese Voraussetungen erfüllst, können wir loslegen.

## Starten wir das Setup

Jetzt da es tatsächlich losgeht und wir bereits einen Überblick haben, was wir erreichen wollen, schauen wir uns die zu installierenden Komponenten einmal an:

| Rolle             | Applikation | Grund                                                                                      | Installationsweise |
|-------------------|-------------|--------------------------------------------------------------------------------------------|--------------------|
| VPN               | WireGuard   | Damit verbinden wir uns zum Heimnetz                                                       | nativ / bare metal |
| Reverse Proxy     | Caddy       | Zuständig für das Routing zu ansprechenden Subdomains und Bezug von Zertifikaten für HTTPS | nativ / bare metal |
| Container Manager | Docker      | Zum Aufbau unserer weiteren Apps                                                           | nativ / bare metal |
| DNS Server        | dnsmasq     | Zur Erstellung schöner Subdomains, die nur aus dem VPN-Netz erreichbar sind                | nativ / bare metal   |
| VPN Admin Seite   | wireguard-ui| Macht die Konfiguration der Clients sehr viel einfacher                                    | Docker Container   |
| SSO Provider      | Authentik   | Zur Absicherung unserer Routen ins Internet. Bietet besseren Auth-Schutz                   | Docker Container   |

Dass _Docker_ nativ installiert werden muss, wird klar sein. Bei _WireGuard_ und _Caddy_ habe ich mich dafür entschieden, weil diese Applikationen recht systemnah sind. Caddy gibt auf seiner [Installationsseite](https://caddyserver.com/docs/install#static-binaries) sogar an, _"Install Caddy as a system service. This is strongly recommended, especially for production servers."_. Daran halten wir uns natürlich, denn so kann Caddy zwischen den Netzwerkinterfaces `eth0` und `wg0` routen, ohne, dass wir große Routingtabellen anlegen müssen.

WireGuard nativ ergibt Sinn, damit eben genau dieses Netzwerkinterface `wg0` auf dem Host liegt. WireGuard ist so auch unabhängig von Docker und wir können Automatisierungen nutzen, um Neustarts einzurichten, wenn die Konfig geändert wird (über watcher). Wie immer, mein Tutorial ist _opinionated_, dir steht frei, es anders umzusetzen.

### WireGuard

Nun soll es aber losgehen. Als erstes installieren wir WireGuard. Meine Befehle setze ich auf einem Ubuntu Server ab und evtl. können diese bei deinem System abweichen. WireGuard gibt aber [in ihrer Doku](https://www.wireguard.com/install/) an, wie es auf jedem System installiert werden kann. Bei Ubuntu reicht:
```
sudo apt install wireguard -y
```

Dem [Quickstart](https://www.wireguard.com/quickstart/) (für uns angepasst) folgend, müssen wir nun das Netzwerk Interface anlegen und mit unserer `wg0` verknüpfen. Die Anlage des Interfaces sollte auch die entsprechenden Kernel Module laden.
```
sudo ip link add dev wg0 type wireguard
```

Dann vergibst du einen IP-Addressbereich, ich nutze im Tutorial das Beispiel `10.8.0.1/24`. Dir steht frei, auch das anzupassen (denk dann aber später daran).
```
sudo ip address add dev wg0 10.8.0.1/24
```

Jetzt erstellst du die `wg0.conf`, die später deine Konfiguration enthält:
```
sudo touch /etc/wireguard/wg0.conf
```

Denn wenn diese Datei nicht existiert, können wir diese nicht mit der Konfig für das `wg0` Interface verlinken:
```
sudo wg setconf wg0 /etc/wireguard/wg0.conf
```

Und zum Schluss sollten wir das Interface noch starten:
```
sudo ip link set up dev wg0
```

Nun teste bitte einmal, ob folgender Befehl einen Fehler wirft oder nicht (dann kommt kein Ergebnis):
```
modprobe wireguard
```

Sofern keine Meldung kommt, kannst du weiter machen. Denn als nächstes wollen wir den WireGuard Service immer neustarten, wenn sich diese Konfigdatei `wg0.conf` ändert. Dafür erstellen wir einen `watcher`, die diese Datei(en) überwacht und bei Bedarf einen Service aufruft, der `wireguard` neu startet.

Die benötigten Dateien werden angelegt:
```
sudo touch /etc/systemd/system/wg-quick-watcher@.path
sudo touch /etc/systemd/system/wg-quick-watcher@.service
```

Und nun mit Leben befüllt:
```
sudo nano /etc/systemd/system/wg-quick-watcher@.path
```

In diese `.path` Datei muss nun folgender Inhalt:
```
[Unit]
Description=Watcher for WireGuard via wg-quick(8) for %I

[Path]
PathChanged=/etc/wireguard/%i.conf

[Install]
WantedBy=multi-user.target
```

Kurze Erklärung zu den Abschnitten:
> `[Unit] => Description`: Beschreibt, wozu dieser Watcher erstellt wird (inkl. Link zu ManPage)  
> `[Path] => PathChanged`: Enthält den Pfad, der auf Änderungen überwacht werden soll. Hier mit Variable für alle Configs. Bei uns wird dort nur `wg0.conf` liegen.  
> `[Install] => WantedBy`: Beschreibt den Systemzustand, ab welchem dieser Service ausgeführt werden kann (Beispiel nach einem Neustart). `multi-user.target` heißt, das System muss bereit sein, dass sich alle User anmelden könnten. Also fertig hochgefahren.

Speichern und schließen:
```
<ctrl>+X
Y
<Enter>
```

Nun geht es weiter mit der `.service` Datei. Erst öffnen:
```
sudo nano /etc/systemd/system/wg-quick-watcher@.service
```

Dann mit folgendem Inhalt befüllen:
```
[Unit]
Description=Restarter for WireGuard via wg-quick(8) for %I
After=network-online.target
StartLimitIntervalSec=10
StartLimitBurst=5

[Service]
Type=oneshot
ExecStart=/usr/bin/systemctl restart wg-quick@%i.service

[Install]
RequiredBy=wg-quick-watcher@%i.path
```

Auch hier eine kurze Erläuterung:
> `[Unit]`: Erst kommt wieder eine Beschreibung. Dann wird sichergestellt, dass das Netzwerk bereit ist. Die Limits verhindern eine Blockierung des Services nach zu vielen Restart-Versuchen des Services in kurzer Zeit.  
> `[Service]`: Der danach kommende Befehl soll nur einmalig ausgeführt werden, dann kommt der eigentliche Restart-Befehl.  
> `[Install]`: Zeigt die Verbindung zu dem eben erstellten Watcher, sodass diese der Watcher diesen Restart ausführen darf.

Auch diese Datei speichern und schließen:
```
<ctrl>+X
Y
<Enter>
```

Jetzt müssen wir den Watcher noch aktivieren:
```
sudo systemctl enable --now wg-quick-watcher@wg0.path
```

Mit dem folgenden Befehl kannst du prüfen, ob der Watcher aktiviert ist:
```
systemctl list-unit-files 'wg-quick-watcher@*.path'
```

Und damit sollte WireGuard erstmal fertig vorbereitet sein. Weiter zum nächsten Schritt.

### Caddy

Auf Caddys [Installationsdokumentation](https://caddyserver.com/docs/install#static-binaries) habe ich bereits weiter oben schon einmal verwiesen. Und diese Schritte befolgen wir nun auch (so ungefähr 😉).

Erstmal müssen wir uns entscheiden, wo wir das Caddy Binary (die auszuführende Datei) auf dem Server ablegen. Der gewählte Pfad sollte sich in euer `$PATH` Variable wiederfinden, damit wir Caddy auch ausführen können, ohne immer den exakten Pfad angeben zu müssen. Die Speicherorte, die in `$PATH` enthalten sind, kannst du prüfen:

```
echo $PATH
```

Eine Mit `:` getrennte Liste an Ordner wird ausgegeben. Bei mir sind beispielsweise `/usr/bin/` und `/usr/local/bin` enthalten. Einer dieser Ordner passt schon. Ich werde Caddy in `/usr/local/bin` ablegen (du musst erneut Pfade anpassen, wenn du es woanders haben möchtest).
```
cd /usr/local/bin
```

Dann laden wir die aktuelle Version von deren GitHub Releases herunter. Passe im folgenden Befehl bitte die zu ladende Version, sowie deine CPU-Architektur an:
```
sudo wget https://github.com/caddyserver/caddy/releases/download/v{VERSION}/caddy_{VERSION}_linux_{CPU-ARCHITEKTUR}.tar.gz
```

Die aktuelle Version zum Zeitpunkt, als ich das hier schreibe, ist `2.10.0` und ich habe eine `amd64` Architektur. Du könntest ggf. auch `arm64` haben.

Dann kann das dort enthaltene Caddy Binary entpackt werden :
```
sudo tar -C /usr/local/bin -xzf caddy_{VERSION}_linux_{CPU-ARCHITEKTUR}.tar.gz caddy
```

Danach kannst du das Archiv wieder löschen:
```
sudo rm caddy_{VERSION}_linux_{CPU-ARCHITEKTUR}.tar.gz
```

Als nächstes müssen wir laut [Doku](https://caddyserver.com/docs/running#manual-installation) eine `caddy` Gruppe und User anlegen, dann noch die entsprechende systemd unit Datei besorgen, diese laden und fertig. Dann können wir Routen im `Caddyfile` hinterlegen. Also weiter gehts, erst Gruppe und User mit eigenem Home-Verzeichnis (alles aus der Caddy Doku):
```
sudo groupadd --system caddy
```
```
sudo useradd --system \
  --gid caddy \
  --create-home \
  --home-dir /var/lib/caddy \
  --shell /usr/sbin/nologin \
  --comment "Caddy web server" \
  caddy
```

Es gibt zwei Caddy systemd unit Dateien. Eine für den Service des Reverse Proxies und eine für die API. Wir wollen den Service. Also speichern wir uns diese unter dem Pfad `/etc/systemd/system/caddy.service`. Wir laden das offizielle `caddy.service` aus deren GitHub und speichern es auf unserem Server.

```
sudo curl -o /etc/systemd/system/caddy.service https://raw.githubusercontent.com/caddyserver/dist/refs/heads/master/init/caddy.service
```

Wenn `curl` nicht verfügbar ist, `sudo apt install curl` und dann öffne nun die Datei mit `nano`...
```
sudo nano /etc/systemd/system/caddy.service
```

...und prüfe die Ordnerpfade des `ExecStart` und `ExecReload` (markierte Zeile 12 + 13):
```{hl_lines=[12,13]}
[Unit]
Description=Caddy
Documentation=https://caddyserver.com/docs/
After=network.target network-online.target
Requires=network-online.target
AssertFileIsExecutable=/usr/local/bin/caddy

[Service]
Type=notify
User=caddy
Group=caddy
ExecStart=/usr/local/bin/caddy run --environ --config /etc/caddy/Caddyfile
ExecReload=/usr/local/bin/caddy reload --config /etc/caddy/Caddyfile --force
TimeoutStopSec=5s
LimitNOFILE=1048576
LimitNPROC=512
PrivateTmp=true
ProtectSystem=full
AmbientCapabilities=CAP_NET_BIND_SERVICE

[Install]
WantedBy=multi-user.target
```

Dort steht im Standard `/usr/bin/caddy`, wenn du die Datei aber - wie ich - unter `/usr/local/bin/caddy` abgelegt hast, musst du den Pfad hier anpassen. Natürlich auf deinen gewählten Pfad. Außerdem siehst du hier, dass User und Gruppe mit "caddy" ausgefüllt sind, weshalb wir eben die Gruppe und den User angelegt haben. Dann ggf. speichern und schließen:
```
<crtl>+X
Y
<Enter>
```

Bevor wir caddy starten können, müssen wir nun noch ein `Caddyfile` anlegen. In dieser Datei speichern wir später all unsere gewünschten proxy Routen.
```
sudo mkdir /etc/caddy
sudo nano /etc/caddy/Caddyfile
```

Nun kannst du den ersten Inhalt einfügen und anpassen:
```
{
  # Used for automatic HTTPS
  email {DEINE EMAIL-ADRESE}
}

deinedomain.de {
  redir https://tueti.space{uri}
}
```

Mit diesen Einträgen wird deine Domain auf den Port 5000 zeigen, über welchen später `wireguard-ui` erreichbar sein wird. Du kannst auch einen `redir` auf andere Seiten einrichten oder `wireguard-ui` via `vpn.deinedomain.de` oder so erreichbar machen. Ganz, wie du magst.

Wir müssen nun noch dafür sorgen, dass der User `caddy`, den wir angelegt haben, Lesezugriff auf dieses `Caddyfile` besitzt, deshalb:
```
sudo chmod 644 /etc/caddy/Caddyfile
```

Jetzt kann `caddy` endlich gestartet werden:
```
sudo systemctl daemon-reload
sudo systemctl enable --now caddy
```
Und nun kannst den Status prüfen:
```
systemctl status caddy
```

Wenn dieser als **active** angezeigt wird, hat alles geklappt und du solltest _deinedomain.de_ in den Browser eingeben können und wirst aktuell auf diese Webseite umgeleitet. Das werden wir später natürlich ändern, aber Caddy läuft somit!

Damit sind WireGuard und Caddy installiert 🎉

### Docker

Noch motiviert? Dann geht es mit Docker weiter, das letzte Tool, das wir nativ installieren. Auch Docker bietet einen [super Installationsanleitung für alle Systeme](https://docs.docker.com/engine/install/), an die ich mich für [Ubuntu](https://docs.docker.com/engine/install/ubuntu/) halte. Auch hier gebe ich der Vollständigkeit halber die Befehle wieder. Aber bitte immer prüfen.

Erst werden alle möglichen Pakete, über die Docker installiert sein könnte, deinstalliert. Auf einem frischen System sollte nichts davon vorhanden sein:
```
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

Dockers `apt` repository muss erst eingerichtet werden, dafür müssen wir erst Dockers GPG Schlüssel holen:
```
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

Danach das Repository zu deinen `apt` Quellen hinzufügen:
```
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

Und aus diesen Quellen wird nun via `apt install` einfach Docker installiert:
```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Und damit ist auch Dockers Installation abgeschlossen 🥳

### wireguard-ui als Container installieren

Wir kommen nun zurück zu WireGuard, genauer zur Admin-Oberfläche. Der Entwickler stellt in seinem [GitHub Repo](https://github.com/ngoduykhanh/wireguard-ui/) verschiedene `docker compose` Templates zur Verfügung, um diese für gewisse Szenarien zu verwenden. Ein solches Szenario ist die Nutzung der Applikation mit einem nativ installierten WireGuard. Das wollen wir.

Auch hier sind meine Ordner wieder _opinionated_ und du kannst dem folgen oder nicht. Dann denk aber bitte überall an die Anpassungen der Pfade, wenn diese von meinen abweichen. Ich lade meine docker compose Dateien in `~/docker/{ContainerName}` und als Template will ich das vorgegebene compose file nutzen. Also laden wir uns das erstmal herunter (wozu wir uns vorher das Zielverzeichnis anlegen):
```
mkdir -p ~/docker/wireguard-ui
cd ~/docker/wireguard-ui
```

Und dann laden wir die Datei, um sie uns anzuschauen:
```
curl -o docker-compose.yml https://raw.githubusercontent.com/ngoduykhanh/wireguard-ui/refs/heads/master/examples/docker-compose/system.yml
nano docker-compose.yml
```

Diese sollte so aussehen:
```{hl_lines=[15,17]}
version: "3"

services:
  wireguard-ui:
    image: ngoduykhanh/wireguard-ui:latest
    container_name: wireguard-ui
    cap_add:
      - NET_ADMIN
    # required to show active clients. with this set, you don't need to expose the ui port (5000) anymore
    network_mode: host
    environment:
      - SENDGRID_API_KEY
      - EMAIL_FROM_ADDRESS
      - EMAIL_FROM_NAME
      - SESSION_SECRET
      - WGUI_USERNAME=admin
      - WGUI_PASSWORD=admin
      - WG_CONF_TEMPLATE
      - WGUI_MANAGE_START=false
      - WGUI_MANAGE_RESTART=false
    logging:
      driver: json-file
      options:
        max-size: 50m
    volumes:
      - ./db:/app/db
      - /etc/wireguard:/etc/wireguard
```

Auch, wenn `version` mittlerweile nicht mehr benötigt wird, belassen wir die Struktur der Datei so. Du kannst das Passwort hier ändern oder in eine `.env` Datei auslagern oder einfach so belassen und später in der UI ändern (aber bitte zügig, das Interface wird vorerst über das Internet erreichbar sein!). Ein `SESSION_SECRET` kannst du auch erstellen, wenn du deine Cookies und andere Settings über Sessions hinweg, wenn der Container neu startet, behalten willst. Um einen 32stelligen Code zu bekommen, kannst du folgenden Befehl nutzen:
```
openssl rand -hex 32
```

Sobald du alles angepasst hast, schließe `nano` mit
```
<ctrl>+X
Y
<Enter>
```

Dann kannst du den Container starten:
```
sudo docker compose up -d
```

Nun läuft der Container, aber wir haben keinen Port deklariert und in _unserem_ Browser können wir kein `localhost:5000` nutzen, denn der Container läuft ja nicht auf _unserem_ Rechner.

Jetzt kommt wieder Caddy ins Spiel und wir legen eine Route zu `wireguard-ui` an:
```
sudo nano /etc/caddy/Caddyfile
```

Du kannst nun entweder den `redir` aus deiner Hauptdomain entfernen oder eine weitere Route anlegen. Auf jeden Fall muss am Ende folgende Route in deinem Caddyfile stehen - die Subdomain dabei ist dabei irrelevant, solange ein Wildcard Eintrag bei deinem deinem Domain Registrar vorliegt:
```
vpn.deinedomain.de {
  reverse_proxy localhost:5000
}
```

Danach Caddy und auch das `wg0` Interface neustarten (ich musste das Interface einmal explizit beenden):
```
sudo systemctl restart caddy
sudo wg-quick down wg0
sudo systemctl restart wg-quick@wg0
```

Nun öffne doch mal die von dir gesetzte URL im Browser und die WireGuard-UI sollte sich öffnen! 🥳

### Routing für das VPN erlauben

Bisher sieht alles gut aus, aber der Teufel steckt im Detail und aktuell würde beim VPN noch einiges nicht ganz funktionieren und deshalb müssen wir uns nun dem Routing der Interfaces widmen. Ich mache das anhand von `nftables`, werde aber nur grundlegend auf die einzelnen Aspekte eingehen. Der Artikel wird sonst einfach zu lang.

Aber wir müssen zwei Dinge tun, um VPN-Verbindungen sauber zu erlauben. Portforwarding aktivieren und dafür gewisse Regeln setzen. Die Erlaubnis ist schnell erteilt:
```
sudo nano /etc/sysctl.conf
```

Suche dort die beiden Einträge
```
...
# net.ipv4.ip_forward = 1
...
# net.ipv6.conf.all.forwarding = 1
...
```
und entferne die `#`, damit diese Zeilen nicht mehr auskommentiert sind. Dann die Änderungen speichern
```
<ctrl>+X
Y
<Enter>
```

Danach die Änderungen aktivieren:
```
sudo sysctl -p
```

Nun gehen wir an die `nftables`. Dieses Tool ist dabei mehr, als nur eine Firewall, es übernimmt auch Routing und NAT Übersetzungsregeln. Wichtig für uns ist, hier können wir einerseits festlegen, welche Pakete _von unserem Server angenommen_ werden sollen und welche Pakete wie geroutet werden müssen. Man kann natürlich auch ausgehende Verbindungen konfigurieren. Das Tool ist mächtig! Wer lieber mit `iptables` arbeitet, macht das gern. Aber wenn du weiß, dass du lieber `iptables` nutzt, dann weißt du sicher auch, wie du die Regeln dort rein bekommst.

**Wichtig**, wenn du anfangs meinem Link zum Hetzner-Beitrag gefolgt bist und deinen Server so abgesichert hast (entweder per ufw oder mit iptables), dann solltest du diese wieder deaktivieren, wenn du nun `nftables` nutzt, um deinen Server zu konfigurieren. Wir wollen keine konkurrierenden Systeme. Alternativ kannst du das benötigte, grundlegende Regelwerk auch in `iptables` umsetzen. Du musst es dir dann nur _übersetzen_.

Mit diesem Hinweis können wir nun `nftables` nutzen und dessen Regelwerk wird in der Datei `/etc/nftables.conf` persistiert. Diese Datei werden wir also anpassen:
```
sudo nano /etc/nftables.conf
```

Das grundlegende Regelwerk, was du für dein VPN brauchst, ist dieses hier und die Erklärung ist im Code-Block.
```
#!/usr/sbin/nft -f

# Leere alle vorhandenen Regeln
flush ruleset

# =======================================================
# Filter-Tabelle: Regelt, welcher Verkehr erlaubt/verworfen wird
# =======================================================
table inet filter {
    # --------------------------------------------------------------------------
    # Chain 'input':
    # Steuert den Verkehr, der FÜR den Server selbst bestimmt ist (z.B. SSH-Verbindungen zum Server,
    # HTTP und HTTPS zu Caddy, Loopbacks zu lokalen Diensten, wie WireGuard-UI).
    # --------------------------------------------------------------------------
    chain input {
        type filter hook input priority 0;
        # 'type filter': Dies ist eine Filter-Chain.
        # 'hook input': Diese Chain wird für alle eingehenden Pakete angewendet, die für den lokalen Server bestimmt sind.
        # 'priority 0': Legt die Reihenfolge fest, in der Chains ausgeführt werden (Standard).

        # Standardmäßig alles zulassen, was für den Server eingeht
        policy accept;

        # DNS-Anfragen von WireGuard-Clients an den dnsmasq-Server erlauben
        # UDP für normale DNS-Abfragen und TCP für größere DNS-Antworten oder Zonentransfers (privat unwahrscheinlich, aber schadet nicht)
        iifname wg0 udp dport 53 ip saddr 10.8.0.0/24 counter accept comment "Allow DNS from VPN clients (UDP)"
        iifname wg0 tcp dport 53 ip saddr 10.8.0.0/24 counter accept comment "Allow DNS from VPN clients (TCP)"

        # Loopback-Verkehr immer zulassen (wichtig für lokale Server-Kommunikation wie Caddy -> WG-UI)
        iif "lo" accept
    }

    # Chain für weitergeleiteten Verkehr (Forward):
    # Regelt den Verkehr, der DURCH den Server geleitet wird (z.B. VPN-Clients ins Internet)
    chain forward {
        type filter hook forward priority 0;
        policy accept; # Standardmäßig alles weiterleiten

        # MSS Clamping für WireGuard, um Fragmentierungsprobleme zu vermeiden
        oifname "wg0" tcp flags syn tcp option maxseg size set 1240
        iifname "wg0" tcp flags syn tcp option maxseg size set 1240

        # Ungültige Verbindungen verwerfen (immer eine gute Idee)
        ct state invalid drop
    }

    # Chain für ausgehenden Verkehr (Output):
    # Regelt den Verkehr, der vom Server selbst generiert wird.
    chain output {
        type filter hook output priority 0;
        policy accept; # Standardmäßig alles zulassen, was der Server aussendet
    }
}

# =======================================================
# NAT-Tabelle: Regelt Network Address Translation (Masquerading)
# =======================================================
table ip ipv4_nat {
    # Chain für Postrouting: Wird angewendet, kurz bevor das Paket das Interface verlässt.
    chain postrouting {
        type nat hook postrouting priority 100;

        # VPN NAT: Maskiere Verkehr, der VOM WireGuard kommt und NACH AUSSEN geht
        # iifname "wg0": Paket kommt vom WireGuard-Interface
        # oifname "eth0": Paket geht über das öffentliche Interface (Anpassen, wenn nicht "eth0")
        masquerade iifname "wg0" oifname "eth0" comment "NAT for WireGuard clients to Internet"
    }
}
```

Mit diesem Regelwerk bleibt dein Server sehr offen! Ich würde empfehlen, es noch etwas zu verschärfen und es so anzulegen:
```
#!/usr/sbin/nft -f

# Leere alle vorhandenen Regeln
flush ruleset

# =======================================================
# Filter-Tabelle: Regelt, welcher Verkehr erlaubt/verworfen wird
# =======================================================
table inet filter {

    # --------------------------------------------------------------------------
    # Chain 'input':
    # Steuert den Verkehr, der FÜR den Server selbst bestimmt ist (z.B. SSH-Verbindungen zum Server,
    # HTTP und HTTPS zu Caddy, Loopbacks zu lokalen Diensten, wie WireGuard-UI).
    # --------------------------------------------------------------------------
    chain input {
        type filter hook input priority 0;
        # 'type filter': Dies ist eine Filter-Chain.
        # 'hook input': Diese Chain wird für alle eingehenden Pakete angewendet, die für den lokalen Server bestimmt sind.
        # 'priority 0': Legt die Reihenfolge fest, in der Chains ausgeführt werden (Standard).

        policy drop;
        # Die Standard-Policy ('policy drop') besagt, dass jedes Paket, das nicht explizit durch eine
        # 'accept'-Regel zugelassen wird, verworfen wird. Ist eine sichere Standardeinstellung.

        # Bestehende und zugehörige Verbindungen erlauben:
        # Erlaubt, dass Pakete, die Teil einer bereits etablierten (z.B. nach einem SSH-Login)
        # oder einer zugehörigen (z.B. FTP-Datentransfer nach einem Kontrollkanal) Verbindung sind, passieren dürfen.
        ct state established,related accept

        # Loopback-Interface erlauben:
        # Ermöglicht, dass Programme auf dem Server über 'localhost' oder '127.0.0.1' miteinander kommunizieren können.
        # Zwingend notwendig für Dienste wie Caddy, die auf andere lokale Dienste (z.B. WireGuard-UI auf Port 5000) zugreifen.
        iif "lo" accept

        # ICMP (Internet Control Message Protocol) erlauben:
        # Ermöglicht Ping-Anfragen (IPv4) und Ping-Antworten (IPv6), hilft bei Netzwerkdiagnosen.
        ip protocol icmp accept
        ip6 nexthdr icmpv6 accept

        # SSH erlauben:
        # Erlaubt eingehende Verbindungen Standard-Port 22.
        # Dadurch kannst du dich per SSH mit deinem Server verbinden.
        # TODO: Hier anpassen, wenn du einen anderen Port nutzt, sonst sperrst du dich aus!
        tcp dport 22 accept

        # HTTP (Port 80) und HTTPS (Port 443) erlauben:
        # Ermöglicht, dass Webserver (wie Caddy selbst) Anfragen über HTTP und HTTPS entgegennehmen können.
        tcp dport { 80, 443 } accept

        # DNS-Anfragen von WireGuard-Clients an den dnsmasq-Server erlauben
        # UDP für normale DNS-Abfragen und TCP für größere DNS-Antworten oder Zonentransfers (privat unwahrscheinlich, aber schadet nicht)
        iifname wg0 udp dport 53 ip saddr 10.8.0.0/24 counter accept comment "Allow DNS from VPN clients (UDP)"
        iifname wg0 tcp dport 53 ip saddr 10.8.0.0/24 counter accept comment "Allow DNS from VPN clients (TCP)"

        # WireGuard VPN-Port erlauben:
        # Erlaubt eingehende UDP-Verbindungen auf dem WireGuard-Port 51820,
        # damit sich deine VPN-Clients mit dem Server verbinden können.
        udp dport 51820 accept
    }

    # --------------------------------------------------------------------------
    # Chain 'forward':
    # Steuert den Verkehr, der DURCH den Server geleitet wird, d.h. von einem Interface zu einem anderen (wg0 zu eth0 und anders herum).
    # Dies ist kritisch für den VPN-Verkehr (Clients untereinander und Clients ins Internet).
    # --------------------------------------------------------------------------
    chain forward {
        type filter hook forward priority 0;
        # 'hook forward': Diese Chain wird für Pakete angewendet, die der Server weiterleiten soll.

        policy drop;
        # Die Standard-Policy 'drop' bedeutet, dass weitergeleiteter Verkehr standardmäßig blockiert wird,
        # sofern er nicht explizit erlaubt ist. Dies ist eine gute Sicherheitseinstellung für einen Router/Gateway.

        # MSS Clamping (Maximum Segment Size Clamping) für WireGuard:
        # Passt die maximale Segmentgröße von TCP-Paketen an, die über das WireGuard-Interface gesendet werden.
        # Dies hilft, Fragmentierung zu vermeiden und Verbindungsprobleme (insbesondere bei HTTPS) zu lösen,
        # die bei VPNs aufgrund des zusätzlichen Headers auftreten können.
        oifname "wg0" tcp flags syn tcp option maxseg size set 1240 # Für Pakete, die über wg0 hinausgehen
        iifname "wg0" tcp flags syn tcp option maxseg size set 1240 # Für Pakete, die über wg0 hereinkommen

        # Erlaube etablierte und zusammengehörige Verbindungen für Forwarding:
        # Ermöglicht den Fluss von Paketen, die zu einer bereits bekannten oder zugehörigen Verbindung gehören.
        # Dies ist essenziell, damit Antworten auf Anfragen durch den Tunnel zurückfließen können.
        ct state related,established accept

        # Erlaube Verkehr vom WireGuard-Interface (wg0) zum öffentlichen Interface (eth0):
        # Dies ist die Kernregel, die es VPN-Clients ermöglicht, auf das Internet zuzugreifen.
        # 'iifname "wg0"': Paket kommt vom WireGuard-Interface.
        # 'oifname "eth0"': Paket ist dafür bestimmt, das öffentliche Interface zu verlassen.
        # 'ct state new,established,related': Erlaubt neue Verbindungen sowie bereits etablierte und zugehörige.
        iifname "wg0" oifname "eth0" ct state new,established,related accept comment "Allow WireGuard to Internet"

        # Erlaube Verkehr vom öffentlichen Interface (eth0) zum WireGuard-Interface (wg0):
        # Ermöglicht, dass Antworten aus dem Internet über das öffentliche Interface hereinkommen und
        # zum entsprechenden WireGuard-Client zurückgeleitet werden.
        iifname "eth0" oifname "wg0" ct state established,related accept comment "Allow Internet to WireGuard (responses)"

        # Erlaube Verkehr zwischen VPN-Clients:
        # Ermöglicht, dass sich WireGuard-Clients gegenseitig erreichen können, indem der Verkehr
        # innerhalb des 'wg0'-Interfaces weitergeleitet wird.
        iifname "wg0" oifname "wg0" accept comment "Allow traffic between VPN clients"

        # Diese Regel ist redundant, da die spezifischeren Regeln darüber den WireGuard-Traffic bereits abdecken.
        # In einer 'policy drop' Chain sollte man so spezifisch wie möglich sein.
        # Sie würde Pakete erlauben, die vom wg0 kommen, unabhängig davon, wohin sie gehen.
        # Für das aktuelle Setup nicht schädlich, aber könnte entfernt werden.
        iifname "wg0" accept

        # Die folgenden Regeln (Kommentar und ICMP) sind im Kontext der vorherigen Regeln nicht mehr nötig,
        # da die spezifischeren "accept"-Regeln darüber bereits den relevanten Traffic abdecken.
        # 'iifname != "wg0" oifname "wg0" accept' würde Verkehr ins VPN von anderen Interfaces erlauben.
        # Diese Regel ist auskommentiert, d.h. sie ist inaktiv.
        # iifname != "wg0" oifname "wg0" accept

        # ICMP (Ping) & ICMPv6 erlauben für weitergeleiteten Verkehr:
        # Ermöglicht, dass Ping-Anfragen und -Antworten durch den Server geleitet werden.
        # Nützlich für die Diagnose von Netzwerkproblemen durch die VPN-Clients.
        meta l4proto icmp accept
        meta l4proto icmpv6 accept

        # Ungültige weitergeleitete Pakete verwerfen:
        # Eine weitere wichtige Sicherheitsmaßnahme, um fehlerhafte oder manipulierte Pakete zu blockieren.
        ct state invalid drop
    }

    # --------------------------------------------------------------------------
    # Chain 'output':
    # Steuert den Verkehr, der VOM Server selbst generiert wird (z.B. Server-Updates, Logs senden).
    # --------------------------------------------------------------------------
    chain output {
        type filter hook output priority 0;
        # 'hook output': Diese Chain wird für alle ausgehenden Pakete des lokalen Servers angewendet.

        policy accept;
        # Die Standard-Policy 'accept' bedeutet, dass der Server Pakete standardmäßig uneingeschränkt senden kann.
    }
}

# ==============================================================================
# Tabelle 'ip ipv4_nat':
# Definiert NAT-Regeln speziell für IPv4-Pakete.
# NAT (Network Address Translation) ist notwendig, damit die internen IP-Adressen der VPN-Clients
# beim Zugriff auf das Internet durch die öffentliche IP-Adresse des Servers ersetzt werden.
# ==============================================================================
table ip ipv4_nat {

    # --------------------------------------------------------------------------
    # Chain 'prerouting':
    # Wird für Pakete angewendet, BEVOR Routing-Entscheidungen getroffen werden.
    # Hier könnte z.B. Port-Weiterleitung (DNAT) konfiguriert werden.
    # Für dein aktuelles VPN-Szenario ist diese Chain nicht zwingend notwendig.
    # --------------------------------------------------------------------------
    chain prerouting {
        type nat hook prerouting priority -100;
        # 'hook prerouting': Wird für eingehende Pakete angewendet, bevor sie geroutet werden.
        # 'priority -100': Standardpriorität für NAT-Prerouting.
    }

    # --------------------------------------------------------------------------
    # Chain 'postrouting':
    # Wird für Pakete angewendet, KURZ BEVOR sie das Ausgangsinterface verlassen.
    # Hier findet das Masquerading statt.
    # --------------------------------------------------------------------------
    chain postrouting {
        type nat hook postrouting priority 100;
        # 'hook postrouting': Wird für Pakete angewendet, nachdem sie geroutet wurden und kurz vor dem Verlassen des Servers.
        # 'priority 100': Standardpriorität für NAT-Postrouting.

        # VPN NAT (Masquerading):
        # Diese entscheidende Regel ändert die Quell-IP-Adresse von Paketen, die vom WireGuard-Interface kommen
        # und über dein öffentliches Interface (z.B. eth0) ins Internet gehen.
        # Die Quell-IP wird dabei durch die öffentliche IP-Adresse des Servers ersetzt.
        # Das ist notwendig, damit die Internet-Server die Pakete beantworten können.
        # Passe "eth0" an, wenn dies nicht der Name deines öffentlichen Interfaces ist!
        masquerade iifname "wg0" oifname "eth0" comment "NAT for WireGuard clients to Internet"
    }
}
```

Wenn du dich für eine Konfiguration entschieden hast, speichere diese wieder:
```
<ctrl>+X
Y
<Enter>
```

Danach können wir die Syntax überprüfen, die Konfig anwenden und nftables neustarten:
```
sudo nft -c -f /etc/nftables.conf
sudo systemctl restart nftables
```

Das war eine Handvoll, oder? Ich hoffe, mit den Kommentaren konnte die Konfiguration ein wenig nachvollzogen werden. Auch, wenn das zweite Regelset den Zugriff von außen etwas strikter filtert, reicht das als Absicherung eines Servers nicht komplett aus. Denk noch über _Fail2Ban_ und eine dedizierte Firewall nach. Absicherung des Servers ist außerhalb des Fokuses für diesen Beitrag, aber da ich das Thema mit dem `nftables` Regelwerk ein wenig angeschnitten habe, wollte ich es nicht unkommentiert lassen.

## Für heute fertig!

Und damit ist deine Konfiguration abgeschlossen!

Für diesen Beitrag soll es das auch gewesen sein, da dieser schon irre lang geworden ist und einfach sehr viel Konfiguration beinhaltet. Wir haben WireGuard (inklusive WireGuard-UI), Caddy und Docker installiert und darüber hinaus das Routing für die VPN-Nutzung vorbereitet. Das war Einiges! Im zweiten Teil widmen wir uns dann der eigentlichen Konfiguration des VPN Netzes. Wir werden WireGuard-UI nutzen, um VPN-Clients zu erstellen und wir werden unser NAS ins VPN-Netz bringen, was ebenfalls nochmal einiger Konfiguration bedarf.

Im dritten und letzten Teil geht es dann um die Absicherung der Routen. Welche Routen nur aus dem VPN heraus und welche öffentlich und wie implementieren wir einen Authentication Service. Bis dahin haben wir uns alle einen Kaffee verdient! ☕️