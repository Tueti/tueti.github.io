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
---

Willkommen zum dritten - und letzten - Teil der Tutorialreihe zur Einrichtung des NAS als Private Cloud. Nachdem in [Teil 1](/nas-als-private-cloud-1-eigener-tunnel-mit-virtuellem-privaten-server-reverse-proxy-und-wireguard/) unser virtueller privater Server vorbereitet und in [Teil 2](/nas-als-private-cloud-2-wireguard-für-synology-nas-und-konfiguration-des-vpn/) das VPN-Netz aufgebaut wurde, geht es jetzt mit diesem Teil an den Abschluss des Setups. Dieser Beitrag widmet sich zwei Themen. Erstens wollen wir einen DNS Server einrichten, welcher dafür sorgt, dass Clients, die mit dem VPN verbunden sind, die nicht öffentlichen Endpunkte trotzdem mit einer schönen URL erreichen können. Damit kommen wir auch schon zum Ziel des heutigen Beitrags.

## Das Ziel vor Augen: Was wollen wir erreichen?

Ich möchte die Weboberfläche meines NAS nicht publik ins Internet stellen, will aber trotzdem von überall darauf zugreifen können und das mit der URL `dms.meinedomain.de` und nicht via `10.8.0.2:5001`. _Aber Moment mal_, wir haben eine ähnliche Subdomain `vpn.meinedomain.de` doch bereits für WireGuard-UI eingerichtet? Ja, aber in Caddy und damit ist dieser Endpunkt öffentlich! Jeder Mensch im Internet könnte aktuell `vpn.meinedomain.de` erreichen. Auch dieser Endpunkt soll aus der Öffentlichkeit verschwinden und VPN erfordern.

Dinge, wie `nextcloud.meinedomain.de` oder eventuell sogar bei `photos.meinedomain.de` könnte man darüber nachdenken, ob diese öffentlich erreichbar sein sollen. Und da kommen wir zum zweiten Thema. Nachdem wir den ersten Teil erledigt haben, möchte ich den Dienst _Authentik_ als Docker Container hochziehen und mit diesem Dienst eine sichere Authentifizierung der Nutzer erzwingen. Dann muss sich derjenige, der die dahinterliegende Seite öffnen will, erst authentifizieren und wird erst danach auf die eigentliche Webseite geleitet.

## Fangen wir an mit dem DNS Server