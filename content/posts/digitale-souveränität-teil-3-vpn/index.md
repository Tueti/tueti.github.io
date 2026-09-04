---
author: ["Chrischi"]
title: "Digitale Souveränität - Teil 3: Was bringt ein VPN?"
slug: "digitale-souveraenitaet-teil-3-was-bringt-ein-vpn"
date: "2026-09-04"
draft: false
description: "Was ein VPN wirklich leistet - und woran du erkennst, welchem Anbieter du trauen kannst. Fünf prüfbare Kriterien statt Werbeversprechen."
summary: "VPN-Anbieter versprechen Anonymität und eine No-Log-Policy. Nur: Versprechen kann man nicht überprüfen. Statt zu fragen, wem man vertrauen sollte, schaue ich mir an, welche Anbieter so gebaut sind, dass man ihnen gar nicht vertrauen muss - und leite daraus fünf Kriterien ab, die du selbst nachprüfen kannst. Mullvad, Windscribe, Proton VPN und NordVPN im Vergleich."
ShowToc: true
TocOpen: false
tags: ["Big Tech", "Digitale Souveränität", "NordVPN", "Proton", "VPN", "Mullvad", "Windscribe"]
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

Diese Meinung vertrete ich ebenfalls und deshalb wollte ich das Thema *VPN* erst angehen, nachdem ich die [Wahl eines datenschutzorientierten Browsers](/digitale-souveraenitaet-teil-2-weg-von-chrome/) beschrieben habe.

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

Glücklicherweise ist eine solche Struktur etwas, was du im Vorfeld *prüfen* kannst. Der Dienst, der die meisten Creator und Podcaster sponsert, muss nicht der beste sein. Bevor du dein Geld ausgibst, lass uns die Unternehmensstrukturen ansehen.

Für mich sind es fünf Fragen:

1. **Welche Daten verlangt der Anbieter bei der Registrierung und wie kannst du bezahlen?**
2. **Hat der Service ein klares und nachvollziehbares Geschäftsmodell?**
3. **In welchem Land operiert das Unternehmen und wie ist dort die Rechtslage für VPN-Anbieter?**
4. **Sind sie Open-Source?**
5. **Wie tritt der Dienst öffentlich auf?**

### Welche Daten verlangt der VPN-Anbieter?

Ein VPN nutzt du, um deine *Privatsphäre* noch weiter zu schützen und es ist tatsächlich nicht notwendig, dass der VPN-Anbieter Daten über dich abfragt, wenn du dich registrierst.

