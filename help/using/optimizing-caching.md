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
workflow-type: tm+mt
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
>Dispatcher-Versionen sind unabhängig von AEM. Möglicherweise wurden Sie von der Dokumentation zu einer früheren AEM-Version zu dieser Seite weitergeleitet.

Der Dispatcher bietet verschiedene integrierte Mechanismen, mit denen Sie die Leistung optimieren können. In diesem Abschnitt erfahren Sie, wie Sie Ihre Website einrichten, um die Vorteile der Zwischenspeicherung zu maximieren.

>[!NOTE]
>
>Beachten Sie dabei, dass der Dispatcher den Cache auf einem Standardwebserver speichert. Dies bedeutet, dass Sie:
>
>* alle Daten zwischenspeichern können, die als Seite gespeichert und mit einer URL abgerufen werden können
>* keine anderen Daten speichern können, z. B. HTTP-Header, Cookies, Sitzungsdaten und Formulardaten

>
>Allgemein müssen für viele Caching-Strategien geeignete URLs ausgewählt werden, damit diese zusätzlichen Daten nicht benötigt werden.

## Verwenden einer einheitlichen Seitencodierung  {#using-consistent-page-encoding}

HTTP-Anforderungs-Header werden nicht zwischengespeichert. Daher können Probleme auftreten, wenn Sie Seitenkodierungsinformationen im Header speichern. Wenn der Dispatcher in diesem Fall eine Seite aus dem Cache bereitstellt, wird die Standardkodierung des Webservers für die Seite verwendet. Es gibt zwei Möglichkeiten, um dieses Problem zu vermeiden:

* Wenn Sie nur eine Kodierung verwenden, achten Sie darauf, dass die auf dem Webserver verwendete Kodierung der Standardkodierung der AEM-Website entspricht.
* Verwenden Sie ein `<META>`-Tag im HTML-Abschnitt `head`, um die Codierung festzulegen. Beispiel:

```xml
        <META http-equiv="Content-Type" content="text/html; charset=EUC-JP">
```

## Vermeiden von URL-Parametern {#avoid-url-parameters}

