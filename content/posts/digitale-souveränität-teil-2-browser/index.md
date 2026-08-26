---
author: ["Chrischi"]
title: "Digitale Souveränität - Teil 2: Weg von Chrome"
slug: "digitale-souveraenitaet-teil-2-weg-von-chrome"
date: "2026-08-26"
draft: false
description: "Warum Chrome keine gute Wahl mehr ist - und was Brave, Firefox, Mullvad und Tor besser machen. Vier Browser-Alternativen mit ihren jeweiligen Kompromissen."
summary: "Chrome hat rund 68% Marktanteil - und Google verdient sein Geld mit genau den Daten, vor denen ein Browser dich eigentlich schützen sollte. Google entscheidet sich immer offener dazu, diesen Schutz nicht zu gewährleisten. Deshalb schaue ich mir vier Alternativen an, die deine Privatsphäre im Internet stärker respektieren: Brave, Firefox, Mullvad und Tor."
ShowToc: true
TocOpen: false
tags: ["Big Tech", "Digitale Souveränität", "Google", "Chrome", "Brave", "Mullvad", "Tor", "Browser"]
categories: ["Digitale Souveränität"]
series: ["Digitale Souveränität"]
sitemap:
  priority: 0.6
cover:
  image: featured-image.webp
  caption: Foto von [appshunter.io](https://unsplash.com/de/@appshunter?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) auf [Unsplash](https://unsplash.com/de/fotos/ein-handy-das-auf-einem-holztisch-sitzt-iOWLBQewoDo?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)
---

## TL;DR

Nimm [Brave](https://brave.com/). Seit Juni 2026 gibt es [Brave Origin](https://brave.com/origin/) für $59.99, aber der gratis Brave Browser reicht völlig aus. Danach deaktivierst du alle Features, die du nicht brauchst (Wallet, VPN, Leo AI, Web 3.0 Feature) und hast einen Browser, der einiges dafür macht, deine Privatsphäre relativ gut zu schützen. Brave ist ein Browser mit einem klaren Geschäftsmodell, weshalb er mit einigen optionalen Features kommt, mit denen das Unternehmen Geld verdienen kann. Mit *Brave Origin* gibt es nun eine schlanke Alternativ-Version gegen Bezahlung. Wenn du keine Settings durchwühlen möchtest und das Geld übrig hast, erwirb die Version gern, aber das ist kein Pflichtkauf. Du erreichst ziemlich dasselbe mit der freien Version richtig konfiguriert. Außerdem gibt es Brave für Desktop und Mobilgeräte und du kannst eine private Synchronisation einrichten.

## Wieso der Browser wichtig ist

Mit unserem Browser navigieren wir durchs Internet. Wir recherchieren zu Fragen, die wir haben, wir buchen Urlaube, wir suchen auch mal nach Infos zu diesem Hautausschlag oder diesem dauerhaften Ziehen, welches wir immer spüren - und wir shoppen natürlich. Der Browser begleitet uns auf einer sehr privaten Reise durchs Internet.

Wie im [ersten Teil der Reihe](/digitale-souveraenitaet-teil-1-big-tech-treibt-es-zu-weit/) erwähnt, sind solche privaten Daten für einige Unternehmen viel Geld wert. Ein guter Browser kann uns jedoch zu gewissen Teilen dabei unterstützen, diese Daten nicht an ebendiese Unternehmen zu senden oder sie nur so zu senden, dass die Daten uns nicht zuzuordnen sind.

## Google Chrome und der Interessenkonflikt

Googles _Chrome_ Browser ist laut [StatCounter](https://gs.statcounter.com/browser-market-share) mit zuletzt ~68% weltweitem Marktanteil die unangefochtene Nummer 1 (Stand August 2026). Und Chrome ist ein schneller und sicherer Browser, den ich in seinen Anfangszeiten extrem gern genutzt habe. Doch mittlerweile wird Googles Interessenkonflikt immer deutlicher.

Google verdient sehr viel Geld mit Daten und das können sie nur, wenn sie diese Daten erfassen. Mittlerweile nutzen [99,3% aller werbetreibenden Internetseiten Google Ads](https://w3techs.com/technologies/overview/advertising), um die Werbung zielgruppenorientiert anzuzeigen (das sind 43% aller Webseiten). [83,2% aller Webseiten, die ihren eigenen Traffic analysieren, tun dies mit Google Analytics](https://w3techs.com/technologies/details/ta-googleanalytics) (das sind 47,7% aller Webseiten). Dazu kommt, dass [91,3% aller Internetsuchen über die Google Suchmaschine](https://www.statista.com/statistics/1381664/worldwide-all-devices-market-share-of-search-engines/) getätigt werden.

Google weiß also, welche Webseiten du besuchst, was du auf diesen Webseiten machst (was du anklickst, wo du hin navigierst, ggf. was du kaufst) und Google ist dafür verantwortlich, dir Suchergebnisse zu deinen ganz persönlichen Fragen zu liefern. Positiv könnte man es so formulieren, dass Google dir sehr spezifische Webseiten für deine Anfragen ausliefern könnte - aber Google ist nicht dein Freund, der dir helfen will. Google ist ein gewinnorientiertes Unternehmen und verkauft Anzeigeplätze bei Google Suchen. Das heißt, Google kennt dich gut genug, um einem zahlenden Werbetreibenden anzubieten, dessen Webseite genau _dir_ anzuzeigen, da du laut Googles Profil sehr wahrscheinlich drauf klicken wirst. Die obersten Plätze sind nicht das beste Ergebnis für dich, sondern verkaufte Plätze mit Anzeigen, die wie ein Ergebnis auf deine Suche wirken, um dich zum Draufklicken zu bringen. Wo die organischen Suchergebnisse beginnen, siehst du erst, wenn du genau hinschaust.

Schockierenderweise wollen nicht alle Internetnutzer eine solche fremdbestimmte Nutzung erfahren und so entstanden Browser und Plugins, die solche Analysetools unterbanden. Ohne zu tief ins Detail zu gehen, kam es zu einem Katz- und Mausspiel. Unternehmen wie Google und Meta (die mit dem *Meta Pixel* ein ähnliches Tool wie Google Analytics anbieten und mit Werbung/Daten Geld verdienen) fanden immer ausgefeiltere Wege, Nutzer zu tracken - unabhängig davon, ob diese einen Werbeblocker hatten: Browsergröße, Cookies, IP, Internetanbieter, welche Plugins der Browser installiert hatte, wie der Browser Grafiken anzeigt und noch ganz viel mehr. So viele Datenpunkte, dass die Masse der Daten eine Art eindeutigen Fingerabdruck lieferte.

Plugins wurden ebenfalls aktualisiert, um genau diese neuen Techniken zu unterbinden. Ich denke, so kam Google auf eine (für sie) tolle Idee: "Wenn fast 70% der Menschen unseren Browser verwendet, um sich durchs Internet zu bewegen, dann lasst uns einfach unterbinden, dass Tracking geblockt werden kann". Google kassierte viele versprochene Privatsphäre-Funktionen innerhalb von Chrome wieder ein ([keine Unterdrückung von Third-Party-Cookies](https://privacysandbox.google.com/blog/privacy-sandbox-next-steps?hl=de), [IP Protection und anderes](https://privacysandbox.google.com/blog/update-on-plans-for-privacy-sandbox-technologies?hl=de)) und überarbeitete das eigene Plugin-Manifest zum **Manifest V3**. Plugins wie [uBlock Origin](https://github.com/uBlockOrigin/uBlock-issues/wiki/About-Google-Chrome's-%22This-extension-may-soon-no-longer-be-supported%22) (der beste Werbe- und Tracking-Blocker) werden so nicht mehr unterstützt.

Chrome selbst muss gar keine genauen Daten sammeln, aber Chrome kann dafür sorgen, dass Nutzer des Browsers sich nicht mehr dagegen schützen können, dass die _Tools auf den Webseiten_ und _die Google Suche_ diese Daten sammeln.

Da wir als Internetnutzer keinen administrativen Zugriff auf die Webseiten haben, ist die Wahl des Browsers essenziell, um diesen Analysetools auf den Webseiten keine identifizierbaren Daten zu geben. Wem Privatsphäre nicht gänzlich egal ist, sollte Chrome nicht mehr verwenden. Doch welche Alternativen gibt es?

## Die Alternativen

Es gibt glücklicherweise Browser, die unsere Privatsphäre deutlich mehr respektieren. Vier Alternativen finde ich dabei besonders spannend und mit jeder dieser Alternativen fährst du besser als mit Chrome.

### Brave (Brave Origin)

[Brave](https://brave.com/) ist seit 2016 auf dem Markt und [Open Source](https://github.com/brave/brave-core). Der Browser basiert auf Chromium und fokussierte sich von Anfang an auf privates Surfen im Internet: Blockieren von Werbung (sogar bei YouTube), Blockieren von Trackern, automatisches Wegräumen von Cookie-Bannern und die Randomisierung des Fingerprints. Brave verhindert hier nicht das Auslesen, sondern verfälscht den Fingerabdruck jedes Mal ein wenig, sodass gar kein stabiler Wiedererkennungswert entsteht. Bei den [neusten Erweiterungen in diesem Bereich](https://cyberinsider.com/brave-browser-adds-new-defenses-against-gpu-fingerprinting/) nutzt Brave zusätzlich zur Verfälschung auch die Gleichstellung von Fingerprints. Beides ist ein valider Weg, die Gleichstellung wird von Tor und Mullvad ebenfalls genutzt.

Für die normale Nutzung brauchst du keinen Account - Lesezeichen, Verlauf und Einstellungen synchronisierst du über eine manuell eingerichtete Synchronisationskette, ganz ohne Cloud-Konto.

Obwohl Brave auf Chromium basiert (das von Google entwickelt und gesteuert wird), lässt Brave das Plugin-Manifest V2 am Leben und unterstützt so weiterhin Plugins wie uBlock Origin, AdGuard, NoScript und uMatrix. In den Settings gibt es hierfür sogar einen eigenen Plugin-Bereich unter `brave://settings/extensions/v2`. Brave gibt sein Bestes, diese Plugins bzw. das Manifest V2 weiterhin zu unterstützen.

Aber selbst ohne die Plugins sind die Privatsphäre-Mechanismen fest in den Browser integriert und die hauseigenen Blocker nutzen dieselben Listen wie uBlock Origin.

Hinter Brave steht jedoch keine gemeinnützige Stiftung. *Brave Software Inc.* muss Geld verdienen. Brave nutzt dazu ein eigenes, privates Werbesystem. Auf der *Neue Tab*-Seite kann Werbung ausgespielt werden (was man jedoch deaktivieren kann), Brave bietet Premium-Features wie einen eigenen VPN-Dienst, Premium-Suche und einen Premium-KI-Dienst. Außerdem bietet es eine Krypto-Wallet und erhält eine 1%-Gebühr pro Kauf von Krypto mit echtem Geld.

Alles optional und deaktivierbar. Das Unternehmen sagt selbst, es würde [70% der Werbeeinnahmen an die Nutzer zurückgeben](https://brave.com/faq/#how-brave-makes-money). Denn wer sich die Werbung anzeigen lässt, bekommt *[Basic Attention Token](https://brave.com/faq/#what-does-bat-stand-for) (BAT)*. Braves eigene Kryptowährung, welche man später beispielsweise gegen Gutscheine eintauschen kann. Wichtig dabei: Diese Anzeigen ersetzen nicht die Werbung, die Brave auf Webseiten blockt. Die bleibt einfach weg. Und das Ganze ist standardmäßig aus, man nimmt per Opt-in teil.

Der entscheidende Unterschied zu Google ist damit: Brave verdient daran, dass du den Browser benutzt und vielleicht irgendwann etwas kaufst - nicht daran, dass du wiedererkennbar bist.

Wer aber diese Funktionen nicht mal in den Einstellungen sehen will, kann seit Juni 2026 [Brave Origin](https://brave.com/origin/) für einmalig $59.99 erwerben, für Linux sogar kostenlos. Das ist die zu bezahlende Version von Brave, welche aber dafür all diese Features gar nicht erst enthält: Kein VPN, keine Leo AI, keine Krypto-Wallet, kein Rewards-System, nicht mal mehr der integrierte Tor Browser.

Ob es einem das wert ist, muss jeder selbst entscheiden. Mit dem Setzen einiger Einstellungen bekommst du ziemlich dasselbe in der Gratisversion hin - und hast sogar die Möglichkeit, Tor direkt innerhalb von Brave zu nutzen.

Insgesamt bietet Brave gute Privatsphäre-Funktionen gepaart mit einer angenehmen Nutzungserfahrung. Mobile Versionen sind verfügbar, sodass du zwischen Desktop und Smartphone/Tablet synchronisieren kannst.

### Firefox

[Firefox](https://www.firefox.com/de/) kam 2002 (bzw. in Version 1.0 2004) auf den Markt und ist ebenfalls [Open Source](https://github.com/mozilla-firefox/firefox). Firefox läuft auf der hauseigenen Engine *Gecko*, welche außerhalb von Googles technischem Einfluss ist. Das ist meiner Ansicht nach ein großer Pluspunkt, denn auch bei der Engine - dem Motor des Browsers - sollte es Konkurrenz geben. Änderungen an Chromium (wie dem Update auf das Manifest V3) betreffen Firefox nicht und Plugins werden unterstützt. Es gibt einen großen Marktplatz, auf welchem natürlich auch uBlock Origin vertreten ist.

Auch wenn die Rechte an Firefox bei der *[Mozilla Foundation](https://www.mozillafoundation.org/de/)* (einer gemeinnützigen Organisation) liegen, wird der Firefox-Browser von der [Mozilla Corporation](https://mozilla-insights.org/de/organisationsstruktur#mozilla-corporation) entwickelt und vertrieben. Das wiederum ist eine *For-Profit-Organisation* und muss somit auch Geld verdienen. Dafür bietet auch die Mozilla Corporation Premium-Dienste wie Mozilla VPN oder Mozilla Monitor Plus. Fairerweise muss man erwähnen, dass Google sehr viel Geld investiert, um bei Firefox die Standard-Suchmaschine zu sein. Über 80% von Mozillas Einnahmen stammen von Google. Eine Abhängigkeit ist somit vorhanden, auch wenn [Mozilla diese verteidigt](https://blog.mozilla.org/en/mozilla/internet-policy/defending-an-open-web/) und sagt, ohne diese Einnahmen hätten es unabhängige Mitbewerber schwerer, konkurrierende Produkte zu betreiben.

Trotzdem werden auch die Privatsphäre-Funktionen von Firefox weiterentwickelt. Seit Neustem besitzt auch Firefox einen in der Standardkonfiguration aktivierten Fingerprinting-Schutz - noch nicht auf Braves Niveau, aber Mozilla entwickelt das Feature hoffentlich weiter. Laut Mozilla rund [14% weniger eindeutig identifizierbare Nutzer, auf macOS etwa 49%](https://www.firefox.com/en-US/firefox/151.0/releasenotes/). Auch ein Cloud-Dienst ist verfügbar, sodass man per *Firefox Sync* seine Daten über verschiedene Geräte hinweg aktuell halten kann. Ob man einen Cloud-Dienst oder eine Synchronisationskette wie bei Brave bevorzugt, ist jedem selbst überlassen.

Firefox bietet mit der unabhängigen Browser-Engine ein angenehmes Gegengewicht zum von Google gelenkten Chromium. Trotzdem ist Brave in der Standardkonfiguration der aktuell privatere Browser. Bei einer Firefox-Nutzung würde ich jedoch auf jeden Fall uBlock Origin installieren, die Standardsuchmaschine ändern und den *Verbesserten Schutz vor Aktivitätenverfolgung* (Enhanced Tracking Protection) auf **Streng** stellen. Dann machst du aber auch mit Firefox nichts falsch.

### Mullvad Browser

Der [Mullvad Browser](https://mullvad.net/de/browser) kommt von *Mullvad VPN*, einem schwedischen Unternehmen, welches das Ziel verfolgt, die Bewegung durchs Internet wieder privater zu gestalten. Deren erstes Produkt - und das Produkt, auf dem ihr Geschäftsmodell beruht - ist ein VPN-Dienst. Aber wie sie selbst schreiben (und womit sie Recht haben), ein VPN allein sichert keine Privatsphäre. In Kombination mit einem datenschutzfreundlichen Browser geht das schon eher und so hat Mullvad in Kooperation mit Tor einen eigenen sicheren Browser entwickelt. Sie selbst beschreiben ihren Browser als *"Tor Browser ohne Tor-Netzwerk"*.

Der Mullvad Browser basiert auf Firefox, wurde dann jedoch extrem in Richtung Datenschutz optimiert. Wie deren VPN-Dienst ist auch der Browser [Open Source](https://github.com/mullvad/mullvad-browser). Er ist vermutlich einer der datenschutzfreundlichsten Browser in der Standardeinstellung. Die Extensions *NoScript* und *uBlock Origin* sind [fest in den Browser integriert](https://mullvad.net/de/help/tag/mullvad-browser) und können auch nicht entfernt werden. Auch Fingerprinting adressiert der Mullvad Browser, allerdings anders als Brave es tut. Während Brave den individuellen Fingerprint verfälscht, gibt es beim Mullvad Browser verschiedene "Schubladen", denen ein Client zugeordnet wird. So sehen die Mullvad-Nutzer für Analysetools identisch aus. Auch lokale Daten wie Cookies und der Browser-Verlauf werden beim Schließen gelöscht, sodass hier in der nächsten Session keine Reste für Fingerprinting existieren.

Mullvad selbst - so scheint es mir - gibt sich als Unternehmen für den Menschen. Für ihr Geschäftsmodell geben sie an, dass sie "mit einem nachhaltigen Zahlungsmodell [...] in hochwertige Entwicklung und Bandbreite investieren" können (Quelle: [Preisseite Mullvad](https://mullvad.net/de/pricing)). Der Browser ist hierbei jedoch gratis, die Einnahmen erzielt Mullvad mit ihrem VPN-Dienst. Mit diesem nachhaltigen Modell mussten sie seit 2009 noch nie die Preise anheben, bieten aber auch keine Gratisversion des VPN-Dienstes an. Die Zahlung kann nicht nur per Standardmethode oder Krypto erfolgen, sondern sogar in bar. Sie leben den Privatsphäre-Ansatz.

Der Mullvad Browser ist allerdings bewusst sehr schlank gehalten. Das reduziert die Angriffsfläche beim Fingerprinting. Extensions funktionieren grundsätzlich im Mullvad Browser, aber das Unternehmen rät davon ab, welche zu installieren. Denn genau dies könnte die beschriebene Angriffsfläche für Fingerprinting vergrößern und den Nutzer aus Mullvads "Schubladen" herausfallen lassen. Auch einen Synchronisationsdienst gibt es nicht. Mehr noch, den Mullvad Browser gibt es nicht als mobile App für das Smartphone. Ein sehr auf Privatsphäre fokussierter Browser für den Desktop.

### Tor

Als vierte Empfehlung habe ich den [Tor Browser](https://www.torproject.org/de/download/). Der Tor Browser kann auch für die alltägliche Internetnutzung verwendet werden, nicht nur für `.onion`-Webseiten. Dabei nutzt Tor ebenfalls seine starken Datenschutzfunktionen. Jeder Request wird über drei Stationen (Knoten) durch das *Onion-Netzwerk* geroutet. Der Ziel-Server sieht dabei nur den Exit-Knoten, nicht deinen Client. Jeder "Sprung" von Knoten zu Knoten ist in eine eigene Verschlüsselungsschicht gewickelt, die nur vom jeweils nächsten Ziel-Knoten entschlüsselt werden kann. Daher der Name "The Onion Router" (kurz: Tor): Die Anfrage wird zwiebelschichtig aufgelöst.

Tor nimmt den Schutz der Privatsphäre wirklich ernst. Ein Abschirmen der eigenen IP-Adresse ergibt sich schon aus dem Traffic-Routing über die drei Knoten. Der Third-Party-Cookie-Schutz, den Firefox, Brave und andere heutzutage implementieren, wurde von Tor als erster umgesetzt. So können Tracker dich nicht über mehrere Webseiten hinweg wiedererkennen. Chrome implementiert diesen Tracking-Schutz - wen überrascht es? - nicht. Beim Fingerprinting agiert Tor ähnlich zu Mullvad und versucht, alle Clients zu vereinheitlichen. Hier erkennt man die Kollaboration zwischen Tor und Mullvad bei der Entwicklung des Mullvad Browsers. Einen Werbeblocker bringt Tor bewusst nicht mit, da dies auch hier die Angriffsfläche für Fingerprinting erhöhen würde. Der Plugin-Ansatz ist noch minimalistischer als bei Mullvad.

Die Kehrseite bei diesem Browser ist die Geschwindigkeit - das, was der Mullvad Browser adressiert, indem dieser *ein Tor Browser ohne Tor-Netzwerk* ist. Die Sprünge durchs Tor-Netzwerk bringen den maximalen Datenschutz bei jeder Anfrage, gehen aber auf Kosten der Geschwindigkeit. Die Stationen zwischen deinem Tor Browser und dem Ziel-Server können unterschiedlich performant sein und so können Anfragen deutlich länger dauern.

Außerdem blocken einige Webseiten (manche Banken oder Webseiten mit CAPTCHAs) den Tor Browser. Als alltäglicher Browser ist er deshalb nur bedingt zu empfehlen. Als Browser für maximale Privatsphäre (wichtig, mit dem Tor Browser allein ist man nicht direkt anonym, er ist jedoch ein sehr guter Baustein) bei spezifischen Themen ist er jedoch die beste Wahl.

## Fazit

Die vier vorgeschlagenen Alternativen sind alle ein Schritt nach vorn, wenn wir auf unsere Privatsphäre achten wollen. Ich hoffe, ich konnte aber auch aufzeigen, dass jeder dieser Browser Kompromisse eingeht. Brave nutzt Chromium und man kann dem Geschäftsmodell kritisch gegenüberstehen; Firefox nimmt Geld von Google und braucht für ein anständiges Level an Datenschutz eine gewisse Konfiguration sowie das uBlock-Origin-Plugin, und der Fingerprinting-Schutz steht noch am Anfang; Mullvad ist eine Insellösung für den Desktop-PC gänzlich ohne Synchronisationsdienst; Tor geht die Extrameile in Sachen Datenschutz, nimmt dafür aber Geschwindigkeitseinbußen in Kauf.

Was für dich der richtige Weg ist, musst du entscheiden. Eine falsche Entscheidung triffst du mit keinem dieser Browser. In meinem TL;DR ganz oben empfehle ich schlicht den Brave Browser, den man so einstellen soll, dass die Funktionen, die einen nicht interessieren, ausgeschaltet sind. Das ist *meine* Lösung. Ich nutze den Brave Browser für mein alltägliches Browsen. Ich bin bisher auf keine Webseite gestoßen, die Brave aussperrt, und genieße das werbefreie und stark trackingreduzierte Browsen. Mit der Synchronisationskette habe ich alle gewünschten Infos auf all meinen Geräten - ganz ohne Accountzwang.

Dieses Setup ist für mich der nutzbarste Mittelweg und ich denke, die meisten könnten dieses nutzen, ohne nach gewisser Zeit frustriert zu sein. Deshalb ist dies *meine persönliche* Empfehlung.