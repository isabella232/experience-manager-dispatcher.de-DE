---
source-git-commit: 053f90d9a0574c7d470cec6794498919e3340713
workflow-type: ht
source-wordcount: '740'
ht-degree: 100%

---
# Richtlinien für Beiträge zur Adobe Experience Manager-Dokumentation

## Dokumentationsphilosophie

Wir wissen, dass Benutzer von Adobe Experience Manager in extrem wettbewerbsorientierten Umgebungen arbeiten, um digitale Erlebnisse zu erstellen, die sie von ihren Wettbewerbern abheben. Deshalb ist es wichtig, wenn Adobe erweiterte neue Tools in AEM bereitstellt, dass diese Werkzeuge durch genaue und klare Dokumentation ergänzt werden, damit der Kunde sofort seine AEM-Investition nutzen und den ROI maximieren kann.

Das Ziel der AEM-Dokumentation besteht darin, AEM-Benutzer schnellstmöglich mit der Dokumentation vertraut zu machen. Daher steht bei uns eine genaue und nutzbare Dokumentation im Vordergrund und wir bemühen uns ständig um eine aktualisierte und bessere Dokumentation.

## Beiträge zur Dokumentation

Zur stetigen Verbesserung der AEM-Dokumentation ist die gesamte Community von AEM-Benutzern herzlich eingeladen, zur Dokumentation beizutragen. Sei es durch Pull-Anfragen oder Probleme, bei den Verbesserungen der Dokumentation kann es sich um Korrekturen, Klarstellungen, Erweiterungen und weitere Beispiele handeln.

## Dokumentationsstandards

Auch wenn wir Beiträge zu unserer Dokumentation begrüßen, sollte jeder Beitrag zur AEM-Dokumentation in Form einer Pull-Anfrage oder eines Problems mit unseren Beitrags- und Dokumentationsstandards übereinstimmen.

Beiträge, die diesen Standards nicht entsprechen, können abgelehnt werden.

### Wir nutzen für die Dokumentation Standard-Anwendungsfälle.

Die AEM-Dokumentation umfasst Standard-Anwendungsfälle. Anwendungsfälle, die über den Umfang der standardmäßigen Installation und Nutzung des Produkts hinausgehen, sind nicht Teil der AEM-Dokumentation.

### Wir dokumentieren üblicherweise keine Fehler oder ihre Umgehungslösungen.

Die AEM-Dokumentation umfasst Standard-Anwendungsfälle. Aus diesem Grund werden Fehler, durch Fehler verursachte Effekte und Problemumgehungen normalerweise nicht dokumentiert.

Ausnahmen gelten für die Versionshinweise, in denen bekannte Probleme mit möglichen Lösungen aufgelistet werden können, die vom AEM Product Management genehmigt wurden.

### Dokumentationsbeiträge sind nicht zur Beantwortung technischer Fragen geeignet.

Alle Ideen, die Sie möglicherweise zur Verbesserung der AEM-Dokumentation haben, sind als Beiträge willkommen. Kommentare, Probleme und Pull-Anfragen sind jedoch nur für *Beiträge* vorgesehen. Sie sind nicht zur Beantwortung Ihrer Fragen über die Verwendung von AEM, Implementierung Ihres AEM-Projekts oder zur Lösung technischer Probleme gedacht.

Fragen zur Verwendung von AEM oder zu technischen Fehlern, die möglicherweise bei Ihnen auftreten, sollten entsprechend dem herkömmlichen Support-Vorgang über das [Enterprise-Support-Portal für Experience Cloud](https://helpx.adobe.com/de/contact/enterprise-support.ec.html) gemeldet oder in der [Experience Manager-Community](https://forums.adobe.com/community/experience-cloud/marketing-cloud/experience-manager) diskutiert werden.

***AEM-Dokumentationsbeiträge sind kein Ersatz für die Adobe-Kundenunterstützung*** und Beiträge, die Antworten auf Support-Fragen suchen, werden abgelehnt.

### Die Beiträge müssen sich eindeutig auf die betroffenen Dokumentationsseiten beziehen.

Wenn Sie ein Problem erstellen, um Verbesserungen an der Dokumentation vorzuschlagen, müssen Sie Links zu den betroffenen Seiten angeben. Wenn Sie ein Problem mithilfe des Links **Diese Seite bearbeiten** auf einer Dokumentationsseite erstellen, wird das Problem automatisch mit einem Link zur Seite erstellt.

Dies gilt nicht für Pull-Anfragen, da Pull-Anfragen standardmäßig auf die betroffene(n) Seite(n) verweisen.

## Dokumentationsrichtlinien

Wir bitten darum, dass alle Beiträge zu unserer Dokumentation bestimmten Stilrichtlinien folgen.

Durch das Befolgen dieser Richtlinien erleichtern Sie die Überprüfung Ihres Beitrags und beschleunigen somit dessen Aufnahme in unsere Dokumentation.

### Sprache und Stil

#### Sprache

* Die AEM-Dokumentation wird in englischer Sprache verfasst und verwaltet.
* Ausdrücke sollten so einfach wie möglich sein.
* Drücken Sie sich klar und bündig aus.

Denken Sie daran, dass die wir internationale Leser der AEM-Dokumentation haben und von ihnen nicht erwartet werden kann, dass sie fließend Englisch beherrschen oder Muttersprachler sind. Vermeiden Sie umgangssprachliche Wendungen und verwenden Sie eine klare und einfache Sprache wie möglich.

#### Befolgen Sie das Stil-Handbuch von Microsoft.

Das [Manual of Style von Microsoft](https://docs.microsoft.com/de-de/style-guide/welcome/) ist ein kostenloses Stil-Handbuch zur Dokumentation von Software. Die AEM-Dokumentation folgt soweit möglich diesem Modell.

### Formatierung

| Element | Stil |
|---|---|
| Element oder Option der Benutzeroberfläche | **fett** |
| Dateiname, Pfad, Benutzereingabe, Parameterwerte | `monospaced` |
| Code, Befehlszeile | ```Code Block``` |

### Screenshots

Screenshots sollen mit Bedacht und nur dann verwendet werden, wenn eine Textbeschreibung nicht ausreicht.

Markierungen oder andere Anmerkungen in Screenshots (wie rote Rahmen, Pfeile oder Text) sollten nicht verwendet werden. Auf diese Weise können die Screenshots in lokalisierten Versionen der Dokumentation einfacher wiederverwendet oder repliziert werden.

### Versionsspezifische Verweise

Versuchen Sie nach Möglichkeit direkte Verweise auf eine bestimmte Version im gesamten Dokumentationsinhalt zu vermeiden. Dadurch wird die Dokumentation für künftige Versionen flexibler und erweiterbarer.

### Verwendung von Day, AEM, CQ, CRX

Das Produkt sollte in einem Artikel zum ersten Mal immer mit dem vollständigen Namen **Adobe Experience Manager** genannt und anschließend als **AEM** bezeichnet werden.

Day, Day-Software, CQ und CRX sollten nur verwendet werden, wenn dies unvermeidlich ist, z. B. in Klassennamen oder bei Verweisen auf die AEM-Historie.