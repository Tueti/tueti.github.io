---
author: ["Chrischi"]
title: "Hallo Host: Wie man Virtuelle Maschinen auf dem Ugreen NAS mit dem Host sprechen lässt"
slug: ""
date: "2026-07-10"
draft: true
description: ""
summary: ""
ShowToc: true
TocOpen: false
tags: ["VM", "Docker", "Ugreen", "UGOS"]
categories: ["Ugreen NAS", "Tutorials"]
cover:
  image: 
  caption: ""
sitemap:
  priority: 0.5
---

## Wie eine UGOS-Default-Konfig fast meine Proxmox-Migration ausgelöst hätte

Mein UGREEN DXP4800 Plus läuft seit Monaten brav. Docker-Stacks für Jellyfin, n8n, UniFi-Controller, das Übliche. Dann sollte Home Assistant dazukommen – als VM, nicht als Container, weil ich AddOn-Support wollte. HAOS-Image runtergeladen, qcow2 in den UGOS VM Manager importiert, statische IP konfiguriert, Boot.

Die Installation war relativ geradlinig (ein Tutorial hierzu wird vermutlich folgen) und das Webinterface war erreichbar. Allerdings nicht via `homeassistant.local`, wie es sein sollte, sondern lediglich über die direkte IP der VM. Merkwürdig, aber okay... Ich richtete alles weiter ein, bis ich das UniFi-AddOn installieren und konfigurieren wollte: Der UniFi-Controller, der in einem Docker-Container auf demselben NAS lebt, wurde einfach nicht gefunden!

Also begann das Debugging.

## Die Symptome

Zuerst dachte ich an ein internes Routing-Problem zwischen Docker und der VM auf dem NAS. Docker-Bridge- & Host-Netzwerke, die Netzwerkkonfiguration einer VM, irgendwo hier vermutete ich die Ursache. Aber jedes gute Debugging beginnt mit einer sauberen Analyse. Was ist der Ausgangszustand?

- VM erreichbar von jedem anderen Gerät im LAN
- Host-NAS erreicht VM _nicht_
- Container auf dem Host erreichen VM _nicht_
- VM erreicht die Docker-Container auf dem Host _nicht_
- mDNS-Auflösung von `homeassistant.local` funktioniert nicht

Aha! Das NAS selbst findet die Route nicht, das Problem liegt _vor_ den Docker Containern. Was mich anfangs nur stutzig machte, ist Teil des größeren Problems: `homeassistant.local` wird nicht aufgelöst, weil das NAS die Route zur VM nicht auflösen kann.

## Die Ursache

Das Problem scheint also zu sein, dass sowohl das NAS als auch die virtuelle Maschine am selben physischen Netzwerk-Interface hängen und sich somit nicht gegenseitig sehen können. Geräte, die im über andere Anschlüsse im selben LAN sind, können beide Geräte sehen, aber die VM und das NAS (der Host) können einander über diese Schnittstelle nicht erreichen.

![Blinder Fleck für Geräte am selben NIC](./macvtap_ohne_bridge_host_isolation.svg)

## Was macvtap damit zu tun hat

UGOS verbindet virtuelle Maschinen standardmäßig über **macvtap** mit dem Netzwerk. Dabei erhält die VM eine eigene virtuelle Netzwerkkarte samt eigener MAC-Adresse, die direkt an die physische Netzwerkkarte des NAS gekoppelt ist. Für andere Geräte im LAN wirkt die VM dadurch wie ein ganz normales, eigenständiges Gerät.

Der Aufbau ist vergleichsweise schlank: Statt ein virtuelles TAP-Interface mit einer Linux Bridge zu verbinden, hängt macvtap die VM direkt an das physische Interface. Der Traffic läuft weiterhin durch den Linux-Kernel – es handelt sich also nicht um echtes Hardware-Passthrough –, aber eine zusätzliche virtuelle Switch-Schicht entfällt.

Genau hier liegt allerdings auch der Haken: **Der Host kann eine macvtap-VM standardmäßig nicht über dasselbe physische Interface erreichen.**

