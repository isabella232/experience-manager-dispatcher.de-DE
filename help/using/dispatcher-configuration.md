---
title: Konfigurieren des Dispatchers
seo-title: Konfigurieren des Dispatchers
description: Erfahren Sie, wie Sie Dispatcher konfigurieren.
seo-description: Erfahren Sie, wie Sie Dispatcher konfigurieren.
uuid: 253 ef 0 f 7-2491-4 cec-ab 22-97439 df 29 fd 6
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: '1193211344162'
topic-tags: dispatcher
content-type: Referenz
discoiquuid: abrue 8 e-bb 44-42 a 7-9 a 5 e-b 7 d 0 e 848391 a
translation-type: tm+mt
source-git-commit: bd8fff69a9c8a32eade60c68fc75c3aa411582af

---


# Konfigurieren des Dispatchers{#configuring-dispatcher}

>[!NOTE]
>
>Dispatcher-Versionen sind unabhängig von AEM. Möglicherweise wurden Sie von der Dokumentation zu einer früheren AEM-Version zu dieser Seite weitergeleitet.

In den folgenden Abschnitten wird beschrieben, wie Sie verschiedene Aspekte des Dispatchers konfigurieren.

## Unterstützung für IPv6 und IPv4 {#support-for-ipv-and-ipv}

