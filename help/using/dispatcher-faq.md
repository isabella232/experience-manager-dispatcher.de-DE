---
title: Häufige Probleme beim Dispatcher
seo-title: Häufige Probleme bei AEM Dispatcher
description: Häufige Probleme bei AEM Dispatcher
seo-description: Häufige Probleme bei Adobe AEM Dispatcher
translation-type: tm+mt
source-git-commit: 76cffbfb616cd5601aed36b7076f67a2faf3ed3b

---


# Häufig gestellte Fragen zu AEM Dispatcher - Häufige Probleme

![Konfigurieren des Dispatchers](assets/CQDispatcher_workflow_v2.png)

## Einführung

### Was ist der Dispatcher?

Der Dispatcher ist das Caching- und/oder Lastenausgleichstool von Adobe Experience Manager, mit dem eine schnelle und dynamische Web-Authoring-Umgebung erstellt werden kann. Zur Zwischenspeicherung funktioniert der Dispatcher als Teil eines HTTP-Servers, z. B. Apache, mit dem Ziel, den statischen Website-Inhalt so weit wie möglich zu speichern (oder &quot;Caching&quot; zu speichern) und so oft wie möglich auf die Layout-Engine der Website zuzugreifen. Bei einer Rolle für den Lastenausgleich verteilt der Dispatcher Benutzeranforderungen (laden) über verschiedene AEM-Instanzen (rendern).

Zur Zwischenspeicherung verwendet das Dispatcher-Modul die Fähigkeit des Webservers, statische Inhalte bereitzustellen. Der Dispatcher platziert die zwischengespeicherten Dokumente im Dokumentenstamm des Webservers.

### Wie führt der Dispatcher Zwischenspeicherung durch?

Der Dispatcher verwendet die Fähigkeit des Webservers, statische Inhalte bereitzustellen. Der Dispatcher speichert zwischengespeicherte Dokumente im Dokumentenstamm des Webservers. Der Dispatcher verwendet zwei Hauptverfahren zum Aktualisieren des zwischengespeicherten Inhalts, wenn Änderungen an der Website vorgenommen werden.

* **Inhaltsaktualisierungen** entfernen die geänderten Seiten sowie Dateien, die direkt mit ihnen verknüpft sind.
* **Die automatische Ungültigmachung** ungültig die Teile des Zwischenspeichers, die nach einer Aktualisierung möglicherweise nicht mehr auf dem neuesten Stand sind. So werden relevante Seiten zum Beispiel als veraltet eingestuft, ohne dass etwas gelöscht wird.

### Welche Vorteile hat der Lastenausgleich?

Lastenausgleich verteilt Benutzeranforderungen (Laden) über mehrere AEM-Instanzen. Die folgende Liste beschreibt die Vorteile für Lastenausgleich:

* **Erhöhte Verarbeitungsleistung**: In der Praxis bedeutet das, dass der Dispatcher Dokumentanforderungen zwischen verschiedenen Instanzen von AEM sendet. Da jede Instanz weniger Dokumente zur Verarbeitung enthält, haben Sie schnellere Reaktionszeiten. Der Dispatcher führt interne Statistiken für jede Dokumentenkategorie, sodass die Anforderungen geschätzt und die Abfragen effizient aufgeteilt werden können.
* **Verbesserte Abdeckung ohne Ausfallsicherheit**: Wenn der Dispatcher keine Antworten von einer Instanz erhält, werden Anforderungen automatisch an einen der anderen Instanzen weitergeleitet. Wenn eine Instanz nicht verfügbar ist, ist die einzige Auswirkung daher eine Verlangsamung der Site, proportional zur verloren gegangenen Rechenleistung.

>[!NOTE]
>
>Weitere Informationen finden Sie auf [der Seite Dispatcher-Übersicht.](dispatcher.md)

## Installieren und Konfigurieren

### Woher lade ich das Dispatcher-Modul herunter?

Sie können das neueste Dispatcher-Modul über die Seite [Dispatcher-Versionshinweise](release-notes.md) herunterladen.

### Wie installiere ich das Dispatcher-Modul?

Siehe Abschnitt [&quot;Installieren von Dispatcher](dispatcher-install.md) «

