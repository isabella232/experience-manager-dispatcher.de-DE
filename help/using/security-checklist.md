---
title: Die Dispatcher-Sicherheitscheckliste
seo-title: Die Dispatcher-Sicherheitscheckliste
description: Eine Sicherheitscheckliste, die vor der Produktion geprüft werden sollte.
seo-description: Eine Sicherheitscheckliste, die vor der Produktion geprüft werden sollte.
uuid: 7bfa3202-03f6-48e9-8d2e-2a40e137ecbe
contentOwner: Benutzer
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: Dispatcher
content-type: Referenz
discoiquuid: fbfafa55-c029-4ed7-ab3e-1bebfde18248
jcr-lastmodifiedby: remove-legacypath-6-1
index: y
internal: n
snippet: y
translation-type: tm+mt
source-git-commit: 6d3ff696780ce55c077a1d14d01efeaebcb8db28

---


# Die Dispatcher-Sicherheitscheckliste{#the-dispatcher-security-checklist}

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-05T05:14:35.365-0400

<p>Food for thought listed on <a href="https://jira.corp.adobe.com/browse/DOC-5649">DOC-5649</a>. To be considered while proof-reading.</p> 
<p> </p>

 -->

Der Dispatcher bietet als Front-End-System zusätzliche Sicherheit für Ihre Adobe Experience Manager-Infrastruktur. Adobe empfiehlt, vor dem Einsatz in einer Produktionsumgebung unbedingt die folgende Checkliste abzuarbeiten.

>[!CAUTION]
>
>Sie müssen vor dem Produktivstart auch die Sicherheitscheckliste für Ihre AEM-Version abarbeiten. Weitere Informationen finden Sie in der entsprechenden [Adobe Experience Manager-Dokumentation](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html).

## Verwenden der neuesten Version des Dispatchers {#use-the-latest-version-of-dispatcher}

Sie sollten die neueste Version installieren, die für Ihre Plattform verfügbar ist. Sie sollten Ihre Dispatcher-Instanz aktualisieren, um die neueste Version zu verwenden und von Produkt- und Sicherheitsverbesserungen zu profitieren. Weitere Informationen finden Sie unter [Installieren des Dispatchers](dispatcher-install.md).

>[!NOTE]
>
>Die Version Ihrer Dispatcher-Installation können Sie der Dispatcher-Protokolldatei entnehmen.
>
>`[Thu Apr 30 17:30:49 2015] [I] [23171(140735307338496)] Dispatcher initialized (build 4.1.9)`
>
>Um die Protokolldatei zu finden, überprüfen Sie die Dispatcher-Konfiguration in der Datei `httpd.conf`.

## Einschränken von Clients, die den Cache leeren können {#restrict-clients-that-can-flush-your-cache}

