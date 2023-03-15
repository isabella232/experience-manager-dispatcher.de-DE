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
workflow-type: tm+mt
source-wordcount: '560'
ht-degree: 43%

---

# Beheben von Problemen beim Dispatcher {#troubleshooting-dispatcher-problems}

>[!NOTE]
>
>Dispatcher-Versionen sind unabhängig von AEM, die Dispatcher-Dokumentation ist jedoch in die AEM-Dokumentation eingebettet. Verwenden Sie immer die Dispatcher-Dokumentation, die in der Dokumentation für die neueste Version von AEM eingebettet ist.
>
>Möglicherweise wurden Sie von der Dispatcher-Dokumentation zu einer früheren AEM-Version zu dieser Seite weitergeleitet.

>[!NOTE]
>
>Überprüfen Sie die [Dispatcher-Wissensdatenbank](https://helpx.adobe.com/experience-manager/kb/index/dispatcher.html), [Beheben von Problemen beim Leeren des Dispatchers](https://experienceleague.adobe.com/search.html?lang=en#q=troubleshooting%20dispatcher%20flushing%20issues&amp;sort=relevancy&amp;f:el_product=[Experience%20Manager]) und [Häufig gestellte Fragen zu Dispatcher - Häufige Probleme](dispatcher-faq.md) für weitere Informationen.

## Überprüfen der Basiskonfiguration {#check-the-basic-configuration}

Wie immer müssen zunächst die Grundlagen überprüft werden:

* [Sicherstellen der grundlegenden Funktion](/help/using/dispatcher-configuration.md#confirming-basic-operation)
* Überprüfen Sie alle Protokolldateien für Ihren Webserver und Dispatcher. Erhöhen Sie bei Bedarf die `loglevel` wird für den Dispatcher verwendet [Protokollierung](/help/using/dispatcher-configuration.md#logging).

* [Überprüfen der Konfiguration](/help/using/dispatcher-configuration.md):

   * Haben Sie mehrere Dispatcher?

      * Haben Sie ermittelt, welcher Dispatcher die Website/Seite verarbeitet, die Sie prüfen?
   * Haben Sie Filter implementiert?

      * Beeinflusst diese Filter die Angelegenheit, die Sie untersuchen?


## IIS-Diagnosewerkzeuge  {#iis-diagnostic-tools}

IIS bietet verschiedene Werkzeuge für die Ablaufverfolgung, abhängig von der jeweiligen Version:

* IIS 6: Die IIS-Diagnosewerkzeuge können heruntergeladen und konfiguriert werden.
* IIS 7: Die Ablaufverfolgung ist vollständig integriert.

Mithilfe dieser Tools können Sie Aktivitäten überwachen.

## IIS und 404 (Nicht gefunden)  {#iis-and-not-found}

Bei Verwendung von IIS kann es vorkommen, dass `404 Not Found` in verschiedenen Szenarien zurückgegeben werden. Wenn dies der Fall ist, finden Sie weitere Informationen in den folgenden Knowledge Base-Artikeln. 

* [IIS 6/7: POST-Methode gibt 404 zurück](https://helpx.adobe.com/experience-manager/kb/IIS6IsapiFilters.html)
* [IIS 6: Anforderungen an URLs, die den Basispfad enthalten `/bin` eine `404 Not Found`](https://helpx.adobe.com/experience-manager/kb/RequestsToBinDirectoryFailInIIS6.html)

Überprüfen Sie außerdem, ob der Cache-Stammordner des Dispatchers und der IIS-Dokumentenstamm auf denselben Ordner festgelegt sind.

## Probleme beim Löschen von Workflow-Modellen {#problems-deleting-workflow-models}

**Symptome** 

Beim Löschen von Workflow-Modellen beim Zugriff auf eine AEM-Autoreninstanz durch den Dispatcher treten Probleme auf.

**Zu reproduzierende Schritte:**

1. Melden Sie sich bei Ihrer Autoreninstanz an (überprüfen Sie, ob Anforderungen über den Dispatcher weitergeleitet werden).
1. Workflow erstellen; z. B. mit dem Titel auf workflowToDelete festgelegt ist.
1. Vergewissern Sie sich, dass der Workflow erfolgreich erstellt wurde.
1. Wählen Sie den Workflow aus, klicken Sie mit der rechten Maustaste darauf und klicken Sie auf **Löschen**.

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

Dieser Vorgang beschreibt, wie der Dispatcher mit `mod_dir` innerhalb des Apache-Webservers, da dies zu verschiedenen, möglicherweise unerwarteten Auswirkungen führen kann:

### Apache 1.3 {#apache}

In Apache 1.3 `mod_dir` verarbeitet alle Anfragen, bei denen die URL einem Ordner im Dateisystem zugeordnet ist.

Es wird eine der folgenden Aktionen ausgeführt:

* Die Anfrage wird an eine vorhandene `index.html`-Datei weitergeleitet.
* Es wird eine Verzeichnisliste erstellt.

Wenn der Dispatcher aktiviert ist, verarbeitet er solche Anforderungen, indem er sich selbst als Handler für den Inhaltstyp registriert `httpd/unix-directory`.

### Apache 2.x {#apache-x}

In Apache 2.x sind die Dinge anders. Ein Modul kann die verschiedenen Phasen der Anfrage behandeln, z. B. URL-Korrekturen. Die `mod_dir` verarbeitet diese Phase, indem eine Anforderung (wenn die URL einem Verzeichnis zugeordnet ist) an die URL mit einer `/` angefügt.

Der Dispatcher fängt die `mod_dir` Korrektur, aber die Anfrage an die umgeleitete URL vollständig verarbeitet (d. h. mit `/` angehängt). Dieser Prozess kann ein Problem darstellen, wenn der Remote-Server (z. B. AEM) Anfragen an `/a_path` anders als Anforderungen an `/a_path/` (when `/a_path` in ein vorhandenes Verzeichnis verweist).

In diesem Fall müssen Sie entweder:

* disable `mod_dir` für `Directory` oder `Location` vom Dispatcher verarbeitete Unterstruktur

* Verwenden Sie `DirectorySlash Off`, um `mod_dir` so zu konfigurieren, dass `/` nicht angefügt wird.
