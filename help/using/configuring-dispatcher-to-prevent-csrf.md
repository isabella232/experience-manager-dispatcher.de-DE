---
title: Konfigurieren des Dispatchers zum Verhindern von CSRF-Angriffen
seo-title: Adobe AEM Dispatcher konfigurieren, um CSRF-Angriffe zu verhindern
description: Erfahren Sie, wie Sie AEM Dispatcher konfigurieren, um Cross-Site Request Forgery-Angriffe zu verhindern.
seo-description: Erfahren Sie, wie Sie Adobe AEM Dispatcher konfigurieren, um Cross-Site Request Forgery-Angriffe zu verhindern.
uuid: f 290 bdeb -54 e 2-4649-b 0 fc -6257 b 422 af 2 d
topic-tags: dispatcher
content-type: Referenz
discoiquuid: d 61 d 021 e-b 338-4 a 1 d -91 ee -55427557 e 931
translation-type: tm+mt
source-git-commit: f35c79b487454059062aca6a7c989d5ab2afaf7b

---


# Konfigurieren des Dispatchers zum Verhindern von CSRF-Angriffen{#configuring-dispatcher-to-prevent-csrf-attacks}

AEM bietet ein Framework, mit dem CSRF-Angriffe (Cross Site Request Forgery) verhindert werden können. Um dieses Framework ordnungsgemäß nutzen zu können, müssen Sie die folgenden Änderungen an Ihrer Dispatcher-Konfiguration vornehmen:

>[!NOTE]
>
>Aktualisieren Sie unbedingt die Regelnummern in den unten aufgeführten Beispielen anhand Ihrer bestehenden Konfiguration. Beachten Sie, dass der Dispatcher die letzte zutreffende Regel verwendet, um eine Genehmigung zu erteilen oder zu verweigern. Platzieren Sie die Regeln entsprechend nahe dem Ende Ihrer vorhandenen Liste.

1. Fügen Sie im `/clientheaders` Abschnitt Ihres Autors-farm. any und publish-farm. any den folgenden Eintrag am Ende der Liste hinzu:\
   `CSRF-Token`
1. Fügen Sie im Abschnitt /filters Ihrer `author-farm.any` und `publish-farm.any` oder `publish-filters.any` Datei die folgende Zeile hinzu, um Anforderungen `/libs/granite/csrf/token.json` über den Dispatcher zuzulassen.\
   `/0999 { /type "allow" /glob " * /libs/granite/csrf/token.json*" }`
1. Fügen Sie unter dem `/cache /rules` Abschnitt eine `publish-farm.any`Regel hinzu, um den Dispatcher von der Zwischenspeicherung der `token.json` Datei zu blockieren. Normalerweise umgehen Autoren die Zwischenspeicherung, sodass Sie die Regel nicht zu Ihrer `author-farm.any`hinzufügen müssen.\
   `/0999 { /glob "/libs/granite/csrf/token.json" /type "deny" }`

Um zu überprüfen, ob die Konfiguration korrekt ist, sehen Sie sich die Datei „dispatcher.log“ im Debugmodus an und stellen Sie sicher, dass die Datei „token.json“ weder zwischengespeichert noch von Filtern blockiert wird. Meldungen sollten ähnlich wie:\
`... checking [/libs/granite/csrf/token.json]  `
`... request URL not in cache rules: /libs/granite/csrf/token.json`\
`... cache-action for [/libs/granite/csrf/token.json]: NONE`

Sie können auch überprüfen, dass die Anforderungen in Ihrer Apache `access_log`erfolgreich sind. Anforderungen für &quot;/libs/granite/csrf/token. json&quot; geben einen HTTP 200-Statuscode zurück.
