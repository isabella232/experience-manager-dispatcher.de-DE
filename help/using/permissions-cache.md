---
title: Zwischenspeichern von geschützten Inhalten
seo-title: Caching Secured Content in AEM Dispatcher
description: Erfahren Sie, wie die Zwischenspeicherung mit Berechtigungen im Dispatcher funktioniert.
seo-description: Learn how permission-sensitive caching works in AEM Dispatcher.
uuid: abfed68a-2efe-45f6-bdf7-2284931629d6
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: 4f9b2bc8-a309-47bc-b70d-a1c0da78d464
exl-id: 3d8d8204-7e0d-44ad-b41b-6fec2689c6a6
source-git-commit: 31eaa42b17838d97cacd5c535e04be01a3eb6807
workflow-type: ht
source-wordcount: '910'
ht-degree: 100%

---

# Zwischenspeichern von geschützten Inhalten {#caching-secured-content}

Mit dem Zwischenspeichern unter Beachtung von Berechtigungen können Sie gesicherte Seiten zwischenspeichern. Der Dispatcher prüft die Zugriffsberechtigungen der Benutzer für eine Seite, bevor die zwischengespeicherte Seite bereitgestellt wird.

Der Dispatcher umfasst das AuthChecker-Modul, das das Zwischenspeichern unter Berücksichtigung von Berechtigungen implementiert. Wenn das Modul aktiviert ist, ruft der Dispatcher ein AEM-Servlet auf, um die Benutzerauthentifizierung und -autorisierung für die angeforderten Inhalte durchzuführen. Die Servlet-Antwort bestimmt, ob der Inhalt aus dem Cache im Webbrowser bereitgestellt wird oder nicht.

Da die Methoden zur Authentifizierung und Autorisierung spezifisch für die AEM-Bereitstellung sind, müssen Sie das Servlet erstellen.

>[!NOTE]
>
>Verwenden Sie `deny`-Filter, um allgemeine Sicherheitseinschränkungen zu erzwingen. Verwenden Sie die Zwischenspeicherung unter Berücksichtigung von Berechtigungen für Seiten, die zum Zulassen des Zugriffs auf eine Untergruppe von Benutzenden oder Gruppen konfiguriert sind.

Die folgenden Abbildungen zeigen die Abfolge der Ereignisse, wenn ein Webbrowser eine Seite anfordert, für die die Zwischenspeicherung unter Berücksichtigung von Berechtigungen verwendet wird.

## Die Seite wurde zwischengespeichert und der Benutzer ist autorisiert  {#page-is-cached-and-user-is-authorized}

![](assets/chlimage_1.png)

1. Der Dispatcher ermittelt, dass der angeforderte Inhalt zwischengespeichert wurde und gültig ist.
1. Der Dispatcher sendet eine Anfragenachricht an den Renderer. Der HEAD-Abschnitt enthält alle Kopfzeilen aus der Browser-Anfrage.
1. Der Renderer ruft das Auth-Checker-Servlet auf, um die Sicherheitsprüfung durchzuführen, und antwortet dem Dispatcher. Die Antwortnachricht enthält den HTTP-Status-Code „200“, um anzuzeigen, dass die Person autorisiert ist.
1. Der Dispatcher sendet eine Antwortnachricht an den Browser, die aus den Kopfzeilen der Renderer-Antwort und dem zwischengespeicherten Inhalt im Textkörper besteht.

## Die Seite wurde nicht zwischengespeichert und der Benutzer ist autorisiert  {#page-is-not-cached-and-user-is-authorized}

![](assets/chlimage_1-1.png)

1. Der Dispatcher ermittelt, dass der Inhalt nicht zwischengespeichert wurde oder aktualisiert werden muss.
1. Der Dispatcher leitet die ursprüngliche Anfrage an den Renderer weiter.
1. Der Renderer ruft das autorisierende AEM-Servlet (dies ist nicht das Dispatcher-AuthChecker-Servlet) auf, um eine Sicherheitsprüfung durchzuführen. Wenn die Person autorisiert ist, schließt der Renderer die gerenderte Seite im Text der Antwortnachricht ein.
1. Der Dispatcher leitet die Antwort an den Browser weiter. Der Dispatcher fügt den Text der Antwortnachricht des Renderers zum Cache hinzu. 

