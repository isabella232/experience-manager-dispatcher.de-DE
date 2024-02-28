---
title: Installieren des Dispatchers
seo-title: Installing AEM Dispatcher
description: Erfahren Sie, wie Sie das Dispatcher-Modul auf Microsoft Internet Information Server, Apache Web Server und Sun Java Web Server-iplanet installieren.
seo-description: Learn how to install the AEM Dispatcher module on Microsoft Internet Information Server, Apache Web Server and Sun Java Web Server-iPlanet.
uuid: 2384b907-1042-4707-b02f-fba2125618cf
contentOwner: User
converted: true
topic-tags: dispatcher
content-type: reference
discoiquuid: f00ad751-6b95-4365-8500-e1e0108d9536
exl-id: 9375d1c0-8d9e-46cb-9810-fa4162a8c1ba
source-git-commit: c3a5f415df91bee4b6e0a6c9b813b62a906670c6
workflow-type: tm+mt
source-wordcount: '3798'
ht-degree: 47%

---

# Installieren des Dispatchers {#installing-dispatcher}

<!-- 

Comment Type: draft

<h2>Introduction</h2>

 -->

Rufen Sie über die Seite [Versionshinweise zu Dispatcher](release-notes.md) die neueste Dispatcher-Installationsdatei für Ihr Betriebssystem und Ihren Webserver ab. Die Dispatcher-Versionsnummern sind unabhängig von den Adobe Experience Manager-Versionsnummern und mit den Versionen Adobe Experience Manager 6.x, 5.x und Adobe CQ 5.x kompatibel.

>[!NOTE]
>
>Beachten Sie, dass für Adobe Experience Manager 6.5 die Dispatcher-Version 4.3.2 oder höher erforderlich ist. Allerdings sind Dispatcher-Versionen unabhängig von AEM, z. B. ist die Dispatcher-Version 4.3.2 auch mit Adobe Experience Manager 6.4 kompatibel.

Die folgende Dateibenennungskonvention wird verwendet:

`dispatcher-<web-server>-<operating-system>-<dispatcher-version-number>.<file-format>`

Beispielsweise enthält die Datei `dispatcher-apache2.4-linux-x86_64-ssl-4.3.1.tar.gz` die Dispatcher-Version 4.3.1 für einen Apache 2.4-Webserver, der unter Linux i686 ausgeführt wird. Diese Datei ist im **tar**-Format gepackt.

In der folgenden Tabelle ist die Webserverkennung aufgeführt, die in den Dateinamen für die einzelnen Webserver verwendet wird:

| Webserver | Installations-Kit |
|--- |--- |
| Apache 2.4 | dispatcher-apache **2.4**-&lt;andere Parameter> |
| Microsoft Internet Information Server 7.5, 8, 8.5, 10 | dispatcher-**iis**-&lt;andere Parameter> |
| Sun Java Web Server/iPlanet | dispatcher-**ns**-&lt;andere Parameter> |

>[!CAUTION]
>
>Sie sollten die neueste Version des Dispatchers installieren, die für Ihre Plattform verfügbar ist. Jedes Jahr sollten Sie Ihre Dispatcher-Instanz so aktualisieren, dass sie die neueste Version verwendet, um von den Produktverbesserungen zu profitieren.

