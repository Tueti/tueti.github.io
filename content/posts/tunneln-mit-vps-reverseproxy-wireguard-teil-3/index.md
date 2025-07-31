---
author: ["Chrischi"]
title: "NAS als Private Cloud #3: Lokaler DNS Server für schönere Endpunkt-Adressen"
slug: "nas als private cloud 3 lokaler dns server fuer schoenere endpunkt-adressen"
date: "2025-08-11"
draft: false
description: "Eine Private Cloud geht auch als DIY-Lösung mit eigenem Server. In Teil 3 geht es um den eigenen DNS für schöne Routen"
summary: "In Teil 1 haben wir unser Setup vorbereitet und in Teil 2 das VPN-Netz aufgesetzt. Der Zugriff mit verbundenem VPN-Client auf Endpunkte im VPN-Netz steht. Im dritten und letzten Teil erstellen wir ansprechende interne Routennamen."
ShowToc: true
TocOpen: false
tags: ["Synology", "WireGuard", "dnsmasq", "Virtual Private Server"]
categories: ["Tutorials", "Synology NAS"]
series: ["Private Cloud mit einem Synology NAS"]
cover:
  image: featured-image.webp
  caption: Der Tunnel zur Cloud - Erstellt von ChatGPT
sitemap:
  priority: 0.8
---

