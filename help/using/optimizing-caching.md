---
title: Optimierung von Websites für die Cache-Leistung
seo-title: Optimizing a Website for Cache Performance
description: Sie erfahren, wie Sie Ihre Website einrichten, um die Vorteile der Zwischenspeicherung zu maximieren.
seo-description: Dispatcher offers a number of built-in mechanisms that you can use to optimize performance. Learn how to design your web site to maximize the benefits of caching.
uuid: 2d4114d1-f464-4e10-b25c-a1b9a9c715d1
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: ba323503-1494-4048-941d-c1d14f2e85b2
redirecttarget: https://helpx.adobe.com/experience-manager/6-4/sites/deploying/using/configuring-performance.html
index: y
internal: n
snippet: y
source-git-commit: 762f575a58f53d25565fb9f67537e372c760674f
workflow-type: ht
source-wordcount: '1134'
ht-degree: 100%

---


# Optimierung von Websites für die Cache-Leistung {#optimizing-a-website-for-cache-performance}

<!-- 

Comment Type: remark
Last Modified By: Silviu Raiman (raiman)
Last Modified Date: 2017-10-25T04:13:34.919-0400

<p>This is a redirect to /experience-manager/6-2/sites/deploying/using/configuring-performance.html</p>

 -->

>[!NOTE]
>
>Dispatcher-Versionen sind unabhängig von AEM. Sie wurden möglicherweise zu dieser Seite umgeleitet, wenn Sie einem Link zur Dispatcher-Dokumentation gefolgt sind, der in der Dokumentation für eine frühere AEM-Version eingebettet ist.

Der Dispatcher bietet verschiedene integrierte Mechanismen, mit denen Sie die Leistung optimieren können. In diesem Abschnitt erfahren Sie, wie Sie Ihre Website gestalten können, um die Vorteile der Zwischenspeicherung zu maximieren.

>[!NOTE]
>
>Vielleicht erinnern Sie sich daran, dass der Dispatcher den Cache auf einem standardmäßigen Webserver speichert. Dies bedeutet, dass Sie:
>
>* alle Daten zwischenspeichern können, die als Seite gespeichert und mit einer URL abgerufen werden können
>* keine anderen Daten speichern können, z. B. HTTP-Header, Cookies, Sitzungs- und Formulardaten
>
>Allgemein müssen für viele Caching-Strategien geeignete URLs ausgewählt werden, damit diese zusätzlichen Daten nicht benötigt werden.

## Verwenden einer einheitlichen Seitencodierung  {#using-consistent-page-encoding}

HTTP-Anfrage-Header werden nicht zwischengespeichert. Daher können Probleme auftreten, wenn Sie Seitencodierungsinformationen im Header speichern. In diesem Fall wird die Standardcodierung des Webservers für die Seite verwendet, wenn der Dispatcher eine Seite aus dem Cache bereitstellt. Es gibt zwei Möglichkeiten, dieses Problem zu vermeiden:

* Wenn Sie nur eine Codierung verwenden, stellen Sie sicher, dass die auf dem Webserver verwendete Codierung mit der Standardcodierung der AEM-Website übereinstimmt.
* Verwenden Sie ein `<META>`-Tag im HTML-Abschnitt `head`, um die Codierung festzulegen. Beispiel:

```xml
        <META http-equiv="Content-Type" content="text/html; charset=EUC-JP">
```

## Vermeiden von URL-Parametern {#avoid-url-parameters}

