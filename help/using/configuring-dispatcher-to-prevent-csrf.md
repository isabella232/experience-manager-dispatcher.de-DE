---
title: Konfigurieren des Dispatchers zum Verhindern von CSRF-Angriffen
seo-title: Konfigurieren von AEM Dispatcher zum Verhindern von CSRF-Angriffen
description: Erfahren Sie, wie Sie AEM Dispatcher konfigurieren, um Cross-Site Request Forgery-Angriffe zu verhindern.
seo-description: Erfahren Sie, wie Sie Adobe AEM Dispatcher konfigurieren, um Cross-Site Request Forgery-Angriffe zu verhindern.
uuid: f290bdeb-54e2-4649-b0fc-6257b422af2d
topic-tags: dispatcher
content-type: reference
discoiquuid: d61d021e-b338-4a1d-91ee-55427557e931
translation-type: tm+mt
source-git-commit: 69edbe7608b46c93d238515e4223606eadad0ac4
workflow-type: tm+mt
source-wordcount: '246'
ht-degree: 100%

---


# Konfigurieren des Dispatchers zum Verhindern von CSRF-Angriffen{#configuring-dispatcher-to-prevent-csrf-attacks}

AEM bietet ein Framework, mit dem CSRF-Angriffe (Cross-Site Request Forgery) verhindert werden können. Um dieses Framework ordnungsgemäß nutzen zu können, müssen Sie die folgenden Änderungen an Ihrer Dispatcher-Konfiguration vornehmen:

>[!NOTE]
>
>Aktualisieren Sie unbedingt die Regelnummern in den unten aufgeführten Beispielen anhand Ihrer bestehenden Konfiguration. Beachten Sie, dass der Dispatcher die letzte zutreffende Regel verwendet, um eine Genehmigung zu erteilen oder zu verweigern. Platzieren Sie die Regeln entsprechend nahe dem Ende Ihrer vorhandenen Liste.

1. Fügen Sie in den Dateien „author-farm.any“ und „publish-farm.any“ im Abschnitt `/clientheaders` am Ende der Liste den folgenden Eintrag hinzu:\
   `CSRF-Token`
1. Fügen Sie in den Dateien `author-farm.any` und `publish-farm.any` oder `publish-filters.any` im Abschnitt „/filter“ die folgende Zeile hinzu, um Anfragen für `/libs/granite/csrf/token.json` durch den Dispatcher zuzulassen.\
   `/0999 { /type "allow" /glob " * /libs/granite/csrf/token.json*" }`
1. Fügen Sie in der Datei `publish-farm.any` unter dem Abschnitt `/cache /rules` eine Regel hinzu, um den Dispatcher daran zu hindern, die Datei `token.json` zwischenzuspeichern. Meist umgehen Autoren das Zwischenspeichern. In `author-farm.any` muss die Regel somit typischerweise nicht eingefügt werden.\
   `/0999 { /glob "/libs/granite/csrf/token.json" /type "deny" }`

Um zu überprüfen, ob die Konfiguration korrekt ist, sehen Sie sich die Datei „dispatcher.log“ im Debugmodus an und stellen Sie sicher, dass die Datei „token.json“ weder zwischengespeichert noch von Filtern blockiert wird. Sie sollten Meldungen erhalten, die dieser ähneln:\
`... checking [/libs/granite/csrf/token.json]  `
`... request URL not in cache rules: /libs/granite/csrf/token.json`\
`... cache-action for [/libs/granite/csrf/token.json]: NONE`

Im `access_log` von Apache kann zudem überprüft werden, ob Anfragen erfolgreich sind. Anfragen für „/libs/granite/csrf/token.json“ sollten einen HTTP-Statuscode 200 zurückgeben.