_Ein Hinweis zur Transparenz vorab: Ich habe dieses Setup einige Zeit als Lösung für mein CGNat-Problem genutzt, bin aber mittlerweile auf [Pangolin](https://github.com/fosrl/pangolin) umgestiegen, da es eine All-In-One-Lösung mit ungefähr demselben Tech Stack ist, wie ich ihn in dieser Tutorialreihe selbst aufbaue. Nichtsdestotrotz kann diese Lösung natürlich verwendet werden. Ein Beitrag zu Pangolin wird folgen._

---

Willkommen zum dritten Teil der Tutorialreihe zur Einrichtung des NAS als Private Cloud. Nachdem in [Teil 1](/nas-als-private-cloud-1-eigener-tunnel-mit-virtuellem-privaten-server-reverse-proxy-und-wireguard/) unser virtueller privater Server vorbereitet und in [Teil 2](/nas-als-private-cloud-2-wireguard-für-synology-nas-und-konfiguration-des-vpn/) das VPN-Netz aufgebaut wurde, möchte ich mich hier der _Usability_ widmen.

## Das Ziel vor Augen: Was wollen wir erreichen?

Ich möchte irgendwann einige Services, die aktuell über Cloud-Dienste konsumiert werden, auf der eigenen Hardware bereitstellen. Das heißt aber auch, dass manche Dienste von meiner Familie genutzt werden. Auf deren Geräten _WireGuard_ zu installieren und konfigurieren und zu zeigen, wie man über das Kontrollzentrum der Geräte VPN aktiviert (sollte es mal aus sein, es könnte ja dauerhaft laufen), ist kein Problem. Aber dann für gewisse Dienste IPs, statt schönen DNS-Namen zu nutzen, ist deutlich schwieriger.  
_"Wie war nochmal die Adresse vom Foto-Dienst"_? - _"10.8.0.14:5725"_  
... das wird nichts. Da muss es etwas geben, wie _"photos.vpn.meinedomain.de"_.

Deshalb geht es erstmal darum, unsere internen Routen aufzuhübschen:
- Eine URL, die man sich merken kann (am besten ohne Ports)
- Keine HTTPS-Warnung, da diese im _internen Netz_ eher nervig sind (für das Internet natürlich sehr sinnvoll!)

## Setup für eigene private Routen

Eigene private Routen können wir nicht einfach in _Caddy_ anlegen. Jede Route dort ist über eine öffentliche URL erreichbar. Deshalb können wir aktuell `vpn.meinedomain.de` ohne verbundenem VPN Client aus dem Internet heraus erreichen. Bei unserem Registrar haben wir den Wildcard A Record auf die öffentlich erreichbare IP unseres Servers gerichtet.

Das war auch wichtig, damit Caddy auf einfache Art und Weise ein TLS Zertifikat für diesen Route bekommen konnte. Für Routen bei unserer Domain, die aber nicht öffentlich erreichbar sein sollen, geht das nicht so einfach. Lass uns hierzu mal einen kurzen Blick hinter die Kulissen werfen, um zu verstehen, wieso wir nun einen etwas anderen Weg gehen müssen.

## Wie bekommt man eigentlich ein TLS Zertifikat?

Es gibt verschiedene Arten, ein TLS Zertifikat zu bekommen. _Let's Encrypt_ beschreibt diese in [der eigenen Doku](https://letsencrypt.org/docs/challenge-types/) recht ausführlich. Für uns wichtig sind dabei die zwei Wege.

Die gewöhnliche Methode - die auch Caddy für unsere `vpn.*` Subdomain nutzte, ist die **HTTP-01 Challenge** (oder einfach _HTTP Challenge_). Hierbei ruft unser ACME Client (Bei uns in Caddy integriert) einen Token bei der Zertifikatsstelle ab und legt diesen auf dem Webserver ab. Das heißt, Caddy fragt bei Let's Encrypt:  
"_Hey, ich brauche ein Zertifikat für `vpn.deinedomain.de`_"  
Let's Encrypt erwidert:  
"_Alles klar, hier ist ein Token. Beweise mir, dass die Seite hinter der URL dir gehört und lege den Token unter `vpn.deinedomain.de/.well-known/acme-challenge/{TOKEN}` ab._"  
Caddy erhält den Token, legt diesen ab und lässt Let's Encrypt dies prüfen. Findet Let's Encrypt den Token, bekommt Caddy das Zertifikat, da der Besitz der Seite hinter der Domain bewiesen wurde.

Diese Art ist ziemlich einfach umzusetzen, aber hat für unser Vorhaben einen großen Nachteil. Die URL muss öffentlich erreichbar sein und auf einen öffentlich erreibaren Server zeigen, auf dem der Token abgelegt werden kann. Würde `vpn.deinedomain.de` auf eine IP im VPN Netz zeigen (10.8.0.1), dann würde Let's Encrypt diesen Endpunkt nicht erreichen, den Token nicht finden und kein Zertifikat ausstellen.

Es gibt aber noch eine zweite Variante, die **DNS-01 Challenge** (oder _DNS Challenge_). Mit dieser Methode beweisen wir nicht, dass und _der Webserver hinter der URL gehört_, sondern, dass uns die _Domain selbst gehört_. Hierbei muss der Token also nicht auf dem Webserver landen, sondern als `TXT Record` bei der Domain und dieser würde von Let's Encrypt geprüft - ohne auf die A Records zu achten. Die Verhandlung liefe also so ab:  
Caddy: "_Hey, ich brauche ein Zertifikat für `vpn.deinedomain.de`_ und will die DNS Challenge nutzen"  
Let's Encrypt: "_Alles klar, hier ist ein Token-String, hinterlege diesen als `TXT Record` bei der Domain_"  
Caddy erhält den Token und braucht nun einen Weg, um diesen Eintrag tatsächlich bei unserem Registrar zu hinterlegen. Sobald der `TXT Record` vorhanden ist und Let's Encrypt diesen gefunden hat, wird das Zertifikat ausgestellt. Dabei werden die A Records ignoriert, da gar nicht verfolgt werden muss, wohin diese zeigen. Caddy hat auch so bewiesen, dass wir die Hoheit über die Domain haben.

Bei diesem Weg gibt es zwei wesentliche Unterschiede zur _HTTP Challenge_. Erstens müssen wir Caddy tatsächlich ermächtigen, den Eintrag zu setzen (Zugangsdaten oder Token vom Domain Registrar), aber zweitens können wir damit Zertifikate für Domains holen, die **keinen A Record Eintrag haben**.

## DNS Challenge mit Caddy vorbereiten

Caddy kann von Haus aus keine DNS Challenge durchführen, aber es gibt `caddy-dns` Plugins für diverse Domain Registrare (Cloudflare, route53, duckDNS, Digital Ocean, Hetzner und auch INWX). Besucht dafür einfach mal die [Caddy Download Seite](https://caddyserver.com/download) und sucht nach eurem Registrar. Für INWX finde ich das Plugin `caddy-dns/inwx`.
!["Das Download Center von Caddy mit der Suche nach INWX"](/caddy_downloadcenter.webp "Das Download Center von Caddy mit der Suche nach INWX")

Achte dabei darauf, bei der "Platform" oben links nicht dein vorausgewähltes Betriebssystem zu nutzen, sondern "Linux amd64" oder "Linux arm64", je nachdem, welche Architektur dein Server hat. Selektierst du dieses Paket, kannst du es per _Download_ herunterladen.

Dieses Paket ist ein Binary von Caddy, mit diesem einen Plugin. Damit werden wir das vorhandene Binary ersetzen. Dann sollte erstmal alles so laufen, wie vorher, nur haben wir dann Zugriff auf das Modul, welches bei INWX (du hoffentlich bei deinem Registrar) eine DNS Challenge durchführen kann.

Es gibt auch andere Möglichkeiten, die DNS Challenge durchzuführen. Das Tool [acme.sh](https://github.com/acmesh-official/acme.sh) ist ebenfalls beliebt, kann gut mit Caddy zusammenarbeiten, aber wie es nunmal so ist. Es führen oft mehrere Wege nach Rom oder zum Zertifikat. Wenn die Caddy Community mir die Möglichkeit bietet, dann will ich erstmal diesen Weg gehen, um meine Tools im Überblick zu behalten.

Also, tauschen wir das vorhandene Binary mit diesem aus. Im [ersten Teil](/nas-als-private-cloud-1-eigener-tunnel-mit-virtuellem-privaten-server-reverse-proxy-und-wireguard/#caddy) der Reihe haben wir `caddy` unter `/usr/local/bin/` abgelegt. Lass uns einmal die aktuelle Version untersuchen, dann das Binary ersetzen und die Versionen vergleichen. Dafür starten wir damit, wieder per `ssh` auf den Server zu gehen:

```
ssh USERNAME@DEINEDOMAIN.DE
caddy --version
caddy list-modules | grep dns
```

Damit siehst du die aktuelle Caddy Version, sowie alle installierten Module. Am Ende siehst du, wie viele Standard Module, wie viele Extra Module und wie viele unbekannte Module vorhanden sind.

Von dieser Version machst du ein Backup (optional, da wir es immer wieder herunterladen können) und löscht dann das eigentliche File:
```
sudo tar -czvf /usr/local/bin/caddy.tar.gz /usr/local/bin/caddy
sudo rm /usr/local/bin/caddy
```

Dann per `exit` die SSH Session verlassen und die heruntergeladene Caddy Datei auf den Server kopieren (passe dabei deinen lokalen Pfad an):
```
exit
scp ~/Downloads/caddy_linux_amd64_custom NUTZER@DEINEDOMAIN:~/caddy
```
Achtung, wenn du SSH auf einen anderen Port gelegt hast, wird dieser (anders, als bei SSH) mit einem `P` als Großbuchstabe spezifiziert, `scp -P 2222 ~/Downloads/...`.

Danach geht's wieder per `ssh` auf den Server, wir legen die Datei in den korrekten Ordner (per scp hatten wir die Rechte nicht), machen sie ausführbar und vergleichen sie mit der ursprünglichen Version:
```
ssh USERNAME@DEINEDOMAIN.DE
sudo mv ~/caddy /usr/local/bin/
sudo chmod +x /usr/local/bin/caddy
caddy --version
caddy list-modules | grep dns
```

Die Version ist die gleich, wie bei der vorigen Installation. Bei der Auflistung der DNS-Module sollte nun aber euer DNS Provider dabei sein. Für mich ist das `dns.providers.inwx`. Du solltest natürlich deinen DNS Provider sehen.

Damit können wir Caddy nutzen, um Zertifikate per DNS Challenge von unserem Provider zu bekommen.

## dnsmasq einrichten

Bevor wir nun mit Caddy weitermachen (aktuell sollte noch alles so funktionieren, wie vorher), will ich `dnsmasq` einrichten. Dazu müssen wir das Paket installieren und ein wenig konfigurieren.

Linux Distributionen nutzen im Standard `systemd-resolve` mit der Konfigurationsdatei `/etc/resolve.conf`, um Namensauflösungen zu vollziehen. Nachdem wir also `dnsmasq` installiert haben, müssen wir einmal dnsmasq konfigurieren (dazu nutzen wir dessen config `/etc/dnsmasq.conf`) und dann in einer eigenen `/etc/resolve.conf` hinterlegen, dass für Namensauflösungen dnsmasq verwendet werden soll.

### Legen wir los mit der Installation

```
sudo apt update && sudo apt upgrade
sudo apt install dnsmasq
```

Es kann sein, dass bei der Installation ein Fehler auftritt, dass eine andere Applikation auf Port 53 horcht und deshalb der Listener nicht erstellt werden konnte. Das ist erstmal kein Problem. Damit konnte dnsmasq erstmal nur nicht gestartet werden. Da es vermutlich `systemd-resolved` ist, was auf den Port hört, sind die nächsten Schritte trotzdem identisch für alle (egal, ob mit oder ohne diesen Fehler). Denn wir wollen `systemd-resolved` sowieso beenden und vor allem deaktivieren, damit es sich nicht beim nächsten Reboot des Servers wieder aktiviert.

```
sudo systemctl stop systemd-resolved
sudo systemctl disable systemd-resolved
```

Danach bennen wir die bestehende `/etc/resolve.conf` um. Du könntest sie auch löschen, aber was, wenn man die Konfiguration später zurückdrehen will? Danach erstellen wir eine eigene Version der Datei, welche als `nameserver` den `localhost` nutzt. Damit verweist unsere Version dieser Datei dann auf `dnsmasq` und so werden interne Anfragen zur Namensauflösung ebenfalls an `dnsmasq` weitergeleitet. Außerdem müssen wir dafür sorgen, dass alle die Datei lesen können. All das passiert mit den folgenden Commands:
```
sudo mv /etc/resolve.conf /etc/resolve.conf.bkp
sudo touch /etc/resolve.conf
sudo echo "nameserver 127.0.0.1 >> /etc/resolve.conf
sudo chmod 644 /etc/resolve.conf
```

Jetzt editieren wir die Konfig-Datei für `dnsmasq`:
```
sudo nano /etc/dnsmasq.conf
```

Die Datei ist groß, deshalb gebe ich hier nur die Zeilen an, die geändert werden sollen - dabei muss das Hashsymbol entfernt werden, damit es kein Kommentar mehr ist:

| Alter Wert       | Neuer Wert      |
|------------------|-----------------|
| `#domain-needed` | `domain-needed` |
| `#bogus-priv`    | `bogus-priv`    |
| `#no-resolve`    | `no-resolve`    |
| Unter `#add other name servers here... #server=/localnet/192...` | `server=1.1.1.1` |
| Unter `#Add domains which you want to force... #address=/double-click.net...` | `address=/internal.{deinedomain.de}/10.8.0.1` |
| `#listen-adress=` | `listen-address=127.0.0.1,10.8.0.1` |

Letztendlich ist es egal, ob du die Zeilen so anpasst oder die neuen Werte einfach ganz ans Ende der Datei schreibst. Ich habe die Werte nur auch dort, wo sie in der Datei beschrieben werden. Mit dem folgenden Befehl kannst du nun prüfen, welche Zeilen in der Datei aktiviert sind, das Ergebnis siehst du (für dich wohl leicht angepasst) darunter.
```
grep -v -e "^#" -e "^$" /etc/dnsmasq.conf
```
`grep` steht für "Global Regular Expression Print" und kann Ausgaben anhand von _Regular Expression_ erzeugen. _Regular Expression_ sind sozusagen Muster, die in einem Text erkannt werden können und die Passagen, die auf dieses Muster zutreffen, werden selektiert. 

* `-v` steht für die Umkehrung der folgenden Muster. Also es soll alles ausgegeben werden, was _nicht_ dem Muster entspricht
* `-e "^#"` steht für _"Jede Zeile, die mit einem Hash-Symbol beginnt"_
* `-e "^$"` steht für _"Leere Zeile: '^' heißt Zeilenanfang und '$' heißt Zeilenende. Also Anfang direkt vor Ende, nicht dazwischen_

Somit gibt diese _Regular Expression_ aus, _"Alle Zeilen, die **nicht** (-v) mit einem Hashsymbol beginnen und **nicht** (-v) leer sind, aus der Datei /etc/dnsmasq.config"_. Das Ergebnis sollte so aussehen:
```
no-resolv
server=1.1.1.1
listen-address=127.0.0.1,10.8.0.1
domain-needed
bogus-priv
address=/internal.{deinedomain.de}/10.8.0.1
```

Eine kurze Erklärung dazu. `no-resolv` weist `dnsmasq` an, nicht den Standard-System-Resolver zu verwenden (den wir bereits deaktiviert haben). Der `server` ist der DNS-Server, der verwendet werden soll, wenn der Name nicht lokal aufgelöst werden kann (Web-Adressen, wie _google.de_). Hier nutzen wir Cloudflare, dir steht frei, welchen du nutzt. `listen-address` gibt an, auf welchen Interfaces `dnsmasq` auf Anfragen hören soll, wir nutzen beide Interfaces. `domain-needed` sagt, dass nur volle Domainnamen an die DNS Server geleitet werden. Dinge, wie "mycomputer" werden über `/etc/hosts` aufgelöst. `bogus-priv` verhindert umgekehrte DNS Lookups (PTR Records) und schlussendlich das Kernstück: `address` gibt an, dass die Domain selbst (`internal.deinedomain.de`), sowie alle Subdomains (`*.internal.deinedomain.de`) an 10.8.0.1 geleitet werden. Dahinter hängt dann unser Caddy, der diese Anfragen entgegennimmt und anhand seiner Proxy Routen weiterleiten kann. Statt `internal` kannst du hier wieder ein Kürzel wählen, wie du es möchtest. Als kleinen **Hinweis** könntest du überlegen, ob du eine Route nimmst, die auch aus deinem Heimnetz (ohne VPN) sinnvoll wäre. Dann könntest du das Setup auch lokal machen, aber auf `192.168.X.Y` auflösen und somit dieselben URLs im Heimnetz und VPN haben und dabei trotzdem immer lokal routen.

Jetzt müssen wir nochmal alles testen bzw. verifizieren und dann können wir `dnsmasq` (neu) starten und aktivieren, damit es nach Neustarts automatisch aktiviert wird.
```
sudo dnsmasq --test
sudo systemctl restart dnsmasq
sudo systemctl status dnsmasq
sudo systemctl enable dnsmasq
```

Sollte dein `dnsmasq` anfangs auf den Portfehler gelaufen sein, muss du in der zweiten Zeile nur `start` statt `restart` eingeben. Und damit läuft dein lokaler DNS Server!

## Caddyfile vorbereiten

Nachdem `dnsmasq` jetzt läuft, müssen wir die Routen in Caddy auch entgegennehmen und sauber auf die Services routen. Also aktualisieren wir als nächstes das `Caddyfile`.
```
sudo nano /etc/caddy/Caddyfile
```

Aufgrund des [ersten Teils](/nas-als-private-cloud-1-eigener-tunnel-mit-virtuellem-privaten-server-reverse-proxy-und-wireguard/) dieser Serie, sollte dein Caddyfile ungefähr so aussehen:

```
{
  # Used for automatic HTTPS
  email {DEINE EMAIL-ADRESSE}
}

vpn.deinedomain.de {
  reverse_proxy localhost:5000
}
```

Das müssen wir nun anpassen, um die DNS Challenge einzubauen und die gewünschten Routen aufzubauen. Diese können bei dir natürlich anders aussehen:
```
{
  # Globaler Block für DNS Challenge
  # Kann auch ein Token sein.
	acme_dns inwx {
		username <username>
		password <password>
		shared_secret <shared_secret>
	}
}

{
  # Globaler Block für HTTP Challenge
  # Dafür wird die Email-Adresse benötigt
  email {DEINE EMAIL-ADRESSE}
}

vpn.deinedomain.de {
  # Nutzt implizit HTTP Challenge
  reverse_proxy localhost:5000
}

*.internal.deinedomain.de {
  # Nutzt explizit die DNS Challenge
  tls {
    dns inwx
  }
  
  @nas host nas.internal.deinedomain.de
  reverse_proxy @nas 10.8.0.2:5001 {
    # Wird das DSM via HTTPS erreicht,
    # kann die Zertifikatswarnung unterdrückt werden
    transport http {
      tls_client_auth {
        insecure_skip_verify
      }
    }
  }

  @photos host photos.internal.deinedomain.de
  reverse_proxy @photos 10.8.0.2:2000

  @media host media.internal.deinedomain.de
  reverse_proxy @media 10.8.0.2:8097
}
```

Und damit sollte es geschafft sein. Datei speichern und schließen:
```
<ctrl> + X
Y
<enter>
```

Und abschließend Caddy noch einmal neustarten:
```
sudo systemctl restart caddy
```

Fertig, damit sollte Caddy Anfragen zu den entsprechenden `internal...` Subdomains an die korrekten Services leiten und die Routen sollten dank DNS Challenge per HTTPS abgesichert sein. Diese Domains haben aber keinen öffentlichen A Record, sondern werden vom _lokalen DNS Server_ an Caddy geleitet.

Als letztes müssen wir also den DNS Server bei unseren VPN Clients hinterlegen.

## DNS für VPN Clients hinterlegen

Jetzt hast du zwei Optionen, deine bestehenden WireGuard Clients zu aktualisieren. Entweder du editierst die `wg0.conf` überall manuell oder du wirfst die Clients nochmal aus WireGuard-UI heraus, editierst die _Global Config_, wo du bei den DNS Servern nun die VPN IP eintragen kannst und fügst diese Konfig dann nochmal bei deinen Clients hinzu (dabei musst du auch auf den Clients dann die alte Konfig löschen).

Solltest du in [Teil 2]() dein NAS mit einer WireGuard Konfig versehen haben, müsstest du das NAS eigentlich nicht ändern. Du wirst ja vermutlich nicht vom NAS aus (also per SSH oder aus dem DSM heraus) auf die lokalen Routen zugreifen, die wir eben erstellt haben. Bei denen geht es ja eher darum, dass du diese Domains auf deinem Rechner, Smartphone oder Tablet nutzen kannst.

Ich denke, mit diesen zwei Wegen kannst du die Anpassung der Clients selber durchführen und sobald einer, deiner Clients eine neue Konfig mit dem entsprechenden DNS Server hat, heißt es, deine Route mal zu testen. Öffne mal `https://dns.internal.deinedomain.de` und du solltest mit gültigem Zertifikat auf das DSM deines NAS geleitet werden. Mit solchen Domains lässt sich viel mehr anfangen und sie haben die Sicherheit, nur mit verbundenem VPN Client erreichbar zu sein.

Als optionalen nächsten Schritt könnten nun die Routen, die ins offene Internet gehen, noch mit Diensten, wie Authentik abgesichert werden. Wenn du das nicht tust, stell zumindest sicher, dass alle Dienste mit einem starken Passwort versehen sind, es keine bekannten Sicherheitslücken gibt und am besten auch Multi-Faktor-Authentication unterstützen.

Im nächsten Beitrag widme ich mich [Pangolin](https://github.com/fosrl/pangolin), was eine Art All-In-One-Lösung für unser Setup ist. Oder vielleicht eher eine Selfhosted-Lösung für Cloudflare Tunnels. Bis dahin erstmal viel Spaß mit deinem Setup!