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
source-git-commit: e87af532ee3268f0a45679e20031c3febc02de58
workflow-type: tm+mt
source-wordcount: '3165'
ht-degree: 54%

---

# Dispatcher-Übersicht  {#dispatcher-overview}

>[!NOTE]
>
>Dispatcher-Versionen sind unabhängig von AEM. Möglicherweise wurden Sie von der Dokumentation zu einer früheren AEM-Version zu dieser Seite weitergeleitet.

Der Dispatcher ist das Tool zum Zwischenspeichern und Lastenausgleich von Adobe Experience Manager, das mit einem Webserver der Unternehmensklasse verwendet wird.

Der Prozess für die Bereitstellung des Dispatchers ist unabhängig vom Webserver und der gewählten Betriebssystemplattform:

1. Weitere Informationen zum Dispatcher (diese Seite). Siehe auch [Häufig gestellte Fragen zum Dispatcher](https://experienceleague.adobe.com/docs/experience-manager-dispatcher/using/troubleshooting/dispatcher-faq.html?lang=en).
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
* [Die Dispatcher-Knowledge-Base](https://helpx.adobe.com/experience-manager/kb/index/dispatcher.html)
* [Optimierung von Websites für die Cache-Leistung](https://experienceleague.adobe.com/docs/experience-manager-64/deploying/configuring/configuring-performance.html?lang=en)
* [Verwenden des Dispatchers mit mehreren Domänen](dispatcher-domains.md)
* [Verwenden von SSL mit dem Dispatcher](dispatcher-ssl.md)
* [Implementieren der Zwischenspeicherung unter Berücksichtigung von Berechtigungen](permissions-cache.md)
* [Beheben von Problemen beim Dispatcher](dispatcher-troubleshooting.md)
* [Häufig gestellte Fragen zu Dispatcher - Häufige Probleme](dispatcher-faq.md)

>[!NOTE]
>
>******Dispatcher wird am häufigsten zum Zwischenspeichern von Antworten aus einer AEM-Veröffentlichungsinstanz** verwendet, um die Reaktionsfähigkeit und Sicherheit einer öffentlich zugänglichen veröffentlichten Website zu erhöhen. Diese Anwendung steht im Mittelpunkt des Themas.
>
>Mit dem Dispatcher kann jedoch auch die Reaktionsgeschwindigkeit der **Autoreninstanz** gesteigert werden, besonders wenn viele Benutzer Ihre Website bearbeiten und aktualisieren. Weitere Informationen zu diesem speziellen Fall finden Sie unten unter [Verwenden eines Dispatchers mit einem Autorenserver](#using-a-dispatcher-with-an-author-server).

## Gründe für die Verwendung des Dispatchers zum Implementieren der Zwischenspeicherung  {#why-use-dispatcher-to-implement-caching}

Es gibt zwei grundlegende Ansätze zum Veröffentlichen im Web:

* **Statische Webserver**: wie Apache oder IIS sind einfach, aber schnell.
* **Inhaltsverwaltungsserver** stellen dynamische, intelligente Inhalte in Echtzeit bereit, benötigen jedoch erheblich längere Berechnungszeiten und andere Ressourcen.

Mit dem Dispatcher können Sie eine Umgebung realisieren, die gleichzeitig schnell und dynamisch ist. Der Dispatcher funktioniert als Teil eines statischen HTML-Servers wie Apache mit folgendem Ziel:

* Speichern (oder „Caching“) von möglichst vielen Site-Inhalten in Form einer statischen Website
* Möglichst wenig Zugriff auf die Layout-Engine

Dies bedeutet:

* **statische Inhalte** wird mit derselben Geschwindigkeit und Leichtigkeit wie auf einem statischen Webserver verarbeitet. Außerdem können Sie die Verwaltungs- und Sicherheitstools verwenden, die für Ihre statischen Webserver verfügbar sind.

* **Dynamische Inhalte** werden nach Bedarf generiert, ohne das System mehr als absolut notwendig zu verlangsamen.

Der Dispatcher umfasst Mechanismen zum Generieren und Aktualisieren von statischem HTML auf Grundlage der Inhalte der dynamischen Website. Sie können detailliert angeben, welche Dokumente als statische Dateien gespeichert werden sollen und welche immer dynamisch generiert werden.

In diesem Abschnitt werden die Grundlagen dieses Prozesses erläutert.

### Statischer Webserver  {#static-web-server}

![](assets/chlimage_1-3.png)

Ein statischer Webserver, wie z. B. Apache oder IIS, stellt statische HTML-Dateien für die Besucher Ihrer Website bereit. Statische Seiten werden einmal erstellt, sodass für jede Anforderung derselbe Inhalt bereitgestellt wird.

Dieser Prozess ist einfach und effizient. Wenn ein Besucher eine Datei wie eine HTML-Seite anfordert, wird die Datei direkt aus dem Speicher übernommen. Im schlimmsten Fall wird es von der lokalen Festplatte gelesen. Statische Webserver sind schon seit geraumer Zeit verfügbar, sodass es eine breite Palette von Tools für Verwaltung und Sicherheitsmanagement gibt und sie gut in Netzwerkinfrastrukturen integriert sind.

### Inhaltsverwaltungsserver  {#content-management-servers}

![](assets/chlimage_1-4.png)

Wenn Sie ein CMS (Content Management Server) wie AEM verwenden, verarbeitet eine erweiterte Layout-Engine die Anforderung eines Besuchers. Die Engine liest Inhalte aus einem Repository, das in Kombination mit Stilen, Formaten und Zugriffsrechten den Inhalt in ein Dokument umwandelt, das auf die Bedürfnisse und Rechte eines Besuchers zugeschnitten ist.

Dieser Workflow ermöglicht die Erstellung umfassenderer, dynamischer Inhalte, wodurch die Flexibilität und Funktionalität Ihrer Website gesteigert wird. Allerdings ist für die Layout-Engine mehr Verarbeitungsleistung als für einen statischen Server erforderlich. Daher ist dieses Setup für Verzögerungen anfällig, wenn viele Besucher das System verwenden.

## Zwischenspeicherung durch den Dispatcher  {#how-dispatcher-performs-caching}

![](assets/chlimage_1-5.png)

**Das Cache-Verzeichnis** Zum Zwischenspeichern nutzt das Dispatcher-Modul die Fähigkeit des Webservers, statische Inhalte bereitzustellen. Der Dispatcher legt die zwischengespeicherten Dokumente im Basisverzeichnis des Webservers ab.

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

Bei einer Inhaltsaktualisierung werden ein oder mehrere AEM-Dokumente geändert. AEM sendet eine Syndizierungsanforderung an den Dispatcher, der den Cache entsprechend aktualisiert:

1. Löscht die geänderten Dateien aus dem Cache.
1. Es werden alle Dateien aus dem Cache gelöscht, die mit demselben Handle beginnen. Wenn beispielsweise die Datei „/de/index.html“ aktualisiert wird, werden alle Dateien, die mit „/de/index.“ beginnen, gelöscht. Mit diesem Mechanismus können Sie Cache-effiziente Sites erstellen, insbesondere über die Bildnavigation.
1. Es *Touch* der so genannten **statfile**, der den Zeitstempel der Statfile aktualisiert, um das Datum der letzten Änderung anzugeben.

Beachten Sie die folgenden Punkte:

* Inhaltsaktualisierungen werden in der Regel mit einem Authoring-System verwendet, das &quot;weiß&quot;, was ersetzt werden muss.
* Dateien, die von einer Aktualisierung betroffen sind, werden entfernt, aber nicht sofort ersetzt. Wenn eine solche Datei das nächste Mal angefordert wird, ruft der Dispatcher die neue Datei aus der AEM-Instanz ab und legt sie im Cache ab, wobei der alte Inhalt überschrieben wird.
* In der Regel werden automatisch generierte Bilder, die Text von einer Seite enthalten, in den Bilddateien gespeichert, die mit demselben Handle beginnen. So wird gewährleistet, dass die Zuordnung zum Löschen vorhanden ist. Sie können z. B. den Titeltext der Seite „mypage.html“ als Bilddatei „mypage.titlePicture.gif“ im selben Verzeichnis speichern. Auf diese Weise wird das Bild automatisch jedes Mal aus dem Cache gelöscht, wenn die Seite aktualisiert wird. So können Sie sicher sein, dass das Bild immer die aktuelle Version der Seite darstellt.
* Sie können mehrere Statfiles verwenden, z. B. eine pro Ordner. Wenn eine Seite aktualisiert wird, sucht AEM den nächsten übergeordneten Ordner, der eine Statfile enthält, und *ändert*  diese Datei.

### Automatische Invalidierung

Bei der automatischen Invalidierung werden Teile des Caches automatisch ungültig gemacht – ohne Dateien physisch zu löschen. Bei jeder Inhaltsaktualisierung wird die sogenannte Statfile bearbeitet, sodass der Zeitstempel den Zeitpunkt der letzten Aktualisierung angibt.

Der Dispatcher verfügt über eine Liste der Dateien, für die die automatische Invalidierung gilt. Wenn ein Dokument aus dieser Liste angefordert wird, vergleicht der Dispatcher das Datum des zwischengespeicherten Dokuments mit dem Zeitstempel der Statfile:

* Wenn das zwischengespeicherte Dokument neuer ist, gibt der Dispatcher es zurück.
* Wenn es älter ist, ruft der Dispatcher die aktuelle Version aus der AEM-Instanz ab.

Auch hier sollten bestimmte Punkte beachtet werden:

* Die automatische Invalidierung wird normalerweise verwendet, wenn die Beziehungen komplex sind, z. B. HTML-Seiten. Diese Seiten enthalten Links und Navigationseinträge, sodass sie in der Regel nach einer Inhaltsaktualisierung aktualisiert werden müssen. Wenn Sie automatisch PDF- oder Bilddateien generiert haben, können Sie auch diese Dateien automatisch invalidieren lassen.
* Die automatische Invalidierung erfordert zum Zeitpunkt der Aktualisierung keine Aktion des Dispatchers, außer dass die Statfile bearbeitet wird. Durch das Bearbeiten der Statfile ist allerdings automatisch der Inhalt des Zwischenspeichers veraltet, ohne dass er physisch aus dem Cache entfernt wird.

## Zurückgeben von Dokumenten durch den Dispatcher  {#how-dispatcher-returns-documents}

![](assets/chlimage_1-6.png)

### Festlegen, ob ein Dokument zwischengespeichert werden soll

Sie können [definieren, welche Dokumente der Dispatcher in der Konfigurationsdatei zwischenspeichert](https://experienceleague.adobe.com/docs/experience-manager-dispatcher/using/configuring/dispatcher-configuration.html?lang=de). Der Dispatcher überprüft die Anforderung anhand der Liste der Dokumente, die zwischengespeichert werden können. Wenn das Dokument nicht in dieser Liste enthalten ist, fragt der Dispatcher das Dokument in der AEM-Instanz ab.

Der Dispatcher ruft das Dokument in den folgenden Fällen immer direkt aus der AEM-Instanz ab:

* Der Anfrage-URI enthält ein Fragezeichen &quot;`?`&quot;. Dieses Szenario zeigt normalerweise eine dynamische Seite an, z. B. ein Suchergebnis, die nicht zwischengespeichert werden muss.
* Die Dateierweiterung fehlt. Der Webserver benötigt die Erweiterung, um den Dokumenttyp (den MIME-Typ) zu bestimmen.
* Der Authentifizierungs-Header ist festgelegt (konfigurierbar).

>[!NOTE]
>
>Die Methoden GET oder HEAD (für den HTTP-Header) können vom Dispatcher zwischengespeichert werden. Weitere Informationen zum Zwischenspeichern von Antwortheadern finden Sie im Abschnitt [Zwischenspeichern von HTTP-Antwortheadern](https://experienceleague.adobe.com/docs/experience-manager-dispatcher/using/configuring/dispatcher-configuration.html?lang=de).

### Bestimmen, ob ein Dokument zwischengespeichert wird 

Der Dispatcher speichert die zwischengespeicherten Dateien auf dem Webserver, als wären sie Teil einer statischen Website. Wenn ein Benutzer ein zwischenspeicherbares Dokument anfordert, prüft der Dispatcher, ob dieses Dokument im Dateisystem des Webservers vorhanden ist:

* Wenn das Dokument zwischengespeichert wurde, gibt der Dispatcher die Datei zurück.
* Wenn es nicht zwischengespeichert wurde, fordert der Dispatcher das Dokument von der AEM-Instanz an.

### Bestimmen, ob ein Dokument aktuell ist

Um festzustellen, ob ein Dokument aktuell ist, führt der Dispatcher zwei Schritte aus:

1. Es wird geprüft, ob für das Dokument die automatische Invalidierung ausgeführt wird. Ist dies nicht der Fall, wird das Dokument als aktuell betrachtet.
1. Wenn das Dokument für die automatische Invalidierung konfiguriert wurde, überprüft der Dispatcher, ob es älter oder neuer als die letzte verfügbare Änderung ist. Wenn es älter ist, ruft der Dispatcher die aktuelle Version von der AEM-Instanz ab und ersetzt die Version im Cache.

>[!NOTE]
>
>Dokumente ohne **automatische Invalidierung** bleiben im Cache, bis sie physisch gelöscht werden. Beispielsweise durch eine Inhaltsaktualisierung auf der Website.

## Vorteile des Lastenausgleichs {#the-benefits-of-load-balancing}

Beim Lastenausgleich wird die Rechenleistung für eine Website auf mehrere Instanzen von AEM verteilt.

![](assets/chlimage_1-7.png)

Vorteile:

* **erhöhte Verarbeitungsleistung**
In der Praxis bedeutet eine höhere Verarbeitungsleistung, dass der Dispatcher Dokumentanforderungen zwischen mehreren Instanzen von AEM teilt. Da jede Instanz jetzt weniger Dokumente verarbeiten muss, sind die Reaktionszeiten kürzer. Der Dispatcher führt interne Statistiken für jede Dokumentenkategorie, sodass die Anforderungen geschätzt und die Abfragen effizient aufgeteilt werden können.

* **Verbesserte Fail-Safe-Abdeckung**
Wenn der Dispatcher keine Antworten von einer Instanz erhält, werden Anforderungen automatisch an eine der anderen Instanzen weitergeleitet. Wenn eine Instanz nicht mehr verfügbar ist, ist die einzige Auswirkung eine Verlangsamung der Site, proportional zur verlorenen Rechenleistung. Alle Dienste werden jedoch fortgesetzt.

* Sie können auch verschiedene Websites auf demselben statischen Webserver verwalten.

>[!NOTE]
>
>Während die Auslastung durch den Lastenausgleich effizient verteilt wird, wird sie durch die Zwischenspeicherung reduziert. Daher sollten Sie das Zwischenspeichern optimieren und die Gesamtauslastung reduzieren, bevor Sie den Lastenausgleich einrichten. Durch eine optimal konfigurierte Zwischenspeicherung kann die Leistung des Lastenausgleichs gesteigert oder der Lastausgleich überflüssig gemacht werden.

>[!CAUTION]
>
>Während ein einzelner Dispatcher die Kapazität der verfügbaren Veröffentlichungsinstanzen überlasten kann, kann es bei einigen seltenen Anwendungen sinnvoll sein, auch die Last zwischen zwei Dispatcher-Instanzen auszugleichen. Konfigurationen mit mehreren Dispatchern müssen sorgfältig geprüft werden, da ein zusätzlicher Dispatcher die Last auf den verfügbaren Veröffentlichungsinstanzen erhöhen und die Leistung in den meisten Anwendungen leicht verringern kann.

## Durchführen des Lastenausgleichs durch den Dispatcher  {#how-the-dispatcher-performs-load-balancing}

### Leistungsstatistiken

Der Dispatcher führt interne Statistiken dazu, wie schnell die einzelnen AEM-Instanzen Dokumente verarbeiten. Basierend auf diesen Daten schätzt der Dispatcher, welche Instanz die schnellste Reaktionszeit bei der Beantwortung einer Anfrage bereitstellen kann, und reserviert so die erforderliche Berechnungszeit für diese Instanz.

Verschiedene Anforderungstypen können unterschiedliche durchschnittliche Fertigstellungszeiten aufweisen. Daher können Sie vom Dispatcher Dokumentkategorien angeben. Diese Kategorien werden dann bei der Berechnung der Zeitschätzungen berücksichtigt. Sie können beispielsweise zwischen HTML-Seiten und Bildern unterscheiden, da die typischen Antwortzeiten sehr unterschiedlich sein können.

Wenn Sie eine umfangreiche Suchfunktion verwenden, können Sie eine Kategorie für Suchabfragen erstellen. Diese Methode hilft dem Dispatcher dabei, Suchanfragen an die Instanz zu senden, die am schnellsten antwortet. Außerdem wird dadurch verhindert, dass eine langsamere Instanz angehalten wird, wenn sie mehrere &quot;teure&quot;Suchanfragen erhält, während die anderen die &quot;billigeren&quot;Anforderungen erhalten.

### Personalisierte Inhalte („Sticky-Verbindungen“) 

Sticky-Verbindungen stellen sicher, dass alle Dokumente für einen Benutzer auf derselben AEM-Instanz erstellt werden. Dieser Punkt ist wichtig, wenn Sie personalisierte Seiten und Sitzungsdaten verwenden. Die Daten werden in der Instanz gespeichert, sodass nachfolgende Anforderungen vom selben Benutzer zu dieser Instanz zurückführen müssen. Andernfalls gehen die Daten verloren.

Da durch Sticky-Verbindungen die Fähigkeit des Dispatchers eingeschränkt wird, die Anforderungen zu optimieren, sollten Sie sie nur bei Bedarf verwenden. Sie können den Ordner angeben, in dem die „Sticky-Dokumente“ gespeichert werden, und so sicherstellen, dass alle Dokumente in diesem Ordner für jeden Benutzer auf derselben Instanz erstellt werden.

>[!NOTE]
>
>Bei den meisten Seiten, die Sticky-Verbindungen verwenden, müssen Sie das Zwischenspeichern deaktivieren. Andernfalls wird die Seite für alle Benutzer gleich angezeigt, unabhängig vom Sitzungsinhalt.
>
>Bei *einigen* Anwendungen ist es möglich, Sticky-Verbindungen und Zwischenspeichern zu verwenden, zum Beispiel, wenn Sie ein Formular anzeigen, das Daten in die Sitzung schreibt.

## Verwenden mehrerer Dispatcher  {#using-multiple-dispatchers}

In komplexen Umgebungen können Sie mehrere Dispatcher verwenden. Folgende Szenarien sind möglich:

* Ein Dispatcher zum Veröffentlichen einer Website im Intranet
* Ein zweiter Dispatcher mit einer anderen Adresse und anderen Sicherheitseinstellungen zum Veröffentlichen desselben Inhalts im Internet

In einem solchen Fall müssen Sie sicherstellen, dass jede Anforderung nur einen Dispatcher durchläuft. Ein Dispatcher verarbeitet keine Anforderungen, die von einem anderen Dispatcher stammen. Stellen Sie daher sicher, dass beide Dispatcher direkt auf die AEM-Website zugreifen.

## Verwenden des Dispatchers mit einem CDN  {#using-dispatcher-with-a-cdn}

Ein CDN (Content Delivery Network) wie Akamai Edge Delivery oder Amazon Cloud Front stellt Inhalte von einem Speicherort in der Nähe des Endbenutzers bereit. Dies hat folgende Vorteile:

* Verkürzung der Reaktionszeiten für Endbenutzer
* Verringern der Auslastung Ihrer Server

Als HTTP-Infrastrukturkomponente funktioniert ein CDN ähnlich wie der Dispatcher. Wenn ein CDN-Knoten eine Anforderung erhält, wird die Anforderung nach Möglichkeit aus dem Cache bereitgestellt (die Ressource ist im Cache verfügbar und gültig). Andernfalls wird der nächstgelegene Server kontaktiert, um die Ressource abzurufen und sie ggf. für weitere Anforderungen zwischenzuspeichern.

Welcher Server der nächstgelegene ist, hängt von Ihrem jeweiligen Setup ab. In einem Akamai-Setup kann die Abfrage z. B. den folgenden Pfad durchlaufen:

* Akamai Edge-Knoten
* Akamai-Midgress-Ebene
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

In einer typischen AEM-Einrichtung bietet die Konfiguration durch Erweiterung, Pfad oder beides - was über die Punkte 1 und 2 erreicht werden kann - Möglichkeiten, angemessene Caching-Zeiträume für häufig verwendete Ressourcen festzulegen, die sich häufig nicht ändern, wie z. B. Design-Bilder und Client-Bibliotheken. Wenn neue Releases bereitgestellt werden, ist in der Regel eine manuelle Invalidierung erforderlich.

Wenn verwaltete Inhalte mit diesem Verfahren zwischengespeichert werden, bedeutet dies, dass Änderungen am Inhalt für Endbenutzer erst sichtbar sind, wenn die konfigurierte Dauer für die Zwischenspeicherung abgelaufen ist und das Dokument erneut vom Dispatcher abgerufen wird.

Für eine präzisere Kontrolle können Sie mit der API-basierten Invalidierung den Cache eines CDN invalidieren, wenn der Dispatcher-Cache ungültig gemacht wird. Basierend auf der CDNs-API können Sie Ihre [ContentBuilder](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/replication/ContentBuilder.html) und [TransportHandler](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/replication/TransportHandler.html) (wenn die API nicht auf REST basiert) und richten Sie einen Replikationsagenten ein, der diese Teile verwendet, um den Cache des CDN zu invalidieren.

>[!NOTE]
>
>Siehe auch [AEM (CQ) Dispatcher-Sicherheit und CDN+Browser-Caching](https://www.slideshare.net/andrewmkhoury/dispatcher-caching-aemgemspart2jan2015) und Aufzeichnung einer Präsentation zu [Dispatcher-Caching](https://experienceleague.adobe.com/docs/experience-manager-gems-events/gems/gems2015/aem-dispatcher-caching-new-features-and-optimizations.html?lang=en).

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

Ein Dispatcher kann vor einer Autoreninstanz verwendet werden, um die Bearbeitungsleistung zu verbessern. Um einen Bearbeitungs-Dispatcher zu konfigurieren, führen Sie die folgenden Schritte aus:

1. Installieren Sie einen Dispatcher auf einem Webserver (einem Apache- oder IIS-Webserver, siehe [Installieren des Dispatchers](dispatcher-install.md)).
1. Testen Sie den neu installierten Dispatcher mit einer funktionierenden AEM Veröffentlichungsinstanz. Dadurch wird sichergestellt, dass eine Grundlinie korrekte Installation erreicht wurde.
1. Stellen Sie dann sicher, dass der Dispatcher über TCP/IP eine Verbindung zur Autoreninstanz herstellen kann.
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
