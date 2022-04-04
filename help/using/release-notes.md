---
title: Versionshinweise zu AEM Dispatcher
seo-title: AEM Dispatcher Release Notes
description: Spezifische Versionshinweise für Adobe Experience Manager Dispatcher
seo-description: Release notes specific to Adobe Experience Manager Dispatcher
uuid: ae3ccf62-0514-4c03-a3b9-71799a482cbd
topic-tags: release-notes
content-type: reference
products: SG_EXPERIENCEMANAGER/6.4
discoiquuid: ff3d38e0-71c9-4b41-85f9-fa896393aac5
exl-id: b55c7a34-d57b-4d45-bd83-29890f1524de
source-git-commit: 4661a71aa4e5fc99a0e4de26d5740a1dc0258bb0
workflow-type: tm+mt
source-wordcount: '977'
ht-degree: 54%

---

# Versionshinweise zu AEM Dispatcher{#aem-dispatcher-release-notes}

## Versionshinweise {#release-information}

|--- |--- |
| Produkte | Adobe Experience Manager (AEM) Dispatcher |
| Version | 4,3,5 |
| Typ | Nebenversion |
| Datum | 04. April 2022 |
| Download-URL | <ul><li>[Apache 2.4](release-notes.md#apache)</li><li>[Microsoft Internet Information Services (IIS)](release-notes.md#iis)</li></ul> |
| Kompatibilität | AEM 6.1 oder höher |

## Systemanforderungen und Voraussetzungen {#system-requirements-and-prerequisites}

Siehe [Unterstützte Plattformen](https://helpx.adobe.com/de/experience-manager/6-4/sites/deploying/using/technical-requirements.html)für weitere Informationen zu Anforderungen und Voraussetzungen.

Adobe empfiehlt dringend, die neueste Version AEM Dispatcher zu verwenden, um von den neuesten Funktionen, Fehlerbehebungen und der bestmöglichen Leistung zu profitieren.

## Installationsanweisungen {#installation-instructions}

Detaillierte Anweisungen finden Sie unter [Installieren des Dispatchers](dispatcher-install.md).

## Versionsverlauf {#release-history}

### Version 4.3.5 (2022-April-29) {#apr}

**Verbesserungen**:

* DISP-954 - Invalidierung unterstützen, auch wenn das Ablaufdatum nicht überschritten wurde
* DISP-949 - Dispatcher gibt 200 anstelle von 404 zurück, selbst wenn die POST-Anforderung durch den Filter blockiert wird

### Version 4.3.4 (2021-29. November) {#nov}

**Fehlerbehebungen**:

* DISP-833 - X-Forwarded-Host-Header können eine Liste mit kommagetrennten Hostnamen enthalten
* DISP-835 - DispatcherUseForwardedHost verschlingt Host-Header, wenn er zuletzt kommt

**Verbesserungen**:

* DISP-874 - Erstellt eine Dispatcher-Konfiguration, um die Implementierung von DISP-818 über eine Markierung entweder ein- oder aus zu schalten `DispatcherRestrictUncacheableContent`. Der Standardwert ist „Aus“. Wenn &quot;Ein&quot;aktiviert ist, werden alle durch &quot;mod&quot;festgelegten Zwischenspeicherkopfzeilen für nicht speicherbare Inhalte entfernt. Dies unterscheidet sich vom Verhalten in Version 4.3.3 (wo die Standardeinstellung &quot;Ein&quot;war), aber vom Verhalten in früheren Versionen als 4.3.3 (wo die Standardeinstellung &quot;Aus&quot;war). Keeping `DispatcherRestrictUncacheableContent`Der Standardwert Aus ist der empfohlene Ansatz, sodass der Browser-Cache flexibler ist. Wenn Sie beim Upgrade von Version 4.3.3 auf 4.3.4 dasselbe Verhalten wie in Version 4.3.3 beibehalten möchten, müssen Sie explizit festlegen `DispatcherRestrictUncacheableContent` auf &quot;An&quot;.
* DISP-841 - Dispatcher respektiert /serverStaleOnError für 504-Antwortcode nicht
* DISP-874 - Erstellen einer Dispatcher-Konfiguration, um die Implementierung von DISP-818 zu aktivieren oder zu deaktivieren
* DISP-883 - Verfolgen der URL-Anforderungsdekomposition im Dispatcher
* DISP-944 - Vanity-URLs vor dem Laden

### Version 4.3.3 (18. Oktober 2019) {#october}

**Fehlerbehebungen**:

* DISP-739 - LogLevel Dispatcher: **level** funktioniert nicht
* DISP-749 - Alpine Linux Dispatcher stürzt mit Trace Log Level ab

**Verbesserungen**:

* DISP-813 - Unterstützung im Dispatcher für openssl 1.1.x
* DISP-814 - Apache 40x-Fehler während Cache-Leeren
* DISP-818 - mod_expires fügt Cache-Control-Header für nicht speicherbare Inhalte hinzu
* DISP-821 - Protokollkontext nicht im Socket speichern
* DISP-822 - Dispatcher sollte &quot;ppoll&quot;anstelle von &quot;pselect&quot;verwenden
* DISP-824 - Secure DispatcherUseForwardedHost
* DISP-825 - Meldung protokollieren, wenn kein Speicherplatz mehr auf der Festplatte vorhanden ist
* DISP-826 - Unterstützung von Refzierungs-URIs mit einer Abfragezeichenfolge

**Neue Funktionen**:

* DISP-703 - Farm-spezifisches Cache-Trefferverhältnis
* DISP-827 - Lokaler Server zum Testen
* DISP-828 - Erstellen eines Testdocker-Bildes für Dispatcher

### Version 4.3.2 (31. Januar 2019) {#jan}

**Fehlerbehebungen**:

* DISP-734 – Dispatcher verursacht Absturz in insert_output_filter wenn nicht als Handler definiert
* DISP-735 – REs funktionieren nicht auf Linux
* DISP-740 – Laden des Dispatchers in macOS Mojave standardmäßig deaktiviert
* DISP-742 – Blockierte Anfragen übergeben ggf. Informationen an authentifizierungsgeschützte Ressourcen

**Verbesserungen**:

* DISP-746 – Nicht gekennzeichnete Zeichenfolgen in Dispatcher.any sollten Warnung generieren

**Neue Funktion**:

* DISP-747 – Bereitstellen von Anfrageinformationen in Apache-Umgebung

### Version 4.3.1 (16. Oktober 2018) {#oct}

**Fehlerbehebungen**:

* DISP-656 – Dispatcher gibt falsche ETag-Kopfzeile aus
* DISP-694 – Warnungen im Falle unzureichender Keep-Alive-Verbindungen unterdrücken
* DISP-714 – Cookiebasiertes Sitzungsmanagement funktioniert nicht in IIS
* DISP-715 – Sichere Markierung für Renderid-Cookie
* DISP-720 – Exhaustion durch nicht geschlossene temporäre Dateien (zu viele geöffnete Dateien)
* DISP-721 – Dispatcher unterbricht poll() bei vollständigem Neustart des untergeordneten Elements durch Apache
* DISP-722 – Erstellung von Cache-Dateien mit Oktalmodus 0600
* DISP-723 – Implizites 10-minütiges Timeout (mit Wiederholung) bei Render-Timeouts auf 0
* DISP-725 – Umwandlung von nachgestellten Zeichen in unbenannte Werte ohne Hinweis
* DISP-726 – Protokollierung einer Warnung, wenn keine Farm dem eingehenden Host entspricht
* DISP-727 – Überprüfung der Anfrageinhaltslänge leerer Cachedateien durch Dispatcher
* DISP-730 – 404-Fehler beim Versuch, auf Header-Datei über Dispatcher zuzugreifen
* DISP-731 – Dispatcher anfällig für Log Injection
* DISP-732 – Dispatcher soll aufeinander folgende /-Zeichen in der URL entfernen
* DISP-733 – Dispatcher soll Age-Header festlegen (berechnen)

**Verbesserungen**:

* DISP-656 – Dispatcher gibt falsche ETag-Kopfzeile aus
* DISP-694 – Warnungen im Falle unzureichender Keep-Alive-Verbindungen unterdrücken
* DISP-715 – Sichere Markierung für Renderid-Cookie
* DISP-722 – Erstellung von Cache-Dateien mit Oktalmodus 0600
* DISP-726 – Protokollierung einer Warnung, wenn keine Farm dem eingehenden Host entspricht

### Version 4.3.0 (13. Juni 2018) {#jun}

**Fehlerbehebungen**:

* DISP-682 – Numerische Protokollierungsebene falsch angewendet
* DISP-685 – 32-Bit Solaris SPARC-Binärdateien weisen nicht definierten Verweis auf __divdi3 auf
* DISP-688 – Dispatcher gibt auf 404-Antwort nicht den Header „X-Cache-Info“ zurück
* DISP-690 – Zuletzt geänderte Kopfzeile nicht zwischenspeicherbar
* DISP-691 – Zugriffsverletzungen in w3wp.exe
* DISP-693 – Aktualisierung der Architekturdetails für Solaris-Server auf der Dispatcher-Downloadseite notwendig
* DISP-695 – Problem mit DispatcherLog-Ebene im Dispatcher-Modul 4.2.3
* DISP-698 – Dispatcher-TTL muss s-maxage und private Anweisungen unterstützen
* DISP-700 – Modul funktioniert nicht ordnungsgemäß auf Alpine Linux
* DISP-704 – Browseranforderungen mit %2b werden nicht codiert an den Herausgeber gesendet
* DISP-705 – Dispatcher-Absturz aufgrund doppeltem Aufruf der Funktion free oder Beschädigung (fasttop)
* DISP-706 – Potenzielle Dauerschleife, da Dispatcher im Verlauf der Invalidierung Referenz-Symlinks folgt
* DISP-709 – Blockieren einiger Vanity-URL-Erweiterungen
* DISP-710 – Builds für Linux nicht verwendbar auf Cent OS 6

**Verbesserungen**:

* DISP-652 – Dispatcher gibt falschen Datums-Header aus

## Hilfreiche Ressourcen {#helpful-resources}

* [Übersicht über AEM Dispatcher](dispatcher.md)

## Downloads {#downloads}

### Apache 2.4 {#apache}

| Plattform | Architektur | OpenSSL-Unterstützung | Download |
|---|---|---|---|
| Linux | i686 (32 Bit) | Keine | [dispatcher-apache2.4-linux-i686-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-4.3.5.tar.gz) |
| Linux | i686 (32 Bit) | 1.0 | [dispatcher-apache2.4-linux-i686-ssl1.0-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.0-4.3.5.tar.gz) |
| Linux | i686 (32 Bit) | 1,1 | [dispatcher-apache2.4-linux-i686-ssl1.1-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.1-4.3.5.tar.gz) |
| Linux | x86_64 (64 Bit) | Keine | [dispatcher-apache2.4-linux-x86_64-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-4.3.5.tar.gz) |
| Linux | x86_64 (64 Bit) | 1,0 | [dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.5.tar.gz) |
| Linux | x86_64 (64 Bit) | 1,1 | [dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.5.tar.gz) |
| macOS | x86_64 (64 Bit) | Keine | [dispatcher-apache2.4-darwin-x86_64-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-x86_64-4.3.5.tar.gz) |

### IIS {#iis}

| Plattform | Architektur | OpenSSL-Unterstützung | Herunterladen |
|---|---|---|---|
| Windows | x86 (32 Bit) | Keine | [dispatcher-iis-windows-x86-4.3.5.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-4.3.5.zip) |
| Windows | x86 (32 Bit) | 1,0 | [dispatcher-iis-windows-x86-ssl1.0-4.3.5.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.0-4.3.5.zip) |
| Windows | x86 (32 Bit) | 1,1 | [dispatcher-iis-windows-x86-ssl1.1-4.3.5.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.1-4.3.5.zip) |
| Windows | x64 (64 Bit) | Keine | [dispatcher-iis-windows-x64-4.3.5.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-4.3.5.zip) |
| Windows | x64 (64 Bit) | 1,0 | [dispatcher-iis-windows-x64-ssl1.0-4.3.5.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.0-4.3.5.zip) |
| Windows | x64 (64 Bit) | 1,1 | [dispatcher-iis-windows-x64-ssl1.1-4.3.5.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.1-4.3.5.zip) |
