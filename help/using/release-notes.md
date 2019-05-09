---
title: AEM Dispatcher Versionshinweise
seo-title: AEM Dispatcher Versionshinweise
description: Versionshinweise speziell für Adobe Experience Manager Dispatcher
seo-description: Versionshinweise speziell für Adobe Experience Manager Dispatcher
uuid: ae 3 ccf 62-0514-4 c 03-a 3 b 9-71799 a 482 cbd
topic-tags: Versionshinweise
content-type: Referenz
products: SG_ EXPERIENCEMANAGER/6.4
discoiquuid: ff 3 d 38 e 0-71 c 9-4 b 41-85 f 9-fa 896393 aac 5
translation-type: tm+mt
source-git-commit: 2d72839d459973cba40f6a938ee157198c7cf50a

---


# AEM Dispatcher Versionshinweise{#aem-dispatcher-release-notes}

## Versionshinweise {#release-information}

|  |
|--- |--- |
| Produkte | Adobe Experience Manager (AEM) Dispatcher |
| Version | 4.3.2 |
| Typ | Nebenversion |
| Datum | 31. Januar 2019 |
| Download-URL | <ul><li>[Apache 2.4](release-notes.md#apache)</li><li>[Microsoft Internet Information Services (IIS)](release-notes.md#iis)</li></ul> |
| Kompatibilität | AEM 6.1 oder höher |

## Systemanforderungen und Voraussetzungen {#system-requirements-and-prerequisites}

Weitere Informationen [zu Anforderungen und Voraussetzungen finden Sie auf der](https://helpx.adobe.com/experience-manager/6-4/sites/deploying/using/technical-requirements.html) Seite Unterstützte Plattformen.

Adobe empfiehlt dringend, die neueste Version von AEM Dispatcher zu verwenden, um die neuesten Funktionen, die neuesten Fehlerbehebungen und die bestmögliche Leistung zu nutzen.

## Installationsanweisungen {#installation-instructions}

Detaillierte Anweisungen finden Sie unter [Dispatcher installieren](dispatcher-install.md).

## Versionsverlauf {#release-history}

### Version 4.3.2 (2019-Jan -31) {#jan}

**Fehlerbehebungen**:

* Dialogfeld -734 - Dispatcher verursacht Absturz in insert_ output_ filter, falls nicht als Handler festgelegt
* Dialog-735 - RES funktioniert nicht auf Linux
* DISP -740 - Das Laden von Dispatcher in macos Mojave ist standardmäßig deaktiviert
* Dialogfeld -742 - Blockierte Anforderungen können Informationen zu Authentifizierungs-geschützten Ressourcen enthalten.

**Verbesserungen**:

* Dialogfeld -746 - Nicht gekennzeichnete Zeichenfolgen im Dispatcher. any sollte eine Warnung generieren.

**Neue Funktion**:

* Dialogfeld -747 - Bereitstellen von Anforderungsinformationen in der Apache-Umgebung

### Version 4.3.1 (2018-Okt. -16) {#oct}

**Fehlerbehebungen**:

* DISP -656 - Dispatcher liefert falsche etag-Kopfzeile
* Dialogfeld -694 - Unterdrücken Sie Warnungen, wenn Sie Verbindungen mit einer schlechten Verbindung beibehalten.
* DISP -714 - Sitzungsbasiertes Sitzungsmanagement funktioniert nicht in IIS.
* Dialogfeld -715 - Sichere Kennzeichnung für Renderid-Cookie
* DIALOGFELD -720 - Temporäre Dateien, die nicht geschlossen wurden, können zur Erschöpfung führen (zu viele geöffnete Dateien)
* Dialogfeld -721 - Dispatcher unterbricht Umfrage (), wenn die untergeordneten Elemente des Kindes vollständig neu gestartet werden
* Dialogfeld -722 - Cache-Dateien werden mit dem oktalen Modus 0600 erstellt.
* Dialogfeld -723 - Implizites 10-minütiges Timeout (und Wiederholen), wenn Render-Timeouts auf 0 gesetzt werden
* Dialogfeld -725 - Nachgestellte Zeichen werden nacheinander in unbenannte Werte umgewandelt.
* --726 - Protokolliert eine Warnung, wenn keine Farm tatsächlich dem eingehenden Host entspricht
* Dialogfeld -727 - Dispatcher prüft die Inhaltslänge für leere Cachedateien.
* Dialogfeld -730 - 404 beim Versuch, auf Header-Datei über Dispatcher zuzugreifen
* Dialogfeld -731 - Dispatcher ist anfällig für Protokollinjizierung
* Dialogfeld -732 - Dispatcher sollte aufeinander folgender &#39;/&#39; in der URL entfernt werden
* Dialogfeld -733 - Dispatcher sollte eine Altersüberschrift festlegen (berechnen)

**Verbesserungen**:

* DISP -656 - Dispatcher liefert falsche etag-Kopfzeile
* Dialogfeld -694 - Unterdrücken Sie Warnungen, wenn Sie Verbindungen mit einer schlechten Verbindung beibehalten.
* Dialogfeld -715 - Sichere Kennzeichnung für Renderid-Cookie
* Dialogfeld -722 - Cache-Dateien werden mit dem oktalen Modus 0600 erstellt.
* --726 - Protokolliert eine Warnung, wenn keine Farm tatsächlich dem eingehenden Host entspricht

### Version 4.3.0 (2018-Juni -13) {#jun}

**Fehlerbehebungen**:

* Dialogfeld -682 - Numerische Protokollierungsstufe falsch angewendet
* DISP -685 - 32-Bit Solaris SPARC-Binärdateien haben einen nicht definierten Verweis auf__ divdi 3
* DISP -688 - Dispatcher gibt nicht den Header &quot;X-Cache-Info&quot; auf 404 Antwort zurück.
* DISP -690 - Die zuletzt geänderte Kopfzeile ist nicht cachable.
* Dialog-691 - Zugriffsverletzungen in w 3 wp. exe
* Dialogfeld -693 - Aktualisierung der Architekturdetails für solaris-Server auf der Dispatcher-Downloadseite
* DIALOGFELD -695 - Problem mit dispatcherprotokollebene im Dispatcher-Modul 4.2.3
* DISP -698 - Dispatcher TTL muss s-maxage und private Anweisungen unterstützen.
* Dialog-700 - Modul funktioniert auf Linux nicht ordnungsgemäß
* Dialogfeld -704 - Browseranforderungen mit % 2 b werden nicht kodiert an den Herausgeber gesendet.
* Dialogfeld -705 - Dispatcher stürzt aufgrund doppelter Kostenloser oder Beschädigung (Fastoben) ab
* -706 - Während der Ungültigmachung lautet der Dispatcher die folgenden Referenzsymlinks, die eine Endlosschleife verursachen können.
* Dialogfeld -709 - Blockieren einiger Vanity-URL-Erweiterungen
* DISP -710 - Builds für Linux nicht verwendbar für Cent OS 6

**Verbesserungen**:

* DISP -652 - Dispatcher liefert falsche Datumskopfzeile

## Hilfreiche Ressourcen {#helpful-resources}

* [AEM Dispatcher-Übersicht](dispatcher.md)

## Downloads {#downloads}

### Apache 2.4 {#apache}

| Plattform | Architektur | SSL-Unterstützung | Herunterladen |
|---|---|---|---|
| AIX | Powerpc (32 Bit) | Nein | [dispatcher-apache2.4-aix-powerpc-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-aix-powerpc-4.3.2.tar.gz) |
| AIX | Powerpc (32 Bit) | Ja | [dispatcher-apache2.4-aix-powerpc-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-aix-powerpc-ssl-4.3.2.tar.gz) |
| AIX | Powerpc (64 Bit) | Nein | [dispatcher-apache2.4-aix-powerpc64-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-aix-powerpc64-4.3.2.tar.gz) |
| AIX | Powerpc (64 Bit) | Ja | [dispatcher-apache2.4-aix-powerpc64-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-aix-powerpc64-ssl-4.3.2.tar.gz) |
| Linux | i 686 (32 Bit) | Nein | [dispatcher-apache2.4-linux-i686-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-4.3.2.tar.gz) |
| Linux | i 686 (32 Bit) | Ja | [dispatcher-apache2.4-linux-i686-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl-4.3.2.tar.gz) |
| Linux | x 86_ 64 (64 Bit) | Nein | [dispatcher-apache2.4-linux-x86_64-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-4.3.2.tar.gz) |
| Linux | x 86_ 64 (64 Bit) | Ja | [dispatcher-apache2.4-linux-x86_64-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl-4.3.2.tar.gz) |
| macOS | x 86_ 64 (64 Bit) | Nein | [dispatcher-apache2.4-darwin-x86_64-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-x86_64-4.3.2.tar.gz) |
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
| Windows | x 86 (32 Bit) | Nein | [dispatcher-iis-windows-x86-4.3.2.zip](http://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-4.3.2.zip) |
| Windows | x 86 (32 Bit) | Ja | [dispatcher-iis-windows-x86-ssl-4.3.2.zip](http://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl-4.3.2.zip) |
| Windows | x 64 (64 Bit) | Nein | [dispatcher-iis-windows-x64-4.3.2.zip](http://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-4.3.2.zip) |
| Windows | x 64 (64 Bit) | Ja | [dispatcher-iis-windows-x64-ssl-4.3.2.zip](http://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl-4.3.2.zip) |
