---
title: Versionshinweise zu AEM Dispatcher
seo-title: Versionshinweise zu AEM Dispatcher
description: Spezifische Versionshinweise für Adobe Experience Manager Dispatcher
seo-description: Spezifische Versionshinweise für Adobe Experience Manager Dispatcher
uuid: ae3ccf62-0514-4c03-a3b9-71799a482cbd
topic-tags: Versionshinweise
content-type: Referenz
products: SG_EXPERIENCEMANAGER/6.4
discoiquuid: ff3d38e0-71c9-4b41-85f9-fa896393aac5
translation-type: tm+mt
source-git-commit: 2d72839d459973cba40f6a938ee157198c7cf50a

---


# Versionshinweise zu AEM Dispatcher{#aem-dispatcher-release-notes}

## Versionshinweise {#release-information}

|  |  |
|--- |--- |
| Produkte | Adobe Experience Manager (AEM) Dispatcher |
| Version | 4.3.2 |
| Typ | Nebenversion |
| Datum | 31.Januar 2019 |
| Download-URL | <ul><li>[Apache 2.4](release-notes.md#apache)</li><li>[Microsoft Internet Information Services (IIS)](release-notes.md#iis)</li></ul> |
| Kompatibilität | AEM 6.1 oder höher |

## Systemanforderungen und Voraussetzungen {#system-requirements-and-prerequisites}

Please see the [Supported Platforms](https://helpx.adobe.com/experience-manager/6-4/sites/deploying/using/technical-requirements.html) page for more information regarding requirements and prerequisites.

Adobe rät ausdrücklich dazu, die neueste Version von AEM Dispatcher zu verwenden, um von den neuesten Funktionen, behobenen Fehlern und der bestmöglichen Leistung zu profitieren.

## Installationsanweisungen {#installation-instructions}

Detaillierte Anweisungen finden Sie unter [Installieren des Dispatchers](dispatcher-install.md).

## Versionsverlauf {#release-history}

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

| Plattform | Architektur | SSL-Unterstützung | Herunterladen |
|---|---|---|---|
| AIX | PowerPC (32 Bit) | Nein | [dispatcher-apache2.4-aix-powerpc-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-aix-powerpc-4.3.2.tar.gz) |
| AIX | PowerPC (32 Bit) | Ja | [dispatcher-apache2.4-aix-powerpc-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-aix-powerpc-ssl-4.3.2.tar.gz) |
| AIX | PowerPC (64 Bit) | Nein | [dispatcher-apache2.4-aix-powerpc64-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-aix-powerpc64-4.3.2.tar.gz) |
| AIX | PowerPC (64 Bit) | Ja | [dispatcher-apache2.4-aix-powerpc64-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-aix-powerpc64-ssl-4.3.2.tar.gz) |
| Linux | i686 (32 Bit) | Nein | [dispatcher-apache2.4-linux-i686-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-4.3.2.tar.gz) |
| Linux | i686 (32 Bit) | Ja | [dispatcher-apache2.4-linux-i686-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl-4.3.2.tar.gz) |
| Linux | x86_64 (64 Bit) | Nein | [dispatcher-apache2.4-linux-x86_64-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-4.3.2.tar.gz) |
| Linux | x86_64 (64 Bit) | Ja | [dispatcher-apache2.4-linux-x86_64-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl-4.3.2.tar.gz) |
| macOS | x86_64 (64 Bit) | Nein | [dispatcher-apache2.4-darwin-x86_64-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-x86_64-4.3.2.tar.gz) |
| Solaris | AMD (32 Bit) | Nein | [dispatcher-apache2.4-solaris-i386-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-i386-4.3.2.tar.gz) |
| Solaris | AMD (32 Bit) | Ja | [dispatcher-apache2.4-solaris-i386-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-i386-ssl-4.3.2.tar.gz) |
| Solaris | AMD (64 Bit) | Nein | [dispatcher-apache2.4-solaris-amd64-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-amd64-4.3.2.tar.gz) |
| Solaris | AMD (64 Bit) | Ja | [dispatcher-apache2.4-solaris-amd64-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-amd64-ssl-4.3.2.tar.gz) |
| Solaris | SPARC (32 Bit) | Nein | [dispatcher-apache2.4-solaris-sparc-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-sparc-4.3.2.tar.gz) |
| Solaris | SPARC (32 Bit) | Ja | [dispatcher-apache2.4-solaris-sparc-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-sparc-ssl-4.3.2.tar.gz) |
| Solaris | SPARC (64 Bit) | Nein | [dispatcher-apache2.4-solaris-sparcv9-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-sparcv9-4.3.2.tar.gz) |
| Solaris | SPARC (64 Bit) | Ja | [dispatcher-apache2.4-solaris-sparcv9-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-sparcv9-ssl-4.3.2.tar.gz) |

### IIS {#iis}

| Plattform | Architektur | SSL-Unterstützung | Herunterladen |
|---|---|---|---|
| Windows | x86 (32 Bit) | Nein | [dispatcher-is-windows-x86-4.3.2.zip](http://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-4.3.2.zip) |
| Windows | x86 (32 Bit) | Ja | [dispatcher-is-windows-x86-ssl-4.3.2.zip](http://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl-4.3.2.zip) |
| Windows | x64 (64 Bit) | Nein | [dispatcher-is-windows-x64-4.3.2.zip](http://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-4.3.2.zip) |
| Windows | x64 (64 Bit) | Ja | [dispatcher-is-windows-x64-ssl-4.3.2.zip](http://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl-4.3.2.zip) |