Adobe empfiehlt, [die Clients einzuschränken, die den Cache leeren können](dispatcher-configuration.md#limiting-the-clients-that-can-flush-the-cache).

## Aktivieren von HTTPS für Transport Layer Security {#enable-https-for-transport-layer-security}

Adobe empfiehlt, die HTTPS-Transportschicht auf Autoren- und Veröffentlichungsinstanzen zu aktivieren. 

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:41:28.841-0400

<p>Recommended to have SSL termination, front end SSL.</p> 
<p>Question is do we want to have SSL communication between dispatcher and AEM instances (publish and/or author).</p> 
<p>We might want to have two items:</p> 
<ul> 
 <li>MUST HTTPS clients -&gt; dispatcher / load balancer</li> 
 <li>NICE load balancer -&gt; dispatcher<br /> </li> 
 <li>NICE dispatcher -&gt; instances if sensitive information such as credit cards / or infrastructure requirements such as DMZ</li> 
</ul>

 -->

## Restrict Access {#restrict-access}

Wenn Sie den Dispatcher konfigurieren, sollten Sie externe Zugriffe so weit wie möglich einschränken. Siehe [Abschnitt „Beispiel/Filter“](dispatcher-configuration.md#main-pars_184_1_title) in der Dispatcher-Dokumentation.

## Verweigern des Zugriffs auf administrative URLs  {#make-sure-access-to-administrative-urls-is-denied}

Stellen Sie sicher, dass Sie Filter verwenden, um den externen Zugriff auf sämtliche administrative URLs wie die Web-Konsole zu blockieren.

Unter [Testen der Dispatcher-Sicherheit](dispatcher-configuration.md#testing-dispatcher-security) finden Sie eine Liste der URLs, die blockiert werden müssen.

## Verwenden von Whitelists anstelle von Blacklists {#use-whitelists-instead-of-blacklists}

Whitelists eignen sich besser zur Zugriffskontrolle, da sie grundsätzlich voraussetzen, dass alle Zugriffsanforderungen verweigert werden sollten, sofern sie nicht ausdrücklich Teil der Whitelist sind. Dieses Modell ermöglicht eine strengere Kontrolle über neue Anforderungen, die während einer Konfigurationsphase möglicherweise noch nicht überprüft oder in Betracht gezogen wurden.

## Ausführen des Dispatchers mit einem dedizierten Systembenutzer {#run-dispatcher-with-a-dedicated-system-user}

Beim Konfigurieren des Dispatchers sollten Sie sicherstellen, dass der Webserver von einem dedizierten Benutzer mit den geringsten Rechten ausgeführt wird. Es empfiehlt sich, nur Schreibrechte für den Dispatcher-Cacheordner zu erteilen.

Darüber hinaus müssen IIS-Benutzer ihre Website folgendermaßen konfigurieren:

1. Wählen Sie in den Einstellungen für den physischen Pfad für Ihre Website die Option zum Verbinden als bestimmter Benutzer ****.
1. Legen Sie den Benutzer fest.

## Vermeiden von Denial-of-Service-Angriffen  {#prevent-denial-of-service-dos-attacks}

Ein Denial-of-Service-Angriff (DoS) zielt darauf ab, eine Computerressource für die vorgesehenen Benutzer unzugänglich zu machen.

Auf Dispatcher-Ebene gibt es zwei Konfigurationsmöglichkeiten, um DoS-Angriffe zu verhindern:  [](https://docs.adobe.com/content/docs/en/dispatcher.html#/filter (Filter))

* Verwenden Sie das mod_rewrite-Modul (beispielsweise [Apache 2.4](https://httpd.apache.org/docs/2.4/mod/mod_rewrite.html)), um URL-Überprüfungen durchzuführen (sofern die Regeln für das URL-Muster nicht zu komplex sind).

* Hindern Sie den Dispatcher daran, URLs mit falschen Erweiterungen zwischenzuspeichern, indem Sie [Filter](dispatcher-configuration.md#configuring-access-to-conten-tfilter) verwenden.\
   Ändern Sie beispielsweise die Cachingregeln, um die Zwischenspeicherung auf die erwarteten MIME-Typen zu begrenzen. Dazu zählen unter anderem:

   * `.html`
   * `.jpg`
   * `.gif`
   * `.swf`
   * `.js`
   * `.doc`
   * `.pdf`
   * `.ppt`
   Sie können eine Beispielkonfigurationsdatei zum [Einschränken des externen Zugriffs](#restrict-access) einsehen, diese beinhaltet die Einschränkungen für MIME-Typen. 

Um sicher den vollen Funktionsumfang für die Veröffentlichungsinstanzen zu aktivieren, konfigurieren Sie Filter, um den Zugriff auf die folgenden Knoten zu verhindern:

* `/etc/`
* `/libs/`

Konfigurieren Sie anschließend Filter, um den Zugriff auf die folgenden Knotenpfade zuzulassen:

* `/etc/designs/*`
* `/etc/clientlibs/*`
* `/etc/segmentation.segment.js`
* `/libs/cq/personalization/components/clickstreamcloud/content/config.json`
* `/libs/wcm/stats/tracker.js`
* `/libs/cq/personalization/*` (JS, CSS und JSON)
* `/libs/cq/security/userinfo.json` (CQ-Benutzerinformationen)
* `/libs/granite/security/currentuser.json` (**Daten dürfen nicht zwischengespeichert werden**)

* `/libs/cq/i18n/*` (Internalisierung)

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:38:17.016-0400

<p>We need to highlight whether a path applies to all versions or specific ones.<br /> </p>

 -->

## Konfigurieren des Dispatchers zum Verhindern von CSRF-Angriffen {#configure-dispatcher-to-prevent-csrf-attacks}

AEM bietet ein [Framework](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html#verification-steps), mit dem CSRF-Angriffe (Cross Site Request Forgery) verhindert werden können. Um dieses Framework ordnungsgemäß zu verwenden, müssen Sie in den Dispatcher die CSRF-Token-Unterstützung in die Whitelist aufnehmen. Sie können dies durch folgende Maßnahmen erreichen:

1. Erstellen Sie einen Filter, um den Pfad `/libs/granite/csrf/token.json` zuzulassen.
1. Fügen Sie die Kopfzeile `CSRF-Token` dem Abschnitt `clientheaders` der Dispatcher-Konfiguration hinzu.

## Verhindern von Clickjacking {#prevent-clickjacking}

Um Clickjacking zu verhindern, sollten Sie Ihren Webserver so konfigurieren, dass er die HTTP-Kopfzeile `X-FRAME-OPTIONS` bereitstellt, die auf `SAMEORIGIN` festgelegt ist.

Weitere [Informationen zum Thema Clickjacking finden Sie auf der OWASP-Website](https://www.owasp.org/index.php/Clickjacking).

## Durchführen eines Penetrationstests {#perform-a-penetration-test}

Adobe empfiehlt dringend, Ihre AEM-Infrastruktur vor dem Einsatz in einer Produktionsumgebung einem Penetrationstest zu unterziehen. 

