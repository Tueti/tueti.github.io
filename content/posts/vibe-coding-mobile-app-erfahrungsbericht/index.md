---
author: ["Chrischi"]
title: "Vibe Coding einer mobilen App - Mein Erfahrungsbericht"
slug: "vibe-coding-mobile-app-erfahrungsbericht"
date: "2026-03-10"
draft: true
description: "Ich habe eine komplette App per Vibe Coding mit Claude gebaut. Mein ehrlicher Erfahrungsbericht: Was funktioniert, was nicht — und was es kostet."
summary: "Vibe Coding ist momentan voll im Trend. Die einen träumen vom nächsten 100k-Projekt, die anderen sorgen sich um den Daten-GAU. Aber was ist dran am Hype? Ich habe mal probiert, ein App-Projekt mit Claude Code im \"Vibe Coding\"-Stil umzusetzen. In diesem Post will ich meine Erfahrung einmal teilen."
ShowToc: true
TocOpen: false
tags: ["Claude Code", "Vibe Coding", "Opus", "Expo", "React Native", "Cross-Platform", "Softwareentwicklung", "KI", "Mein Stall"]
categories: ["Softwareentwicklung"]
cover:
  image: featured-image.webp
  caption: "Erstellt von Nanobanana/Gemini"
---

Der Hype um _Vibe Coding_ kann an keinem Entwickler mehr vorbeigehen. Die Entwicklergemeinde spaltet sich zwischen _"Ich schreibe keinen Code mehr"_ und _"KI kann keine produktionsreifen Applikationen schreiben"_. Es gibt renommierte Autoren in der Industrie, wie [Steve Yegge](https://steve-yegge.medium.com), die [Bücher](https://www.amazon.de/Vibe-Coding-Building-Production-Grade-Software/dp/1966280025) und [Blog-Artikel](https://sourcegraph.com/blog/revenge-of-the-junior-developer) zu dem Thema schreiben. Das Buch heißt _"Vibe Coding: Building Production-Grade Software With GenAI, Chat, Agents, and Beyond"_ und gibt Anleitungen und Tipps, wie produktionsreife Software vibecoded werden kann.

Im Blog-Artikel beschreibt Steve Yegge, dass sich Softwareentwicklung stetig weiterentwickelt hat. Agentic Development sei der nächste Schritt und keine Modeerscheinung. Die Pointe des Artikels ist jedoch, dass Yegge herausstellt, Seniorentwickler würden KI kritisch gegenüberstehen, da KI sich als "genauso fähig" beweisen müsse. Dies führe zu einer eher ablehnenden Haltung, während Juniorentwickler den Mehrwert sehen. Diese sehen, dass der eigene Output besser wird, wenn das Tool richtig bedient wird. Das Lernen der KI-Tools ist also ein intrinsisches Interesse und Unternehmen würden zukünftig eher KI-affine Juniorentwickler bevorzugen und so den alteingesessenen Seniorentwicklern vorziehen.

Mit all den Meinungen und auch diesen Beiträgen, wollte ich Vibe Coding ausprobieren. Beruflich programmiere ich (leider) selbst nicht mehr, aber privat habe ich immer wieder eigene Projekte. Projekte, bei denen ich in der Umsetzung frei bin.

## Das Projekt

Die besten Projekte entstehen, wenn man eigene Probleme lösen möchte und so kam es, dass meine Frau gerade auf der Suche nach einer Smartphone-App war, um das Stall-Management zu digitalisieren. Wir haben zwei Pferde und die Buchung von Hallenzeiten im Stall fand noch immer via Stift und Zettel statt. Die vorhandenen Apps (und es gab nicht viele) wollten die Pferdebesitzer per In-App-Kauf zur Kasse bitten und so ist die Idee geboren, eine solche App neu zu denken. Modern, gern auch mit Abo - aber nur für den Stallbesitzer - und mit all den Features, die meiner Frau aufgrund ihrer Erfahrung bereits bekannt waren.

Das passende Problem: Ich kenne mich in der iOS-Sphäre aus. Hatte bereits früher per Objective-C und in neuerer Zeit auch mit Swift und SwiftUI programmiert, aber mit Android hatte ich keine Erfahrung. Und generell ist ein solches Projekt für einen Ein-Mann-Entwickler doch besser cross-plattform umzusetzen. So entstand der Gedanke, mir einen Claude API Key zu besorgen und das Projekt komplett im Vibe Coding Stil umzusetzen.

_Disclaimer:_ Ich hatte davor keine Erfahrung, was Agentic Coding angeht, kannte mich jedoch mit Softwareentwicklung aus.

## Vibe Coding "Mein Stall"

Ganz transparent vorweg: Anfangs zahlte ich (im wahrsten Sinne des Wortes) etwas Lehrgeld. Ganz naiv dachte ich, ich beschreibe meine App mal eben so genau wie möglich, übergebe das an Claude und los geht's. Nunja, es ging los, aber das Ergebnis war nicht lauffähig und das Debugging einfach ein Graus. Nach recht kurzer Zeit verwarf ich das Projekt und startete neu. Diesmal iterativ und mit der Hilfe von Gemini. Denn um eine KI zu bändigen, sollte man auf jeden Fall eine andere nutzen 😎

Mit Gemini (in der Gratisversion) übernahm ich die Architektur der App und zerlegte diese in sinnvolle, testbare Schritte. So entwarfen wir einen Prompt für Claude, der das Fundament der App beschrieb: Den Tech-Stack (Expo App, mit `zustand` als State-Management, Firebase Backend, usw.), die Vision der Features und die benötigten Datenmodelle und wie diese fachlich zusammenspielen sollten. Außerdem konfigurierte ich die MCP Server von [Firebase](https://firebase.google.com) und [Context7](https://context7.com), sodass Claude Zugriff auf mein Backend bekam und die neuste Entwickler-Dokumentation zur Verfügung hatte.

Mit diesem Prompt ließ ich Claude [planen](https://code.claude.com/docs/en/common-workflows#use-plan-mode-for-safe-code-analysis) und den entworfenen Plan reviewte ich mit Gemini, der als mein Software-Architekt agierte. Und nach kurzen Anpassungen ließ ich Claude laufen. Ich iterierte über meine Features. Nach dem Fundament kam das Auth-Feature, ebenfalls so detailliert, dass kein "Back-Button"-Hack auf Android möglich sein sollte, dass ein Passwortreset möglich sein muss und potentiell Social Sign Ups notwendig sind. Ich formulierte auch aus, dass ein `user`-Dokument in Firebase angelegt werden muss, wenn sich ein neuer Nutzer registriert. Ich beschrieb die fachlichen Features und erwähnte technische Clues und Claude plante und baute mir diese.

Danach kamen die einzelnen Tabs als dedizierte Features (manchmal auch nur spezifische Screens, wenn diese umfangreich waren). Claude baute mir somit nach und nach eine vollständige App, die "von außen betrachtet" ziemlich gut aussah. Also nicht UI-technisch, das kann Claude anscheinend nicht, da ließ ich Gemini Code anpassen und Claude musste sich dann zukünftig daran orientieren. Aber vom Feature-Set und der Bedienung wirkte die App überzeugend.

## Der Charme des Vibe Coding

Ich fing an, den Hype zu verstehen. Das Ergebnis war eine immer umfänglicher werdende App. Und das nicht als _proof of concept_, sondern vollständig funktionsfähig. Über Signup und Login wurden reale Nutzer in der App angelegt. Man konnte echt Ställe anlegen und tatsächlich User Management betreiben. Nutzer konnten zu Ställen eingeladen werden und diesen beitreten, dann auch ihre Pferde dem Stall hinzufügen und Reithallen buchen. Das Herzstück der App schien ausgereift. Ich konnte in der App live sehen, sobald andere Nutzer eine neue Reitplatzbuchung durchführten. Meine Frau fand sowohl UI als auch UX wirklich gelungen. Nach nicht mal zwei Wochen Entwicklung als Feierabendprojekt war es eine _echte_ App! Kein Dummy-Backend, keine "Mock"-Daten. Echtes Backend, funktionierende Security im Backend, produktive Cloud Functions, über die relevante Aktionen durchgeführt wurden, eben wirklich _echte Interaktionen_! Die App _wirkte_ produktionsreif.

## Der Teufel steckt im Detail: Probleme beim Vibe Coding

Aber nach gut 2 Wochen _Vibe Coding_, in denen ich wirklich kaum Code selbst gelesen hatte und mich schlichtweg auf die Features konzentrierte, stieg ich tiefer ein. In den Logs konnte ich immer sehen, wann die App welche Daten vom Backend las und hier hätte es in Produktion irgendwann teuer werden können:
- Kein Caching
- Unnötig viele Lesezugriffe im Backend (beispielsweise alle Nutzer eines Stalles, obwohl nur n spezifische Nutzer benötigt waren)
- Pro Screen immer wieder dieselben Lesezugriffe, obwohl diese im State hätten persistiert werden können

Aber ich baue die App als _Vibe Coder_, ich werde diese Punkte also nicht selbst fixen und so promptete ich Verbesserungsaufträge. Beschrieb exakt, was gecached sein darf und was Live-Daten sein müssen. Beschrieb außerdem, wann die App trotz Cache ein Update anzeigen soll (Nutzer sollen zum Beispiel Änderungen an den eigenen Daten sofort reflektiert sehen) und ließ Claude all die Auffälligkeiten fixen.

## Claude als Berater

TODO: Beschreiben, wie ich mit Hilfe von Claude meine Apple und Google Dev Portale konfiguriert habe, um Social Sign ups und InAppPurchases zu ermöglichen

## Fazit

Bevor ich nun Nachrichten bekomme, dass dies "_Vibe Coding_ mit Stützrädern" war, das weiß ich. Ich habe mit Kollegen gesprochen, die mehrere DIN A4-Seiten an Prompts ausformuliert haben, den Auftrag geben, einzelne Features an Sub-Agents zu übergeben und dann so eine featuregetriebene App parallelisiert über Agents bauen zu lassen. Ich finde das wirklich total interessant und spannend, habe mich aber aus drei Gründen dagegen entschieden.

1. Das kann ziemlich teuer werden oder die Agents unterbrechen, weil das Budget alle ist. Bei Claude per API nutze ich die Prepaid-Variante und lasse Anthropic auch nicht automatisch das Konto aufladen 😅 Ich wollte auch ein Gefühl dafür bekommen, welche Prompts und Tätigkeiten wie viel kosten und ich wollte meine Kosten im Überblick behalten.
2. Ich war gar nicht auf dem Niveau, einen solchen Prompt zu erarbeiten. Mit dieser App habe ich viel gelernt, was Prompt- und Context-Engineering angeht. Mein erster, kläglicher Versuch, die Beschreibung einer App an Claude zu übergeben, ist gescheitert. Heute weiß ich, wieso und was ich alles nicht beachtet habe. Und ich bin mir sicher, auch mit meinem Wissen heute, würde ich wohl nicht alles beachten. Aber ich würde einiges mehr sehen, als noch vor ein paar Wochen. Das Thema ist super spannend!
3. Ich wollte die App iterativ selbst testen. Mein Vertrauen darin, die Tests einzelner Schritte ebenfalls von den Sub-Agents vornehmen zu lassen, gefiel mir nicht. Und ich denke, sofern mein eigenes Geld involviert ist, würde ich aktuell auch weiter meinen Ansatz fahren. Einfach, um zwischendrin immer sicher zu sein, dass der aktuelle Stand funktionsfähig ist. So hat man einen "Fallback", zu dem man zurückdrehen kann, sollte mal ein neues Feature komplett in die Hose gehen.

TODO: Noch den Abschnitt "Berater" einbauen

Am Ende habe ich ca. $400 in die Entwicklung der App gesteckt. Knapp $100 sind allerdings das gezahlte "Lehrgeld", das ich anfangs verbraten hatte. Nach dem Neustart investierte ich somit ungefähr die restlichen $300. Ist es das wert? Das muss jeder für sich entscheiden. Ich hoffe, die App wird zahlende Kunden generieren und wenn ich bedenke, in welcher Zeit ich die App auf die Beine stellen konnte, dann ist sie den Invest für mich wert, ja. Selbst, wenn sie keinen Umsatz generieren sollte, hat mich das Projekt vorangebracht und mir Einblicke in die Welt des _Agentic Coding_ oder _Vibe Coding_ gegeben. Es hat mir Spaß gemacht und allein deshalb war es das wert.

Ich bin fest der Meinung, dass Agentic Coding die Zukunft ist. Ich hätte diese App _niemals_ in der Zeit auf diesem Niveau bauen können! Ich hätte einige Entscheidungen vermutlich anders - und ehrlich gesagt auch schlechter - getroffen. Ich denke aber auch, dass - zumindest zum Zeitpunkt meines Schreibens - _Agentic Coding_ oder auch _Vibe Coding_ von Entwicklern durchgeführt werden sollte, denn eine gewisse Review muss noch stattfinden. Ich bin mir nicht mal sicher, ob dies zwingend eine typische Code Review sein muss oder eher eine architektonische Review. Aber selbst damit hat man schon eine brisante Meinung, denn die Diskussionen zu dem Thema bekomme ich beruflich hautnah mit und die Meinungen könnten teilweise nicht weiter auseinander liegen.

In diesem Sinne, viel Spaß mit dem KI-Assistenten eurer Wahl und wenn ihr selbst Pferdebesitzer seid und euren Stallbesitzer von der App überzeugen wollt, hier die Links zu den App Stores:

[iOS Link folgt nach Veröffentlichung]  
[Android Link folgt nach Veröffentlichung]