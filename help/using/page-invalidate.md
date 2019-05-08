---
title: Invalidierung zwischengespeicherter Seiten aus AEM
seo-title: Ungültige Seiten aus Adobe AEM ungültig
description: Erfahren Sie, wie Sie die Interaktion zwischen Dispatcher und AEM konfigurieren, um eine effektive Cacheverwaltung sicherzustellen.
seo-description: Erfahren Sie, wie Sie die Interaktion zwischen Adobe AEM Dispatcher und AEM konfigurieren, um eine effektive Cacheverwaltung sicherzustellen.
uuid: 66533299-55 c 0-4864-9 beb -77 e 281 af 9359
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: '1193211344162'
template: /apps/docs/templates/contentpage
contentOwner: Benutzer
products: SG_ EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: Referenz
discoiquuid: 79 cd 94 be-a 6 bc -4 d 34-bfe 9-393 b 4107925 c
translation-type: tm+mt
source-git-commit: f35c79b487454059062aca6a7c989d5ab2afaf7b

---


# Invalidierung zwischengespeicherter Seiten aus AEM {#invalidating-cached-pages-from-aem}

Wenn Sie Dispatcher mit AEM verwenden, muss die Interaktion so konfiguriert werden, dass eine effektive Cacheverwaltung gewährleistet wird. Je nach Umgebung kann die Konfiguration auch zu einer Leistungsverbesserung führen.

## Einrichten von AEM-Benutzerkonten {#setting-up-aem-user-accounts}