>[!NOTE]
>
>Kunden, die ein spezifisches Upgrade von Version 4.3.3 auf Version 4.3.4 durchführen, werden feststellen, dass beim Festlegen von Zwischenspeicherkopfzeilen für nicht erreichbare Inhalte ein anderes Verhalten auftritt. Weitere Informationen zu dieser Änderung finden Sie unter [Versionshinweise](/help/using/release-notes.md#nov) Seite.

Jedes Archiv enthält die folgenden Dateien:

* die Dispatcher-Module
* eine Beispielkonfigurationsdatei
* die README-Datei mit Installationsanweisungen und Last-Minute-Informationen
* die CHANGES-Datei, in der Probleme aufgeführt sind, die in der aktuellen und in vorherigen Versionen behoben wurden

>[!NOTE]
>
>Bitte informieren Sie sich in der README-Datei über kurzfristig eingeführte Änderungen/plattformspezifische Hinweise, bevor Sie mit der Installation beginnen.

<!-- 

Comment Type: draft

<h3>Supported Web Servers</h3>

 -->

<!-- 

Comment Type: draft

<p>The following web servers are supported for use with Dispatcher version 4.1.12:</p>

 -->

<!-- 

Comment Type: draft

<p>The following sections detail the specific web server installation procedures.</p>

 -->

## Microsoft Internet Information Server {#microsoft-internet-information-server}

Informationen zur Installation dieses Webservers finden Sie in den folgenden Ressourcen:

* Microsoft-eigene Dokumentation zum Internet Information Server
* [&quot;Offizielle IIS-Website von Microsoft&quot;](https://www.iis.net/)

### Erforderliche IIS-Komponenten {#required-iis-components}

Für die Nutzung der IIS-Versionen 8.5 und 10 müssen die folgenden IIS-Komponenten installiert sein:

* ISAPI-Erweiterungen

Außerdem müssen Sie die Rolle &quot;Webserver (IIS)&quot;hinzufügen. Verwenden Sie Server Manager , um die Rolle und die Komponenten hinzuzufügen.

## Microsoft IIS – Installieren des Dispatcher-Moduls  {#microsoft-iis-installing-the-dispatcher-module}

Das erforderliche Archiv für Microsoft Internet Information System ist:

* `dispatcher-iis-<operating-system>-<dispatcher-release-number>.zip`

Die ZIP-Datei enthält die folgenden Dateien:

| File | Beschreibung |
|--- |--- |
| `disp_iis.dll` | Die DLL-Datei (Dynamic Link Library) des Dispatchers. |
| `disp_iis.ini` | Konfigurationsdatei für IIS. Dieses Beispiel kann mit Ihren Anforderungen aktualisiert werden. **Hinweis**: Die INI-Datei muss denselben Namensstamm wie die DLL haben. |
| `dispatcher.any` | Eine Beispielkonfigurationsdatei für den Dispatcher. |
| `author_dispatcher.any` | Eine Beispielkonfigurationsdatei für den Dispatcher bei Verwendung der Autoreninstanz. |
| README | Datei mit Installationsanweisungen und aktuellen Informationen. **Hinweis**: Lesen Sie die Informationen in dieser Datei, bevor Sie mit der Installation beginnen. |
| ÄNDERUNGEN | Änderungsdatei, in der Probleme aufgelistet werden, die in aktuellen und früheren Versionen behoben wurden. |

Führen Sie die folgenden Schritte aus, um die Dispatcher-Dateien an den richtigen Speicherort zu kopieren.

1. Verwenden Sie Windows Explorer, um das Verzeichnis `<IIS_INSTALLDIR>/Scripts` zu erstellen, beispielsweise `C:\inetpub\Scripts`.

1. Extrahieren Sie die folgenden Dateien aus dem Dispatcher-Paket in dieses Skriptverzeichnis:

   * `disp_iis.dll`
   * `disp_iis.ini`
   * Eine der folgenden Dateien, je nachdem ob der Dispatcher mit einer AEM-Autoreninstanz oder -Veröffentlichungsinstanz eingesetzt wird:
      * Autoreninstanz: `author_dispatcher.any`
      * Veröffentlichungsinstanz: `dispatcher.any`

## Microsoft IIS - Konfigurieren der INI-Datei des Dispatchers {#microsoft-iis-configure-the-dispatcher-ini-file}

Bearbeiten Sie die Datei `disp_iis.ini`, um die Dispatcher-Installation zu konfigurieren. Das Standardformat der `.ini`-Datei lautet wie folgt:

```xml
[main]
configpath=<path to dispatcher.any>
loglevel=1|2|3
servervariables=0|1
replaceauthorization=0|1
```

In der folgenden Tabelle werden die einzelnen Eigenschaften beschrieben.

| Parameter | Beschreibung |
|--- |--- |
| configpath | Der Speicherort von `dispatcher.any` im lokalen Dateisystem (absoluter Pfad). |
| logfile | Der Speicherort der `dispatcher.log`-Datei. Wenn dieser nicht festgelegt ist, werden Protokollmeldungen im Windows-Ereignisprotokoll gespeichert. |
| loglevel | Definiert die Protokollebene, die zum Ausgeben von Nachrichten in das Ereignisprotokoll verwendet wird. Die folgenden Werte können angegeben werden:Protokollebene für die Protokolldatei: <br/>0 - nur Fehlermeldungen. <br/>1 – Fehlermeldungen und Warnungen. <br/>2 – Fehler, Warnungen und Informationsmeldungen <br/>3 – Fehler, Warnungen, Informations- und Problembehebungsnachrichten. <br/>**Hinweis**: Es wird empfohlen, die Protokollebene während der Installation und Tests auf 3 festzulegen und dann bei Ausführung in einer Produktionsumgebung auf 0 zu setzen. |
| Ersatzgenehmigung | Gibt an, wie Autorisierungskopfzeilen in der HTTP-Anforderung verarbeitet werden. Die folgenden Werte sind gültig:<br/>0 - Autorisierungskopfzeilen werden nicht geändert. <br/>1 – ersetzt alle Header mit dem Namen „Authorization“ mit Ausnahme von „Basic“ durch die `Basic <IIS:LOGON\_USER>`-Entsprechung.<br/> |
| servervariables | Definiert, wie Servervariablen verarbeitet werden.<br/>0 – IIS-Servervariablen werden weder an den Dispatcher noch an AEM gesendet. <br/>1 – Alle IIS-Servervariablen (wie `LOGON\_USER, QUERY\_STRING, ...`) werden zusammen mit den Anforderungsheadern an den Dispatcher gesendet (und an die AEM-Instanz, wenn keine Zwischenspeicherung erfolgt).  <br/>Zu den Servervariablen gehören `AUTH\_USER, LOGON\_USER, HTTPS\_KEYSIZE` und viele andere. In der IIS-Dokumentation finden Sie eine umfassende Liste der Variablen mit detaillierten Informationen. |
| enable_chunked_transfer | Definiert, ob die Blockübertragung für die Clientantwort aktiviert (1) oder deaktiviert (0) werden soll. Der Standardwert ist 0. |

Eine Beispielkonfiguration:

```xml
[main]
configpath=C:\Inetpub\Scripts\dispatcher.any
loglevel=1
servervariables=1
replaceauthorization=0
```

### Konfigurieren von Microsoft IIS {#configuring-microsoft-iis}

Konfigurieren Sie IIS, um das ISAPI-Modul des Dispatchers zu integrieren. In IIS verwenden Sie die Zuordnung von Wildcard-Anwendungen.

### Anonymen Zugriff konfigurieren - IIS 8.5 und 10 {#configuring-anonymous-access-iis-and}

Der standardmäßige Flush-Replikationsagent auf der -Autoreninstanz ist so konfiguriert, dass keine Sicherheitsberechtigungen mit Flush-Anfragen gesendet werden. Daher muss die Website, die Sie mit einem Dispatcher-Cache verwenden, einen anonymen Zugriff zulassen.

Wenn Ihre Website eine Authentifizierungsmethode verwendet, muss der Flush-Replikationsagent entsprechend konfiguriert werden.

1. Öffnen Sie IIS Manager und wählen Sie die Website aus, die Sie als Dispatcher-Cache verwenden.
1. Doppelklicken Sie im Ansichtsmodus &quot;Funktionen&quot;im Abschnitt &quot;IIS&quot;auf &quot;Authentifizierung&quot;.
1. Wenn die anonyme Authentifizierung nicht aktiviert ist, wählen Sie Anonyme Authentifizierung und klicken Sie im Bereich &quot;Aktionen&quot;auf Aktivieren.

### Integrieren des ISAPI-Moduls des Dispatchers - IIS 8.5 und 10 {#integrating-the-dispatcher-isapi-module-iis-and}

Gehen Sie wie folgt vor, um das ISAPI-Modul des Dispatchers zu IIS hinzuzufügen.

1. Öffnen Sie IIS Manager.
1. Wählen Sie die Website aus, die Sie als Dispatcher-Cache verwenden.
1. Doppelklicken Sie im Ansichtsmodus &quot;Funktionen&quot;im Abschnitt &quot;IIS&quot;auf Handler-Zuordnungen .
1. Klicken Sie im Bereich &quot;Aktionen&quot;der Seite &quot;Handler-Zuordnungen&quot;auf &quot;Wildcard Script Map hinzufügen&quot;, fügen Sie die folgenden Eigenschaftswerte hinzu und klicken Sie auf &quot;OK&quot;:

   * Anfragepfad: &#42;
   * Ausführbare Datei: der absolute Pfad der Datei „disp_iis.dll“, beispielsweise `C:\inetpub\Scripts\disp_iis.dll`.
   * Name: ein beschreibender Name für die Handlerzuordnung, beispielsweise `Dispatcher`.

1. Klicken Sie im angezeigten Dialogfeld auf &quot;Ja&quot;, um die Bibliothek disp_iis.dll zur Liste der ISAPI- und CGI-Einschränkungen hinzuzufügen.

   Bei IIS 7.0 und 7.5 ist die Konfiguration abgeschlossen. Fahren Sie mit den restlichen Schritten fort, wenn Sie IIS 8.0 konfigurieren.

1. (IIS 8.0) Wählen Sie in der Liste der Handler-Mappings das soeben erstellte aus und klicken Sie im Bereich &quot;Aktionen&quot;auf &quot;Bearbeiten&quot;.
1. (IIS 8.0) Klicken Sie im Dialogfeld „Skriptzuordnung bearbeiten“ auf die Schaltfläche „Einschränkungen“.
1. (IIS 8.0) Um sicherzustellen, dass der Handler für Dateien und Ordner verwendet wird, die noch nicht im Cache zwischengespeichert sind, deaktivieren Sie das Kontrollkästchen „Handler nur bei folgender Zuordnung aufrufen“ und klicken Sie auf „OK“.
1. (IIS 8.0) Klicken Sie im Dialogfeld „Skriptzuordnung bearbeiten“ auf „OK“.

### Konfigurieren des Zugriffs auf den Cache - IIS 8.5 und 10 {#configuring-access-to-the-cache-iis-and}

Geben Sie den standardmäßigen Anwendungspoolbenutzer mit Schreibzugriff auf den Ordner an, der als Dispatcher-Cache verwendet werden soll.

1. Klicken Sie mit der rechten Maustaste auf den Basisordner der Website, die Sie als Dispatcher-Cache verwenden, und klicken Sie auf „Eigenschaften“, beispielsweise `C:\inetpub\wwwroot`
1. Klicken Sie auf der Registerkarte Sicherheit auf Bearbeiten und klicken Sie anschließend im Dialogfeld Berechtigungen auf Hinzufügen. Es wird ein Dialogfeld zur Auswahl von Benutzerkonten geöffnet. Klicken Sie auf die Schaltfläche Standorte , wählen Sie den Namen des Computers aus und klicken Sie auf OK.

   Lassen Sie dieses Dialogfeld geöffnet, während Sie den nächsten Schritt abschließen.

1. Wählen Sie im IIS-Manager die IIS-Website, die Sie als Dispatcher-Cache verwenden, und klicken Sie rechts im Fenster auf „Erweiterte Einstellungen“.
1. Wählen Sie den Wert der Eigenschaft &quot;Application Pool&quot;aus und kopieren Sie ihn in die Zwischenablage.
1. Kehren Sie zum Dialogfeld &quot;Öffnen&quot;zurück. Geben Sie im Feld „Geben Sie die zu verwendenden Objektnamen ein“ `IIS AppPool\` ein und fügen Sie dann den Inhalt aus der Zwischenablage ein. Der Wert sollte wie folgt lauten:

   `IIS AppPool\DefaultAppPool`

1. Klicken Sie auf die Schaltfläche Namen überprüfen . Wenn Windows das Benutzerkonto auflöst, klicken Sie auf OK.
1. Wählen Sie im Dialogfeld „Berechtigungen“ für den Ordner „Dispatcher“ das Konto, das Sie gerade hinzugefügt haben, aktivieren Sie alle Berechtigungen für das Konto **außer Vollzugriff** und klicken Sie auf „OK“. Klicken Sie auf „OK“, um das Dialogfeld „Eigenschaften“ zu schließen.

### Registrieren des JSON Mime-Typs - IIS 8.5 und 10 {#registering-the-json-mime-type-iis-and}

Führen Sie das folgende Verfahren aus, um den JSON-MIME-Typ zu registrieren, wenn der Dispatcher JSON-Aufrufe zulassen soll.

1. Wählen Sie im IIS-Manager Ihre Website aus und doppelklicken Sie in der Funktionsansicht auf MIME-Typen.
1. Wenn die JSON-Erweiterung nicht in der Liste enthalten ist, klicken Sie im Bereich &quot;Aktionen&quot;auf &quot;Hinzufügen&quot;, geben Sie die folgenden Eigenschaftswerte ein und klicken Sie auf &quot;OK&quot;:

   * Dateinamenerweiterung: `.json`
   * MIME Type: `application/json`

### Entfernen des ausgeblendeten Segments &quot;bin&quot;- IIS 8.5 und 10 {#removing-the-bin-hidden-segment-iis-and}

Führen Sie die folgenden Schritte aus, um das ausgeblendete Segment `bin` zu entfernen. Nicht neue Websites können dieses ausgeblendete Segment enthalten.

1. Wählen Sie im IIS-Manager Ihre Website aus und doppelklicken Sie in der Ansicht &quot;Features&quot;auf &quot;Request Filtering&quot;.
1. Wählen Sie das Segment `bin`, klicken Sie auf „Entfernen“ und klicken Sie im Bestätigungsdialogfeld auf „Ja“.

### Protokollieren von IIS-Meldungen in einer Datei - IIS 8.5 und 10 {#logging-iis-messages-to-a-file-iis-and}

Führen Sie die folgenden Schritte aus, um Dispatcher-Protokollmeldungen in eine Protokolldatei statt in das Windows-Ereignisprotokoll zu schreiben. Sie müssen den Dispatcher für die Verwendung der Protokolldatei konfigurieren und IIS Schreibzugriff auf die Datei gewähren.

1. Verwenden Sie Windows Explorer, um unter dem Protokollordner der IIS-Installation einen Ordner mit dem Namen `dispatcher` zu erstellen. Der Pfad dieses Ordners für eine Standardinstallation lautet `C:\inetpub\logs\dispatcher`.

1. Klicken Sie mit der rechten Maustaste auf den Ordner &quot;Dispatcher&quot;und klicken Sie auf Eigenschaften.
1. Klicken Sie auf der Registerkarte Sicherheit auf Bearbeiten und klicken Sie anschließend im Dialogfeld Berechtigungen auf Hinzufügen. Es wird ein Dialogfeld zur Auswahl von Benutzerkonten geöffnet. Klicken Sie auf die Schaltfläche Standorte , wählen Sie den Namen des Computers aus und klicken Sie auf OK.

   Lassen Sie dieses Dialogfeld geöffnet, während Sie den nächsten Schritt abschließen.

1. Wählen Sie im IIS-Manager die IIS-Website, die Sie als Dispatcher-Cache verwenden, und klicken Sie rechts im Fenster auf „Erweiterte Einstellungen“.
1. Wählen Sie den Wert der Eigenschaft &quot;Application Pool&quot;aus und kopieren Sie ihn in die Zwischenablage.
1. Kehren Sie zum Dialogfeld &quot;Öffnen&quot;zurück. Geben Sie im Feld „Geben Sie die zu verwendenden Objektnamen ein“ `IIS AppPool\` ein und fügen Sie dann den Inhalt aus der Zwischenablage ein. Der Wert sollte wie folgt lauten:

   `IIS AppPool\DefaultAppPool`

1. Klicken Sie auf die Schaltfläche Namen überprüfen . Wenn Windows das Benutzerkonto auflöst, klicken Sie auf OK.
1. Wählen Sie im Dialogfeld &quot;Berechtigungen&quot;für den Ordner &quot;Dispatcher&quot;das Konto aus, das Sie gerade hinzugefügt haben, aktivieren Sie alle Berechtigungen für das Konto **mit Ausnahme der vollständigen Kontrolle,** und klicken Sie auf OK. Klicken Sie auf „OK“, um das Dialogfeld „Eigenschaften“ zu schließen.
1. Verwenden Sie einen Text-Editor, um die Datei `disp_iis.ini` zu öffnen.
1. Fügen Sie eine Textzeile ähnlich wie im folgenden Beispiel hinzu, um den Speicherort der Protokolldatei zu konfigurieren, und speichern Sie die Datei anschließend

   ```xml
   logfile=C:\inetpub\logs\dispatcher\dispatcher.log
   ```

### Nächste Schritte {#next-steps}

Bevor Sie mit der Verwendung des Dispatchers beginnen können, ist Folgendes zu beachten:

* [Konfiguration](dispatcher-configuration.md) des Dispatchers
* [Konfiguration von AEM](page-invalidate.md) zur Verwendung mit dem Dispatcher

## Apache-Webserver {#apache-web-server}

>[!CAUTION]
>
>Installationsanweisungen unter beiden **Windows** und **Unix** werden hier behandelt. Seien Sie beim Ausführen der Schritte vorsichtig.

### Installieren eines Apache-Webservers  {#installing-apache-web-server}

Informationen zum Installieren eines Apache-Webservers finden Sie im Installationshandbuch: [online](https://httpd.apache.org/) oder in der Distribution.

>[!CAUTION]
>
>Wenn Sie eine Apache-Binärdatei durch Kompilieren der Quelldateien erstellen, müssen Sie die Option **Unterstützung dynamischer Module**. Dies kann durch die Verwendung eines der **—enable-shared** Optionen. Nehmen Sie mindestens das `mod_so`-Modul auf.
>
>Weitere Informationen finden Sie im Installationshandbuch für Apache-Webserver.

Weitere Informationen finden Sie unter Apache HTTP Server [Sicherheitstipps](https://httpd.apache.org/docs/2.4/misc/security_tips.html) und [Sicherheitsberichte](https://httpd.apache.org/security_report.html).

### Apache-Webserver - Hinzufügen des Dispatcher-Moduls {#apache-web-server-add-the-dispatcher-module}

Der Dispatcher umfasst Folgendes:

* **Windows**: eine Dynamic Link Library (DLL)
* **Unix**: ein Dynamic Shared Object (DSO)

Die Installationsarchivdateien enthalten die folgenden Dateien - abhängig davon, ob Sie Windows oder Unix ausgewählt haben:

| File | Beschreibung |
|--- |--- |
| disp_apache&lt;x.y>.dll | Windows: die DLL-Datei des Dispatchers. |
| dispatcher-apache&lt;x.y>-&lt;Versionsnr>.so | Unix: die DSO-Datei des Dispatchers. |
| mod_dispatcher.so | Unix: ein Beispiellink. |
| http.conf.disp&lt;x> | Eine Beispielkonfigurationsdatei für den Apache-Server. |
| dispatcher.any | Eine Beispielkonfigurationsdatei für den Dispatcher. |
| README | Datei mit Installationsanweisungen und aktuellen Informationen. **Hinweis**: Lesen Sie die Informationen in dieser Datei, bevor Sie mit der Installation beginnen. |
| ÄNDERUNGEN | Änderungsdatei, in der Probleme aufgelistet werden, die in aktuellen und früheren Versionen behoben wurden. |

Führen Sie die folgenden Schritte aus, um den Dispatcher zu Ihrem Apache-Webserver hinzuzufügen:

1. Platzieren Sie die Dispatcher-Datei im entsprechenden Apache-Modulverzeichnis:

   * **Windows**: `disp_apache<x.y>.dll` in `<APACHE_ROOT>/modules` platzieren.
   * **Unix**: Suchen Sie je nach Installation entweder nach dem Verzeichnis `<APACHE_ROOT>/libexec` oder `<APACHE_ROOT>/modules`.\
     Kopieren Sie `dispatcher-apache<options>.so` in diesen Ordner.\
     Um die Verwaltung langfristig zu vereinfachen, können Sie auch eine symbolische Verknüpfung mit dem Namen `mod_dispatcher.so` zum Dispatcher erstellen:\
     `ln -s dispatcher-apache<x>-<os>-<rel-nr>.so mod_dispatcher.so`

1. Kopieren Sie die Datei „dispatcher.any“ in das Verzeichnis `<APACHE_ROOT>/conf`.

   **Hinweis:** Sie können diese Datei an einem anderen Speicherort ablegen, solange die DispatcherLog-Eigenschaft des Dispatcher-Moduls entsprechend konfiguriert ist. (Siehe Dispatcher-spezifische Konfigurationseinträge unten.)

### Apache-Webserver – Konfigurieren der SELinux-Eigenschaften  {#apache-web-server-configure-selinux-properties}

Wenn Sie den Dispatcher auf RedHat Linux Kernel 2.6 ausführen und SELinux aktiviert ist, werden Ihnen in der Dispatcher-Protokolldatei möglicherweise Fehlermeldungen wie diese angezeigt.

`Mon Jun 30 00:03:59 2013] [E] [16561(139642697451488)] Unable to connect to backend rend01 (10.122.213.248:4502): Permission denied`

Dies ist wahrscheinlich auf eine aktivierte SELinux-Sicherheit zurückzuführen. Führen Sie dann die folgenden Aufgaben aus:

* Konfigurieren Sie den SELinux-Kontext der Dispatcher-Moduldatei.
* Aktivieren Sie HTTPD-Skripte und -Module, um Netzwerkverbindungen herzustellen.
* Konfigurieren des SELinux-Kontextes des Dokumentenstamms, in dem die zwischengespeicherten Dateien gespeichert sind 

Geben Sie die folgenden Befehle in ein Terminal-Fenster ein und ersetzen Sie `[path to the dispatcher.so file]` mit dem Pfad zum Dispatcher-Modul, das Sie auf dem Apache-Webserver installiert haben, und *`path to the docroot`* mit dem Pfad, in dem sich das Basisverzeichnis befindet (z. B. `/opt/cq/cache`):

```shell
semanage fcontext -a -t httpd_modules_t [path to the dispatcher.so file]
setsebool -P httpd_can_network_connect on
chcon -R --type httpd_sys_rw_content_t [path to the docroot]
semanage fcontext -a -t httpd_sys_rw_content_t "[path to the docroot](/.*)?"
```

### Apache-Webserver - Konfigurieren des Apache-Webservers für den Dispatcher {#apache-web-server-configure-apache-web-server-for-dispatcher}

Der Apache-Webserver muss mithilfe von `httpd.conf` konfiguriert werden. Im Dispatcher Installation Kit finden Sie eine Beispielskonfigurationsdatei mit dem Namen `httpd.conf.disp<x>`.

Die folgenden Schritte sind erforderlich:

1. Navigieren Sie zu `<APACHE_ROOT>/conf`.
1. Öffnen Sie `httpd.conf` zur Bearbeitung.
1. Die folgenden Konfigurationseinträge müssen in der angegebenen Reihenfolge hinzugefügt werden:

   * **LoadModule** , um das Modul beim Start zu laden.
   * Dispatcher-spezifische Konfigurationseinträge, einschließlich **DispatcherConfig, DispatcherLog** und **DispatcherLogLevel**.
   * **SetHandler** , um den Dispatcher zu aktivieren. **LoadModule**.
   * **ModMimeUsePathInfo** zur Konfiguration des Verhaltens von **mod_mime**.

1. (Optional) Es wird empfohlen, den Eigentümer des Ordners &quot;htdocs&quot;zu ändern:

   * Der Apache-Server startet als Root, obwohl die untergeordneten Prozesse als Daemon starten (aus Sicherheitsgründen). DocumentRoot (`<APACHE_ROOT>/htdocs`) muss dem Benutzer Daemon angehören:

     ```xml
     cd <APACHE_ROOT>  
     chown -R daemon:daemon htdocs
     ```

**LoadModule**

In der folgenden Tabelle sind Beispiele aufgeführt, die verwendet werden können. Die genauen Einträge hängen von Ihrem Apache-Webserver ab:

|  |  |
|--- |--- |
| Windows | `... LoadModule dispatcher_module modules\disp_apache.dll ...` |
| Unix  (ausgehend von einer symbolischen Verknüpfung) | `... LoadModule dispatcher_module libexec/mod_dispatcher.so ...` |

>[!NOTE]
>
>Der erste Parameter jeder Anweisung muss genau wie in den oben aufgeführten Beispielen geschrieben sein.
>
>Ausführliche Informationen zu diesem Befehl finden Sie in den bereitgestellten Beispielkonfigurationsdateien und in der Dokumentation zum Apache-Webserver .

**Dispatcher-spezifische Konfigurationseinträge**

Die Dispatcher-spezifischen Konfigurationseinträge werden nach dem LoadModule-Eintrag platziert. Die folgende Tabelle enthält eine Beispielkonfiguration, die sowohl für Unix als auch für Windows anwendbar ist:

**Windows und Unix**

```
...
<IfModule disp_apache2.c>
DispatcherConfig conf/dispatcher.any
DispatcherLog logs/dispatcher.log DispatcherLogLevel 3
DispatcherNoServerHeader 0 DispatcherDeclineRoot 0
DispatcherUseProcessedURL 0
DispatcherPassError 0
DispatcherKeepAliveTimeout 60
</IfModule>
...
```

>[!NOTE]
>
>Kunden, die ein spezifisches Upgrade von Version 4.3.3 auf Version 4.3.4 durchführen, werden feststellen, dass beim Festlegen von Zwischenspeicherkopfzeilen für nicht erreichbare Inhalte ein anderes Verhalten auftritt. Weitere Informationen zu dieser Änderung finden Sie unter [Versionshinweise](/help/using/release-notes.md#nov) Seite.

Die einzelnen Konfigurationsparameter lauten: 

| Parameter | Beschreibung |
|--- |--- |
| DispatcherConfig | Speicherort und Name der Dispatcher-Konfigurationsdatei. <br/>Wenn sich diese Eigenschaft in der Hauptserverkonfiguration befindet, übernehmen alle virtuellen Hosts den Eigenschaftenwert. Allerdings können virtuelle Hosts die Eigenschaft DispatcherConfig einschließen, um die Hauptserverkonfiguration zu überschreiben. |
| DispatcherLog | Speicherort und Name der Protokolldatei. |
| DispatcherLogLevel | Protokollebene für die Protokolldatei: <br/>0 - Fehler <br/>1 - Warnungen <br/>2 - Infos <br/>3 - Debuggen <br/>**Hinweis**: Es wird empfohlen, die Protokollebene während der Installation und Tests auf 3 festzulegen und dann bei Ausführung in einer Produktionsumgebung auf 0 zu setzen. |
| DispatcherNoServerHeader | *Dieser Parameter wird veraltet und hat keine Auswirkung mehr.*<br/><br/> Definiert den zu verwendenden Server-Header: <br/><ul><li>nicht definiert oder 0 – der HTTP-Server-Header enthält die AEM-Version. </li><li>1 – der Apache-Serverheader wird verwendet.</li></ul> |
| DispatcherDeclineRoot | Definiert, ob Anforderungen an den Stamm &quot;/&quot;abgelehnt werden sollen: <br/>**0** - Anforderungen akzeptieren an / <br/>**1** - Anfragen an / werden vom Dispatcher nicht verarbeitet. Verwenden Sie mod_alias für die korrekte Zuordnung. |
| DispatcherUseProcessedURL | Definiert, ob vorverarbeitete URLs für die weitere Verarbeitung durch den Dispatcher verwendet werden sollen: <br/>**0** - die ursprüngliche URL verwenden, die an den Webserver übergeben wurde. <br/>**1** - Der Dispatcher verwendet die URL, die bereits von den Handlern verarbeitet wurde, die dem Dispatcher vorangehen (d. h. `mod_rewrite`) anstelle der ursprünglichen URL, die an den Webserver übergeben wird.  Beispielsweise wird entweder die ursprüngliche oder verarbeitete URL mit den Dispatcher-Filtern abgeglichen. Die URL wird auch als Grundlage für die Cachedateistruktur verwendet.   Weitere Informationen zu „mod_rewrite“ finden Sie in der Dokumentation auf der Apache-Website, z. B. Apache 2.4. Bei Verwendung von „mod_rewrite“ wird empfohlen, die Markierung „passthrough“ zu verwenden | PT&#39; (Weiterleiten an den nächsten Handler), um die Rewrite-Engine zum Festlegen des URI-Felds der internen Struktur auf den Wert des Dateinamenfelds zu zwingen. |
| DispatcherPassError | Definiert, wie Fehlercodes für die Verarbeitung von ErrorDocument unterstützt werden: <br/>**0** - Der Dispatcher spoolt alle Fehlerantworten an den Client. <br/>**1** - Der Dispatcher spoolt keine Fehlerantwort an den Client (wenn der Statuscode größer oder gleich 400 ist), übergibt jedoch den Statuscode an Apache, was es beispielsweise einer ErrorDocument-Direktive ermöglicht, einen solchen Statuscode zu verarbeiten. <br/>**Codebereich** - Geben Sie eine Reihe von Fehlercodes an, für die die Antwort an Apache übergeben wird. Andere Fehlercodes werden an den Client übergeben. Beispielsweise übergibt die folgende Konfiguration Antworten für Fehler 412 an den Client und alle anderen Fehler werden an Apache übergeben: DispatcherPassError 400-411,413-417 |
| DispatcherKeepAliveTimeout | Gibt den Keep-Alive-Timeout in Sekunden an. Ab Dispatcher-Version 4.2.0 beträgt der Standardwert für die Keep-Alive-Funktion 60. Wenn der Wert 0 lautet, wird der Keep-Alive-Timeout deaktiviert. |
| DispatcherNoCanonURL | Wenn Sie diesen Parameter auf „Ein“ setzen, wird die unformatierte URL anstelle der kanonisierten URL an das Backend übergeben und die Einstellungen von DispatcherUseProcessedURL überschrieben. Der Standardwert ist „Aus“. <br/>**Hinweis**: Die Filterregeln in der Dispatcher-Konfiguration werden immer anhand der bereinigten URL und nicht anhand der unformatierten URL ausgewertet. |




>[!NOTE]
>
>Pfadeinträge sind relativ zum Stammverzeichnis des Apache-Webservers.

>[!NOTE]
>
>Die Standardeinstellungen für den Server-Header lauten:
>
>`ServerTokens Full`
>
>`DispatcherNoServerHeader 0`
>
>Dies zeigt die AEM-Version (statistische Zwecke). Wenn Sie diese Informationen in der Kopfzeile deaktivieren möchten, können Sie Folgendes festlegen:
>
>`ServerTokens Prod`
>
>Siehe [Apache-Dokumentation zur ServerTokens-Direktive (zum Beispiel für Apache 2.4)](https://httpd.apache.org/docs/2.4/mod/core.html) für weitere Informationen.

**SetHandler**

Nach diesen Einträgen müssen Sie die Anweisung **SetHandler** zum Kontext Ihrer Konfiguration (`<Directory>`, `<Location>`) hinzufügen, damit der Dispatcher die eingehenden Anfragen verarbeiten kann. Im folgenden Beispiel wird der Dispatcher zur Bearbeitung von Anforderungen für die gesamte Website konfiguriert:

**Windows und Unix**

```
...  
<Directory />  
<IfModule disp_apache2.c>  
SetHandler dispatcher-handler  
</IfModule>  
  
Options FollowSymLinks  
AllowOverride None  
</Directory>  
...
```

Im folgenden Beispiel wird der Dispatcher zur Bearbeitung von Anforderungen für eine virtuelle Domäne konfiguriert:

**Windows**

```
...  
<VirtualHost 123.45.67.89>  
ServerName www.mycompany.com  
DocumentRoot _\[cache-path\]_\\docs  
<Directory _\[cache-path\]_\\docs>  
<IfModule disp_apache2.c>  
SetHandler dispatcher-handler  
</IfModule>  
AllowOverride None  
</Directory>  
</VirtualHost>  
...
```

**Unix**

```
...  
<VirtualHost 123.45.67.89>  
ServerName www.mycompany.com  
DocumentRoot /usr/apachecache/docs  
<Directory /usr/apachecache/docs>  
<IfModule disp_apache2.c>  
SetHandler dispatcher-handler  
</IfModule>  
AllowOverride None  
</Directory>  
</VirtualHost>  
...
```

>[!NOTE]
>
>Der Parameter der Anweisung **SetHandler** muss *genau wie in den oben aufgeführten Beispielen* geschrieben sein, da dies der Name des im Modul definierten Handlers ist.
>
>Ausführliche Informationen zu diesem Befehl finden Sie in den bereitgestellten Beispielkonfigurationsdateien und in der Dokumentation zum Apache-Webserver .

**ModMimeUsePathInfo**

Nach der Anweisung **SetHandler** sollten Sie auch die Definition **ModMimeUsePathInfo** hinzufügen.

>[!NOTE]
>
>Der Parameter `ModMimeUsePathInfo` sollte nur verwendet und konfiguriert werden, wenn Sie die Dispatcher-Version 4.0.9 oder höher verwenden.
>
>(Beachten Sie, dass die Dispatcher-Version 4.0.9 2011 veröffentlicht wurde. Wenn Sie eine ältere Version verwenden, ist ein Upgrade auf eine aktuelle Dispatcher-Version angemessen.)

Der Parameter **ModMimeUsePathInfo** sollte für alle Apache-Konfigurationen auf `On` festgelegt sein:

`ModMimeUsePathInfo On`

Das mod_mime-Modul (siehe beispielsweise [Apache Module mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html)) wird verwendet, um Inhaltsmetadaten dem Inhalt zuzuweisen, der für eine HTTP-Antwort ausgewählt wurde. Standardsetup bedeutet, dass nur der Teil der URL berücksichtigt wird, der einer Datei oder einem Verzeichnis zugeordnet ist, wenn „mod_mime“ den Inhaltstyp bestimmt.

Wenn die Angabe `On` lautet, gibt der Parameter `ModMimeUsePathInfo` an, dass `mod_mime` den Inhaltstypen anhand der *vollständigen* URL bestimmen wird. Das bedeutet, dass auf virtuelle Ressourcen anhand ihrer Erweiterung Metainformationen angewendet werden.

Das folgende Beispiel aktiviert **ModMimeUsePathInfo**:

**Windows und Unix**

```
...  
<Directory />  
<IfModule disp_apache2.c>  
SetHandler dispatcher-handler  
ModMimeUsePathInfo On  
</IfModule>  
  
Options FollowSymLinks  
AllowOverride None  
</Directory>  
...
```

### Aktivieren der Unterstützung für HTTPS (Unix und Linux) {#enable-support-for-https-unix-and-linux}

Der Dispatcher verwendet OpenSSL, um sichere Kommunikation über HTTP zu implementieren. Starten aus der Dispatcher-Version **4.2.0**, OpenSSL 1.0.0 und OpenSSL 1.0.1 werden unterstützt. Der Dispatcher verwendet standardmäßig OpenSSL 1.0.0. Wenden Sie zur Nutzung von OpenSSL 1.0.1 das folgende Verfahren an, um symbolische Verknüpfungen zu erstellen, sodass der Dispatcher die installierten OpenSSL-Bibliotheken verwendet.

1. Öffnen Sie ein Terminal und ändern Sie das aktuelle Verzeichnis in den Ordner, in dem die OpenSSL-Bibliotheken installiert sind, z. B.:

   ```shell
   cd /usr/lib64
   ```

1. Geben Sie die folgenden Befehle ein, um die symbolischen Verknüpfungen zu erstellen:

   ```shell
   ln -s libssl.so libssl.so.1.0.1
   ln -s libcrypto.so libcrypto.so.1.0.1
   ```

>[!NOTE]
>
>Wenn Sie eine angepasste Version von Apache verwenden, stellen Sie sicher, dass Apache und Dispatcher mit derselben Version von [OpenSSL](https://www.openssl.org/source/).

### Nächste Schritte {#next-steps-1}

Bevor Sie mit der Verwendung des Dispatchers beginnen können, müssen Sie Folgendes durchführen:

* [Konfiguration](dispatcher-configuration.md) des Dispatchers
* [Konfiguration von AEM](page-invalidate.md) zur Verwendung mit dem Dispatcher

## Sun Java System Web Server/iPlanet {#sun-java-system-web-server-iplanet}

>[!NOTE]
>
>Anweisungen für Windows- und Unix-Umgebungen finden Sie hier.
>
>Seien Sie vorsichtig bei der Auswahl, welche ausgeführt werden soll.

### Sun Java System Web Server/iPlanet – Installieren des Webservers  {#sun-java-system-web-server-iplanet-installing-your-web-server}

Umfassende Informationen zur Installation dieser Webserver finden Sie in der jeweiligen Dokumentation:

* Sun Java System Web Server
* iPlanet-Webserver

### Sun Java System Web Server/iPlanet – Hinzufügen des Dispatcher-Moduls  {#sun-java-system-web-server-iplanet-add-the-dispatcher-module}

Der Dispatcher umfasst Folgendes:

* **Windows**: eine Dynamic Link Library (DLL)
* **Unix**: ein Dynamic Shared Object (DSO)

Die Installationsarchivdateien enthalten die folgenden Dateien - abhängig davon, ob Sie Windows oder Unix ausgewählt haben:

| File | Beschreibung |
|---|---|
| `disp_ns.dll` | Windows: die DLL-Datei des Dispatchers. |
| `dispatcher.so` | Unix: die DSO-Datei des Dispatchers. |
| `dispatcher.so` | Unix: ein Beispiellink. |
| `obj.conf.disp` | Eine Beispielkonfigurationsdatei für den iPlanet-/Sun Java-System-Webserver. |
| `dispatcher.any` | Eine Beispielkonfigurationsdatei für den Dispatcher. |
| README | Datei mit Installationsanweisungen und aktuellen Informationen. Hinweis: Lesen Sie die Informationen in dieser Datei, bevor Sie mit der Installation beginnen. |
| ÄNDERUNGEN | Änderungsdatei, in der Probleme aufgelistet werden, die in aktuellen und früheren Versionen behoben wurden. |

Führen Sie die folgenden Schritte aus, um den Dispatcher Ihrem Webserver hinzuzufügen:

1. Platzieren Sie die Dispatcher-Datei im Verzeichnis `plugin` des Webservers:

### Sun Java System Web Server/iPlanet - Konfigurieren für den Dispatcher {#sun-java-system-web-server-iplanet-configure-for-the-dispatcher}

Der Webserver muss mithilfe von `obj.conf` konfiguriert werden. Im Dispatcher Installation Kit finden Sie eine Beispielskonfigurationsdatei mit dem Namen `obj.conf.disp`.

1. Navigieren Sie zu `<WEBSERVER_ROOT>/config`.
1. Öffnen Sie `obj.conf` zur Bearbeitung.
1. Kopieren Sie die wie folgt beginnende Zeile\
   `Service fn="dispService"`\
   aus `obj.conf.disp` in den Initialisierungsabschnitt von `obj.conf`.

1. Speichern Sie die Änderungen.
1. Öffnen Sie `magnus.conf` zur Bearbeitung.
1. Kopieren Sie die wie folgt beginnenden Zeilen\
   `Init funcs="dispService, dispInit"`\
   und\
   `Init fn="dispInit"`\
   aus `obj.conf.disp` in den Initialisierungsabschnitt von `magnus.conf`.

1. Speichern Sie die Änderungen.

>[!NOTE]
>
>Die folgenden Konfigurationen sollten sich alle in einer Zeile befinden. `$(SERVER_ROOT)` und `$(PRODUCT_SUBDIR)` müssen durch die entsprechenden Werte ersetzt werden.

**Init**

In der folgenden Tabelle sind Beispiele aufgeführt, die verwendet werden können. Die genauen Einträge hängen von Ihrem spezifischen Webserver ab:

**Windows und Unix**

```
...  
Init funcs="dispService,dispInit" fn="load-modules" shlib="$(SERVER\_ROOT)/plugins/dispatcher.so"  
Init fn="dispInit" config="$(PRODUCT\_SUBDIR)/dispatcher.any" loglevel="1" logfile="$(PRODUCT\_SUBDIR)/logs/dispatcher.log"  
keepalivetimeout="60"  
...
```

Hierbei gilt:

| Parameter | Beschreibung |
|--- |--- |
| config | Speicherort und Name der Konfigurationsdatei `dispatcher.any.` |
| logfile | Speicherort und Name der Protokolldatei. |
| loglevel | Protokollebene für beim Schreiben von Nachrichten in die Protokolldatei: <br/>**0** Fehler <br/>**1** Warnungen <br/>**2** Informationen <br/>**3** Debuggen <br/>**Hinweis:** Es wird empfohlen, die Protokollebene während der Installation und Tests auf 3 festzulegen und während der Ausführung in einer Produktionsumgebung auf 0 zu setzen. |
| keepalivetimeout | Gibt den Keep-Alive-Timeout in Sekunden an. Ab Dispatcher-Version 4.2.0 beträgt der Standardwert für die Keep-Alive-Funktion 60. Wenn der Wert 0 lautet, wird der Keep-Alive-Timeout deaktiviert. |

Je nach Ihren Anforderungen können Sie den Dispatcher als Dienst für Ihre Objekte definieren. Um den Dispatcher für die gesamte Website zu konfigurieren, ändern Sie das Standardobjekt:


**Windows**

```
...  
NameTrans fn="document-root" root="$(PRODUCT\_SUBDIR)\\dispcache"  
...  
Service fn="dispService" method="(GET|HEAD|POST)" type="\*\\\*"  
...
```

**Unix**

```
...  
NameTrans fn="document-root" root="$(PRODUCT\_SUBDIR)/dispcache"  
...  
Service fn="dispService" method="(GET|HEAD|POST)" type="\*/\*"  
...
```

### Nächste Schritte {#next-steps-2}

Bevor Sie mit der Verwendung des Dispatchers beginnen können, müssen Sie Folgendes durchführen:

* [Konfiguration](dispatcher-configuration.md) des Dispatchers
* [Konfiguration von AEM](page-invalidate.md) zur Verwendung mit dem Dispatcher
