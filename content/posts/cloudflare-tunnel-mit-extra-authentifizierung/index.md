---
author: ["Chrischi"]
title: "Cloudflare Tunnel mit extra Authentifizierung"
slug: "Cloudflare Tunnel mit extra Authentifizierung"
date: "2025-07-21"
draft: true
description: "Nachdem wir einen Cloudflare Tunnel aufgebaut und so unsere eigene _Private Cloud_ hochgezogen haben, sollten wir einige Pfade besonders schützen. Cloudflare bietet vielfältige und granulare Möglichkeiten, die Endpunkte vor unbefugtem Zugriff zu schützen. Lass uns das mal gemeinsam beleuchten."
ShowToc: true
TocOpen: false
tags: ["Synology", "Cloudflare"]
categories: ["Tutorials", "Synology NAS"]
series: ["Private Cloud mit einem Synology NAS"]
cover:
  image: featured-image.webp
  caption: Erstellt von ChatGPT
---

Unsere [Private Cloud](/synology-nas-als-private-cloud-via-cloudflare-tunnel-ohne-portweiterleitung) steht und ich erfreue mich noch immer regelmäßig daran. Aber ich würde sie gern mehr absichern. Einige URLs im Cloudflare Tunnel mit extra Authentifizierung versehen und genau diese Möglichkeit bietet uns der Dienst - selbst im "Free" Tier.

## Grundlegende Sicherheit bei Cloudflare

Bevor wir anfangen, mehr zu konfigurieren, lass uns einmal einen Blick darauf werfen, was Cloudflare im Standard bereits für uns tut. Sobald man seine Domain über Cloudflare routet, bekommt man eine Reihe an Sicherheitsfunktionen, die Cloudflare selbst in der  [eigenen Dokumentation beschreibt](https://developers.cloudflare.com/learning-paths/application-security/default-traffic-security/). Diese Sicherheitsmechanismen sind:

* Schutz vor DDoS Angriffen
* Verschlüsselte Verbindung via SSL/TLS
* DNS Security Extension (DNSSEC): Sichert die DNS Authentizität und schützt vor DNS Spoofing
* Browser Integrity Check (BIC): Soll sicherstellen, dass nur legitime Nutzer die Webseite besuchen

Unser NAS ist somit bereits gut vor automatisierten Zugriffen geschützt. Trotzdem kann es ja sein, dass gewisse Services auf unserem NAS auch nur einem bestimmten Personenkreis zugänglich gemacht werden sollen. Exakt so etwas möchte ich heute einrichten.

## Unser Ziel: Cloudflare Tunnel mit extra Authentifizierung

Ziel ist es, einen extra Schritt der Authentifizierung zu erschaffen. Bevor unser NAS überhaupt erreicht wird, soll es eine Seite geben, auf der eine Email Adresse angegeben werden muss. Gibt man eine Adresse an, die wir im Vorfeld berechtigt haben, bekommt der Besitzer der Email Adresse einen Verifizierungscode zugeschickt, den er eingeben muss. Erst nach erfolgreicher Verifizierung wird dann die Seite des NAS geöffnet und auch da muss man sich dann ganz normalen anmelden.

## Los geht's mit der Einrichtung

Ich hoffe, du hast dich etwas mit dem Cloudflare Dashboard auseinandergesetzt und kannst dort nun immer sicher zu deinem Zero Trust Dashboard, deinem Network Tunnel und den Hostnames navigieren. Öffne also bitte das Zero Trust Dashboard via [Cloudflare Dashboard](https://dash.cloudflare.com/), dann im Navigationsbaum links _Zero Trust_ und dort gibt es im neuen Navigationsbaum ein Modul _Access_.



========= AB HIER NEU

Dies bitte öffnen und auf _Policies_ gehen.

Eine _Policy_ ist ein Satz Regeln 





Eine _Rule Group_ ist ein Set an Regeln für die Zugriffsverwaltung, das wir modular wiederverwenden können. Wir können beispielsweise Regeln aufstellen, wie...
* ... jeder, der eine Email Adresse besitzt, die auf folgenden Namen endet: "@tueti.space"...
* ... jede, der folgenden Email Adressen
* ... jeder in dieser IP Range
* ... und viele Weitere

Da es sich um _Rule **Groups**_ handelt, können wir auch mehrere Regeln aufstellen und definieren, ob eine, ein Teilset oder alle Regeln zutreffen müssen. Und diese Regelgruppen können wir modular vor alle oder nur einzelne unserer Hostnames legen, sodass ein Zugriff auf diese Hostnames die Erfüllung aller definierten Zugriffsregeln voraussetzt.

Nun also auf "Add a group" klicken.

### Regel erstellen

Gib der Regel bei **Basic Information** einen Namen und im Kasten **Rules** wähle ich für das genannte Ziel der Email Verifizierung den _Selector_ "Emails" aus. Im _Value_ gebe ich nun alle Email Adressen an, die einen Verifizierungscode erhalten sollen. Das können all meine eigenen Email Adressen sein oder alle aus dem eigenen Haushalt, Freunde, Familie, wer auch immer für euch passt.

Dann speichern und du solltest nun in der Übersicht deiner _Rule Groups_ den Namen des eben angelegten Regelwerkes sehen.

### Eine Cloudflare Application erstellen

Wir müssen nun eine Application erstellen, die sich zwischen unsere URL und unserem Ziel (unser NAS) hängt und die extra Schicht an Authentifizierung bringt. Hierzu im selben _Access_ Modul des Navigationsbaums auf _Applications_ klicken und "Add an application" wählen. Unsere Applikation, unser NAS, ist _Self-Hosted_ also wählen wir auch das aus.

Nun müssen wir die Applikation konfigurieren. In den **Basic Information** können wir den Namen und die Sessiondauer angeben (wann muss sich jemand neu verifizieren).

Beim **Public hostname** musst du exakt die hostname eintragen, den du im Tunnel konfiguriert hast.

Darunter gibt es einen Abschnitt "_Create reusable application policies_", in welchem du per "Add a policy" eine neue Policy anlegen kannst. Das wollen wir machen