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

Eigentlich sollte dies ein Beitrag werden, in dem ich beschreibe, wie ich ganz einfach auf meinem iPad einen [Hugo](https://gohugo.io "Hugo")-Blog (bzw. das Ordner-Setup) erstellte, einen Kurzbefehl erschuf, der das Hugo-Gerüst mit meinen Beiträgen zu [GitHub](https://github.com "GitHub") deployed und eine GitHub Action dann meine Webseite erstellt und zu einem Hoster, wie [Uberspace](https://uberspace.de "Uberspace") oder [Netlify](https://www.netlify.com/ "Netlify") deployed. Natürlich wollte ich das ganze Setup auf dem iPad erstellen und dann stolz präsentieren, wie ich meinen iPad Blog Workflow vom Schreiben bis zur Veröffentlichung der Beiträge erschuf.

Aber da habe ich die Rechnung ohne Hugo gemacht. Kurz gesagt: Mein Setup steht, aber wow, war das eine Odysee - und am Ende habe ich doch am MacBook das Setup finalisiert. Aber ich kann den Blog nun vom iPad aus betreiben 🥳.

## Wieso ein iPad Blog Workflow?

Ich habe schon immer gern geschrieben und auch das Bloggen immer wieder probiert, einige Blogs bekamen sogar bereits eine (für mich) beachtliche Anzahl von Besuchern. Aber letztendlich war ich nie voll überzeugt, _wie_ ich bloggte. Über irgendein Admin-Interface von Wordpress oder Squarespace am Rechner. Die mobile Erfahrung war nie wirklich überzeugend und so endete es immer darin, dass ich "mir die Zeit nehmen musste" und in mein Büro an den Schreibtisch verschwand. Das fühlte sich wie "Aufwand" an und so schlief das Schreiben von Beiträgen ein.

So stellte ich mir die Frage, _wie_ ich denn gern bloggen würde?! Und immer wieder kam ich aufs iPad. Beiträge ganz simpel auf dem iPad verfassen, in einer Schreib-App meiner Wahl, dann per Klick veröffentlichen. Kein Aufwand mit Admin-Interfaces, einfach Schreiben und fertig. Motiviert wurde ich durch Federico Viticci und seiner erfolgreichen Seite [macstories.net](https://macstories.net "macstories.net"). Er bereibt diese Seite vollständig vom iPad und so dachte ich mir, dass auch ich einen Workflow finden müsste.

## Meine Vorstellung eines iPad Blog Workflows

Meine Vorstellung war dann immer noch recht einfach, nachdem ich es etwas konkretisierte: Schreiben in Markdown. Kurzbefehl mit Hilfe der App _[Working Copy](https://workingcopy.app/ "Working Copy")_ erstellen, welcher dann meinen Text zu GitHub pusht. Dort könnte eine GitHub Action laufen, welche meine Seite dann deployed. Theoretisch ganz easy.

Dann fiel meine Entscheidung auf das Framework [Hugo](https://gohugo.io "Hugo") und die Odysee sollte beginnen.

## WTF Hugo?!

Technisch ist Hugo die beste Entscheidung, zumindest für mich. Und ich dachte, ein einfach Scaffolding meines Projektes auf dem iPad wäre simpel und bauen würde meine Webseite ja via GitHub Action. Aber Pustekuchen: Bis eine Hugo Webseite steht, ist wirklich Konfiguration notwendig und diese ohne eine lokal laufende Instanz durchzuführen, ist sicherlich möglich, hätte mich aber verrückt gemacht! Hugo ist aktuell in der Version 0.147.9 veröffentlicht, nicht mal Version 1.0 und das merkt man einfach. Die Themes sind vermutlich meist von Privatleuten erstellt, die selbst ein solches Theme haben wollten und so einfach, wie Hugo es im Quickstart darstellt, ist es nicht, einen Blog nach seinen eigenen Vorstellungen vorzubereiten.

Ich habe schlussendlich ein eigenes HTML-Template erstellt, zwei vorhandene HTML-Layouts überschrieben, mein RSS-Template angepasst und eine eigene CSS-Datei mit Style-Erweiterungen erstellt. Das ist nichts, was ich hätte machen wollen, wenn ich nach jeder kleinen Änderung ein ganzes Deployment hätte durchlaufen lassen müssen. Eine lokal laufende Instanz war einfach notwendig für mich.

Bis ich bei dem Punkt war, mein gewähltes Theme - [PaperMod](https://github.com/adityatelange/hugo-PaperMod "PaperMod Theme") - so anzupassen, verlangte jedoch drei zuvor getestete Themes, die ich alle so gar nicht nach meinen Vorstellungen zu Laufen bringen konnte. Ein Theme setzte von sich aus Links, die zu der 404-Seite führten. Wieso?! Und wieso darf so ein Theme in die Liste der offiziellen Hugo-Seite aufgenommen werden? Ich dachte erst, es wäre mein Fehler und ich hätte etwas kaputt konfiguriert. Aber auch bei einer frischen Seite mit dem Theme war das Verhalten identisch.

Ich glaube nicht, dass Hugo tatsächlich für nicht technische Blogger aktuell nutzbar ist. Und auch ich wollte einfach nur eine Webseite, um mein iPad Blog Workflow zu leben. Aber bis dahin habe ich nun also ein paar Tage investiert, um diese Seite endlich für mich zufriedenstellend zum Laufen zu bringen.

Aber nach all den Anpassungen war es geschafft und zumindest lokal stand die Webseite und ich war bereit, auf dem iPad meine Beiträge zu schreiben.

## Deployment und Hosting

Manchmal sollte man einfach auf sein Bauchgefühl hören. Denn wie eingangs erwähnt war meine erste Idee, meinen Code auf GitHub zu hosten und via GitHub Action zu Uberspace zu deployen. Allerdings kostet Uberspace einen monatlichen Betrag und für recht kleine, statische Webseiten, wie mein Blog eine ist, gibt es tatsächlich Gratislösungen. GitHub selbst bietet GitHub Pages, einen gratis Service, der statische Webseiten hosten kann. Dieser Service wird vermutlich querfinanziert. Allerdings hatte ich GitHub Pages recht schnell aussortiert, da der Service ein paar Limitierungen hat (beispielsweise nur eine eigene Domain und auch Hugo wird nicht nativ unterstützt). Allerdings stieß ich dann auf die Konkurrenz [GitLab](https://about.gitlab.com/ "GitLab").

GitLab kannte ich bereits, hatte nur noch keinen privaten Account. Auch GitLab kann statische Seiten hosten und so war ein Account schnell erstellt ich wollte meine Datenschutzerklärung anpassen, um darauf hinzuweisen, dass ich meine Seite auf GitLab hoste. Nun, da merkte ich, dass es sich um ein US-Unternehmen handelt, das sich wenig um die DSGVO schert. Bereits 2018 wurde angefragt, welche Daten von Webseitenbesuchern geloggt werden - bis heute keine endgültige Antwort, nur der Verweis auf die generische Privacy Policy für Leute mit Account. Außerdem nutzt GitLab Cloudflare, gibt aber nicht genau an, ob die Webseiten dann über diesen CDN verteilt werden.

Erst wollte ich es mir schönreden: Für eine kleine Webseite sei es doch egal. Ich verweise einfach auf deren Policy... Nunja, damit fühlte ich mich trotzdem nicht wohl und mein Blog soll **Spaß machen**. Ich will mich wohlfühlen, diesen Blog als mein digitales Zuhause ansehen und so konnte ich meinen GitLab-Account direkt wieder löschen (denn ich nutze GitHub für all meinen Code). Dann nochmal in die Recherche gegangen, Uberspace gibt sich einfach super sympathisch und sie schätzen den Datenschutz (siehe meine [Datenschutzerklärung](impressum)). Und Uberspace kostet auch nicht mehr, als meine aktuelle Wordpress-Seite, die ich nicht wirklich gern nutze. Also Uberspace Account erstellt, Domain auf diese Seite gewechselt und GitHub Action erstellt, die sowohl beim Push neuer Seiten in den main Branch, sowie auch ein Mal täglich meine Seite neu erstellen.

Mit dem tägliche Build kann ich schon im Vorfeld neue Beiträge mit einem publishDate zu GitHub pushen und täglich wird meine Seite aktualisiert, um Beiträge zu veröffentlichen, deren publishDate erreicht ist.

Nach viel hin und her, bin ich nun also tatsächlich bei GitHub als Repo-Hoster, Uberspace als Hoster meiner Webseite und HostEurope für meine Domain gelandet.

## Mein iPad Blog Workflow: Vom Schreiben bis zur Veröffentlichung

Hat es denn nun nach den Startschwierigkeiten geklappt? Kann ich meinen Blog vom iPad aus betreiben? Ja! Aktuell sieht mein iPad Blog Workflow so aus, dass ich _[Ulysses](https://ulysses.app/ "Ulysse Webseite")_ nutze, um meine Beiträge zu schreiben. Auch, wenn die App mit einem Abo kommt, finde ich das Schreiben in der App einfach am Schönsten. Alternativ denke ich noch an _[iA Writer](https://ia.net/de/writer "iA Writer Webseite")_, diese App habe ich vor etlichen Jahren gekauft und sie war meine erste dedizierte Schreib-App. Ich finde, mittlerweile hat Ulysses sie überholt, aber dafür kommt sie ohne Abo und die Dateien liegen direkt in der Dateien-App des iPads, was die Verwaltung hinterher etwas leichter macht. Ich kann alle Bilder im selben Ordner halten und am Ende den Ordner zu _Working Copy_ importieren. Als letztes überlege ich, ob ich nicht einfach _[Textastic](https://www.textasticapp.com/ "Textastic Webseite")_ nutze. Ich fühle mich auch in Code Editoren wohl, was eventuell an meiner Vergangenheit als professioneller Programmierer liegt (mittlerweile schreibe ich nur noch privat Code), aber ich tippe gern in Code Editoren. Sie machen exakt das, was man will. Zeigen den Code, wie er geschrieben wurde. Das hat Charme für mich. Beide Alternativen sind allerdings Einmalkäufe und nicht gratis. Aber ich finde sympathisch, dass sie kein Abomodell nutzen.

In _Ulysses_ nutze ich aktuell eine Vorlage, die mir direkt das _Hugo Front Matter_, also die Metadaten für jeden Beitrag, in die Seite schreiben. So kann ich keine Angaben vergessen und muss die Vorlage nur ausfüllen.

Ist der Beitrag fertig, exportiere ich diesen als Markdown und nutze einen Kurzbefehl, um das Markdown und die zugehörigen Bilder ein einen Order meines Git Repos zu kopieren. Das Git Repo wird von _Working Copy_ verwaltet, die Dateien und die Ordnerstruktur der einzelnen Repos ist jedoch coolerweise in der Dateien-App einsehbar. Danach wird diese Änderung automatisch committed und zu GitLab gepusht.

In GitLab läuft eine CI/CD (Continous Integration / Continous Deployment) Pipeline - letztendlich eine GitLab Action - die meine aktualisierte Seite baut und dann veröffentlicht. Und so kommt mein neuer Beitrag nach dem Schreiben recht automatisiert online.

Ich kann natürlich auch Beiträge mit einem publishDate versehen und sie planen, denn meine Action läuft auch ohne Push neuer Dateien ein Mal am Tag. Ist also das publishDate eines Beitrags erreicht, würde dieser mit diesem täglichen Lauf ebenfalls veröffentlicht 😎

## Von nun an also am iPad

Von nun an kann ich also tatsächlich einen iPad Blog Workflow leben, vom Schreiben bis zur Veröffentlichung. In Ulysses getippt, via Kurzbefehl direkt an Working Copy in den richtigen Ordner exportieren und automatisch zu GitLab deployen. Der Rest ist automatisiert 😎

Von nun an liegt es also nur noch an den Artikel-Ideen und meiner Kreativität, die in einem solchen Umfeld hoffentlich blüht.

Und somit viel Spaß auf diesem vom iPad betriebenen Blog! 