## Der Benutzer ist nicht autorisiert  {#user-is-not-authorized}

![](assets/chlimage_1-2.png)

1. Der Dispatcher überprüft den Cache.
1. Der Dispatcher sendet eine Anfragenachricht an den Renderer, die alle Kopfzeilen der Browser-Anfrage enthält.
1. Der Renderer ruft das Auth-Checker-Servlet auf, eine Sicherheitsprüfung durchzuführen. Diese führt zu einem Fehler und der Renderer leitet die ursprüngliche Anfrage an den Dispatcher weiter.
1. Der Dispatcher leitet die ursprüngliche Anfrage an den Renderer weiter.
1. Der Renderer ruft das autorisierende AEM-Servlet (dies ist nicht das Dispatcher-AuthChecker-Servlet) auf, um eine Sicherheitsprüfung durchzuführen. Wenn die Person autorisiert ist, schließt der Renderer die gerenderte Seite im Text der Antwortnachricht ein.
1. Der Dispatcher leitet die Antwort an den Browser weiter. Der Dispatcher fügt den Text der Antwortnachricht des Renderers zum Cache hinzu. 

## Implementieren der Zwischenspeicherung unter Berücksichtigung von Berechtigungen {#implementing-permission-sensitive-caching}

Wenn Sie die Zwischenspeicherung unter Berücksichtigung von Berechtigungen implementieren möchten, führen Sie die folgenden Schritte aus:

* Entwickeln eines Servlets zur Authentifizierung und Autorisierung
* Konfigurieren des Dispatchers

>[!NOTE]
>
>In der Regel werden sichere Ressourcen in einem anderen Ordner als nicht sichere Dateien gespeichert, z. B. unter „/content/secure/“.

>[!NOTE]
>
>Wenn sich ein CDN (oder ein anderer Cache) vor dem Dispatcher befindet, sollten Sie die Caching-Header entsprechend festlegen, damit das CDN den privaten Inhalt nicht zwischenspeichert. Beispiel: `Header always set Cache-Control private`.
>Weitere Informationen zum Festlegen von privaten Cache-Control-Headern finden Sie auf der Seite [Caching in AEM as a Cloud Service](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/content/implementing/content-delivery/caching.html?lang=de).

## Erstellen des Auth-Checker-Servlets {#create-the-auth-checker-servlet}

Erstellen Sie ein Servlet, das die Authentifizierung und Autorisierung der Person ausführt, die den Web-Inhalt anfordert. Stellen Sie dann das Servlet bereit. Das Servlet kann beliebige Authentifizierungs- und Autorisierungsmethoden verwenden, z. B. das AEM-Benutzerkonto und Repository-ACLs oder einen LDAP-Lookup-Dienst. Stellen Sie das Servlet in der AEM-Instanz bereit, die der Dispatcher als Renderer verwendet.

Alle Benutzenden müssen auf das Servlet zugreifen können. Daher sollte das Servlet die `org.apache.sling.api.servlets.SlingSafeMethodsServlet`-Klasse erweitern, die Lesezugriff auf das System bereitstellt.

Das Servlet empfängt nur HEAD-Anforderungen vom Renderer, Sie müssen daher nur die `doHead`-Methode implementieren.

Der Renderer schließt den URI der angeforderten Ressource als Parameter der HTTP-Anforderung ein. Der Zugriff auf ein Autorisierungsservlet erfolgt z. B. über `/bin/permissioncheck`. Um eine Sicherheitsprüfung auf der Seite „/content/geometrixx-outdoors/en.html“ durchzuführen, schließt der Renderer die folgende URL in die HTTP-Anforderung ein:

`/bin/permissioncheck?uri=/content/geometrixx-outdoors/en.html`

Die Antwortnachricht des Servlets muss den folgenden HTTP-Status-Code enthalten:

* 200: Authentifizierung und Autorisierung erfolgreich.

Das folgende Beispiel-Servlet ruft die URL der angeforderten Ressource aus der HTTP-Anfrage ab. Der Code verwendet die `Property`-Anmerkung der Felix SCR, um den Wert der `sling.servlet.paths`-Eigenschaft auf „/bin/permissioncheck“ festzulegen. In der `doHead`-Methode ruft das Servlet das Sitzungsobjekt ab und bestimmt mit der `checkPermission`-Methode den entsprechenden Antwortcode.

