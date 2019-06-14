---
title: Verwenden von SSL mit dem Dispatcher
seo-title: Verwenden von SSL mit dem Dispatcher
description: Erfahren Sie, wie der Dispatcher für die Kommunikation mit AEM mithilfe von SSL-Verbindungen konfiguriert wird.
seo-description: Erfahren Sie, wie der Dispatcher für die Kommunikation mit AEM mithilfe von SSL-Verbindungen konfiguriert wird.
uuid: 1a8f448c-d3d8-4798-a5cb-9579171171ed
contentOwner: Benutzer
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: Dispatcher
content-type: Referenz
discoiquuid: 771cfd85-6c26-4ff2-a3fe-dff8d8f7920b
index: y
internal: n
snippet: y
translation-type: ht
source-git-commit: f35c79b487454059062aca6a7c989d5ab2afaf7b

---


# Verwenden von SSL mit dem Dispatcher {#using-ssl-with-dispatcher}

Verwenden Sie SSL-Verbindungen zwischen dem Dispatcher und dem Rendercomputer:

* [Unidirektionale SSL-Kommunikation](dispatcher-ssl.md#main-pars-title-1)
* [Bidirektionale SSL-Kommunikation](dispatcher-ssl.md#main-pars-title-2)

>[!NOTE]
>
>Vorgänge im Zusammenhang mit SSL-Zertifikaten sind von Drittanbieterprodukten abhängig. Sie sind nicht durch den Adobe Platinum Maintenance and Support-Vertrag abgedeckt.

## Verwenden von SSL, wenn der Dispatcher eine Verbindung zu AEM herstellt {#use-ssl-when-dispatcher-connects-to-aem}

Konfigurieren Sie den Dispatcher für die Kommunikation mit der AEM- oder CQ-Renderinstanz mithilfe von SSL-Verbindungen.

Bevor Sie den Dispatcher konfigurieren, konfigurieren Sie zunächst AEM oder CQ für die Verwendung von SSL:

* AEM 6.2: [Aktivieren von HTTP über SSL](https://helpx.adobe.com/experience-manager/6-2/sites/deploying/using/config-ssl.html)
* AEM 6.1: [Aktivieren von HTTP über SSL](https://docs.adobe.com/content/docs/de/aem/6-1/deploy/configuring/config-ssl.html)
* Ältere AEM-Versionen: siehe [dieser Seite](https://helpx.adobe.com/experience-manager/aem-previous-versions.html).

### SSL-bezogene Anforderungsheader {#ssl-related-request-headers}

Wenn der Dispatcher eine HTTPS-Anforderung erhält, nimmt Dispatcher die folgenden Header in die nachfolgende Anforderung auf, die an AEM oder CQ gesendet wird:

* `X-Forwarded-SSL`
* `X-Forwarded-SSL-Cipher`
* `X-Forwarded-SSL-Keysize`
* `X-Forwarded-SSL-Session-ID`

Eine Anfrage durch Apache 2.2 mit `mod_ssl` enthält ähnliche Kopfzeilen wie im folgenden Beispiel:

```shell
X-Forwarded-SSL: on
X-Forwarded-SSL-Cipher: DHE-RSA-AES256-SHA
X-Forwarded-SSL-Session-ID: 814825E8CD055B4C166C2EF6D75E1D0FE786FFB29DEB6DE1E239D5C771CB5B4D
```

### Konfigurieren des Dispatchers für die Verwendung von SSL {#configuring-dispatcher-to-use-ssl}

Um den Dispatcher so zu konfigurieren, dass über SSL eine Verbindung mit AEM oder CQ hergestellt wird, muss die Datei [dispatcher.any](dispatcher-configuration.md) die folgenden Eigenschaften aufweisen:

* Ein virtueller Host, der HTTPS-Anforderungen bearbeitet.
* Der Abschnitt `renders` des virtuellen Hosts beinhaltet ein Element, das den Hostnamen und den Port der CQ- oder AEM-Instanz identifiziert, die HTTPS verwendet.
* Das Element `renders` beinhaltet eine Eigenschaft namens `secure` mit dem Wert `1`.

Hinweis: Erstellen Sie einen weiteren virtuellen Host, um bei Bedarf HTTP-Anforderungen zu verarbeiten.

Im folgenden Beispiel weist die Datei „dispatcher.any“ die Eigenschaftenwerte für das Herstellen einer Verbindung mithilfe von HTTPS zu einer CQ-Instanz auf, die auf dem Host `localhost` und Port `8443` ausgeführt wird:

```
/farms
{
   /secure
   { 
      /virtualhosts
      {
         # select this farm for all incoming HTTPS requestss
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
         "https://*"
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

Konfigurieren Sie die Verbindungen zwischen dem Dispatcher und dem Rendercomputer für die Wiedergabe (normalerweise eine AEM- oder CQ-Veröffentlichungsinstanz) zur Verwendung der bidirektionalen SSL-Kommunikation:

* Der Dispatcher stellt über SSL eine Verbindung zur Renderinstanz her.
* Die Renderinstanz überprüft die Gültigkeit des Zertifikats des Dispatchers.
* Der Dispatcher überprüft, ob die Zertifizierungsstelle des Zertifikats der Renderinstanz als vertrauenswürdig eingestuft wird.
* (Optional) Der Dispatcher überprüft, ob das Zertifikat der Renderinstanz mit der Serveradresse der Renderinstanz übereinstimmt.

Um die bidirektionale SSL-Kommunikation zu konfigurieren, benötigen Sie Zertifikate, die von einer vertrauenswürdigen Zertifizierungsstelle signiert sind. Selbstsignierte Zertifikate sind nicht ausreichend. Entweder können Sie als Zertifizierungsstelle fungieren oder Sie können eine externe Zertifizierungsstelle in Anspruch nehmen, um Ihre Zertifikate zu signieren. Um die bidirektionale SSL-Kommunikation zu konfigurieren, benötigen Sie Folgendes:

* Signierte Zertifikate für die Renderinstanz und den Dispatcher
* Das Zertifikat der Zertifizierungsstelle (wenn Sie als Zertifizierungsstelle fungieren)
* OpenSSL-Bibliotheken für die Generierung der Zertifizierungsstelle, Zertifikate und Zertifikatanforderungen

Führen Sie die folgenden Schritte aus, um die bidirektionale SSL-Kommunikation zu konfigurieren:

1. [Installieren](dispatcher-install.md) Sie die neueste Version des Dispatchers für Ihre Plattform. Verwenden Sie eine Dispatcher-Binärdatei, die SSL unterstützt (SSL ist im Dateinamen enthalten, etwa „dispatcher-apache2.4-linux-x86-64-ssl10-4.1.7.tar“).
1. [Erstellen oder beziehen Sie ein von einer Zertifizierungsstelle signiertes Zertifikat](dispatcher-ssl.md#main-pars-title-3) für den Dispatcher und die Renderinstanz.
1. [Erstellen Sie einen Keystore, der das Renderzertifikat enthält](dispatcher-ssl.md#main-pars-title-6) und konfigurieren Sie den HTTP-Dienst des Renderknotens so, dass er es verwendet.
1. [Konfigurieren Sie das Dispatcher-Webservermodul](dispatcher-ssl.md#main-pars-title-4) für die bidirektionale SSL-Kommunikation.

### Erstellen oder Beziehen von von Zertifizierungsstellen signierten Zertifikaten  {#creating-or-obtaining-ca-signed-certificates}

Erstellen oder beziehen Sie von einer Zertifizierungsstelle signierte Zertifikate, die die Veröffentlichungsinstanz und den Dispatcher authentifizieren.

#### Erstellen einer Zertifizierungsstelle  {#creating-your-ca}

Wenn Sie als Zertifizierungsstelle fungieren, verwenden Sie [OpenSSL](https://www.openssl.org/) zum Erstellen der Zertifizierungsstelle, die die Server- und Clientzertifikate signiert. (Sie müssen die OpenSSL-Bibliotheken installiert haben.) Führen Sie diese Schritte nicht durch, wenn Sie eine externe Zertifizierungsstelle verwenden.

1. Öffnen Sie ein Terminalfenster und ändern Sie das aktuelle Verzeichnis in das Verzeichnis, in dem sich die Datei „CA.sh“ befindet, beispielsweise `/usr/local/ssl/misc`.
1. Um die Zertifizierungsstelle zu erstellen, geben Sie den folgenden Befehl ein und geben Sie dann Werte an, wenn Sie dazu aufgefordert werden:

   ```shell
   ./CA.sh -newca
   ```

   >[!NOTE]
   >
   >Einige Eigenschaften in der Datei „openssl.cnf“ steuern das Verhalten des Skripts „CA.sh“. Sie sollten diese Datei entsprechend ändern, bevor Sie die Zertifizierungsstelle erstellen.

#### Erstellen von Zertifikaten  {#creating-the-certificates}

Verwenden Sie OpenSSL, um die Zertifikatanforderungen zu erstellen, die an die externe Zertifizierungsstelle gesendet oder von Ihrer Zertifizierungsstelle signiert werden sollen.

Wenn Sie ein Zertifikat erstellen, verwendet OpenSSL die Eigenschaft für den allgemeinen Namen des Zertifikats, um den Zertifikatinhaber zu identifizieren. Verwenden Sie für das Zertifikat der Renderinstanz den Hostnamen des Instanzcomputers als den allgemeinen Namen, wenn Sie den Dispatcher so konfigurieren, dass das Zertifikat nur dann akzeptiert wird, wenn es dem Hostnamen der Veröffentlichungsinstanz entspricht. (Siehe Eigenschaft [DispatcherCheckPeerCN](dispatcher-ssl.md#main-pars-title-11))

1. Öffnen Sie ein Terminalfenster und ändern Sie das aktuelle Verzeichnis in das Verzeichnis, in dem sich die Datei „CH.sh“ Ihrer OpenSSL-Bibliotheken befindet.
1. Geben Sie den folgenden Befehl ein und geben Sie Werte an, wenn Sie dazu aufgefordert werden. Falls erforderlich, verwenden Sie den Hostnamen der Veröffentlichungsinstanz als den allgemeinen Namen. Der Hostname ist ein von DNS auflösbarer Name für die IP-Adresse des Renderknotens:

   ```shell
   ./CA.sh -newreq
   ```

   Wenn Sie eine externe Zertifizierungsstelle in Anspruch nehmen, senden Sie die Datei „newreq.pem“ zum Signieren an die Zertifizierungsstelle. Wenn Sie als Zertifizierungsstelle fungieren, fahren Sie mit Schritt 3 fort.

1. Geben Sie den folgenden Befehl ein, um das Zertifikat mithilfe des Zertifikats Ihrer Zertifizierungsstelle zu signieren:

   ```shell
   ./CA.sh -sign
   ```

   Es werden zwei Dateien mit den Namen „newcert.pem“ und „newkey.pem“in dem Ordner erstellt, der Ihre Dateien für die Verwaltung der Zertifizierungsstelle enthält. Hierbei handelt es sich um das öffentliche Zertifikat und den privaten Schlüssel für den Rendercomputer.

1. Benennen Sie „newcert.pem“ in „rendercert.pem“ und „newkey.pem“ in „renderkey.pem“ um.
1. Wiederholen Sie die Schritte 2 und 3, um ein neues Zertifikat und einen neuen öffentlichen Schlüssel für das Dispatcher-Modul zu erstellen. Stellen Sie sicher, dass Sie einen allgemeinen Namen verwenden, der für die Dispatcher-Instanz spezifisch ist.
1. Benennen Sie „newcert.pem“ in „dispcert.pem“ und „newkey.pem“ in „dispkey.pem“ um.

### Konfigurieren von SSL auf dem Rendercomputer  {#configuring-ssl-on-the-render-computer}

Konfigurieren Sie SSL auf der Renderinstanz mithilfe der Dateien „rendercert.pem“ und „renderkey.pem“.

#### Konvertieren des Renderzertifikats in das JKS-Format  {#converting-the-render-certificate-to-jks-format}

Verwenden Sie den folgenden Befehl, um das Renderzertifikat, bei dem es sich um eine PEM-Datei handelt, in eine Datei im PKCS#12-Format zu konvertieren. Nehmen Sie außerdem das Zertifikat der Zertifizierungsstelle auf, die das Renderzertifikat signiert hat:

1. Ändern Sie in einem Terminalfenster das aktuelle Verzeichnis in den Speicherort des Renderzertifikats und des privaten Schlüssels.
1. Geben Sie den folgenden Befehl ein, um das Renderzertifikat, bei dem es sich um eine PEM-Datei handelt, in eine Datei im PKCS#12-Format zu konvertieren. Nehmen Sie außerdem das Zertifikat der Zertifizierungsstelle auf, die das Renderzertifikat signiert hat:

   ```shell
   openssl pkcs12 -export -in rendercert.pem -inkey renderkey.pem  -certfile demoCA/cacert.pem -out rendercert.p12
   ```

1. Geben Sie den folgenden Befehl ein, um die Datei im PKCS#12-Format in das JKS-Format (Java KeyStore) zu konvertieren:

   ```shell
   keytool -importkeystore -srckeystore servercert.p12 -srcstoretype pkcs12 -destkeystore render.keystore
   ```

1. Der Java-Keystore wird mithilfe eines Standardalias erstellt. Ändern Sie den Alias (falls gewünscht):

   ```shell
   keytool -changealias -alias 1 -destalias jettyhttp -keystore render.keystore
   ```

#### Hinzufügen des Zertifizierungsstellenzertifikats zum Render-TrustStore  {#adding-the-ca-cert-to-the-render-s-truststore}

Wenn Sie als Zertifizierungsstelle fungieren, importieren Sie das Zertifizierungsstellenzertifikat in einen Keystore. Konfigurieren Sie anschließend die JVM, in der die Renderinstanz ausgeführt wird, sodass sie dem Keystore vertraut.

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (sbroders@adobe.com)
Last Modified Date: 2014-08-12T13:11:21.401-0400

<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">The jetty http service has properties to specify trusted CA certificates for mutual SSL for 6.0. Whether they are operable is undetetermined. See https://issues.adobe.com/browse/DOC-4738.</p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;"> </p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">For 5.6.1, you would specify the system property javax.net.ssl.trustStore, using the path to cacerts as value.</p>

 -->

1. Verwenden Sie einen Text-Editor, um die Datei „cacert.pem“ zu öffnen und den gesamten Text zu entfernen, der der folgenden Zeile vorangeht:

   `-----BEGIN CERTIFICATE-----`

1. Verwenden Sie den folgenden Befehl, um das Zertifikat in den Keystore zu importieren:

   ```shell
   keytool -import -keystore cacerts.keystore -alias myca -storepass password -file cacert.pem
   ```

1. Um die JVM zu konfigurieren, in der die Renderinstanz ausgeführt wird, sodass sie dem Keystore vertraut, verwenden Sie die folgende Systemeigenschaft:

   ```shell
   -Djavax.net.ssl.trustStore=<location of cacerts.keystore>
   ```

   Wenn Sie beispielsweise das Skript „crx-quickstart/bin/quickstart script“ verwenden, um Ihre Veröffentlichungsinstanz zu starten, können Sie die Eigenschaft „CQ_JVM_OPTS“ ändern:

   ```shell
   CQ_JVM_OPTS='-server -Xmx2048m -XX:MaxPermSize=512M -Djavax.net.ssl.trustStore=/usr/lib/cq6.0/publish/ssl/cacerts.keystore'
   ```

#### Konfigurieren der Renderinstanz  {#configuring-the-render-instance}

Verwenden Sie das Renderzertifikat mit den Anweisungen im Abschnitt *Aktivieren von SSL auf der Veröffentlichungsinstanz*, um den HTTP-Dienst der Renderinstanz für die Verwendung von SSL zu konfigurieren:

* AEM 6.2: [Aktivieren von HTTP über SSL](https://helpx.adobe.com/experience-manager/6-2/sites/deploying/using/config-ssl.html)
* AEM 6.1: [Aktivieren von HTTP über SSL](https://docs.adobe.com/content/docs/de/aem/6-1/deploy/configuring/config-ssl.html)
* Ältere AEM-Versionen: Siehe [diese Seite.](https://helpx.adobe.com/experience-manager/aem-previous-versions.html)

### Konfigurieren von SSL für das Dispatcher-Modul {#configuring-ssl-for-the-dispatcher-module}

Um den Dispatcher für die Verwendung der bidirektionalen SSL-Kommunikation zu konfigurieren, bereiten Sie das Dispatcher-Zertifikat vor und konfigurieren Sie dann das Webservermodul.

### Erstellen eines einheitlichen Dispatcher-Zertifikats  {#creating-a-unified-dispatcher-certificate}

Führen Sie das Dispatcher-Zertifikat und den unverschlüsselten privaten Schlüssel in einer einzelnen PEM-Datei zusammen. Verwenden Sie einen Text-Editor oder den Befehl `cat`, um eine Datei wie im folgenden Beispiel zu erstellen:

1. Öffnen Sie ein Terminalfenster und ändern Sie das aktuelle Verzeichnis in den Speicherort der Datei „dispkey.pem“.
1. Geben Sie zum Entschlüsseln des privaten Schlüssels den folgenden Befehl ein:

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