Vermeiden Sie, wenn möglich, URL-Parameter für Seiten, die Sie zwischenspeichern möchten. Wenn Sie beispielsweise eine Bildergalerie betrachten, wird die folgende URL nie zwischengespeichert (es sei denn, der Dispatcher wurde [entsprechend konfiguriert](dispatcher-configuration.md#main-pars_title_24)):

```xml
www.myCompany.com/pictures/gallery.html?event=christmas&amp;page=1
```

Sie können diese Parameter allerdings in der URL der Seite platzieren:

```xml
www.myCompany.com/pictures/gallery.christmas.1.html
```

>[!NOTE]
>
>Diese URL ruft dieselbe Seite und Vorlage auf wie „gallery.html“. In der Vorlagendefinition können Sie angeben, welches Skript die Seite rendern soll, oder Sie können ein Skript für alle Seiten verwenden.

## Anpassen nach URL  {#customize-by-url}

Wenn Sie Benutzern die Möglichkeit geben, die Schriftgröße zu ändern (oder andere Layoutanpassungen vorzunehmen), stellen Sie sicher, dass die verschiedenen Anpassungen in der URL repräsentiert werden.

Beispielsweise werden Cookies nicht zwischengespeichert. Wenn Sie also die Schriftgröße in einem Cookie (oder einem ähnlichen Mechanismus) speichern, bleibt die Schriftgröße für die zwischengespeicherte Seite nicht erhalten. Daher gibt der Dispatcher Dokumente mit beliebiger Schriftgröße nach dem Zufallsprinzip zurück.

Wenn Sie die Schriftgröße in der URL als Selektor einschließen, wird dieses Problem vermieden:

```xml
www.myCompany.com/news/main.large.html
```

>[!NOTE]
>
>Bei den meisten Layoutkomponenten können auch Stylesheets und/oder clientseitige Skripts verwendet werden. Diese funktionieren normalerweise gut mit dem Zwischenspeichern.
>
>Dies ist auch für Druckversionen nützlich, für die Sie eine URL wie in diesem Beispiel erstellen können:
>
>`www.myCompany.com/news/main.print.html`
>
>Unter Verwendung des Skript-Globbings der Vorlagendefinition können Sie ein anderes Skript festlegen, das die Seiten für das Drucken anzeigt.

## Invalidierung von als Titel verwendeten Bilddateien  {#invalidating-image-files-used-as-titles}

Wenn Sie Seitentitel oder anderen Text als Grafik rendern, sollten Sie die Dateien speichern, damit sie nach einer Inhaltsaktualisierung auf der Seite gelöscht werden:

1. Platzieren Sie die Bilddatei im selben Verzeichnis wie die Seite.
1. Verwenden Sie das folgende Namensformat für die Bilddatei:

   `<page file name>.<image file name>`

Beispielsweise können Sie den Titel der Seite „myPage.html“ in der Datei „myPage.title.gif“ speichern. Diese Datei wird automatisch gelöscht, wenn die Seite aktualisiert wird. Alle Änderungen des Seitentitels werden daher automatisch im Cache übernommen.

>[!NOTE]
>
>Die Bilddatei ist nicht unbedingt tatsächlich in der AEM-Instanz vorhanden. Sie können ein Skript verwenden, das die Bilddatei dynamisch erstellt. Der Dispatcher speichert die Datei dann auf dem Webserver.

## Invalidierung von Bilddateien für die Navigation  {#invalidating-image-files-used-for-navigation}

Wenn Sie Bilder als Navigationseinträge verwenden, gehen Sie im Prinzip wie bei Titeln vor, das Verfahren ist nur etwas komplexer. Speichern Sie alle Navigationsgrafiken mit den Zielseiten. Wenn Sie zwei Bilder für den normalen und aktiven Status verwenden, können Sie die folgenden Skripts verwenden:

* Ein Skript, das die Seite normal anzeigt.
* Ein Skript, das „.normal“-Anforderungen verarbeitet und das normale Bild zurückgibt.
* Ein Skript, das „.active“ verarbeitet und das aktivierte Bild zurückgibt.

Sie müssen diese Grafiken mit demselben Namenhandle wie die Seite erstellen, um sicherzustellen, dass bei einer Inhaltsaktualisierung diese Bilder sowie die Seite gelöscht werden.

Bei Seiten, die nicht geändert werden, bleiben die Bilder im Cache, auch wenn die Seiten selbst normalerweise automatisch ungültig gemacht werden.

## Personalisierung  {#personalization}

Der Dispatcher kann keine personalisierten Daten zwischenspeichern. Sie sollten die Personalisierung daher nur bei Bedarf verwenden. Dies hat folgende Gründe:

* Wenn Sie eine frei anpassbare Startseite verwenden, muss diese Seite jedes Mal erstellt werden, wenn ein Benutzer sie anfordert.
* Wenn Sie stattdessen eine Auswahl von 10 verschiedenen Startseiten anbieten, können Sie diese zwischenspeichern und so die Leistung verbessern.

>[!NOTE]
>
>Wenn Sie jede Seite personalisieren (zum Beispiel durch Einfügen des Benutzernamens in der Titelleiste), können Sie sie nicht zwischenspeichern. Dies kann die Leistung erheblich beeinträchtigen.
>
>Wenn dies jedoch erforderlich ist, haben Sie folgende Möglichkeiten:
>
>* Sie können die Seite mit iFrames aufteilen in einen Teil, der für alle Benutzer gleich ist, und einen Teil, der bei allen Seiten eines Benutzers gleich ist. Diese beiden Teile können dann zwischengespeichert werden.
>* Sie können mit clientseitigem JavaScript personalisierte Informationen anzeigen. Sie müssen jedoch sicherstellen, dass die Seite weiterhin richtig angezeigt wird, wenn ein Benutzer JavaScript deaktiviert.

>


## Sticky-Verbindungen  {#sticky-connections}

[Sticky-Verbindungen](dispatcher.md#TheBenefitsofLoadBalancing) stellen sicher, dass alle Dokumente für einen Benutzer auf demselben Server erstellt werden. Wenn ein Benutzer dieses Verzeichnis verlässt und später zurückkehrt, bleibt die Verbindung erhalten. Definieren Sie einen Ordner für alle Dokumente, die Sticky-Verbindungen für die Website benötigen. Speichern Sie möglichst keine anderen Dokumente in diesem Ordner. Dies wirkt sich auf den Lastenausgleich aus, wenn Sie personalisierte Seiten und Sitzungsdaten verwenden.

## MIME-Typen  {#mime-types}

Es gibt zwei Möglichkeiten, wie ein Browser den Typ einer Datei bestimmen kann:

1. Durch die Erweiterung (z. B. .html, .gif, .jpg)
1. Durch den MIME-Typ, den der Server mit der Datei sendet.

Für die meisten Dateien wird der MIME-Typ durch die Dateierweiterung angegeben :

1. Durch die Erweiterung (z. B. .html, .gif, .jpg)
1. Durch den MIME-Typ, den der Server mit der Datei sendet.

Wenn der Dateiname keine Erweiterung aufweist, wird er als einfacher Text dargestellt.

Der MIME-Typ ist ein Bestandteil des HTTP-Headers. Daher wird er nicht vom Dispatcher zwischengespeichert. Wenn Ihre AEM-Anwendung Dateien zurückgibt, deren Dateiendung nicht erkannt wird, sondern bei denen der MIME-Typ verwendet wird, werden diese Dateien möglicherweise nicht korrekt angezeigt.

Um sicherzustellen, dass Dateien richtig zwischengespeichert werden, halten Sie sich an die folgenden Richtlinien.

* Stellen Sie sicher, dass Dateien immer die richtige Erweiterung aufweisen.
* Verwenden Sie möglichst keine allgemeinen Dateibereitstellungsskripts mit URLs wie „download.jsp?file=2214“. Schreiben Sie das Skript so um, dass die URLs die Dateispezifikation enthalten. Im obigen Beispiel wäre dies „download.2214.pdf“.

