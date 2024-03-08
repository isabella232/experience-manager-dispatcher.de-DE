---
title: Beheben von Problemen beim Dispatcher
seo-title: Troubleshooting AEM Dispatcher Problems
description: Erfahren Sie mehr zur Fehlerbehebung bei Dispatcher-Problemen.
seo-description: Learn to troubleshoot AEM Dispatcher issues.
uuid: 9c109a48-d921-4b6e-9626-1158cebc41e7
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: 1193211344162
template: /apps/docs/templates/contentpage
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: a612e745-f1e6-43de-b25a-9adcaadab5cf
exl-id: 29f338ab-5d25-48a4-9309-058e0cc94cff
source-git-commit: 26c8edbb142297830c7c8bd068502263c9f0e7eb
workflow-type: ht
source-wordcount: '538'
ht-degree: 100%

---

# Beheben von Problemen beim Dispatcher {#troubleshooting-dispatcher-problems}

>[!NOTE]
>
>Dispatcher-Versionen sind AEM-unabhängig, die Dispatcher-Dokumentation ist jedoch in die AEM-Dokumentation eingebettet. Verwenden Sie immer die Dispatcher-Dokumentation, die in der Dokumentation für die neueste Version von AEM eingebettet ist.
>
>Sie wurden möglicherweise zu dieser Seite umgeleitet, wenn Sie einem Link zur Dispatcher-Dokumentation gefolgt sind, der in der Dokumentation für eine frühere AEM-Version eingebettet ist.

>[!NOTE]
>
>Weitere Informationen erhalten Sie auch in der [Dispatcher-Wissensdatenbank](https://helpx.adobe.com/de/experience-manager/kb/index/dispatcher.html), unter [Fehlerbehebung bei Problemen mit dem Leeren des Dispatchers](https://experienceleague.adobe.com/search.html?lang=en#q=troubleshooting%20dispatcher%20flushing%20issues&amp;sort=relevancy&amp;lang=de&amp;f:el_product=[Experience%20Manager]) sowie in den [am häufigsten gestellten Fragen zu Dispatcher-Problemen](dispatcher-faq.md).

## Überprüfen der Basiskonfiguration {#check-the-basic-configuration}

Wie immer müssen zunächst die Grundlagen überprüft werden:

* [Sicherstellen der grundlegenden Funktion](/help/using/dispatcher-configuration.md#confirming-basic-operation)
* Überprüfen Sie alle Protokolldateien für den Webserver und den Dispatcher. Erhöhen Sie ggf. das `loglevel` für die Dispatcher-[Protokollierung](/help/using/dispatcher-configuration.md#logging).

* [Überprüfen der Konfiguration](/help/using/dispatcher-configuration.md):

   * Haben Sie mehrere Dispatcher?

      * Haben Sie ermittelt, welcher Dispatcher die Website/Seite verarbeitet, die Sie prüfen?

   * Haben Sie Filter implementiert?

      * Haben diese Filter Auswirkungen auf den Aspekt, den Sie prüfen?

## IIS-Diagnosewerkzeuge {#iis-diagnostic-tools}

IIS bietet verschiedene Werkzeuge für die Ablaufverfolgung, abhängig von der jeweiligen Version:

* IIS 6: Die IIS-Diagnosewerkzeuge können heruntergeladen und konfiguriert werden.
* IIS 7: Die Ablaufverfolgung ist vollständig integriert.

Mit diesen Tools können Sie die Aktivität überwachen.

## IIS und 404 (Nicht gefunden) {#iis-and-not-found}

Wenn Sie IIS verwenden, wird möglicherweise in verschiedenen Situationen `404 Not Found` zurückgegeben. Wenn dies der Fall ist, finden Sie weitere Informationen in den folgenden Knowledgebase-Artikeln.

* [IIS 6/7: POST-Methode gibt 404 zurück](https://helpx.adobe.com/de/experience-manager/kb/IIS6IsapiFilters.html)
* [IIS 6: Anfragen an URLs mit Basispfad `/bin` geben `404 Not Found`](https://helpx.adobe.com/de/experience-manager/kb/RequestsToBinDirectoryFailInIIS6.html) zurück

Überprüfen Sie außerdem, ob das Cache-Stammverzeichnis des Dispatchers und das IIS-Basisverzeichnis auf denselben Ordner festgelegt wurden.

## Probleme beim Löschen von Workflow-Modellen {#problems-deleting-workflow-models}

**Symptome**

Beim Löschen von Workflow-Modellen beim Zugriff auf eine AEM-Autoreninstanz durch den Dispatcher treten Probleme auf.

**Zu reproduzierende Schritte:**

1. Melden Sie sich bei der Autoreninstanz an (um zu bestätigen, dass Anfragen über den Dispatcher weitergeleitet werden).
1. Erstellen Sie einen Workflow, z. B. mit dem Titel „workflowToDelete“.
1. Vergewissern Sie sich, dass der Workflow erfolgreich erstellt wurde.
1. Wählen Sie den Workflow aus, klicken Sie mit der rechten Maustaste darauf und klicken Sie dann auf **Löschen**.

1. Klicken Sie zur Bestätigung auf **Ja**.
1. Es wird ein Fehlermeldungsfeld mit folgenden Informationen angezeigt:\
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

Dieser Prozess beschreibt, wie der Dispatcher mit `mod_dir` im Apache-Webserver interagiert, da dies zu verschiedenen, möglicherweise unerwarteten Auswirkungen führen kann:

### Apache 1.3 {#apache}

In Apache 1.3 verarbeitet `mod_dir` alle Anfragen, bei denen die URL auf ein Verzeichnis im Dateisystem verweist.

Es wird eine der folgenden Aktionen ausgeführt:

* Die Anfrage wird an eine vorhandene `index.html`-Datei weitergeleitet.
* Es wird eine Verzeichnisliste erstellt.

Wenn der Dispatcher aktiviert ist, verarbeitet er solche Anfragen, indem er sich selbst als Handler für den Inhaltstyp `httpd/unix-directory` registriert.

### Apache 2.x {#apache-x}

In Apache 2.x sind die Dinge anders. Ein Modul kann die verschiedenen Phasen der Anfrage behandeln, z. B. URL-Korrekturen. `mod_dir` verarbeitet diese Phase, indem eine Anfrage (wenn die URL einem Ordner zugeordnet ist) an die URL mit angefügtem `/` weitergeleitet wird.

Der Dispatcher fängt die `mod_dir`-Korrektur nicht ab, sondern verarbeitet die Anfrage an die umgeleitete URL (d. h. mit angehängtem `/`) vollständig. Dieser Prozess kann ein Problem darstellen, wenn der Remote-Server (z. B. AEM) Anfragen an `/a_path` anders als Anfragen an `/a_path/` behandelt (wenn `/a_path` einem vorhandenen Ordner zugeordnet ist).

Wenn dies der Fall ist, müssen Sie einen der beiden folgenden Schritte ausführen:

* Deaktivieren Sie `mod_dir` für die Unterstruktur `Directory` oder `Location`, die vom Dispatcher verarbeitet wird.

* Verwenden Sie `DirectorySlash Off`, um `mod_dir` so zu konfigurieren, dass `/` nicht angefügt wird.
