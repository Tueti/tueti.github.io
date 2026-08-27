---
author: ["Chrischi"]
title: "Digitale Souveränität - Teil 4: Was bringt ein VPN"
slug: "digitale-souveraenitaet-teil-4-was-bringt-ein-vpn"
date: "2026-09-01"
draft: true
description: "VPN-Anbieter wie NordVPN, Surfshark und weitere bewerben ihre Dienste als Sorglospaket für deinen digitalen Datenschutz. Aber ist das so? Was bringt ein VPN und was sind vertrauenswürdige Anbieter?"
summary: ""
ShowToc: true
TocOpen: false
tags: ["Big Tech", "Digitale Souveränität", "NordVPN", "Proton", "VPN", "Mullvad"]
categories: ["Digitale Souveränität"]
series: ["Digitale Souveränität"]
sitemap:
  priority: 0.6
cover:
  image: featured-image.webp
  caption: Foto von [Petter Lagson](https://unsplash.com/de/@lagopett?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) auf [Unsplash](https://unsplash.com/de/fotos/person-die-einen-schwarzen-laptop-benutzt-NEtFkKuo7VY?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)
---

## Der Hype der VPN-Dienste

VPN-Anbieter tauchen immer häufiger als Sponsoren von Tech-Influencern auf: NordVPN, Surfshark, ExpressVPN und weitere. Dabei wird durch die Blume jedoch oft zu viel versprochen und die Einstiegspreise, die via Affiliate-Links angeboten werden, sind oft um 70-85% reduziert. Für mich wirken solche Lock-Versuche immer etwas *shady*. Als würden diese Anbieter die berechtigte Sorge der Leute ausnutzen, um ihnen das Abo anzudrehen. Ohne dass das gesamte Bild gezeichnet wird. Ein VPN allein ist kein Allheilmittel für den digitalen Datenschutz. Diese aggressive Werbung erweist der Branche eher einen Bärendienst.

> Große Tech-Unternehmen, Behörden und Datenbroker verwenden Cookies, Skripte, Browser-Fingerprinting und andere Methoden von Dritten, um Sie online zu identifizieren und zu verfolgen. Deshalb reicht ein VPN für den Datenschutz nicht aus und Sie benötigen zusätzlich einen datenschutzorientierten Browser.
>
> *<cite>Quelle: [Mullvad](https://mullvad.net/de/browser)</cite>*

Diese Meinung vertrete ich ebenfalls und deshalb wollte ich das Thema *VPN* erst angehen, nachdem ich die [Wahl eines datenschutzorientierten Browsers](/digitale-souveraenitaet-teil-2-weg-von-chrome/) und einer entsprechenden [Suchmaschine](/digitale-souveraenitaet-teil-3-private-suchmaschinen/) beschrieben habe.

## Was ist ein VPN und welchen Mehrwert bietet es?

VPN steht für *Virtual Private Network* und ist eine Technologie, um verschiedene Geräte ortsunabhängig zu einem Netzwerk zu verbinden. Du kennst es sicher, wenn du beruflich am Computer arbeitest und dich von zuhause verbinden kannst. Du verbindest deinen eigenen Computer *virtuell* mit dem Netzwerk des Unternehmens, für das du tätig bist. Dabei ist die Verbindung ins Unternehmen verschlüsselt. Die Kommunikation ist also sicher und dein Computer verhält sich, als wäre er physisch im Unternehmensnetzwerk. Der Sinn eines VPN ist es, einen sicheren *Einstiegspunkt* in das Zielnetzwerk zu bieten.

Diese Technologie machen sich die VPN-Dienste zunutze, um ein anderes Problem zu lösen. Dein Internetanbieter (ISP) kann auch mit einem [datenschutzorientierten Browser](/digitale-souveraenitaet-teil-2-weg-von-chrome/) sehen, welche Domains du besuchst. Das heißt nicht, dass dein ISP sieht, welches Video du bei YouTube schaust, aber er kann sehen, dass du auf YouTube warst. Gleiches gilt für die Webseite deiner Arztpraxis: Was du dort liest, sieht dein Anbieter nicht - dass du dort warst, schon. Webseitenbetreiber auf der anderen Seite sehen deine öffentliche IP-Adresse. Laut [Urteil des Europäischen Gerichtshofs](https://www.bfdi.bund.de/SharedDocs/Pressemitteilungen/DE/2016/15_BfDIBegruesstUrteilEuGHIPAdressen.html) stellen - auch dynamische - IP-Adressen ein personenbezogenes Datum dar. Webseitenbetreiber sehen also personenbezogene Daten, selbst wenn du nur die Webseite besuchst.

VPN-Anbieter drehen den Nutzen der Technologie um. Sie bieten nicht den Mehrwert eines sicheren *Einstiegspunktes*, sondern *verlagern deinen Austrittspunkt*. Verbindest du dich via VPN mit einem Server deines Anbieters, ist die Kommunikation zwischen deinem Gerät und dem VPN-Server *verschlüsselt*. Dein Internetanbieter sieht also nicht mehr, welche Domains du besuchst. Du veränderst den *Austrittspunkt*, über den du die Webseite besuchst. Damit bekommt der Webseitenbetreiber die IP-Adresse des VPN-Servers und nicht deine persönliche.

Wichtig ist, diese Verschleierung allein ist **kein Trackingschutz**. Aber im Zusammenspiel mit einem datenschutzorientierten Browser erschwerst du das Auslesen persönlicher Metadaten erheblich, sodass die Gefahr einer Identifizierung und die Möglichkeit des Trackings reduziert werden.

Das ist es, was ein VPN-Dienst ehrlicherweise versprechen darf.

Es ist aber wichtig, ein kritisches Detail nicht zu übersehen: Anstelle deines Internetanbieters kann nun dein VPN-Anbieter deine Aktivitäten nachvollziehen. Was weiß der VPN-Anbieter deiner Wahl also über dich?

## Versprechen vs. Struktur

Wie anonym bist du, wenn du einen VPN-Dienst nutzt? Das ist die Frage, auf die es hinausläuft, und ihre Beantwortung zeigt am Ende, welchen VPN-Anbietern du deinen Datenverkehr anvertrauen solltest.

Services werben fast immer mit einer *No-Log-Policy* oder *Anonymität* als Schlagwort. Das ist ihr *Versprechen* an uns. Dass dieses Versprechen jedoch nicht immer eingehalten wurde, ist belegt: Siehe [PureVPN](https://www.bleepingcomputer.com/news/security/cyberstalking-suspect-arrested-after-vpn-providers-shared-logs-with-the-fbi/) oder [IPVanish](https://torrentfreak.com/ipvanish-no-logging-vpn-led-homeland-security-to-comcast-user-180505/). Dazu kommt, dass Facebook zwischen 2016 und 2019 einen VPN-Dienst betrieb, um [seine Nutzer auszuspionieren und noch mehr Daten zu sammeln](https://techcrunch.com/2019/01/29/facebook-project-atlas/). Angeworben wurden sie ab 13 Jahren, für bis zu 20 Dollar im Monat.

Das sind extreme Fälle und mittlerweile auch einige Jahre her, aber die Gefahr ist weiterhin real. Es gibt keine Meldepflicht für VPN-Anbieter, die sie zwingen würde, Log-Aushändigungen zu veröffentlichen. Im Gegenteil, es gibt sogar *Gag Orders*, die ausdrücklich untersagen können, über gewisse behördliche Ermittlungen zu berichten.

*Versprechen* sind nichts Greifbares. *"Trust me bro"* sollte dich nicht dazu verleiten, deine intimen Daten in die Hände von Unternehmen zu legen.

Aber wie kannst du dann VPN-Anbietern vertrauen? Nun, es geht nicht um *Vertrauen* - es gibt VPN-Dienste, die strukturell deine Identität schützen und dich das auch *überprüfen* lassen.

Diese Services sind so aufgebaut, dass sie selbst dich gar nicht kennen: Du kannst anonym in bar bezahlen, du brauchst keine E-Mail-Adresse für die Anmeldung und zusätzlich belegen sie auch noch per Sicherheitsaudit oder Open-Source-Code, dass sie tatsächlich nichts loggen - wobei das dann nur noch das Sahnehäubchen ist. Denn wer nichts über dich weiß, kann dir auch keine Logs zuordnen.

## Die Wahl des Dienstes

Glücklicherweise ist eine solche Struktur etwas, was du im Vorfeld *prüfen* kannst.

Wie erkennst du nun einen solchen Dienst? Für mich sind es vier Fragen:

1. **Welche Daten verlangt der Anbieter bei der Registrierung und wie kannst du bezahlen?**
2. **Hat der Service ein klares und nachvollziehbares Geschäftsmodell?**
3. **In welchem Land operiert das Unternehmen und wie ist dort die Rechtslage für VPN-Anbieter?**
4. **Will der Service dich mit exzessiver Werbung an Bord holen?**








## OPTIONAL: VPN-Services mit erweiterten Features

- Dark Web Monitoring (https://nordvpn.com/de/features/dark-web-monitor/)
- Anti-Phishing, Anti-Tracking (https://nordvpn.com/de/next-gen-antivirus/)
- Anruf-Schutz (https://nordvpn.com/de/features/call-protection/)