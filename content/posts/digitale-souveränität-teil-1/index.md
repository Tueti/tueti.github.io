---
author: ["Chrischi"]
title: "Digitale Souveränität - Teil 1: Big Tech treibt es zu weit"
slug: "digitale-souveraenitaet-teil-1-big-tech-treibt-es-zu-weit"
date: "2026-08-21"
draft: false
description: "Aus personalisierter Werbung ist längst mehr geworden. Wieso Big Tech es zu weit treibt und digitale Souveränität immer wichtiger wird - mein Essay dazu."
summary: "Ich baue seit Jahren an meiner privaten Cloud. In dieser Serie gehe ich einen Schritt zurück und beleuchte, wieso ich das eigentlich mache. Denn Daten sind Kontrolle - und Kontrolle ist Macht. Aus personalisierter Werbung ist längst mehr geworden: gezielte Manipulation, Profile über Menschen, die nie etwas gepostet haben, und Regierungen, die auch auf europäische Rechenzentren zugreifen dürfen."
ShowToc: true
TocOpen: false
tags: ["Big Tech", "Digitale Souveränität", "Google", "Facebook", "Datenschutz", "Privatsphäre", "CLOUD Act"]
categories: ["Digitale Souveränität"]
series: ["Digitale Souveränität"]
sitemap:
  priority: 0.6
