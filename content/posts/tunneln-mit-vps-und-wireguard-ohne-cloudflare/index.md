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

* Den ersten Haken erwähnte ich bereits im entsprechenden Post: Der Status des Streaming ist in der Community ungewiss
* Und natürlich geht unser Traffic über Cloudflare. Jeder darf selbst entscheiden, ob und in wie weit man Cloudflare vertrauen möchte, aber ich verstehe jeden, der seinen Traffic gern vollständig in der eigenen Hand hat.

Wie im Beitrag zum Setup des Cloudflare Tunnels erwähnt, haben manche von uns einfach nicht die Möglichkeit, Portfreigaben einzurichten und müssen deshalb Kompromisse eingehen. Wenn wir keine Portfreigaben einrichten können oder wollen, dann brauchen wir eine weitere Komponente, mit der wir aus dem Internet heraus auf unsere Services im Heimnetzwerk zugreifen können.

Eine Möglichkeit, die uns sehr viel Kontrolle ermöglicht, wäre ein _eigener Zugriffstunnel mit Hilfe eines virtuellen privaten Servers und WireGuard_ - optional auch ganz ohne Cloudflare. Genau dieses Setup möchte ich hier starten.

## Ein Wort (bzw. Absatz) der Warnung

Ich sage so einfach, wir nutzen einen _privaten virtuellen Server_ (kurz VPS), da dieser viel Kontrolle in die eigene Hand legt. Aber Kontrolle ist Macht und _mit großer Macht kommt auch große Verantwortung (Onkel Ben)_. Ein virtueller privater Server - gerade, wenn dieser als Cloud Service irgendwo gehostet ist - ist ein vollständiger Server, der aus dem Internet heraus erreichbar ist. Ein Server, auf dem ihr vermutlich Admin-Rechte besitzt und der wahrscheinlich auch erstmal keine Firewall aktiviert hat. Kurzum, ein Server, den ihr selbst bestmöglich schützen müsst!

Aber lass dich davon nicht abschrecken! Meine Empfehlung für VPS Hosting Services ist [Hetzner](https://www.hetzner.com/de/cloud). Die sind wirklich günstig und bieten für den Anfang auch Tutorials zur [Ersteinrichtung eines Ubuntu Servers](https://community.hetzner.com/tutorials/howto-initial-setup-ubuntu/de) und der [grundlegenden Sicherheit](https://community.hetzner.com/tutorials/howto-initial-setup-ubuntu/de) bei frischen Servern. Damit komme ich aber auch zu den Voraussetzungen.

## Voraussetzungen

Um diesem Tutorial folgen zu können, brauchst du...

* ... einen Server, den du aus dem Internet heraus erreichen kannst
* ... eine lauffähige Docker-Installation auf dem Server
* ... optional eine Domain oder einen DynDNS, wie beispielsweise von [IPv64](https://ipv64.net/) oder [DuckDNS](https://www.duckdns.org/)

Ein solcher Server hat natürlich monatliche Kosten. Hetzner rechnet tatsächlich stundenweise ab. Du kannst also auch dem Tutorial folgen, alles aufsetzen, entscheiden, dass alles doof war und den Server abreißen. Dann zahlst du nur die Stunden, die der Server tatsächlich existierte. Sollte dies aber eine Dauerlösung werden, musst du monatlich für den Server zahlen. Auch deine Domain kann etwas kosten, sofern du eine eigene erworben hast.

Docker sollte installiert sein, da eine Anleitung für die Installation den Rahmen dieses Tutorials sprengen würde. Die offiziell empfohlene Routine, an dich auch ich mich gehalten habe, könnte sich geändert haben und dein Server könnte ein ganz anderes Betriebssystem haben, als meins. Deshalb, bitte, installiere Docker selbst auf dem Server. Die offizielle Anleitung für alle Plattformen gibt es [hier bei Docker selbst](https://docs.docker.com/engine/install/).

Mein letzter, wichtiger Hinweis: Ich werde in diesem Tutorial nicht beschreiben, wie du deinen Server bekommst oder eine sinnvolle Ersteinrichtung zur Absicherung aussieht. Dies ist ein Tutorial mit Fokus auf das Setup eines eigenen Zugriffstunnels vom Internet auf heimische Services und diesen Fokus will ich auch halten.

## Unser Ziel-Setup

Worauf arbeiten wir hier eigentlich hin? Es gibt ein zweistufiges Ziel. Der erste Schritt ist, auf unserem VPS einen VPN-Server (wir nutzen WireGuard) zu installieren. Unser NAS, sowie unsere gewünschten Clients berechtigen wir für dieses VPN. Damit sollten wir unseren Clients (Smartphone, Tabelet, Notebook) ermöglichen, von überall aus auf unser NAS zugreifen zu können - allerdings nur mit aktiver VPN-Verbindung. Wer damit zufrieden ist, ist dann auch fertig.

Im zweiten Teil möchte ich dann aber noch bestimmte Services aus unserem VPN-Netz nach außen öffnen. Wenn ihr eurer Familie Zugriff auf ein Familien-Fotoalbum oder einen Medienserver geben wollt, dann ist es schwierig, diesen zu erklären, dass sich sich die WireGuard App installieren und eure Client Config hinzufügen sollen. Das schreit förmlich nach dem nächsten Support-Anruf. Einfacher ist es, diese Services über einen Reverse Proxy (dazu kommen wir später) ins Internet zu öffnen. Und dies ist das Ziel des zweiten Teils.

Wenn wir dazu kommen, muss ich natürlich den Disclaimer bringen, dass diese Services natürlich angreifbar werden und geschützt werden sollten. Aber dazu später mehr.

Dieser Beitrag ist Teil 1, Ziel ist also erstmal ein VPN-Netz. Ich werde trotzdem schon jetzt zu Beginn einen Reverse Proxy installieren. Wenn du den zweiten Schritt nicht planst und auch weißt, dass du einen Reverse Proxy nicht brauchen wirst, kannst du diesen Schritt überspringen. Ich sage dir trotzdem, wie du weiter kommst. Aber ein Reverse Proxy kann trotzdem als sinnvoll angesehen werden, selbst, wenn wir nur die UI für unser WireGuard Admin Interface ohne Portangabe aufrufen wollen. Ich empfehle also, diesen trotzdem zu installieren.

Da du nun die Voraussetzungen kennst (und hoffentlich erfüllst) und auch das Ziel vor Augen hast, kann's losgehen 😎

## Los geht's

