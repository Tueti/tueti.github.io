---
author: ["Chrischi"]
title: "NAS als Private Cloud #3: Private und öffentliche Routen plus Authentication"
slug: "nas als private cloud 3 private und oeffentliche routen plus authentication"
date: "2025-08-11"
draft: false
description: "Eine Private Cloud geht auch als DIY-Lösung mit eigenem Server. In Teil 3 öffnen wir Routen ins Internet"
summary: "In Teil 1 haben wir unser Setup vorbereitet und in Teil 2 das VPN-Netz aufgesetzt. Der Zugriff mit verbundenem VPN-Client auf Endpunkte im VPN-Netz steht. Im dritten und letzten Teil öffnen wir einige Services ins Internet, sichern diese ab und erstellen ansprechende interne Routennamen."
ShowToc: true
TocOpen: false
tags: ["Synology", "WireGuard", "dnsmasq", "Authentik", "Virtual Private Server"]
categories: ["Tutorials", "Synology NAS"]
series: ["Private Cloud mit einem Synology NAS"]
cover:
  image: featured-image.webp
  caption: Der Tunnel zur Cloud - Erstellt von ChatGPT
sitemap:
  priority: 0.8
---

Willkommen zum dritten Teil der Tutorialreihe zur Einrichtung des NAS als Private Cloud. Nachdem in [Teil 1](/nas-als-private-cloud-1-eigener-tunnel-mit-virtuellem-privaten-server-reverse-proxy-und-wireguard/) unser virtueller privater Server vorbereitet und in [Teil 2](/nas-als-private-cloud-2-wireguard-für-synology-nas-und-konfiguration-des-vpn/) das VPN-Netz aufgebaut wurde, geht es jetzt mit diesem Teil an den Abschluss des Setups. Dieser Beitrag widmet sich zwei Themen. Schauen wir und das Ziel einmal an.

## Das Ziel vor Augen: Was wollen wir erreichen?

Genau genommen möchte ich zwei Ziele erreichen. Erstens will ich einen lokalen DNS Server einrichten, der uns ansprechende Domains für lokale Routen zur Verfügung stellt. Diesen DNS Server möchte ich dann in den Client Configs der VPN Clients hinterlegen, sodass diese ansprechenden Routen aufgelöst wird. Genau genommen wird es _eine_ Route, zu unserem Reverse Proxy _Caddy_. In Zusammenarbeit mit Caddy sollen dann Subdomains zu internen Routen aufgelöst werden. Grund ist, dass in meinem Setup die IP von Services gleich sein kann und nur der Port anders ist. Das ist via DNS nicht darstellbar.

Das zweite Ziel ist die extra Authentifizierung mit _Authentik_. Einige Endpunkte sollen ganz bewusst über das Internet erreichbar sein und diese will ich mit einem extra Service absichern. Da manche Services mit _Authentik_ integrierbar sind, könnten wir teilweise sogar ein Single-Sign-On erreichen.

Eine weiteres Ziel habe ich: Ich will, dass alle Routen per HTTPS abgesichert sind, um diese nervigen _"Diese Webseite ist nicht sicher"_-Warnungen nicht zu bekommen. Also diese Meldungen sind in der freien Wildbahn natürlich _nicht_ nervig und wirklich sinnvoll. Aber im Kontext unseres _privaten_ Netzes, will ich das nicht. Aber das führt zu einer zusätzlichen Herausforderung.

## Eigene private Routen

Eigene private Routen können wir nicht einfach in _Caddy_ anlegen. Jede Route dort ist über eine öffentliche URL erreichbar. Deshalb können wir aktuell `vpn.meinedomain.de` ohne verbundenem VPN Client aus dem Internet heraus erreichen. Bei unserem Registrar haben wir den Wildcard A Record auf die öffentlich erreichbare IP unseres Services gerichtet.

Das war auch wichtig, damit Caddy auf einfache Art und Weise ein TLS Zertifikat für diesen Route bekommen konnte. Lass uns hierzu mal einen kurzen Blick hinter die Kulissen werfen, um zu verstehen, wieso wir nun einen etwas anderen Weg gehen müssen.

### Wie bekommt man eigentlich ein TLS Zertifikat?

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

### DNS Challenge vorbereiten

Caddy kann von Haus aus keine DNS Challenge durchführen, aber es gibt `caddy-dns` Plugins für diverse Domain Registrare (Cloudflare, route53, duckDNS, Digital Ocean, Hetzner und auch INWX). Besucht dafür einfach mal die [Caddy Download Seite](https://caddyserver.com/download) und sucht nach eurem Registrar. Für INWX finde ich das Plugin `caddy-dns/inwx`.
!["Das Download Center von Caddy mit der Suche nach INWX"](/caddy_downloadcenter.webp "Das Download Center von Caddy mit der Suche nach INWX")

Achte dabei darauf, bei der "Platform" oben links nicht dein vorausgewähltes Betriebssystem zu nutzen, sondern "Linux amd64" oder "Linux arm64", je nachdem, welche Architektur dein Server hat. Selektierst du dieses Paket, kannst du es per _Download_ herunterladen.

Dieses Paket ist ein Binary von Caddy, mit diesem einen Plugin. Damit werden wir das vorhandene Binary ersetzen. Dann sollte erstmal alles so laufen, wie vorher, nur haben wir dann Zugriff auf das Modul, welches bei INWX (du hoffentlich bei deinem Registrar) eine DNS Challenge durchführen kann.

Es gibt auch andere Möglichkeiten, die DNS Challenge durchzuführen. Das Tool [acme.sh](https://github.com/acmesh-official/acme.sh) ist ebenfalls beliebt, kann gut mit Caddy zusammenarbeiten, aber wie es nunmal so ist. Es führen oft mehrere Wege nach Rom oder zum Zertifikat. Wenn die Caddy Community mir die Möglichkeit bietet, dann will ich erstmal diesen Weg gehen, um meine Tools im Überblick zu behalten.

Also, tauschen wir das vorhandene Binary mit diesem aus. Im [ersten Teil](/nas-als-private-cloud-1-eigener-tunnel-mit-virtuellem-privaten-server-reverse-proxy-und-wireguard/#caddy) der Reihe haben wir `caddy` unter `/usr/local/bin/` abgelegt. Lass uns einmal die aktuelle Version untersuchen, dann das Binary ersetzen und die Versionen vergleichen. Dafür starten wir damit, wieder per `ssh` auf den Server zu gehen:

```
ssh USERNAME@DEINEDOMAIN.DE
caddy --version
caddy list-modules
```

Damit siehst du die aktuelle Caddy Version, sowie alle installierten Module. Am Ende siehst du, wie viele Standard Module, wie viele Extra Module und wie viele unbekannte Module vorhanden sind.

Von dieser Version machst du ein Backup (oder du kannst sie auch löschen, da wir sie einfach wieder herunterladen und dort platzieren können)

```
mv /usr/local/bin/caddy /usr/local/bin/_caddy.bkp
```

Dann per `exit` die SSH Session verlassen und die heruntergeladene Caddy Datei auf den Server kopieren (passe dabei deinen lokalen Pfad an):
```
exit
scp ~/Downloads/caddy_linux_amd64_custom NUTZER@DEINEDOMAIN:/usr/local/bin/caddy
```

Danach geht's wieder per `ssh` auf den Server, um die Version nun kurz ebenfalls zu untersuchen:
```
ssh USERNAME@DEINEDOMAIN.DE
caddy --version
caddy list-modules
```

--- 

WAS SOLLTE BEIM VERGLEICH AUFFALLEN?

Danach dnsmasq installieren, dann Caddyfile anpassen mit