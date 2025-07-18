---
author: ["Chrischi"]
title: "NAS als Private Cloud: Eigener Tunnel mit virtuellem privaten Server, Reverse Proxy & WireGuard - Teil 2"
slug: "nas als cloud eigener tunnel mit virtuellem privaten server reverse proxy und wireguard teil 2"
date: "2025-08-04"
draft: false
description: "Eine Private Cloud geht auch als DIY-Lösung mit eigenem Server. In Teil 2 folgt das VPN-Netz."
summary: "In Teil 1 haben wir unser Setup vorbereitet und die Container für den Reverse Proxy und WireGuard auf unserem VPS einsatzbereit konfiguriert. Im zweiten Teil der Reihe wollen wir das VPN-Netzwerk erstellen, um eine erste Kommunikation mit dem NAS aus dem Internet heraus zu ermöglichen."
ShowToc: true
TocOpen: false
tags: ["Synology", "WireGuard", "Zoraxy", "Virtual Private Server"]
categories: ["Tutorials", "Synology NAS"]
series: ["Private Cloud mit einem Synology NAS"]
cover:
  image: featured-image.webp
  caption: Der Tunnel zur Cloud - Erstellt von ChatGPT
---

Lasst uns direkt einsteigen, wo wir [letztes Mal](/nas-als-cloud-eigener-tunnel-mit-virtuellem-privaten-server-reverse-proxy-und-wireguard-teil-1/) aufgehört haben. Unser VPS steht, wir haben [Zoraxy](https://github.com/tobychui/zoraxy) und [WireGuard](https://github.com/wg-easy/wg-easy) via Docker installiert und die Admin Webseiten über Subdomains erreichbar gemacht.

## Weiter zum Ziel

Ziel dieses Teils der Reihe ist es, unser VPN-Netzwerk zu etablieren. Unser Server läuft und über das Interface von wg-easy können wir relativ leicht Client Konfigurationen erstellen. Wir werden also das NAS als Client im Interface anlegen, um eine solche Konfiguration zu bekommen. Wir müssen außerdem auch WireGuard auf unserem NAS installieren, damit es sich mit unserem Server verbinden kann. Dabei sind ein paar Dinge zu beachten, da wir ja nicht den gesamten Traffic des NAS über unseren Server leiten wollen. DSM-Updates und anderer regulärer Internetzugriff soll weiterhin über unseren normalen Internetzugang zu Hause laufen. Nur Zugriff auf andere VPN-Clients und den Server sollen auch über VPN abgewickelt werden. Zu guter Letzt werden wir noch ein weiteres Gerät, beispielsweise ein Smartphone als Client hinzufügen, um zu testen, ob wir aus dem Mobilfunknetz, mit verbundenem VPN, auf das NAS zugreifen können.

Wenn dieses Setup steht, dann könntet ihr einfach alle gewünschten Clients in wg-easy anlegen, den WireGuard Client auf den Geräten installieren und die Konfiguration dort hinterlegen und habt ein voll funktionsfähiges VPN-Netz, mit welchem ihr aus dem Internet heraus per VPN aufs heimische NAS zugreifen könnt.

Für viele wird das Ziel damit erreicht sein. Für Andere folgt noch ein dritter Teil, wenn ein paar interne Routen auch ohne VPN-Verbindung erreicht werden sollen (beispielweise eine Nextcloud Instanz oder so).

Da wir alle wieder auf demselben Stand sind, lasst uns loslegen!

## WG Easy vorbereiten

Du solltest via `{deineErstellteSubdomain}` nun ganz einfach auf das wg-easy Admin Interface zugreifen können. Hierzu haben wir die [Proxy Routen in Teil 1](/nas-als-cloud-eigener-tunnel-mit-virtuellem-privaten-server-reverse-proxy-und-wireguard-teil-1/#proxy-routen-anlegen) der Reihe angelegt.

Beim ersten Öffnen musst du einen Admin Account in dem Interface anlegen und dich danach mit ebendiesem Account anmelden. Danach erscheint eine super aufgeräumte Oberfläche und genau das macht den Charme dieser Lösung aus. Bevor du nun voreilig auf den Button klickst, um einen neuen Client hinzuzufügen, klicke einmal oben rechts auf _Administrator_ => _Admin-Konsole_ und dann links auf _Konfiguration_.

Hier kannst du ein paar ... nunja ... Konfigurationen setzen 🤓 Für Clients, die du via VPN ins Internet verbinden willst, siehst du beispielsweise den eingetragenen DNS Server. `1.1.1.1` ist der DNS von Cloudflare, Google wäre `8.8.8.8` und `8.8.4.4`. Du kannst dir einen aussuchen. Der [Chaos Computer Club bewirbt](https://www.ccc.de/censorship/dns-howto/) zum Beispiel den zensurfreien DNS von [digitalcourage](https://digitalcourage.de/support/zensurfreier-dns-server) unter der IPv4 `5.9.164.112` und der IPv6 `2a01:4f8:251:554::2`. Ihr dürft eintragen, was ihr wollt oder den Cloudflare DNS belassen, die Info war ein freier Service, falls ihr die Alternativen noch nicht kanntet. Unser NAS wird das Internet von eurem Heimnetz weiter verwenden und nicht den eingetragenen DNS nutzen (zumindest, wenn ihr meinem Tutorial folgt). Aber auch in diesem Kontext sind die DNS Adressen eventuell hilfreich.

Prüft einmal euren **Host** als ersten Eintrag. Dies sollte entweder eure Server IP oder besser noch, eure Domain sein, die auf eure Server IP zeigt ([hier in Teil 1 eingerichtet](/nas-als-cloud-eigener-tunnel-mit-virtuellem-privaten-server-reverse-proxy-und-wireguard-teil-1/#domain--dyndns-auf-server-ip-richten)).

Nun könnt ihr wieder auf die Hauptseite navigieren und einen neuen Client anlegen.

### Konfig für das NAS

Sobald ihr den entsprechenden Button drückt, öffnet sich ein kleines Fenster. Gebt einen sprechenden Namen ein ("NAS"?), lasst das Datum leer - bzw. mit dem Platzhalter versehen und sagt "Client erstellen". Fertig.

Also fast. Wir wären im einfachsten Fall fertig, wenn _alles an Traffic_ über unseren Server geroutet werden sollte. Aber wir möchten ein wenig einschränken, also klickt bitte auf den "Editieren" Button neben dem roten Schalter bei eurem Client. Dieser Button führt euch in die Konfiguration _pro Client_. Eben waren wir in der _globalen Konfiguration_, die _Client Konfiguration_ überschreibt die globale Konfiguration _für diesen Client_.

Zwei Werte möchte ich anpassen.

1. **Erlaubte IP-Adressen**: 10.8.0.0/24  
2. Unter **Erweitert** => **Dauerhaftes Keepalive**: 25  

Wieso diese Änderungen? Die erste Einschränkung sagt, nur Verbindungen, die eine IP im Bereich 10.8.0.X haben (die Standard Range von WireGuard, die auch wir nutzen) sollen über den VPN Server geroutet werden. Also unser NAS leitet nur Anfragen an solche IPs über unseren Server. Anfragen an andere IP (internets Netzwerk oder öffentliche IPs im Internet) gehen nicht über den VPN Server.

Der zweite Wert sorgt dafür, dass unser NAS dauerhaft verbunden bleibt, auch, wenn kein aktiver Traffic läuft. Alle 25 Sekunden hält er die Verbindung aufrecht. Das ist der empfohlene Wert von WireGuard. Macht unser Client das nicht, könnte die Verbindung irgendwann einschlafen und dann wäre das NAS gegebenenfalls nicht mehr erreichbar, wenn wir es erreichen wollen. Einfach, weil lange keine Verbindung mehr stand.

Dann ganz unten auf der Seite _speichern_, du landest wieder auf der Hauptseite und kannst die Konfig-Datei über den _Herunterladen_ Button als `wg0.conf` auf deinem Rechner speichern. Diese Datei brauchen wir gleich für unser NAS.

## Das NAS verbinden

Prüfe [hier](https://github.com/runfalk/synology-wireguard?tab=readme-ov-file#compatibility-list) nochmal, ob dein NAS kompatibel mit der WireGuard-Lösung ist. NAS mit einem zu alten Kernel werden WireGuard nicht zu Laufen kriegen. Leider wird WireGuard generell nicht nativ von Synology angeboten und es gibt auch (für DSM 7) kein Communitypaket. Es gibt aber die Möglichkeit, das Paket selber zu kompilieren oder einfach aus [diesem Google Drive](https://drive.google.com/drive/folders/1Ci-8oWZ_gW8tH3mv5wKL19nKE1pR8ZfH) zu laden. Allerdings muss man dann demjenigen vertrauen, der es kompiliert hat.

Bevor ihr das Paket für euch kompilieren könnt, müsst ihr eure NAS CPU Architektur herausfinden. Dafür stellt Synology selbst eine [nette Übersicht](https://kb.synology.com/en-global/DSM/tutorial/What_kind_of_CPU_does_my_NAS_have) zur Verfügung. Öffnet diese Seite einmal und prüft den "Package Arch" eures NAS. Außerdem benötigt ihr Docker und Git, dann solltet ihr dies auch auf eurem eigenen Rechner machen können.

Wenn ihr alles habt, geht's los.

### Paket kompilieren

https://github.com/runfalk/synology-wireguard?tab=readme-ov-file#compiling

### Paket installieren

https://github.com/runfalk/synology-wireguard?tab=readme-ov-file#installation

### Konfig hinzufügen und Interface starten

https://github.com/runfalk/synology-wireguard?tab=readme-ov-file#installation

## Unser Smartphone verbinden