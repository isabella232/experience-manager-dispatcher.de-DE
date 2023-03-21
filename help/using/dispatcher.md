---
title: Dispatcher-Übersicht
seo-title: Adobe AEM Dispatcher Overview
description: Erfahren Sie, wie Sie den Dispatcher für verbesserte Sicherheit, Zwischenspeicherung und mehr zu AEM Cloud Services verwenden.
seo-description: This article provides a general overview of Adobe Experience Manager Dispatcher.
uuid: 71766f86-5e91-446b-a078-061b179d090d
pageversionid: 1193211344162
topic-tags: dispatcher
content-type: reference
discoiquuid: 1d449ee2-4cdd-4b7a-8b4e-7e6fc0a1d7ee
exl-id: c9266683-6890-4359-96db-054b7e856dd0
source-git-commit: 7dd2ba37e149af960ba428421d64a5a24542eeeb
workflow-type: tm+mt
source-wordcount: '3154'
ht-degree: 17%

---

# Dispatcher-Übersicht  {#dispatcher-overview}

>[!NOTE]
>
>Dispatcher-Versionen sind AEM unabhängig. Sie wurden möglicherweise zu dieser Seite umgeleitet, wenn Sie einen Link zur Dispatcher-Dokumentation befolgt haben, die in die Dokumentation für eine frühere Version von AEM eingebettet ist.

Der Dispatcher ist das Tool zum Zwischenspeichern und Lastenausgleich von Adobe Experience Manager, das mit einem Webserver der Unternehmensklasse verwendet wird.

Der Prozess für die Bereitstellung des Dispatchers ist unabhängig vom Webserver und der gewählten Betriebssystemplattform:

1. Weitere Informationen zum Dispatcher (diese Seite). Siehe auch [Häufig gestellte Fragen zum Dispatcher](/help/using/dispatcher-faq.md).
1. Installieren Sie eine [unterstützter Webserver](https://experienceleague.adobe.com/docs/experience-manager-65/deploying/introduction/technical-requirements.html?lang=en) entsprechend der Webserver-Dokumentation.
1. [Installieren Sie das Dispatcher-Modul](dispatcher-install.md) auf Ihrem Webserver und konfigurieren Sie den Webserver dementsprechend.
1. [Konfigurieren Sie den Dispatcher](dispatcher-configuration.md) (Datei „dispatcher.any“).
1. [Konfigurieren Sie AEM](page-invalidate.md), sodass der Cache durch Inhaltsaktualisierungen invalidiert wird.

>[!NOTE]
>
>So erhalten Sie ein besseres Verständnis darüber, wie der Dispatcher mit AEM funktioniert:
>
>* Siehe [Fragen an die AEM Community-Experten für Juli 2017](https://communities.adobeconnect.com/pf0gem7igw1f/).
>* Zugriff [dieses Repository](https://github.com/adobe/aem-dispatcher-experiments). Es enthält eine Sammlung von Experimenten im Laborformat &quot;Take-home&quot;.



Ziehen Sie bei Bedarf die folgenden Ressourcen hinzu:

* [Die Dispatcher-Sicherheitscheckliste](security-checklist.md)
* [Dispatcher-Wissensdatenbank](https://helpx.adobe.com/experience-manager/kb/index/dispatcher.html)
* [Optimierung von Websites für die Cache-Leistung](https://experienceleague.adobe.com/docs/experience-manager-65/deploying/configuring/configuring-performance.html)
* [Verwenden des Dispatchers mit mehreren Domänen](dispatcher-domains.md)
* [Verwenden von SSL mit dem Dispatcher](dispatcher-ssl.md)
* [Implementieren der Zwischenspeicherung unter Berücksichtigung von Berechtigungen](permissions-cache.md)
* [Beheben von Problemen beim Dispatcher](dispatcher-troubleshooting.md)
* [Häufig gestellte Fragen zu Dispatcher - Häufige Probleme](dispatcher-faq.md)

>[!NOTE]
>
>******Dispatcher wird am häufigsten zum Zwischenspeichern von Antworten aus einer AEM-Veröffentlichungsinstanz** verwendet, um die Reaktionsfähigkeit und Sicherheit einer öffentlich zugänglichen veröffentlichten Website zu erhöhen. Die meisten Diskussionen konzentrieren sich auf diesen Fall.
>
>Der Dispatcher kann jedoch auch verwendet werden, um die Reaktionsfähigkeit Ihrer **Autoreninstanz**, insbesondere wenn Sie eine große Anzahl von Benutzern haben, die Ihre Website bearbeiten und aktualisieren. Weitere Informationen zu diesem Fall finden Sie unter [Verwenden eines Dispatchers mit einem Autorenserver](#using-a-dispatcher-with-an-author-server), unten.

## Gründe für die Verwendung des Dispatchers zum Implementieren der Zwischenspeicherung  {#why-use-dispatcher-to-implement-caching}

Es gibt zwei grundlegende Ansätze für die Web-Veröffentlichung:

* **Statische Webserver**: wie Apache oder IIS sind einfach, aber schnell.
* **Inhaltsverwaltungsserver**: die dynamische, in Echtzeit und intelligent Inhalte bereitstellen, aber viel mehr Berechnungszeit und andere Ressourcen erfordern.

Der Dispatcher hilft bei der Realisierung einer Umgebung, die sowohl schnell als auch dynamisch ist. Es funktioniert als Teil eines statischen HTML-Servers, wie z. B. Apache, mit dem Ziel,

* Speichern (oder &quot;Zwischenspeichern&quot;) eines möglichst großen Teils des Site-Inhalts in Form einer statischen Website
* Zugriff auf die Layout-Engine so wenig wie möglich.

Das bedeutet:

* **statische Inhalte** wird mit derselben Geschwindigkeit und Leichtigkeit wie auf einem statischen Webserver verarbeitet. Außerdem können Sie die Verwaltungs- und Sicherheitstools verwenden, die für Ihre statischen Webserver verfügbar sind.

* **Dynamische Inhalte** werden nach Bedarf generiert, ohne das System mehr als absolut notwendig zu verlangsamen.

Der Dispatcher verfügt über Mechanismen zum Generieren und Aktualisieren statischer HTML basierend auf dem Inhalt der dynamischen Site. Sie können detailliert angeben, welche Dokumente als statische Dateien gespeichert und welche immer dynamisch generiert werden.

In diesem Abschnitt werden die Grundlagen dieses Prozesses erläutert.

### Statischer Webserver  {#static-web-server}

![](assets/chlimage_1-3.png)

Ein statischer Webserver, z. B. Apache oder IIS, stellt statische HTML-Dateien für Besucher Ihrer Website bereit. Statische Seiten werden einmal erstellt, sodass für jede Anforderung derselbe Inhalt bereitgestellt wird.

Dieser Prozess ist einfach und effizient. Wenn ein Besucher eine Datei wie eine HTML-Seite anfordert, wird die Datei direkt aus dem Speicher übernommen. Im schlimmsten Fall wird es von der lokalen Festplatte gelesen. Statische Webserver sind schon seit geraumer Zeit verfügbar, sodass es eine breite Palette von Tools für Verwaltung und Sicherheitsmanagement gibt und sie gut in Netzwerkinfrastrukturen integriert sind.

### Inhaltsverwaltungsserver  {#content-management-servers}

![](assets/chlimage_1-4.png)

Wenn Sie ein CMS (Content Management Server) wie AEM verwenden, verarbeitet eine erweiterte Layout-Engine die Anforderung eines Besuchers. Die Engine liest Inhalte aus einem Repository, das in Kombination mit Stilen, Formaten und Zugriffsrechten den Inhalt in ein Dokument umwandelt, das auf die Bedürfnisse und Rechte eines Besuchers zugeschnitten ist.

Dieser Workflow ermöglicht die Erstellung umfassenderer, dynamischer Inhalte, wodurch die Flexibilität und Funktionalität Ihrer Website gesteigert wird. Die Layout-Engine benötigt jedoch mehr Verarbeitungsleistung als ein statischer Server. Daher kann dieses Setup zu einer Verlangsamung führen, wenn viele Besucher das System verwenden.

## Zwischenspeicherung durch den Dispatcher  {#how-dispatcher-performs-caching}

![](assets/chlimage_1-5.png)

**Das Cache-Verzeichnis** Für die Zwischenspeicherung nutzt das Dispatcher-Modul die Fähigkeit des Webservers, statische Inhalte bereitzustellen. Der Dispatcher legt die zwischengespeicherten Dokumente im Basisverzeichnis des Webservers ab.

>[!NOTE]
>
>Wenn die Konfiguration für das Zwischenspeichern von HTTP-Headern fehlt, speichert der Dispatcher nur den HTML-Code der Seite. Die HTTP-Header werden nicht gespeichert. Dieses Szenario kann bei der Verwendung verschiedener Kodierungen auf Ihrer Website problematisch sein, da diese Seiten möglicherweise verloren gehen. Informationen zum Aktivieren der HTTP-Header-Zwischenspeicherung finden Sie unter [Konfigurieren des Dispatcher-Cache.](https://experienceleague.adobe.com/docs/experience-manager-dispatcher/using/configuring/dispatcher-configuration.html?lang=de)

>[!NOTE]
>
>Das Suchen des Dokumentenstamms Ihres Webservers auf NAS-Speichern führt zu einer verringerten Leistung. Wenn ein Dokumentenstamm auf NAS von mehreren Webservern gemeinsam genutzt wird, kann es bei Replikationsaktionen zu zeitweiligen Sperren kommen.

>[!NOTE]
>
>Der Dispatcher speichert das zwischengespeicherte Dokument in einer Struktur, die der angeforderten URL entspricht.
>
>Für die Länge des Dateinamens kann es Einschränkungen auf Betriebssystemebene geben. Das heißt, wenn Sie eine URL mit zahlreichen Selektoren haben.

### Methoden für die Zwischenspeicherung 

Der Dispatcher verwendet zwei Hauptverfahren zum Aktualisieren des zwischengespeicherten Inhalts, wenn Änderungen an der Website vorgenommen werden.

* **Inhaltsaktualisierungen** die geänderten Seiten und die Dateien entfernen, die ihnen direkt zugeordnet sind.
* **Automatische Invalidierung** macht automatisch jene Teile des Caches ungültig, die nach einer Aktualisierung möglicherweise veraltet sind. Das heißt, dass relevante Seiten effektiv als veraltet markiert werden, ohne dass irgendetwas gelöscht wird.

### Inhaltsaktualisierungen

Bei einer Inhaltsaktualisierung ändern sich ein oder mehrere AEM Dokumente. AEM sendet eine Syndizierungsanforderung an den Dispatcher, der den Cache entsprechend aktualisiert:

1. Löscht die geänderten Dateien aus dem Cache.
1. Löscht alle Dateien, die mit demselben Handle beginnen, aus dem Cache. Wenn beispielsweise die Datei /en/index.html aktualisiert wird, beginnen alle Dateien mit /en/index. werden gelöscht. Mit diesem Mechanismus können Sie Cache-effiziente Sites erstellen, insbesondere über die Bildnavigation.
1. Es *Touch* der so genannten **statfile**, der den Zeitstempel der Statfile aktualisiert, um das Datum der letzten Änderung anzugeben.

Folgende Punkte sind zu beachten:

* Inhaltsaktualisierungen werden in der Regel mit einem Authoring-System verwendet, das &quot;weiß&quot;, was ersetzt werden muss.
* Dateien, die von einer Inhaltsaktualisierung betroffen sind, werden entfernt, aber nicht sofort ersetzt. Wenn eine solche Datei das nächste Mal angefordert wird, ruft der Dispatcher die neue Datei aus der AEM-Instanz ab und legt sie im Cache ab, wobei der alte Inhalt überschrieben wird.
* In der Regel werden automatisch generierte Bilder, die Text aus einer Seite enthalten, in Bilddateien gespeichert, die mit demselben Handle beginnen. Dadurch wird sichergestellt, dass die Zuordnung zum Löschen vorhanden ist. Beispielsweise können Sie den Titeltext der Seite mypage.html als Bild mypage.titlePicture.gif im selben Ordner speichern. Dadurch wird das Bild bei jeder Aktualisierung der Seite automatisch aus dem Cache gelöscht, sodass Sie sicher sein können, dass das Bild immer die aktuelle Version der Seite widerspiegelt.
* Sie können mehrere Statfiles haben, z. B. eine pro Sprachordner. Wenn eine Seite aktualisiert wird, sucht AEM den nächsten übergeordneten Ordner, der eine Statfile enthält, und *ändert*  diese Datei.

### Automatische Invalidierung

Durch die automatische Invalidierung werden Teile des Caches automatisch invalidiert, ohne dass Dateien physisch gelöscht werden. Bei jeder Inhaltsaktualisierung wird die so genannte Statfile geändert, sodass ihr Zeitstempel die letzte Inhaltsaktualisierung widerspiegelt.

Der Dispatcher verfügt über eine Liste von Dateien, die der automatischen Invalidierung unterliegen. Wenn ein Dokument aus dieser Liste angefordert wird, vergleicht der Dispatcher das Datum des zwischengespeicherten Dokuments mit dem Zeitstempel der Statfile:

* Wenn das zwischengespeicherte Dokument neuer ist, gibt der Dispatcher es zurück.
* Wenn sie älter ist, ruft der Dispatcher die aktuelle Version von der AEM-Instanz ab.

Auch hier sollten bestimmte Punkte beachtet werden:

* Die automatische Invalidierung wird normalerweise verwendet, wenn die Beziehungen komplex sind, z. B. HTML-Seiten. Diese Seiten enthalten Links und Navigationseinträge, sodass sie normalerweise nach einer Inhaltsaktualisierung aktualisiert werden müssen. Wenn Sie automatisch PDF- oder Bilddateien generiert haben, können Sie auch diese Dateien automatisch invalidieren lassen.
* Die automatische Invalidierung erfordert zum Zeitpunkt der Aktualisierung keine Aktion des Dispatchers, außer dass die Statfile bearbeitet wird. Wenn Sie jedoch die Statfile ändern, wird der Cache-Inhalt automatisch veraltet, ohne ihn physisch aus dem Cache zu entfernen.

## Zurückgeben von Dokumenten durch den Dispatcher  {#how-dispatcher-returns-documents}

![](assets/chlimage_1-6.png)

### Festlegen, ob ein Dokument zwischengespeichert werden soll

Sie können [definieren, welche Dokumente der Dispatcher in der Konfigurationsdatei zwischenspeichert](https://experienceleague.adobe.com/docs/experience-manager-dispatcher/using/configuring/dispatcher-configuration.html?lang=de). Der Dispatcher überprüft die Anforderung anhand der Liste der zwischenspeicherbaren Dokumente. Wenn das Dokument nicht in dieser Liste enthalten ist, fordert der Dispatcher das Dokument von der AEM-Instanz an.

Der Dispatcher ruft das Dokument in den folgenden Fällen immer direkt aus der AEM-Instanz ab:

* Der Anfrage-URI enthält ein Fragezeichen &quot;`?`&quot;. Dieses Szenario zeigt normalerweise eine dynamische Seite an, z. B. ein Suchergebnis, die nicht zwischengespeichert werden muss.
* Die Dateierweiterung fehlt. Der Webserver benötigt die Erweiterung, um den Dokumenttyp (den MIME-Typ) zu bestimmen.
* Der Authentifizierungs-Header ist festgelegt (konfigurierbar).

>[!NOTE]
>
>Die Methoden GET oder HEAD (für den HTTP-Header) können vom Dispatcher zwischengespeichert werden. Weitere Informationen zum Zwischenspeichern von Antwortheadern finden Sie im Abschnitt [Zwischenspeichern von HTTP-Antwortheadern](https://experienceleague.adobe.com/docs/experience-manager-dispatcher/using/configuring/dispatcher-configuration.html?lang=de).

### Bestimmen, ob ein Dokument zwischengespeichert wird 

Der Dispatcher speichert die zwischengespeicherten Dateien auf dem Webserver so, als wären sie Teil einer statischen Website. Wenn ein Benutzer ein zwischenspeicherbares Dokument anfordert, prüft der Dispatcher, ob dieses Dokument im Dateisystem des Webservers vorhanden ist:

* Wenn das Dokument zwischengespeichert ist, gibt der Dispatcher die Datei zurück.
* Wenn es nicht zwischengespeichert wird, fordert der Dispatcher das Dokument von der AEM-Instanz an.

### Bestimmen, ob ein Dokument aktuell ist

Um herauszufinden, ob ein Dokument aktuell ist, führt der Dispatcher zwei Schritte aus:

1. Es wird geprüft, ob für das Dokument die automatische Invalidierung ausgeführt wird. Ist dies nicht der Fall, wird das Dokument als aktuell betrachtet.
1. Wenn das Dokument für die automatische Invalidierung konfiguriert ist, prüft der Dispatcher, ob es älter oder neuer als die letzte verfügbare Änderung ist. Wenn sie älter ist, fordert der Dispatcher die aktuelle Version von der AEM-Instanz an und ersetzt die Version im Cache.

>[!NOTE]
>
>Dokumente ohne **automatische Invalidierung** bleiben im Cache, bis sie physisch gelöscht werden. Beispielsweise durch eine Inhaltsaktualisierung auf der Website.

## Vorteile des Lastenausgleichs {#the-benefits-of-load-balancing}

Lastenausgleich ist die Praxis, die Rechenlast der Website auf mehrere Instanzen von AEM zu verteilen.

![](assets/chlimage_1-7.png)

Sie gewinnen:

* **erhöhte Verarbeitungsleistung**
In der Praxis bedeutet eine höhere Verarbeitungsleistung, dass der Dispatcher Dokumentanforderungen zwischen mehreren Instanzen von AEM teilt. Da nun jede Instanz weniger Dokumente zu verarbeiten hat, sind die Reaktionszeiten kürzer. Der Dispatcher führt interne Statistiken für jede Dokumentenkategorie, sodass die Anforderungen geschätzt und die Abfragen effizient aufgeteilt werden können.

* **Verbesserte Fail-Safe-Abdeckung**
Wenn der Dispatcher keine Antworten von einer Instanz erhält, werden Anforderungen automatisch an eine der anderen Instanzen weitergeleitet. Wenn eine Instanz nicht mehr verfügbar ist, ist die einzige Auswirkung eine Verlangsamung der Site, proportional zur verlorenen Rechenleistung. Alle Dienste werden jedoch fortgesetzt.

* Sie können auch verschiedene Websites auf demselben statischen Webserver verwalten.

>[!NOTE]
>
>Während der Lastenausgleich die Last effizient verteilt, hilft das Caching, die Last zu reduzieren. Versuchen Sie daher, die Zwischenspeicherung zu optimieren und die Gesamtlast zu reduzieren, bevor Sie den Lastenausgleich einrichten. Eine gute Zwischenspeicherung kann die Leistung des Lastenausgleichs erhöhen oder den Lastenausgleich überflüssig machen.

>[!CAUTION]
>
>Während ein einzelner Dispatcher die Kapazität der verfügbaren Veröffentlichungsinstanzen überlasten kann, kann es bei einigen seltenen Anwendungen sinnvoll sein, auch die Last zwischen zwei Dispatcher-Instanzen auszugleichen. Konfigurationen mit mehreren Dispatchern müssen sorgfältig geprüft werden, da ein zusätzlicher Dispatcher die Last auf den verfügbaren Veröffentlichungsinstanzen erhöhen und die Leistung in den meisten Anwendungen leicht verringern kann.

## Durchführen des Lastenausgleichs durch den Dispatcher  {#how-the-dispatcher-performs-load-balancing}

### Leistungsstatistiken

Der Dispatcher führt interne Statistiken darüber, wie schnell jede Instanz von AEM Dokumente verarbeitet. Basierend auf diesen Daten schätzt der Dispatcher, welche Instanz die schnellste Reaktionszeit bei der Beantwortung einer Anfrage bereitstellen kann, und reserviert so die erforderliche Berechnungszeit für diese Instanz.

Verschiedene Anforderungstypen können unterschiedliche durchschnittliche Fertigstellungszeiten aufweisen. Daher können Sie vom Dispatcher Dokumentkategorien angeben. Diese Kategorien werden dann bei der Berechnung der Zeitschätzungen berücksichtigt. Sie können beispielsweise zwischen HTML-Seiten und Bildern unterscheiden, da die typischen Antwortzeiten sehr unterschiedlich sein können.

Wenn Sie eine umfangreiche Suchfunktion verwenden, können Sie eine Kategorie für Suchabfragen erstellen. Diese Methode hilft dem Dispatcher dabei, Suchanfragen an die Instanz zu senden, die am schnellsten antwortet. Außerdem wird dadurch verhindert, dass eine langsamere Instanz angehalten wird, wenn sie mehrere &quot;teure&quot;Suchanfragen erhält, während die anderen die &quot;billigeren&quot;Anforderungen erhalten.

### Personalisierte Inhalte („Sticky-Verbindungen“) 

Sticky-Verbindungen stellen sicher, dass Dokumente für einen Benutzer alle auf derselben Instanz von AEM erstellt werden. Dieser Punkt ist wichtig, wenn Sie personalisierte Seiten und Sitzungsdaten verwenden. Die Daten werden in der Instanz gespeichert, sodass nachfolgende Anforderungen desselben Benutzers an diese Instanz zurückgegeben werden müssen oder die Daten verloren gehen.

Da durch Sticky-Verbindungen die Fähigkeit des Dispatchers eingeschränkt wird, die Anforderungen zu optimieren, sollten Sie sie nur bei Bedarf verwenden. Sie können den Ordner angeben, der die &quot;Sticky&quot;-Dokumente enthält, und so sicherstellen, dass alle Dokumente in diesem Ordner für jeden Benutzer auf derselben Instanz erstellt werden.

>[!NOTE]
>
>Bei den meisten Seiten, die Sticky-Verbindungen verwenden, müssen Sie die Zwischenspeicherung deaktivieren. Andernfalls sieht die Seite für alle Benutzer gleich aus, unabhängig vom Sitzungsinhalt.
>
>Für *some* Anwendungen können sowohl Sticky-Verbindungen als auch Caching verwendet werden; z. B. wenn Sie ein Formular anzeigen, das Daten in die Sitzung schreibt.

## Verwenden mehrerer Dispatcher  {#using-multiple-dispatchers}

Bei komplexen Setups können Sie mehrere Dispatcher verwenden. Sie können beispielsweise Folgendes verwenden:

* einen Dispatcher zum Veröffentlichen einer Website im Intranet
* einen zweiten Dispatcher, der unter einer anderen Adresse und mit unterschiedlichen Sicherheitseinstellungen denselben Inhalt im Internet veröffentlicht.

Stellen Sie in diesem Fall sicher, dass jede Anfrage nur einen Dispatcher durchläuft. Ein Dispatcher verarbeitet keine Anforderungen, die von einem anderen Dispatcher stammen. Stellen Sie daher sicher, dass beide Dispatcher direkt auf die AEM Website zugreifen.

## Verwenden des Dispatchers mit einem CDN  {#using-dispatcher-with-a-cdn}

Ein CDN (Content Delivery Network) wie Akamai Edge Delivery oder Amazon Cloud Front stellt Inhalte von einem Speicherort in der Nähe des Endbenutzers bereit. Dies hat folgende Vorteile:

* Verkürzung der Reaktionszeiten für Endbenutzer
* Verringern der Auslastung Ihrer Server

Als HTTP-Infrastrukturkomponente funktioniert ein CDN ähnlich wie der Dispatcher. Wenn ein CDN-Knoten eine Anforderung erhält, wird die Anforderung nach Möglichkeit aus dem Cache bereitgestellt (die Ressource ist im Cache verfügbar und gültig). Andernfalls gelangt er zum nächstgelegenen Server, um die Ressource abzurufen und sie gegebenenfalls für weitere Anfragen zwischenzuspeichern.

Der &quot;nächstgelegene Server&quot;hängt von Ihrem spezifischen Setup ab. In einem Akamai-Setup kann die Abfrage z. B. den folgenden Pfad durchlaufen:

* Akamai Edge-Knoten
* Akamai Midgres-Ebene
* Ihre Firewall
* Ihr Lastenausgleich
* Dispatcher
* AEM

Normalerweise ist der Dispatcher der nächste Server, der das Dokument aus einem Cache bereitstellt und die an den CDN-Server zurückgegebenen Antwortheader beeinflusst.

## Steuern eines CDN-Caches  {#controlling-a-cdn-cache}

Es gibt mehrere Möglichkeiten, zu steuern, wie lange ein CDN eine Ressource zwischenspeichert, bevor sie erneut vom Dispatcher abgerufen wird.

1. Explizite Konfiguration\
   Konfigurieren Sie, wie lange bestimmte Ressourcen im Cache des CDN gespeichert werden, je nach MIME-Typ, Erweiterung, Anfragetyp usw.

1. Ablauf- und Cache-Steuerelemente\
   Die meisten CDNs berücksichtigen `Expires:` und `Cache-Control:` HTTP-Header, wenn vom Upstream-Server gesendet. Diese Methode kann beispielsweise mithilfe der [mod_expires](https://httpd.apache.org/docs/2.4/mod/mod_expires.html) Apache-Modul.

1. Manuelle Invalidierung\
   Durch CDNs können Ressourcen über Webschnittstellen aus dem Cache entfernt werden.
1. API-basierte Invalidierung\
   Die meisten CDNs bieten außerdem eine REST- und/oder eine SOAP-API, mit der Ressourcen aus dem Cache entfernt werden können.

In einer typischen AEM-Einrichtung bietet die Konfiguration durch Erweiterung, Pfad oder beides - was über die Punkte 1 und 2 erreicht werden kann - Möglichkeiten, angemessene Caching-Zeiträume für häufig verwendete Ressourcen festzulegen, die sich häufig nicht ändern, wie z. B. Design-Bilder und Client-Bibliotheken. Wenn neue Versionen bereitgestellt werden, ist normalerweise eine manuelle Invalidierung erforderlich.

Wenn dieser Ansatz zum Zwischenspeichern verwalteter Inhalte verwendet wird, bedeutet dies, dass Inhaltsänderungen nur für Endbenutzer sichtbar sind, wenn der konfigurierte Zwischenspeicherungszeitraum abgelaufen ist und das Dokument erneut vom Dispatcher abgerufen wird.

Für eine präzisere Kontrolle können Sie mit der API-basierten Invalidierung den Cache eines CDN invalidieren, wenn der Dispatcher-Cache ungültig gemacht wird. Basierend auf der CDNs-API können Sie Ihre [ContentBuilder](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/replication/ContentBuilder.html) und [TransportHandler](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/replication/TransportHandler.html) (wenn die API nicht auf REST basiert) und richten Sie einen Replikationsagenten ein, der diese Teile verwendet, um den Cache des CDN zu invalidieren.

>[!NOTE]
>
>Siehe auch [AEM (CQ) Dispatcher Security und CDN+Browser Caching](https://www.slideshare.net/andrewmkhoury/dispatcher-caching-aemgemspart2jan2015) und aufgezeichnete Darstellung [Dispatcher-Caching](https://experienceleague.adobe.com/docs/experience-manager-gems-events/gems/gems2015/aem-dispatcher-caching-new-features-and-optimizations.html?lang=en).

## Using a Dispatcher with an Author Server {#using-a-dispatcher-with-an-author-server}

>[!CAUTION]
>
>Wenn Sie [AEM mit Touch-Benutzeroberfläche](https://experienceleague.adobe.com/docs/experience-manager-65/developing/introduction/touch-ui-concepts.html?lang=en), do **not** Inhalt der Autoreninstanz zwischenspeichern. Wenn die Zwischenspeicherung für die Autoreninstanz aktiviert wurde, müssen Sie sie deaktivieren und den Inhalt des Cache-Ordners löschen. Um die Zwischenspeicherung zu deaktivieren, bearbeiten Sie die `author_dispatcher.any` und ändern Sie die `/rule` -Eigenschaft der `/cache` wie folgt:

```xml
/rules
{
/0000
{ /type "deny" /glob "*"}
}
```

Ein Dispatcher kann vor einer Autoreninstanz verwendet werden, um die Authoring-Leistung zu verbessern. Gehen Sie wie folgt vor, um einen Authoring-Dispatcher zu konfigurieren:

1. Installieren Sie einen Dispatcher auf einem Webserver (einem Apache- oder IIS-Webserver, siehe [Installieren des Dispatchers](dispatcher-install.md)).
1. Testen Sie den neu installierten Dispatcher mit einer funktionierenden AEM Veröffentlichungsinstanz. Dadurch wird sichergestellt, dass eine Grundlinie korrekte Installation erreicht wurde.
1. Stellen Sie nun sicher, dass der Dispatcher über TCP/IP eine Verbindung zu Ihrer Autoreninstanz herstellen kann.
1. Beispiel ersetzen `dispatcher.any` -Datei mit `author_dispatcher.any` -Datei, die mit dem [Dispatcher-Download](release-notes.md#downloads).
1. Öffnen Sie `author_dispatcher.any` in einem Texteditor und nehmen Sie die folgenden Änderungen vor:

   1. Ändern Sie die `/hostname` und `/port` des `/renders` -Abschnitt, damit sie auf Ihre Autoreninstanz verweisen.
   1. Ändern Sie die `/docroot` des `/cache` -Abschnitt, damit sie auf ein Cache-Verzeichnis verweisen. In diesem Fall verwenden Sie [AEM mit Touch-Benutzeroberfläche](https://experienceleague.adobe.com/docs/experience-manager-65/developing/introduction/touch-ui-concepts.html?lang=en), siehe die Warnung oben.
   1. Speichern Sie die Änderungen.

1. Löschen Sie alle vorhandenen Dateien im Verzeichnis `/cache` > `/docroot`, das oben konfiguriert wurde.
1. Starten Sie den Webserver neu.

>[!NOTE]
>
>Mit den bereitgestellten `author_dispatcher.any` Konfiguration, wenn Sie ein CQ5-Feature Pack, Hotfix oder Anwendungs-Code-Paket installieren, das sich auf alle Inhalte unter `/libs` oder `/apps`müssen Sie die zwischengespeicherten Dateien unter diesen Ordnern im Dispatcher-Cache löschen. Dadurch wird sichergestellt, dass beim nächsten Anfordern die neu aktualisierten Dateien abgerufen werden und nicht die alten zwischengespeicherten Dateien.

>[!CAUTION]
>
>Wenn Sie den zuvor konfigurierten Autor-Dispatcher verwendet und eine *Dispatcher-Flushing-Agent* führen Sie folgende Schritte aus:

1. Löschen oder deaktivieren Sie die **Autoren-Dispatcher** Flushing-Agent in Ihrer AEM Autoreninstanz.
1. Wiederholen Sie die Konfiguration des Autoren-Dispatchers, indem Sie die neuen Anweisungen oben befolgen.

<!--
[Author Dispatcher configuration file (Dispatcher 4.1.2 or later)](assets/author_dispatchernew.any)
-->
<!--[!NOTE]
>
>A related knowledge base article can be found here:  
>[How to configure the dispatcher in front of an authoring environment](https://helpx.adobe.com/cq/kb/HowToConfigureDispatcherForAuthoringEnvironment.html)
-->