Das Standardbenutzerkonto `admin` wird zur Authentifizierung der Replikationsagenten verwendet, die standardmäßig installiert sind. Sie sollten ein dediziertes Benutzerkonto zur Verwendung mit Replikationsagenten erstellen. [](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html#VerificationSteps)

Weitere Informationen finden Sie im [Abschnitt Replizierungs- und Transportbenutzer](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html#VerificationSteps) konfigurieren der AEM Security Checkliste.

## Invalidierung des Dispatcher-Caches aus der Autorenumgebung {#invalidating-dispatcher-cache-from-the-authoring-environment}

Ein Replikationsagent auf der AEM-Autoreninstanz sendet eine Anforderung zur Invalidierung des Caches an Dispatcher, wenn eine Seite veröffentlicht wird. Der Anforderung führt dazu, dass der Dispatcher bei Veröffentlichung neuer Inhalte die Datei im Cache schließlich aktualisiert.

<!-- 

Comment Type: draft

<p>Cache invalidation requests for a page are also generated for any aliases or vanity URLs that are configured in the page properties. </p>

 -->

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-0436B4A35714BFF67F000101@AdobeID)
Last Modified Date: 2017-05-25T10:37:23.679-0400

<p>Hiding this information due to <a href="https://jira.corp.adobe.com/browse/CQDOC-5805">CQDOC-5805</a>.</p>

 -->

Verwenden Sie das folgende Verfahren, um einen Replizierungsagenten auf der AEM-Autoreninstanz zu konfigurieren, um den Dispatcher-Cache bei Seitenaktivierung ungültig zu machen:

1. Öffnen Sie die AEM-Tools-Konsole. (`https://localhost:4502/miscadmin#/etc`)
1. Öffnen Sie den erforderlichen Replizierungsagenten unter Tools/Replication/Agenten für den Autor. Sie können den standardmäßig installierten Dispatcher Flush-Agent verwenden.
1. Klicken Sie auf „Bearbeiten“ und stellen Sie sicher, dass auf der Registerkarte „Einstellungen“ **Aktiviert** ausgewählt ist. 

1. (Optional) Um Alias- oder Vanity Path-Ungültigkeitsanforderungen zu aktivieren, wählen Sie die Option **Alias-Aktualisierung** .
1. Geben Sie auf der Registerkarte „Transport“ den für den Zugriff auf den Dispatcher erforderlichen URI ein.\
   Wenn Sie den Standard-Dispatcher Flush-Agenten verwenden, müssen Sie wahrscheinlich den Hostnamen und Anschluss aktualisieren. Beispiel: https:// &lt;*dispatcherhost*&gt;: &lt;*Portapache*&gt;/dispatcher/invalidate. cache

   **Hinweis:** Bei Dispatcher Flush-Agenten wird die URI-Eigenschaft nur verwendet, wenn Sie pfadbasierte virtualhost-Einträge verwenden, um zwischen den Farms zu unterscheiden. Verwenden Sie dieses Feld, um die Farm als ungültig festzulegen. Beispiel: farm # 1 hat eine virtuelle Gruppe von `www.mysite.com/path1/*` und farm # 2 hat eine virtuelle Gruppe von `www.mysite.com/path2/*`. Sie können eine URL des `/path1/invalidate.cache` ersten Betriebs verwenden und `/path2/invalidate.cache` die zweite Farm als Ziel auswählen. Weitere Informationen finden Sie unter [Verwenden des Dispatchers mit mehreren Domänen](dispatcher-domains.md).

1. Konfigurieren Sie ggf. weitere Parameter. 
1. Klicken Sie auf OK, um den Agenten zu aktivieren.

Alternativ können Sie auch über die [AEM Touch-Benutzeroberfläche auf den Dispatcher Flush-Agenten zugreifen](https://helpx.adobe.com/experience-manager/6-2/sites/deploying/using/replication.html#ConfiguringaDispatcherFlushagent)und diese konfigurieren.

Weitere Informationen zum Aktivieren des Zugriffs auf Vanity-urls finden Sie unter [Aktivieren des Zugriffs auf Vanity-urls](dispatcher-configuration.md#enabling-access-to-vanity-urls-vanity-urls).

>[!NOTE]
>
>Für den Agenten zum Leeren des Dispatcher-Caches ist kein Benutzername und Kennwort erforderlich, diese werden bei entsprechender Konfiguration aber mit Standardauthentifizierung gesendet.

Dieser Ansatz birgt zwei mögliche Probleme:

* Der Dispatcher muss von der Autoreninstanz erreichbar sein. Wenn Ihr Netzwerk (z. B. die Firewall) so konfiguriert ist, dass der Zugriff zwischen den beiden eingeschränkt wird, ist dies nicht der Fall.

* Die Veröffentlichung und die Invalidierung des Caches finden gleichzeitig statt. Ein Benutzer kann möglicherweise eine Seite anfordern, nachdem diese gerade aus dem Cache entfernt wurde und bevor die neue Seite veröffentlicht wird. AEM gibt dann die alte Seite zurück und der Dispatcher speichert sie erneut im Cache. Dies stellt vor allem für große Sites ein Problem dar.

## Invalidierung des Dispatcher-Caches von einer Veröffentlichungsinstanz {#invalidating-dispatcher-cache-from-a-publishing-instance}

Unter bestimmten Umständen lassen sich Leistungsverbesserungen erzielen, indem die Cacheverwaltung aus der Autorenumgebung auf eine Veröffentlichungsinstanz verlagert wird. Dann sendet die Veröffentlichungsumgebung (nicht die Autorenumgebung von AEM) eine Anforderung zur Invalidierung an den Dispatcher, wenn eine veröffentlichte Seite empfangen wird.

Beispiele:

<!-- 

Comment Type: draft

<p>Cache invalidation requests for a page are also generated for any aliases or vanity URLs that are configured in the page properties. </p>

 -->

* Verhindern möglicher Zeitkonflikte zwischen dem Dispatcher und der Veröffentlichungsinstanz (siehe [Invalidierung des Dispatcher-Caches aus der Autorenumgebung](#invalidating-dispatcher-cache-from-the-authoring-environment)).
* Das System umfasst eine Reihe von Veröffentlichungsinstanzen, die sich auf Hochleistungsservern befinden, und nur eine Autoreninstanz.

>[!NOTE]
>
>Ein erfahrener AEM-Administrator sollte entscheiden, ob diese Methode anzuwenden ist.

Das Leeren des Dispatchers wird von einem Replikationsagenten gesteuert, der auf der Veröffentlichungsinstanz ausgeführt wird. Allerdings erfolgt die Konfiguration in der Autorenumgebung und wird anschließend übertragen, indem der Agent aktiviert wird:

1. Öffnen Sie die AEM-Tools-Konsole.
1. Öffnen Sie den erforderlichen Replizierungsagenten unter Tools/Replikation/Agenten beim Veröffentlichen. Sie können den standardmäßig installierten Dispatcher Flush-Agent verwenden.
1. Klicken Sie auf „Bearbeiten“ und stellen Sie sicher, dass auf der Registerkarte „Einstellungen“ **Aktiviert** ausgewählt ist. 
1. (Optional) Um Alias- oder Vanity Path-Ungültigkeitsanforderungen zu aktivieren, wählen Sie die Option **Alias-Aktualisierung** .
1. Geben Sie auf der Registerkarte „Transport“ den für den Zugriff auf den Dispatcher erforderlichen URI ein.\
   Wenn Sie den Standard-Dispatcher Flush-Agenten verwenden, müssen Sie wahrscheinlich den Hostnamen und Anschluss aktualisieren. Beispiel: `http://<dispatcherHost>:<portApache>/dispatcher/invalidate.cache`

   **Hinweis:** Bei Dispatcher Flush-Agenten wird die URI-Eigenschaft nur verwendet, wenn Sie pfadbasierte virtualhost-Einträge verwenden, um zwischen den Farms zu unterscheiden. Verwenden Sie dieses Feld, um die Farm als ungültig festzulegen. Beispiel: farm # 1 hat eine virtuelle Gruppe von `www.mysite.com/path1/*` und farm # 2 hat eine virtuelle Gruppe von `www.mysite.com/path2/*`. Sie können eine URL des `/path1/invalidate.cache` ersten Betriebs verwenden und `/path2/invalidate.cache` die zweite Farm als Ziel auswählen. Weitere Informationen finden Sie unter [Verwenden des Dispatchers mit mehreren Domänen](dispatcher-domains.md).

1. Konfigurieren Sie ggf. weitere Parameter. 
1. Wiederholen Sie den Vorgang für jede betreffende Veröffentlichungsinstanz.

Wenn Sie nach dem Konfigurieren eine Seite aktivieren und so in der Autorenumgebung erstellte Inhalte in der Veröffentlichungsumgebung verfügbar machen, initiiert dieser Agent eine Standardreplikation. Das Protokoll enthält Meldungen, die auf Anforderungen von Ihrem Publish-Server hinweisen und wie folgt aussehen:

1. `<publishserver> 13:29:47 127.0.0.1 POST /dispatcher/invalidate.cache 200`

## Manuelle Invalidierung des Dispatcher-Caches {#manually-invalidating-the-dispatcher-cache}

Um den Dispatcher-Cache ungültig zu machen (oder zu leeren), ohne eine Seite zu aktivieren, können Sie eine HTTP-Anforderung an den Dispatcher ausgeben. Sie können beispielsweise eine AEM-Anwendung erstellen, die es Administratoren oder anderen Anwendungen ermöglicht, den Cache zu leeren.

Die HTTP-Anforderung veranlasst den Dispatcher zum Löschen bestimmter Dateien aus dem Cache. Der Dispatcher aktualisiert den Cache dann mit einer neuen Kopie (optional).

### Löschen zwischengespeicherter Dateien {#delete-cached-files}

Geben Sie eine HTTP-Anforderung aus, die den Dispatcher zum Löschen von Dateien aus dem Cache veranlasst. Dispatcher speichert die Dateien nur dann erneut, wenn sie eine Client-Anforderung für die Seite erhalten. Das Löschen zwischengespeicherter Dateien auf diese Weise eignet sich für Websites, die nicht gleichzeitig gleichzeitig Anforderungen derselben Seite erhalten.

Die HTTP-Anforderung sieht folgendermaßen aus:

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate  
CQ-Handle: path-pattern
Content-Length: 0
```

Der Dispatcher leert (löscht) die zwischengespeicherten Dateien und Ordner mit Namen, die dem Wert des Headers `CQ-Handler` entsprechen. Zum Beispiel entspricht eine `CQ-Handle` Übereinstimmung `/content/geomtrixx-outdoors/en` den folgenden Elementen:

* allen Dateien (mit beliebiger Dateierweiterung) mit dem Namen `en` im Verzeichnis `geometrixx-outdoors`

* Beliebiger Ordner mit dem Namen &quot; `_jcr_content`«unterhalb des Ordners en (der, falls vorhanden, zwischengespeicherte Renderings der Seite enthält)

Alle anderen Dateien im Dispatcher-Cache (oder bis zu einer bestimmten Ebene, je nach `/statfileslevel` Einstellung) werden durch Berühren der `.stat` Datei ungültig. Das Datum der letzten Änderung dieser Datei wird mit dem Datum der letzten Änderung eines zwischengespeicherten Dokuments verglichen und das Dokument wird erneut abgerufen, wenn die `.stat` Datei höher ist. Weitere Informationen finden Sie unter [Invalidierung von Dateien nach Ordnerebene](dispatcher-configuration.md#main-pars_title_26).

Die Invalidierung (d. h. das nicht inhaltsverändernde Bearbeiten von STAT-Dateien) kann durch Senden des zusätzlichen Headers `CQ-Action-Scope: ResourceOnly` verhindert werden. Damit können bestimmte Ressourcen geleert werden, ohne andere Teile des Caches ungültig zu machen, wie JSON-Daten, die dynamisch erstellt werden und unabhängig vom Cache regelmäßig geleert werden müssen (z. B. Daten, die aus Drittanbietersystemen abgerufen werden, um Nachrichten, Börsenticker usw. anzuzeigen).

### Löschen und erneutes Zwischenspeichern von Dateien {#delete-and-recache-files}

Geben Sie eine HTTP-Anforderung aus, die den Dispatcher veranlasst, zwischengespeicherte Dateien zu löschen und die Datei unmittelbar abzurufen und erneut zwischenzuspeichern. Löschen und speichern Sie Dateien sofort erneut im Cache, wenn Websites wahrscheinlich gleichzeitige Anforderungen für ein und dieselbe Seite empfangen. Mit dem unmittelbaren erneuten Zwischenspeichern wird sichergestellt, dass der Dispatcher die Seite insgesamt nur einmal abruft und zwischenspeichert, anstatt einmal für alle gleichzeitigen Clientanforderungen.

**Hinweis:** Das Löschen und Neuspeichern von Dateien sollte nur auf der Veröffentlichungsinstanz erfolgen. Bei der Ausführung von der Autoreninstanz treten die Race-Bedingungen auf, wenn versucht wird, Ressourcen zu wiederholen, bevor sie veröffentlicht wurden.

Die HTTP-Anforderung sieht folgendermaßen aus:

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate   
`Content-Type: text/plain  
CQ-Handle: path-pattern  
Content-Length: numchars in bodypage_path0

page_path1 
...  
page_pathn
```

Die Seitenpfade für das unmittelbare erneute Zwischenspeichern werden im Nachrichtentext in getrennten Zeilen aufgeführt. Der Wert von `CQ-Handle` ist der Pfad einer Seite, die die erneut zwischenzuspeichernde Seite ungültig macht. (Siehe Parameter `/statfileslevel` des [Cachekonfigurationselements](dispatcher-configuration.md#main-pars_146_44_0010) .) The following example HTTP request message deletes and recaches the `/content/geometrixx-outdoors/en.html page`:

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate  
Content-Type: text/plain   
CQ-Handle: /content/geometrixx-outdoors/en/men.html  
Content-Length: 36

/content/geometrixx-outdoors/en.html
```

### Beispielservlet für das Leeren des Caches {#example-flush-servlet}

Mit dem folgenden Code wird ein Servlet implementiert, das eine Invalidierungsanforderung an den Dispatcher sendet. Das Servlet erhält eine Anforderungsmeldung mit den Parametern `handle` und `page` Parametern. Diese Parameter stellen den Wert des Headers `CQ-Handle` bereit und dementsprechend auch den Pfad zu der Datei, die erneut zwischengespeichert werden soll. Das Servlet verwendet die Werte, um die HTTP-Anforderung für den Dispatcher zu erstellen.

Wenn das Servlet für die Veröffentlichungsintanz bereitgestellt wird, veranlasst die folgende URL den Dispatcher dazu, die Seite „/content/geometrixx-outdoors/en.html“ zu löschen und dann eine neue Kopie zwischenzuspeichern.

`10.36.79.223:4503/bin/flushcache/html?page=/content/geometrixx-outdoors/en.html&handle=/content/geometrixx-outdoors/en/men.html`

>[!NOTE]
>
>Dieses Beispielservlet ist nicht sicher und dient nur dazu, die Verwendung der HTTP-Post-Anforderungsmeldung zu veranschaulichen. Ihre Lösung sollte den Zugriff auf das Servlet sichern.


```java
package com.adobe.example;

import org.apache.felix.scr.annotations.Component;
import org.apache.felix.scr.annotations.Service;
import org.apache.felix.scr.annotations.Property;

import org.apache.sling.api.SlingHttpServletRequest;
import org.apache.sling.api.SlingHttpServletResponse;
import org.apache.sling.api.servlets.SlingSafeMethodsServlet;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import org.apache.commons.httpclient.*;
import org.apache.commons.httpclient.methods.PostMethod;
import org.apache.commons.httpclient.methods.StringRequestEntity;

@Component(metatype=true)
@Service
public class Flushcache extends SlingSafeMethodsServlet {

 @Property(value="/bin/flushcache")
 static final String SERVLET_PATH="sling.servlet.paths";

 private Logger logger = LoggerFactory.getLogger(this.getClass());

 public void doGet(SlingHttpServletRequest request, SlingHttpServletResponse response) {
  try{ 
      //retrieve the request parameters
      String handle = request.getParameter("handle");
      String page = request.getParameter("page");

      //hard-coding connection properties is a bad practice, but is done here to simplify the example
      String server = "localhost"; 
      String uri = "/dispatcher/invalidate.cache";

      HttpClient client = new HttpClient();

      PostMethod post = new PostMethod("https://"+host+uri);
      post.setRequestHeader("CQ-Action", "Activate");
      post.setRequestHeader("CQ-Handle",handle);
   
      StringRequestEntity body = new StringRequestEntity(page,null,null);
      post.setRequestEntity(body);
      post.setRequestHeader("Content-length", String.valueOf(body.getContentLength()));
      client.executeMethod(post);
      post.releaseConnection();
      //log the results
      logger.info("result: " + post.getResponseBodyAsString());
      }
  }catch(Exception e){
      logger.error("Flushcache servlet exception: " + e.getMessage());
  }
 }
}
```

