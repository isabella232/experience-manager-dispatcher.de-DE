---
title: Konfigurieren des Dispatchers zum Verhindern von CSRF-Angriffen
seo-title: Configuring Adobe AEM Dispatcher to Prevent CSRF Attacks
description: Erfahren Sie, wie Sie AEM Dispatcher konfigurieren, um CSRF-Angriffe (Cross-Site Request Forgery) zu verhindern.
seo-description: Learn how to configure Adobe AEM Dispatcher to prevent Cross-Site Request Forgery attacks.
uuid: f290bdeb-54e2-4649-b0fc-6257b422af2d
topic-tags: dispatcher
content-type: reference
discoiquuid: d61d021e-b338-4a1d-91ee-55427557e931
exl-id: bcd38878-f977-46a6-b01a-03e4d90aef01
source-git-commit: 3a0e237278079a3885e527d7f86989f8ac91e09d
workflow-type: ht
source-wordcount: '227'
ht-degree: 100%

---

# Konfigurieren des Dispatchers zum Verhindern von CSRF-Angriffen{#configuring-dispatcher-to-prevent-csrf-attacks}

AEM bietet ein Framework, mit dem CSRF-Angriffe (Cross-Site Request Forgery) verhindert werden können. Um dieses Framework richtig nutzen zu können, müssen Sie die folgenden Änderungen an Ihrer Dispatcher-Konfiguration vornehmen:

>[!NOTE]
>
>Aktualisieren Sie unbedingt die Regelnummern in den unten aufgeführten Beispielen gemäß Ihrer bestehenden Konfiguration. Denken Sie daran, dass der Dispatcher die letzte zutreffende Regel verwendet, um eine Genehmigung zu erteilen oder zu verweigern. Platzieren Sie die Regeln daher im unteren Bereich Ihrer vorhandenen Liste.

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
