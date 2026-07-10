---
author: ["Chrischi"]
title: "Home Assistant als VM auf Ugreen NAS installieren"
slug: ""
date: "2026-05-12"
draft: true
description: ""
summary: ""
ShowToc: true
TocOpen: false
tags: ["Home Assistant", "Smart Home", "Ugreen"]
series: ["Private Cloud mit einem Ugreen NAS"]
categories: ["Ugreen NAS", "Home Assistant"]
cover:
  image: featured-image.webp
  caption: "Erstellt von Nanobanana/Gemini"
sitemap:
  priority: 0.5
---

1. .qcow2 Image herunterladen: https://www.home-assistant.io/installation/alternative/
2. Die App "Virtuelle Maschine" aus dem "App Center" installieren (Image "app-center.webp")
 => Image kann per "Zhe Unarchiver" oder `xz -d haos_ova-{versionsnummer}.qcow2.xz` entpackt werden
3. "+ Neue VM" in "Virtuelle Maschine" => "Virtuelle Maschine importieren" => "Von Datenträger importieren" (Dort steht als unterstütztes Format `qcow2`) => Wizard durchklicken, bis die eben heruntergeladene Datei selektiert ist => Namen (wie `home-assistant` vergeben) => Bestätigen ==> Image wird importiert
4. Danach sah die UI nicht weiterführend aus, aber das Import-Fenster kann einfach geschlossen werden, im Drop-Down gibt es nun das zuvor importierte Image, wähle dies aus. Wenn du einen SSD-Pool hast, nutze diesen. Ansonsten HDD-Pool. Danach wähle als "Systemtyp" "Linux" und weise die CPU-Kerne und den RAM hinzu. Ich habe 2 Kerne und erstmal 4GB RAM gewählt. Eins ISO-Image müssen wir nicht mounten, Festplatte und Netzwerk belassen wir auf dem Standard (virtio für beides und Netzwerk als "vnet-bridge0"). Klicke auf den "Erweitert" Tab und wähle bei "Systemstartmethode" `uefi`. Prüfe nochmal das Tastaturlayot, dies sollte "de" sein, wenn du die deutsche Tastaturbelegung möchtest. Ich habe auch den automatischen Start aktiviert, da ich möchte, dass Home Assistant nach NAS-Reboots automatisch startet.

Danach wird die VM fertig importiert und kann gestartet werden. Nach dem Start kannst du dich auch "verbinden" (beides Klicks auf den Linux-Pinguin links).

5. Nach erfolgreichem Boot, sollte - sofern du dich auf die VM verbunden hast - die CLI zu sehen sein. Außerdem siehst du auch die IP deiner VM im Netzwerk und die URL, wie du Home Assistant im Browser starten kannst (home-assistant-vm-start.webp).

6. Gib die Adresse im Browser ein und die Home Assistant Webseite sollte sich öffnen (home-assistant-start.webp) => Ich hatte das Problem, dass sich die Seite erst nicht öffnete, aber das war ein IP-Konflikt. Aus irgendeinem Grund wurde eine bereits vergebene IP in meinem Netzwerk an die VM vergeben. Ich habe der VM in meinem DHCP-Server eine statische IP zugewiesen und nach einem Neustart der VM lies sich auch Home Assistant öffnen  


ha network update enp0s3 --ipv4-method static \
  --ipv4-address 192.168.2.114/24 \
  --ipv4-gateway 192.168.2.1 \
  --ipv4-nameserver 192.168.2.1