### Wie konfiguriere ich das Dispatcher-Modul?

Siehe [Konfigurieren](dispatcher-configuration.md) der Dispatcher-Seite.

### Wie konfiguriere ich den Dispatcher für die Autoreninstanz?

Ausführliche Anweisungen finden Sie unter [Verwenden von Dispatcher mit einer Autoreninstanz](dispatcher.md#using-a-dispatcher-with-an-author-server) .

### Wie konfiguriere ich den Dispatcher mit mehreren Domänen?

Sie können den CQ Dispatcher mit mehreren Domänen konfigurieren, sofern die Domänen die folgenden Bedingungen erfüllen:

* Der Webinhalt für beide Domänen wird in einem einzelnen AEM-Repository gespeichert.
* Die Dateien im Dispatcher-Cache können getrennt für jede Domäne ungültig gemacht werden

Lesen [Sie den Dispatcher mit mehreren Domänen](dispatcher-domains.md) , um weitere Informationen zu erhalten.

### Wie konfiguriere ich den Dispatcher so, dass alle Anforderungen eines Benutzers auf dieselbe Instanz im Veröffentlichungsmodus geleitet werden?

Sie können die Funktion [für persistente Verbindungen](dispatcher-configuration.md#identifying-a-sticky-connection-folder-stickyconnectionsfor) verwenden, um sicherzustellen, dass alle Dokumente für einen Benutzer in derselben Instanz von AEM verarbeitet werden. Diese Funktion ist wichtig, wenn Sie personalisierte Seiten und Sitzungsdaten verwenden. Die Daten werden in der Instanz gespeichert. Nachfolgende Anforderungen desselben Benutzers müssen daher zu dieser Instanz zurückkehren oder die Daten gehen verloren.

Da Sticky-Verbindungen die Fähigkeit des Dispatchers einschränken, Anforderungen zu optimieren, sollten Sie diesen Ansatz nur verwenden, wenn erforderlich. Sie können den Ordner angeben, der die Dokumente &quot;sticky&quot; enthält, sodass alle Dokumente in diesem Ordner für einen Benutzer verarbeitet werden.

### Kann ich persistente Verbindungen und Zwischenspeicherung gemeinsam verwenden?

Bei den meisten Seiten, die persistente Verbindungen verwenden, sollten Sie die Zwischenspeicherung deaktivieren. Andernfalls wird allen Benutzern unabhängig vom Sitzungsinhalt dieselbe Instanz der Seite angezeigt.

Bei einigen Anwendungen kann es möglich sein, sowohl persistente Verbindungen als auch Zwischenspeicherung zu verwenden. Wenn Sie z. B. ein Formular anzeigen, das Daten in eine Sitzung schreibt, können Sie haftbare Verbindungen und Zwischenspeicherung in Verbindung mit dem Zwischenspeicher verwenden.

### Kann sich ein Dispatcher und eine AEM-Veröffentlichungsinstanz auf demselben physischen Computer befinden?

Ja, wenn der Computer ausreichend leistungsfähig ist. Es wird jedoch empfohlen, den Dispatcher und die AEM-Veröffentlichungsinstanz auf verschiedenen Computern einzurichten.

Normalerweise befindet sich die Instanz im Veröffentlichungsmodus in der Firewall und der Dispatcher befindet sich in DMZ. Wenn Sie sowohl die Instanz im Veröffentlichungsmodus als auch den Dispatcher auf demselben physischen Computer haben, stellen Sie sicher, dass die Firewalleinstellungen direkten Zugriff auf die Veröffentlichungsinstanz aus externen Netzwerken verhindern.

### Kann ich nur Dateien mit bestimmten Erweiterungen zwischenspeichern?

Ja. Wenn Sie beispielsweise nur GIF-Dateien zwischenspeichern möchten, geben Sie *. gif im Cache-Abschnitt der Konfigurationsdatei dispatcher. any an.

### Wie lösche ich Dateien aus dem Cache?

Sie können Dateien aus dem Cache löschen, indem Sie eine HTTP-Anforderung verwenden. Wenn die HTTP-Anforderung empfangen wird, löscht Dispatcher die Dateien aus dem Cache. Dispatcher speichert die Dateien nur dann erneut, wenn sie eine Client-Anforderung für die Seite erhalten. Das Löschen zwischengespeicherter Dateien auf diese Weise eignet sich für Websites, die nicht gleichzeitig gleichzeitig Anforderungen derselben Seite erhalten.

Die HTTP-Anforderung hat folgende Syntax:

```
POST /dispatcher/invalidate.cache HTTP/1.1
CQ-Action: Activate
CQ-Handle: path-pattern
Content-Length: 0
```

Dispatcher löscht die zwischengespeicherten Dateien und Ordner mit Namen, die dem Wert des CQ-Handle-Headers entsprechen. Beispiel: Ein CQ-Handle für `/content/geomtrixx-outdoors/en` die folgenden Elemente:

Alle Dateien (der Dateierweiterung) mit dem Namen en im geometrixx-outdoors-Ordner
beliebige Ordner unter `_jcr_content` dem Ordner en (, falls vorhanden, enthält, falls vorhanden, enthält zwischengespeicherte Renderings der Seite)
Der Ordner en wird nur gelöscht, wenn die Variable `CQ-Action` lautet `Delete` oder `Deactivate`.

Weitere Informationen zu diesem Thema finden Sie unter [Manuelles Ungültigen des Dispatcher-Cache](page-invalidate.md).

### Wie implementiere ich die Berechtigungssensitive Zwischenspeicherung?

Weitere Informationen finden Sie auf [der Seite zum Zwischenspeichern geschützter Inhalte](permissions-cache.md) .

### Wie sichere ich die Kommunikation zwischen den Dispatcher- und CQ-Instanzen?

Siehe [Dispatcher Security Checkliste](security-checklist.md) und die Seiten der [AEM Security Checkliste](https://helpx.adobe.com/experience-manager/6-4/sites/administering/using/security-checklist.html) .

### Dispatcher-Problem `jcr:content` wurde geändert zu `jcr%3acontent`

**Frage**: Wir haben kürzlich ein Problem bei Dispatcher-Ebene behoben, das einen des AJAX-Aufrufs auslöst, der ein Datenformular-CQ-Repository erhalten hat `jcr:content` und das kodiert wurde, um `jcr%3acontent` zu falscher Ergebnismenge zu führen.

**Antwort**: Bitte verwenden `ResourceResolver.map()` Sie Methode, um eine &quot;freundliche&quot; URL zu erhalten, die verwendet/ausgegeben werden soll, Abrufen von Anfragen aus und auch zur Lösung des Cacheproblems mit Dispatcher. Die map ()-Methode kodiert den `:` Doppelpunkt zu Unterstriche und die Auflösung ()-Methode dekodiert sie wieder in das SLING JCR-lesbare Format. Sie müssen die map ()-Methode verwenden, um die im Ajax-Aufruf verwendete URL zu generieren.

Weitere lesen: [https://sling.apache.org/documentation/the-sling-engine/mappings-for-resource-resolution.html#namespace-mangling](https://sling.apache.org/documentation/the-sling-engine/mappings-for-resource-resolution.html#namespace-mangling)

## Dispatcher bereinigen

### Wie konfiguriere ich Dispatcher-Flush-Agenten auf einer Veröffentlichungsinstanz?

Siehe [Replizierungsseite](https://helpx.adobe.com/content/help/en/experience-manager/6-4/sites/deploying/using/replication.html#ConfiguringyourReplicationAgents) .

### Wie kann ich Fehlerbehebung bei Dispatcher-Problemen durchführen?

[In diesem Artikel](https://helpx.adobe.com/content/help/en/experience-manager/kb/troubleshooting-dispatcher-flushing-issues.html) zur Fehlerbehebung können Sie die folgenden Fragen beantworten:

* Wie kann ich Situationen debuggen, in denen keine Inhalte im Dispatcher-Cache gespeichert werden?
* Wie kann ich ein Problem debuggen, durch das Cachedateien nicht aktualisiert werden?
* Wie kann ich eine Situation debuggen, in der nichts mit Dispatcher-Flushing funktioniert?

Wenn Vorgänge Löschen dazu führen, dass der Dispatcher bereinigt werden kann, [verwenden Sie die Problemumgehung in diesem Blog-Blog von Sensei Martin](https://mkalugin-cq.blogspot.in/2012/04/i-have-been-working-on-following.html).

### Wie leere ich DAM-Assets aus dem Dispatcher-Cache?

Sie können die Funktion &quot;chain replication&quot; verwenden. Wenn diese Funktion aktiviert ist, sendet der dispatcher flush-Agent eine Flush-Anforderung, wenn eine Replikation vom Autor empfangen wird.

So aktivieren Sie ihn:

1. [Führen Sie die hier Schritte](page-invalidate.md#invalidating-dispatcher-cache-from-a-publishing-instance) zum Erstellen von Agenten beim Veröffentlichen aus.
1. Wechseln Sie zu den Konfigurationen des Agenten und überprüfen Sie auf der Registerkarte **&quot;Auslöser&quot;** das Feld **&quot; Bei Empfang** &quot; .

## Sonstiges

Wie erkennt der Dispatcher, ob ein Dokument aktuell ist?
Um zu bestimmen, ob ein Dokument aktuell ist, führt der Dispatcher diese Aktionen durch:

Es wird geprüft, ob für das Dokument die automatische Invalidierung ausgeführt wird. Ist dies nicht der Fall, wird das Dokument als aktuell betrachtet.
Wenn das Dokument für die automatische Invalidierung konfiguriert wurde, überprüft der Dispatcher, ob es älter oder neuer als die letzte verfügbare Änderung ist. Wenn es älter ist, ruft der Dispatcher die aktuelle Version von der AEM-Instanz ab und ersetzt die Version im Cache.

### Wie werden die Dispatcher-Rückgabedokumente zurückgegeben?

You can define whether the Dispatcher caches a document by using the [Dispatcher configuration](dispatcher-configuration.md) file, `dispatcher.any`. Der Dispatcher überprüft die Anforderung anhand der Liste der Dokumente, die zwischengespeichert werden können. Wenn das Dokument nicht in dieser Liste enthalten ist, fragt der Dispatcher das Dokument in der AEM-Instanz ab.

Die `/rules` Eigenschaft steuert, welche Dokumente gemäß dem Dokumentpfad zwischengespeichert werden. Unabhängig von der `/rules` Eigenschaft speichert Dispatcher ein Dokument nie in den folgenden Situationen:

* Wenn die Anforderung URI ein Fragezeichen `(?)`enthält.
* Hierdurch wird normalerweise eine dynamische Seite angegeben (z. B. ein Suchergebnis), die nicht zwischengespeichert werden muss.
* Die Dateierweiterung fehlt.
* Der Webserver benötigt die Erweiterung, um den Dokumenttyp (den MIME-Typ) zu bestimmen.
* Der Authentifizierungsheader wurde festgelegt (dies kann konfiguriert werden).
* Die AEM-Instanz antwortet mit folgenden Headern:
   * no-cache
   * no-store
   * must-revalidate

Der Dispatcher speichert zwischengespeicherte Dateien auf dem Webserver, als ob sie Teil einer statischen Website wären. Wenn ein Benutzer ein zwischengespeichertes Dokument anfordert, prüft der Dispatcher, ob das Dokument im Dateisystem des Webservers vorhanden ist. Wenn dies der Fall ist, gibt der Dispatcher die Dokumente zurück. Andernfalls ruft der Dispatcher das Dokument aus der AEM-Instanz auf.

>[!NOTE]
>
>Die GET- oder HEAD-Methoden (für HTTP-Header) sind vom Dispatcher cache bar. Weitere Informationen zur Zwischenspeicherung der Antwortkopfzeile finden Sie im Abschnitt [Zwischenspeichern von HTTP-Antwort-Kopfzeilen](dispatcher-configuration.md#caching-http-response-headers) .

### Kann ich mehrere Dispatcher in einem Setup implementieren?

Ja. In solchen Fällen müssen beide Dispatcher direkt auf die AEM-Website zugreifen. Ein Dispatcher kann keine Anforderungen aus einem anderen Dispatcher verarbeiten.

