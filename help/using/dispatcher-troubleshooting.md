---
title: Beheben von Problemen beim Dispatcher
seo-title: Beheben von Problemen bei AEM Dispatcher
description: Erfahren Sie mehr zur Fehlerbehebung bei Dispatcher-Problemen.
seo-description: Erfahren Sie mehr zur Fehlerbehebung bei AEM Dispatcher-Problemen.
uuid: 9c109a48-d921-4b6e-9626-1158cebc41e7
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: 1193211344162
template: /apps/docs/templates/contentpage
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: a612e745-f1e6-43de-b25a-9adcaadab5cf
translation-type: tm+mt
source-git-commit: 9af0dc22d32f1176b84c28a70b1a4701414d434e
workflow-type: tm+mt
source-wordcount: '553'
ht-degree: 90%

---


# Beheben von Problemen beim Dispatcher {#troubleshooting-dispatcher-problems}

>[!NOTE]
>
>Dispatcher-Versionen sind unabhängig von AEM, die Dispatcher-Dokumentation ist jedoch in die AEM-Dokumentation eingebettet. Verwenden Sie immer die Dispatcher-Dokumentation, die in der Dokumentation für die neueste Version von AEM eingebettet ist.
>
>Möglicherweise wurden Sie von der Dispatcher-Dokumentation zu einer früheren AEM-Version zu dieser Seite weitergeleitet.

>[!NOTE]
>
>Weitere Informationen finden Sie in den Abschnitten [Wissensdatenbank für Dispatcher](https://helpx.adobe.com/cq/kb/index/dispatcher.html), [Fehlerbehebung bei Problemen mit dem Flushing von Dispatcher](https://helpx.adobe.com/adobe-cq/kb/troubleshooting-dispatcher-flushing-issues.html) und [Häufig gestellte Fragen zu Dispatcher-Problemen](dispatcher-faq.md).

## Überprüfen der Basiskonfiguration {#check-the-basic-configuration}

Wie immer müssen zunächst die Grundlagen überprüft werden:

* [Sicherstellen der grundlegenden Funktion](/help/using/dispatcher-configuration.md#confirming-basic-operation)
* Überprüfen Sie alle Protokolldateien für den Webserver und den Dispatcher. Erhöhen Sie ggf. die `loglevel` für die Dispatcher-[Protokollierung](/help/using/dispatcher-configuration.md#logging).

* [Überprüfen der Konfiguration](/help/using/dispatcher-configuration.md):

   * Haben Sie mehrere Dispatcher?

      * Haben Sie ermittelt, welcher Dispatcher die Website/Seite verarbeitet, die Sie prüfen?
   * Haben Sie Filter implementiert?

      * Haben diese Auswirkungen auf den Aspekt, den Sie prüfen?


## IIS-Diagnosewerkzeuge  {#iis-diagnostic-tools}

IIS bietet verschiedene Werkzeuge für die Ablaufverfolgung, abhängig von der jeweiligen Version:

* IIS 6: Die IIS-Diagnosewerkzeuge können heruntergeladen und konfiguriert werden.
* IIS 7: Die Ablaufverfolgung ist vollständig integriert.

Hiermit können Sie die Aktivität überwachen.

## IIS und 404 (Nicht gefunden)   {#iis-and-not-found}

Wenn Sie IIS verwenden, wird möglicherweise in verschiedenen Situationen `404 Not Found` zurückgegeben. Wenn dies der Fall ist, finden Sie weitere Informationen in den folgenden Knowledge Base-Artikeln. 

* [IIS 6/7: POST-Methode gibt 404 zurück](https://helpx.adobe.com/dispatcher/kb/IIS6IsapiFilters.html)
* [IIS 6: Anforderungen an URLs, die den Basispfad enthalten,  `/bin` geben  `404 Not Found`](https://helpx.adobe.com/dispatcher/kb/RequestsToBinDirectoryFailInIIS6.html)

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

Fügen Sie die folgenden Header im Abschnitt `/clientheaders` der Datei `dispatcher.any` hinzu:

* `x-http-method-override`
* `x-requested-with`

```
{  
{  
/clientheaders  
{  
...  
"x-http-method-override"  
"x-requested-with"  
}
```

## Wechselwirkung mit „mod_dir“ (Apache) {#interference-with-mod-dir-apache}

Im Folgenden wird beschrieben, wie der Dispatcher mit `mod_dir` im Apache-Webserver interagiert, da dies zu verschiedenen, möglicherweise unerwarteten Auswirkungen führen kann:

### Apache 1.3 {#apache}

In Apache 1.3 verarbeitet `mod_dir` alle Anforderungen, bei denen die URL auf einen Ordner im Dateisystem verweist.

Es wird eine der folgenden Aktionen ausgeführt:

* Die Anfrage wird an eine vorhandene `index.html`-Datei weitergeleitet.
* Es wird eine Verzeichnisliste erstellt.

Wenn der Dispatcher aktiviert ist, verarbeitet er solche Anforderungen, indem er sich selbst als Handler für den Inhaltstyp `httpd/unix-directory` registriert.

### Apache 2.x {#apache-x}

In Apache 2.x unterscheiden sich die Abläufe. Ein Modul kann die verschiedenen Phasen der Anfrage behandeln, z. B. URL-Korrekturen. `mod_dir` verarbeitet diese Phase, indem eine Anfrage (wenn die URL einem Ordner zugeordnet ist) an die URL mit angefügtem `/` weitergeleitet wird.

Der Dispatcher fängt die `mod_dir`-Korrektur nicht ab, sondern behandelt die Anfrage an die umgeleitete URL (d. h. mit angehängtem `/`) vollständig. Dies kann ein Problem darstellen, wenn der Remoteserver (z. B. AEM) Anfragen an `/a_path` anders als Anfragen an `/a_path/` behandelt (wenn `/a_path` einem vorhandenen Ordner zugeordnet ist).

Wenn dies der Fall ist, müssen Sie einen der beiden folgenden Schritte ausführen:

* Deaktivieren Sie `mod_dir` für die Unterstruktur `Directory` oder `Location`, die vom Dispatcher bearbeitet wird.

* Verwenden Sie `DirectorySlash Off`, um `mod_dir` so zu konfigurieren, dass `/` nicht angefügt wird.
