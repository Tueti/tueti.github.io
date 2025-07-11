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

Um diesem Tutorial folgen zu können, wirst du bereits einen Cloudflare Tunnel eingerichtet haben (vielleicht auch durch meinen [ersten Beitrag dazu](/synology-nas-als-private-cloud-via-cloudflare-tunnel-ohne-portweiterleitung) und dich deshalb ein wenig im Cloudflare Portal auskennen. Deshalb lass uns direkt starten, indem du einmal das Zero Trust Dashboard öffnest. Das findest du im Navigationsbaum des [Cloudflare Dashboards](https://dash.cloudflare.com/) auf der linken Seite unter _Zero Trust_. Im sich dann öffnenden Dashboard findest du im neuen Navigationsbaum ein Modul _Access_.

Wenn du dies öffnest, siehst du verschiedene Einträge. Wir werden zwei davon nutzen:

* **Policies**
* **Applications**

Eine _Policy_ ist ein Satz Regeln für die Zugriffsverwaltung, welcher modular wiederverwendet werden kann. Wir können unter anderem sehr granular definieren, wer berechtigt wird, auf unser NAS zuzugreifen.

Eine _Application_ hingegen ist sozusagen eine Zwischenschicht, die sich zwischen den anfragenden Nutzer und unseren Endpunkt schiebt und die Policy durchsetzt. Wenn also jemand `photos.meinedomain.de` aufruft, kann ich eine _Application_ definieren, die sich bei dieser URL zwischenschiebt und die Policy forciert.

### Policy erstellen

Wähle also im Navigationsbaum erstmal _Policies_ und dann „Add a policy“. Du kannst der Policy einen aussagekräftigen Namen geben und dann bei den **Rules** auswählen, wie der anfragende Nutzer verifiziert werden soll. Schau dir die Liste gern genauer an, es gibt einige Möglichkeiten. Für unser Beispiel nutzen wir „Emails“ als _Selector_ und die Email Adressen, dessen Besitzer einen Verifizierungscode erhalten sollen. Im einfachsten Fall erstmal nur deine eigene Email Adresse. Du kannst aber auch mehrere angeben.

Der Rest kann erstmal so bleiben und du kannst die Policy speichern.

### Application erstellen

Nun brauchen wir noch die _Application_. Also im Navigationsbaum, unter _Access_ auf _Applications_ und eine neue Applikation anlegen. Unsere Applikation, unser NAS, ist _Self-Hosted_ also wählen wir auch das aus.

Nun müssen wir die Applikation ebenfalls konfigurieren. In den **Basic Information** können wir den Namen und die Sessiondauer angeben (wann muss sich jemand neu verifizieren).

Beim **Public hostname** musst du exakt die hostname eintragen, den du im Tunnel konfiguriert hast.

Und zu guter Letzt verknüpfst du nun noch die eben angelegte Policy im entsprechenden Abschnitt. Auf den folgenden Seiten kannst du noch konfigurieren, wie die zwischengeschaltete Seite aussehen soll, für unsere Bedürfnisse reicht aber erstmal der Standard. Klicke dich also durch die restlichen Seiten, belasse aber dabei die Standardwerte, bis du am Ende speichern kannst.

Wenn du nun die Application erstellt und richtig mit der Policy verknüpft hast und auch die korrekten Hostnames in der Application hinterlegt sind, dann sollte nun alles abgesichert sein!

Öffne einmal (am besten in einem privaten Tab oder einem anderen Browser) die URL, die du in deiner Application angegeben hast und du solltest eine Cloudflare Seite sehen, die dich auffordert, deine Email Adresse anzugeben. Nur die in der Policy hinterlegte Email Adresse bekommt dann auch einen Verifizierungscode, wenn du diese Email Adresse im Feld angibst und erst, wenn der Verifizierungscode eingegeben wurde, wirst du auf dein NAS weitergeleitet.

Und schon kommt nicht mehr Jedermann, der deine URL kennt, auf dein NAS. Glückwunsch zur extra Schicht Sicherheit und viel Spaß mit deiner _Private Cloud_.