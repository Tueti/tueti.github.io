---
author: ["Chrischi"]
title: "Eigener Zugriffstunnel mit virtuellem privaten Server & WireGuard (diesmal ohne Cloudflare) - Teil 1"
slug: "eigener uugriffstunnel mit virtuellem privaten server und wireguard diesmal ohne cloudflare-teil-1"
date: "2025-07-28"
draft: true
description: "Wenn wir das heimische Netzwerk aus dem Internet heraus erreichen, aber keine Portweiterleitung einrichten wollen, geht dies nicht nur über Cloudflare. Ein solcher Tunnel kann auch selbst eingerichtet werden: Mit virtuellem privaten Server (VPS) und einem VPN. Dabei nutzen wir heute WireGuard."
ShowToc: true
TocOpen: false
tags: ["Synology", "WireGuard", "Virtual Private Server"]
categories: ["Tutorials", "Synology NAS"]
series: ["Private Cloud mit einem Synology NAS"]
cover:
  image: featured-image.webp
  caption: Der Tunnel zur Cloud - Erstellt von ChatGPT
---

## Wieso nicht einfach Cloudflare?

In den beiden letzten Artikeln habe ich mich des Problems angenommen, das [heimische NAS via Cloudflare Tunnel aus dem Internet heraus erreichbar zu machen](/synology-nas-als-private-cloud-via-cloudflare-tunnel-ohne-portweiterleitung) und dann auch noch [abzusichern](/cloudflare-tunnel-mit-extra-authentifizierung). Das Setup funktioniert soweit, aber es hat zwei Haken:

* Den ersten Haken erwähnte ich bereits im entsprechenden Post: Streaming, beispielsweise vom heimischen Medienserver, darf über Cloudflare nicht durchgeführt werden.
* Und natürlich geht unser Traffic über Cloudflare. Jeder darf selbst entscheiden, ob und in wieweit man Cloudflare vertrauen möchte, aber ich verstehe jeden, der seinen Traffic gern vollständig in der eigenen Hand hat.

Wie im Beitrag zum Setup des Cloudflare Tunnels erwähnt, haben manche von uns einfach nicht die Möglichkeit, Portfreigaben einzurichten und müssen deshalb Kompromisse eingehen. Wenn wir keine Portfreigaben einrichten können oder wollen, dann brauchen wir eine weitere Komponente, mit der wir aus dem Internet heraus auf unsere Services im Heimnetzwerk zugreifen können.

Eine Möglichkeit, die uns sehr viel Kontrolle ermöglicht, wäre ein _eigener Zugriffstunnel mit Hilfe eines virtuellen privaten Servers und WireGuard_ - optional auch ganz ohne Cloudflare. Genau dieses Setup möchte ich hier starten.

## Ein Wort (bzw. Absatz) der Warnung

Ich sage so einfach, wir nutzen einen _privaten virtuellen Server_ (kurz VPS), da dieser viel Kontrolle in die eigene Hand legt. Aber Kontrolle ist Macht und _mit großer Macht kommt auch große Verantwortung (Onkel Ben)_. Ein virtueller privater Server - gerade, wenn dieser als Cloud Service irgendwo gehostet ist - ist ein vollständiger Server, der aus dem Internet heraus erreichbar ist. Ein Server, auf dem ihr vermutlich Admin-Rechte besitzt und der wahrscheinlich auch erstmal keine Firewall aktiviert hat. Kurzum, ein Server, den ihr selbst bestmöglich schützen müsst!

Aber lass dich davon nicht abschrecken! Meine Empfehlung für VPS Hosting Services ist [Hetzner](https://www.hetzner.com/de/cloud). Die sind wirklich günstig und bieten für den Anfang auch Tutorials zur [Ersteinrichtung eines Ubuntu Servers](https://community.hetzner.com/tutorials/howto-initial-setup-ubuntu/de) und der [grundlegenden Sicherheit](https://community.hetzner.com/tutorials/howto-initial-setup-ubuntu/de) bei frischen Servern. Damit komme ich aber auch zu den Voraussetzungen.

## Voraussetzungen

Um diesem Tutorial folgen zu können, brauchst du...

* ... einen Server, den du aus dem Internet heraus erreichen kannst und auf dem Docker laufen kann
* ... optional eine Domain oder einen DynDNS, wie beispielsweise von [IPv64](https://ipv64.net/) oder [DuckDNS](https://www.duckdns.org/)

Ein solcher Server hat natürlich monatliche Kosten. Hetzner rechnet tatsächlich stundenweise ab. Du kannst also auch dem Tutorial folgen, alles aufsetzen, entscheiden, dass alles doof war und den Server abreißen. Dann zahlst du nur die Stunden, die der Server tatsächlich existierte. Sollte dies aber eine Dauerlösung werden, musst du monatlich für den Server zahlen. Auch deine Domain kann etwas kosten, sofern du eine eigene erworben hast.

Mein letzter, wichtiger Hinweis: Ich werde in diesem Tutorial nicht beschreiben, wie du deinen Server bekommst oder eine sinnvolle Ersteinrichtung zur Absicherung aussieht. Dies ist ein Tutorial mit Fokus auf das Setup eines eigenen Zugriffstunnels vom Internet auf heimische Services und diesen Fokus will ich auch halten.

Aber nun, hast du alles? Dann kann's losgehen 😎

## Nun geht's los!

