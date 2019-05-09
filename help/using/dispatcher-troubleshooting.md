---
title: Beheben von Problemen beim Dispatcher
seo-title: Fehlerbehebung für AEM Dispatcher-Probleme
description: Erfahren Sie mehr zur Fehlerbehebung bei Dispatcher-Problemen.
seo-description: Erfahren Sie mehr zur Fehlerbehebung bei AEM Dispatcher-Problemen.
uuid: 9 c 109 a 48-d 921-4 b 6 e -9626-1158 cebc 41 e 7
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: '1193211344162'
template: /apps/docs/templates/contentpage
contentOwner: Benutzer
products: SG_ EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: Referenz
discoiquuid: a 612 e 745-f 1 e 6-43 de-b 25 a -9 adcaadab 5 cf
translation-type: tm+mt
source-git-commit: 76cffbfb616cd5601aed36b7076f67a2faf3ed3b

---


# Beheben von Problemen beim Dispatcher {#troubleshooting-dispatcher-problems}

>[!NOTE]
>
>Dispatcher-Versionen sind unabhängig von AEM, die Dispatcher-Dokumentation ist jedoch in die AEM-Dokumentation eingebettet. Verwenden Sie immer die Dispatcher-Dokumentation, die in der Dokumentation für die neueste Version von AEM eingebettet ist.
>
>Möglicherweise wurden Sie von der Dispatcher-Dokumentation zu einer früheren AEM-Version zu dieser Seite weitergeleitet.

>[!NOTE]
>
>Weitere Informationen finden Sie auch im [Abschnitt Dispatcher Knowledge Base](https://helpx.adobe.com/cq/kb/index/dispatcher.html), [Fehlerbehebung bei](https://helpx.adobe.com/adobe-cq/kb/troubleshooting-dispatcher-flushing-issues.html) Dispatcher-Bereinigungsproblemen [und häufig](dispatcher-faq.md) gestellte Fragen zu Dispatcher.

## Überprüfen der Basiskonfiguration {#check-the-basic-configuration}

Wie immer müssen zunächst die Grundlagen überprüft werden:

* [Sicherstellen der grundlegenden Funktion](#ConfirmBasicOperation)
* Überprüfen Sie alle Protokolldateien für den Webserver und den Dispatcher. Erhöhen Sie gegebenenfalls die `loglevel` Verwendung für die dispatcher [-Protokollierung](#Logging).

* [Überprüfen der Konfiguration](#ConfiguringtheDispatcher):

   * Haben Sie mehrere Dispatcher?

      * Haben Sie ermittelt, welcher Dispatcher die Website/Seite verarbeitet, die Sie prüfen?
   * Haben Sie Filter implementiert?

      * Haben diese Auswirkungen auf den Aspekt, den Sie prüfen?


## IIS-Diagnosewerkzeuge {#iis-diagnostic-tools}

IIS bietet verschiedene Werkzeuge für die Ablaufverfolgung, abhängig von der jeweiligen Version:

* IIS 6: Die IIS-Diagnosewerkzeuge können heruntergeladen und konfiguriert werden.
* IIS 7: Die Ablaufverfolgung ist vollständig integriert.

Hiermit können Sie die Aktivität überwachen.

## IIS und 404 (Nicht gefunden) {#iis-and-not-found}

Wenn Sie IIS verwenden, wird möglicherweise in verschiedenen Situationen `404 Not Found` zurückgegeben. Wenn dies der Fall ist, finden Sie weitere Informationen in den folgenden Knowledge Base-Artikeln. 

* [IIS 6/7: POST-Methode gibt 404 zurück](https://helpx.adobe.com/dispatcher/kb/IIS6IsapiFilters.html)
* [IIS 6: Anforderungen an urls, die den Basispfad `/bin` -Rücklauf enthalten `404 Not Found`](https://helpx.adobe.com/dispatcher/kb/RequestsToBinDirectoryFailInIIS6.html)

Sie sollten überprüfen, ob der Cache-Stammordner des Dispatchers und der IIS-Dokumentenstamm auf denselben Ordner festgelegt wurden.

## Probleme beim Löschen von Workflow-Modellen {#problems-deleting-workflow-models}

**Symptome** 

Beim Löschen von Workflow-Modellen beim Zugriff auf eine AEM-Autoreninstanz durch den Dispatcher treten Probleme auf.

**Zu reproduzierende Schritte:**

1. Melden Sie sich bei der Autoreninstanz an (um zu bestätigen, dass Anfragen über den Dispatcher weitergeleitet werden).
1. Erstellen Sie einen neuen Workflow, z. B. mit dem Titel „workflowToDelete“.
1. Vergewissern Sie sich, dass der Workflow erfolgreich erstellt wurde.
1. Wählen Sie den Workflow aus, klicken Sie mit der rechten Maustaste darauf und klicken Sie dann auf **Löschen**.

1. Klicken Sie zur Bestätigung auf **Ja**.
1. Es wird ein Fehlermeldungsfeld angezeigt:\
   &quot; `ERROR 'Could not delete workflow model!!`&quot;.

**Problemlösung**

Fügen Sie dem `/clientheaders` Abschnitt Ihrer `dispatcher.any` Datei die folgenden Header hinzu:

* `x-http-method-override`
* `x-requested-with`

`{  
{  
/clientheaders  
{  
...  
"x-http-method-override"  
"x-requested-with"  
}`

## Wechselwirkung mit „mod_dir“ (Apache) {#interference-with-mod-dir-apache}

Im Folgenden wird beschrieben, wie der Dispatcher mit `mod_dir` im Apache-Webserver interagiert, da dies zu verschiedenen, möglicherweise unerwarteten Auswirkungen führen kann:

### Apache 1.3 {#apache}

In Apache 1.3 verarbeitet `mod_dir` alle Anforderungen, bei denen die URL auf einen Ordner im Dateisystem verweist.

Es wird eine der folgenden Aktionen ausgeführt:

* die Anforderung an eine vorhandene `index.html` Datei weiterleiten
* Es wird eine Verzeichnisliste erstellt.

Wenn der Dispatcher aktiviert ist, verarbeitet er solche Anforderungen, indem er sich selbst als Handler für den Inhaltstyp `httpd/unix-directory` registriert.

### Apache 2.x {#apache-x}

In Apache 2.x unterscheiden sich die Abläufe. Ein Modul kann die verschiedenen Phasen der Anfrage behandeln, z. B. URL-Korrekturen. `mod_dir` verarbeitet diese Phase durch Umleitung einer Anforderung (wenn die URL einem Ordner zugeordnet ist) zur URL mit einem `/` angehängten Angehängten.

Dispatcher führt die `mod_dir` Korrektur nicht durch, sondern verarbeitet die Anforderung vollständig an die umgeleitete URL (d. h. durch `/` Angehängt). Dies kann ein Problem darstellen, wenn der Remote-Server (z. B. AEM) Anforderungen an Anforderungen `/a_path` an eine andere weiterleitet (wenn `/a_path/``/a_path` die Zuordnung zu einem vorhandenen Ordner erfolgt).

Wenn dies der Fall ist, müssen Sie einen der beiden folgenden Schritte ausführen:

* für `mod_dir` die `Directory` vom `Location` Dispatcher verwaltete Unterstruktur deaktivieren

* verwenden `DirectorySlash Off` , `mod_dir` um nicht zu hängen `/`