[Mullvad VPN](https://mullvad.net/de) ist ein sehr gutes Beispiel. Das Unternehmen verlangt weder deinen Namen noch deine E-Mail-Adresse für eine Registrierung. Dein Account ist eine 16-stellige Nummer, die nicht auf deine Person schließen lässt. Du kannst in Krypto oder bar in einem Briefumschlag (unter Nennung der Kontonummer) für den Dienst zahlen, wenn du möchtest. Dabei lädst du dein Konto auf (Prepaid) und hast kein Abo. Natürlich ist es dir möglich, auch per PayPal, Kreditkarte oder Überweisung zu bezahlen. Aber du könntest anonym bleiben.

[Windscribe](https://windscribe.com/de) hält es ähnlich. Dein Account basiert auf einem [32-stelligen Hash](https://windscribe.com/de/knowledge-base/articles/hashed-login). Du brauchst keine E-Mail-Adresse anzugeben und [kannst - auch, wenn es nicht deren empfohlener Weg ist - bar bezahlen](https://windscribe.com/blog/windscribe-accepts-cash-payments/).

[Proton VPN](https://protonvpn.com/de) und [NordVPN](https://nordvpn.com/de/) hingegen verlangen eine E-Mail-Adresse bei Account-Erstellung. Proton bietet ebenfalls Bargeld als Zahlungsmethode, während NordVPN das nur über den Umweg von [Retail-Karten ermöglicht, die man in Läden erwerben kann](https://nordvpn.com/de/retail/?nv_tri=TC_03849085667469132_1787744276534&nv_trs=1788522775488_1788522865730_3_27).

Während Mullvad und Windscribe hier sehr gut vorlegen, ist das noch keine Ablehnung der anderen Services. Jedoch wäre es bei einer solchen Account-Erstellung sehr schwierig (gar unmöglich?!), deiner Person gewisse Daten zuzuordnen.

### Das Geschäftsmodell des VPN-Dienstes

Wie verdient der Service Geld? Mullvad sagt ganz klar, sie bieten den Dienst nicht gratis an. 5€ pro Monat, keine Ermäßigungen, keine Rabattaktionen, dafür aber bisher auch noch nie eine Preiserhöhung. Sie sagen, ihr Geschäftsmodell trägt. Windscribe kostet ähnlich viel pro Monat, sofern man jährlich zahlt. Es gibt Aktionen, um Neukunden zu werben, etwas mehr Marketing, aber sie bieten ebenfalls nichts gratis an.

Proton hingegen gibt es sowohl gratis in eingeschränkter (aber sehr gut nutzbarer) Version als auch in einer zu bezahlenden *Plus*-Version. Es gibt auch immer wieder Neukundenrabatte, Black-Friday-Aktionen und Preisreduzierungen. Aber wie trägt sich die Gratisversion? Die Proton AG bietet nicht nur einen VPN-Dienst an. Es ist eine ganze Suite an datenschutzorientierten Services: E-Mail, Kalender, Passwortmanager, VPN, Online-Speicher [und noch einiges mehr](https://proton.me/de). Es gibt pro Service entweder die Möglichkeit, diese einzeln zu abonnieren oder aber als *[Proton Unlimited](https://proton.me/de/pricing)* ein Bundle zu nutzen. Sie schreiben offen, dass es ihnen wichtig ist, jedem ihre Dienste anbieten zu können - in nutzbarer Ausstattung. Dabei wollen sie auch alle einbinden, die solche privaten Services nutzen wollen (oder müssen), [dafür aber nicht zahlen können](https://proton.me/support/proton-plans#proton-free).

NordVPN bietet wie Mullvad und Windscribe keinen Gratisservice, dafür eine Geld-zurück-Garantie. Das macht den Einstieg möglicherweise leichter. Außerdem sind die Einstiegspreise oft extrem reduziert und man findet von den meisten Tech-Creators irgendeinen Affiliate-Link. Dafür steigt der Preis danach auch am drastischsten. Aber auch hier trägt sich der Service durch die zahlenden Kunden. Außerdem erobert NordVPN auch weitere Bereiche, wie beispielsweise eSIMs mit dem Dienst [Saily](https://saily.com/de/) und [Online-Speicher mit dem Dienst NordLocker](https://nordlocker.com/).

Gerade bei Diensten, die eine Gratisversion anbieten, solltest du dich fragen, wie der Service Geld verdient. Wie kann die Infrastruktur bezahlt und der Service am Leben gehalten werden? Gibt es darauf eine klare Antwort?

### In welchem Land sitzt der Dienst?

Es ist wichtig, in welchem Land ein VPN-Anbieter ansässig ist, denn die Rechtslagen sind sehr unterschiedlich. Viele Länder erzwingen von Internet-Anbietern, den Traffic der Kunden zu protokollieren. Du musst bei der Wahl deines VPN-Anbieters prüfen, ob das auch für diese Dienste gilt. Dazu kommen geopolitische Bündnisse: Welches Land teilt mit welchem anderen Land Informationen? All das muss beachtet werden.

Mullvad sitzt in Schweden und Schweden gehört zu den *[14 Eyes](https://netzpolitik.org/2013/schweden-ein-wichtiger-partner-der-five-eyes/)*, einer Geheimdienstkooperation verschiedener Länder zum Austausch abgefangener Kommunikation. Mullvad selbst argumentiert aber, dass Schweden trotzdem ein gutes Land für den Betrieb eines VPN-Dienstes sei. Denn ein Gesetz, welches Internetanbieter verpflichte, Traffic der Kunden zu loggen, beträfe VPN-Anbieter explizit nicht. Und [wer nichts loggt, kann nichts an die eigenen Behörden oder Behörden von Ländern des Bündnisses ausliefern](https://mullvad.net/en/vpn/laws-that-matter).

Dieselbe Argumentation nutzt auch [Yegor Sak (Mitbegründer) für Windscribe](https://windscribe.com/blog/i-doesnt-matter-how-many-eyes-you-have-66f59fc1e777/). Windscribe sitzt in Kanada, welches Mitglied bei den [5 Eyes, 9 Eyes](https://netzpolitik.org/2013/neun-augen-sehen-besser-als-fuenf-aber-deutschland-darf-nicht-mitspielen/) und 14 Eyes ist, weshalb die lokalen Gesetze hier ebenfalls wichtig sind.

Proton und NordVPN sitzen dagegen in Ländern, die in keiner *Eyes*-Allianz sind. Proton in der Schweiz und NordVPN in Panama. In beiden Ländern gibt es keine Speicherpflicht, wobei in der Schweiz aktuell die [VÜPF-Revision zur Disposition steht](https://www.digitale-gesellschaft.ch/2025/05/02/bundesrat-will-ueberwachungsstaat-per-verordnung-massiv-ausbauen-stellungnahme-zur-teilrevision-vuepf-und-vd-uepf/), welche eine digitale Überwachung massiv ausbauen würde. Proton unternimmt mittlerweile Anstrengungen, Teile seiner Dienste nach Deutschland und Norwegen zu verlagern.

Neben der (auch in der Schweiz noch existierenden) Freiheit, als VPN-Anbieter nichts speichern zu müssen, haben die beiden Länder ein starkes Datenschutzrecht. Ausländischen Anliegen wird nur stattgegeben, wenn ein jeweils [nationales Gericht das nach nationalem Recht bewertet und dem Ersuchen stattgegeben hat](https://protonvpn.com/features/swiss-based).

Proton aktualisiert dabei den eigenen [Transparenz-Report](https://protonvpn.com/blog/transparency-report), in welchem sie darlegen, wie viele Ersuchen es gab und wie viele bisher abgelehnt wurden (Spoiler: Zwischen 2019 und Juni 2026 waren es 458 Ersuchen, denen allen **nicht** stattgegeben wurde).

Bei diesem Punkt gibt es keinen klaren Sieger. Die Richtlinien sind nun bekannt, die Argumentation "Trotz *Eyes*-Allianz kann nichts herausgegeben werden, was nicht erfasst wird" ist schlüssig. Wie steht man zur Schweiz, Kanada, Schweden und Panama? Schau dir beim VPN-Dienst deiner Wahl zumindest den Sitz und die dortige Rechtslage an.

### Ist der VPN-Client Open Source?

Alle vier Anbieter legen (mindestens teilweise) den Quellcode ihrer VPN-Clients offen: Open-Source. Heißt, jeder kann sich den Programmcode ansehen und prüfen, ob der dort stehende Code für die VPN-Software, die man sich installiert, schadhaft ist.

[Mullvad](https://github.com/mullvad/mullvadvpn-app) geht hier aber einen Schritt weiter als die drei anderen: Deren Android-Builds sind seit dem 09. Mai 2025 (Version 2025.2) **reproduzierbar**. Mit demselben Quellcode, derselben Build-Umgebung und denselben Anweisungen kann jeder eine bit-für-bit identische Kopie erzeugen. Open Source zeigt nur, was im Code steht. Reproducible Builds beweisen, dass die App, die du [tatsächlich installierst, aus genau diesem Code gebaut wurde und unterwegs nicht manipuliert worden ist](https://mullvad.net/en/blog/reproducible-builds-verify-our-android-app-builds-bit-for-bit).

[Proton](https://github.com/ProtonVPN) legt den Code all seiner VPN-Clients offen, inklusive der dort herunterladbaren Releases (wo möglich).

[Windscribe](https://github.com/Windscribe) nennt seinen offenen Code einen "public mirror". Ob das immer derselbe Stand der aktuellsten Version ist, kann ich nicht sagen.

[NordVPN](https://github.com/NordSecurity/nordvpn-linux) legt von seinen VPN-Clients ausschließlich den Linux-Client offen. All die anderen Clients bleiben proprietär.

Open-Source ist gut und meiner Ansicht nach ein wichtiges Kriterium. Allerdings gehen die Anbieter unterschiedlich weit, was die Clients angeht. Mullvad läuft die Extra-Meile, während NordVPN das Nötigste macht. Keiner der Anbieter legt seinen Server-Code offen. Ein Aspekt wie die Datenpreisgabe bei der Anmeldung bleibt deshalb ein wichtiger Punkt.

### Wie tritt der Dienst öffentlich auf?

Die Dienste sind unterschiedlich populär und es gibt unterschiedliche Arten der Schlagzeilen.

Mullvad betreibt im Stillen seinen VPN-Dienst. Sie liefern möglichst viel, um zu *beweisen*, dass ihr Service ein guter Baustein im Schutz deiner Privatsphäre ist - so weit es für einen VPN-Dienst möglich ist, denk ans Zusammenspiel mit anderen Faktoren, wie deinem Browser.

Proton stärkt die eigene Wahrnehmung durch Aufklärungsvideos auf YouTube. Videos, in denen aufgezeigt und erklärt wird, wieso Privatsphäre auch im digitalen Raum wichtig ist. So bauen sie eine Marke um ihre Dienste herum, die sich als *datenschutzorientierte Suite* positioniert. Mehr als nur VPN, alles, was man für eine Online-Identität benötigt. Dabei ist ihr Ansatz, dir eine Identität zu geben (E-Mail, Online-Speicher etc.), aber dabei Funktionen zu liefern, sodass du diese Identität keinem anderen Service offenbaren musst (beispielsweise durch den VPN-Dienst und unbegrenzte E-Mail-Aliasse). Proton selbst legt dabei großen Wert darauf, dass alles, was bei ihnen liegt, clientseitig verschlüsselt ist und auch Proton selbst kein Profil über dich erstellt.

Windscribe wirbt natürlich damit, dass sie bereits die Chance hatten zu belegen, [vor Gericht nichts herausgeben zu können](https://windscribe.com/blog/windscribe-greek-court-case/). Damit stärken sie den eigenen Ruf. Das ist aber für die Zukunft nur als Argument zu gebrauchen, wenn die Struktur, die dies ermöglichte, weiterhin erhalten bleibt.

NordVPN betreibt aggressives Marketing und baut um den VPN-Dienst weitere Services herum - von denen manche auch bei anderen Anbietern verfügbar, nur nicht so stark beworben sind. [Dark Web Monitoring](https://nordvpn.com/de/features/dark-web-monitor/), [Anti-Phishing und Anti-Tracking Features](https://nordvpn.com/de/next-gen-antivirus/) oder sogar ein [Anruf-Schutz](https://nordvpn.com/de/features/call-protection/). Das klingt wie eine Sicherheits-Suite, ist aber oft schon etwas, was andere Services ebenfalls bieten: Browser, Passwort-Manager oder dein Smartphone. Das macht den Dienst auf keinen Fall schlecht oder nicht vertrauenswürdig (ich habe gerade erläutert, weshalb es nicht um Vertrauen geht), aber es gehört zur Reputation.

## Fazit

Alle vier sind gute Kandidaten und können genutzt werden. Trotzdem gibt es Unterschiede: Mullvad bietet reproduzierbare Builds und eine gänzlich anonyme Registrierung. NordVPN hingegen legt nur Code für den Linux-Client offen (womit die meisten genutzten Clients weiterhin proprietär bleiben) und erzwingt eine E-Mail-Adresse. Außerdem investiert NordVPN riesige Summen in Influencer-Marketing, während Mullvad sauber einen stabilen Preis kalkuliert.

Ich empfehle gern Mullvad für alle und Proton für diejenigen, die bereits im Proton-Ökosystem zuhause sind. Allerdings liegen alle vier Anbieter über der Schwelle. Triff deine eigene Wahl. Es ist tatsächlich (kombiniert mit anderen Maßnahmen) sinnvoll und du kennst nun die Kriterien für die Auswahl.