Das NAS und die VM teilen sich zwar dieselbe Netzwerkkarte, hängen intern aber nicht an einem gemeinsamen virtuellen Switch. Pakete vom Host zur VM werden deshalb nicht einfach innerhalb des Systems zurückgeleitet. Sie müssten erst über das physische Netzwerk laufen – und selbst wenn der externe Switch sie zurücksendet, werden sie auf diesem Weg nicht wieder zur macvtap-VM zugestellt.

Andere Geräte im LAN sind davon nicht betroffen. Sie senden ihre Pakete über einen anderen Switch-Port an die Netzwerkkarte des NAS, wo macvtap sie der VM zuordnen kann. Deshalb war Home Assistant von meinem Mac oder Smartphone erreichbar, vom NAS selbst aber nicht.

Da Docker-Container ihre Verbindung ebenfalls über den Host beziehen, betrifft die Einschränkung auch sie. Aus Sicht meines UniFi-Containers war die Home-Assistant-VM damit genauso unerreichbar wie aus Sicht von UGOS selbst. Auch das Problem mit `homeassistant.local` passte in dieses Bild: Die für mDNS benötigte Kommunikation zwischen Host und VM wurde durch dieselbe Trennung verhindert.

macvtap war also nicht falsch konfiguriert. Es tat exakt das, wofür es gebaut wurde – nur passte dieses Verhalten überhaupt nicht zu meinem Anwendungsfall. Home Assistant sollte schließlich mit Diensten kommunizieren, die direkt auf demselben NAS liefen.

## Was eine echte Linux Bridge anders macht

Eine Linux Bridge arbeitet vereinfacht wie ein virtueller Switch innerhalb des NAS. Die physische Netzwerkkarte, der Host und die virtuellen Maschinen hängen dabei jeweils an einem eigenen Port dieser Bridge.

Die VM wird nicht mehr direkt über macvtap an die physische NIC gekoppelt. Stattdessen erhält sie ein virtuelles TAP-Interface, das als eigener Port an der Bridge hängt. Auch die physische Netzwerkkarte wird Teil dieser Bridge. Die IP-Adresse des NAS liegt anschließend üblicherweise nicht mehr direkt auf der physischen NIC, sondern auf dem Bridge-Interface.

Vereinfacht sieht der Aufbau dann so aus:

![Kommunikation mit virtuellem Switch](./vbr_lan1_linux_bridge_topologie.svg)

Damit befinden sich Host und VM tatsächlich im selben virtuellen Layer-2-Netzwerk. Die Bridge lernt, hinter welchem Port welche MAC-Adresse erreichbar ist, und leitet die Ethernet-Frames entsprechend weiter. Pakete zwischen NAS und VM müssen das Gerät dabei gar nicht erst verlassen. Sie werden direkt innerhalb des Linux-Kernels von einem Bridge-Port zum anderen transportiert.

Genau das fehlte bei macvtap.

Das NAS kann die Home-Assistant-VM nun direkt erreichen – und umgekehrt. Da die Docker-Container über den Netzwerk-Stack des Hosts beziehungsweise dessen Docker-Netzwerke angebunden sind, können auch sie mit der VM kommunizieren, sofern keine Firewall- oder Docker-Regel den Zugriff blockiert.

Auch Broadcasts und Multicasts können innerhalb der gemeinsamen Bridge weitergeleitet werden. Das ist unter anderem für Protokolle wie mDNS relevant, über die Namen wie `homeassistant.local` gefunden werden. Ob die Namensauflösung tatsächlich bis in einen bestimmten Docker-Container funktioniert, hängt zusätzlich von dessen Netzwerkmodus ab. Das grundlegende Hindernis zwischen Host und VM ist mit der Bridge aber beseitigt.

Der Unterschied ist damit weniger „macvtap schlecht, Linux Bridge gut“, sondern eine Frage des Einsatzzwecks: macvtap ist schlank und ausreichend, wenn eine VM nur mit dem externen Netzwerk kommunizieren muss. Eine Linux Bridge ist die passendere Wahl, sobald Host, VMs und lokale Dienste miteinander sprechen sollen.

## Die UGOS-Falle

UGOS bietet beide Konzepte an, aber versteckt sie hinter zwei verschiedenen Einstellungen, die auf den ersten Blick ähnlich klingen.

