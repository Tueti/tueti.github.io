---
author: ["Chrischi"]
title: "iPad Blog Workflow: Vom Schreiben bis zur Veröffentlichung"
slug: "iPad Blog Workflow Vom Schreiben bis zur Veroeffentlichung"
date: "2025-06-30"
draft: false
description: "Meine Odysee, einen Hugo-Blog zu erstellen"
summary: "Vom iPad aus bloggen, ganz ohne halbherzig produzierte App von Squarespace oder Wordpress. Das war meine Idee und so landete ich bei Hugo. Aber wie naiv meine Vorstellung einer kurzen Einrichtung war, beschreibe ich hier."
tags: ["iPad", "Blog", "Hugo"]
categories: ["Blog"]
series: ["iPad Blog Workflow"]
#ShowToc: false
#TocOpen: false
cover:
  image: featured-image.webp
  caption: "Erstellt von ChatGPT"
---

Eigentlich sollte dies ein Beitrag werden, in dem ich beschreibe, wie ich ganz einfach auf meinem iPad einen [Hugo](https://gohugo.io "Hugo")-Blog (bzw. das Ordner-Setup) erstellte, einen Kurzbefehl erschuf, der das Hugo-Gerüst mit meinen Beiträgen zu [GitHub](https://github.com "GitHub") deployed und eine GitHub Action dann meine Webseite erstellt und zu einem Hoster, wie [Uberspace](https://uberspace.de "Uberspace") oder [Netlify](https://www.netlify.com/ "Netlify") kopierte. Natürlich wollte ich das ganze Setup auf dem iPad erstellen und dann stolz präsentieren, wie ich meinen iPad Blog Workflow vom Schreiben bis zur Veröffentlichung der Beiträge erschuf.

Aber da habe ich die Rechnung ohne Hugo gemacht. Kurz gesagt: Mein Setup steht, aber wow, war das eine Odysee - und am Ende habe ich doch am MacBook das Setup finalisiert. Aber ich kann den Blog nun vom iPad aus betreiben 🥳.

## Wieso ein iPad Blog Workflow?

Ich habe schon immer gern geschrieben und auch das Bloggen immer wieder probiert, einige Blogs bekamen sogar bereits eine (für mich) beachtliche Anzahl von Besuchern. Aber letztendlich war ich nie voll überzeugt, _wie_ ich bloggte. Über irgendein Admin-Interface von Wordpress oder Squarespace am Rechner. Die mobile Erfahrung war nie überzeugend und so endete es immer darin, dass ich mir "die Zeit nehmen musste" und in mein Büro an den Schreibtisch verschwand. Das fühlte sich wie "Aufwand" an und so schlief das Schreiben von Beiträgen ein.

Deshalb stellte ich mir die Frage, _wie_ ich denn gern bloggen würde?! Und immer wieder kam ich aufs iPad. Beiträge ganz simpel auf dem iPad verfassen, in einer Schreib-App meiner Wahl, dann per Klick veröffentlichen. Kein Aufwand mit Admin-Interfaces, einfach Schreiben und fertig. Motiviert wurde ich durch Federico Viticci und seiner erfolgreichen Seite [macstories.net](https://macstories.net "macstories.net"). Er bereibt diese Seite vollständig vom iPad und so dachte ich mir, dass auch ich einen Workflow finden könnte.

## Meine Vorstellung eines iPad Blog Workflows

Meine Vorstellung war dann immer noch recht einfach, nachdem ich sie etwas konkretisierte:
1. Schreiben in Markdown.
2. Per Kurzbefehl mit Hilfe der App _[Working Copy](https://workingcopy.app/ "Working Copy")_ zu GitHub pusht.
3. Dort automatisiert per GitHub Action zum Hoster deployen.

Theoretisch ganz einfach... Doch dann fiel meine Entscheidung auf das Framework [Hugo](https://gohugo.io "Hugo") und die Odysee sollte beginnen.

## WTF Hugo?!

Technisch ist Hugo die beste Entscheidung, zumindest für mich. Und ich dachte, ein einfaches Scaffolding meines Projektes auf dem iPad wäre simpel und bauen würde ich meine Webseite ja via GitHub Action. Aber Pustekuchen: Bis eine Hugo Webseite steht, ist wirklich Konfiguration notwendig und diese kann man kaum leisten, wenn nicht eine lokale Instanz läuft. Nun, sicherlich wäre es möglich gewesen, aber ich hätte meinen Verstand wohl verloren. Hugo ist aktuell in der Version 0.147.9 veröffentlicht, nicht mal Version 1.0 und das merkt man einfach. Es gibt beispielsweise wirklich schönes Themes, aber alle, die ich probierte hatten irgendein merkwürdiges Verhalten. Eine Hugo Webseite ist sehr viel mehr, als im Quickstart dargestellt.

Ich habe drei Themes probiert, die ich allesamt wieder verworfen habe, da es einfach nicht sauber laufen wollte. Am Ende war das gar nicht schlecht, da ich erst durch diese Suche bei [PaperMod](https://github.com/adityatelange/hugo-PaperMod "PaperMod Theme") landete, ein Theme, das mir vom Design direkt ins Auge fiel und das ich sicher schon früher probiert hätte, wäre es mir früher ins Auge gefallen.

Aber auch für dieses Theme habe ich schlussendlich ein eigenes HTML-Template erstellt für meine "Über mich"-Seite und mein Impressum. Ich habe außerdem zwei vorhandene HTML-Layouts überschrieben (um sie zu modifizieren), das RSS-Template angepasst und eine eigene CSS-Datei mit Style-Erweiterungen erstellt. Das ist nichts, was ich hätte machen wollen, wenn ich nach jeder kleinen Änderung ein ganzes Deployment hätte durchlaufen lassen müssen. Eine lokal laufende Instanz war einfach notwendig für mich. Am Ende war es gut, dass ich vorher schon mehrere Themes ausprobierte, denn nur dadurch war ich am Ende in der Lage, PaperMod zu zu modifizieren.

Ich glaube nicht, dass Hugo für Leute ist, die einfach und schnell zum fertigen Blog kommen wollen. Ich habe das mitgemacht, da ich mich für Technik interessiere, selbst in der IT arbeite und mein Ehrgeiz dann etwas geweckt war. Aber bis zur Fertigstellung musste ich mehrere Tage investieren.

Aber nach all den Anpassungen war es geschafft und zumindest lokal stand die Webseite nun und ich war bereit, diese zu veröffentlichen und danach auf dem iPad weiter zu machen.

## Hosting

Manchmal sollte man einfach auf sein Bauchgefühl hören. Denn wie eingangs erwähnt war meine erste Idee, meinen Code auf GitHub zu hosten und via GitHub Action zu Uberspace zu deployen. Allerdings kostet Uberspace einen monatlichen Betrag und für recht kleine, statische Webseiten, wie mein Blog eine ist, gibt es tatsächlich Gratislösungen. GitHub selbst bietet GitHub Pages, einen gratis Service, der statische Webseiten hosten kann. Dieser Service wird vermutlich querfinanziert und so kann GitHub diesen gratis anbieten. Allerdings hatte ich GitHub Pages recht schnell aussortiert, da der Service ein paar Limitierungen hat (beispielsweise nur eine eigene Domain und auch Hugo wird nicht nativ unterstützt). Allerdings stieß ich dann auf die Konkurrenz [GitLab](https://about.gitlab.com/ "GitLab").

GitLab kannte ich bereits, hatte allerdings noch keinen privaten Account. Auch GitLab kann statische Seiten hosten und macht dies gratis. Ein Account war schnell erstellt ich wollte meine Datenschutzerklärung anpassen, um darauf hinzuweisen, dass ich meine Seite auf GitLab hoste. Nun, da merkte ich, dass es sich um ein US-Unternehmen handelt, das sich wenig um die DSGVO schert. Bereits 2018 wurde angefragt, welche Daten von Webseitenbesuchern geloggt werden - bis heute keine zufriedenstellende Antwort. Nur der Verweis auf die generische Privacy Policy für Leute mit Account. Außerdem nutzt GitLab Cloudflare, gibt aber nicht genau an, ob die Webseiten dann über diesen CDN verteilt werden.

Erst wollte ich es mir schönreden: Für eine kleine Webseite sei es doch egal. Ich verweise einfach auf deren Policy... Nunja, damit fühlte ich mich trotzdem nicht wohl und mein Blog sollte **Spaß machen**. Er soll mein digitales Zuhause werden und so wollte ich hier nicht einfach das Nächstbeste wählen. Also nochmal in die Recherche gegangen, Uberspace gibt sich einfach super sympathisch und sie schätzen Datenschutz (siehe meine [Datenschutzerklärung](impressum)). Außerdem kostet Uberspace auch nicht mehr, als meine aktuelle Wordpress-Seite, die ich auf [HostEurope](https://hosteurope.de) betreibe, aber das Konzept gefällt mir deutlich besser. Nun also doch einen Uberspace Account erstellt und eine für mich endlich passende Domain auf [INWX](https://www.inwx.de/de) registriert. Ich hatte keine Ahnung, wie viel Zufriedenheit es einem gibt, wenn man die richtige URL für seine Seite gefunden hat. Ich hatte schon verschiedene URLs, meinen ganzen Namen, "Chrischis Blog" und mehr, aber es fühlte sich alles nicht zufriedenstellend an. [tueti.space](https://tueti.space) ist anders. Mein Online-Name, tueti, hat eine Bedeutung für mich, ist mein Spitzname, seit ich ein Teenager war und so war diese URL zum ersten Mal passend. Komisch zu beschreiben, aber es stimmte einfach.

Als nächstes habe ich also die URL mit meiner Seite auf Uberspace verknüpft - und dabei muss ich sagen, ich habe noch nirgends eine so gute Doku gelesen, wie bei Uberspace! Selbst die Beschreibung, wie ich die Domain für meine Seite konfiguriere, war top. Um alle DNS-Einträge sauber zu haben und Uberspace die Zeit zu geben, meine Zertifikate zu erstellen, wartete ich mit dem Rest bis zum nächsten Tag. Und tatsächlich, meine Domain [tueti.space](https://tueti.space) zeigte auf meine noch leere Uberspace-Seite 🥳.

Meine Webseite lieg lokal und das Hosting war vorbereitet. Jetzt musste noch beides verknüpft werden.

## Deployment

Nun ging es also ans automatische Deployment. Da GitLab nicht mehr für das Hosting benötigt war, entschied ich mich, meinen Code auf GitHub zu pushen und dort via GitHub Action an Uberspace zu deployen. Immerhin hält GitHub auch all meine anderen Repos und so war es ja auch meine erste Idee.

Ziel war ein Aktualisierung meiner Webseite, wenn ich in den _main_ Branch auf GitHub deploye. Das würde bedeuten, ich habe neuen Inhalt erstellt. Zusätzlich möchte ich ein Mal täglich in der Früh die Action laufen lassen. Sollte ich mal Beiträge im Voraus schreiben und mit einem in der Zukunft liegenden publishDate versehen, würden diese veröffentlicht, wenn die Seite neu gebaut würde. Aber genau das will ich dann ja nicht manuell machen. Also lasse ich die Seite ein Mal täglich neu bauen und zu Uberspace deployen, um so auch Beiträge zu veröffentlichen, die ihr publishDate erreicht haben.

Die Action - oder eher die gesamte Build & Deployment Pipeline - war tatsächlich schnell geschrieben, da es bereits fertige Actions gibt, die man nutzen kann, um einzelne Schritte auszuführen. Es ist also eher eine Art Baustein-System, das ich nur für meine "Deployment-Straße" zusammensetzen musste.

Und so läuft es nun im Hintergrund: Ich schreibe meine Beiträge, deploye zu GitHub und der Rest passiert automatisch 😎.

## Fazit - Mein iPad Blog Workflow: Vom Schreiben bis zur Veröffentlichung

Mein gewünschtes Setup existiert nun! Ich konnte den Blog zwar nicht vom iPad aus aufsetzen - bzw. hätte ich das sicher gekonnt, aber es hätte extrem viel länger gedauert - aber ich kann ihn nun vom iPad aus befüllen und betreiben.

Mein Workflow sieht nun so aus, dass ich entweder in _[iA Writer](https://ia.net/de/writer "iA Writer Webseite")_ oder _[Textastic](https://www.textasticapp.com/ "Textastic Webseite")_ (ich schreibe gern in Code Editoren, mag vielleicht daran liegen, dass ich auch gern programmiere) meine Beiträge verfasse und diese dann per Kurzbefehl in den entsprechenden Ordner meines GitHub Repos verschiebe. Dieses Repo habe ich dank _[Working Copy](https://workingcopy.app/ "Working Copy")_ auf meinem iPad und sehe es auch in der Dateien App. Danach committed und pusht _Working Copy_ meine neuen Dateien zu GitHub und schon läuft die GitHub Action, die meine Seite aktualisiert.

Und das Allerbeste ist, Uberspace ist ein außergewöhnlicher Hosting-Service und bietet SSH-Zugriff für deren Kunden an. Ich kann also via der App _[WebSSH](https://webssh.net/)_ direkt auf meine Hostingumgebung zugreifen. Ganz ohne nerviges bzw. nicht für Mobilgeräte optimiertes Admin-Interface, herrlich!

Von nun an kann ich also tatsächlich einen iPad Blog Workflow leben, vom Schreiben bis zur Veröffentlichung. Jetzt bleibt die Hoffnung, dass dieses Setup meine Kreativität und meinen Schreibdrang fördert, die Artikel fließen und der Blog hoffentlich blühen kann.

In diesem Sinne, viel Spaß auf diesem vom iPad betriebenen Blog! 