Alle Elemente von AEM und Dispatcher können sowohl in IPv4- als auch in IPv6-Netzwerken installiert werden. Siehe [IPV4 und IPV6](https://helpx.adobe.com/experience-manager/6-3/sites/deploying/using/technical-requirements.html#AdditionalPlatformNotes).

## Dispatcher-Konfigurationsdateien {#dispatcher-configuration-files}

Standardmäßig wird die Dispatcher-Konfiguration in der `dispatcher.any` Textdatei gespeichert. Sie können jedoch den Namen und Speicherort dieser Datei während der Installation ändern.

Die Konfigurationsdatei enthält eine Reihe von Eigenschaften mit einzelnen oder mehreren Werten, die das Verhalten des Dispatchers steuern:

* Eigenschaftsnamen werden mit einem Schrägstrich `/`versehen.
* Mit Eigenschaften für mehrere Werte werden untergeordnete Elemente mit Klammern `{ }`eingeschlossen.

Eine Beispielkonfiguration sieht wie folgt aus:

```xml
# name of the dispatcher
/name "internet-server"

# each farm configures a set off (loadbalanced) renders
/farms
 {
  # first farm entry (label is not important, just for your convenience)
   /website 
     {  
     /clientheaders
       {
       # List of headers that are passed on
       }
     /virtualhosts
       {
       # List of URLs for this Web site
       }
     /sessionmanagement 
       {
       # settings for user authentification
       }
     /renders
       {
       # List of AEM instances that render the documents
       }
     /filter
       {
       # List of filters
       }
     /vanity_urls
       {
       # List of vanity URLs
       }
     /cache
       {
       # Cache configuration
       /rules
         {
         # List of cachable documents
         }
       /invalidate
         {
         # List of auto-invalidated documents
         }
       }
     /statistics
       {
       /categories
         {
         # The document categories that are used for load balancing estimates
         }
       }
     /stickyConnectionsFor "/myFolder"
     /health_check
       {
       # Page gets contacted when an instance returns a 500
       }
     /retryDelay "1"
     /numberOfRetries "5"
     /unavailablePenalty "1"
     /failover "1"
     }
 }
```

Sie können auch andere Dateien in die Konfiguration einbeziehen:

* Wenn Ihre Konfigurationsdatei groß ist, können Sie sie in mehrere kleinere Dateien unterteilen (die leichter zu verwalten sind), und Sie können diese dann einschließen.
* Automatisch generierte Dateien

Um beispielsweise die Datei myfarm. any in der /farms einzubeziehen, verwenden Sie den folgenden Code:

```xml
/farms
  {
  $include "myFarm.any"
  }
```

Verwenden Sie das Sternchen („*“) als Platzhalter, um einen Dateibereich auszuwählen.

Wenn beispielsweise die Dateien `farm_1.any` bis `farm_5.any` die Konfiguration der Farmen 1 bis 5 enthalten, können Sie sie wie folgt angeben bzw. auswählen:

```xml
/farms
  {
  $include "farm_*.any"
  }
```

## Verwenden von Umgebungsvariablen {#using-environment-variables}

Anstatt einer Hartkodierung von Werten können Sie in der Datei „dispatcher.any“ Umgebungsvariablen in Zeichenfolgen-Eigenschaften verwenden. Verwenden Sie das Format `${variable_name}`, um den Wert einer Umgebungsvariablen einzuschließen.

Wenn sich die Datei dispatcher. any im selben Verzeichnis befindet wie der Cache-Ordner, kann der folgende Wert für die [docroot](dispatcher-configuration.md#main-pars-title-30) -Eigenschaft verwendet werden:

```xml
/docroot "${PWD}/cache"
```

Wenn Sie als weiteres Beispiel eine Umgebungsvariable mit dem Namen `PUBLISH_IP` erstellen, in der der Hostname der AEM-Veröffentlichungsinstanz gespeichert ist, kann die folgende Konfiguration der [/renders](dispatcher-configuration.md#main-pars-127-25-0008)-Eigenschaft verwendet werden:

```xml
/renders {
  /0001 {
    /hostname "${PUBLISH_IP}"
    /port "8443"
  }
}
```

## Benennen der Dispatcher-Instanz {#naming-the-dispatcher-instance-name}

Verwenden Sie die `/name` Eigenschaft, um einen eindeutigen Namen zur Identifizierung Ihrer Dispatcher-Instanz anzugeben. Die `/name` Eigenschaft ist eine Eigenschaft der obersten Ebene in der Konfigurationsstruktur.

## Definieren von Beständen {#defining-farms-farms}

Die `/farms` Eigenschaft definiert ein oder mehrere Sätze von Dispatcher-Verhaltensweisen, wobei jeder Satz mit verschiedenen Websites oder urls verknüpft ist. Die `/farms` Eigenschaft kann eine einzelne Farm oder mehrere Betriebe umfassen:

* Verwenden Sie eine einzelne Farm, wenn Dispatcher alle Webseiten oder Websites auf dieselbe Art und Weise verarbeiten soll.
* Erstellen Sie mehrere Farmen, wenn verschiedene Bereiche Ihrer Website oder verschiedene Websites unterschiedliche Dispatcher-Verhalten erfordern.

Die `/farms` Eigenschaft ist eine Eigenschaft der obersten Ebene in der Konfigurationsstruktur. Um eine Farm zu definieren, fügen Sie der `/farms` Eigenschaft eine untergeordnete Eigenschaft hinzu. Verwenden Sie einen Eigenschaftsnamen, der die Farm in der Dispatcher-Instanz eindeutig identifiziert.

Die `/*farmname*` Eigenschaft ist mehrere Werte und enthält andere Eigenschaften, die das Dispatcher-Verhalten definieren:

* Die URLs der Seiten, zu denen die Farm gehört
* Eine oder mehrere Dienst-URLs (in der Regel von AEM-Veröffentlichungsinstanzen) zum Rendern von Dokumenten
* Die Statistiken für den Lastenausgleich mehrerer Dokumenten-Renderer
* Andere Verhalten, z. B. welche Dateien wo zwischengespeichert werden sollen

Der Wert kann jedes alphanumerische Zeichen (a-z, 0-9) enthalten. Das folgende Beispiel zeigt die Definition der Skeleton für zwei Farms mit dem Namen `/daycom` und `/docsdaycom`:

```xml
#name of dispatcher
/name "day sites"

#farms section defines a list of farms or sites
/farms
{
   /daycom
   {
       ...
   }
   /docdaycom
   {
      ...
   }
}
```

>[!NOTE]
>
>Wenn Sie mehr als eine Render-Farm verwenden, wird die Liste unten nach oben bewertet. Dies ist besonders relevant, wenn [Sie virtuelle Hosts](dispatcher-configuration.md#main-pars-117-15-0006) für Ihre Websites definieren.

Jede Farmeigenschaft kann die folgenden untergeordneten Eigenschaften enthalten:

| Eigenschaftsname | Beschreibung |
|--- |--- |
| [/homepage](#specify-a-default-page-iis-only-homepage) | Standardhomepage (optional) (nur IIS) |
| [/clientheaders](#specifying-the-http-headers-to-pass-through-clientheaders) | Die Header der weiterzuleitenden Client-HTTP-Anforderung |
| [/virtualhosts](#identifying-virtual-hosts-virtual-hosts) | Die virtuellen Hosts für diese Farm |
| [/sessionmanagement](#enabling-secure-sessions-session-management) | Unterstützung für die Sitzungsverwaltung und -authentifizierung |
| [/renders](#defining-page-renderers-renders) | Die Server, die die gerenderten Seiten liefern (in der Regel AEM-Veröffentlichungsinstanzen) |
| [/filter](#configuring-access-to-content-filter) | Definiert die URLs, auf die der Dispatcher den Zugriff ermöglicht |
| [/vanity_urls](#enabling-access-to-vanity-urls-vanity-urls) | Konfiguriert den Zugriff auf Vanity-URLs |
| [/propagateSyndPost](#forwarding-syndication-requests-propagate-syndpost) | Unterstützung für die Weiterleitung von Syndizierungsanforderungen |
| [/cache](#configuring-the-dispatcher-cache-cache) | Konfiguriert das Cacheverhalten |
| [/statistics](#configuring-load-balancing-statistics) | Definieren von Statistikkategorien für Lastenausgleichsberechnungen |
| [/stickyConnectionsFor](#identifying-a-sticky-connection-folder-sticky-connections-for) | Der Ordner, der „Sticky“-Dokumente enthält. |
| [/health_check](#specifying-a-health-check-page) | URL zum Prüfen der Serververfügbarkeit |
| [/retryDelay](#specifying-the-page-retry-delay) | Verzögerung vor dem Wiederholen einer fehlgeschlagenen Verbindung |
| [/unavailablePenalty](#reflecting-server-unavailability-in-dispatcher-statistics) | Strafzeiten, die sich auf Statistiken für Lastenausgleichsberechnungen auswirken |
| [/failover](#using-the-fail-over-mechanism) | Erneutes Senden von Anforderungen an andere Renderer, wenn die ursprüngliche Anforderung fehlschlägt |

## Angeben einer Standardseite (nur IIS) – /homepage {#specify-a-default-page-iis-only-homepage}

>[!CAUTION]
>
>Der `/homepage`Parameter (nur IIS) funktioniert nicht mehr. Stattdessen sollten Sie das [IIS URL-Modul umschreiben](https://docs.microsoft.com/en-us/iis/extensions/url-rewrite-module/using-the-url-rewrite-module).
>
>Wenn Sie Apache verwenden, sollten Sie das `mod_rewrite` Modul verwenden. Weitere Informationen dazu `mod_rewrite` finden Sie in der Apache-Website-Dokumentation (z. [B. Apache 2.4](https://httpd.apache.org/docs/current/mod/mod_rewrite.html)). Bei Verwendung `mod_rewrite`wird empfohlen, die Flag** [&#39; passthrough&#39;zu verwenden. | PT&#39; (Wechsel zum nächsten Handler)](https://helpx.adobe.com/dispatcher/kb/DispatcherModReWrite.html)**, um die Umschreiben der Suchmaschine zum Festlegen des `uri` Feldes der internen `request_rec` Struktur auf den Wert des `filename` Felds zu erzwingen.

<!-- 

Comment Type: draft

<p>The optional /homepage parameter specifies the page that Dispatcher returns when a client requests an undeterminable page or file.</p> 
<p>Typically this situation occurs when a user specifies an URL for which neither IIS or AEM provides an automatic redirection target. For example, if the AEM render instance is shut down after the content is cached, the content redirect URL is unavailable.</p> 
<p>The following example configuration displays the <span class="code">index.html</span> page in such circumstances:</p>

 -->

<!-- 

Comment Type: draft

<codeblock gutter="true" class="syntax xml">
  /homepage&nbsp;"/index.html" 
</codeblock>

 -->

<!-- 

Comment Type: draft

<p>The <span class="code">/homepage</span> section is located inside the <span class="code">/farms</span> section, for example:<br /> </p>

 -->

<!-- 

Comment Type: draft

<codeblock gutter="true" class="syntax xml">
  #name&nbsp;of&nbsp;dispatcher!!discoiqbr!!/name&nbsp;"day&nbsp;sites"!!discoiqbr!!!!discoiqbr!!#farms&nbsp;section&nbsp;defines&nbsp;a&nbsp;list&nbsp;of&nbsp;farms&nbsp;or&nbsp;sites!!discoiqbr!!/farms!!discoiqbr!!{!!discoiqbr!!&nbsp;&nbsp;&nbsp;/daycom!!discoiqbr!!&nbsp;&nbsp;&nbsp;{!!discoiqbr!!&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;/homepage&nbsp;"/index.html"!!discoiqbr!!&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;...!!discoiqbr!!&nbsp;&nbsp;&nbsp;}!!discoiqbr!!&nbsp;&nbsp;&nbsp;/docdaycom!!discoiqbr!!&nbsp;&nbsp;&nbsp;{!!discoiqbr!!&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;...!!discoiqbr!!&nbsp;&nbsp;&nbsp;}!!discoiqbr!!} 
</codeblock>

 -->

## Angeben der zu übergebenden HTTP-Header {#specifying-the-http-headers-to-pass-through-clientheaders}

Die `/clientheaders` Eigenschaft definiert eine Liste der HTTP-Header, die Dispatcher von der Client-HTTP-Anforderung an den Renderer (AEM-Instanz) weiterleitet.

Standardmäßig leitet der Dispatcher die Standard-HTTP-Header an die AEM-Instanz weiter. In einigen Fällen kann es notwendig werden, zusätzliche Header weiterzuleiten oder bestimmte Header zu entfernen: 

* Hinzufügen von Headern, z. B. benutzerdefinierte Header, die von der AEM-Instanz in der HTTP-Anforderung erwartet werden
* Entfernen von Headern, z. B. Authentifizierungsheader, die nur für den Webserver relevant sind

Wenn Sie die weiterzuleitenden Header anpassen, müssen Sie diese in einer vollständigen Liste angeben, die auch die standardmäßigen Header umfasst.

Beispiel: Eine Dispatcher-Instanz, die Seitenaktivierungsanfragen für Veröffentlichungsinstanzen verarbeitet, erfordert die `PATH` Kopfzeile im `/clientheaders` Abschnitt. Der Header `PATH` ermöglicht die Kommunikation zwischen dem Replikationsagenten und dem Dispatcher.

Der folgende Code ist eine Beispielkonfiguration für `/clientheaders`:

```shell
/clientheaders
  {
  "CSRF-Token"
  "X-Forwarded-Proto"
  "referer"
  "user-agent"
  "authorization"
  "from"
  "content-type"
  "content-length"
  "accept-charset"
  "accept-encoding"
  "accept-language"
  "accept"
  "host"
  "if-match"
  "if-none-match"
  "if-range"
  "if-unmodified-since"
  "max-forwards"
  "proxy-authorization"
  "proxy-connection"
  "range"
  "cookie"
  "cq-action"
  "cq-handle"
  "handle"
  "action"
  "cqstats"
  "depth"
  "translate"
  "expires"
  "date"
  "dav"
  "ms-author-via"
  "if"
  "lock-token"
  "x-expected-entity-length"
  "destination"
  "PATH"
  }
```

## Identifizieren von virtuellen Hosts {#identifying-virtual-hosts-virtualhosts}

Die `/virtualhosts` Eigenschaft definiert eine Liste aller Hostname/URI-Kombinationen, die Dispatcher für diese Farm akzeptiert. Das Sternchen (*) kann als Platzhalter verwendet werden. Die Werte für die `virtualhosts`/--Eigenschaft weisen folgendes Format auf:

```xml
[scheme]host[uri][*]
```

* `scheme`: (Optional) Either `https://` or `https://.`
* `host`: Der Name oder die IP-Adresse des Hostcomputers und die Portnummer, falls erforderlich (Siehe [https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.23)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.23)
* `uri`: (Optional) Der Pfad zu den Ressourcen

Die folgende Beispielkonfiguration handhabt Anforderungen nach den Domänen.com und. ch von mycompany sowie allen Domänen von mysubdivision:

```xml
   /virtualhosts
    {
    "www.myCompany.com"
    "www.myCompany.ch"
    "www.mySubDivison.*"
    }
```

Die folgende Konfiguration verarbeitet *alle* Anforderungen:

```xml
   /virtualhosts
    {
    "*"
    }
```

### Auflösen des virtuellen Hosts {#resolving-the-virtual-host}

Wenn Dispatcher eine HTTP- oder HTTPS-Anforderung erhält, findet er den virtuellen Hostwert, der am besten mit den und `host,``uri``scheme` headers der Anforderung übereinstimmt. Dispatcher bewertet die Werte in den `virtualhosts` Eigenschaften in der folgenden Reihenfolge:

* Der Dispatcher beginnt in der Datei „dispatcher.any“ bei der niedrigsten Farm und fährt von unten nach oben fort.
* Bei jeder Farm beginnt der Dispatcher mit dem obersten Wert in der `virtualhosts`-Eigenschaft und geht die Werteliste dann von oben nach unten durch.

Der Dispatcher ermittelt wie folgt den passendsten Wert für den virtuellen Host:

* Der erste virtuelle Host, der alle drei der `host`, des `scheme`und der Anforderung `uri` entspricht, wird verwendet.
* Wenn keine `virtualhosts` Werte vorhanden `scheme` und `uri` Teile vorhanden sind, die mit `scheme` der `uri` Anforderung und der Anforderung übereinstimmen, wird der erste virtuelle Host verwendet, der mit dem `host` der Anforderung übereinstimmt.
* Wenn keine `virtualhosts`-Werte einen Host-Teil haben, der mit dem Host der Anforderung übereinstimmt, wird der oberste virtuelle Host der obersten Farm verwendet.

Aus diesem Grund sollten Sie Ihren standardmäßigen virtuellen Host ganz oben in der `virtualhosts`-Eigenschaft in der obersten Farm Ihrer Datei „dispatcher.any“ platzieren.

### Beispiel für die Auflösung von virtuellen Hosts {#example-virtual-host-resolution}

Im folgenden Beispiel wird ein Codefragment der Datei „dispatcher.any“ gezeigt, mit dem zwei Dispatcher-Farmen definiert werden, mit denen jeweils eine `virtualhosts`-Eigenschaft definiert wird.

```xml
/farms
  {
  /myProducts 
    { 
    /virtualhosts
      {
      "www.mycompany.com"
      }
    /renders
      {
      /hostname "server1.myCompany.com"
      /port "80"
      }
    }
  /myCompany 
    { 
    /virtualhosts
      {
      "www.mycompany.com/products/*"
      }
    /renders
      {
      /hostname "server2.myCompany.com"
      /port "80"
      }
    }
  }
```

In der folgenden Tabelle sind die virtuellen Hosts aufgeführt, die für die HTTP-Anforderungen im Beispiel aufgelöst werden:

| URL-Anforderung | Aufgelöster virtueller Host |
|---|---|
| `https://www.mycompany.com/products/gloves.html` | `www.mycompany.com/products/*;` |
| `https://www.mycompany.com/about.html` | `www.mycompany.com` |

## Ermöglichen von sicheren Sitzungen – /sessionmanagement {#enabling-secure-sessions-sessionmanagement}

>[!CAUTION]
>
>`/allowAuthorized`**muss** im `"0"``/cache` Abschnitt festgelegt werden, um diese Funktion zu aktivieren.

Erstellen Sie eine sichere Sitzung für den Zugriff auf die Render-Farm, damit Benutzer sich anmelden müssen, um auf eine beliebige Seite in der Farm zuzugreifen. Nach der Anmeldung können Benutzer auf alle Seiten in der Farm zugreifen. Weitere Informationen zur Verwendung dieser Funktion mit geschlossenen Benutzergruppen finden Sie unter [Erstellen einer geschlossenen Benutzergruppe](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/cug.html#CreatingTheUserGroupToBeUsed).

Die `/sessionmanagement` Eigenschaft ist eine Subeigenschaft von `/farms`.

>[!CAUTION]
>
>Wenn für verschiedene Abschnitte Ihrer Website verschiedene Zugriffsanforderungen gelten, müssen Sie mehrere Farmen definieren.

**/sessionmanagement** weist mehrere Unterparameter auf:

**/directory** (obligatorisch)

Der Ordner, in dem die Sitzungsinformationen gespeichert werden. Wenn der Ordner nicht vorhanden ist, wird er erstellt.

**/encode** (optional)

Gibt an, wie die Sitzungsinformationen kodiert werden. Verwenden Sie „md5“ für eine Verschlüsselung mithilfe des md5-Algorithmus oder „hex“ für eine Hexadezimalkodierung. Wenn Sie die Sitzungsdaten verschlüsseln, kann auch ein Benutzer mit Zugriff auf das Dateisystem den Sitzungsinhalt nicht lesen. Der Standardwert ist „md5“.

**/header** (optional)

Der Name des HTTP-Headers oder Cookies, in dem die Autorisierungsinformationen gespeichert sind. Wenn Sie die Informationen im HTTP-Header speichern möchten, verwenden Sie `HTTP:<*header-name*>`. Verwenden `Cookie:<header-name>`Sie zum Speichern der Informationen in einem Cookie. Wenn Sie keinen Wert angeben, wird `HTTP:authorization` verwendet.

**/timeout** (optional)

Die Anzahl der Sekunden, nach deren Ablauf eine Zeitüberschreitung der Sitzung eintritt, wenn sie nicht mehr verwendet wird. Wird kein Wert angegeben, wird „800“ verwendet, sodass eine Zeitüberschreitung für die Sitzung etwa 13 Minuten nach der letzten Benutzeranforderung eintritt.

Eine Beispielkonfiguration sieht wie folgt aus:

```xml
/sessionmanagement 
  { 
  /directory "/usr/local/apache/.sessions" 
  /encode "md5" 
  /header "HTTP:authorization" 
  /timeout "800" 
  }
```

## Definieren von Seitenrenderern {#defining-page-renderers-renders}

Mit der /renders-Eigenschaft wird die URL definiert, an die der Dispatcher Anforderungen zum Rendern eines Dokuments sendet. Im folgenden Beispiel `/renders` wird eine einzelne AEM-Instanz für die Wiedergabe identifiziert:

```xml
/renders
  {
    /myRenderer
      {
      # hostname or IP of the renderer
      /hostname "aem.myCompany.com"
      # port of the renderer
      /port "4503"
      # connection timeout in milliseconds, "0" (default) waits indefinitely
      /timeout "0"
      }
  }
```

Im folgenden beispielhaften /renders-Abschnitt wird eine AEM-Instanz definiert, die auf demselben Computer wie der Dispatcher ausgeführt wird:

```xml
/renders
  {
    /myRenderer
     {
     /hostname "127.0.0.1"
     /port "4503"
     }
  }
```

Mit folgendem beispielhaften /renders-Abschnitt werden Renderanforderungen gleichmäßig auf zwei AEM-Instanzen verteilt:

```xml
/renders
  {
    /myFirstRenderer
      {
      /hostname "aem.myCompany.com"
      /port "4503"
      }
    /mySecondRenderer
      {
      /hostname "127.0.0.1"
      /port "4503"
      }
  }
```

### Renderoptionen {#renders-options}

**/timeout**

Gibt den Verbindungstimeout für die AEM-Instanz in Millisekunden an. Der Standardwert ist „0“, was bedeutet, dass der Dispatcher unendlich wartet.

**/receiveTimeout**

Gibt die Zeit in Millisekunden an, die eine Antwort annehmen darf. Der Standardwert ist „600000“, was bedeutet, dass der Dispatcher 10 Minuten wartet. Bei einem Wert von „0“ gilt kein Zeitlimit.\
Wenn die Zeitüberschreitung beim Analysieren der Antwortheader auftritt, wird der HTTP-Status 504 (Fehlerhaftes Gateway) zurückgegeben. Wenn die Zeitüberschreitung beim Lesen des Antworttexts auftritt, gibt der Dispatcher die unvollständige Antwort zwar an den Client zurück, löscht aber alle Cachedateien, die geschrieben wurden.

**/ipv4**

Gibt an, ob der Dispatcher die `getaddrinfo`-Funktion (für IPv6) oder die `gethostbyname`-Funktion (für IPv4) zum Abrufen der IP-Adresse des Renderers nutzt. Ein Wert von 0 bewirkt, dass `getaddrinfo` verwendet wird. Ein Wert von 1 bewirkt, dass `gethostbyname` verwendet wird. Der Standardwert ist 0.

Die getaddrinfo-Funktion gibt eine Liste der IP-Adressen zurück. Der Dispatcher durchläuft die Liste der Adressen, bis eine TCP/IP-Verbindung hergestellt ist. Daher ist die ipv4-Eigenschaft wichtig, wenn der Renderer-Hostname mit mehreren IP-Adressen verknüpft ist und der Host als Antwort auf die getaddrinfo-Funktion eine Liste mit IP-Adressen zurückgibt, die immer in derselben Reihenfolge aufgeführt sind. In diesem Fall sollten Sie die gethostbyname-Funktion verwenden, damit die IP-Adresse, mit der sich der Dispatcher verbindet, ein Zufallswert ist.

Amazon Elastic Load Balancing (ELB) ist ein Dienst, der auf „getaddrinfo“ mit einer potenziell gleich sortierten Liste von IP-Adressen reagiert.

**/secure**

Wenn die `/secure` Eigenschaft den Wert &quot;1&quot; hat, verwendet&quot; Dispatcher&quot; HTTPS zur Kommunikation mit der AEM-Instanz. Weitere Informationen finden Sie unter [Konfigurieren von Dispatcher für die Verwendung von SSL](dispatcher-ssl.md#configuring-dispatcher-to-use-ssl).

**/always-resolve**

Mit Dispatcher Version **4.1.6** können Sie die `/always-resolve` Eigenschaft wie folgt konfigurieren:

* Bei Festlegung auf &quot;1&quot; löst er den Hostnamen bei jeder Anforderung aus (der Dispatcher speichert keine IP-Adresse). Aufgrund des zusätzlichen Aufrufs, der erforderlich ist, um die Host-Informationen für jede Anforderung abzurufen, kann es zu einer leichten Leistungsbeeinträchtigung kommen.
* Wenn die Eigenschaft nicht festgelegt ist, wird die IP-Adresse standardmäßig zwischengespeichert.

Diese Eigenschaft kann auch verwendet werden, wenn Sie in dynamischen IP-Auflösungsproblemen ausgeführt werden, wie im folgenden Beispiel gezeigt:

```xml
/rend {
  /0001 {
     /hostname "host-name-here"
     /port "4502"
     /ipv4 "1"
     /always-resolve "1"
     }
  }
```

## Konfigurieren des Zugriffs auf Inhalte {#configuring-access-to-content-filter}

Verwenden Sie den `/filter` Abschnitt, um die HTTP-Anforderungen anzugeben, die Dispatcher akzeptiert. Alle anderen Anforderungen werden zum Webserver mit dem Fehlercode 404 (Seite nicht gefunden) zurückgesendet. Wenn kein `/filter` Abschnitt vorhanden ist, werden alle Anforderungen akzeptiert.

**Hinweis:** Anforderungen für die [Statfile](dispatcher-configuration.md#main-pars-title-28) werden immer abgelehnt.

>[!CAUTION]
>
>Weitere Überlegungen finden Sie in der [Dispatcher Security Checkliste](security-checklist.md) , wenn Sie den Zugriff mit Dispatcher einschränken. Lesen Sie außerdem die [AEM Security-Klickliste](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html) für weitere Sicherheitsdetails zu Ihrer AEM-Installation.

Der /filter-Abschnitt besteht aus einer Reihe von Regeln, die den Zugriff auf die Inhalte zulassen oder verweigern, wozu Muster in der Anforderungszeile der HTTP-Anforderung abgeglichen werden. Sie sollten einen Whitelist-Ansatz für den /filter-Abschnitt verwenden:

* Verweigern Sie zunächst den Zugriff auf alles.
* Erlauben Sie dann den Zugriff auf die relevanten Inhalte.

### Definieren eines Filters {#defining-a-filter}

Jedes Element im `/filter` Abschnitt enthält einen Typ und ein Muster, das mit einem bestimmten Element der Anforderungszeile oder der gesamten Anforderungszeile übereinstimmt. Jeder Filter kann die folgenden Elemente enthalten:

* **Typ**: Die Angabe `/type` gibt an, ob der Zugriff auf die Anforderungen, die dem Muster entsprechen, zulässig oder verweigern soll. Der Wert kann entweder `allow` oder `deny`.

* **Element der Anforderungszeile:** Einbeziehen `/method``/url`, `/query`oder `/protocol` ein Muster zum Filtern von Anforderungen gemäß diesen bestimmten Teilen des Anforderungsteil der HTTP-Anforderung. Das Filtern nach Elementen der Anforderungszeile (und nicht nach der gesamten Anforderungszeile) ist die bevorzugte Filtermethode.

* **glob-Eigenschaft**: Die `/glob` Eigenschaft wird mit der gesamten Anforderungs-Zeile der HTTP-Anforderung übereinstimmen.

Weitere Informationen zu /glob-Eigenschaften finden Sie unter [Entwerfen von Mustern für glob-Eigenschaften](#designing-patterns-for-glob-properties). Die Regeln für die Verwendung von Platzhalterzeichen in /glob-Eigenschaften gelten auch für die Muster zum Abgleichen der Elemente der Anforderungszeile.

>[!NOTE]
>
>Ab Dispatcher Version 4.2.0 wurden verschiedene Verbesserungen für Filterkonfigurationen und Protokollfunktionen hinzugefügt:
>
>* [Unterstützung für reguläre POSIX-Ausdrücke](dispatcher-configuration.md#main-pars-title-1996763852)
>* [Unterstützung für das Filtern von zusätzlichen Elementen der Anforderungs-URL](dispatcher-configuration.md#main-pars-title-694578373)
>* [Ablaufprotokollierung](dispatcher-configuration.md#main-pars-title-1950006642)
>



#### Anforderungszeilen von HTTP-Anforderungen {#the-request-line-part-of-http-requests}

HTTP/1.1 definiert die [Anforderungszeile](https://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html) wie folgt:

*Methode Anforderungs-URI HTTP-Version*&lt;CRLF&gt;

Bei den Zeichen &lt; CRLF &gt; wird ein Wagenrücklauf gefolgt von einem Zeilenfeed dargestellt. Im folgenden Beispiel wird die Anforderungszeile gezeigt, die empfangen wird, wenn ein Client die en-Seite der „Geometrixx-Outdoors“-Website anfordert:

GET /content/geometrixx-outdoors/en.html HTTP.1.1&lt;CRLF&gt;

Ihre Muster müssen die Leerzeichen in der Anforderungs-Zeile und die Zeichen &lt; CRLF &gt; berücksichtigen.

#### Beispielfilter: Alle verweigern {#example-filter-deny-all}

Bei folgendem beispielhaften Filterabschnitt verweigert der Dispatcher Anforderungen für alle Dateien. Sie können zunächst den Zugriff auf alle Dateien verweigern und dann den Zugriff auf bestimmte Bereiche zulassen.

```xml
  /0001  { /glob "*" /type "deny" }
```

Anforderungen für explizit verweigerte Bereiche führen zur Rückgabe des Fehlercodes 404 (Seite nicht gefunden).

#### Beispielfilter: Zugriff auf bestimmte Bereiche verweigern {#example-filter-deny-acess-to-specific-areas}

Mit Filtern können Sie den Zugriff auf bestimmte Elemente verweigern, z. B. ASP-Seiten und sensible Bereiche innerhalb einer Veröffentlichungsinstanz. Der folgende Filter verweigert den Zugriff auf ASP-Seiten:

```xml
/0002  { /type "deny" /url "*.asp"  }
```

#### Beispielfilter: Ermöglichen von POST-Anforderungen {#example-filter-enable-post-requests}

Mit dem folgenden Beispielfilter wird das Senden von Formulardaten mit der POST-Methode ermöglicht:

```xml
/filter {
    /0001  { /glob "*" /type "deny" }
    /0002 { /type "allow" /method "POST" /url "/content/[.]*.form.html" }
}
```

#### Beispielfilter: Ermöglichen des Zugriffs auf die Workflow-Konsole {#example-filter-allow-access-to-the-workflow-console}

Im folgenden Beispiel wird ein Filter gezeigt, mit dem der externe Zugriff auf die Workflow-Konsole ermöglicht wird:

```xml
/filter {
    /0001  { /glob "*" /type "deny" }
    /0002  {  /type "allow"  /url "/libs/cq/workflow/content/console*"  }
}
```

Wenn Ihre Veröffentlichungsinstanz einen Webanwendungskontext (z. B. „publish“) verwendet, kann er Ihrer Filterdefinition hinzugefügt werden.

```xml
/0003   { /type "deny"  /url "/publish/libs/cq/workflow/content/console/archive*"  }
```

Wenn Sie innerhalb eines eingeschränkten Bereichs trotzdem auf einzelne Seiten zugreifen möchten, können Sie den Zugriff auf diese zulassen. Um beispielsweise den Zugriff auf die Archivregisterkarte in der Workflow-Konsole zu ermöglichen, fügen Sie den folgenden Abschnitt hinzu:

```xml
/0004  { /type "allow"  /url "/libs/cq/workflow/content/console/archive*"   }
```

>[!NOTE]
>
>Wenn mehrere Filtermuster auf eine Anforderung zutreffen, wird das letzte passende Filtermuster verwendet.

#### Beispielfilter: Verwenden von regulären Ausdrücken {#example-filter-using-regular-expressions}

Dieser Filter ermöglicht mithilfe dieses regulären Ausdrucks (in einfachen Anführungszeichen) Erweiterungen in nicht öffentlichen Inhaltsordnern:

```xml
/005  {  /type "allow" /extension '(css|gif|ico|js|png|swf|jpe?g)' }
```

#### Beispielfilter: Filtern zusätzlicher Elemente einer Anforderungs-URL {#example-filter-filter-additional-elements-of-a-request-url}

Eine der in dispatcher 4.2.0 eingeführten Erweiterungen besteht darin, zusätzliche Elemente der Anforderungs-URL zu filtern. Diese Elemente sind folgende:

* path
* selectors
* extension
* suffix

Diese können durch Hinzufügen der Eigenschaft desselben Namens zur Filterregel konfiguriert werden: `/path``/selectors``/extension``/suffix` .

Im Folgenden finden Sie ein Regelbeispiel, das Inhalte aus dem `/content` Pfad und der Unterstruktur blockiert und Filter für Pfade, Selektoren und Erweiterungen verwendet:

```xml
/006 {
        /type "deny"
        /path "/content/*"
        /selectors '(feed|rss|pages|languages|blueprint|infinity|tidy)'
        /extension '(json|xml|html)'
        }
```

### Beispiel für einen /filter-Abschnitt {#example-filter-section}

Bei der Konfiguration des Dispatchers sollten Sie den externen Zugriff so stark wie möglich einschränken. Im folgenden Beispiel wird externen Besuchern ein minimaler Zugriff gewährt:

* `/content`
* Gemischter Inhalt, wie z. B. Designs und Clientbibliotheken. Beispiel:

   * `/etc/designs/default*`
   * `/etc/designs/mydesign*`

Nachdem Sie Filter erstellt haben, [testen Sie den Seitenzugriff](dispatcher-configuration.md#main-pars-title-19) , um sicherzustellen, dass die AEM-Instanz sicher ist.

Den folgenden /filter-Abschnitt der Datei „dispatcher.any“ können Sie in Ihrer [Dispatcher-Konfigurationsdatei](dispatcher-configuration.md) als Grundlage verwenden.

Dieses Beispiel beruht auf der Standardkonfigurationsdatei, die mit dem Dispatcher zur Verfügung gestellt wird, und dient als Beispiel für die Verwendung in einer Produktionsumgebung. Elemente, die mit # versehen wurden, werden deaktiviert (kommentiert). Wenn Sie sich entscheiden, diese zu aktivieren (indem Sie die # in dieser Zeile entfernen), wird eine Sicherheitsauswirkung erzielt.

Sie sollten zunächst den Zugriff auf alles verweigern und dann den Zugriff auf bestimmte Elemente zulassen:

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:32:37.986-0400

<p>We should mention the config files that are shipped with the dispatcher distribution and only give a few examples here. This aims to avoid confusion and reduce content maintenance.<br /> </p>

 -->

```xml
  /filter
      {
      # Deny everything first and then allow specific entries
      /0001 { /type "deny" /glob "*" }
      
      # Open consoles
#     /0011 { /type "allow" /url "/admin/*"  }  # allow servlet engine admin
#     /0012 { /type "allow" /url "/crx/*"    }  # allow content repository
#     /0013 { /type "allow" /url "/system/*" }  # allow OSGi console
        
      # Allow non-public content directories
#     /0021 { /type "allow" /url "/apps/*"   }  # allow apps access
#     /0022 { /type "allow" /url "/bin/*"    }
      /0023 { /type "allow" /url "/content*" }  # disable this rule to allow mapped content only
      
#     /0024 { /type "allow" /url "/libs/*"   }
#     /0025 { /type "deny"  /url "/libs/shindig/proxy*" } # if you enable /libs close access to proxy

#     /0026 { /type "allow" /url "/home/*"   }
#     /0027 { /type "allow" /url "/tmp/*"    }
#     /0028 { /type "allow" /url "/var/*"    }

      # Enable extensions in non-public content directories, using a regular expression
      /0041
        {
        /type "allow"
        /extension '(css|gif|ico|js|png|swf|jpe?g)'
        }

      # Enable features 
      /0062 { /type "allow" /url "/libs/cq/personalization/*"  }  # enable personalization

      # Deny content grabbing, on all accessible pages, using regular expressions
      /0081
        {
        /type "deny"
        /selectors '((sys|doc)view|query|[0-9-]+)'
        /extension '(json|xml)'
        }
      # Deny content grabbing for /content and its subtree
      /0082
        {
        /type "deny"
        /path "/content/*"
        /selectors '(feed|rss|pages|languages|blueprint|infinity|tidy)'
        /extension '(json|xml|html)'
        }

#     /0087 { /type "allow" /method "GET" /extension 'json' "*.1.json" }  # allow one-level json requests
}
```

>[!NOTE]
>
>Wenn Sie Apache verwenden, gestalten Sie Ihre Filter-URL-Muster entsprechend der Dispatcher UseProcessedURL-Eigenschaft des Dispatcher-Moduls. (Siehe [Apache Web Server - Konfigurieren des Apache Web Server für Dispatcher](dispatcher-install.md#main-pars-55-35-1022).)

>[!NOTE]
>
>Die Filter 0030 und 0031 für dynamische Medien gelten für AEM 6.0 und höher.

Berücksichtigen Sie die folgenden Hinweise, wenn Sie den Zugriff erweitern möchten:

* Der externe Zugriff sollte `/admin` immer *vollständig* deaktiviert werden, wenn Sie CQ Version 5.4 oder eine frühere Version verwenden.

* Es muss darauf geachtet werden, wenn der Zugriff auf Dateien zugelassen `/libs`wird. Der Zugriff sollte nur für bestimmte Personen möglich sein.
* Verweigern Sie den Zugriff auf die Replikationskonfiguration, damit sie nicht zu sehen ist:

   * `/etc/replication.xml*`
   * `/etc/replication.infinity.json*`

* Verweigern Sie den Zugriff auf den Reverse-Proxy von Google-Gadgets:

   * `/libs/opensocial/proxy*`

Je nach Installation kann es unter Umständen `/libs`zusätzliche Ressourcen geben, `/apps` die verfügbar gemacht werden müssen. Sie können die Datei `access.log` zum Ermitteln von Ressourcen verwenden, auf die extern zugegriffen wird.

>[!CAUTION]
>
>Der Zugriff auf Konsolen und Ordner kann ein Sicherheitsrisiko für Produktionsumgebungen darstellen. Sofern keine triftigen Gründe für den Zugriff vorliegen, sollte er deaktiviert bleiben (durch Auskommentierung).

>[!CAUTION]
>
>Wenn Sie Berichte [in einer Veröffentlichungsumgebung](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/reporting.html#UsingReportsinaPublishEnvironment) verwenden, sollten Sie Dispatcher konfigurieren, um den Zugriff `/etc/reports` auf externe Besucher verweigern zu können.

### Einschränken von Abfragezeichenfolgen {#restricting-query-strings}

Verwenden Sie seit der Dispatcher-Version 4.1.5 den `/filter` Abschnitt, um Abfragezeichenfolgen einzuschränken. Es wird dringend empfohlen, mit `allow`-Filterelementen bestimmte Abfragezeichenfolgen explizit zuzulassen und alle anderen zu verweigern.

Ein einzelner Eintrag kann *entweder einen Glob* oder eine Kombination aus *Methode*,*URL*,*Abfrage* und *Version* haben, nicht beides. Im folgenden Beispiel ist die `a=*` Abfragezeichenfolge zulässig und alle anderen Abfragezeichenfolgen für urls, die auf den `/etc` Knoten aufgelöst werden, werden abgeblendet:

```xml
/filter {
 /0001 { /type "deny" /method "POST" /url "/etc/*" }
 /0002 { /type "allow" /method "GET" /url "/etc/*" /query "a=*" }
}
```

>[!NOTE]
>
>Wenn eine Regel A `/query`enthält, stimmt sie nur mit Anforderungen überein, die eine Abfragezeichenfolge enthalten und dem bereitgestellten Abfragemuster entsprechen.
>
>Wenn in einem Beispiel Anforderungen ohne `/etc` Abfragezeichenfolge ebenfalls zulässig sein sollten, sind die folgenden Regeln erforderlich:


```xml
/filter {  
>/0001 { /type "deny" /method “*" /url "/path/*" }  
>/0002 { /type "allow" /method "GET" /url "/path/*" }  
>/0003 { /type “deny" /method "GET" /url "/path/*" /query "*" }  
>/0004 { /type "allow" /method "GET" /url "/path/*" /query "a=*" }  
}  
```

### Testen der Dispatcher-Sicherheit {#testing-dispatcher-security}

Dispatcher-Filter sollten den Zugriff auf folgende Seiten und Skripts auf AEM-Veröffentlichungsinstanzen nicht zulassen. Öffnen Sie diese Seiten in einem Webbrowser, als wären Sie ein Besucher, und stellen Sie sicher, dass der Code 404 zurückgegeben wird. Passen Sie die Filter an, wenn ein anderes Ergebnis angezeigt wird.

Bei /content/add_valid_page.html?debug=layout sollte eine normale Seitendarstellung zu sehen sein.


* /admin
* /system/console
* /dav/crx.default
* /crx
* /bin/crxde/logs
* /jcr:system/jcr:versionStorage.json
* /_jcr_system/_jcr_versionStorage.json
* /libs/wcm/core/content/siteadmin.html
* /libs/collab/core/content/admin.html
* /libs/cq/ui/content/dumplibs.html
* /var/linkchecker.html
* /etc/linkchecker.html
* /home/users/a/admin/profile.json
* /home/users/a/admin/profile.xml
* /libs/cq/core/content/login.json
* /content/../libs/foundation/components/text/text.jsp
* /content/.{.}/libs/foundation/components/text/text.jsp
* /apps/sling/config/org.apache.felix.webconsole.internal.servlet.OsgiManager.config/jcr%3acontent/jcr%3adata
* /libs/foundation/components/primary/cq/workflow/components/participants/json.GET.servlet
* /content.pages.json
* /content.languages.json
* /content.blueprint.json
* /content.-1.json
* /content.10.json
* /content.infinity.json
* /content.tidy.json
* /content.tidy.-1.blubber.json
* /content/dam.tidy.-100.json
* /content/content/geometrixx.sitemap.txt
* /content/add_valid_page.query.json?statement=//*
* /content/add_valid_page.qu%65ry.js%6Fn?statement=//*
* /content/add_valid_page.query.json? statement =//*[@ transporpassword]/(@ transporpassword % 20|% 20@transportUri % 20|% 20@transportUser)
* /content/add_valid_path_to_a_page/_jcr_content.json
* /content/add_valid_path_to_a_page/jcr:content.json
* /content/add_valid_path_to_a_page/_jcr_content.feed
* /content/add_valid_path_to_a_page/jcr:content.feed
* /content/add_valid_path_to_a_page/pagename._jcr_content.feed
* /content/add_valid_path_to_a_page/pagename.jcr:content.feed
* /content/add_valid_path_to_a_page/pagename.docview.xml
* /content/add_valid_path_to_a_page/pagename.docview.json
* /content/add_valid_path_to_a_page/pagename.sysview.xml
* /etc.xml
* /content.feed.xml
* /content.rss.xml
* /content.feed.html
* /content/add_valid_page.html?debug=layout
* /projects
* /tagging
* /etc/replication.html
* /etc/cloudservices.html
* /Willkommen

Geben Sie den folgenden Befehl in einem Terminalfenster oder in einer Eingabeaufforderung ein, um zu prüfen, ob der anonyme Schreibzugriff aktiviert ist. Es sollte nicht möglich sein, Daten in den Knoten zu schreiben.

`curl -X POST "https://anonymous:anonymous@hostname:port/content/usergenerated/mytestnode"`

Geben Sie den folgenden Befehl in einem Terminalfenster oder in einer Eingabeaufforderung ein, um den Dispatcher-Cache ungültig zu machen, und stellen Sie sicher, dass als Antwort der Code 404 zu sehen ist:

`curl -H "CQ-Handle: /content" -H "CQ-Path: /content" https://yourhostname/dispatcher/invalidate.cache`

## Zugriff auf Vanity urls aktivieren {#enabling-access-to-vanity-urls-vanity-urls}

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (sbroders@adobe.com)
Last Modified Date: 2015-03-25T14:23:05.185-0400

<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">For https://jira.corp.adobe.com/browse/DOC-4812</p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">The com.adobe.granite.dispatcher.vanityurl.content package needs to be made public before publishing this contnet.</p>

 -->

Sie können den Dispatcher so konfigurieren, dass der Zugriff auf Vanity-URLs möglich ist, die für Ihre CQ- oder AEM-Seiten konfiguriert wurden.

Wenn der Zugriff auf die Vanity-URLs aktiviert ist, ruft der Dispatcher regelmäßig einen Dienst auf der Renderer-Instanz auf, um eine Liste der Vanity-URLs zu erhalten. Der Dispatcher speichert diese Liste in einer lokalen Datei. Wenn eine Anforderung für eine Seite aufgrund eines Filters im `/filter` Abschnitt abgelehnt wird, wird Dispatcher die Liste der Vanity-urls-Adressen angezeigt. Befindet sich die abgelehnte URL in der Liste, kann der Dispatcher den Zugriff auf diese Vanity-URL zulassen.

Um den Zugriff auf Vanity-urls zu aktivieren, fügen Sie dem Abschnitt einen `/vanity_urls``/farms` Abschnitt hinzu, ähnlich dem folgenden Beispiel:

```xml
 /vanity_urls {
      /url "/libs/granite/dispatcher/content/vanityUrls.html"
      /file "/tmp/vanity_urls"
      /delay 300
 }
```

Der `/vanity_urls` Abschnitt enthält die folgenden Eigenschaften:

* `/url`: Der Pfad zum Vanity-URL-Dienst, der auf der Renderer-Instanz ausgeführt wird. Der Wert dieser Eigenschaft muss `"/libs/granite/dispatcher/content/vanityUrls.html"`sein.

* `/file`: Der Pfad zur lokalen Datei, in der der Dispatcher die Liste der Vanity-URLs speichert. Vergewissern Sie sich, dass der Dispatcher über Schreibzugriff auf diese Datei verfügt.
* `/delay`: (Sekunden) Die Zeit zwischen den einzelnen Aufrufen des Vanity-URL-Diensts.

>[!NOTE]
>
>Wenn Ihr Rendervorgang eine Instanz von AEM ist, müssen Sie das [vanityurls-Components](https://www.adobeaemcloud.com/content/marketplace/marketplaceProxy.html?packagePath=/content/companies/public/adobe/packages/cq600/component/vanityurls-components) -Paket installieren, um den Vanity-URL-Dienst zu installieren. (Siehe [Bei Package Share anmelden](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/package-manager.html#SigningIntoPackageShare).)

Führen Sie die folgenden Schritte aus, um den Zugriff auf Vanity-URLs zu aktivieren.

1. Wenn Ihr Render-Dienst eine AEM-Instanz ist, installieren Sie das Paket com. adobe. granite. dispatcher. vanityurl. content auf der Veröffentlichungsinstanz (siehe Hinweis oben).
1. Stellen Sie für jede Vanity-URL, die Sie für eine AEM- oder CQ-Seite konfiguriert haben, sicher, dass die ` [/filter](dispatcher-configuration.md#main-pars_134_32_0009)` Konfiguration die URL angibt. Fügen Sie ggf. einen Filter hinzu, durch den die URL verweigert wird.
1. Fügen Sie den `/vanity_urls` folgenden Abschnitt hinzu `/farms`.
1. Starten Sie den Apache-Webserver neu.

## Weiterleiten von Syndizierungsanforderungen – /propagateSyndPost {#forwarding-syndication-requests-propagatesyndpost}

Syndizierungsanforderungen beziehen sich normalerweise nur auf den Dispatcher, sodass sie standardmäßig nicht an den Renderer (z. B. eine AEM-Instanz) gesendet werden.

Falls erforderlich, können Sie die /propagateSyndPost-Eigenschaft auf „1“ festlegen, damit Syndizierungsanforderungen an den Dispatcher weitergeleitet werden. Wenn festgelegt, müssen Sie sicherstellen, dass POST-Anforderungen im Filterabschnitt nicht abgelehnt werden.

## Konfigurieren des Dispatcher-Cache – /cache {#configuring-the-dispatcher-cache-cache}

Der `/cache` Abschnitt steuert, wie Dispatcher Dokumente zwischenspeichert. Konfigurieren Sie Untereigenschaften, um die Zwischenspeicherung nach Ihren Vorstellungen zu implementieren:


* /docroot
* /statfile
* /serveStaleOnError
* /allowAuthorized
* /rules
* /statfileslevel
* /invalidate
* /invalidateHandler
* /allowedClients
* /ignoreUrlParams
* /headers
* /mode
* /gracePeriod


Ein Cacheabschnitt kann beispielsweise wie folgt aussehen:

```xml
/cache
  {
  /docroot "/opt/dispatcher/cache"
  /statfile  "/tmp/dispatcher-website.stat"          
  /allowAuthorized "0"
      
  /rules
    {
    # List of files that are cached
    }

  /invalidate
    {
    # List of files that are auto-invalidated
    }
  }
  
```

>[!NOTE]
>
>Informationen zum berechtigungsbezogenen Zwischenspeichern finden Sie unter [Zwischenspeichern von geschütztem Inhalt](permissions-cache.md).

### Festlegen des Cacheordners {#specifying-the-cache-directory}

Die `/docroot` Eigenschaft identifiziert den Ordner, in dem zwischengespeicherte Dateien gespeichert werden.

>[!NOTE]
>
>Der Wert muss dem Pfad des Basisverzeichnisses des Webservers entsprechen, damit der Dispatcher und der Webserver dieselben Dateien verarbeiten.\
>Der Webserver ist für die Bereitstellung des richtigen Statuscodes für die Dispatcher-Cachedateien zuständig und muss daher auf sie zugreifen können.

Wenn Sie mehrere Bausteine verwenden, muss jede Farm einen anderen Dokumenstamm verwenden.

### Benennen der Statfile {#naming-the-statfile}

Die `/statfile` Eigenschaft identifiziert die Datei, die als Statusdatei verwendet werden soll. Der Dispatcher verwendet diese Datei, um den Zeitpunkt der letzten Inhaltsaktualisierung zu protokollieren. Die Statfile kann jede beliebige Datei auf dem Webserver sein.

Die Statfile hat keinen Inhalt. Nach einer Inhaltsaktualisierung ändert der Dispatcher lediglich ihren Zeitstempel. Die Standardstatusdatei lautet &quot;. stat&quot; und wird im docroot gespeichert. Der Dispatcher blockiert den Zugriff auf die Statfile.

>[!NOTE]
>
>Wenn `/statfileslevel` sie konfiguriert ist, ignoriert Dispatcher die `/statfile` Eigenschaft und verwendet. stat als Name.

### Bereitstellen veralteter Dokumente, wenn Fehler auftreten {#serving-stale-documents-when-errors-occur}

Die `/serveStaleOnError` Eigenschaft steuert, ob Dispatcher ungültige Dokumente zurückgibt, wenn der Render-Server einen Fehler zurückgibt. Wenn eine Statfile geändert wird und zwischengespeicherten Inhalt ungültig macht, löscht der Dispatcher standardmäßig den zwischengespeicherten Inhalt bei der nächsten Anforderung.

Wenn `/serveStaleOnError` auf &quot;1&quot; gesetzt, löscht Dispatcher keine ungültigen Inhalte aus dem Cache, es sei denn, der Renderserver gibt eine erfolgreiche Antwort zurück. Eine 5 xx-Antwort von AEM oder einer Verbindungszeitüberschreitung führt dazu, dass Dispatcher den veralteten Inhalt bereitstellt und mit dem und den HTTP-Status 111 (Erneut Überprüfung fehlgeschlagen) antwortet.

### Zwischenspeicherung bei Verwendung von Authentifizierung {#caching-when-authentication-is-used}

Die `/allowAuthorized` Eigenschaft steuert, ob Anforderungen, die eine der folgenden Authentifizierungsinformationen enthalten, zwischengespeichert werden:

* Die `authorization` Kopfzeile.
* Ein Cookie namens `authorization`.
* Ein Cookie namens `login-token`.

Standardmäßig werden Anforderungen, die diese Authentifizierungsinformationen enthalten, nicht zwischengespeichert, da bei der Rückgabe eines zwischengespeicherten Dokuments an den Client keine Authentifizierung durchgeführt wird. Diese Konfiguration verhindert, dass der Dispatcher zwischengespeicherte Dokumente Benutzern bereitstellt, die nicht über die erforderlichen Rechte verfügen.

Wenn Sie das Zwischenspeichern von authentifizierten Dokumenten jedoch erlauben möchten, legen Sie „/allowAuthorized“ auf „1“ fest:

`/allowAuthorized "1"`

>[!NOTE]
>
>Zur Aktivierung der Sitzungsverwaltung (mithilfe der `/sessionmanagement` Eigenschaft) muss die `/allowAuthorized` Eigenschaft auf `"0"`festgelegt werden.

### Festlegen der Dokumente, die zwischengespeichert werden sollen {#specifying-the-documents-to-cache}

Die `/rules` Eigenschaft steuert, welche Dokumente gemäß dem Dokumentpfad zwischengespeichert werden. Unabhängig von der /rules-Eigenschaft werden in folgenden Fällen Dokumente nie zwischengespeichert: 

* Der Anforderungs-URI enthält ein Fragezeichen („?“).\
   Hierdurch wird normalerweise eine dynamische Seite angegeben (z. B. ein Suchergebnis), die nicht zwischengespeichert werden muss.
* Die Dateierweiterung fehlt.\
   Der Webserver benötigt die Erweiterung, um den Dokumenttyp (den MIME-Typ) zu bestimmen.
* Der Authentifizierungsheader wurde festgelegt (dies kann konfiguriert werden).
* Die AEM-Instanz antwortet mit folgenden Headern:

   * `no-cache`
   * `no-store`
   * `must-revalidate`

>[!NOTE]
>
>Die GET- oder HEAD-Methoden (für HTTP-Header) sind vom Dispatcher cache bar. Weitere Informationen zur Zwischenspeicherung der Antwortkopfzeile finden Sie im Abschnitt [Zwischenspeichern von HTTP-Antwort-Kopfzeilen](dispatcher-configuration.md#caching-http-response-headers) .

Jedes Element in der /rules enthält ein [Glob](#designing-patterns-for-glob-properties) -Muster und einen Typ:

* Das Glob-Muster wird verwendet, um mit dem Pfad des Dokuments zu übereinstimmen.
* Der Typ gibt an, ob die Dokumente zwischengespeichert werden sollen, die dem Glob-Muster entsprechen. Der Wert kann entweder zulässig sein (zum Zwischenspeichern des Dokuments) oder ablehnen (um das Dokument immer wiederzugeben).

Wenn Sie keine dynamischen Seiten haben (abgesehen von denen, die durch die oben genannten Regeln sowieso ignoriert werden), können Sie den Dispatcher so konfigurieren, dass alles zwischengespeichert wird. Der /rules-Abschnitt sieht dann wie folgt aus:

```xml
/rules
  { 
    /0000  {  /glob "*"   /type "allow" }
  }
```

Weitere Informationen zu Glob-Eigenschaften finden Sie unter [Entwerfen von Mustern für Glob-Eigenschaften](#designing-patterns-for-glob-properties).

Für dynamische Bereiche Ihrer Seite (z. B. eine Anwendung für Neuigkeiten) oder für geschlossene Benutzergruppen können Sie Ausnahmen definieren:

>[!NOTE]
>
>Geschlossene Benutzergruppen dürfen nicht zwischengespeichert werden, da die Benutzerberechtigungen bei zwischengespeicherten Seiten nicht geprüft werden.

```xml
/rules
  {
   /0000  { /glob "*" /type "allow" }
   /0001  { /glob "/en/news/*" /type "deny" }
   /0002  { /glob "*/private/*" /type "deny"  }   
  }
```

**Komprimierung**

Auf Apache -Webservern können Sie die im Cache gespeicherten Dokumente komprimieren. Mit der Komprimierung kann Apache das Dokument in einem komprimierten Formular zurückgeben, wenn es vom Client angefordert wird. Die Komprimierung erfolgt automatisch, indem das Apache-Modul `mod_deflate`aktiviert wird, zum Beispiel:

```xml
AddOutputFilterByType DEFLATE text/plain
```

Das Modul wird standardmäßig mit Apache 2. x installiert.

<!-- 

Comment Type: draft

<note type="note"> 
 <p>Depending on the Apache web server version you can enable <span class="code">gzip</span> compression as follows:</p> 
 <ul> 
  <li>For Apache 1.3 you can enable the <span class="code">mod_gzip </span>module. The module must be downloaded and installed.</li> 
  <li>For Apache 2.x you can enable the <span class="code">mod_deflate</span> module. The module is installed by default with Apache 2.x.<br /> </li> 
 </ul> 
 <p> </p> 
</note>

 -->

<!-- 

Comment Type: draft

<p>The following rule caches all documents in compressed form; Apache can return either the uncompressed or the compressed form to the client:</p>

 -->

<!-- 

Comment Type: draft

<codeblock gutter="true" class="syntax xml">
  /rules!!discoiqbr!!&nbsp;&nbsp;{!!discoiqbr!!&nbsp;&nbsp;&nbsp;/rulelabel&nbsp;&nbsp;{&nbsp;&nbsp;/glob&nbsp;"*"&nbsp;/type&nbsp;"allow"&nbsp;&nbsp;/compress&nbsp;"gzip"&nbsp;}!!discoiqbr!!&nbsp;&nbsp;} 
</codeblock>

 -->

<!-- 

Comment Type: remark
Last Modified By: Silviu Raiman (raiman)
Last Modified Date: 2017-11-13T09:23:24.326-0500

<p>Hidden the <span class="code">mod_gzip</span> content as requested in CQDOC-11124.</p>

 -->

### Invalidierung von Dateien nach Ordnerebene {#invalidating-files-by-folder-level}

Verwenden Sie die `/statfileslevel` Eigenschaft, um zwischengespeicherte Dateien gemäß ihrem Pfad ungültig zu machen:

* Dispatcher erstellt `.stat`Dateien in jedem Ordner aus dem docroot-Ordner zu der angegebenen Ebene. Das Basisverzeichnis hat die Ordnerebene 0.
* Dateien werden durch Berühren der `.stat` Datei ungültig. Das Datum der letzten Änderung `.stat` der Datei wird mit dem Datum der letzten Änderung eines zwischengespeicherten Dokuments verglichen. Das Dokument wird erneut abgerufen, wenn die `.stat` Datei höher ist.

* Wenn eine Datei auf einer bestimmten Ebene ungültig ist, **werden alle** `.stat` Dateien aus dem docroot **zur** Ebene der ungültigen Datei oder der konfigurierten `statsfilevel` (je nachdem, was kleiner ist).

   * Beispiel: Wenn Sie die `statfileslevel` Eigenschaft auf 6 einstellen und eine Datei auf Ebene 5 ungültig ist, wird jede `.stat` Datei von docroot zu 5 weitergeleitet. Fahren Sie mit diesem Beispiel fort, wenn eine Datei auf Ebene 7 ungültig ist, dann alle. `stat` die Datei von docroot to 6 (seit), wird weiter berührt (da `/statfileslevel = "6"`).

Davon sind nur Ressourcen** entlang des Pfades** zu der ungültigen Datei betroffen. Betrachten Sie das folgende Beispiel: Eine Website verwendet die Struktur `/content/myWebsite/xx/.` , Wenn Sie `statfileslevel` als 3 festgelegt ist, wird eine `.stat`Datei wie folgt erstellt:

* `docroot`
* `/content`
* `/content/myWebsite`
* `/content/myWebsite/*xx*`

Wenn eine Datei `/content/myWebsite/xx` ungültig ist, wird jede `.stat` Datei von docroot nach unten `/content/myWebsite/xx`verschoben. Dies wäre nur für `/content/myWebsite/xx` und nicht zum Beispiel `/content/myWebsite/yy` der Fall oder `/content/anotherWebSite`.

>[!NOTE]
>
>Die Ungültigmachung kann durch Senden einer zusätzlichen Kopfzeile verhindert `CQ-Action-Scope:ResourceOnly`werden. Dies kann zum Bereinigen bestimmter Ressourcen verwendet werden, ohne dass andere Teile des Cache ungültig werden. Weitere Details finden Sie auf [dieser Seite](https://adobe-consulting-services.github.io/acs-aem-commons/features/dispatcher-flush-rules/index.html) und unter [Manuelles Ungültigmachen des Dispatcher-Cache](https://helpx.adobe.com/experience-manager/dispatcher/using/page-invalidate.html) .

>[!NOTE]
>
>Wenn Sie einen Wert für die `/statfileslevel` Eigenschaft angeben, wird die `/statfile` Eigenschaft ignoriert.

### Automatische Invalidierung zwischengespeicherter Dateien {#automatically-invalidating-cached-files}

Die `/invalidate` Eigenschaft definiert die Dokumente, die beim Aktualisieren des Inhalts automatisch ungültig werden.

Bei der automatischen Invalidierung löscht der Dispatcher die im Cache gespeicherten Dateien nach einer Inhaltsaktualisierung nicht, prüft aber ihre Gültigkeit, wenn sie das nächste Mal angefordert werden. Dokumente im Cache, die nicht automatisch ungültig gemacht werden, verbleiben im Cache, bis sie durch eine Inhaltsaktualisierung explizit gelöscht werden.

Die automatische Invalidierung wird in der Regel für HTML-Seiten verwendet. HTML-Seiten enthalten oft Links zu anderen Seiten, sodass nicht immer klar ist, ob sich eine Inhaltsaktualisierung auf eine Seite auswirkt. Um sicherzustellen, dass alle relevanten Seiten beim Aktualisieren des Inhalts ungültig sind, müssen Sie alle HTML-Seiten automatisch ungültig machen. Mit der folgenden Konfiguration werden alle HTML-Seiten ungültig gemacht:

```xml
  /invalidate
  {
   /0000  { /glob "*" /type "deny" }
   /0001  { /glob "*.html" /type "allow" }
  }
```

Weitere Informationen zu Glob-Eigenschaften finden Sie unter [Entwerfen von Mustern für Glob-Eigenschaften](#designing-patterns-for-glob-properties).

Diese Konfiguration führt zur folgenden Aktivität, wenn /content/geometrixx/en aktiviert ist:

* Alle Dateien mit dem Muster „de.*“ werden aus dem /content/geometrixx/-Ordner entfernt.
* Der Ordner /content/geometrixx/en/_jcr_content wird entfernt.
* Alle anderen Dateien, die mit der /invalidate-Konfiguration übereinstimmen, werden nicht sofort gelöscht. Diese Dateien werden bei der nächsten Anforderung gelöscht. In unserem Beispiel wird „/content/geometrixx.html“ nicht sofort gelöscht, sondern erst bei Anforderung von „/content/geometrixx.html“.

Wenn Sie automatisch generierte PDF-Dateien und ZIP-Dateien zum Download anbieten, müssen Sie diese möglicherweise auch automatisch ungültig machen. Eine mögliche Konfiguration sieht wie folgt aus:

```xml
/invalidate
  {
   /0000 { /glob "*" /type "deny" }
   /0001 { /glob "*.html" /type "allow" }
   /0002 { /glob "*.zip" /type "allow" }
   /0003 { /glob "*.pdf" /type "allow" }
  }
```

Die AEM-Integration in Adobe Analytics liefert Konfigurationsdaten in einer analytics.sitecatalyst.js-Datei innerhalb Ihrer Website. Die beispielhafte dispatcher.any-Datei, die mit dem Dispatcher bereitgestellt wird, enthält die folgende Regel zur Invalidierung für diese Datei:

```xml
{
   /glob "*/analytics.sitecatalyst.js"  /type "allow"
}
```

### Verwenden von benutzerdefinierten Skripts zur Invalidierung {#using-custom-invalidation-scripts}

Die /invalidateHandler-Eigenschaft ermöglicht es Ihnen, ein Skript zu definieren, das bei jeder empfangenen Anforderung zur Invalidierung ausgeführt wird.

Es wird mit folgenden Argumenten aufgerufen:

* Ziehgriff\
    Der Inhaltspfad, der ungültig gemacht wird.
* Aktion\
   Die Replizierungsaktion (z. B. Aktivieren, Deaktivieren)
* Aktionsbereich\
   Der Umfang der Replizierungsaktion (leer, es sei denn, eine Kopfzeile von `CQ-Action-Scope: ResourceOnly` wird gesendet, siehe [Ungültige zwischengespeicherte Seiten aus AEM](page-invalidate.md) für weitere Informationen)

Dies kann verwendet werden, um verschiedene Anwendungsfälle abzudecken, wie z. B. die Invalidierung anderer anwendungsspezifischer Caches, oder für Fälle, in denen die externalisierte URL einer Seite und ihre Position im Basisverzeichnis nicht mit dem Inhaltspfad übereinstimmen.

Beim Beispielskript unten wird jede Anforderung für die Invalidierung in einer Datei protokolliert.

```xml
/invalidateHandler "/opt/dispatcher/scripts/invalidate.sh"
```

#### Beispielskript für Invalidierung {#sample-invalidation-handler-script}

```shell
#!/bin/bash

printf "%-15s: %s %s" $1 $2 $3>> /opt/dispatcher/logs/invalidate.log
```

### Eingrenzen der Clients, die den Cache leeren können {#limiting-the-clients-that-can-flush-the-cache}

Mit der /allowedClients-Eigenschaft werden bestimmte Clients definiert, die den Cache leeren können. Die globbing-Muster werden mit der IP-Adresse abgeglichen.

Im folgenden Beispiel:

1. wird der Zugriff auf jeden Client verweigert.
1. wird der Zugriff auf „localhost“ ausdrücklich zugelassen.

```xml
/allowedClients
  {
   /0001 { /glob "*.*.*.*"  /type "deny" }
   /0002 { /glob "127.0.0.1" /type "allow" }
  }
```

Weitere Informationen zu Glob-Eigenschaften finden Sie unter [Entwerfen von Mustern für Glob-Eigenschaften](#designing-patterns-for-glob-properties).

>[!CAUTION]
>
>Es wird empfohlen, dass Sie „/allowedClients“ definieren.
>
>Tun Sie dies nicht, kann jeder Client einen Aufruf zum Leeren des Caches ausgeben. Wenn dies wiederholt geschieht, kann dadurch die Site-Leistung stark beeinträchtigt werden.

### Ignorieren von URL-Parametern {#ignoring-url-parameters}

Im `ignoreUrlParams`-Abschnitt wird definiert, welche URL-Parameter bei der Prüfung ignoriert werden sollen, ob eine Seite zwischengespeichert wird oder aus dem Cache geliefert wird:

* Wenn eine Anforderungs-URL Parameter enthält, die alle ignoriert werden, wird die Seite zwischengespeichert.
* Wenn eine Anforderungs-URL mindestens einen Parameter enthält, der nicht ignoriert wird, wird die Seite nicht zwischengespeichert.

Wenn ein Parameter für eine Seite ignoriert wird, wird die Seite zwischengespeichert, wenn sie zum ersten Mal angefordert wird. Bei nachfolgenden Anforderungen der Seite wird die zwischengespeicherte Seite geliefert, unabhängig vom Wert des Parameters in der Anforderung.

Um festzulegen, welche Parameter ignoriert werden, fügen Sie der `ignoreUrlParams`-Eigenschaft glob-Regeln hinzu:

* Um einen Parameter zu ignorieren, erstellen Sie eine glob-Eigenschaft, die den Parameter zulässt.
* Damit die Seite nicht zwischengespeichert wird, erstellen Sie eine glob-Eigenschaft, die den Parameter verweigert.

Bei folgendem Beispiel ignoriert der Dispatcher den Parameter „q“, sodass URL-Anforderungen mit diesem Parameter zwischengespeichert werden:

```xml
/ignoreUrlParams
{
    /0001 { /glob "*" /type "deny" }
    /0002 { /glob "q" /type "allow" }
}
```

Mit dem Beispielwert `ignoreUrlParams` sorgt die folgende HTTP-Anforderung dafür, dass die Seite zwischengespeichert wird, da der `q` Parameter ignoriert wird:

```xml
GET /mypage.html?q=5
```

Mit dem Beispielwert `ignoreUrlParams` sorgt die folgende HTTP-Anforderung dafür, dass die Seite **nicht** zwischengespeichert wird, da der `p` Parameter nicht ignoriert wird:

```xml
GET /mypage.html?q=5&p=4
```

Weitere Informationen zu Glob-Eigenschaften finden Sie unter [Entwerfen von Mustern für Glob-Eigenschaften](#designing-patterns-for-glob-properties).

### Zwischenspeichern von HTTP-Antwortheadern {#caching-http-response-headers}

>[!NOTE]
>
>Diese Funktion ist in Version **4.1.11** des Dispatchers verfügbar.

Mit dieser `/headers` Eigenschaft können Sie die HTTP-Header-Typen definieren, die vom Dispatcher zwischengespeichert werden sollen. Bei der ersten Anforderung an eine nicht zwischengespeicherte Ressource werden alle Header, die einer der konfigurierten Werte entsprechen (siehe Konfigurationsmuster unten) in einer separaten Datei neben der Cache-Datei gespeichert. Bei nachfolgenden Anforderungen an die zwischengespeicherte Ressource werden die gespeicherten Header der Antwort hinzugefügt.

Im Folgenden finden Sie ein Beispiel aus der Standardkonfiguration:

```xml
/cache {
  ...
  /headers {
    "Cache-Control"
    "Content-Disposition"
    "Content-Type"
    "Expires"
    "Last-Modified"
    "X-Content-Type-Options"
    "Last-Modified"
  }
}
```

>[!NOTE]
>
>Beachten Sie außerdem, dass Datei-Globbing-Zeichen nicht zulässig sind. Weitere Informationen finden Sie unter [Entwerfen von Mustern für Glob-Eigenschaften](#designing-patterns-for-glob-properties).

>[!NOTE]
>
>Wenn Sie Dispatcher benötigen, um etag-Antwortkopfzeilen aus AEM zu speichern und bereitzustellen, führen Sie folgende Schritte aus:
>
>* Fügen Sie den Kopfzeilennamen im `/cache/headers`Abschnitt hinzu.
>* Fügen Sie im Abschnitt Dispatcher verwandte [Apache-Richtlinie](https://httpd.apache.org/docs/2.4/mod/core.html#fileetag) hinzu:
>



```xml
FileETag none
```

### Dispatcher-Cache-Dateiberechtigungen {#dispatcher-cache-file-permissions}

Die `mode` Eigenschaft gibt an, welche Dateiberechtigungen auf neue Ordner und Dateien im Cache angewendet werden. Diese Einstellung wird durch den `umask` Aufruf des aufrufenden Prozesses eingeschränkt. Es handelt sich um eine Oktalzahl, die aus der Summe eines oder mehrerer der folgenden Werte erstellt wird:

* 0400 Schreibweise nach Inhaber zulassen.
* 0200 Schreibweise nach Inhaber zulassen.
* 0100 Der Eigentümer darf in Ordnern suchen.
* 0040 Schreibweise nach Gruppenmitgliedern zulassen.
* 0020 Schreiben Sie nach Gruppenmitgliedern.
* 0010 Gruppenmitglieder können im Ordner suchen.
* 0004 Allow read by others.
* 0002 Schreibweise durch andere zulassen.
* 0001 Zulassen, dass andere im Ordner suchen.

Der Standardwert ist 0755, mit dem der Inhaber das Lesen, Schreiben oder Suchen sowie die Gruppe und andere zum Lesen oder Suchen ermöglicht.

### &quot; Throttling. stat&quot; -Datei berühren {#throttling-stat-file-touching}

Mit der Standardeigenschaft `/invalidate` ungültig jede Aktivierung effektiv alle `.html` Dateien (wenn ihr Pfad mit dem `/invalidate` Abschnitt übereinstimmt). Auf einer Website mit hohem Traffic und hohem Traffic erhöht sich die CPU-Auslastung auf dem Backend. In einem solchen Fall wäre es wünschenswert, `.stat` Dateiberühren zu &quot;einschränken&quot; , damit die Website reagiert. Dazu verwenden Sie die `/gracePeriod` Eigenschaft.

Die `/gracePeriod` Eigenschaft definiert die Anzahl der Sekunden, die ein statisches Element nach der letzten Aktivierung vom Cache nach wie vor aus dem Cache bereitstellt. Die Eigenschaft kann in einer Einrichtung verwendet werden, bei der ein Stapel von Aktivierungen andernfalls den gesamten Cache wiederholt ungültig macht. Der empfohlene Wert beträgt 2 Sekunden.

Weitere Details finden Sie `/invalidate``/statfileslevel`weiter oben.

## Konfigurieren der zeitbasierten Cache-Invalidierung – /enableTTL {#configuring-time-based-cache-invalidation-enablettl}

Mit der `enableTTL`-Eigenschaft werden die Antwortheader aus dem Backend ausgewertet und wenn sie ein maximales Alter vom Typ `Cache-Control` oder ein Datum vom Typ `Expires` enthalten, wird eine leere Hilfsdatei neben der Cachedatei erstellt, deren Änderungszeitpunkt dem Ablaufdatum entspricht. Wenn die zwischengespeicherte Datei nach dem Änderungszeitpunkt angefordert wird, wird sie automatisch erneut aus dem Backend angefordert.

Sie können diese Funktion aktivieren, indem Sie folgende Zeile in die Datei `dispatcher.any` einfügen:

```xml
/enableTTL "1"
```

>[!NOTE]
>
>Diese Funktion ist in Version **4.1.11** des Dispatchers verfügbar.

## Konfigurieren des Lastenausgleichs – /statistics {#configuring-load-balancing-statistics}

Der `/statistics` Abschnitt definiert Kategorien von Dateien, für die Dispatcher die Reaktionsfähigkeit jedes Rendervorgangs auswählt. Der Dispatcher nutzt diese Bewertung, um festzulegen, an welchen Renderer welche Anforderung gesendet wird.

Jede Kategorie, die Sie erstellen, definiert ein glob-Muster. Der Dispatcher vergleicht den URI des angeforderten Inhalts mit diesen Mustern, um die Kategorie des angeforderten Inhalts zu ermitteln:

* Durch die Reihenfolge der Kategorien wird festgelegt, in welcher Reihenfolge sie mit dem URI verglichen werden.
* Das erste Kategoriemuster, das dem URI entspricht, ist die Kategorie der Datei. Keine weiteren Kategoriemuster werden danach ausgewertet.

Der Dispatcher unterstützt maximal 8 Statistikkategorien. Wenn Sie mehr als 8 Kategorien definieren, werden nur die ersten 8 verwendet.

**Renderer-Auswahl**

Jedes Mal, wenn der Dispatcher eine gerenderte Seite anfordert, wird der folgende Algorithmus zur Auswahl des Renderers angewendet:

1. Wenn die Anforderung den Namen des Renderers in einem `renderid`-Cookie enthält, verwendet der Dispatcher diesen Renderer.
1. Wenn die Anforderung keinen `renderid`-Cookie enthält, vergleicht der Dispatcher die Renderer-Statistiken:

   1. Der Dispatcher ermittelt die Kategorie des Anforderung-URI.
   1. Der Dispatcher ermittelt, welcher Renderer die beste Antwortzeit für diese Kategorie hat, und wählt diesen aus.

1. Falls noch kein Renderer ausgewählt ist, wird der erste in der Liste verwendet.

Die Bewertung für eine Renderer-Kategorie basiert auf vorherigen Antwortzeiten sowie auf früheren fehlgeschlagenen und erfolgreichen Verbindungsversuchen durch den Dispatcher. Bei jedem Versuch wird die Bewertung für die Kategorie des angeforderten URI aktualisiert.

>[!NOTE]
>
>Wenn Sie keinen Lastenausgleich verwenden, können Sie diesen Abschnitt überspringen.

### Definieren von Statistikkategorien {#defining-statistics-categories}

Definieren einer Kategorie für jeden Dokumenttyp, für den Sie Statistiken für die Renderer-Auswahl erheben möchten. Der /statistics-Abschnitt enthält einen /categories-Abschnitt. Wenn Sie eine Kategorie definieren möchten, fügen Sie unter dem /categories-Abschnitt eine Zeile mit folgendem Format hinzu:

`/name { /glob "pattern"}`

Die Kategorie `name` muss für die Farm eindeutig sein. Die Beschreibung `pattern` wird im Abschnitt [Designing-Muster für Glob-Eigenschaften](#designing-patterns-for-glob-properties) beschrieben.

Um die Kategorie eines URI zu ermitteln, vergleicht der Dispatcher den URI so lange mit den einzelnen Kategoriemustern, bis ein Treffer gefunden wird. Der Dispatcher beginnt mit der ersten Kategorie in der Liste und geht diese dann weiter nach unten durch. Platzieren Sie daher Kategorien mit spezifischeren Mustern zuerst.

Beispielsweise definiert Dispatcher die standarddatei dispatcher. any eine HTML-Kategorie und eine andere Kategorie. Die Kategorie „html“ ist die spezifischere und erscheint daher als erste:

```xml
/statistics
  {
  /categories
    {
      /html { /glob "*.html" }
      /others  { /glob "*" }
    }
  }
```

Das folgende Beispiel enthält außerdem eine Kategorie für Suchseiten:

```xml
/statistics
  {
  /categories
    {
      /search { /glob "*search.html" }
      /html { /glob "*.html" }
      /others  { /glob "*" }
    }
  }
```

### Abbildung der Server-Nichtverfügbarkeit in Dispatcher-Statistiken {#reflecting-server-unavailability-in-dispatcher-statistics}

Die `/unavailablePenalty` Eigenschaft legt die Zeit (in Zehnteleiner Sekunde) fest, die auf die Render-Statistik angewendet wird, wenn eine Verbindung zum Rendering fehlschlägt. Der Dispatcher addiert diese Zeit der Statistikkategorie hinzu, die dem angeforderten URI entspricht.

Beispielsweise wird diese Strafzeit angewendet, wenn die TCP/IP-Verbindung zum angegebenen Hostnamen/Port nicht hergestellt werden konnte, weil AEM nicht ausgeführt wird (und nicht empfangsbereit ist), oder aufgrund von netzwerkbezogenen Problemen.

Die `/unavailablePenalty` Eigenschaft ist ein direkt untergeordnetes Element des `/farm` Abschnitts (ein Sibling des `/statistics` Abschnitts).

Wenn keine `/unavailablePenalty` Eigenschaft vorhanden ist, wird der Wert &quot;1&quot; verwendet.

```xml
/unavailablePenalty "1"
```

## Identifizieren eines „Sticky“-Verbindungsordners – /stickyConnectionsFor {#identifying-a-sticky-connection-folder-stickyconnectionsfor}

Die `/stickyConnectionsFor` Eigenschaft definiert einen Ordner, der persistente Dokumente enthält. darauf wird mithilfe der URL zugegriffen. Der Dispatcher sendet alle Anforderungen von einem Benutzer, die sich in diesem Ordner befinden, an eine Renderer-Instanz. Mit „Sticky“-Verbindungen wird sichergestellt, dass Sitzungsdaten für alle Dokumente vorhanden und konsistent sind. Für diesen Mechanismus wird der `renderid`-Cookie verwendet.

Im folgenden Beispiel wird eine persistente Verbindung zum Ordner /products definiert:

```xml
/stickyConnectionsFor "/products"
```

Wenn eine Seite aus Inhalten aus verschiedenen Inhaltsknoten besteht, schließen Sie die `/paths` Eigenschaft ein, die die Pfade zum Inhalt auflistet. Eine Seite enthält beispielsweise Inhalte aus `/content/image`, `/content/video`und `/var/files/pdfs`. Die folgende Konfiguration ermöglicht „Sticky“-Verbindungen für alle Inhalte auf der Seite:

```xml
/stickyConnections {
  /paths {
    "/content/image"
    "/content/video"
    "/var/files/pdfs"
  }
}
```

### Httponly {#httponly}

Wenn sticky-Verbindungen aktiviert sind, setzt das Dispatcher-Modul das `renderid` Cookie. Dieses Cookie verfügt nicht `httponly` über das Flag, das hinzugefügt werden sollte, um die Sicherheit zu verbessern. Sie können dies tun, indem Sie die `httpOnly` Eigenschaft in der `/stickyConnections` Knoten einer `dispatcher.any` Konfigurationsdatei festlegen. Der Wert der Eigenschaft (0 oder 1) bestimmt, ob das `renderid` Cookie das `HttpOnly` Attribut angehängt hat. Der Standardwert ist 0, d. h. das Attribut wird nicht hinzugefügt.

Weitere Informationen zum `httponly` Flag finden Sie [auf dieser Seite](https://www.owasp.org/index.php/HttpOnly).

### sicher {#secure}

Wenn sticky-Verbindungen aktiviert sind, setzt das Dispatcher-Modul das `renderid` Cookie. Dieses Cookie verfügt nicht über das **sichere** Flag, das hinzugefügt werden sollte, um die Sicherheit zu verbessern. Sie können dies tun, indem Sie die `secure` Eigenschaft in der `/stickyConnections` Knoten einer `dispatcher.any` Konfigurationsdatei festlegen. Der Wert der Eigenschaft (0 oder 1) bestimmt, ob das `renderid` Cookie das `secure` Attribut angehängt hat. Der Standardwert ist 0, d. h. das Attribut wird hinzugefügt, wenn * * die eingehende Anforderung sicher ist. Wenn der Wert auf 1 gesetzt ist, wird das sichere Flag hinzugefügt, unabhängig davon, ob die eingehende Anforderung sicher ist oder nicht.

## Umgang mit Renderer-Verbindungsfehlern {#handling-render-connection-errors}

Konfigurieren Sie das Dispatcher-Verhalten für die Fälle, in denen der Renderer-Server einen 500-Fehler zurückgibt oder nicht verfügbar ist.

### Angeben einer Seite für die Konsistenzprüfung {#specifying-a-health-check-page}

Verwenden Sie die `/health_check` Eigenschaft, um eine URL anzugeben, die beim Auftreten eines 500-Status-Codes überprüft wird. Wenn diese Seite auch einen 500 Status-Code zurückgibt, wird die Instanz als nicht verfügbar betrachtet und eine konfigurierbare Zeitstrafe ( `/unavailablePenalty`) wird auf das Rendering angewendet, bevor erneut versucht wird.

```xml
/health_check
  {
  # Page gets contacted when an instance returns a 500
  /url "/health_check.html"
  }
```

### Festlegen der Wiederholungsverzögerung für die Seite {#specifying-the-page-retry-delay}

Die/ `retryDelay` Eigenschaft legt die Zeit (in Sekunden) fest, die Dispatcher zwischen Runden von Verbindungsversuchen mit der Farm wartet. Die maximale Anzahl der Verbindungsversuche pro Runde entspricht der Anzahl der Renderer in der Farm.

Dispatcher verwendet einen Wert, der `"1"``/retryDelay` nicht explizit definiert ist. Der Standardwert ist in den meisten Fällen angemessen.

```xml
/retryDelay "1"
```

### Konfigurieren der Wiederholungsanzahl {#configuring-the-number-of-retries}

Die `/numberOfRetries` Eigenschaft legt die maximale Anzahl an Verbindungsversuchen fest, die Dispatcher mit den Rendern ausführt. Wenn der Dispatcher nach diesen Wiederholungen keine erfolgreiche Verbindung zu einem Renderer herstellen konnte, gibt er einen Fehler zurück.

Die maximale Anzahl der Verbindungsversuche pro Runde entspricht der Anzahl der Renderer in der Farm. Aus diesem Grund ist die maximale Anzahl der Versuche, mit denen Dispatcher eine Verbindung versucht, ( `/numberOfRetries`) x (die Anzahl der Wiedergaben).

Wenn der Wert nicht explizit definiert ist, lautet der Standardwert **5**.

```xml
/numberOfRetries "5"
```

### Verwenden des Failover-Mechanismus {#using-the-failover-mechanism}

Aktivieren Sie den Failover-Mechanismus in Ihrer Dispatcher-Farm, um Anforderungen an verschiedene Renderer zu senden, wenn die ursprüngliche Anforderung fehlschlägt. Wenn das Failover aktiviert ist, weist der Dispatcher folgendes Verhalten auf:

* Wenn bei einer Renderer-Anforderung der HTTP-Status 503 (Nicht verfügbar) zurückgegeben wird, sendet der Dispatcher die Anforderung an einen anderen Renderer.
* Wenn eine Anforderung an eine Wiedergabe HTTP-Status 50 x (außer 503) zurückgibt, sendet Dispatcher eine Anforderung für die Seite, die für die `health_check` Eigenschaft konfiguriert ist.

   * Wenn bei der Konsistenzprüfung 500 (INTERNAL_SERVER_ERROR) zurückgegeben wird, sendet der Dispatcher die ursprüngliche Anforderung an einen anderen Renderer.
   * Wenn die Konsistenzprüfung den HTTP-Status 200 zurückgibt, gibt der Dispatcher den HTTP-Fehler 500 an den Client zurück.

Um das Failover zu aktivieren, fügen Sie der Farm (oder Website) folgende Zeile hinzu:

```xml
/failover "1" 
```

>[!NOTE]
>
>Um HTTP-Anforderungen, die einen Haupttext enthalten, erneut auszuprobieren, sendet Dispatcher eine `Expect: 100-continue` Anforderungs-Kopfzeile an das Rendering, bevor die eigentlichen Inhalte spoffert werden. CQ 5.5 mit CQSE antwortet dann umgehend mit 100 (CONTINUE) oder einem Fehlercode. Andere Servlet-Container sollten dies ebenfalls unterstützen.

## Ignorieren von Netzwerkunterbrechungsfehlern – /ignoreEINTR {#ignoring-interruption-errors-ignoreeintr}

>[!CAUTION]
>
>Diese Option wird normalerweise nicht benötigt. Sie müssen sie nur verwenden, wenn Sie die folgenden Protokollmeldungen sehen:
>
>`Error while reading response: Interrupted system call`

Jeder Systemaufruf für ein dateiorientiertes System kann mit `EINTR` ignoriert werden, wenn sich das Objekt des Systemaufrufs in einem Remotesystem befindet, auf das über NFS zugegriffen wird. Ob diese Systemaufrufe einen Zeitwert überschreiten oder unterbrochen werden können, richtet sich danach, wie das zugrundeliegende Dateisystem auf dem lokalen Computer bereitgestellt wurde.

Verwenden Sie den /ignoreEINTR-Parameter, wenn Ihre Instanz eine solche Konfiguration aufweist und folgende Meldung enthält:

`Error while reading response: Interrupted system call`

Intern liest der Dispatcher die Antwort vom Remote-Server (z. B. AEM) mit einer Schleife, die sich wie folgt darstellen lässt:

`while (response not finished) {  
read more data  
}`

Solche Meldungen können generiert werden, wenn das Ereignis `EINTR` im Abschnitt `read more data`&quot;&quot; auftritt und durch den Empfang eines Signals ausgelöst wird, bevor Daten empfangen wurden.

Um diese Unterbrechungen zu ignorieren, können Sie die folgenden Parameter zu `dispatcher.any` hinzufügen (vor `/farms`):

`/ignoreEINTR "1"`

Einstellung `/ignoreEINTR` , `"1"` die Dispatcher dazu führt, dass versucht wird, Daten zu lesen, bis die vollständige Antwort gelesen wird. Der Standardwert ist „0“ und deaktiviert die Option.

## Entwerfen von Mustern für glob-Eigenschaften {#designing-patterns-for-glob-properties}

In einigen Abschnitten in der Dispatcher-Konfigurationsdatei werden `glob`-Eigenschaften als Auswahlkriterien für Clientanforderungen verwendet. Die Werte der glob-Eigenschaften sind Muster, die der Dispatcher mit einem Aspekt der Anforderung abgleicht, wie der Pfad der angeforderten Ressource oder die IP-Adresse des Clients. Beispielsweise verwenden die Elemente im `/filter` Abschnitt glob-Muster, um die Pfade der Seiten zu identifizieren, die Dispatcher ein- oder ablehnt.

Die glob-Werte können Platzhalterzeichen und alphanumerische Zeichen enthalten, um das Muster zu definieren.

| Platzhalterzeichen | Beschreibung | Beispiele |
|--- |--- |--- |
| `*` | Entspricht null oder mehreren aufeinanderfolgenden Instanzen eines Zeichens in der Zeichenfolge. Das letzte Zeichen der Entsprechung ergibt sich durch eine der folgenden Bedingungen: <br/>Ein Zeichen in der Zeichenfolge entspricht dem nächsten Zeichen im Muster und das Musterzeichen verfügt über die folgenden Merkmale:<br/><ul><li>Kein *</li><li>Kein ?</li><li>Ein literales Zeichen (einschließlich Leerzeichen) oder eine Zeichenklasse.</li><li>Das Ende des Musters wird erreicht.</li></ul>Innerhalb einer Zeichenklasse wird das Zeichen als Literal interpretiert. | `*/geo*` Entspricht einer beliebigen Seite unterhalb der `/content/geometrixx` Node und des `/content/geometrixx-outdoors` Knotens. Die folgenden HTTP-Anforderungen entsprechen dem glob-Muster: <br/><ul><li>`"GET /content/geometrixx/en.html"`</li><li>`"GET /content/geometrixx-outdoors/en.html"` </li></ul><br/> `*outdoors/*`<br/>Entspricht einer beliebigen Seite unterhalb des `/content/geometrixx-outdoors` Knotens. Die folgende HTTP-Anforderung entspricht beispielsweise dem glob-Muster: <br/><ul><li>`"GET /content/geometrixx-outdoors/en.html"`</li></ul> |
| `?` | Entspricht einem beliebigen einzelnen Zeichen (außerhalb von Zeichenklassen). Innerhalb einer Zeichenklasse wird dieses Zeichen als Literal interpretiert. | `*outdoors/??/*`<br/>Entspricht den Seiten für eine beliebige Sprache der Site „geometrixx-outdoors“. Die folgende HTTP-Anforderung entspricht beispielsweise dem glob-Muster: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/>Die folgende Anforderung entspricht nicht dem glob-Muster: <br/><ul><li>&quot;GET /content/geometrixx-outdoors/en.html&quot;</li></ul> |
| `[ and ]` | Markiert den Anfang und das Ende einer Zeichenklasse. Zeichenklassen können einen oder mehrere Zeichenbereiche und einzelne Zeichen enthalten.<br/>Eine Übereinstimmung tritt auf, wenn das Zielzeichen einem Zeichen in der Zeichenklasse oder innerhalb eines bestimmten Bereichs entspricht.<br/>Wenn die schließende Klammer nicht vorhanden ist, liefert das Muster keine Treffer. | `*[o]men.html*`<br/> Entspricht der folgenden HTTP-Anforderung:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul><br/>Entspricht nicht der folgenden HTTP-Anforderung:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/> `*[o/]men.html*`<br/>Stimmt mit den folgenden HTTP-Anforderungen überein: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul> |
| `-` | Steht für einen Zeichenbereich. Zur Verwendung in Zeichenklassen.  Außerhalb einer Zeichenklasse wird dieses Zeichen als Literal interpretiert. | `*[m-p]men.html*` Entspricht der folgenden HTTP-Anforderung: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul>Entspricht nicht der folgenden HTTP-Anforderung:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul> |
| `!` | Schließt das nachfolgende Zeichen oder die nachfolgende Zeichenklasse aus. Verwenden Sie dies nur zum Ausschließen von Zeichen und Zeichenbereichen innerhalb von Zeichenklassen. Entspricht `^ wildcard`dem Wert. <br/>Außerhalb einer Zeichenklasse wird dieses Zeichen als Literal interpretiert. | `*[!o]men.html*`<br/> Entspricht der folgenden HTTP-Anforderung: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/>Entspricht nicht der folgenden HTTP-Anforderung: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul><br/>`*[!o!/]men.html*`<br/> Entspricht nicht der folgenden HTTP-Anforderung:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"` oder `"GET /content/geometrixx-outdoors/en/men. html"`</li></ul> |
| `^` | Schließt das nachfolgende Zeichen oder den nachfolgenden Zeichenbereich aus. Verwenden Sie dies nur zum Ausschließen von Zeichen und Zeichenbereichen innerhalb von Zeichenklassen. Entspricht dem `!` Platzhalterzeichen. <br/>Außerhalb einer Zeichenklasse wird dieses Zeichen als Literal interpretiert. | Die Beispiele für `!` das Platzhalterzeichen werden angewendet, wobei die `!` Zeichen in den Beispielmustern mit `^` Zeichen ersetzt werden. |


<!--- need to troubleshoot table

The following table describes the wildcard characters.

<table border="1" cellpadding="1" cellspacing="0" width="100%"> 
 <tbody> 
  <tr> 
   <th>Wildcard character</th> 
   <th>Description</th> 
   <th>Examples</th> 
  </tr> 
  <tr> 
   <td>*</td> 
   <td><p>Matches zero or more contiguous instances of any character in the string. The final character of the match is determined by either of the following situations:</p> 
    <ul> 
     <li>A character in the string matches the next character in the pattern, and the pattern character has the following characteristics: 
      <ul> 
       <li>Not a *</li> 
       <li>Not a ?</li> 
       <li>A literal character (including a space) or a character class.</li> 
      </ul> </li> 
     <li>The end of the pattern is reached.</li> 
    </ul> <p>Inside a character class, the character is interpreted literally.</p> </td> 
   <td><p>*/geo*</p> <p>Matches any page below the /content/geometrixx node and the /content/geometrixx-outdoors node. The following HTTP requests match the glob pattern:</p> 
    <ul> 
     <li>"GET /content/geometrixx/en.html"</li> 
     <li>"GET /content/geometrixx-outdoors/en.html" </li> 
    </ul> <p>*outdoors/*</p> <p>Matches any page below the /content/geometrixx-outdoors node. For example, the following HTTP request matches the glob pattern:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en.html"</li> 
    </ul> </td> 
  </tr> 
  <tr> 
   <td>?</td> 
   <td><p>Matches any single character. Use outside character classes.</p> <p>Inside a character class, this character is interpreted literally.</p> </td> 
   <td><p>*outdoors/??/*</p> <p>Matches the pages for any language in the geometrixx-outdoors site. For example, the following HTTP request matches the glob pattern:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/men.html"</li> 
    </ul> <p>The following request does not match the glob pattern:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en.html" </li> 
    </ul> </td> 
  </tr> 
  <tr> 
   <td>[ and ]</td> 
   <td><p>Demarks the beginning and end of a character class.</p> <p>Character classes can include one or more character ranges and single characters.</p> <p>A match occurs if the target character matches any of the characters in the character class, or within a defined range.</p> <p>If the closing bracket is not included, the pattern produces no matches.</p> <p></p> <p></p> <p></p> </td> 
   <td><p>*[o]men.html*</p> <p>Matches the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html"</li> 
    </ul> <p>Does not match the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/men.html" </li> 
    </ul> <p>*[o/]men.html*</p> <p>Matches the following HTTP requests: </p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html" </li> 
     <li> "GET /content/geometrixx-outdoors/en/men.html" </li> 
    </ul> <p></p> </td> 
  </tr> 
  <tr> 
   <td>-</td> 
   <td><p>Denotes a range of characters. For use in character classes.<br /> </p> <p>Outside of a character class, this character is interpreted literally.</p> <p></p> </td> 
   <td><p>*[m-p]men.html*</p> <p>Matches the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html"</li> 
    </ul> <p>Does not match the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/men.html"</li> 
    </ul> <p></p> </td> 
  </tr> 
  <tr> 
   <td>!</td> 
   <td><p>Negates the character or character class that follows. Use only for negating characters and character ranges inside character classes. Equivalent to the ^ wildcard.</p> <p>Outside of a character class, this character is interpreted literally.</p> <p></p> </td> 
   <td><p>*[!o]men.html*</p> <p>Matches the following HTTP request: </p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/men.html"</li> 
    </ul> <p>Does not match the following HTTP request</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html" </li> 
    </ul> <p>*[!o!/]men.html*</p> <p>Does not match the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html" or "GET /content/geometrixx-outdoors/en/men. html" </li> 
    </ul> </td> 
  </tr> 
  <tr> 
   <td>^</td> 
   <td><p>Negates the character or character range that follows. Use for negating only characters and character ranges inside character classes. Equivalent to the ! wildcard character.</p> <p>Outside of a character class, this charcter is interpreted literally.</p> </td> 
   <td>The examples for the ! wildcard character apply, substituting the ! characters in the example patterns with ^ characters.</td> 
  </tr> 
 </tbody> 
</table>
-->

## Protokollierung {#logging}

In der Webserverkonfiguration können Sie Folgendes festlegen:

* Den Speicherort der Dispatcher-Protokolldatei
* Die Protokollierungsebene

Weitere Informationen finden Sie in der Dokumentation zum Webserver und in der Readme-Datei Ihrer Dispatcher-Instanz.

**Rotierende/wechselnde Protokolle in Apache**

Wenn Sie einen **Apache**-Webserver verwenden, können Sie die Standardfunktionen für das Rotieren und/oder Wechseln von Protokollen verwenden. Beispiel für das Wechseln von Protokollen:

`DispatcherLog "| /usr/apache/bin/rotatelogs logs/dispatcher.log%Y%m%d 604800"`

Hiermit wird automatisch rotiert:

* die Dispatcher-Protokolldatei mit einem Zeitstempel in der Erweiterung (logs/dispatcher.log%J%m%t)
* auf Wochenbasis (60 x 60 x 24 x 7 = 604800 Sekunden).

Lesen Sie die Apache-Webserverdokumentation, um weitere Informationen zum Rotieren und Wechseln von Protokollen zu erhalten, z. B. hier für [Apache 2.2](https://httpd.apache.org/docs/2.2/logs.html).

>[!NOTE]
>
>Nach der Installation ist die Standardprotokollebene hoch (d. h. Ebene 3 = Debugging), sodass der Dispatcher alle Fehler und Warnungen protokolliert. Dies ist am Anfang sehr nützlich.
>
>Allerdings erfordert dies zusätzliche Ressourcen. Wenn der Dispatcher reibungslos und *gemäß Ihren Anforderungen funktioniert*, können (sollten) Sie daher die Protokollierungsebene reduzieren.

### Ablaufprotokollierung {#trace-logging}

Unter anderem bietet die Version 4.2.0 auch die Trace-Protokollierung an.

Dies ist eine höhere Ebene als die Debug-Protokollierungsebene, bei der den Protokollen zusätzliche Informationen hinzugefügt werden. Folgende Informationen werden protokolliert:

* Die Werte der weitergeleiteten Header
* Die Regel, die für eine bestimmte Aktion angewendet wurde

Sie können die Ablaufprotokollierung aktivieren, indem Sie die Protokollierungsebene in Ihrem Webserver auf `4` festlegen.

Nachfolgend finden Sie ein Beispiel für ein Ablaufprotokoll:

```xml
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[Host] = "localhost:8443"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[User-Agent] = "curl/7.43.0"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[Accept] = "*/*"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-SSL-Client-Cert] = "(null)"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[Via] = "1.1 localhost:8443 (dispatcher)"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-For] = "::1"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-SSL] = "on"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-SSL-Cipher] = "DHE-RSA-AES256-SHA"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-SSL-Session-ID] = "ba931f5e4925c2dde572d766fdd436375e15a0fd24577b91f4a4d51232a934ae"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-Port] = "8443"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[Server-Agent] = "Communique-Dispatcher"
```

Das protokollierte Ereignis, wenn eine Datei angefordert wird, die einer blockierenden Regel entspricht:

```xml
[Thu Mar 03 14:42:45 2016] [T] [11831] 'GET /content.infinity.json HTTP/1.1' was blocked because of /0082
```

## Bestätigen der normalen Funktionsweise {#confirming-basic-operation}

Um die normale Funktionsweise und Interaktion des Webservers, des Dispatchers und der AEM-Instanz zu prüfen, können Sie folgende Schritte ausführen:

1. Setzen `loglevel``3`Sie die Einstellung auf.

1. Starten Sie den Webserver. Dabei wird auch der Dispatcher gestartet.
1. Starten Sie die AEM-Instanz.
1. Überprüfen Sie die Protokoll- und Fehlerdateien für den Webserver und Dispatcher.\
   Je nach Webserver sollten Meldungen wie z. B. folgende angezeigt werden:\
   `[Thu May 30 05:16:36 2002] [notice] Apache/2.0.50 (Unix) configured`\
   und:\
   `[Fri Jan 19 17:22:16 2001] [I] [19096] Dispatcher initialized (build XXXX)`

1. Sehen Sie sich die Website vom Webserver aus an. Vergewissern Sie sich, dass die Inhalte wie gewünscht angezeigt werden.\
   Beispielsweise bei einer lokalen Installation, bei der AEM auf Port `4502` ausgeführt wird und der Webserver auf der `80` Website-Konsole mit beiden:\
   ` https://localhost:4502/libs/wcm/core/content/siteadmin.html  
https://localhost:80/libs/wcm/core/content/siteadmin.html  
`Die Ergebnisse sollten identisch sein. Prüfen Sie den Zugriff auf andere Seiten nach demselben Prinzip.

1. Überprüfen Sie, ob der Cacheordner gefüllt wird.
1. Aktivieren Sie eine Seite, um zu überprüfen, ob der Cache ordnungsgemäß geleert wird.
1. Wenn alles ordnungsgemäß funktioniert, können Sie das `loglevel` auf `0` verringern.

## Verwenden mehrerer Dispatcher {#using-multiple-dispatchers}

In komplexen Umgebungen können Sie mehrere Dispatcher verwenden. Folgende Szenarien sind möglich:

* Ein Dispatcher zum Veröffentlichen einer Website im Intranet
* Ein zweiter Dispatcher mit einer anderen Adresse und anderen Sicherheitseinstellungen zum Veröffentlichen desselben Inhalts im Internet

In einem solchen Fall müssen Sie sicherstellen, dass jede Anforderung nur einen Dispatcher durchläuft. Ein Dispatcher verarbeitet keine Anforderungen, die von einem anderen Dispatcher stammen. Stellen Sie daher sicher, dass beide Dispatcher direkt auf die AEM-Website zugreifen.

## Debugging {#debugging}

Wenn der Header `X-Dispatcher-Info` einer Anforderung hinzugefügt wurde, beantwortet Dispatcher, ob das Ziel zwischengespeichert wurde, von zwischengespeichert oder gar nicht zwischengespeichert wurde. Die Antwort-Kopfzeile `X-Cache-Info` enthält diese Informationen in lesbarer Form. Sie können diese Antwortheader verwenden, um Probleme mit Antworten zu beheben, die vom Dispatcher zwischengespeichert werden.

Diese Funktion ist standardmäßig nicht aktiviert. Damit die Antwort-Kopfzeile `X-Cache-Info` eingeschlossen wird, muss die Farm den folgenden Eintrag enthalten:

```xml
/info "1"
```

Beispiel:

```xml
/farm
{
    /mywebsite
    {
        # Include X-Cache-Info response header if X-Dispatcher-Info is in request header
        /info "1"
    }
}
```

Außerdem benötigt die `X-Dispatcher-Info` Kopfzeile keinen Wert. Wenn Sie jedoch `curl` zum Testen verwenden, müssen Sie einen Wert angeben, um die Kopfzeile zu senden, z. B.:

```xml
curl -v -H "X-Dispatcher-Info: true" https://localhost/content/we-retail/us/en.html
```

Im Folgenden finden Sie eine Liste mit den Antwortheader, die `X-Dispatcher-Info` zurückgegeben werden:

* **zwischengespeichert**\
   Die Zieldatei ist im Cache enthalten und der Dispatcher hat festgestellt, dass sie für die Bereitstellung gültig ist.
* **Zwischenspeicherung**\
   Die Zieldatei ist nicht im Cache enthalten und der Dispatcher hat festgestellt, dass sie gültig ist, um die Ausgabe zwischenzuspeichern und bereitzustellen.
* **Zwischenspeicherung: stat-Datei ist aktuellere Datei ist**Die Zieldatei
im Cache enthalten. Sie wird jedoch durch eine neuere STAT-Datei ungültig gemacht. Der Dispatcher löscht die Zieldatei, erstellt sie aus der Ausgabe und stellt sie bereit.
* **nicht cachable: Kein Dokumentstamm**
Die Konfiguration der Farm enthält keinen Dokumentstamm (Konfigurationselement `cache.docroot`).
* **nicht cachable: Cache-Dateipfad zu lang**\
   Die Zieldatei - die Verkettung von Dokumentstamm und URL-Datei - überschreitet den längsten Dateinamen im System.
* **nicht cachable: temporärer Dateipfad zu lang**\
   Die Vorlage für den temporären Dateinamen überschreitet den längsten Dateinamen auf dem System. Der Dispatcher erstellt zuerst eine temporäre Datei, bevor die zwischengespeicherte Datei tatsächlich erstellt oder überschrieben wird. Der Name der temporären Datei ist der Zieldateiname mit den `_YYYYXXXXXX` angehängten Zeichen, wobei der `Y` Name durch `X` die Erstellung eines eindeutigen Namens ersetzt wird.
* **nicht cachable: Anforderungs-URL hat keine Erweiterung**\
   Die Anforderungs-URL hat keine Erweiterung oder es gibt einen Pfad nach der Dateierweiterung, z. B.: `/test.html/a/path`.
* **nicht cachable: request war kein GET oder HEAD**
Die HTTP-Methode ist weder ein GET noch ein HEAD. Der Dispatcher geht davon aus, dass die Ausgabe dynamische Daten enthält, die nicht zwischengespeichert werden sollten.
* **nicht cachable: Anforderung enthielt eine Abfragezeichenfolge**\
   Die Anforderung enthielt eine Abfragezeichenfolge. Der Dispatcher geht davon aus, dass die Ausgabe von der angegebenen Abfragezeichenfolge abhängt und daher nicht zwischengespeichert wird.
* **nicht cachable: session manager hat keine Authentifizierung durchgeführt**\
   Der Cache der Farm wird von einem Sitzungsmanager bestimmt (die Konfiguration enthält einen `sessionmanagement` Knoten) und die Anforderung enthielt keine entsprechenden Authentifizierungsinformationen.
* **nicht cachable: Anforderung enthält Autorisierung**\
   Die Farm darf keine Ausgabe zwischenspeichern ( `allowAuthorized 0`) und die Anforderung enthält Authentifizierungsinformationen.
* **nicht cachable: target ist ein Verzeichnis**\
   Die Zieldatei ist ein Verzeichnis. This might point to some conceptual mistake, where a URL and some sub-URL both contain cacheable output, for example if a request to `/test.html/a/file.ext` comes first and contains cacheable output, the dispatcher will not be able to cache the output of a subsequent request to `/test.html`.
* **nicht cachable: Anforderungs-URL hat einen nachfolgenden Schrägstrich**\
   Die Anforderungs-URL hat einen nachfolgenden Schrägstrich.
* **nicht cachable: Anforderungs-URL nicht in Cacheregeln**\
   Die Cacheregeln der Farm verweigern explizit Zwischenspeicherung der Ausgabe einiger Anforderungs-Urls.
* **nicht cachable: Autorisierungsprüfung verweigert**\
   Die Autorisierungsprüfung der Farm hat den Zugriff auf die zwischengespeicherte Datei verweigert.
* **nicht cachable: Sitzung ungültig**:
Der Cachespeicher der Farm wird von einem Sitzungsmanager (Konfiguration enthält einen `sessionmanagement` Knoten) und die Sitzung des Benutzers ist nicht oder nicht mehr gültig.
* **nicht cachable: Die Antwort enthält den`no_cache `** zurückgegebenen Header, `Dispatcher: no_cache` sodass der Dispatcher die Ausgabe zwischenspeichert.
* **nicht cachable: length content length is zero**
The content length of the response is null; Der Dispatcher erstellt keine Datei mit null Längen.