Vermeiden Sie nach Möglichkeit URL-Parameter für Seiten, die Sie zwischenspeichern möchten. Wenn Sie beispielsweise über eine Bildergalerie verfügen, wird die folgende URL nie zwischengespeichert (es sei denn, der Dispatcher ist [entsprechend konfiguriert](dispatcher-configuration.md#main-pars_title_24)):

```xml
www.myCompany.com/pictures/gallery.html?event=christmas&amp;page=1
```

Sie können diese Parameter jedoch wie folgt in die Seiten-URL einfügen:

```xml
www.myCompany.com/pictures/gallery.christmas.1.html
```

>[!NOTE]
>
>Diese URL ruft dieselbe Seite und Vorlage auf wie „gallery.html“. In der Vorlagendefinition können Sie angeben, welches Skript die Seite rendert, oder Sie können dasselbe Skript für alle Seiten verwenden.

## Anpassen nach URL  {#customize-by-url}

Wenn Sie Benutzerinnen und Benutzern erlauben, die Schriftgröße (oder eine andere Layout-Anpassung) zu ändern, stellen Sie sicher, dass die verschiedenen Anpassungen in der URL berücksichtigt werden.

Beispielsweise werden Cookies nicht zwischengespeichert. Wenn Sie die Schriftgröße also in einem Cookie (oder einem ähnlichen Mechanismus) speichern, bleibt die Schriftgröße für die zwischengespeicherte Seite nicht erhalten. Daher gibt der Dispatcher Dokumente mit zufälliger Schriftgröße zurück.

Durch Einbeziehung der Schriftgröße in die URL als Selektor wird dieses Problem vermieden:

```xml
www.myCompany.com/news/main.large.html
```

>[!NOTE]
>
>Bei den meisten Layoutkomponenten können auch Stylesheets und/oder Client-seitige Skripts verwendet werden. Diese funktionieren normalerweise gut beim Caching.
>
>Dies ist auch für Druckversionen nützlich, für die Sie eine URL wie in diesem Beispiel erstellen können:
>
>`www.myCompany.com/news/main.print.html`
>
>Mithilfe des Skript-Globbings der Vorlagendefinition können Sie ein separates Skript angeben, das die Druckseiten rendert.

## Invalidierung von als Titel verwendeten Bilddateien  {#invalidating-image-files-used-as-titles}

Wenn Sie Seitentitel oder anderen Text als Bilder rendern, wird empfohlen, die Dateien so zu speichern, dass sie bei einer Inhaltsaktualisierung auf der Seite gelöscht werden:

1. Platzieren Sie die Bilddatei im selben Ordner wie die Seite.
1. Verwenden Sie das folgende Namensformat für die Bilddatei:

   `<page file name>.<image file name>`

Beispielsweise können Sie den Titel der Seite „myPage.html“ in der Datei „myPage.title.gif“ speichern. Diese Datei wird automatisch gelöscht, wenn die Seite aktualisiert wird, sodass jede Änderung am Seitentitel automatisch im Cache angezeigt wird.

>[!NOTE]
>
>Die Bilddatei ist nicht unbedingt physisch auf der AEM-Instanz vorhanden. Sie können ein Skript verwenden, das die Bilddatei dynamisch erstellt. Der Dispatcher speichert die Datei dann auf dem Webserver.

## Invalidierung von Bilddateien für die Navigation  {#invalidating-image-files-used-for-navigation}

Wenn Sie Bilder als Navigationseinträge verwenden, gehen Sie im Prinzip wie bei Titeln vor, das Verfahren ist nur etwas komplexer. Speichern Sie alle Navigationsbilder mit den Zielseiten. Wenn Sie zwei Bilder für „normal“ und „aktiv“ verwenden, können Sie die folgenden Skripte verwenden:

* Ein Skript, das die Seite wie gewohnt anzeigt.
* Ein Skript, das „.normal“-Anforderungen verarbeitet und das normale Bild zurückgibt.
* Ein Skript, das „.active“-Anfragen verarbeitet und das aktivierte Bild zurückgibt.

Sie müssen diese Bilder mit demselben Namens-Handle wie die Seite erstellen, um sicherzustellen, dass bei einer Inhaltsaktualisierung diese Bilder sowie die Seite gelöscht werden.

Bei Seiten, die nicht geändert werden, bleiben die Bilder im Cache, auch wenn die Seiten selbst normalerweise automatisch invalidiert werden.

## Personalisierung {#personalization}

Der Dispatcher kann keine personalisierten Daten zwischenspeichern. Sie sollten die Personalisierung daher nur bei Bedarf verwenden. Um zu veranschaulichen, warum:

* Wenn Sie eine frei anpassbare Startseite verwenden, muss diese Seite jedes Mal erstellt werden, wenn eine Benutzerin oder ein Benutzer sie anfordert.
* Wenn Sie stattdessen eine Auswahl von 10 verschiedenen Startseiten anbieten, können Sie diese jeweils zwischenspeichern und so die Leistung verbessern.

>[!NOTE]
>
>Wenn Sie jede Seite personalisieren (z. B. durch Einfügen des Benutzernamens in der Titelleiste), können Sie sie nicht zwischenspeichern. Dies kann die Leistung erheblich beeinträchtigen.
>
>Wenn dies jedoch erforderlich ist, haben Sie folgende Möglichkeiten:
>
>* Sie können die Seite mit iFrames aufteilen – in einen Teil, der für alle Benutzenden gleich ist, und einen Teil, der bei allen Seiten einer Person gleich ist. Diese beiden Teile können dann zwischengespeichert werden.
>* Sie können mit Client-seitigem JavaScript personalisierte Informationen anzeigen. Sie müssen jedoch sicherstellen, dass die Seite weiterhin richtig angezeigt wird, wenn jemand JavaScript deaktiviert.
>

## Sticky-Verbindungen  {#sticky-connections}

[Sticky-Verbindungen](dispatcher.md#TheBenefitsofLoadBalancing) stellen sicher, dass die Dokumente für eine Benutzerin oder einen Benutzer alle auf demselben Server erstellt werden. Wenn eine Benutzerin oder ein Benutzer diesen Ordner verlässt und später zu ihm zurückkehrt, bleibt die Verbindung erhalten. Definieren Sie einen Ordner für alle Dokumente, die Sticky-Verbindungen für die Website benötigen. Dieser sollte keine anderen Dokumente enthalten. Dies wirkt sich auf den Lastenausgleich aus, wenn Sie personalisierte Seiten und Sitzungsdaten verwenden.

## MIME-Typen {#mime-types}

Es gibt zwei Möglichkeiten, wie ein Browser den Typ einer Datei bestimmen kann:

1. Durch ihre Erweiterung (wie .html, .gif, .jpg usw.)
1. Über den MIME-Typ, den der Server mit der Datei sendet.

Für die meisten Dateien wird der MIME-Typ durch die Dateierweiterung angegeben d. h.:

1. Durch ihre Erweiterung (wie .html, .gif, .jpg usw.)
1. Über den MIME-Typ, den der Server mit der Datei sendet.

Wenn der Dateiname keine Erweiterung aufweist, wird er als Nur-Text angezeigt.

Der MIME-Typ ist ein Bestandteil des HTTP-Headers. Daher wird er nicht vom Dispatcher zwischengespeichert. Wenn Ihre AEM-Anwendung Dateien zurückgibt, deren Dateiendung nicht erkannt wird, sondern bei denen der MIME-Typ verwendet wird, werden diese Dateien möglicherweise nicht korrekt angezeigt.

Um sicherzustellen, dass Dateien ordnungsgemäß zwischengespeichert werden, befolgen Sie die folgenden Richtlinien:

* Stellen Sie sicher, dass Dateien immer die richtige Erweiterung haben.
* Verwenden Sie möglichst keine allgemeinen Dateibereitstellungsskripte mit URLs wie „download.jsp?file=2214“. Schreiben Sie das Skript so um, dass die URLs die Dateispezifikation enthalten. Im obigen Beispiel wäre dies „download.2214.pdf“.

