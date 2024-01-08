---
title: Konfigurieren des Dispatchers
description: Erfahren Sie, wie Sie den Dispatcher konfigurieren. Erfahren Sie mehr über die Unterstützung für IPv4 und IPv6, Konfigurationsdateien, Umgebungsvariablen, Benennen der Instanz, Definieren von Farmen, Identifizieren von virtuellen Hosts und mehr.
exl-id: 91159de3-4ccb-43d3-899f-9806265ff132
source-git-commit: 410346694a134c0f32a24de905623655f15269b4
workflow-type: tm+mt
source-wordcount: '8857'
ht-degree: 41%

---

# Konfigurieren des Dispatchers {#configuring-dispatcher}

>[!NOTE]
>
>Dispatcher-Versionen sind AEM unabhängig. Sie wurden möglicherweise zu dieser Seite umgeleitet, wenn Sie einem Link zur Dispatcher-Dokumentation gefolgt sind, der in die Dokumentation für eine frühere Version von AEM eingebettet ist.

In den folgenden Abschnitten wird beschrieben, wie Sie verschiedene Aspekte des Dispatchers konfigurieren.

## Unterstützung für IPv6 und IPv4  {#support-for-ipv-and-ipv}

Alle Elemente von AEM und Dispatcher können sowohl in IPv4- als auch in IPv6-Netzwerken installiert werden. Siehe [IPV4 und IPV6](https://experienceleague.adobe.com/docs/experience-manager-65/deploying/introduction/technical-requirements.html?lang=en#ipv-and-ipv).

## Dispatcher-Konfigurationsdateien {#dispatcher-configuration-files}

Die Dispatcher-Konfiguration wird standardmäßig in der Textdatei `dispatcher.any` gespeichert. Sie können aber Namen und Speicherort der Datei während der Installation ändern.

Die Konfigurationsdatei enthält eine Reihe von Eigenschaften mit einzelnen oder mehreren Werten, die das Verhalten des Dispatchers steuern:

* Eigenschaftsnamen ist ein Schrägstrich `/` vorangestellt.
* In Eigenschaften mit mehreren Werten stehen die untergeordneten Elemente in geschweiften Klammern `{ }`.

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

* Wenn Ihre Konfigurationsdatei groß ist, können Sie sie in mehrere kleinere Dateien aufteilen (die einfacher zu verwalten sind) und jede Datei einschließen.
* So können Sie automatisch generierte Dateien aufnehmen.

Zum Einbeziehen der Datei „myFarm.any“ in die /farms-Konfiguration verwenden Sie z. B. folgenden Code:

```xml
/farms
  {
  $include "myFarm.any"
  }
```

Verwenden Sie das Sternchen (`*`) als Platzhalter.

Wenn beispielsweise die Dateien `farm_1.any` bis `farm_5.any` die Konfiguration der Farmen 1 bis 5 enthalten, können Sie sie wie folgt angeben bzw. auswählen:

```xml
/farms
  {
  $include "farm_*.any"
  }
```

## Verwenden von Umgebungsvariablen {#using-environment-variables}

Anstatt einer Hartkodierung von Werten können Sie in der Datei „dispatcher.any“ Umgebungsvariablen in Zeichenfolgen-Eigenschaften verwenden. Um den Wert einer Umgebungsvariablen einzubeziehen, verwenden Sie das Format `${variable_name}`.

Wenn sich die Datei &quot;dispatcher.any&quot;beispielsweise im selben Ordner wie das Cache-Verzeichnis befindet, lautet der folgende Wert für [docroot](#specifying-the-cache-directory) -Eigenschaft kann verwendet werden:

```xml
/docroot "${PWD}/cache"
```

Wenn Sie als weiteres Beispiel eine Umgebungsvariable mit dem Namen `PUBLISH_IP` erstellen, in der der Hostname der AEM-Veröffentlichungsinstanz gespeichert ist, kann die folgende Konfiguration der [/renders](#defining-page-renderers-renders)-Eigenschaft verwendet werden:

```xml
/renders {
  /0001 {
    /hostname "${PUBLISH_IP}"
    /port "8443"
  }
}
```

## Benennen der Dispatcher-Instanz {#naming-the-dispatcher-instance-name}

Verwenden Sie die `/name`-Eigenschaft, um einen eindeutigen Namen für die Dispatcher-Instanz anzugeben. Die `/name`-Eigenschaft ist eine Eigenschaft der obersten Ebene in der Konfigurationsstruktur.

## Definieren von Farmen {#defining-farms-farms}

Die `/farms`-Eigenschaft definiert eine oder mehrere Gruppen von Dispatcher-Verhalten, wobei jede Gruppe mit verschiedenen Websites oder URLs verknüpft ist. Die `/farms`-Eigenschaft kann eine oder mehrere Farmen umfassen:

* Verwenden Sie eine einzelne Farm, wenn Sie möchten, dass der Dispatcher alle Webseiten oder Websites auf die gleiche Weise behandelt.
* Erstellen Sie mehrere Farmen, wenn verschiedene Bereiche Ihrer Website oder verschiedene Websites unterschiedliches Verhalten des Dispatchers erfordern.

Diese `/farms`-Eigenschaft ist eine Eigenschaft der obersten Ebene in der Konfigurationsstruktur. Zum Definieren einer Farm fügen Sie der `/farms`-Eigenschaft eine untergeordnete Eigenschaft hinzu. Verwenden Sie einen Eigenschaftsnamen, der die Farm in der Dispatcher-Instanz eindeutig identifiziert.

Die `/farmname`-Eigenschaft umfasst mehrere Werte und enthält andere Eigenschaften, die das Verhalten des Dispatchers definieren:

* Die URLs der Seiten, zu denen die Farm gehört.
* Eine oder mehrere Dienst-URLs (in der Regel von AEM-Veröffentlichungsinstanzen) zum Rendern von Dokumenten.
* Die Statistiken von einem Lastenausgleich, die beim Rendering mehrerer Dokumente angewendet werden.
* Verschiedene andere Verhaltensweisen, z. B. welche Dateien zwischengespeichert werden sollen und wo sie zwischengespeichert werden sollen.

Der Wert kann jedes alphanumerische Zeichen (a-z, 0-9) enthalten. Das folgende Beispiel zeigt das Definitionsgerüst für zwei Farmen mit den Namen `/daycom` und `/docsdaycom`:

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
>Wenn Sie mehrere Renderfarmen verwenden, wird die Liste von unten nach oben ausgewertet. Dieser Ablauf ist bei der Definition von [Virtuelle Hosts](#identifying-virtual-hosts-virtualhosts) für Ihre Websites.

Jede Farmeigenschaft kann die folgenden untergeordneten Eigenschaften enthalten:

| Eigenschaftsname | Beschreibung |
|--- |--- |
| [/homepage](#specify-a-default-page-iis-only-homepage) | Standard-Homepage (optional) (nur IIS) |
| [/clientheaders](#specifying-the-http-headers-to-pass-through-clientheaders) | Die Header aus der Client-HTTP fragen eine Weiterleitung an. |
| [/virtualhosts](#identifying-virtual-hosts-virtualhosts) | Die virtuellen Hosts für diese Farm. |
| [/sessionmanagement](#enabling-secure-sessions-sessionmanagement) | Unterstützung für die Sitzungsverwaltung und -authentifizierung. |
| [/renders](#defining-page-renderers-renders) | Die Server, welche die gerenderten Seiten liefern (in der Regel AEM-Veröffentlichungsinstanzen). |
| [/filter](#configuring-access-to-content-filter) | Definiert die URLs, auf die der Dispatcher den Zugriff ermöglicht. |
| [/vanity_urls](#enabling-access-to-vanity-urls-vanity-urls) | Konfiguriert den Zugriff auf Vanity-URLs. |
| [/propagateSyndPost](#forwarding-syndication-requests-propagatesyndpost) | Unterstützung bei der Weiterleitung von Syndizierungsanforderungen |
| [/cache](#configuring-the-dispatcher-cache-cache) | Konfiguriert das Cache-Verhalten. |
| [/statistics](#configuring-load-balancing-statistics) | Definieren von statistischen Kategorien zur Berechnung des Lastenausgleichs. |
| [/stickyConnectionsFor](#identifying-a-sticky-connection-folder-stickyconnectionsfor) | Der Ordner mit fixierbaren Dokumenten. |
| [/health_check](#specifying-a-health-check-page) | Die URL, mit der die Serververfügbarkeit ermittelt wird. |
| [/retryDelay](#specifying-the-page-retry-delay) | Verzögerung bevor eine fehlgeschlagene Verbindung wiederholt wird. |
| [/unavailablePenalty](#reflecting-server-unavailability-in-dispatcher-statistics) | Nachteile, die sich auf Statistiken für Berechnungen zum Lastenausgleich auswirken. |
| [/failover](#using-the-failover-mechanism) | Erneutes Senden von Anforderungen an verschiedene Renderer, wenn die ursprüngliche Anforderung fehlschlägt. |
| [/auth_checker](permissions-cache.md) | Informationen zum Zwischenspeichern unter Berücksichtigung von Berechtigungen finden Sie unter [Zwischenspeichern von geschützten Inhalten](permissions-cache.md). |

## Angeben einer Standardseite (nur IIS) - /homepage {#specify-a-default-page-iis-only-homepage}

>[!CAUTION]
>
>Der `/homepage`-Parameter (nur IIS) funktioniert nicht mehr. Stattdessen sollten Sie die [IIS URL Rewrite-Modul](https://learn.microsoft.com/en-us/iis/extensions/url-rewrite-module/using-the-url-rewrite-module).
>
>Wenn Sie Apache verwenden, sollten Sie das Modul `mod_rewrite` verwenden. Informationen zu `mod_rewrite` (zum Beispiel: [Apache 2.4](https://httpd.apache.org/docs/current/mod/mod_rewrite.html)). Bei Verwendung von `mod_rewrite`ist es ratsam, die Markierung &#39;passthrough|PT&#39; (Weiterleiten an den nächsten Handler) zu verwenden, um die Rewrite-Engine zu zwingen, die `uri` des internen `request_rec` Struktur zum Wert der `filename` -Feld.

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

## Festlegen der HTTP-Header für die Weiterleitung {#specifying-the-http-headers-to-pass-through-clientheaders}

Die `/clientheaders`-Eigenschaft definiert eine Liste von HTTP-Headern, die der Dispatcher von der Client-HTTP-Anfrage an den Renderer (AEM-Instanz) übergibt.

Standardmäßig leitet der Dispatcher die Standard-HTTP-Header an die AEM-Instanz weiter. In einigen Fällen kann es notwendig werden, zusätzliche Header weiterzuleiten oder bestimmte Header zu entfernen:

* Hinzufügen von Headern, z. B. benutzerdefinierten Headern, die von der AEM-Instanz in der HTTP-Anforderung erwartet werden.
* Entfernen Sie Kopfzeilen wie Authentifizierungskopfzeilen, die nur für den Webserver relevant sind.

Wenn Sie den Satz von Überschriften anpassen, müssen Sie eine umfassende Liste von Überschriften angeben, einschließlich der Kopfzeilen, die normalerweise standardmäßig enthalten sind.

Für eine Dispatcher-Instanz, die Seitenaktivierungsanfragen für Veröffentlichungsinstanzen verarbeitet, wird beispielsweise der Header `PATH` im `/clientheaders`-Abschnitt benötigt. Die `PATH` -Kopfzeile ermöglicht die Kommunikation zwischen dem Replikationsagenten und dem Dispatcher.

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

Mit der `/virtualhosts`-Eigenschaft wird eine Liste aller Hostname-/URI-Kombinationen definiert, die der Dispatcher für diese Farm akzeptiert. Sie können das Sternchen (`*`) als Platzhalter. Die Werte für die `virtualhosts`Eigenschaft weisen folgendes Format auf:

```xml
[scheme]host[uri][*]
```

* `scheme`: (optional) Entweder `https://` oder `https://.`
* `host`: Der Name oder die IP-Adresse des Hostcomputers und die Portnummer, falls erforderlich. (Siehe [https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.23](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.23))
* `uri`: (Optional) Der Pfad zu den Ressourcen

Die folgende Beispielkonfiguration verarbeitet Anforderungen für die `.com` und `.ch` Domänen von myCompany und alle Domänen von mySubDivision:

```xml
   /virtualhosts
    {
    "www.myCompany.com"
    "www.myCompany.ch"
    "www.mySubDivison.*"
    }
```

Mithilfe der folgenden Konfiguration werden *alle* Anfragen verarbeitet:

```xml
   /virtualhosts
    {
    "*"
    }
```

### Auflösen des virtuellen Hosts {#resolving-the-virtual-host}

Wenn der Dispatcher eine HTTP- oder HTTPS-Anforderung erhält, wird der Wert des virtuellen Hosts gesucht, der den Headern `host,` `uri` und `scheme` der Anforderung am besten entspricht. Der Dispatcher wertet die Werte in den `virtualhosts`-Eigenschaften in der folgenden Reihenfolge aus:

* Der Dispatcher beginnt bei der niedrigsten Farm und bewegt sich in der dispatcher.any-Datei nach oben.
* Bei jeder Farm beginnt der Dispatcher in der Eigenschaft`virtualhosts`mit dem obersten Wert und geht die Werteliste dann von oben nach unten durch.

Der Dispatcher ermittelt wie folgt den passendsten Wert für den virtuellen Host:

* Der erste virtuelle Host, bei dem alle drei Elemente `host`, `scheme` und `uri` mit der Anfrage übereinstimmen, wird verwendet.
* Wenn nicht `virtualhosts` -Werte haben `scheme` und `uri` Teile, die beide mit dem `scheme` und `uri` der Anfrage den ersten virtuellen Host, der mit der `host` verwendet wird.
* Wenn keine `virtualhosts`-Werte einen Host-Teil haben, der mit dem Host der Anforderung übereinstimmt, wird der oberste virtuelle Host der obersten Farm verwendet.

Daher sollten Sie Ihren standardmäßigen virtuellen Host oben in der `virtualhosts` -Eigenschaft in der obersten Farm Ihrer `dispatcher.any` -Datei.

### Beispielauflösung für virtuellen Host {#example-virtual-host-resolution}

Das folgende Beispiel stellt einen Ausschnitt aus einem `dispatcher.any` -Datei, die zwei Dispatcher-Farmen definiert und jede Farm eine `virtualhosts` -Eigenschaft.

```xml
/farms
  {
  /myProducts
    {
    /virtualhosts
      {
      "www.mycompany.com/products/*"
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
      "www.mycompany.com"
      }
    /renders
      {
      /hostname "server2.myCompany.com"
      /port "80"
      }
    }
  }
```

Unter Verwendung dieses Beispiels zeigt die folgende Tabelle die virtuellen Hosts, die für die angegebenen HTTP-Anfragen aufgelöst wurden:

| Anforderungs-URL | Aufgelöster virtueller Host |
|---|---|
| `https://www.mycompany.com/products/gloves.html` | `www.mycompany.com/products/` |
| `https://www.mycompany.com/about.html` | `www.mycompany.com` |

## Aktivieren sicherer Sitzungen - /sessionmanagement {#enabling-secure-sessions-sessionmanagement}

>[!CAUTION]
>
>`/allowAuthorized` Legen Sie `"0"` im `/cache` -Abschnitt, um diese Funktion zu aktivieren. Wie im Abschnitt [Zwischenspeicherung bei Verwendung der Authentifizierung](#caching-when-authentication-is-used) Abschnitt, wenn Sie `/allowAuthorized 0 ` Anforderungen, die Authentifizierungsinformationen enthalten **not** zwischengespeichert. Wenn die Zwischenspeicherung unter Berücksichtigung von Berechtigungen erforderlich ist, lesen Sie den Abschnitt [Zwischenspeichern von geschützten Inhalten](https://experienceleague.adobe.com/docs/experience-manager-dispatcher/using/configuring/permissions-cache.html?lang=de) Seite.

Erstellen Sie eine sichere Sitzung für den Zugriff auf die Renderer-Farm, sodass sich Benutzer anmelden müssen, um auf jede Seite in der Farm zuzugreifen. Nach Anmeldung können sie dann auf Seiten in der Farm zugreifen. Siehe [Erstellen einer geschlossenen Benutzergruppe](https://experienceleague.adobe.com/docs/experience-manager-65/administering/security/cug.html?lang=en#creating-the-user-group-to-be-used) für Informationen zur Verwendung dieser Funktion mit CUGs. Lesen Sie vor der Live-Schaltung auch die [Sicherheits-Checkliste](/help/using/security-checklist.md) für den Dispatcher.

Die `/sessionmanagement`-Eigenschaft ist eine Untereigenschaft von `/farms`.

>[!CAUTION]
>
>Wenn verschiedene Abschnitte Ihrer Website unterschiedliche Zugriffsanforderungen verwenden, müssen Sie mehrere Farmen definieren.

**/sessionmanagement** weist mehrere Unterparameter auf:

**/directory** (erforderlich)

Das Verzeichnis, in dem die Sitzungsinformationen gespeichert werden. Wenn der Ordner nicht vorhanden ist, wird er erstellt.

>[!CAUTION]
>
> Beim Konfigurieren des Unterparameters directory **nicht** auf den Stammordner (`/directory "/"`), da dies schwerwiegende Probleme verursachen kann. Geben Sie immer den Pfad zum Ordner an, in dem die Sitzungsinformationen gespeichert werden. Beispiel:

```xml
/sessionmanagement
  {
  /directory "/usr/local/apache/.sessions"
  }
```

**/encode** (optional)

Kodierung der Sitzungsinformationen. Verwendung `md5` zur Verschlüsselung mit dem Md5-Algorithmus oder `hex` für die Hexadezimalkodierung. Wenn Sie die Sitzungsdaten verschlüsseln, kann ein Benutzer mit Zugriff auf das Dateisystem die Sitzungsinhalte nicht lesen. Der Standardwert lautet `md5`.

**/header** (optional)

Der Name des HTTP-Headers oder Cookies, in dem die Autorisierungsinformationen gespeichert werden. Wenn Sie die Informationen im HTTP-Header speichern möchten, verwenden Sie `HTTP:<header-name>`. Um die Informationen in einem Cookie zu speichern, verwenden Sie `Cookie:<header-name>`. Wenn Sie keinen Wert angeben, `HTTP:authorization` verwendet.

**/timeout** (optional)

Die Anzahl der Sekunden, bis die Sitzung nach der letzten Verwendung beendet wird. Wenn nicht angegeben `"800"` verwendet wird, sodass die Sitzung etwas länger als 13 Minuten nach der letzten Anforderung des Benutzers abläuft.

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

## Definieren von Seiten-Renderern {#defining-page-renderers-renders}

Mit der /renders-Eigenschaft wird die URL definiert, an die der Dispatcher Anforderungen zum Rendern eines Dokuments sendet. Der folgende beispielhafte `/renders`-Abschnitt definiert eine einzelne AEM-Instanz zum Rendern:

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

**/Zeitüberschreitung**

Gibt die Verbindungszeitüberschreitung für den Zugriff auf die AEM-Instanz in Millisekunden an. Der Standardwert ist `"0"`, wodurch der Dispatcher unbegrenzt lange warten musste.

**/receiveTimeout**

Gibt die Zeit in Millisekunden an, für die auf eine Antwort gewartet wird. Der Standardwert ist `"600000"`, wodurch der Dispatcher 10 Minuten warten musste. Eine Einstellung von `"0"` eliminiert die Zeitüberschreitung .

Wenn beim Analysieren der Antwortheader die Zeitüberschreitung auftritt, wird der HTTP-Status 504 (fehlerhaftes Gateway) zurückgegeben. Wenn die Zeitüberschreitung beim Lesen des Antworttextes erreicht wird, gibt der Dispatcher die unvollständige Antwort an den Client zurück. Außerdem werden alle Cachedateien gelöscht, die möglicherweise geschrieben wurden.

**/ipv4**

Gibt an, ob der Dispatcher die `getaddrinfo`-Funktion (für IPv6) oder die `gethostbyname`-Funktion (für IPv4) zum Abrufen der IP-Adresse des Renderers nutzt. Ein Wert von 0 bewirkt, dass `getaddrinfo` verwendet wird. Ein Wert von `1` verursacht `gethostbyname` verwendet werden. Der Standardwert ist `0`.

Die `getaddrinfo` gibt eine Liste von IP-Adressen zurück. Der Dispatcher iteriert die Liste der Adressen, bis er eine TCP/IP-Verbindung herstellt. Daher wird die `ipv4` -Eigenschaft ist wichtig, wenn der Renderer-Hostname mit mehreren IP-Adressen und dem Host als Antwort auf `getaddrinfo` -Funktion eine Liste von IP-Adressen zurückgibt, die sich immer in derselben Reihenfolge befinden. In diesem Fall sollten Sie die `gethostbyname` -Funktion, sodass die IP-Adresse, mit der sich der Dispatcher verbindet, randomisiert wird.

Amazon Elastic Load Balancing (ELB) ist ein Dienst, der auf getaddrinfo mit einer potenziell gleich geordneten IP-Adressenliste reagiert.

**/secure**

Wenn die Variable `/secure` -Eigenschaft hat den Wert `"1"`, verwendet der Dispatcher HTTPS zur Kommunikation mit der AEM-Instanz. Weitere Einzelheiten finden Sie auch unter [Konfigurieren des Dispatchers für die Verwendung von SSL](dispatcher-ssl.md#configuring-dispatcher-to-use-ssl).

**/always-resolve**

Mit der Dispatcher-Version **4.1.6** können Sie die `/always-resolve`-Eigenschaft wie folgt konfigurieren:

* Wenn festgelegt auf `"1"`aufgelöst, wird der Hostname bei jeder Anfrage aufgelöst (der Dispatcher speichert keine IP-Adresse zwischen). Aufgrund des zusätzlichen Aufrufs, der erforderlich ist, um die Hostinformationen für jede Anfrage abzurufen, kann es zu leichten Leistungseinbußen kommen.
* Wenn die Eigenschaft nicht festgelegt ist, wird die IP-Adresse standardmäßig zwischengespeichert.

Diese Eigenschaft kann auch verwendet werden, wenn Probleme mit der dynamischen IP-Auflösung auftreten, siehe folgendes Beispiel:

```xml
/renders {
  /0001 {
     /hostname "host-name-here"
     /port "4502"
     /ipv4 "1"
     /always-resolve "1"
     }
  }
```

## Konfigurieren des Zugriffs auf Inhalte {#configuring-access-to-content-filter}

Verwenden Sie den `/filter`-Abschnitt, um die HTTP-Anfragen anzugeben, die der Dispatcher akzeptiert. Alle anderen Anfragen werden zum Webserver mit dem Fehlercode 404 (Seite nicht gefunden) zurückgesendet. Wenn kein `/filter`-Abschnitt vorhanden ist, werden alle Anfragen akzeptiert.

**Hinweis:** Anforderungen für die [Statfile](#naming-the-statfile) werden immer abgelehnt.

>[!CAUTION]
>
>In der [Dispatcher-Sicherheits-Checkliste](security-checklist.md) finden Sie weitere Aspekte, wenn der Zugriff unter Verwendung des Dispatchers eingeschränkt ist. Lesen Sie auch den Abschnitt [AEM Sicherheitscheckliste](https://experienceleague.adobe.com/docs/experience-manager-65/administering/security/security-checklist.html?lang=de#security) für zusätzliche Sicherheitsdetails bezüglich Ihrer AEM Installation.

Die `/filter` -Abschnitt besteht aus einer Reihe von Regeln, die den Zugriff auf Inhalte gemäß den Mustern im Anforderungszeilen-Teil der HTTP-Anforderung verweigern oder zulassen. Verwenden Sie eine Zulassungsliste-Strategie für Ihre `/filter` Abschnitt:

* Erstens, verweigern Sie den Zugriff auf alles.
* Erlauben Sie den Zugriff auf die Inhalte nach Bedarf.

>[!NOTE]
>
>Bereinigen Sie den Cache bei jeder Änderung der Filterregeln.

### Definieren eines Filters  {#defining-a-filter}

Jedes Element im `/filter`-Abschnitt enthält einen Typ und ein Muster, die mit einem bestimmten Element der Anfragezeile oder mit der gesamten Anfragezeile abgeglichen werden. Jeder Filter kann die folgenden Elemente enthalten:

* **Typ**: `/type` gibt an, ob Anforderungen, die mit dem Muster übereinstimmen, der Zugriff erlaubt oder verweigert wird. Der Wert kann entweder `allow` oder `deny` lauten.

* **Element der Anforderungszeile:** Verwenden Sie `/method`, `/url`, `/query` oder `/protocol` und ein Muster, um Anfragen entsprechend diesen Elementen zu filtern. Das Filtern nach Elementen der Anforderungszeile (und nicht nach der gesamten Anforderungszeile) ist die bevorzugte Filtermethode.

* **Erweiterte Elemente der Anforderungszeile:** Ab Dispatcher 4.2.0 sind vier neue Filterelemente verfügbar. Diese neuen Elemente sind `/path`, `/selectors`, `/extension`, und `/suffix` bzw. Nutzen Sie eines oder mehrere dieser Elemente zur weiteren Kontrolle von URL-Mustern.

>[!NOTE]
>
>Weitere Informationen dazu, welcher Teil der Anforderungszeile jedes dieser Elemente referenziert, finden Sie unter [Sling-URL-Dekomposition](https://sling.apache.org/documentation/the-sling-engine/url-decomposition.html) Wiki-Seite.

* **glob-Eigenschaft**: Die `/glob`-Eigenschaft wird verwendet, wenn eine Übereinstimmung mit der gesamten Anforderungszeile der HTTP-Anforderung gegeben sein soll.

>[!CAUTION]
>
>Filtern nach Dateien mit Wildcards (globs) wird vom Dispatcher nicht mehr unterstützt. Von daher sollte die Verwendung von Dateinamen mit Wildcards (globs) in den `/filter`-Abschnitten vermieden werden, zumal diese zu Sicherheitsproblemen führen können. Entsprechend sollten Sie anstatt
>
>`/glob "* *.css *"`
>
>use
>
>`/url "*.css"`

#### Der Anforderungszeilen-Teil von HTTP-Anforderungen {#the-request-line-part-of-http-requests}

HTTP/1.1 definiert die [request-line](https://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html) wie folgt:

`Method Request-URI HTTP-Version<CRLF>`

Die `<CRLF>` -Zeichen stellen einen Zeilenumbruch dar, gefolgt von einem Zeilenvorschub. Das folgende Beispiel zeigt die Anforderungszeile, die empfangen wird, wenn ein Client die US-englische Seite der WKND-Site anfordert:

`GET /content/wknd/us/en.html HTTP.1.1<CRLF>`

Ihre Muster müssen die Leerzeichen in der Anfragezeile und die `<CRLF>` Zeichen.

#### Doppelte Anführungszeichen vs. einfache Anführungszeichen {#double-quotes-vs-single-quotes}

Verwenden Sie bei der Erstellung Ihrer Filterregeln doppelte Anführungszeichen `"pattern"` für einfache Muster. Wenn Sie die Dispatcher-Version 4.2.0 oder höher verwenden und Ihr Muster einen regulären Ausdruck enthält, müssen Sie das RegEx-Muster `'(pattern1|pattern2)'` in einfache Anführungszeichen setzen.

#### Reguläre Ausdrücke {#regular-expressions}

In Dispatcher-Versionen nach 4.2.0 können Sie POSIX-erweiterte reguläre Ausdrücke in Ihre Filtermuster aufnehmen.

#### Fehlerbehebung bei Filtern {#troubleshooting-filters}

Wenn Ihre Filter nicht wie erwartet ausgelöst werden, aktivieren Sie [Ablaufprotokollierung](#trace-logging) auf dem Dispatcher angezeigt, damit Sie sehen können, welcher Filter die Anforderung abfängt.

#### Beispielfilter: Alle verweigern {#example-filter-deny-all}

Bei dem folgendem Beispiel Filterabschnitt verweigert der Dispatcher Anforderungen für alle Dateien. Verweigern Sie den Zugriff auf alle Dateien und erlauben Sie dann den Zugriff auf bestimmte Bereiche.

```xml
/0001  { /type "deny" /url "*"  }
```

Anforderungen für explizit verweigerte Bereiche führen zur Rückgabe des Fehlercodes 404 (Seite nicht gefunden).

#### Beispielfilter: Zugriff auf bestimmte Bereiche verweigern {#example-filter-deny-access-to-specific-areas}

Mit Filtern können Sie auch den Zugriff auf verschiedene Elemente verweigern, z. B. ASP-Seiten und sensible Bereiche in einer Veröffentlichungsinstanz. Mit dem folgenden Filter wird der Zugriff auf ASP-Seiten verweigert:

```xml
/0002  { /type "deny" /url "*.asp"  }
```

#### Beispielfilter: Aktivieren von POST-Anforderungen {#example-filter-enable-post-requests}

Mit dem folgenden Beispielfilter wird das Senden von Formulardaten mit der POST-Methode ermöglicht:

```xml
/filter {
    /0001  { /glob "*" /type "deny" }
    /0002 { /type "allow" /method "POST" /url "/content/[.]*.form.html" }
}
```

#### Beispielfilter: Ermöglichen des Zugriffs auf die Workflow-Konsole {#example-filter-allow-access-to-the-workflow-console}

Das folgende Beispiel zeigt einen Filter, der verwendet wird, um externen Zugriff auf die Workflow-Konsole zu ermöglichen:

```xml
/filter {
    /0001  { /glob "*" /type "deny" }
    /0002  {  /type "allow"  /url "/libs/cq/workflow/content/console*"  }
}
```

Wenn Ihre Veröffentlichungsinstanz einen Webanwendungskontext verwendet (z. B. Veröffentlichung), kann sie auch zu Ihrer Filterdefinition hinzugefügt werden.

```xml
/0003   { /type "deny"  /url "/publish/libs/cq/workflow/content/console/archive*"  }
```

Wenn Sie innerhalb des eingeschränkten Bereichs auf einzelne Seiten zugreifen müssen, können Sie den Zugriff darauf zulassen. Um beispielsweise den Zugriff auf die Registerkarte Archiv in der Workflow-Konsole zuzulassen, fügen Sie den folgenden Abschnitt hinzu:

```xml
/0004  { /type "allow"  /url "/libs/cq/workflow/content/console/archive*"   }
```

>[!NOTE]
>
>Wenn mehrere Filtermuster auf eine Anforderung zutreffen, ist das zuletzt angewendete Filtermuster effektiv.

#### Beispielfilter: Verwenden von regulären Ausdrücken {#example-filter-using-regular-expressions}

Dieser Filter ermöglicht mithilfe dieses regulären Ausdrucks (in einfachen Anführungszeichen) Erweiterungen in nicht öffentlichen Inhaltsordnern:

```xml
/005  {  /type "allow" /extension '(css|gif|ico|js|png|swf|jpe?g)' }
```

#### Beispielfilter: Filtern zusätzlicher Elemente einer Anforderungs-URL  {#example-filter-filter-additional-elements-of-a-request-url}

Nachfolgend finden Sie ein Regelbeispiel, das die Inhaltsabfrage aus der `/content` path und seine Unterstruktur mit Filtern für Pfad, Selektoren und Erweiterungen:

```xml
/006 {
        /type "deny"
        /path "/content/*"
        /selectors '(feed|rss|pages|languages|blueprint|infinity|tidy|sysview|docview|query|jcr:content|_jcr_content|search|childrenlist|ext|assets|assetsearch|[0-9-]+)'
        /extension '(json|xml|html|feed))'
        }
```

### Beispiel /filter -Abschnitt {#example-filter-section}

Bei der Konfiguration des Dispatchers sollten Sie den externen Zugriff so weit wie möglich einschränken. Im folgenden Beispiel wird der Zugriff auf externe Besucher minimal gehalten:

* `/content`
* Verschiedene Inhalte wie Designs und Client-Bibliotheken. Zum Beispiel:

   * `/etc/designs/default*`
   * `/etc/designs/mydesign*`

Nachdem Sie Filter erstellt haben, [Testseitenzugriff](#testing-dispatcher-security) , um sicherzustellen, dass Ihre AEM-Instanz sicher ist.

Die folgenden `/filter` Abschnitt `dispatcher.any` -Datei als Grundlage in Ihrer [Dispatcher-Konfigurationsdatei.](#dispatcher-configuration-files)

Dieses Beispiel beruht auf der Standardkonfigurationsdatei, die mit dem Dispatcher zur Verfügung gestellt wird, und dient als Beispiel für die Verwendung in einer Produktionsumgebung. Elemente mit dem Präfix `#` deaktiviert (auskommentiert) werden. Wenn Sie sich entscheiden, eines dieser Elemente zu aktivieren (indem Sie die `#` auf dieser Zeile). Dies kann Auswirkungen auf die Sicherheit haben.

Verweigern Sie den Zugriff auf alles und erlauben Sie dann den Zugriff auf bestimmte (begrenzte) Elemente:

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
      /0001  { /type "deny" /url "*"  }

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
>Wenn Sie Apache verwenden, gestalten Sie Ihre Filter-URL-Muster entsprechend der Dispatcher UseProcessedURL-Eigenschaft des Dispatcher-Moduls. (Siehe [Apache-Webserver – Konfigurieren des Apache-Webservers für den Dispatcher](dispatcher-install.md##apache-web-server-configure-apache-web-server-for-dispatcher).)

<!----
>[!NOTE]
>
>Filters `0030` and `0031` regarding Dynamic Media are applicable to AEM 6.0 and higher. -->

Beachten Sie die folgenden Empfehlungen, wenn Sie den Zugriff erweitern:

* Externen Zugriff auf deaktivieren `/admin` wenn Sie CQ Version 5.4 oder eine frühere Version verwenden.

* Gewähren Sie den Zugriff auf die Dateien in `/libs` mit Bedacht. Der Zugriff sollte bestimmten Personen möglich sein.
* Verweigern Sie den Zugriff auf die Replikationskonfiguration, sodass diese nicht angezeigt wird:

   * `/etc/replication.xml*`
   * `/etc/replication.infinity.json*`

* Verweigern Sie den Zugriff auf den Reverse-Proxy von Google-Gadgets:

   * `/libs/opensocial/proxy*`

Je nach Installation stehen unter `/libs`, `/apps` oder an einem anderen Ort möglicherweise zusätzliche Ressourcen zur Verfügung, die ebenfalls verfügbar gemacht werden müssen. Sie können die Datei `access.log` zum Ermitteln von Ressourcen verwenden, auf die extern zugegriffen wird.

>[!CAUTION]
>
>Der Zugriff auf Konsolen und Ordner kann ein Sicherheitsrisiko für Produktionsumgebungen darstellen. Sofern Sie keine ausdrücklichen Rechtfertigungen haben, sollten diese deaktiviert bleiben (auskommentiert).

>[!CAUTION]
>
>Wenn Sie [Verwendung von Berichten in einer Veröffentlichungsumgebung](https://experienceleague.adobe.com/docs/experience-manager-65/administering/operations/reporting.html?lang=en#using-reports-in-a-publish-environment)sollten Sie den Dispatcher so konfigurieren, dass der Zugriff auf `/etc/reports` für externe Besucher.

### Einschränken von Abfragezeichenfolgen {#restricting-query-strings}

Seit der Dispatcher-Version 4.1.5 können Sie den `/filter`-Abschnitt zum Einschränken von Abfragezeichenfolgen nutzen. Es wird dringend empfohlen, mit `allow`-Filterelementen nur bestimmte Abfragezeichenfolgen explizit zuzulassen und alle anderen zu verweigern.

Ein einzelner Eintrag kann `glob` oder eine Kombination aus `method`, `url`, `query`, und `version`, aber nicht beides. Im folgenden Beispiel wird die Abfragezeichenfolge `a=*` zugelassen. Alle anderen Abfragezeichenfolgen für URLs, die zum Knoten `/etc` auflösen, werden verweigert:

```xml
/filter {
 /0001 { /type "deny" /method "POST" /url "/etc/*" }
 /0002 { /type "allow" /method "GET" /url "/etc/*" /query "a=*" }
}
```

>[!NOTE]
>
>Wenn eine Regel eine `/query`, werden nur Anforderungen abgeglichen, die eine Abfragezeichenfolge enthalten und mit dem angegebenen Abfragemuster übereinstimmen.
>
>Wenn im vorstehenden Beispiel Anfragen für `/etc`, die keine Abfragezeichenfolge aufweisen, ebenfalls zulässig sein sollen, wären folgende Regeln nötig:
>

```xml
/filter {  
>/0001 { /type "deny" /method "*" /url "/path/*" }  
>/0002 { /type "allow" /method "GET" /url "/path/*" }  
>/0003 { /type "deny" /method "GET" /url "/path/*" /query "*" }  
>/0004 { /type "allow" /method "GET" /url "/path/*" /query "a=*" }  
}  
```

### Testen der Dispatcher-Sicherheit {#testing-dispatcher-security}

Dispatcher-Filter sollten den Zugriff auf die folgenden Seiten und Skripte in AEM Veröffentlichungsinstanzen blockieren. Verwenden Sie einen Webbrowser, um zu versuchen, die folgenden Seiten so zu öffnen, wie es ein Site-Besucher tun würde, und überprüfen Sie, ob der Code 404 zurückgegeben wird. Wenn ein anderes Ergebnis erzielt wird, passen Sie Ihre Filter an.

Sie sollten das normale Seiten-Rendering für `/content/add_valid_page.html?debug=layout`.

* `/admin`
* `/system/console`
* `/dav/crx.default`
* `/crx`
* `/bin/crxde/logs`
* `/jcr:system/jcr:versionStorage.json`
* `/_jcr_system/_jcr_versionStorage.json`
* `/libs/wcm/core/content/siteadmin.html`
* `/libs/collab/core/content/admin.html`
* `/libs/cq/ui/content/dumplibs.html`
* `/var/linkchecker.html`
* `/etc/linkchecker.html`
* `/home/users/a/admin/profile.json`
* `/home/users/a/admin/profile.xml`
* `/libs/cq/core/content/login.json`
* `/content/../libs/foundation/components/text/text.jsp`
* `/content/.{.}/libs/foundation/components/text/text.jsp`
* `/apps/sling/config/org.apache.felix.webconsole.internal.servlet.OsgiManager.config/jcr%3acontent/jcr%3adata`
* `/libs/foundation/components/primary/cq/workflow/components/participants/json.GET.servlet`
* `/content.pages.json`
* `/content.languages.json`
* `/content.blueprint.json`
* `/content.-1.json`
* `/content.10.json`
* `/content.infinity.json`
* `/content.tidy.json`
* `/content.tidy.-1.blubber.json`
* `/content/dam.tidy.-100.json`
* `/content/content/geometrixx.sitemap.txt `
* `/content/add_valid_page.query.json?statement=//*`
* `/content/add_valid_page.qu%65ry.js%6Fn?statement=//*`
* `/content/add_valid_page.query.json?statement=//*[@transportPassword]/(@transportPassword%20|%20@transportUri%20|%20@transportUser)`
* `/content/add_valid_path_to_a_page/_jcr_content.json`
* `/content/add_valid_path_to_a_page/jcr:content.json`
* `/content/add_valid_path_to_a_page/_jcr_content.feed`
* `/content/add_valid_path_to_a_page/jcr:content.feed`
* `/content/add_valid_path_to_a_page/pagename._jcr_content.feed`
* `/content/add_valid_path_to_a_page/pagename.jcr:content.feed`
* `/content/add_valid_path_to_a_page/pagename.docview.xml`
* `/content/add_valid_path_to_a_page/pagename.docview.json`
* `/content/add_valid_path_to_a_page/pagename.sysview.xml`
* `/etc.xml`
* `/content.feed.xml`
* `/content.rss.xml`
* `/content.feed.html`
* `/content/add_valid_page.html?debug=layout`
* `/projects`
* `/tagging`
* `/etc/replication.html`
* `/etc/cloudservices.html`
* `/welcome`

Um festzustellen, ob der anonyme Schreibzugriff aktiviert ist, geben Sie den folgenden Befehl in einem Terminal oder an einer Eingabeaufforderung aus. Sie sollten keine Daten in den Knoten schreiben können.

`curl -X POST "https://anonymous:anonymous@hostname:port/content/usergenerated/mytestnode"`

Um zu versuchen, den Dispatcher-Cache ungültig zu machen und sicherzustellen, dass Sie eine Antwort mit Code 403 erhalten, geben Sie den folgenden Befehl in einem Terminal oder an einer Eingabeaufforderung aus:

`curl -H "CQ-Handle: /content" -H "CQ-Path: /content" https://yourhostname/dispatcher/invalidate.cache`

## Zugriff auf Vanity-URLs aktivieren {#enabling-access-to-vanity-urls-vanity-urls}

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (sbroders@adobe.com)
Last Modified Date: 2015-03-25T14:23:05.185-0400

<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">For https://jira.corp.adobe.com/browse/DOC-4812</p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">The "com.adobe.granite.dispatcher.vanityurl.content" package needs to be made public before publishing this contnet.</p>
 -->

Konfigurieren Sie den Dispatcher, um den Zugriff auf Vanity-URLs zu aktivieren, die für Ihre AEM konfiguriert sind.

Wenn der Zugriff auf Vanity-URLs aktiviert ist, ruft der Dispatcher regelmäßig einen Dienst auf, der auf der Renderinstanz ausgeführt wird, um eine Liste von Vanity-URLs zu erhalten. Der Dispatcher speichert diese Liste in einer lokalen Datei. Wenn eine Seitenanfrage wegen eines Filters im `/filter`-Abschnitt verweigert wird, prüft der Dispatcher diese Liste von Vanity-URLs. Befindet sich die abgelehnte URL in der Liste, wird der Dispatcher den Zugriff auf diese Vanity-URL zulassen.

Um den Zugriff auf Vanity-URLs zu aktivieren, fügen Sie einen `/vanity_urls`-Abschnitt zum `/farms`-Abschnitt hinzu, ähnlich wie im folgenden Beispiel:

```xml
 /vanity_urls {
      /url "/libs/granite/dispatcher/content/vanityUrls.html"
      /file "/tmp/vanity_urls"
      /delay 300
 }
```

Der `/vanity_urls`-Abschnitt enthält die folgenden Eigenschaften:

* `/url`: Der Pfad zum Vanity-URL-Dienst, der auf der Renderer-Instanz ausgeführt wird. Der Wert dieser Eigenschaft muss `"/libs/granite/dispatcher/content/vanityUrls.html"` lauten.

* `/file`: Der Pfad zur lokalen Datei, in der der Dispatcher die Liste der Vanity-URLs speichert. Vergewissern Sie sich, dass der Dispatcher über Schreibzugriff auf diese Datei verfügt.
* `/delay`: (Sekunden) Die Zeit zwischen den einzelnen Aufrufen des Vanity-URL-Diensts.

>[!NOTE]
>
>Wenn Ihr Renderer eine Instanz von AEM ist, müssen Sie die [VanityURLS-Components-Paket von Software Distribution](https://experience.adobe.com/#/downloads/content/software-distribution/en/aem.html?package=/content/software-distribution/en/details.html/content/dam/aem/public/adobe/packages/granite/vanityurls-components) , um den Vanity-URL-Dienst zu aktivieren. (Siehe [Softwareverteilung](https://experienceleague.adobe.com/docs/experience-manager-65/administering/contentmanagement/package-manager.html?lang=en#software-distribution) für weitere Details.)

Führen Sie die folgenden Schritte aus, um den Zugriff auf Vanity-URLs zu aktivieren.

1. Wenn Ihr Render-Dienst eine AEM-Instanz ist, installieren Sie die `com.adobe.granite.dispatcher.vanityurl.content` auf der Veröffentlichungsinstanz (siehe Hinweis oben).
1. Stellen Sie für jede Vanity-URL, die Sie für eine AEM- oder CQ-Seite konfiguriert haben, sicher, dass die [`/filter`](#configuring-access-to-content-filter)-Konfiguration die URL verweigert. Fügen Sie ggf. einen Filter hinzu, durch den die URL verweigert wird.
1. Ergänzen Sie den `/vanity_urls`-Abschnitt, der sich unter `/farms` befindet.
1. Starten Sie den Apache-Webserver neu.

## Weiterleiten von Syndizierungsanforderungen – /propagateSyndPost  {#forwarding-syndication-requests-propagatesyndpost}

Syndizierungsanforderungen sind nur für den Dispatcher vorgesehen, sodass sie standardmäßig nicht an den Renderer (z. B. eine AEM Instanz) gesendet werden.

Legen Sie bei Bedarf die `/propagateSyndPost` Eigenschaft auf `"1"` , um Syndizierungsanfragen an den Dispatcher weiterzuleiten. Wenn diese Einstellung festgelegt ist, müssen Sie sicherstellen, dass POST-Anforderungen im Filterabschnitt nicht verweigert werden.

## Konfigurieren des Dispatcher-Caches - /cache {#configuring-the-dispatcher-cache-cache}

Über den `/cache`-Abschnitt wird gesteuert, wie der Dispatcher Dokumente zwischenspeichert. Konfigurieren Sie Untereigenschaften, um die Zwischenspeicherung nach Ihren Vorstellungen zu implementieren:

* `/docroot`
* `/statfile`
* `/serveStaleOnError`
* `/allowAuthorized`
* `/rules`
* `/statfileslevel`
* `/invalidate`
* `/invalidateHandler`
* `/allowedClients`
* `/ignoreUrlParams`
* `/headers`
* `/mode`
* `/gracePeriod`
* `/enableTTL`

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

### Festlegen des Cacheordners  {#specifying-the-cache-directory}

Die `/docroot`-Eigenschaft identifiziert den Ordner, in dem die Cachedateien gespeichert werden.

>[!NOTE]
>
>Der Wert muss exakt dem Pfad des Basisverzeichnisses des Webservers entsprechen, damit der Dispatcher und der Webserver dieselben Dateien verarbeiten.\
>Der Webserver ist für die Bereitstellung des richtigen Status-Codes verantwortlich, wenn die Dispatcher-Cachedatei verwendet wird. Daher ist es wichtig, dass auch er ihn finden kann.

Wenn Sie mehrere Farmen verwenden, muss jede Farm ein anderes Basisverzeichnis nutzen.

### Benennen der Statfile {#naming-the-statfile}

In der `/statfile`-Eigenschaft wird die Datei angegeben, die als Statfile verwendet werden soll. Der Dispatcher verwendet diese Datei, um den Zeitpunkt der letzten Inhaltsaktualisierung zu registrieren. Die Statfile kann eine beliebige Datei auf dem Webserver sein.

Die Statfile hat keinen Inhalt. Wenn der Inhalt aktualisiert wird, aktualisiert der Dispatcher den Zeitstempel. Die standardmäßige Statfile heißt `.stat` und im Basisverzeichnis gespeichert wird. Der Dispatcher blockiert den Zugriff auf die stat-Datei.

>[!NOTE]
>
>Wenn `/statfileslevel` konfiguriert ist, ignoriert der Dispatcher die `/statfile` -Eigenschaft und -Verwendung `.stat` als Namen.

### Bereitstellen veralteter Dokumente bei Auftreten von Fehlern {#serving-stale-documents-when-errors-occur}

Die `/serveStaleOnError`-Eigenschaft steuert, ob der Dispatcher als invalidiert gekennzeichnete Dokumente zurückgibt, wenn der Renderserver einen Fehler zurückgibt. Wenn eine Statfile geändert wird und zwischengespeicherten Inhalt invalidiert, löscht der Dispatcher standardmäßig den zwischengespeicherten Inhalt bei der nächsten Anforderung.

Wenn `/serveStaleOnError` auf `"1"`, löscht der Dispatcher invalidierten Inhalt nur dann aus dem Cache, wenn der Renderserver eine erfolgreiche Antwort zurückgibt. Bei einer 5xx-Antwort von AEM oder einer Zeitüberschreitung gibt der Dispatcher den veralteten Inhalt mit dem HTTP-Status 111 (Erneute Validierung fehlgeschlagen) zurück.

### Zwischenspeicherung bei Verwendung der Authentifizierung {#caching-when-authentication-is-used}

Die `/allowAuthorized`-Eigenschaft steuert, ob Anfragen, die eine der folgenden Authentifizierungsinformationen enthalten, zwischengespeichert werden:

* Die `authorization` header
* Ein Cookie mit dem Namen `authorization`
* Ein Cookie mit dem Namen `login-token`

Standardmäßig werden Anforderungen, die diese Authentifizierungsinformationen enthalten, nicht zwischengespeichert, da die Authentifizierung nicht durchgeführt wird, wenn ein zwischengespeichertes Dokument an den Client zurückgegeben wird. Diese Konfiguration verhindert, dass der Dispatcher zwischengespeicherte Dokumente für Benutzer bereitstellt, die nicht über die erforderlichen Berechtigungen verfügen.

Wenn Ihre Anforderungen jedoch das Zwischenspeichern authentifizierter Dokumente zulassen, legen Sie `/allowAuthorized` zu eins:

`/allowAuthorized "1"`

>[!NOTE]
>
>Für die Sitzungsverwaltung (mithilfe der `/sessionmanagement`-Eigenschaft) muss die `/allowAuthorized`-Eigenschaft auf `"0"` eingestellt sein.

### Festlegen der Dokumente, die zwischengespeichert werden sollen {#specifying-the-documents-to-cache}

Die `/rules`-Eigenschaft steuert anhand des Dokumentenpfads, welche Dokumente zwischengespeichert werden sollen. Unabhängig von der `/rules`-Eigenschaft werden in folgenden Fällen Dokumente nie zwischengespeichert:

* Der Anfrage-URI enthält ein Fragezeichen (`?`).
   * Gibt eine dynamische Seite an, z. B. ein Suchergebnis, das nicht zwischengespeichert werden muss.
* Die Dateierweiterung fehlt.
   * Der Webserver benötigt die Erweiterung, um den Dokumenttyp (den MIME-Typ) zu bestimmen.
* Der Authentifizierungs-Header ist festgelegt (konfigurierbar).
* Die AEM-Instanz antwortet mit den folgenden Headern:

   * `no-cache`
   * `no-store`
   * `must-revalidate`

>[!NOTE]
>
>Die Methoden GET oder HEAD (für den HTTP-Header) können vom Dispatcher zwischengespeichert werden. Weitere Informationen zum Zwischenspeichern von Antwortheadern finden Sie im Abschnitt [Zwischenspeichern von HTTP-Antwortheadern](#caching-http-response-headers).

Jedes Element im `/rules` -Eigenschaft enthält [`glob`](#designing-patterns-for-glob-properties) Muster und Typ:

* Die `glob` -Muster wird verwendet, um mit dem Pfad des Dokuments abzugleichen.
* Der Typ gibt an, ob die Dokumente, die mit dem `glob` Muster. Der Wert kann `allow` (zum Zwischenspeichern des Dokuments) oder `deny` (um das Dokument immer wiederzugeben).

Wenn Sie keine dynamischen Seiten haben (über die Seiten hinaus, die bereits durch die obigen Regeln ausgeschlossen sind), können Sie den Dispatcher so konfigurieren, dass alles zwischengespeichert wird. Der Regelabschnitt sieht wie folgt aus:

```xml
/rules
  {
    /0000  {  /glob "*"   /type "allow" }
  }
```

Weitere Informationen zu glob-Eigenschaften finden Sie unter [Entwerfen von Mustern für glob-Eigenschaften](#designing-patterns-for-glob-properties).

Wenn es einige Bereiche auf Ihrer Seite gibt, die dynamisch sind (z. B. eine News-Anwendung), oder in einer geschlossenen Benutzergruppe, können Sie Ausnahmen definieren:

>[!NOTE]
>
>Zwischenspeichern Sie keine geschlossenen Benutzergruppen, da die Benutzerrechte nicht für zwischengespeicherte Seiten überprüft werden.

```xml
/rules
  {
   /0000  { /glob "*" /type "allow" }
   /0001  { /glob "/en/news/*" /type "deny" }
   /0002  { /glob "*/private/*" /type "deny"  }
  }
```

**Komprimierung**

Auf Apache-Webservern können Sie die zwischengespeicherten Dokumente komprimieren. Dann kann Apache Dokumente komprimiert zurückgeben, wenn dies vom Client angefordert wird. Die Komprimierung erfolgt automatisch durch Aktivieren des Apache-Moduls `mod_deflate`, beispielsweise:

```xml
AddOutputFilterByType DEFLATE text/plain
```

Das Modul wird standardmäßig mit Apache 2.x installiert.

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

Verwenden Sie die `/statfileslevel`-Eigenschaft, um zwischengespeicherte Dateien anhand ihres Pfads als invalidiert zu kennzeichnen:

* Der Dispatcher erstellt `.stat`-Dateien in jedem Ordner ab dem Basisverzeichnis bis zu der von Ihnen angegebenen Ebene. Das Basisverzeichnis hat die Ordnerebene 0.
* Dateien werden durch Änderung der `.stat`-Datei als invalidiert gekennzeichnet. Das letzte Änderungsdatum der `.stat`-Datei wird mit dem letzten Änderungsdatum eines zwischengespeicherten Dokuments verglichen. Das Dokument wird neu abgerufen, wenn die `.stat`-Datei neuer ist.

* Wenn eine Datei auf einer bestimmten Ebene invalidiert wird, **all** `.stat` Dateien aus dem Basisverzeichnis **nach** die Ebene der invalidierten Datei oder der konfigurierten `statsfilevel` (je nachdem, welcher Wert kleiner ist) berührt werden.

   * Beispiel: Wenn Sie die Variable `statfileslevel` -Eigenschaft auf 6 gesetzt wurde und eine Datei auf Ebene 5 invalidiert wird, dann alle `.stat` -Datei vom Basisverzeichnis bis 5 geändert. Wenn eine Datei auf Ebene 7 invalidiert wird, wird in diesem Beispiel jede `stat` -Datei vom Basisverzeichnis bis sechs geändert werden (da `/statfileslevel = "6"`).

Nur Ressourcen **entlang des Pfads** auf die invalidierte Datei angewendet werden. Nehmen wir folgendes Beispiel: Eine Website verwendet die Struktur `/content/myWebsite/xx/.`. Wenn Sie `statfileslevel` auf 3 festlegen, wird eine `.stat`-Datei wie folgt erstellt:

* `docroot`
* `/content`
* `/content/myWebsite`
* `/content/myWebsite/*xx*`

Wenn eine Datei in `/content/myWebsite/xx` invalidiert wird, wird jede `.stat` -Datei nach unten `/content/myWebsite/xx`berührt wird. Dieses Szenario ist nur für `/content/myWebsite/xx` und nicht beispielsweise `/content/myWebsite/yy` oder `/content/anotherWebSite`.

>[!NOTE]
>
>Die Invalidierung kann durch Senden eines zusätzlichen Headers verhindert werden `CQ-Action-Scope:ResourceOnly`. Diese Methode kann verwendet werden, um bestimmte Ressourcen zu leeren, ohne andere Teile des Caches zu invalidieren. Siehe [diese Seite](https://adobe-consulting-services.github.io/acs-aem-commons/features/dispatcher-flush-rules/index.html) und [Manuelles Invalidieren des Dispatcher-Caches](https://experienceleague.adobe.com/docs/experience-manager-dispatcher/using/configuring/page-invalidate.html?lang=en#configuring) für weitere Details.

>[!NOTE]
>
>Hinweis: Wenn Sie einen Wert für die `/statfileslevel`-Eigenschaft angeben, wird die `/statfile`-Eigenschaft ignoriert.

### Automatische Invalidierung zwischengespeicherter Dateien {#automatically-invalidating-cached-files}

Die `/invalidate`-Eigenschaft definiert die Dokumente, die bei Aktualisierung des Inhalts automatisch invalidiert werden.

Bei der automatischen Invalidierung löscht der Dispatcher zwischengespeicherte Dateien nach einer Inhaltsaktualisierung nicht, prüft jedoch deren Gültigkeit, wenn sie zum nächsten Mal angefordert werden. Dokumente im Cache, die nicht automatisch invalidiert werden, verbleiben im Cache, bis sie durch eine Inhaltsaktualisierung explizit gelöscht werden.

Die automatische Invalidierung wird normalerweise für HTML-Seiten verwendet. HTML-Seiten enthalten häufig Links zu anderen Seiten, was es schwierig macht festzustellen, ob eine Inhaltsaktualisierung eine Seite betrifft. Um sicherzustellen, dass bei einer Inhaltsaktualisierung alle relevanten Seiten invalidiert werden, sollte dies für alle HTML-Seiten automatisch durchgeführt werden. Mit der folgenden Konfiguration werden alle HTML-Seiten invalidiert:

```xml
  /invalidate
  {
   /0000  { /glob "*" /type "deny" }
   /0001  { /glob "*.html" /type "allow" }
  }
```

Weitere Informationen zu glob-Eigenschaften finden Sie unter [Entwerfen von Mustern für glob-Eigenschaften](#designing-patterns-for-glob-properties).

Diese Konfiguration bewirkt die folgende Aktivität, wenn `/content/wknd/us/en` aktiviert ist:

* Alle Dateien mit dem Muster en.* aus dem `/content/wknd/us` Ordner.
* Die `/content/wknd/us/en./_jcr_content` -Ordner entfernt.
* Alle anderen Dateien, die mit dem `/invalidate` -Konfiguration nicht sofort gelöscht werden. Diese Dateien werden gelöscht, wenn die nächste Anforderung ausgeführt wird. Im Beispiel `/content/wknd.html` wird nicht gelöscht; sie wird gelöscht, wenn `/content/wknd.html` angefordert wird.

Wenn Sie automatisch generierte PDF- und ZIP-Dateien zum Herunterladen anbieten, müssen Sie diese Dateien möglicherweise auch automatisch ungültig machen. Ein Konfigurationsbeispiel sieht wie folgt aus:

```xml
/invalidate
  {
   /0000 { /glob "*" /type "deny" }
   /0001 { /glob "*.html" /type "allow" }
   /0002 { /glob "*.zip" /type "allow" }
   /0003 { /glob "*.pdf" /type "allow" }
  }
```

Die AEM-Integration mit Adobe Analytics stellt Konfigurationsdaten in einer `analytics.sitecatalyst.js` -Datei auf Ihrer Website. Das Beispiel `dispatcher.any` -Datei, die mit dem Dispatcher bereitgestellt wird, enthält die folgende Invalidierungsregel für diese Datei:

```xml
{
   /glob "*/analytics.sitecatalyst.js"  /type "allow"
}
```

### Verwenden von benutzerdefinierten Skripts zur Invalidierung  {#using-custom-invalidation-scripts}

Die `/invalidateHandler` -Eigenschaft können Sie ein Skript definieren, das für jede vom Dispatcher empfangene Invalidierungsanforderung aufgerufen wird.

Sie wird mit den folgenden Argumenten aufgerufen:

* Handle - Der Inhaltspfad, der ungültig gemacht wird
* Aktion - Die Replikationsaktion (z. B. Aktivieren, Deaktivieren)
* Aktionsbereich - Der Umfang der Replikationsaktion (leer, es sei denn, die Kopfzeile von `CQ-Action-Scope: ResourceOnly` gesendet wird, siehe [Invalidierung zwischengespeicherter Seiten aus AEM](page-invalidate.md) für Details)

Diese Methode kann für verschiedene Anwendungsfälle verwendet werden. Beispielsweise bei der Invalidierung anderer anwendungsspezifischer Caches oder zur Behandlung von Fällen, in denen die externalisierte URL einer Seite und deren Position im Basisverzeichnis nicht mit dem Inhaltspfad übereinstimmen.

Unten stehende Beispielskript protokolliert jede Invalidierungsanforderung in eine Datei.

```xml
/invalidateHandler "/opt/dispatcher/scripts/invalidate.sh"
```

#### Beispielskript für Invalidierungs-Handler {#sample-invalidation-handler-script}

```shell
#!/bin/bash

printf "%-15s: %s %s" $1 $2 $3>> /opt/dispatcher/logs/invalidate.log
```

### Eingrenzen der Clients, die den Cache leeren können {#limiting-the-clients-that-can-flush-the-cache}

Die `/allowedClients` -Eigenschaft definiert bestimmte Clients, die den Cache leeren dürfen. Die Globbing-Muster werden mit der IP abgeglichen.

Das folgende Beispiel:

1. verweigert Zugriff auf Clients
1. den Zugriff auf den localhost explizit zulassen

```xml
/allowedClients
  {
   /0001 { /glob "*.*.*.*"  /type "deny" }
   /0002 { /glob "127.0.0.1" /type "allow" }
  }
```

Weitere Informationen zu glob-Eigenschaften finden Sie unter [Entwerfen von Mustern für glob-Eigenschaften](#designing-patterns-for-glob-properties).

>[!CAUTION]
>
>Es wird empfohlen, die `/allowedClients`.
>
>Ist dies nicht der Fall, kann jeder Client einen Aufruf ausgeben, um den Cache zu löschen. Wenn dies wiederholt erfolgt, kann dies die Site-Leistung erheblich beeinträchtigen.

### Ignorieren von URL-Parametern  {#ignoring-url-parameters}

Im `ignoreUrlParams`-Abschnitt wird definiert, welche URL-Parameter bei der Prüfung, ob eine Seite zwischengespeichert wird oder aus dem Cache geliefert wird, ignoriert werden sollen:

* Wenn eine Anfrage-URL Parameter enthält, die alle ignoriert werden, wird die Seite zwischengespeichert.
* Wenn eine Anforderungs-URL einen oder mehrere Parameter enthält, die nicht ignoriert werden, wird die Seite nicht zwischengespeichert.

Wenn ein Parameter für eine Seite ignoriert wird, wird die Seite beim ersten Anfordern der Seite zwischengespeichert. Nachfolgende Anforderungen für die Seite werden auf der zwischengespeicherten Seite bereitgestellt, unabhängig vom Wert des Parameters in der Anfrage.

>[!NOTE]
>
>Es wird empfohlen, die `ignoreUrlParams` auf eine Zulassungsliste festzulegen. Daher werden alle Abfrageparameter ignoriert und nur bekannte oder erwartete Abfrageparameter werden von der Ignorierung ausgeschlossen (&quot;verweigert&quot;). Weitere Informationen und Beispiele finden Sie unter [diese Seite](https://github.com/adobe/aem-dispatcher-optimizer-tool/blob/main/docs/Rules.md#dot---the-dispatcher-publish-farm-cache-should-have-its-ignoreurlparams-rules-configured-in-an-allow-list-manner).

Um festzulegen, welche Parameter ignoriert werden, fügen Sie glob-Regeln zu der `ignoreUrlParams`-Eigenschaft hinzu:

* Um eine Seite zwischenzuspeichern, obwohl die Anfrage einen URL-Parameter enthält, erstellen Sie eine glob-Eigenschaft, die den Parameter ermöglicht (zu ignorieren).
* Um zu verhindern, dass die Seite zwischengespeichert wird, erstellen Sie eine glob-Eigenschaft, die den Parameter verweigert (zu ignorieren).

>[!NOTE]
>
>Bei der Konfiguration der glob-Eigenschaft sollte sie mit dem Parameternamen der Abfrage übereinstimmen. Wenn Sie beispielsweise den Parameter &quot;p1&quot;über die folgende URL ignorieren möchten `http://example.com/path/test.html?p1=test&p2=v2`festgelegt ist, sollte die glob-Eigenschaft wie folgt lauten:
> `/0002 { /glob "p1" /type "allow" }`

Im folgenden Beispiel ignoriert der Dispatcher alle Parameter mit Ausnahme der `nocache` -Parameter. Anforderungs-URLs, die die Variable `nocache` -Parameter werden vom Dispatcher nie zwischengespeichert:

```xml
/ignoreUrlParams
{
    # ignore-all-url-parameters-by-dispatcher-and-requests-are-cached
    /0001 { /glob "*" /type "allow" }
    # allow-the-url-parameter-nocache-to-bypass-dispatcher-on-every-request
    /0002 { /glob "nocache" /type "deny" }
}
```

Im Kontext der `ignoreUrlParams` Konfigurationsbeispiel oben bewirkt die folgende HTTP-Anforderung, dass die Seite zwischengespeichert wird, da die `willbecached` -Parameter wird ignoriert:

```xml
GET /mypage.html?willbecached=true
```

Im Kontext der `ignoreUrlParams` Konfigurationsbeispiel: Die folgende HTTP-Anforderung bewirkt, dass die Seite **not** zwischengespeichert werden, da die `nocache` -Parameter wird nicht ignoriert:

```xml
GET /mypage.html?nocache=true
GET /mypage.html?nocache=true&willbecached=true
```

Weitere Informationen zu glob-Eigenschaften finden Sie unter [Entwerfen von Mustern für glob-Eigenschaften](#designing-patterns-for-glob-properties).

### Zwischenspeichern von HTTP-Antwortheadern {#caching-http-response-headers}

>[!NOTE]
>
>Diese Funktion ist mit Version verfügbar. **4.1.11** des Dispatchers.

Mit der `/headers`-Eigenschaft können HTTP-Headertypen definiert werden, die vom Dispatcher zwischengespeichert werden sollen. Bei der ersten Anfrage an eine nicht zwischengespeicherte Ressource werden alle Header, die mit einem der konfigurierten Werte übereinstimmen (siehe Konfigurationsbeispiel unten), in einer separaten Datei neben der Cache-Datei gespeichert. Bei nachfolgenden Anfragen an die zwischengespeicherte Ressource werden die gespeicherten Header zur Antwort hinzugefügt.

Im Folgenden ein Beispiel aus der Standardkonfiguration:

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
>Datei-Globbing-Zeichen sind nicht zulässig. Weitere Einzelheiten finden Sie unter [Entwerfen von Mustern für Glob-Eigenschaften](#designing-patterns-for-glob-properties).

>[!NOTE]
>
>Gehen Sie wie folgt vor, wenn der Dispatcher ETag-Antwortheader von AEM speichern und übermitteln soll:
>
>* Fügen Sie den Headernamen in den `/cache/headers`-Abschnitt ein.
>* Fügen Sie Folgendes hinzu: [Apache-Richtlinie](https://httpd.apache.org/docs/2.4/mod/core.html#fileetag) im Dispatcher-Abschnitt:
>
>```xml
>FileETag none
>```

### Dispatcher Cache File Permissions {#dispatcher-cache-file-permissions}

Die `mode`-Eigenschaft gibt an, welche Dateiberechtigungen auf neue Verzeichnisse und Dateien im Cache angewendet werden. Diese Einstellung ist durch den Wert `umask` des aufrufenden Prozesses eingeschränkt. Es ist eine Oktalzahl, die aus der Summe eines oder mehrerer der folgenden Werte gebildet wird:

* `0400` Lesen durch Inhaber zulassen.
* `0200` Schreiben durch den Eigentümer zulassen.
* `0100` Dem Eigentümer erlauben, in Verzeichnissen zu suchen.
* `0040` Lesen durch Gruppenmitglieder zulassen.
* `0020` Schreiben durch Gruppenmitglieder zulassen.
* `0010` Gruppenmitglieder können im Verzeichnis suchen.
* `0004` Lesen durch andere zulassen.
* `0002` Schreiben durch andere zulassen.
* `0001` Ermöglichen Sie anderen die Suche im Verzeichnis.

Der Standardwert ist `0755` , die es dem Eigentümer ermöglicht, zu lesen, zu schreiben oder zu suchen und die Gruppe und andere zu lesen oder zu suchen.

### Drosselung der Änderung der .stat-Datei {#throttling-stat-file-touching}

Im Falle der `/invalidate`-Standardeigenschaft werden bei jeder Aktivierung alle `.html`-Dateien invalidiert (wenn ihr Pfad dem `/invalidate`-Abschnitt entspricht). Auf einer Website mit hohem Traffic erhöhen mehrere aufeinander folgende Aktivierungen die CPU-Last im Backend. In einem solchen Szenario ist es wünschenswert, &quot;zu drosseln&quot; `.stat` -Datei berühren, um die Website responsiv zu halten. Sie können diese Aktion mithilfe der `/gracePeriod` -Eigenschaft.

Die `/gracePeriod` -Eigenschaft definiert die Anzahl der Sekunden, die eine veraltete, automatisch invalidierte Ressource nach der letzten Aktivierung weiterhin aus dem Cache bereitgestellt werden kann. Die Eigenschaft kann in einem Setup verwendet werden, bei dem ein Stapel von Aktivierungen ansonsten wiederholt den gesamten Cache invalidieren würde. Der empfohlene Wert ist 2 Sekunden.

Weitere Informationen finden Sie auch in den Abschnitten `/invalidate` und `/statfileslevel` oben.

### Konfigurieren der zeitbasierten Cache-Invalidierung - /enableTTL {#configuring-time-based-cache-invalidation-enablettl}

Die zeitbasierte Cache-Invalidierung hängt von der `/enableTTL` -Eigenschaft und das Vorhandensein regulärer Ablaufkopfzeilen aus dem HTTP-Standard. Wenn Sie die Eigenschaft auf 1 festlegen (`/enableTTL "1"`), werden die Antwortheader vom Backend ausgewertet. Wenn die Kopfzeilen eine `Cache-Control`, `max-age` oder `Expires` -Datum wird eine leere Hilfsdatei neben der zwischengespeicherten Datei erstellt, deren Änderungszeitpunkt dem Ablaufdatum entspricht. Wenn die zwischengespeicherte Datei nach der Änderungszeit angefordert wird, wird sie automatisch erneut vom Backend angefordert.

Vor Dispatcher 4.3.5 basierte die TTL-Invalidierungslogik nur auf dem konfigurierten TTL-Wert. Mit Dispatcher 4.3.5, beidem die festgelegte TTL **und** die Invalidierungsregeln für den Dispatcher-Cache werden berücksichtigt. Daher gilt für eine zwischengespeicherte Datei Folgendes:

1. Wenn `/enableTTL` auf 1 gesetzt ist, wird der Dateiablauf aktiviert. Wenn die Datei gemäß der festgelegten TTL abgelaufen ist, werden keine weiteren Prüfungen durchgeführt und die zwischengespeicherte Datei wird erneut vom Backend angefordert.
2. Wenn die Datei noch nicht abgelaufen ist, oder `/enableTTL` nicht konfiguriert ist, werden die standardmäßigen Cache-Invalidierungsregeln angewendet, z. B. die Regeln, die von [/statfileslevel](#invalidating-files-by-folder-level) und [/invalidate](#automatically-invalidating-cached-files). Dieser Ablauf bedeutet, dass der Dispatcher Dateien ungültig machen kann, für die die TTL noch nicht abgelaufen ist.

Diese neue Implementierung unterstützt Anwendungsfälle, in denen Dateien eine längere TTL aufweisen (z. B. im CDN), aber dennoch invalidiert werden können, selbst wenn die TTL noch nicht abgelaufen ist. Sie bevorzugt die Inhaltsaktualisierung gegenüber dem Cache-Trefferverhältnis im Dispatcher.

Umgekehrt, falls Sie **only** Die auf eine Datei angewendete Ablauflogik wird dann festgelegt `/enableTTL` auf 1 gesetzt und schließen Sie diese Datei aus dem standardmäßigen Cache-Invalidierungsmechanismus aus. Sie können beispielsweise:

* Um die Datei zu ignorieren, konfigurieren Sie die [Invalidierungsregeln](#automatically-invalidating-cached-files) im Abschnitt &quot;Cache&quot;. Im unten stehenden Codefragment enden alle Dateien in `.example.html` werden ignoriert und laufen nur ab, wenn die festgelegte TTL übergeben wurde.

```xml
  /invalidate
  {
   /0000  { /glob "*" /type "deny" }
   /0001  { /glob "*.html" /type "allow" }
   /0002  { /glob "*.example.html" /type "deny" }
  }
```

* Entwerfen Sie die Inhaltsstruktur so, dass Sie einen hohen [/statfilelevel](#invalidating-files-by-folder-level) sodass die Datei nicht automatisch invalidiert wird.

Dadurch wird sichergestellt, dass `.stat` -Dateiinvalidierung nicht verwendet wird und nur der TTL-Ablauf für die angegebenen Dateien aktiv ist.

>[!NOTE]
>
>Beachten Sie Folgendes: `/enableTTL` auf 1 aktiviert die TTL-Zwischenspeicherung nur auf der Dispatcher-Seite. Daher werden die in der zusätzlichen Datei enthaltenen TTL-Informationen (siehe oben) keinem anderen Benutzeragenten bereitgestellt, der einen solchen Dateityp vom Dispatcher anfordert. Wenn Sie nachgelagerten Systemen wie einem CDN oder einem Browser Caching-Header bereitstellen möchten, sollten Sie die `/cache/headers` entsprechend.

>[!NOTE]
>
>Diese Funktion ist in Version verfügbar **4.1.11** oder höher des Dispatchers.

## Konfigurieren des Lastenausgleichs - /statistics {#configuring-load-balancing-statistics}

Im `/statistics`-Abschnitt werden Kategorien von Dateien definiert, für die der Dispatcher die Reaktionsfähigkeit jedes Renderers bewertet. Der Dispatcher verwendet die Bewertungen, um zu bestimmen, welcher Renderer eine Anfrage senden soll.

Jede von Ihnen erstellte Kategorie definiert ein glob-Muster. Der Dispatcher vergleicht den URI des angeforderten Inhalts mit diesen Mustern, um die Kategorie des angeforderten Inhalts zu bestimmen:

* Die Reihenfolge der Kategorien bestimmt die Reihenfolge, in der sie mit dem URI verglichen werden.
* Das erste Kategoriemuster, das mit dem URI übereinstimmt, ist die Kategorie der Datei. Es werden keine Kategoriemuster mehr ausgewertet.

Der Dispatcher unterstützt maximal acht Statistikkategorien. Wenn Sie mehr als acht Kategorien definieren, werden nur die ersten 8 verwendet.

**Renderauswahl**

Jedes Mal, wenn der Dispatcher eine gerenderte Seite benötigt, verwendet er den folgenden Algorithmus zur Auswahl des Renderers:

1. Wenn die Anforderung den Namen des Renderers in einem `renderid`-Cookie enthält, verwendet der Dispatcher diesen Renderer.
1. Wenn die Anforderung keinen `renderid`-Cookie enthält, vergleicht der Dispatcher die Renderer-Statistiken:

   1. Der Dispatcher bestimmt die Kategorie des Anfrage-URI.
   1. Der Dispatcher bestimmt, welcher Renderer den niedrigsten Antwortwert für diese Kategorie hat, und wählt diesen Renderer aus.

1. Wenn noch kein Renderer ausgewählt ist, verwenden Sie den ersten Renderer in der Liste.

Die Bewertung für die Kategorie eines Renderers basiert auf früheren Antwortzeiten sowie auf früheren fehlgeschlagenen und erfolgreichen Verbindungen, die der Dispatcher versucht. Für jeden Versuch wird das Ergebnis für die Kategorie des angeforderten URI aktualisiert.

>[!NOTE]
>
>Wenn Sie keinen Lastenausgleich verwenden, können Sie diesen Abschnitt auslassen.

### Definieren von Statistikkategorien  {#defining-statistics-categories}

Definieren Sie eine Kategorie für jeden Dokumenttyp, für den Sie Statistiken zur Renderer-Auswahl beibehalten möchten. Die `/statistics` -Abschnitt enthält eine `/categories` Abschnitt. Um eine Kategorie zu definieren, fügen Sie eine Zeile unterhalb der `/categories` -Abschnitt mit folgendem Format:

`/name { /glob "pattern"}`

Die Kategorie `name` muss für die Farm eindeutig sein. `pattern` wird im Abschnitt [Entwerfen von Mustern für glob-Eigenschaften](#designing-patterns-for-glob-properties) beschrieben.

Um die Kategorie eines URI zu bestimmen, vergleicht der Dispatcher den URI mit jedem Kategoriemuster, bis eine Übereinstimmung gefunden wird. Der Dispatcher beginnt mit der ersten Kategorie in der Liste und fährt in der Reihenfolge fort. Platzieren Sie daher zuerst Kategorien mit spezifischeren Mustern.

Der Dispatcher ist beispielsweise die Standardeinstellung `dispatcher.any` -Datei definiert eine HTML-Kategorie und eine andere Kategorie. Die Kategorie „HTML“ ist die spezifischere und erscheint daher als erste:

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

Mit der `/unavailablePenalty`-Eigenschaft wird die Strafzeit (in Zehntelsekunden) für Renderer-Statistiken festgelegt, die beim Fehlschlagen einer Verbindung zu einem Renderer angewendet wird. Der Dispatcher addiert diese Zeit der Statistikkategorie hinzu, die dem angeforderten URI entspricht.

Beispielsweise wird diese Strafzeit angewendet, wenn die TCP/IP-Verbindung zum angegebenen Hostnamen/Port nicht hergestellt werden konnte, weil AEM nicht ausgeführt wird (und nicht empfangsbereit ist), oder aufgrund von netzwerkbezogenen Problemen.

Die `/unavailablePenalty`-Eigenschaft ist ein direktes untergeordnetes Element des `/farm`-Abschnitts (gleichrangig mit dem `/statistics`-Abschnitt).

Wenn nicht `/unavailablePenalty` -Eigenschaft vorhanden ist, ist der Wert von `"1"` verwendet.

```xml
/unavailablePenalty "1"
```

## Identifizieren eines Ordners mit gebundener Verbindung - /stickyConnectionsFor {#identifying-a-sticky-connection-folder-stickyconnectionsfor}

Die `/stickyConnectionsFor` -Eigenschaft definiert einen Ordner, der gebundene Dokumente enthält. Der Zugriff auf diese Eigenschaft erfolgt über die URL. Der Dispatcher sendet alle Anforderungen von einem einzelnen Benutzer, der sich in diesem Ordner befindet, an dieselbe Renderinstanz. Sticky-Verbindungen stellen sicher, dass Sitzungsdaten für alle Dokumente vorhanden und konsistent sind. Für diesen Mechanismus wird der `renderid`-Cookie verwendet.

Im folgenden Beispiel wird eine gebundene Verbindung zum /products-Ordner definiert:

```xml
/stickyConnectionsFor "/products"
```

Wenn eine Seite aus Inhalten von mehreren Inhaltsknoten besteht, beziehen Sie die `/paths`-Eigenschaft ein, die die Pfade zum Inhalt auflistet. Eine Seite kann z. B. Inhalte von `/content/image`, `/content/video` und `/var/files/pdfs` enthalten. Die folgende Konfiguration ermöglicht gebundene Verbindungen für alle Inhalte auf der Seite:

```xml
/stickyConnections {
  /paths {
    "/content/image"
    "/content/video"
    "/var/files/pdfs"
  }
}
```

### httpOnly {#httponly}

Wenn Sticky-Verbindungen aktiviert sind, legt das Dispatcher-Modul die `renderid` Cookie. Dieses Cookie verfügt nicht über die `httponly` -Markierung, die hinzugefügt werden sollte, um die Sicherheit zu erhöhen. Sie fügen die `httponly` Markierung durch Festlegen der `httpOnly` -Eigenschaft in der `/stickyConnections` Knoten eines `dispatcher.any` Konfigurationsdatei. Der Wert der Eigenschaft (entweder `0` oder `1`) definiert, ob die Variable `renderid` -Cookie verfügt über die `HttpOnly` angehängt. Der Standardwert ist `0`, was bedeutet, dass das -Attribut nicht hinzugefügt wird.

Weitere Informationen zum `httponly` Markierung, lesen [diese Seite](https://www.owasp.org/index.php/HttpOnly).

### secure {#secure}

Wenn Sticky-Verbindungen aktiviert sind, legt das Dispatcher-Modul die `renderid` Cookie. Dieses Cookie verfügt nicht über die `secure` -Markierung, die hinzugefügt werden sollte, um die Sicherheit zu erhöhen. Sie fügen die `secure` Markierung festlegen `secure` -Eigenschaft in der `/stickyConnections` Knoten eines `dispatcher.any` Konfigurationsdatei. Der Wert der Eigenschaft (entweder `0` oder `1`) definiert, ob die Variable `renderid` -Cookie verfügt über die `secure` angehängt. Der Standardwert ist `0`, was bedeutet, dass das Attribut hinzugefügt wird **if** die eingehende Anfrage sicher ist. Wenn der Wert auf `1`festgelegt ist, wird das sichere Flag hinzugefügt, unabhängig davon, ob die eingehende Anfrage sicher ist oder nicht.

## Umgang mit Renderer-Verbindungsfehlern {#handling-render-connection-errors}

Konfigurieren Sie das Dispatcher-Verhalten für die Fälle, in denen der Renderer-Server einen 500-Fehler zurückgibt oder nicht verfügbar ist.

### Angeben einer Konsistenzprüfungsseite {#specifying-a-health-check-page}

Verwenden Sie die `/health_check`-Eigenschaft, um eine URL anzugeben, die beim Auftreten des Statuscode 500 geprüft wird. Wenn diese Seite auch einen Statuscode 500 zurückgibt, gilt die Instanz als nicht verfügbar und die konfigurierbare Zeitstrafe ( `/unavailablePenalty`) wird vor dem Wiederholen auf den Renderer angewendet.

```xml
/health_check
  {
  # Page gets contacted when an instance returns a 500
  /url "/health_check.html"
  }
```

### Festlegen der Wiederholungsverzögerung für die Seite {#specifying-the-page-retry-delay}

Die `/retryDelay` -Eigenschaft legt die Zeit (in Sekunden) fest, die der Dispatcher zwischen den Runden der Verbindungsversuche mit den Farm-Renderern wartet. Die maximale Anzahl der Verbindungsversuche pro Runde entspricht der Anzahl der Renderer in der Farm.

Der Dispatcher verwendet den Wert `"1"`, wenn `/retryDelay` nicht explizit definiert ist. Der Standardwert ist normalerweise angemessen.

```xml
/retryDelay "1"
```

### Konfigurieren der Wiederholungsanzahl  {#configuring-the-number-of-retries}

Mit der `/numberOfRetries`-Eigenschaft wird die maximale Anzahl der Runden an Verbindungsversuchen festgelegt, die der Dispatcher für die Renderer durchführt. Wenn der Dispatcher nach dieser Anzahl weiterer Versuche keine erfolgreiche Verbindung zu einem Renderer herstellen kann, gibt der Dispatcher eine fehlgeschlagene Antwort zurück.

Die maximale Anzahl der Verbindungsversuche pro Runde entspricht der Anzahl der Renderer in der Farm. Die maximale Anzahl der Verbindungsversuche berechnet sich daher wie folgt: (`/numberOfRetries`) x (Anzahl der Renderer).

Wenn der Wert nicht explizit definiert ist, lautet der Standardwert `5`.

```xml
/numberOfRetries "5"
```

### Verwenden des Failover-Mechanismus {#using-the-failover-mechanism}

Um Anforderungen an verschiedene Renderer erneut zu senden, wenn die ursprüngliche Anforderung fehlschlägt, aktivieren Sie den Failover-Mechanismus in Ihrer Dispatcher-Farm. Wenn das Failover aktiviert ist, verhält sich der Dispatcher wie folgt:

* Wenn eine Renderer-Anforderung den HTTP-Status 503 (UNVERFÜGBAR) zurückgibt, sendet der Dispatcher die Anforderung an einen anderen Renderer.
* Wenn bei einer Renderer-Anforderung der HTTP-Status 50x (außer 503) zurückgegeben wird, sendet der Dispatcher eine Anforderung für die Seite, die in der `health_check`-Eigenschaft konfiguriert ist.
   * Wenn die Konsistenzprüfung 500 (INTERNAL_SERVER_ERROR) zurückgibt, sendet der Dispatcher die ursprüngliche Anforderung an einen anderen Renderer.
   * Wenn die Konsistenzprüfung den HTTP-Status 200 zurückgibt, gibt der Dispatcher den ursprünglichen HTTP 500-Fehler an den Client zurück.

Um das Failover zu aktivieren, fügen Sie der Farm (oder Website) folgende Zeile hinzu:

```xml
/failover "1"
```

>[!NOTE]
>
>Um HTTP-Anfragen zu wiederholen, die einen Text enthalten, sendet der Dispatcher den Anfrageheader `Expect: 100-continue` an den Renderer, bevor die tatsächlichen Inhalte gespoolt werden. CQ 5.5 mit CQSE beantwortet dann sofort entweder 100 (CONTINUE) oder einen Fehlercode. Andere Servlet-Container werden ebenfalls unterstützt.

## Ignorieren von Netzwerkunterbrechungsfehlern – /ignoreEINTR  {#ignoring-interruption-errors-ignoreeintr}

>[!CAUTION]
>
>Diese Option ist nicht erforderlich. Verwenden Sie sie nur, wenn die folgenden Protokollmeldungen angezeigt werden:
>
>`Error while reading response: Interrupted system call`

Jeder dateisystemorientierte Systemaufruf kann unterbrochen werden `EINTR` wenn sich das Objekt des Systemaufrufs auf einem Remotesystem befindet, auf das über NFS zugegriffen wird. Ob bei diesen Systemaufrufen eine Zeitüberschreitung oder eine Unterbrechung möglich ist, hängt davon ab, wie das zugrunde liegende Dateisystem auf dem lokalen Computer bereitgestellt wurde.

Verwenden Sie die `/ignoreEINTR` -Parameter, wenn Ihre Instanz über eine solche Konfiguration verfügt und das Protokoll die folgende Meldung enthält:

`Error while reading response: Interrupted system call`

Intern liest der Dispatcher die Antwort vom Remote-Server (d. h. AEM) mithilfe einer Schleife, die wie folgt dargestellt werden kann:

```text
while (response not finished) {  
read more data  
}
```

Diese Meldungen können generiert werden, wenn `EINTR` im Abschnitt `read more data` auftritt. Sie werden durch den Empfang eines Signals vor dem Empfang von Daten ausgelöst.

Um solche Unterbrechungen zu ignorieren, können Sie den folgenden Parameter zu `dispatcher.any` (bevor `/farms`):

`/ignoreEINTR "1"`

Durch das Festlegen von `/ignoreEINTR` auf `"1"` liest der Dispatcher so lange weiter Daten, bis die vollständige Antwort erfasst wurde. Der Standardwert ist `0` und deaktiviert die Option.

## Entwerfen von Mustern für glob-Eigenschaften {#designing-patterns-for-glob-properties}

In einigen Abschnitten in der Dispatcher-Konfigurationsdatei werden `glob`-Eigenschaften als Auswahlkriterien für Clientanforderungen verwendet. Die Werte von `glob` -Eigenschaften sind Muster, die der Dispatcher mit einem Aspekt der Anforderung vergleicht, z. B. dem Pfad der angeforderten Ressource oder der IP-Adresse des Clients. Beispielsweise die Elemente in der `/filter` Abschnittsanwendung `glob` -Muster, um die Pfade der Seiten zu identifizieren, auf denen der Dispatcher agiert oder die der Dispatcher zurückweist.

Die `glob` -Werte können Platzhalterzeichen und alphanumerische Zeichen enthalten, um das Muster zu definieren.

| Platzhalterzeichen | Beschreibung | Beispiele |
|--- |--- |--- |
| `*` | Entspricht null oder mehreren aufeinanderfolgenden Instanzen eines Zeichens in der Zeichenfolge. Das endgültige Zeichen der Übereinstimmung wird durch eine der folgenden Situationen bestimmt: <br/>Ein Zeichen in der Zeichenfolge entspricht dem nächsten Zeichen im Muster und das Musterzeichen weist die folgenden Merkmale auf:<br/><ul><li>Kein *</li><li>Ist das nicht ?</li><li>Ein Buchstabenzeichen (einschließlich Leerzeichen) oder eine Zeichenklasse</li><li>Das Ende des Musters wird erreicht.</li></ul>Innerhalb einer Zeichenklasse wird das Zeichen wörtlich interpretiert. | `*/geo*` Entspricht allen Seiten unter den Knoten `/content/geometrixx` und `/content/geometrixx-outdoors`. Die folgenden HTTP-Anforderungen entsprechen dem glob-Muster: <br/><ul><li>`"GET /content/geometrixx/en.html"`</li><li>`"GET /content/geometrixx-outdoors/en.html"` </li></ul><br/> `*outdoors/*` <br/>Entspricht allen Seiten unter dem Knoten `/content/geometrixx-outdoors`. Die folgende HTTP-Anforderung entspricht beispielsweise dem glob-Muster: <br/><ul><li>`"GET /content/geometrixx-outdoors/en.html"`</li></ul> |
| `?` | Entspricht einem beliebigen einzelnen Zeichen Zu benutzen außerhalb von Zeichenklassen. Innerhalb einer Zeichenklasse wird dieses Zeichen literal (&quot;wörtlich&quot;) interpretiert. | `*outdoors/??/*`<br/> Entspricht den Seiten für eine beliebige Sprache der Site &quot;geometrixx-outdoors&quot;. Die folgende HTTP-Anforderung entspricht beispielsweise dem glob-Muster: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/>Die folgende Anforderung entspricht nicht dem glob-Muster: <br/><ul><li>&quot;GET /content/geometrixx-outdoors/en.html&quot;</li></ul> |
| `[ and ]` | Markiert den Anfang und das Ende einer Zeichenklasse. Zeichenklassen können einen oder mehrere Zeichenbereiche und einzelne Zeichen enthalten.<br/>Eine Übereinstimmung tritt auf, wenn das Zielzeichen einem Zeichen in der Zeichenklasse oder innerhalb eines bestimmten Bereichs entspricht.<br/>Wenn die schließende Klammer nicht vorhanden ist, liefert das Muster keine Treffer. | `*[o]men.html*`<br/> Entspricht der folgenden HTTP-Anforderung:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul><br/>Entspricht nicht der folgenden HTTP-Anforderung:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/> `*[o/]men.html*` <br/>Entspricht den folgenden HTTP-Anfragen: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul> |
| `-` | Steht für einen Zeichenbereich. Zur Verwendung in Zeichenklassen. Außerhalb einer Zeichenklasse wird dieses Zeichen wörtlich interpretiert. | `*[m-p]men.html*` Entspricht der folgenden HTTP-Anforderung: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul>Entspricht nicht der folgenden HTTP-Anforderung:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul> |
| `!` | Negiert die folgende Zeichen- oder Zeichenklasse. Verwenden Sie dies nur zur Negierung von Zeichen und Zeichenbereichen innerhalb von Zeichenklassen. Entspricht `^ wildcard`. <br/>Außerhalb einer Zeichenklasse wird dieses Zeichen wörtlich interpretiert. | `*[!o]men.html*`<br/> Entspricht der folgenden HTTP-Anforderung: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/>Entspricht nicht der folgenden HTTP-Anforderung: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul><br/>`*[!o!/]men.html*`<br/> Entspricht nicht der folgenden HTTP-Anforderung:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"` oder `"GET /content/geometrixx-outdoors/en/men. html"`</li></ul> |
| `^` | Negiert das folgende Zeichen oder den folgenden Zeichenbereich. Verwenden Sie , um nur Zeichen und Zeichenbereiche innerhalb von Zeichenklassen zu negieren. Entspricht dem Platzhalterzeichen `!`. <br/>Außerhalb einer Zeichenklasse wird dieses Zeichen wörtlich interpretiert. | Es gelten die Beispiele für das Platzhalterzeichen `!`, wobei die `!`-Zeichen in den Beispielmustern durch `^`-Zeichen ersetzt werden. |


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
* Die Protokollierungsebene.

Weitere Informationen finden Sie in der Dokumentation zum Webserver und in der Readme-Datei Ihrer Dispatcher-Instanz.

**Rotierende/wechselnde Protokolle in Apache**

Bei Verwendung von **Apache** Webserver können Sie die Standardfunktion für gedrehte Protokolle, Pipeline-Protokolle oder beides verwenden. Zum Beispiel mit Pipe-Protokollen:

`DispatcherLog "| /usr/apache/bin/rotatelogs logs/dispatcher.log%Y%m%d 604800"`

Diese Funktion dreht sich automatisch:

* die Dispatcher-Protokolldatei mit einem Zeitstempel in der Erweiterung (`logs/dispatcher.log%Y%m%d`).
* auf wöchentlicher Basis (60 x 60 x 24 x 7 = 604800 Sekunden).

Weitere Informationen finden Sie in der Dokumentation zum Apache-Webserver zum Rotieren und Wechseln von Protokollen . Beispiel: [Apache 2.4](https://httpd.apache.org/docs/2.4/logs.html).

>[!NOTE]
>
>Nach der Installation ist die standardmäßige Protokollebene hoch (d. h. Ebene 3 = Debugging), sodass der Dispatcher alle Fehler und Warnungen protokolliert. Diese Ebene ist in den Anfangsphasen nützlich.
>
>Eine solche Ebene erfordert jedoch zusätzliche Ressourcen. Wenn der Dispatcher reibungslos funktioniert *gemäß Ihren Anforderungen* können Sie die Protokollebene verringern.

### Trace-Protokollierung {#trace-logging}

Neben anderen Verbesserungen für den Dispatcher führt Version 4.2.0 auch die Ablaufprotokollierung ein.

Diese Möglichkeit ist höher als die Debug-Protokollierung, die zusätzliche Informationen in den Protokollen anzeigt. Es wird die Protokollierung für hinzugefügt:

* Die Werte der weitergeleiteten Header;
* Die Regel, die für eine bestimmte Aktion angewendet wurde

Sie können die Ablaufprotokollierung aktivieren, indem Sie die Protokollierungsebene in Ihrem Webserver auf `4` festlegen.

Nachfolgend finden Sie ein Beispiel für Protokolle mit aktivierter Ablaufverfolgung:

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

## Bestätigen der normalen Funktionsweise  {#confirming-basic-operation}

Um die normale Funktionsweise und Interaktion des Webservers, des Dispatchers und der AEM-Instanz zu prüfen, können Sie folgende Schritte ausführen:

1. Legen Sie `loglevel` auf `3` fest.

1. Starten Sie den Webserver. Dadurch wird auch der Dispatcher gestartet.
1. Starten Sie die AEM-Instanz.
1. Überprüfen Sie die Protokoll- und Fehlerdateien für Ihren Webserver und den Dispatcher.
   * Je nach Webserver sollten folgende Meldungen angezeigt werden:
      * `[Thu May 30 05:16:36 2002] [notice] Apache/2.0.50 (Unix) configured` und
      * `[Fri Jan 19 17:22:16 2001] [I] [19096] Dispatcher initialized (build XXXX)`

1. Surfen Sie auf der Website über den Webserver. Vergewissern Sie sich, dass der Inhalt nach Bedarf angezeigt wird.\
   Zum Beispiel bei einer lokalen Installation, bei der AEM auf Port `4502` zugreift und der Webserver auf `80`, nutzen Sie die Website-Konsole über:
   * `https://localhost:4502/libs/wcm/core/content/siteadmin.html`
   * `https://localhost:80/libs/wcm/core/content/siteadmin.html`
   * Die Ergebnisse sollten identisch sein. Bestätigen Sie den Zugriff auf andere Seiten mit demselben Mechanismus.

1. Überprüfen Sie, ob das Cache-Verzeichnis ausgefüllt wird.
1. Um sicherzustellen, dass der Cache korrekt geleert wird, aktivieren Sie eine Seite.
1. Wenn alles ordnungsgemäß funktioniert, können Sie die `loglevel` nach `0`.

## Verwenden mehrerer Dispatcher  {#using-multiple-dispatchers}

Bei komplexen Setups können Sie mehrere Dispatcher verwenden. Sie können beispielsweise Folgendes verwenden:

* einen Dispatcher zum Veröffentlichen einer Website im Intranet
* einen zweiten Dispatcher, der unter einer anderen Adresse und mit unterschiedlichen Sicherheitseinstellungen denselben Inhalt im Internet veröffentlicht.

Stellen Sie in diesem Fall sicher, dass jede Anfrage nur einen Dispatcher durchläuft. Ein Dispatcher verarbeitet keine Anforderungen, die von einem anderen Dispatcher stammen. Stellen Sie daher sicher, dass beide Dispatcher direkt auf die AEM Website zugreifen.

## Debugging {#debugging}

Beim Hinzufügen der Kopfzeile `X-Dispatcher-Info` einer Anfrage antwortet der Dispatcher, ob das Ziel zwischengespeichert, aus dem Cache zurückgegeben oder überhaupt nicht zwischenspeicherbar war. Der Antwortheader `X-Cache-Info` enthält diese Informationen in lesbarer Form. Mithilfe dieser Antwortheader können Sie Probleme mit vom Dispatcher zwischengespeicherten Antworten beheben.

Diese Funktion ist standardmäßig nicht aktiviert, daher für die Antwortheader `X-Cache-Info` um einbezogen werden zu können, muss die Farm den folgenden Eintrag enthalten:

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

Außerdem wird die `X-Dispatcher-Info` -Kopfzeile benötigt keinen Wert, aber wenn Sie `curl` zum Testen müssen Sie einen Wert angeben, der an die Kopfzeile gesendet werden soll, z. B.:

```xml
curl -v -H "X-Dispatcher-Info: true" https://localhost/content/wknd/us/en.html
```

Nachfolgend finden Sie eine Liste mit den Antwortheadern, die `X-Dispatcher-Info` gibt zurück:

* **zwischengespeichert**\
  Die Zieldatei ist im Cache enthalten und der Dispatcher hat festgestellt, dass sie für die Bereitstellung gültig ist.
* **Zwischenspeicherung läuft**\
  Die Zieldatei ist nicht im Cache enthalten und der Dispatcher hat festgestellt, dass sie für die Zwischenspeicherung und Bereitstellung der Ausgabe gültig ist.
* **Zwischenspeicherung läuft: stat-Datei ist aktueller**
Die Zieldatei ist im Cache enthalten, wird aber durch eine neuere stat-Datei invalidiert. Der Dispatcher löscht die Zieldatei, erstellt sie aus der Ausgabe neu und stellt sie bereit.
* **Nicht zwischenspeicherbar: kein Basisverzeichnis**
Die Konfiguration der Farm enthält kein Basisverzeichnis (Konfigurationselement `cache.docroot`).
* **Nicht zwischenspeicherbar: Cache-Dateipfad zu lang**\
  Die Zieldatei – die Verknüpfung von Basisverzeichnis und URL-Datei – überschreitet den längsten möglichen Dateinamen im System.
* **Nicht zwischenspeicherbar: temporärer Dateipfad zu lang**\
  Der temporäre Dateiname überschreitet den längsten möglichen Dateinamen im System. Der Dispatcher erstellt zuerst eine temporäre Datei, bevor er die zwischengespeicherte Datei tatsächlich erstellt oder überschreibt. Der temporäre Dateiname ist der Zieldateiname mit den Zeichen `_YYYYXXXXXX` angehängt wurde, wobei die `Y` und `X` ersetzt werden, um einen eindeutigen Namen zu erstellen.
* **Nicht zwischenspeicherbar: Anfrage-URL hat keine Erweiterung**\
  Die Anfrage-URL hat keine Erweiterung oder es liegt ein Pfad nach der Dateiendung vor, z. B.: `/test.html/a/path`.
* **Nicht zwischenspeicherbar: Anfrage war kein GET oder HEAD**
Die HTTP-Methode ist kein GET oder HEAD. Der Dispatcher geht davon aus, dass die Ausgabe dynamische Daten enthält, die nicht zwischengespeichert werden sollen.
* **Nicht zwischenspeicherbar: Anfrage enthielt eine Abfragezeichenfolge**\
  Die Anfrage enthielt eine Abfragezeichenfolge. Der Dispatcher geht davon aus, dass die Ausgabe von der angegebenen Abfragezeichenfolge abhängt und daher nicht zwischengespeichert wird.
* **Nicht zwischenspeicherbar: keine Authentifizierung durch Sitzungsmanager**\
  Der Farm-Cache wird von einem Sitzungsmanager verwaltet (die Konfiguration enthält einen `sessionmanagement`-Knoten) und die Anforderung enthielt nicht die entsprechenden Authentifizierungsinformationen.
* **Nicht zwischenspeicherbar: Anfrage enthält Autorisierung**\
  Die Farm darf keine Cache-Ausgaben zwischenspeichern (`allowAuthorized 0`). Die Anfrage enthält Authentifizierungsinformationen.
* **Nicht zwischenspeicherbar: Ziel ist ein Verzeichnis**\
  Die Zieldatei ist ein Verzeichnis. Dieser Speicherort kann auf einen konzeptionellen Fehler verweisen, bei dem eine URL und einige Unter-URL beide eine zwischenspeicherbare Ausgabe enthalten. Wenn beispielsweise eine Anforderung an `/test.html/a/file.ext` kommt zuerst und enthält zwischenspeicherbare Ausgabe, kann der Dispatcher die Ausgabe einer nachfolgenden Anfrage an `/test.html`.
* **Nicht zwischenspeicherbar: nachlaufender Schrägstrich in Anfrage-URL**\
  Die Anfrage-URL ist mit einem Schrägstrich versehen.
* **Nicht zwischenspeicherbar: URL-Abfrage ist nicht in den Cache-Regeln enthalten**\
  Die Cache-Regeln der Farm lehnen explizit das Zwischenspeichern der Ausgabe einer URL-Abfrage ab.
* **Nicht zwischenspeicherbar: Berechtigungsprüfung verweigert Zugriff**\
  Die Berechtigungsprüfung der Farm hat den Zugriff auf die zwischengespeicherte Datei verweigert.
* **Nicht zwischenspeicherbar: Die Sitzung ist nicht gültig.**
Der Farmcache wird von einem Sitzungsmanager verwaltet (die Konfiguration enthält einen `sessionmanagement`-Knoten) und die Sitzung des Benutzers ist nicht oder nicht mehr gültig.
* **Nicht zwischenspeicherbar: Antwort enthält`no_cache`**
Der Remote-Server gab einen `Dispatcher: no_cache` -Kopfzeile, die dem Dispatcher das Zwischenspeichern der Ausgabe untersagt.
* **Nicht zwischenspeicherbar: Länge des Antwortinhalts ist null**
Die Inhaltslänge der Antwort ist null. Der Dispatcher erstellt keine Datei mit einer Länge von null.