>[!NOTE]
>
>Der Wert der Eigenschaft „sling.servlet.paths“ muss im Sling Servlet Resolver-Dienst (org.apache.sling.servlets.resolver.SlingServletResolver) aktiviert werden.

### Beispielservlet  {#example-servlet}

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

import javax.jcr.Session;

@Component(metatype=false)
@Service
public class AuthcheckerServlet extends SlingSafeMethodsServlet {
 
    @Property(value="/bin/permissioncheck")
    static final String SERVLET_PATH="sling.servlet.paths";
    
    private Logger logger = LoggerFactory.getLogger(this.getClass());
    
    public void doHead(SlingHttpServletRequest request, SlingHttpServletResponse response) {
     try{ 
      //retrieve the requested URL
      String uri = request.getParameter("uri");
      //obtain the session from the request
      Session session = request.getResourceResolver().adaptTo(javax.jcr.Session.class);     
      //perform the permissions check
      try {
       session.checkPermission(uri, Session.ACTION_READ);
       logger.info("authchecker says OK");
       response.setStatus(SlingHttpServletResponse.SC_OK);
      } catch(Exception e) {
       logger.info("authchecker says READ access DENIED!");
       response.setStatus(SlingHttpServletResponse.SC_FORBIDDEN);
      }
     }catch(Exception e){
      logger.error("authchecker servlet exception: " + e.getMessage());
     }
    }
}
```

## Konfigurieren des Dispatchers für die Zwischenspeicherung unter Berücksichtigung von Berechtigungen {#configure-dispatcher-for-permission-sensitive-caching}

>[!NOTE]
>
>Wenn Ihre Anforderungen das Zwischenspeichern von authentifizierten Dokumenten zulassen, legen Sie unter „/cache“ für die Eigenschaft „/allowAuthorized“ den Wert `/allowAuthorized 1` fest. Weitere Informationen finden Sie unter [Zwischenspeicherung bei Verwendung von Authentifizierung](/help/using/dispatcher-configuration.md).

Der Abschnitt „auth_checker“ der Datei „dispatcher.any“ steuert das Verhalten beim Zwischenspeichern unter Berücksichtigung von Berechtigungen. Der Abschnitt „auth_checker“ enthält folgende Unterabschnitte:

* `url`: Der Wert der `sling.servlet.paths`-Eigenschaft des Servlets, das die Sicherheitsprüfung ausführt.

* `filter`: Filter zum Angeben der Ordner, auf die das Zwischenspeichern unter Berücksichtigung von Berechtigungen angewendet wird. In der Regel wird ein `deny`-Filter auf alle Ordner angewendet und `allow`-Filter werden auf gesicherte Ordner angewendet.

* `headers`: Gibt die HTTP-Header an, die das Autorisierungsservlet in die Antwort einschließt.

Beim Start des Dispatchers enthält die Dispatcher-Protokolldatei die folgende Nachricht auf Debug-Ebene:

`AuthChecker: initialized with URL 'configured_url'.`

Im folgenden „auth_checker“-Beispielabschnitt wird der Dispatcher für die Verwendung des Servlets aus dem vorherigen Thema konfiguriert. Durch die Angaben im Abschnitt „filter“ werden Berechtigungsprüfungen nur für sichere HTML-Ressourcen durchgeführt.

### Beispielkonfiguration  {#example-configuration}

```xml
/auth_checker
  {
  # request is sent to this URL with '?uri=<page>' appended
  /url "/bin/permissioncheck"
      
  # only the requested pages matching the filter section below are checked,
  # all other pages get delivered unchecked
  /filter
    {
    /0000
      {
      /glob "*"
      /type "deny"
      }
    /0001
      {
      /glob "/content/secure/*.html"
      /type "allow"
      }
    }
  # any header line returned from the auth_checker's HEAD request matching
  # the section below will be returned as well
  /headers
    {
    /0000
      {
      /glob "*"
      /type "deny"
      }
    /0001
      {
      /glob "Set-Cookie:*"
      /type "allow"
      }
    }
  }
```