cover:
  image: featured-image.webp
  caption: Foto von [Ales Nesetril](https://unsplash.com/de/@alesnesetril?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) auf [Unsplash](https://unsplash.com/de/fotos/grauer-und-schwarzer-laptop-computer-auf-der-oberflache-Im7lZjxeLhg?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)
---

*Digitale Souveränität* und *Big Tech* - das mag erstmal nach "Nerd" klingen, aber das Thema geht uns wirklich alle etwas an. Ja, ich bin ein Nerd, das ist sicher keine Überraschung, aber was in welchem Ausmaß mit unseren Daten passiert, ist kein reines IT-Thema. Also schließe bitte den Tab nicht direkt, ich komme gleich zum Punkt.

Wer meinen Blog verfolgt, weiß, dass ich viel Zeit investiere, um meine private Cloud aufzubauen (erst mit [Synology](/series/private-cloud-mit-einem-synology-nas/), mittlerweile mit meinem [Ugreen NAS](/series/private-cloud-mit-einem-ugreen-nas/)). In dieser Serie möchte ich einen Schritt zurückgehen und einmal beleuchten, wieso ich das eigentlich mache. Denn ich bin fest davon überzeugt, dass Digitale Souveränität immer wichtiger wird, da Big Tech es zu weit treibt. Also wird die Serie meine Art *Essay* zu diesem Thema. Hol dir am besten ein Heißgetränk deiner Wahl (☕️) und viel Spaß beim Lesen.

## Die Verschmelzung von analoger und digitaler Welt

Die digitale Welt ist mittlerweile vollständig mit der analogen Welt verschmolzen. Es ist kein *extra Raum* mehr, den man betreten kann, um dort ein wenig Zeit zu verbringen. Das Digitale *ist* unsere Welt und wir wollen (ich würde sogar sagen, wir *müssen*) teilnehmen. Kommunikation via WhatsApp, die schnelle Bezahlung via PayPal, Google und/oder Apple für das Speichern von Fotos und Navigation, YouTube für Videos, Instagram für Fotos und so weiter.

Doch um das ganze Bild aufzuzeigen, muss ich ein paar Jahrzehnte in die Vergangenheit schauen. Zu Zeiten, als ich in meinem Jugendzimmer Eminem noch auf dem CD-Spieler hörte und auf meinem Röhrenfernseher Formel 1 schaute: Schumacher gegen Häkkinen, diese Ära (oh je, ich glaube, ich verliere alle möglichen jüngeren Leser...). Aber zurück zum Punkt: Formel 1 auf RTL und natürlich gab es Werbung: Rasierer, Bier, Grillzeug, solche Dinge. Doch wieso keine Schminke, Lotion oder Produkte dieser Art? Ich glaube, die Antwort kennen wir alle: Werbung war schon immer zielgruppengerichtet und Formel 1 hatte eine primär männliche Zuschauerschaft, weshalb die Werbung auf diese Zielgruppe ausgerichtet war. Das war recht rudimentär: Die Zuschauerschaft hatte eine riesige Altersspanne, war sicherlich politisch super breit aufgestellt, hatte unterschiedlichste Hobbys und nicht jeder trank Bier oder grillte. Aber bei einer großen männlichen Zuschauerschaft gab es sicher eine erhebliche Teilmenge, die dies doch tat und diese erreichte man ebenfalls.

Mit dem Ausbau der digitalen Services und der Entstehung des interaktiven Internets (oder [Web 2.0](https://de.wikipedia.org/wiki/Web_2.0)) kam es aber zu einer Veränderung der Web-Nutzung. Wir besuchten nicht mehr nur Internetseiten, wir teilten Dinge im Internet. Soziale Netzwerke und Internet-Communities entstanden. Und nicht nur das, wir erstellten uns auch eine _Identität_ im Internet. Meldet man sich irgendwo an, benötigt man fast immer eine E-Mail-Adresse und da wir alle es einfach haben wollen, haben die meisten Menschen _eine Adresse_, mit der sie sich überall anmelden. Doch so kamen die Unternehmen, die große Nutzerschaften hatten (Facebook, Google, etc.), auf die Idee, dass die Nutzer in viel granularere Zielgruppen unterteilt werden könnten, als noch zu Zeiten meines Formel-1-Beispiels. Facebook wusste, was Max Mustermann gern las, was er selbst schrieb, welche Fotos er teilte und in welchen Gruppen er war. Da ist es doch nur lukrativ, wenn Facebook einem Werbetreibenden wie einer Biermarke anbieten könnte, Werbung nur an Leute auszuspielen, die tatsächlich gern mal ein Bier tranken.

## Die Büchse der Pandora

Je lukrativer dieses System wurde, umso mehr wollte Facebook natürlich über die Menschen wissen, um das gezielte Einsortieren in Zielgruppen zu ermöglichen. Somit wurden die Mechanismen zum Sammeln der Daten immer ausgefeilter. Es wurde nicht mehr nur analysiert, was gelesen oder selbst geteilt wurde, sondern auch, wie lange man sich Posts anschaute, wie schnell man durch die Timeline scrollte und wo man stoppte. Facebook war eine Zeit lang **das soziale Netzwerk** und die Leute nutzten es gern. Also bot Facebook Einbindungen für andere Webseiten an und es entstand der Like- und Teilen-Button. Dann sollten andere Webseitenbetreiber auch die Möglichkeit bekommen, zu analysieren, was bei ihren Diensten gut funktioniert. Facebook entwickelte deshalb sogar ein Toolset (ein SDK), welches Webseitenentwicklern ermöglichte, die eigenen Nutzer ebenfalls zu analysieren - nur erfuhr Facebook dadurch auch davon, wer diese Webseiten wie nutzte.

Und so fand Facebook immer ausgefeiltere Möglichkeiten, personenbezogene Daten über die eigenen Nutzer einzusammeln. Dabei hat Facebook über [3 Milliarden Nutzerkonten](https://www.statista.com/statistics/272014/global-social-networks-ranked-by-number-of-users/). Es sind also *sehr viele* Menschen, die Facebook kategorisieren kann.

Mit mehr Geld kamen natürlich mehr Möglichkeiten. Facebook kaufte für eine Milliarde US-Dollar Instagram und für 19 Milliarden US-Dollar WhatsApp. Zwei der größten Services der Welt, die selbst bis dato kein nennenswertes Geld einnahmen. Das wurde zu der Zeit noch von den Medien belächelt, doch Mark Zuckerberg hatte verstanden, dass er sich etwas viel Wertvolleres ins Haus holte, als alle wussten: **Kontrolle**.

Facebook dient mir hier nur als greifbares Beispiel. Google hat sich vom Suchmaschinendienst zu einem Unternehmen entwickelt, das das meistverbreitete Smartphone-Betriebssystem, den meistgenutzten Browser, einen riesigen Foto-Dienst, den meistgenutzten E-Mail-Service und eine große KI (plus noch vieles mehr) betreibt. Auch Google hat ein Toolset für Entwickler, um die Nutzung (und Nutzer) zu analysieren. Alles grundsätzlich gratis zu nutzen. Google *will*, dass du es nutzt, denn Google hat dasselbe begriffen wie Facebook (bzw. heutzutage heißt es ja *Meta*): Daten sind Kontrolle und Kontrolle bringt Geld.

Alphabet (betreibt u. a. Google, YouTube, Gemini, Android), Meta (Facebook, Instagram, WhatsApp), Microsoft, Amazon und Apple gehören alle zu den zehn wertvollsten Unternehmen der Welt, obwohl wir als Konsumenten nichts bis kaum etwas zahlen. Und das liegt an deren Geschäftsmodellen; gerade Alphabet, Meta und Amazon sind extrem stark in der Profilbildung ihrer Nutzer und das Erreichen von so eng zugeschnittenen Zielgruppen aufgrund dieser Profile lässt sich teuer verkaufen.

## Was ist schon schlimm daran?

> Aber was ist denn schlimm an etwas personalisierter Werbung? Besser, als wenn ich Müll angezeigt bekomme!

Nun, Werbung ist nun mal nicht alles, was mit dem Sammeln und dem Verkauf von Daten angestellt werden kann. Doch **Daten sind Kontrolle**. Je exakter das Profil ist, welches Meta, Google, Amazon, Apple und heute auch KI-Anbieter wie OpenAI und Anthropic von einem haben, umso subtiler kann beeinflusst werden, was einem angezeigt wird. Das ist das Prinzip der *Filterblase*, in der wir uns oft in der digitalen Welt befinden. *Du* bestimmst nicht mehr, welche Nachrichten du liest - die Unternehmen tun es.

Und wenn die Daten erst einmal erhoben sind, können sie auch von anderen genutzt werden. Das berühmteste Beispiel ist sicher [Cambridge Analytica](https://www.amnesty.de/informieren/aktuell/great-hack-cambridge-analytica-ist-nur-die-spitze-des-eisbergs), ein Unternehmen, dessen App "thisisyourdigitallife" über eben genau dieses Facebook-Toolset ebenfalls Daten von Facebook-Nutzern erhielt - von 87 Millionen Facebook-Nutzern. Status-Updates, Likes und sogar Privatnachrichten. Die Firma warb damit, zu jeder wahlberechtigten Person in den USA bis zu 5.000 Datenpunkte zu besitzen und daraus Persönlichkeitstypen abzuleiten, um mit maßgeschneiderten Botschaften gezielt Wahlverhalten zu beeinflussen. Ich weiß, dass das Thema umstritten ist, trotzdem zeigt es, dass erhobene Daten missbraucht werden können.

Selbst wenn wir kein Problem damit hätten, dass Meta, Google, Amazon und Apple all diese Daten über uns haben, hört man leider viel zu häufig von Datenlecks. [Gerade Meta war hier in der Vergangenheit recht anfällig](https://www.heise.de/news/Sammelklage-Meta-und-Verbraucherzentralen-pruefen-Vergleich-11228525.html).

Und die Manipulation passierte subtil, da sind ganze Psychologen-Teams involviert. Wir sind nicht immun dagegen. Wären wir es, hätte die Werbebranche schon vor Jahrzehnten das Handtuch geworfen.

> Aber ich habe nichts zu verbergen.

Ich habe kurz überlegt, ob ich zu diesem noch immer kursierenden Ausdruck etwas schreiben soll. Wenn du das liest und dir sagst, das sei ja alles schön und gut, aber du hättest nichts zu verbergen, deshalb sei das für dich alles nicht relevant, dann melde dich gern bei mir unter `christian (at) tueti.space` und sende mir kurz deine Kreditkartendetails inklusive Sicherheitscode oder gewähre mir Zugriff auf deinen Passwortmanager (wenn du keinen hast, reicht mir das eine Allround-Passwort, das du überall nutzt). Falls du das doch nicht möchtest, ist das auch in Ordnung, aber wieso möchtest du diese Infos denn verbergen?!

Es ist übrigens okay, dass wir Dinge zu verbergen haben! *Privatsphäre* ist unser Recht und wir sollten entscheiden dürfen, was wir preisgeben.

## Big Tech treibt es zu weit

Es ist nicht mehr ausschließlich Werbung. Ich habe es mit Cambridge Analytica schon angerissen: Diese Daten können für Manipulation genutzt werden. Wir leben in einer politisch sehr angespannten Zeit und diese Daten werden genutzt, um reißerische, KI-generierte Inhalte einer extrem spitzen Zielgruppe auszuspielen. Um damit Wut zu schüren. Und nein, wir erkennen nicht mehr jedes KI-generierte Bild, die Technik ist mittlerweile einfach so gut.

In Hamburg sollen bei der Polizei bald [Drohnen die Hubschrauber und Reiterstaffel ersetzen](https://www.ndr.de/nachrichten/hamburg/hamburger-polizei-drohnen-sollen-hubschrauber-ersetzen,hubschrauber-206.html). Woher wollen wir wissen, was zukünftige Regierungen mit dem Zusammenspiel von Drohnen und Massendaten über jeden Einzelnen anstellen können? Ist das dystopisch? Vielleicht, aber Gesichtserkennung an Orten wie Bahnhöfen wurde bereits getestet. Wieso sollten Drohnen diese Funktionen nicht erhalten?

Es sind nicht nur Meta, Google und andere bekannte Unternehmen, die solche riesigen Datenmengen besitzen. Auch Palantir, dessen Analysesoftware Profile zu Menschen anhand verfügbarer Daten erstellt und bereits [polizeilich eingesetzt](https://www1.wdr.de/politik/politik-in-nrw/polizei-software-palantir-100.html) wird, ist nur stark, weil all diese Daten bereits verfügbar sind.

## Die Abhängigkeit von Big Tech

Schon lange sind wir von Big Tech abhängig und "Big Tech" in Europa sind US-amerikanische Unternehmen. Das schien lange kein Problem, da die weltpolitische Lage anders war. Als Europäer hätten wir uns nie Sorgen darüber machen müssen, dass wir amerikanische Dienste nutzen. Doch auch das ändert sich langsam und sowohl in Deutschland als auch auf EU-Ebene erkennt man, dass diese Abhängigkeit gebrochen werden muss.

Die USA und China zeigen, dass Regierungen Gesetze wie den [CLOUD Act](https://de.wikipedia.org/wiki/CLOUD_Act) (USA) beziehungsweise das [Nationale Geheimdienstgesetz von 2017](https://en.wikipedia.org/wiki/National_Intelligence_Law_of_the_People%27s_Republic_of_China) (China) verabschieden können, die ihnen ermöglichen, bei Bedarf auf Daten der heimischen Unternehmen zuzugreifen. Selbst wenn die Unternehmen die Daten außerhalb des Heimatlandes speichern. Europäische Rechenzentren bieten somit keinen Schutz mehr vor dem Zugriff dieser Länder. Mehr noch, im CLOUD Act wird festgehalten, dass die betroffenen Personen nicht informiert werden müssen, wenn deren Daten eingesehen werden.

Bei einer Einreise in die USA könnte geprüft werden, wie du zu den USA und deren Regierung stehst. Dabei musst du deine Meinung nicht aktiv gepostet oder politische Posts mit einem *Like* unterstützt haben. Allein durch dein Scrollverhalten, durch die Art der Artikel, die du liest, eben durch deinen digitalen Fußabdruck ist deine politische Gesinnung erfasst.

Dass die Services und Daten mittlerweile auch ein Werkzeug der US-Regierung geworden sind, lässt sich an zwei Beispielen konkretisieren.

Die US-Regierung hat bei starken KI-Modellen wie Anthropics Mythos und Fable 5 schon beschlossen, dass diese [Modelle in vollumfänglicher Stärke nur Amerikanern zur Verfügung gestellt werden dürfen](https://www.anthropic.com/news/fable-mythos-access). Nicht Einwohnern der USA, sondern Menschen mit amerikanischer Staatsbürgerschaft.

Da Anthropic dies nicht gewährleisten konnte, wurden diese Modelle eine Zeit lang komplett vom Markt genommen. Mythos ist bis heute nicht frei verfügbar und Fable 5 ist eine "Version mit Stützrädern", die bei gewissen Themen auf schwächere bzw. ältere Modelle umschwenkt. KI ist mittlerweile starker Treiber der Wirtschaft und wird es immer mehr. Die Entscheidung, wie sehr Europa davon profitieren darf, liegt bei einzelnen Personen in den USA.

Dann hat [Microsoft nach Sanktionen von Präsident Trump das E-Mail-Konto von Karim Khan gesperrt](https://www.heise.de/news/Strafgerichtshof-Microsofts-E-Mail-Sperre-als-Weckruf-fuer-digitale-Souveraenitaet-10387368.html). Karim Khan war Chefankläger des Internationalen Strafgerichtshofs und ein einzelner Regierungschef kann dessen Arbeit praktisch lahmlegen, wenn ihm diese nicht gefällt.

Was bedeutet das für jeden Einzelnen von uns, wenn unsere politische Einstellung, unser wirtschaftlicher Status, unser soziales Umfeld, unsere Hobbys, unsere Geheimnisse (die viel zu viele von uns mit ChatGPT teilen) eingesehen und profiliert werden können?

Es geht um mehr als nur Werbung.

> Aber wieso sollte gerade ich ins Visier genommen werden?

Auch das höre ich immer wieder. Aber es wird kein einzelner Mensch ins Visier genommen (sofern er nicht von speziellem Interesse ist, wie Karim Khan). Sehr wohl werden jedoch Gruppen von Menschen ins Visier genommen, die eine gewisse Einstellung zu einem Thema teilen. Wollen geopolitisch starke Länder andere Nationen manipulieren, kann jeder Teil einer Gruppe werden.

## Goldene Käfige und tolle Ökosysteme

Ich verstehe die Bequemlichkeit. Auch ich nutze Cloud-Dienste von US-Anbietern, habe ein Smartphone und ehrlicherweise meine gesamte Technik-Ausstattung auf das Ökosystem eines US-Konzerns ausgerichtet. Es läuft dann einfach. Es ist leicht zu bedienen und günstig oder gar gratis.

Natürlich wissen die Unternehmen, dass ein Ausbruch schwierig ist. Die Elterngruppe aus der Kita oder Schule wird nicht einfach zu Signal wechseln, weil man selbst kein WhatsApp mehr möchte. Webshops bieten nun mal PayPal an und nicht Wero. Die E-Mail-Adresse hat man seit 20 Jahren, das ändert man nicht in fünf Minuten. Ich verstehe die Reibungspunkte. Wenn ich meine Cloud-Dienste beim selben Anbieter habe, der auch mein Smartphone herstellt, dann spielt alles super zusammen - also meistens...

Es ist ein goldener Käfig, in dem man sitzt, aber die potenzielle Gefahr - oder eher: Verwundbarkeit - ist real.

## Wie geht's weiter mit der Serie Digitale Souveränität?

Unser digitales Leben wird von wenigen Konzernen betrieben. Wenige Entscheider innerhalb dieser Unternehmen sowie Regierungschefs der Länder, in denen diese Unternehmen ansässig sind, üben somit die Kontrolle über ebendieses digitale Leben aus. Und das ist Macht - sehr viel Macht.

Mit dieser Serie zum Thema "Digitale Souveränität" möchte ich in den kommenden Teilen aufzeigen, wie bestimmte Daten wieder in die eigene Hoheit zurückgeholt werden können und wie man seinen digitalen Fußabdruck etwas reduzieren und das digitale Profil entschärfen kann.

Niemand soll oder muss direkt alles löschen und nicht mehr am (digitalen) Leben teilnehmen. Aber jeder kann kleine Schritte machen. Wer mit der Einstellung *"die wissen doch eh schon alles"* resigniert, unterstützt diese großen Unternehmen. Die Services zu nutzen, ist bequem. Wir haben diese Accounts und wir nutzen all die Gratisdienste, aber es ist deshalb nicht zu spät, jetzt ein wenig zu justieren. Jeder nur so viel, wie er oder sie mag. Jeder Schritt ist ein guter Schritt und es muss keine "Alles-oder-Nichts"-Lösung sein.

Hier findest du die Liste der zugehörigen Artikel (erschienene verlinkt und Ideen):
- [Teil 2: Die Browserwahl: Weg von Chrome](/digitale-souveraenitaet-teil-2-weg-von-chrome/)
- Durch Werbung getrieben: Was bringt ein VPN?
- Deine digitale Identität: E-Mail ohne Tracking
- Sichere Service-Alternativen: Fotos, Online-Speicher, Office-Suite, usw.
- Self-Hosting: Daten im eigenen Zuhause
- ...und was mir noch einfällt