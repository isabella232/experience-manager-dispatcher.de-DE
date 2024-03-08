---
title: Dispatcher-Übersicht
seo-title: Adobe AEM Dispatcher Overview
description: Erfahren Sie, wie Sie mit dem Dispatcher u. a. mehr Sicherheit und besseres Caching für AEM-Cloud-Services erhalten.
seo-description: This article provides a general overview of Adobe Experience Manager Dispatcher.
uuid: 71766f86-5e91-446b-a078-061b179d090d
pageversionid: 1193211344162
topic-tags: dispatcher
content-type: reference
discoiquuid: 1d449ee2-4cdd-4b7a-8b4e-7e6fc0a1d7ee
exl-id: c9266683-6890-4359-96db-054b7e856dd0
source-git-commit: 7dd2ba37e149af960ba428421d64a5a24542eeeb
workflow-type: ht
source-wordcount: '3054'
ht-degree: 100%

---

# Dispatcher-Übersicht {#dispatcher-overview}

>[!NOTE]
>
>Dispatcher-Versionen sind unabhängig von AEM. Sie wurden möglicherweise zu dieser Seite umgeleitet, wenn Sie einem Link zur Dispatcher-Dokumentation gefolgt sind, der in der Dokumentation für eine frühere AEM-Version eingebettet ist.

Der Dispatcher ist das Caching- und Lastenausgleichs-Tool von Adobe Experience Manager, das in Verbindung mit einem Webserver der Unternehmensklasse verwendet wird.

Die Bereitstellung des Dispatchers ist unabhängig von gewählten Webserver und Betriebssystem:

1. Weitere Informationen zum Dispatcher (diese Seite). Weitere Informationen finden Sie außerdem in den [häufig gestellten Fragen zum Dispatcher](/help/using/dispatcher-faq.md).
1. Installieren Sie einen [unterstützten Webserver](https://experienceleague.adobe.com/docs/experience-manager-65/deploying/introduction/technical-requirements.html?lang=de) gemäß der Webserver-Dokumentation.
1. [Installieren Sie das Dispatcher-Modul](dispatcher-install.md) auf Ihrem Webserver und konfigurieren Sie den Webserver dementsprechend.
1. [Konfigurieren Sie den Dispatcher](dispatcher-configuration.md) (Datei „dispatcher.any“).
1. [Konfigurieren Sie AEM](page-invalidate.md), sodass der Cache durch Inhaltsaktualisierungen invalidiert wird.

>[!NOTE]
>
>Gehen Sie wie folgt vor, um besser nachvollziehen zu können, wie der Dispatcher mit AEM funktioniert:
>
>* Sehen Sie sich die [Fragen an AEM-Community-Fachleute vom Juli 2017](https://communities.adobeconnect.com/pf0gem7igw1f/) an.
>* Greifen Sie auf [dieses Repository](https://github.com/adobe/aem-dispatcher-experiments) zu. Es enthält eine Sammlung von Experimenten in einem einsatzfähigen Laborformat.


Ziehen Sie bei Bedarf die folgenden Ressourcen hinzu:

* [Die Dispatcher-Sicherheitscheckliste](security-checklist.md)
* [Die Dispatcher-Wissensdatenbank](https://helpx.adobe.com/de/experience-manager/kb/index/dispatcher.html)
* [Optimierung von Websites für die Cache-Leistung](https://experienceleague.adobe.com/docs/experience-manager-65/deploying/configuring/configuring-performance.html?lang=de)
* [Verwenden des Dispatchers mit mehreren Domains](dispatcher-domains.md)
* [Verwenden von SSL mit dem Dispatcher](dispatcher-ssl.md)
* [Implementieren der Zwischenspeicherung unter Berücksichtigung von Berechtigungen](permissions-cache.md)
* [Beheben von Problemen beim Dispatcher](dispatcher-troubleshooting.md)
* [Am häufigsten gestellte Fragen zu Dispatcher-Problemen](dispatcher-faq.md)

>[!NOTE]
>
>**Der Dispatcher wird am häufigsten verwendet**, um Antworten aus einer **AEM-Veröffentlichungsinstanz** zwischenzuspeichern und so die Reaktionsfähigkeit und Sicherheit einer öffentlich zugänglichen veröffentlichten Website zu erhöhen. Diese Anwendung steht im Mittelpunkt dieses Themas.
>
>Mit dem Dispatcher kann jedoch auch die Reaktionsgeschwindigkeit der **Autoreninstanz** gesteigert werden, insbesondere wenn Ihre Website von vielen Benutzenden bearbeitet und aktualisiert wird. Weitere Informationen zu diesem Fall finden Sie unten unter [Verwenden eines Dispatchers mit einem Author-Server](#using-a-dispatcher-with-an-author-server).

## Gründe für die Verwendung des Dispatchers zum Implementieren der Zwischenspeicherung  {#why-use-dispatcher-to-implement-caching}

Es gibt zwei grundlegende Ansätze zum Veröffentlichen im Web:

* **Statische Webserver** wie Apache oder IIS sind einfach und schnell.
* **Content-Management-Server** stellen dynamische, intelligente Inhalte in Echtzeit bereit, haben jedoch eine deutlich längere Rechenzeit und benötigen zudem andere Ressourcen.

Mit dem Dispatcher können Sie eine Umgebung realisieren, die schnell und dynamisch ist. Er funktioniert als Teil eines statischen HTML-Servers wie Apache mit folgendem Ziel:

* Speichern (oder „Caching“) von möglichst vielen Site-Inhalten in Form einer statischen Website
* Möglichst wenig Zugriff auf die Layout-Engine

Dies bedeutet:

* **statische Inhalte** werden mit derselben Geschwindigkeit und Leichtigkeit wie auf einem statischen Webserver verarbeitet. Außerdem können Sie die Verwaltungs- und Sicherheits-Tools verwenden, die für Ihre statischen Webserver verfügbar sind.

* **Dynamische Inhalte** werden nach Bedarf generiert, ohne das System mehr als absolut notwendig zu verlangsamen.

Der Dispatcher umfasst Mechanismen zum Generieren und Aktualisieren von statischem HTML auf Grundlage der Inhalte der dynamischen Site. Sie können detailliert angeben, welche Dokumente als statische Dateien gespeichert werden sollen und welche immer dynamisch generiert werden.

In diesem Abschnitt werden die diesem Prozess zugrunde liegenden Prinzipien beschrieben.

### Statischer Webserver {#static-web-server}

![](assets/chlimage_1-3.png)

Ein statischer Webserver, wie z. B. Apache oder IIS, stellt statische HTML-Dateien für die Besucherinnen und Besucher Ihrer Website bereit. Statische Seiten werden einmalig erstellt, daher wird für jede Anfrage derselbe Inhalt bereitgestellt.

Dieser Vorgang ist sehr einfach und effizient. Wenn jemand eine Datei anfordert (z. B. eine HTML-Seite), wird die Datei direkt aus dem Speicher abgerufen und allenfalls von der lokalen Festplatte gelesen. Statische Webserver sind bereits seit einiger Zeit verfügbar, sodass eine umfangreiche Auswahl an Werkzeugen für Administration und Sicherheitsverwaltung bereitsteht. Außerdem sind sie gut in Netzwerkinfrastrukturen integriert.

### Inhaltsverwaltungs-Server  {#content-management-servers}

![](assets/chlimage_1-4.png)

Wenn Sie einen Inhaltsverwaltungs-Server wie AEM verwenden, wird die Anfrage einer Besucherin oder eines Besuchers durch eine erweiterte Layout-Engine verarbeitet. Die Engine liest Inhalte aus einem Repository, das diese kombiniert mit Stilen, Formaten und Zugriffsrechten in ein Dokument umwandelt, das individuell auf die Anforderungen der Besucherin oder des Besuchers abgestimmt ist.

Mit diesem Workflow können Sie vielseitigere, dynamische Inhalte erstellen und die Flexibilität und Funktionalität Ihrer Website steigern. Allerdings ist für die Layout-Engine mehr Verarbeitungsleistung als für einen statischen Server erforderlich. Daher ist dieses Setup für Verzögerungen anfällig, wenn viele Besucherinnen und Besucher das System verwenden.

## Zwischenspeicherung durch den Dispatcher  {#how-dispatcher-performs-caching}

![](assets/chlimage_1-5.png)

**Das Cache-Verzeichnis** Zum Zwischenspeichern nutzt das Dispatcher-Modul die Fähigkeit des Webservers, statische Inhalte bereitzustellen. Der Dispatcher legt die zwischengespeicherten Dokumente im Basisverzeichnis des Webservers ab.

>[!NOTE]
>
>Wenn die Konfiguration für das Zwischenspeichern von HTTP-Headern fehlt, speichert der Dispatcher nur den HTML-Code der Seite. Die HTTP-Header werden nicht gespeichert. Dieses Szenario kann problematisch sein, wenn Sie verschiedene Codierungen in Ihrer Website verwenden, da diese Seiten unter Umständen verloren gehen. Informationen zum Aktivieren der HTTP-Header-Zwischenspeicherung finden Sie unter [Konfigurieren des Dispatcher-Cache](https://experienceleague.adobe.com/docs/experience-manager-dispatcher/using/configuring/dispatcher-configuration.html?lang=de).

>[!NOTE]
>
>Das Suchen des Stammverzeichnisses Ihres Webservers auf NAS-Speichern führt zu einer Leistungsverringerung. Auch wenn ein Stammverzeichnis auf NAS von mehreren Webservern gemeinsam genutzt wird, können zwischenzeitlich Sperren auftreten, wenn Replikationsaktionen durchgeführt werden.

>[!NOTE]
>
>Der Dispatcher speichert das zwischengespeicherte Dokument in einer Struktur, die der angeforderten URL entspricht.
>
>Für die Länge des Dateinamens kann es Einschränkungen auf Betriebssystemebene geben. Das kann der Fall sein, wenn Sie eine URL mit zahlreichen Selektoren haben.

### Methoden für die Zwischenspeicherung 

Der Dispatcher setzt auf zwei Hauptverfahren zum Aktualisieren des zwischengespeicherten Inhalts, wenn Änderungen an der Website vorgenommen werden:

* **Inhaltsaktualisierungen** entfernen die geänderten Seiten und die Dateien, die sich direkt auf sie beziehen.
* **Automatische Invalidierung** macht automatisch jene Teile des Caches ungültig, die nach einer Aktualisierung möglicherweise veraltet sind. Dies bedeutet, dass entsprechende Seiten als veraltet markiert werden, ohne dass Daten gelöscht werden.

### Inhaltsaktualisierungen

Bei einer Inhaltsaktualisierung werden ein oder mehrere AEM-Dokumente geändert. AEM sendet eine Syndizierungsanfrage an den Dispatcher, der den Cache entsprechend aktualisiert:

1. Die bearbeiteten Dateien werden aus dem Cache gelöscht.
1. Es werden alle Dateien aus dem Cache gelöscht, die mit demselben Handle beginnen. Wenn beispielsweise die Datei „/de/index.html“ aktualisiert wird, werden alle Dateien, die mit „/de/index.“ beginnen, gelöscht. Auf diese Weise können Sie Cache-effiziente Sites erstellen, insbesondere im Hinblick auf die Navigation bei Bildern.
1. Die sogenannte **Stat-Datei** wird *bearbeitet*, indem der Zeitstempel der Stat-Datei aktualisiert wird, um das Datum der letzten Änderung anzugeben.

Beachten Sie die folgenden Punkte:

* Inhaltsaktualisierungen werden in der Regel mit einem Authoring-System verwendet, das „weiß“, was ersetzt werden muss.
* Dateien, die von einer Inhaltsaktualisierung betroffen sind, werden entfernt, aber nicht sofort ersetzt. Das nächste Mal, wenn eine solche Datei angefordert wird, ruft der Dispatcher die neue Datei aus der AEM-Instanz ab und speichert sie im Cache, sodass der alte Inhalt überschrieben wird.
* In der Regel werden automatisch generierte Bilder, die Text von einer Seite enthalten, in den Bilddateien gespeichert, die mit demselben Handle beginnen. So wird gewährleistet, dass die Zuordnung zum Löschen vorhanden ist. Sie können z. B. den Titeltext der Seite „mypage.html“ als Bilddatei „mypage.titlePicture.gif“ im selben Ordner speichern. Auf diese Weise wird das Bild automatisch jedes Mal aus dem Cache gelöscht, wenn die Seite aktualisiert wird. So können Sie sicher sein, dass das Bild immer die aktuelle Version der Seite darstellt.
* Sie können mehrere Stat-Dateien verwenden, z. B. eine pro Sprachen-Ordner. Wenn eine Seite aktualisiert wird, sucht AEM den nächsten übergeordneten Ordner, der eine Statfile enthält, und *ändert*  diese Datei.

### Automatische Invalidierung

Bei der automatischen Invalidierung werden Teile des Caches automatisch invalidiert – ohne Dateien physisch zu löschen. Bei jeder Inhaltsaktualisierung wird die sogenannte Stat-Datei bearbeitet, sodass deren Zeitstempel den Zeitpunkt der letzten Aktualisierung angibt.

Der Dispatcher verfügt über eine Liste der Dateien, für die die automatische Invalidierung gilt. Wenn ein Dokument aus dieser Liste angefordert wird, vergleicht der Dispatcher das Datum des zwischengespeicherten Dokuments mit dem Zeitstempel der Stat-Datei:

* Wenn das zwischengespeicherte Dokument neuer ist, gibt der Dispatcher es zurück.
* Wenn es älter ist, ruft der Dispatcher die aktuelle Version aus der AEM-Instanz ab.

Auch hier sollten bestimmte Punkte beachtet werden:

* Die automatische Invalidierung wird normalerweise bei komplexen Wechselbeziehungen verwendet, z. B. für HTML-Seiten. Diese Seiten enthalten Links und Navigationseinträge, sodass sie in der Regel nach einer Inhaltsaktualisierung aktualisiert werden müssen. Wenn Sie automatisch generierte PDF- oder Bilddateien haben, können Sie auch für diese die automatische Invalidierung festlegen.
* Für die automatische Invalidierung ist bei der Aktualisierung keine Aktion durch den Dispatcher erforderlich, mit Ausnahme der Bearbeitung der Stat-Datei. Durch das Bearbeiten der Stat-Datei ist allerdings automatisch der Inhalt des Zwischenspeichers veraltet, ohne dass er physisch aus dem Cache entfernt wird.

## Zurückgeben von Dokumenten durch den Dispatcher  {#how-dispatcher-returns-documents}

![](assets/chlimage_1-6.png)

### Festlegen, ob ein Dokument zwischengespeichert werden soll

Sie können [in der Konfigurationsdatei definieren, welche Dokumente der Dispatcher zwischenspeichert](https://experienceleague.adobe.com/docs/experience-manager-dispatcher/using/configuring/dispatcher-configuration.html?lang=de). Der Dispatcher überprüft die Anforderung anhand der Liste der Dokumente, die zwischengespeichert werden können. Wenn das Dokument nicht in dieser Liste enthalten ist, fordert der Dispatcher das Dokument von der AEM-Instanz an.

Der Dispatcher ruft das Dokument in den folgenden Fällen immer direkt aus der AEM-Instanz ab:

* Der Anfrage-URI enthält ein Fragezeichen „`?`“. Dieses Szenario bedeutet normalerweise eine dynamische Seite (z. B. ein Suchergebnis), die nicht zwischengespeichert werden muss.
* Die Dateierweiterung fehlt. Der Webserver benötigt die Erweiterung, um den Dokumenttyp (den MIME-Typ) zu ermitteln.
* Der Authentifizierungs-Header wurde festgelegt (konfigurierbar).

>[!NOTE]
>
>Die Methoden GET oder HEAD (für den HTTP-Header) können vom Dispatcher zwischengespeichert werden. Weitere Informationen zum Zwischenspeichern von Antwortheadern finden Sie im Abschnitt [Zwischenspeichern von HTTP-Antwortheadern](https://experienceleague.adobe.com/docs/experience-manager-dispatcher/using/configuring/dispatcher-configuration.html?lang=de).

### Bestimmen, ob ein Dokument zwischengespeichert wird 

Der Dispatcher speichert die zwischengespeicherten Dateien auf dem Webserver, als wären sie Teil einer statischen Website. Wenn jemand ein Cache-fähiges Dokument anfordert, überprüft der Dispatcher, ob das Dokument im Dateisystem des Webservers vorhanden ist:

* Wenn das Dokument zwischengespeichert wurde, gibt der Dispatcher die Datei zurück.
* Wenn es nicht zwischengespeichert wurde, fordert der Dispatcher das Dokument von der AEM-Instanz an.

### Bestimmen, ob ein Dokument aktuell ist

Um festzustellen, ob ein Dokument aktuell ist, führt der Dispatcher zwei Schritte aus:

1. Es wird geprüft, ob für das Dokument die automatische Invalidierung ausgeführt wird. Ist dies nicht der Fall, wird das Dokument als aktuell betrachtet.
1. Wenn das Dokument für die automatische Invalidierung konfiguriert wurde, überprüft der Dispatcher, ob es älter oder neuer als die letzte verfügbare Änderung ist. Wenn es älter ist, ruft der Dispatcher die aktuelle Version von der AEM-Instanz ab und ersetzt die Version im Cache.

>[!NOTE]
>
>Dokumente ohne **automatische Invalidierung** verbleiben im Cache, bis sie physisch gelöscht werden. Beispielsweise durch eine Inhaltsaktualisierung auf der Website.

## Vorteile des Lastenausgleichs {#the-benefits-of-load-balancing}

Beim Lastenausgleich wird die Rechenleistung für eine Website auf mehrere Instanzen von AEM verteilt.

![](assets/chlimage_1-7.png)

Vorteile:

* **Verbesserte Verarbeitungsleistung**
In der Praxis bedeutet eine verbesserte Verarbeitungsleistung, dass der Dispatcher Dokumentanforderungen zwischen mehreren Instanzen von AEM aufteilt. Da jede Instanz jetzt weniger Dokumente verarbeiten muss, sind die Reaktionszeiten kürzer. Der Dispatcher führt interne Statistiken für jede Dokumentenkategorie, sodass die Anforderungen geschätzt und die Abfragen effizient aufgeteilt werden können.

* **Verbesserte Fail-Safe-Abdeckung**
Wenn der Dispatcher keine Antworten von einer Instanz empfängt, werden Anfragen automatisch an eine der anderen Instanzen weitergeleitet. Wenn eine Instanz nicht verfügbar ist, verlangsamt sich dadurch lediglich die Site, und zwar proportional zur verloren gegangenen Rechenleistung. Alle Dienste werden jedoch fortgesetzt.

* Sie können auch verschiedene Websites auf demselben statischen Webserver verwalten.

>[!NOTE]
>
>Beim Lastenausgleich werden die Lasten effizient verteilt, bei der Zwischenspeicherung werden sie dagegen reduziert. Daher sollten Sie das Zwischenspeichern optimieren und die Gesamtlast reduzieren, bevor Sie den Lastenausgleich einrichten. Durch eine optimal konfigurierte Zwischenspeicherung kann der Lastenausgleich leistungsstärker werden oder sich komplett erübrigen.

>[!CAUTION]
>
>Während ein einzelner Dispatcher in der Lage ist, die Kapazität der verfügbaren Veröffentlichungsinstanzen auszunutzen, kann es bei einigen wenigen Anwendungen sinnvoll sein, auch einen Lastenausgleich zwischen zwei Dispatcher-Instanzen einzurichten. Konfigurationen mit mehreren Dispatchern müssen sorgfältig durchdacht werden, da ein weiterer Dispatcher die Last auf die verfügbaren Veröffentlichungsinstanzen erhöhen und schnell zu einer Leistungsverringerung bei den meisten Anwendungen führen kann.

## Wie der Dispatcher einen Lastenausgleich durchführt {#how-the-dispatcher-performs-load-balancing}

### Leistungsstatistiken

Der Dispatcher führt interne Statistiken dazu, wie schnell die einzelnen AEM-Instanzen Dokumente verarbeiten. Auf Grundlage dieser Daten schätzt der Dispatcher, welche Instanz die schnellste Reaktionszeit beim Beantworten einer Anfrage bietet, und reserviert die erforderliche Rechenzeit in dieser Instanz.

Für andere Arten von Anfragen können andere durchschnittliche Bearbeitungszeiten gelten. Daher können Sie mit dem Dispatcher Dokumentkategorien angeben. Diese Kategorien werden dann beim Berechnen der Zeitschätzungen berücksichtigt. Beispielsweise können Sie zwischen HTML-Seiten und Bildern differenzieren, da die typischen Antwortzeiten sich hier wahrscheinlich deutlich unterscheiden.

Wenn Sie eine umfangreiche Suchfunktion verwenden, können Sie eine Kategorie für Suchabfragen erstellen. So kann der Dispatcher Suchabfragen an diejenige Instanz senden, die am schnellsten antwortet. Diese Methode verhindert zudem, dass eine langsamere Instanz nicht mehr reagiert, wenn sie mehrere „schwere“ Suchabfragen erhält, während die anderen „leichtere“ Anfragen erhalten.

### Personalisierte Inhalte („Sticky-Verbindungen“) 

Sticky-Verbindungen stellen sicher, dass alle Dokumente für eine Person in derselben AEM-Instanz erstellt werden. Dieser Punkt ist wichtig, wenn Sie personalisierte Seiten und Sitzungsdaten verwenden. Die Daten werden in der Instanz gespeichert, sodass nachfolgende Anfragen von derselben Person zu dieser Instanz zurückführen müssen. Andernfalls gehen die Daten verloren.

Da durch Sticky-Verbindungen die Fähigkeit des Dispatchers eingeschränkt wird, die Anfragen zu optimieren, sollten Sie sie nur bei Bedarf verwenden. Sie können den Ordner angeben, in dem die „Sticky“-Dokumente gespeichert werden, und so sicherstellen, dass alle Dokumente in diesem Ordner für jede Person in derselben Instanz erstellt werden.

>[!NOTE]
>
>Bei den meisten Seiten, die Sticky-Verbindungen verwenden, müssen Sie das Caching deaktivieren. Andernfalls wird die Seite für alle Benutzenden gleich angezeigt, unabhängig vom Sitzungsinhalt.
>
>Bei *einigen* Anwendungen ist es möglich, Sticky-Verbindungen und Caching gleichzeitig zu verwenden, etwa wenn Sie ein Formular anzeigen, das Daten in die Sitzung schreibt.

## Verwenden mehrerer Dispatcher  {#using-multiple-dispatchers}

In komplexen Umgebungen können Sie mehrere Dispatcher verwenden. Folgende Szenarien sind möglich:

* ein Dispatcher zum Veröffentlichen einer Website im Intranet
* ein zweiter Dispatcher mit einer anderen Adresse und anderen Sicherheitseinstellungen zum Veröffentlichen desselben Inhalts im Internet

In einem solchen Fall müssen Sie sicherstellen, dass jede Anfrage nur einen Dispatcher durchläuft. Ein Dispatcher verarbeitet keine Anfragen, die von einem anderen Dispatcher stammen. Stellen Sie daher sicher, dass beide Dispatcher direkt auf die AEM-Website zugreifen.

## Verwenden des Dispatchers mit einem CDN  {#using-dispatcher-with-a-cdn}

Ein CDN (Content Delivery Network) wie Akamai Edge Delivery oder Amazon Cloud Front stellt Inhalte von einem Speicherort in der Nähe des Endbenutzers bereit. Dies hat folgende Vorteile:

* Verkürzung der Reaktionszeiten für Endbenutzer
* Verringern der Auslastung Ihrer Server

Als HTTP-Infrastrukturkomponente funktioniert ein CDN ähnlich wie der Dispatcher. Wenn ein CDN-Knoten eine Anfrage erhält, wird die Anfrage aus dem Cache bedient, sofern möglich (d. h., die Ressource ist im Cache verfügbar und gültig). Andernfalls wird der nächstgelegene Server kontaktiert, um die Ressource abzurufen und sie ggf. für weitere Anfragen zwischenzuspeichern.

Welcher Server der nächstgelegene ist, hängt vom jeweiligen Setup ab. In einem Akamai-Setup kann die Abfrage z. B. den folgenden Pfad durchlaufen:

* Akamai Edge-Knoten
* Akamai-Midgres-Ebene
* Ihre Firewall
* Ihren Load-Balancer
* Dispatcher
* AEM

Normalerweise ist der Dispatcher der nächstgelegene Server, der das Dokument aus einem Cache bereitstellen und die Antwort-Header beeinflussen kann, die an den CDN-Server zurückgegeben werden.

## Steuern eines CDN-Cache {#controlling-a-cdn-cache}

Es gibt eine Reihe von Möglichkeiten zu steuern, wie lange ein CDN eine Ressource zwischenspeichert, bevor sie erneut vom Dispatcher abgerufen wird.

1. Explizite Konfiguration\
   Sie können konfigurieren, wie lange bestimmte Ressourcen im Cache des CDNs beibehalten werden, abhängig von MIME-Typ, Erweiterung, Anfragetyp usw.

1. Ablauf- und Cache-Control-Header\
   Die meisten CDNs berücksichtigen die HTTP-Header `Expires:` und `Cache-Control:`, wenn sie vom Upstream-Server gesendet werden. Dies kann beispielsweise mit dem Apache-Modul [mod_ expires](https://httpd.apache.org/docs/2.4/mod/mod_expires.html) erreicht werden.

1. Manuelle Invalidierung\
   Durch CDNs können Ressourcen über Webschnittstellen aus dem Cache entfernt werden.
1. API-basierte Invalidierung\
   Die meisten CDNs bieten außerdem eine REST- und/oder eine SOAP-API, mit der Ressourcen aus dem Cache entfernt werden können.

In einem typischen AEM-Setup bietet die Konfiguration per Erweiterung und/oder Pfad, die durch die Punkte 1 und 2 erreicht werden kann, Möglichkeiten zum Festlegen angemessener Caching-Zeiträume für häufig verwendete Ressourcen, die sich nur selten ändern, z. B. Design-Grafiken und Client-Bibliotheken. Wenn neue Versionen bereitgestellt werden, ist in der Regel eine manuelle Invalidierung erforderlich.

Wenn verwaltete Inhalte mit diesem Verfahren zwischengespeichert werden, bedeutet dies, dass Änderungen am Inhalt für Endbenutzende erst sichtbar sind, wenn der konfigurierte Caching-Zeitraum abgelaufen ist und das Dokument erneut vom Dispatcher abgerufen wird.

Für eine präzisere Steuerung können Sie mit der API-basierten Invalidierung den Cache eines CDN invalidieren, wenn der Dispatcher-Cache invalidiert wird. Auf Grundlage der API des CDN können Sie selbst [ContentBuilder](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/replication/ContentBuilder.html) und [TransportHandler](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/replication/TransportHandler.html) implementieren (wenn die API nicht REST-basiert ist) und einen Replikationsagenten einrichten, der den Cache des CDN mithilfe dieser Elemente invalidiert.

>[!NOTE]
>
>Siehe auch [AEM (CQ) Dispatcher-Sicherheit und CDN+Browser-Caching](https://www.slideshare.net/andrewmkhoury/dispatcher-caching-aemgemspart2jan2015) und die aufgezeichnete Präsentation zu [Dispatcher-Caching](https://experienceleague.adobe.com/docs/experience-manager-gems-events/gems/gems2015/aem-dispatcher-caching-new-features-and-optimizations.html?lang=de).

## Verwenden eines Dispatchers mit einem Author-Server {#using-a-dispatcher-with-an-author-server}

>[!CAUTION]
>
>Wenn Sie [AEM mit der Touch-optimierten Benutzeroberfläche](https://experienceleague.adobe.com/docs/experience-manager-65/developing/introduction/touch-ui-concepts.html?lang=de) verwenden, speichern Sie den Inhalt der Autoreninstanz **nicht** zwischen. Wenn die Zwischenspeicherung für die Autoreninstanz aktiviert wurde, müssen Sie sie deaktivieren und den Inhalt des Cache-Verzeichnisses löschen. Um die Zwischenspeicherung zu deaktivieren, bearbeiten Sie die Datei `author_dispatcher.any` und ändern Sie die Eigenschaft `/rule` des Abschnitts `/cache` wie folgt:

```xml
/rules
{
/0000
{ /type "deny" /glob "*"}
}
```

Ein Dispatcher kann vor einer Autoreninstanz verwendet werden, um die Authoring-Leistung zu verbessern. Um einen Authoring-Dispatcher zu konfigurieren, führen Sie die folgenden Schritte aus:

1. Installieren Sie einen Dispatcher auf einem Webserver. (Dabei kann es sich um einen Apache- oder IIS-Webserver handeln, siehe [Installieren des Dispatchers](dispatcher-install.md).)
1. Testen Sie den neu installierten Dispatcher mit einer funktionierenden AEM-Veröffentlichungsinstanz. Dadurch wird sichergestellt, dass eine grundsätzlich korrekte Installation durchgeführt wurde.
1. Stellen Sie dann sicher, dass der Dispatcher über TCP/IP eine Verbindung zur Autoreninstanz herstellen kann.
1. Ersetzen Sie die Beispieldatei `dispatcher.any` durch die Datei `author_dispatcher.any`, die beim [Dispatcher-Download](release-notes.md#downloads) bereitgestellt wird.
1. Öffnen Sie `author_dispatcher.any` in einem Texteditor und nehmen Sie die folgenden Änderungen vor:

   1. Ändern Sie `/hostname` und `/port` im Abschnitt `/renders` so, dass auf Ihre Autoreninstanz verwiesen wird.
   1. Ändern Sie `/docroot` im Abschnitt `/cache` so, dass auf ein Cache-Verzeichnis verwiesen wird. Falls Sie [AEM mit der Touch-optimierten UI](https://experienceleague.adobe.com/docs/experience-manager-65/developing/introduction/touch-ui-concepts.html?lang=de) verwenden, beachten Sie die Warnung oben.
   1. Speichern Sie die Änderungen.

1. Löschen Sie alle vorhandenen Dateien im Verzeichnis `/cache` > `/docroot`, das oben konfiguriert wurde.
1. Starten Sie den Webserver neu.

>[!NOTE]
>
>Mit der zur Verfügung gestellten`author_dispatcher.any`-Konfiguration müssen Sie beim Installieren eines CQ5 Feature Pack, Hotfix oder Anwendungs-Code-Pakets, das sich auf Inhalte unter `/libs` oder `/apps` auswirkt, die zwischengespeicherten Dateien unter diesen Verzeichnissen im Dispatcher-Cache löschen. Dadurch wird sichergestellt, dass bei der nächsten Anfrage die neu aktualisierten Dateien abgerufen werden und nicht die alten zwischengespeicherten Dateien.

>[!CAUTION]
>
>Wenn Sie den vorher konfigurierten Autoren-Dispatcher verwendet und einen *Dispatcher-Flushing-Agenten* aktiviert haben, führen Sie folgende Schritte aus:

1. Löschen oder deaktivieren Sie den Flushing-Agenten des **Autoren-Dispatchers** in Ihrer AEM-Autoreninstanz.
1. Wiederholen Sie die Konfiguration des Autoren-Dispatchers, indem Sie den neuen Anweisungen oben folgen.

<!--
[Author Dispatcher configuration file (Dispatcher 4.1.2 or later)](assets/author_dispatchernew.any)
-->
<!--[!NOTE]
>
>A related knowledge base article can be found here:  
>[How to configure the dispatcher in front of an authoring environment](https://helpx.adobe.com/cq/kb/HowToConfigureDispatcherForAuthoringEnvironment.html)
-->