Im Control Panel unter Netzwerk gibt es zwei "Brücken"-Konzepte: "Normale Netzwerkbrücke" und "Virtuelle Netzwerkbrücke". Die "Normale Netzwerkbrücke" ist NIC-Sharing für am NAS-Port direkt angeschlossene Endgeräte – ein Mini-Switch-Modus, hat mit VMs nichts zu tun und ist beim VM-Problem irrelevant. Die "Virtuelle Netzwerkbrücke" ist das, was du brauchst: sie aktiviert die Linux-Bridge-Funktionalität auf NAS-Ebene und ermöglicht erst, dass VMs in einer echten Bridge laufen können.

Im VM Manager wählst du dann pro VM-Subnetz zwischen "Bridged Mode – macvtap" und "Bridged Mode – LinuxBridge". Der Default beim Anlegen einer VM: macvtap.

Warum der Default macvtap ist, kann ich nur vermuten. Vermutlich Performance-Optimierung für den typischen NAS-Use-Case: "Diese VM ist eine Box im LAN, niemand auf dem NAS muss mit ihr reden, also nehmen wir den effizienteren Pfad." Für die meisten NAS-Nutzer, die eine Windows-VM oder einen einzelnen Linux-Server als isolierte LAN-Maschine betreiben, ist das auch völlig ausreichend.

Für Homelab-Setups mit Docker-Containern auf dem NAS, die mit der VM kommunizieren sollen, oder mit Tools wie einem Twingate-Connector, der vom Host aus VM-Services erreichbar machen soll, oder mit Service-Discovery via mDNS – genau falsch.

## Die Lösung

Zwei Schritte, in dieser Reihenfolge:

1. NAS-Control Panel → Netzwerk → "Virtuelle Netzwerkbrücke" aktivieren. Das ändert das System-Networking: die physische NIC wandert in eine Linux Bridge, der Host bekommt seine IP auf der Bridge statt direkt auf der NIC. Kurzer Netzwerkaussetzer von wenigen Sekunden während der Umstellung. Mach das nicht, während jemand vom NAS streamt.

2. VM Manager → Netzwerk → VM-Subnetz auf "Bridged Mode – LinuxBridge" stellen. Die VM komplett herunterfahren und neu starten – Reboot reicht hier explizit nicht, weil der Network-Detach-Attach beim Mode-Wechsel sonst nicht greift.

Danach: Host erreicht VM. Container erreichen VM. mDNS funktioniert. Saga zu Ende.

## Lesson Learned

Die eigentliche Lektion ist nicht "macvtap ist böse" – es ist ein legitimes Kernel-Feature für seinen Anwendungsfall. Die Lektion ist:

**Bevor du strukturelle Änderungen anstößt – Hypervisor wechseln, Architektur umbauen, Migration planen – prüf die Default-Konfig deines aktuellen Stacks.**

Ich war ernsthaft dabei, Proxmox zu installieren, weil ich UGOS-Networking für strukturell kaputt hielt. Die Häufung von Symptomen – Host erreicht VM nicht, Container erreichen VM nicht, mDNS funktioniert nicht, jeder neue Service braucht einen Workaround – sah aus wie ein Muster, das den Wechsel rechtfertigt. Tatsächlich war es genau ein Dropdown im Control Panel, kombiniert mit einem Verhalten des Kernels, das in der UGOS-Doku nicht explizit gemacht wird.

Die Frage, die ich mir früher hätte stellen sollen: Welcher Bridge-Mode ist eigentlich aktiv? Statt direkt nach Workarounds und Migration zu denken.

Beim nächsten "Host erreicht VM nicht"-Symptom auf einer NAS-Plattform ist die erste Frage: macvtap oder LinuxBridge? Spart vermutlich ein paar Wochenenden Hypervisor-Migration.

---

*Setup: UGREEN DXP4800 Plus, UGOS Pro, HAOS in VM, Docker-Stacks via Container Manager. Der gleiche Mechanismus existiert auf anderen NAS-Plattformen (Synology, QNAP, Asustor) mit teils anderen Default-Modi – Diagnose-Frage bleibt aber identisch.*