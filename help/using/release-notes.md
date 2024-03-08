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
source-git-commit: f2036e8f73d5f9f58ff713df8f04caad89d660fc
workflow-type: ht
source-wordcount: '1040'
ht-degree: 100%

---

# Versionshinweise zu AEM Dispatcher{#aem-dispatcher-release-notes}

## Versionshinweise {#release-information}

|  |  |
|--- |--- |
| Produkte | Adobe Experience Manager (AEM) Dispatcher |
| Version | 4.3.6 |
| Typ | Nebenversion |
| Datum | 25. Juli 2023 |
| Download-URL | <ul><li>[Apache 2.4](release-notes.md#apache)</li><li>[Microsoft Internet Information Services (IIS)](release-notes.md#iis)</li></ul> |
| Kompatibilität | AEM 6.1 oder höher |

## Systemanforderungen und Voraussetzungen {#system-requirements-and-prerequisites}

Weitere Informationen zu Anforderungen und Voraussetzungen finden Sie auf der Seite [Unterstützte Plattformen](https://helpx.adobe.com/de/experience-manager/6-4/sites/deploying/using/technical-requirements.html).

Adobe rät ausdrücklich dazu, die neueste Version von AEM Dispatcher zu verwenden, um von den neuesten Funktionen, behobenen Fehlern und der bestmöglichen Leistung zu profitieren.

## Installationsanweisungen {#installation-instructions}

Detaillierte Anweisungen finden Sie unter [Installieren des Dispatchers](dispatcher-install.md).

## Versionsverlauf {#release-history}

### Version 4.3.6 (25. Juli 2023) {#jyly}

**Verbesserungen**:

* DISP-911 AEM-05: X-Edge-Key kann in disp_apache2.c weitergeleitet werden.
* DISP-937: Es werden alle Selektoren protokolliert.
* DISP-998: Das Laden von Vanity-URLs kann beim Start konfiguriert werden.

### Version 4.3.5 (4. April 2022) {#apr}

**Verbesserungen**:

* DISP-954: Die Invalidierung wird unterstützt, auch wenn das Ablaufdatum nicht überschritten wurde.
* DISP-949: Der Dispatcher gibt den Code 200 statt 404 zurück, selbst wenn die POST-Anfrage durch einen Filter blockiert wird.

### Version 4.3.4 (29. November 2021) {#nov}

**Fehlerbehebungen**:

* DISP-833 – X-Forwarded-Host-Header können eine Liste mit kommagetrennten Host-Namen enthalten.
* DISP-835 – DispatcherUseForwardedHost „schluckt“ den Host-Header, sofern dieser als Letztes kommt.

**Verbesserungen**:

* DISP-874 – Erstellt eine Dispatcher-Konfiguration, um die Implementierung von DISP-818 über das Flag `DispatcherRestrictUncacheableContent` ein- oder auszuschalten. Der Standardwert ist „Off“. Wenn „On“ eingestellt ist, werden alle durch „mod_expires“ festgelegten Caching-Header für nicht zwischenspeicherbare Inhalte entfernt. Dies unterscheidet sich vom Verhalten in Version 4.3.3 (mit der Standardeinstellung „On“), entspricht aber dem Verhalten in früheren Versionen vor 4.3.3 (mit der Standardeinstellung „Off“). Es wird empfohlen, die Standardeinstellung „Off“ für `DispatcherRestrictUncacheableContent` zu belassen, damit der Browsercache flexibler ist. Wenn Sie beim Upgrade von Version 4.3.3 auf 4.3.4 das gleiche Verhalten wie in Version 4.3.3 beibehalten möchten, müssen Sie `DispatcherRestrictUncacheableContent` explizit auf „On“ festlegen.
* DISP-841 – Dispatcher respektiert „/serverStaleOnError“ für den 504-Antwort-Code nicht.
* DISP-874 – Erstellen einer Dispatcher-Konfiguration, um die Implementierung von DISP-818 ein- oder auszuschalten.
* DISP-883 – Verfolgen der Dekomposition von URL-Anfragen im Dispatcher.
* DISP-944 – Vorabladen von Vanity-URLs.

### Version 4.3.3 (16. Oktober 2019) {#october}

**Fehlerbehebungen**:

* DISP-739 – LogLevel-Dispatcher: **level** funktioniert nicht.
* DISP-749 – Alpine Linux-Dispatcher stürzt mit TRACE-Protokollebene ab.

**Verbesserungen**:

* DISP-813 – Unterstützung im Dispatcher für OpenSSL 1.1.x.
* DISP-814 – Apache 40x-Fehler beim Cache-Leeren.
* DISP-818 – „mod_expires“ fügt Cache-Control-Header für nicht zwischenspeicherbare Inhalte hinzu.
* DISP-821 – Protokollkontext wird nicht im Socket gespeichert.
* DISP-822 – Dispatcher sollte „ppoll“ statt „pselect“ verwenden.
* DISP-824 – Sicherheit bei DispatcherUseForwardedHost.
* DISP-825 – Protokollieren einer speziellen Meldung, wenn kein Speicherplatz mehr auf der Festplatte vorhanden ist.
* DISP-826 – Erneutes Abrufen von URIs per Abfragezeichenfolge wird unterstützt.

**Neue Funktionen**:

* DISP-703 – Farm-spezifische Cache-Trefferquote.
* DISP-827 – Lokaler Server zum Testen.
* DISP-828 – Erstellen eines Test-Docker-Bilds für den Dispatcher.

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

* [Überblick über AEM Dispatcher](dispatcher.md)

## Downloads {#downloads}

### Apache 2.4 {#apache}

| Plattform | Architektur | OpenSSL-Unterstützung | Herunterladen |
|---|---|---|---|
| Linux | i686 (32 Bit) | Ohne | [dispatcher-apache2.4-linux-i686-4.3.6.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-4.3.6.tar.gz) |
| Linux | i686 (32 Bit) | 1.0 | [dispatcher-apache2.4-linux-i686-ssl1.0-4.3.6.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.0-4.3.6.tar.gz) |
| Linux | i686 (32 Bit) | 1.1 | [dispatcher-apache2.4-linux-i686-ssl1.1-4.3.6.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.1-4.3.6.tar.gz) |
| Linux | x86_64 (64 Bit) | Ohne | [dispatcher-apache2.4-linux-x86_64-4.3.6.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-4.3.6.tar.gz) |
| Linux | x86_64 (64 Bit) | 1.0 | [dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.6.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.6.tar.gz) |
| Linux | x86_64 (64 Bit) | 1.1 | [dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.6.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.6.tar.gz) |
| Linux | aarch64 (64-Bit) | Ohne | [dispatcher-apache2.4-linux-aarch64-4.3.6.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-aarch64-4.3.6.tar.gz) |
| Linux | aarch64 (64-Bit) | 1.0 | [dispatcher-apache2.4-linux-aarch64-ssl1.0-4.3.6.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-aarch64-ssl1.0-4.3.6.tar.gz) |
| Linux | aarch64 (64-Bit) | 1.1 | [dispatcher-apache2.4-linux-aarch64-ssl1.1-4.3.6.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-aarch64-ssl1.1-4.3.6.tar.gz) |
| macOS | arm64 (64-Bit) | Ohne | [dispatcher-apache2.4-darwin-arm64-4.3.6.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-arm64-4.3.6.tar.gz) |
| macOS | x86_64 (64 Bit) | Ohne | [dispatcher-apache2.4-darwin-x86_64-4.3.6.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-x86_64-4.3.6.tar.gz) |

### IIS {#iis}

| Plattform | Architektur | OpenSSL-Unterstützung | Herunterladen |
|---|---|---|---|
| Windows | x86 (32 Bit) | Ohne | [dispatcher-iis-windows-x86-4.3.6.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-4.3.6.zip) |
| Windows | x86 (32 Bit) | 1.0 | [dispatcher-iis-windows-x86-ssl1.0-4.3.6.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.0-4.3.6.zip) |
| Windows | x86 (32 Bit) | 1.1 | [dispatcher-iis-windows-x86-ssl1.1-4.3.6.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.1-4.3.6.zip) |
| Windows | x64 (64 Bit) | Ohne | [dispatcher-iis-windows-x64-4.3.6.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-4.3.6.zip) |
| Windows | x64 (64 Bit) | 1.0 | [dispatcher-iis-windows-x64-ssl1.0-4.3.6.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.0-4.3.6.zip) |
| Windows | x64 (64 Bit) | 1.1 | [dispatcher-iis-windows-x64-ssl1.1-4.3.6.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.1-4.3.6.zip) |