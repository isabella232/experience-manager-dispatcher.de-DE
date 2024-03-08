---
title: Verwenden von SSL mit dem Dispatcher
seo-title: Using SSL with Dispatcher
description: Erfahren Sie, wie der Dispatcher für die Kommunikation mit AEM mithilfe von SSL-Verbindungen konfiguriert wird.
seo-description: Learn how to configure Dispatcher to communicate with AEM using SSL connections.
uuid: 1a8f448c-d3d8-4798-a5cb-9579171171ed
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: 771cfd85-6c26-4ff2-a3fe-dff8d8f7920b
index: y
internal: n
snippet: y
exl-id: ec378409-ddb7-4917-981d-dbf2198aca98
source-git-commit: e87af532ee3268f0a45679e20031c3febc02de58
workflow-type: ht
source-wordcount: '1311'
ht-degree: 100%

---

# Verwenden von SSL mit dem Dispatcher {#using-ssl-with-dispatcher}

Verwenden Sie SSL-Verbindungen zwischen dem Dispatcher und dem Render-Computer:

* [Unidirektionale SSL-Kommunikation](#use-ssl-when-dispatcher-connects-to-aem)
* [Bidirektionale SSL-Kommunikation](#configuring-mutual-ssl-between-dispatcher-and-aem)

>[!NOTE]
>
>Vorgänge im Zusammenhang mit SSL-Zertifikaten sind von Drittanbieterprodukten abhängig. Sie sind nicht durch den Adobe Platinum Maintenance and Support-Vertrag abgedeckt.

## Verwenden von SSL, wenn der Dispatcher eine Verbindung zu AEM herstellt {#use-ssl-when-dispatcher-connects-to-aem}

Konfigurieren Sie den Dispatcher für die Kommunikation mit der AEM- oder CQ-Render-Instanz mithilfe von SSL-Verbindungen.

Bevor Sie den Dispatcher konfigurieren, konfigurieren Sie zunächst AEM oder CQ für die Verwendung von SSL:

* AEM 6.1: [Aktivieren von HTTP über SSL](https://experienceleague.adobe.com/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions.html?lang=de)
* AEM 6.2: [Aktivieren von HTTP über SSL](https://experienceleague.adobe.com/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions.html?lang=de)
* Ältere AEM-Versionen: Siehe [diese Seite](https://experienceleague.adobe.com/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions.html?lang=de).

### SSL-bezogene Anfrage-Header {#ssl-related-request-headers}

Wenn der Dispatcher eine HTTPS-Anfrage erhält, nimmt der Dispatcher die folgenden Header in die nachfolgende Anfrage auf, die an AEM oder CQ gesendet wird:

* `X-Forwarded-SSL`
* `X-Forwarded-SSL-Cipher`
* `X-Forwarded-SSL-Keysize`
* `X-Forwarded-SSL-Session-ID`

Eine Anfrage durch Apache 2.4 mit `mod_ssl` enthält ähnliche Kopfzeilen wie im folgenden Beispiel:

```shell
X-Forwarded-SSL: on
X-Forwarded-SSL-Cipher: DHE-RSA-AES256-SHA
X-Forwarded-SSL-Session-ID: 814825E8CD055B4C166C2EF6D75E1D0FE786FFB29DEB6DE1E239D5C771CB5B4D
```

### Konfigurieren des Dispatchers für die Verwendung von SSL {#configuring-dispatcher-to-use-ssl}

Um den Dispatcher so zu konfigurieren, dass über SSL eine Verbindung mit AEM oder CQ hergestellt wird, muss die Datei [dispatcher.any](dispatcher-configuration.md) die folgenden Eigenschaften aufweisen:

* Ein virtueller Host, der HTTPS-Anfragen bearbeitet.
* Der Abschnitt `renders` des virtuellen Hosts beinhaltet ein Element, das den Hostnamen und den Port der CQ- oder AEM-Instanz identifiziert, die HTTPS verwendet.
* Das Element `renders` beinhaltet eine Eigenschaft namens `secure` mit dem Wert `1`.

Hinweis: Erstellen Sie einen weiteren virtuellen Host, um bei Bedarf HTTP-Anfragen zu verarbeiten.

Im folgenden Beispiel weist die Datei `dispatcher.any` die Eigenschaftswerte für das Herstellen einer Verbindung mithilfe von HTTPS zu einer CQ-Instanz auf, die auf dem Host `localhost` und Port `8443` ausgeführt wird:

```
/farms
{
   /secure
   { 
      /virtualhosts
      {
         # select this farm for all incoming HTTPS requests
         "https://*"
      }
      /renders
      {
      /0001
         {
            # hostname or IP of the render
            /hostname "localhost"
            # port of the render
            /port "8443"
            # connect via HTTPS
            /secure "1"
         }
      }
     # the rest of the properties are omitted
   }

   /non-secure
   { 
      /virtualhosts
      {
         # select this farm for all incoming HTTP requests
         "http://*"
      }
      /renders
      {
         /0001
      {
         # hostname or IP of the render
         /hostname "localhost"
         # port of the render
         /port "4503"
      }
   }
    # the rest of the properties are omitted
}
```

## Konfigurieren der bidirektionalen SSL-Kommunikation zwischen dem Dispatcher und AEM {#configuring-mutual-ssl-between-dispatcher-and-aem}

Konfigurieren Sie die Verbindungen zwischen dem Dispatcher und dem Render-Computer (normalerweise eine AEM- oder CQ-Veröffentlichungsinstanz) zur Verwendung der bidirektionalen SSL-Kommunikation:

* Der Dispatcher stellt über SSL eine Verbindung zur Render-Instanz her.
* Die Render-Instanz überprüft die Gültigkeit des Zertifikats des Dispatchers.
* Der Dispatcher überprüft, ob die Zertifizierungsstelle des Zertifikats der Render-Instanz als vertrauenswürdig eingestuft wird.
* (Optional) Der Dispatcher überprüft, ob das Zertifikat der Render-Instanz mit der Server-Adresse der Render-Instanz übereinstimmt.

Um die bidirektionale SSL-Kommunikation zu konfigurieren, benötigen Sie Zertifikate, die von einer vertrauenswürdigen Zertifizierungsstelle signiert sind. Selbstsignierte Zertifikate sind nicht ausreichend. Entweder können Sie als Zertifizierungsstelle fungieren oder Sie können eine externe Zertifizierungsstelle in Anspruch nehmen, um Ihre Zertifikate zu signieren. Um die bidirektionale SSL-Kommunikation zu konfigurieren, benötigen Sie Folgendes:

* Signierte Zertifikate für die Render-Instanz und den Dispatcher
* Das Zertifikat der Zertifizierungsstelle (wenn Sie als Zertifizierungsstelle fungieren)
* OpenSSL-Bibliotheken für die Generierung der Zertifizierungsstelle, Zertifikate und Zertifikatsanfragen

Gehen Sie wie folgt vor, um die bidirektionale SSL-Kommunikation zu konfigurieren:

1. [Installieren](dispatcher-install.md) Sie die neueste Version des Dispatchers für Ihre Plattform. Verwenden Sie eine Dispatcher-Binärdatei, die SSL unterstützt (SSL ist im Dateinamen enthalten, etwa „dispatcher-apache2.4-linux-x86-64-ssl10-4.1.7.tar“).
1. [Erstellen oder beziehen Sie ein von einer Zertifizierungsstelle signiertes Zertifikat](dispatcher-ssl.md#main-pars-title-3) für den Dispatcher und die Renderinstanz.
1. [Erstellen Sie einen Keystore, der das Render-Zertifikat enthält](dispatcher-ssl.md#main-pars-title-6) und konfigurieren Sie den HTTP-Dienst der Render-Instanz.
1. [Konfigurieren Sie das Dispatcher-Webservermodul](dispatcher-ssl.md#main-pars-title-4) für die bidirektionale SSL-Kommunikation.

### Erstellen oder Beziehen von von Zertifizierungsstellen signierten Zertifikaten  {#creating-or-obtaining-ca-signed-certificates}

Erstellen oder beziehen Sie von einer Zertifizierungsstelle signierte Zertifikate, die die Veröffentlichungsinstanz und den Dispatcher authentifizieren.

#### Erstellen einer Zertifizierungsstelle  {#creating-your-ca}

Wenn Sie als Zertifizierungsstelle fungieren, verwenden Sie [OpenSSL](https://www.openssl.org/) zum Erstellen der Zertifizierungsstelle, die die Server- und Client-Zertifikate signiert. (Sie müssen die OpenSSL-Bibliotheken installiert haben.) Wenn Sie eine externe Zertifizierungsstelle verwenden, führen Sie dieses Verfahren nicht durch.

1. Öffnen Sie ein Terminal-Fenster und ändern Sie das aktuelle Verzeichnis in das Verzeichnis, in dem sich die Datei `CA.sh` befindet, beispielsweise `/usr/local/ssl/misc`.
1. Um die Zertifizierungsstelle zu erstellen, geben Sie den folgenden Befehl ein und legen Sie dann Werte fest, wenn Sie dazu aufgefordert werden:

   ```shell
   ./CA.sh -newca
   ```

   >[!NOTE]
   >
   >Einige Eigenschaften in der Datei `openssl.cnf` steuern das Verhalten des Skripts „CA.sh“. Bearbeiten Sie diese Datei nach Bedarf, bevor Sie Ihre Zertifizierungsstelle erstellen.

#### Erstellen der Zertifikate {#creating-the-certificates}

Verwenden Sie OpenSSL, um die Zertifikatanforderungen zu erstellen, die an die externe Zertifizierungsstelle gesendet oder von Ihrer Zertifizierungsstelle signiert werden sollen.

Wenn Sie ein Zertifikat erstellen, verwendet OpenSSL die Eigenschaft für den allgemeinen Namen des Zertifikats, um die Inhaberin oder den Inhaber des Zertifikats zu identifizieren. Verwenden Sie für das Zertifikat der Render-Instanz den Host-Namen des Instanz-Computers als den allgemeinen Namen, wenn Sie den Dispatcher so konfigurieren, dass das Zertifikat nur dann akzeptiert wird, wenn es dem Host-Namen der Veröffentlichungsinstanz entspricht. (Siehe Eigenschaft [DispatcherCheckPeerCN](dispatcher-ssl.md#main-pars-title-11))

1. Öffnen Sie ein Terminal-Fenster und ändern Sie das aktuelle Verzeichnis in das Verzeichnis, in dem sich die Datei „CH.sh“ Ihrer OpenSSL-Bibliotheken befindet.
1. Geben Sie den folgenden Befehl ein und legen Sie Werte fest, wenn Sie dazu aufgefordert werden. Verwenden Sie ggf. den Host-Namen der Veröffentlichungsinstanz als allgemeinen Namen. Der Host-Name ist ein von DNS auflösbarer Name für die IP-Adresse des Render-Knotens:

   ```shell
   ./CA.sh -newreq
   ```

   Wenn Sie eine externe Zertifizierungsstelle verwenden, senden Sie die Datei „newreq.pem“ zum Signieren an die Zertifizierungsstelle. Wenn Sie als Zertifizierungsstelle fungieren, fahren Sie mit Schritt 3 fort.

1. Um das Zertifikat mit dem Zertifikat Ihrer Zertifizierungsstelle zu signieren, geben Sie den folgenden Befehl ein:

   ```shell
   ./CA.sh -sign
   ```

   Es werden zwei Dateien mit den Namen `newcert.pem` und `newkey.pem` in dem Verzeichnis erstellt, das Ihre Dateien für die Verwaltung der Zertifizierungsstelle enthält. Bei diesen beiden Dateien handelt es sich um das öffentliche Zertifikat bzw. den privaten Schlüssel für den Render-Computer.

1. Benennen Sie `newcert.pem` in `rendercert.pem` und `newkey.pem` in `renderkey.pem` um.
1. Wiederholen Sie die Schritte 2 und 3, um ein Zertifikat und einen öffentlichen Schlüssel für das Dispatcher-Modul zu erstellen. Stellen Sie sicher, dass Sie einen allgemeinen Namen verwenden, der für die Dispatcher-Instanz spezifisch ist.
1. Benennen Sie `newcert.pem` in `dispcert.pem` und `newkey.pem` in `dispkey.pem` um.

### Konfigurieren von SSL auf dem Render-Computer {#configuring-ssl-on-the-render-computer}

Konfigurieren Sie SSL in der Render-Instanz mithilfe der Dateien `rendercert.pem` und `renderkey.pem`.

#### Konvertieren des Render-Zertifikats in das Java™ KeyStore(JKS)-Format {#converting-the-render-certificate-to-jks-format}

Verwenden Sie den folgenden Befehl, um das Render-Zertifikat, bei dem es sich um eine PEM-Datei handelt, in eine Datei im PKCS#12-Format zu konvertieren. Schließen Sie außerdem das Zertifikat der Zertifizierungsstelle ein, die das Render-Zertifikat signiert hat:

1. Ändern Sie in einem Terminal-Fenster das aktuelle Verzeichnis in den Speicherort des Render-Zertifikats und des privaten Schlüssels.
1. Geben Sie den folgenden Befehl ein, um das Render-Zertifikat, bei dem es sich um eine PEM-Datei handelt, in eine Datei im PKCS#12-Format zu konvertieren. Schließen Sie außerdem das Zertifikat der Zertifizierungsstelle ein, die das Render-Zertifikat signiert hat:

   ```shell
   openssl pkcs12 -export -in rendercert.pem -inkey renderkey.pem  -certfile demoCA/cacert.pem -out rendercert.p12
   ```

1. Geben Sie den folgenden Befehl ein, um die PKCS#12-Datei in das Java™ KeyStore(JKS)-Format zu konvertieren:

   ```shell
   keytool -importkeystore -srckeystore servercert.p12 -srcstoretype pkcs12 -destkeystore render.keystore
   ```

1. Der Java™-Keystore wird mithilfe eines Standardalias erstellt. Ändern Sie ggf. den Alias:

   ```shell
   keytool -changealias -alias 1 -destalias jettyhttp -keystore render.keystore
   ```

#### Hinzufügen des Zertifizierungsstellenzertifikats zum Render-TrustStore  {#adding-the-ca-cert-to-the-render-s-truststore}

Wenn Sie als Zertifizierungsstelle fungieren, importieren Sie das Zertifizierungsstellenzertifikat in einen Keystore. Konfigurieren Sie anschließend die JVM, auf der die Render-Instanz ausgeführt wird, so, dass sie dem Keystore vertraut.

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (sbroders@adobe.com)
Last Modified Date: 2014-08-12T13:11:21.401-0400

<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">The jetty http service has properties to specify trusted CA certificates for mutual SSL for 6.0. Whether they are operable is undetetermined. See https://issues.adobe.com/browse/DOC-4738.</p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;"> </p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">For 5.6.1, you would specify the system property javax.net.ssl.trustStore, using the path to cacerts as value.</p>

 -->

1. Verwenden Sie einen Texteditor, um die Datei „cacert.pem“ zu öffnen und den gesamten Text zu entfernen, der der folgenden Zeile vorangeht:

   `-----BEGIN CERTIFICATE-----`

1. Verwenden Sie den folgenden Befehl, um das Zertifikat in einen Keystore zu importieren:

   ```shell
   keytool -import -keystore cacerts.keystore -alias myca -storepass password -file cacert.pem
   ```

1. Verwenden Sie die folgende Systemeigenschaft, um die JVM, auf der die Render-Instanz ausgeführt wird, so zu konfigurieren, dass sie dem Keystore vertraut:

   ```shell
   -Djavax.net.ssl.trustStore=<location of cacerts.keystore>
   ```

   Wenn Sie beispielsweise das Skript „crx-quickstart/bin/quickstart script“ verwenden, um Ihre Veröffentlichungsinstanz zu starten, können Sie die Eigenschaft „CQ_JVM_OPTS“ ändern:

   ```shell
   CQ_JVM_OPTS='-server -Xmx2048m -XX:MaxPermSize=512M -Djavax.net.ssl.trustStore=/usr/lib/cq6.0/publish/ssl/cacerts.keystore'
   ```

#### Konfigurieren der Renderinstanz  {#configuring-the-render-instance}

Um den HTTP-Dienst der Render-Instanz für SSL zu konfigurieren, verwenden Sie das Render-Zertifikat mit den Anweisungen im Abschnitt *Aktivieren von SSL in Veröffentlichungsinstanzen*:

* AEM 6.2: [Aktivieren von HTTP über SSL](https://experienceleague.adobe.com/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions.html?lang=de)
* AEM 6.1: [Aktivieren von HTTP über SSL](https://experienceleague.adobe.com/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions.html?lang=de)
* Ältere AEM-Versionen: Siehe [diese Seite](https://experienceleague.adobe.com/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions.html?lang=de)

### Konfigurieren von SSL für das Dispatcher-Modul {#configuring-ssl-for-the-dispatcher-module}

Um den Dispatcher für die Verwendung der bidirektionalen SSL-Kommunikation zu konfigurieren, bereiten Sie das Dispatcher-Zertifikat vor und konfigurieren Sie dann das Webservermodul.

### Erstellen eines einheitlichen Dispatcher-Zertifikats  {#creating-a-unified-dispatcher-certificate}

Führen Sie das Dispatcher-Zertifikat und den unverschlüsselten privaten Schlüssel in einer einzelnen PEM-Datei zusammen. Verwenden Sie einen Text-Editor oder den Befehl `cat`, um eine Datei wie im folgenden Beispiel zu erstellen:

1. Öffnen Sie ein Terminal-Fenster und ändern Sie das aktuelle Verzeichnis in den Speicherort der Datei „dispkey.pem“.
1. Geben Sie den folgenden Befehl ein, um den privaten Schlüssel zu entschlüsseln:

   ```shell
   openssl rsa -in dispkey.pem -out dispkey_unencrypted.pem
   ```

1. Verwenden Sie einen Text-Editor oder den Befehl `cat`, um den unverschlüsselten privaten Schlüssel und das Zertifikat wie im folgenden Beispiel in einer Datei zusammenzuführen:

   ```xml
   -----BEGIN RSA PRIVATE KEY-----
   MIICxjBABgkqhkiG9w0B...
   ...M2HWhDn5ywJsX
   -----END RSA PRIVATE KEY-----
   -----BEGIN CERTIFICATE-----
   MIIC3TCCAk...
   ...roZAs=
   -----END CERTIFICATE-----
   ```

### Festlegen des für den Dispatcher zu verwendenden Zertifikats {#specifying-the-certificate-to-use-for-dispatcher}

Fügen Sie die folgenden Eigenschaften zur [Konfiguration des Dispatcher-Moduls](dispatcher-install.md#main-pars-55-35-1022) (in der Datei `httpd.conf`) hinzu:

* `DispatcherCertificateFile`: Der Pfad zu der Datei mit dem einheitlichen Dispatcher-Zertifikat, die das öffentliche Zertifikat und den unverschlüsselten privaten Schlüssel enthält. Diese Datei wird verwendet, wenn der SSL-Server das Dispatcher-Clientzertifikat anfordert.
* `DispatcherCACertificateFile`: Der Pfad zur Datei mit dem Zertifizierungsstellenzertifikat, die verwendet wird, wenn der SSL-Server eine Zertifizierungsstelle präsentiert, die von einer Stammzertifizierungsstelle als nicht vertrauenswürdig eingestuft wird.
* `DispatcherCheckPeerCN`: Gibt an, ob die Überprüfung des Hostnamens für Remoteserverzertifikate aktiviert (`On`) oder deaktiviert (`Off`) werden soll.

Der folgende Code weist eine Beispielkonfiguration auf:

```xml
<IfModule disp_apache2.c>
  DispatcherConfig conf/dispatcher.any
  DispatcherLog    logs/dispatcher.log
  DispatcherLogLevel 3
  DispatcherNoServerHeader 0
  DispatcherDeclineRoot 0
  DispatcherUseProcessedURL 0
  DispatcherPassError 0
  DispatcherCertificateFile disp_unified.pem
  DispatcherCACertificateFile cacert.pem
  DispatcherCheckPeerCN On
</IfModule>
```
