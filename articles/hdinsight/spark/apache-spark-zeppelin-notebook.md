---
title: Zeppelin Notebooks und Apache Spark-Cluster – Azure HDInsight
description: Enthält eine Schritt-für-Schritt-Anleitung zur Verwendung von Zeppelin Notebooks mit Apache Spark-Clustern unter Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: e313048986beca1991e38ce2e65ea12f954170d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598271"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Verwenden von Apache Zeppelin Notebooks mit Apache Spark-Cluster in Azure HDInsight

HDInsight Spark-Cluster enthalten [Apache Zeppelin](https://zeppelin.apache.org/) Notebooks, mit denen Sie [Apache Spark](https://spark.apache.org/)-Aufträge ausführen können. In diesem Artikel wird beschrieben, wie Sie das Zeppelin Notebook in einem HDInsight-Cluster verwenden.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Apache Spark-Cluster unter HDInsight. Eine Anleitung finden Sie unter [Erstellen von Apache Spark-Clustern in Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Das URI-Schema für Ihren primären Clusterspeicher. Dies ist `wasb://` für Azure Blob Storage, `abfs://` für Azure Data Lake Storage Gen2 oder `adl://` für Azure Data Lake Storage Gen1. Wenn die sichere Übertragung für Blob Storage aktiviert ist, lautet der URI `wasbs://`.  Weitere Informationen finden Sie unter [Vorschreiben einer sicheren Übertragung in Azure Storage](../../storage/common/storage-require-secure-transfer.md).

## <a name="launch-an-apache-zeppelin-notebook"></a>Starten des Apache Zeppelin Notebooks

1. Wählen Sie im Spark-Cluster **Übersicht** unter **Clusterdashboards** die Option **Zeppelin-Notebook**. Geben Sie die Administratoranmeldeinformationen für den Cluster ein.  

   > [!NOTE]  
   > Sie können auch das Zeppelin Notebook für Ihren Cluster aufrufen, indem Sie in Ihrem Browser die folgende URL öffnen. Ersetzen Sie **CLUSTERNAME** durch den Namen Ihres Clusters:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Erstellen Sie ein neues Notebook. Navigieren Sie im Headerbereich zu **Notebook** > **Neue Notiz erstellen**.

    ![Erstellen eines neuen Zeppelin Notebooks](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "Erstellen eines neuen Zeppelin Notebooks")

    Geben Sie einen Namen für das Notebook ein, und wählen Sie anschließend **Notiz erstellen**.

3. Stellen Sie sicher, dass im Header des Notebooks der Status „Verbunden“ angezeigt wird. Dies wird durch einen grünen Punkt in der oberen rechten Ecke angezeigt.

    ![Zeppelin Notebook-Status](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Zeppelin Notebook-Status")

4. Laden Sie Beispieldaten in eine temporäre Tabelle. Wenn Sie einen Spark-Cluster in HDInsight erstellen, wird die Beispieldatei `hvac.csv` in das zugeordnete Speicherkonto unter `\HdiSamples\SensorSampleData\hvac` kopiert.

    Fügen Sie in den leeren Absatz, der im neuen Notebook standardmäßig erstellt wird, den folgenden Codeausschnitt ein.

    ```scala
    %livy2.spark
    //The above magic instructs Zeppelin to use the Livy Scala interpreter

    // Create an RDD using the default Spark context, sc
    val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    // Define a schema
    case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)

    // Map the values in the .csv file to the schema
    val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
        s => Hvac(s(0),
                s(1),
                s(2).toInt,
                s(3).toInt,
                s(6)
        )
    ).toDF()

    // Register as a temporary table called "hvac"
    hvac.registerTempTable("hvac")
    ```

    Drücken Sie **UMSCHALT+EINGABETASTE**, oder wählen Sie die Schaltfläche **Wiedergeben** für den Absatz aus, um den Codeausschnitt auszuführen. Der Status in der rechten Ecke des Absatzes sollte sich entsprechend ändern: BEREIT, AUSSTEHEND, WIRD AUSGEFÜHRT bis zu BEENDET. Die Ausgabe wird unten im Absatz angezeigt. Der Screenshot sieht folgendermaßen aus:

    ![Erstellen einer temporären Tabelle aus Rohdaten](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "Erstellen einer temporären Tabelle aus Rohdaten")

    Sie können auch einen Titel für jeden Absatz angeben. Wählen Sie in der rechten Ecke des Absatzes das Symbol **Einstellungen** (Zahnrad) und dann **Titel anzeigen**.  

    > [!NOTE]  
    > Der %spark2-Interpreter wird in Zeppelin-Notebooks bei keiner HDInsight-Version unterstützt, und der %sh-Interpreter wird ab HDInsight 4.0 nicht unterstützt.

5. Sie können jetzt Spark SQL-Anweisungen für die Tabelle `hvac` ausführen. Fügen Sie die folgende Abfrage in einen neuen Absatz ein. Mit der Abfrage werden die Gebäude-ID und der Unterschied zwischen den Ziel- und Ist-Temperaturen für jedes Gebäude an einem bestimmten Datum abgerufen. Drücken Sie **UMSCHALT+EINGABETASTE**.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"
    ```  

    Mit der **%sql**-Anweisung am Anfang wird das Notebook angewiesen, den Livy Scala-Interpreter zu verwenden.

6. Wählen Sie das Symbol **Balkendiagramm** aus, um die Anzeige zu ändern.  Unter der Option **Einstellungen**, die nach dem Auswählen von **Balkendiagramm** angezeigt wird, können Sie **Schlüssel** und **Werte** auswählen.  Im folgenden Screenshot ist die Ausgabe dargestellt.

    ![Ausführen einer Spark-SQL-Anweisung mit dem Notebook: 1](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "Ausführen einer Spark-SQL-Anweisung mit dem Notebook: 1")

7. Sie können auch Spark-SQL-Anweisungen ausführen, indem Sie die Variablen in der Abfrage verwenden. Der nächste Codeausschnitt zeigt, wie Sie eine Variable (`Temp`) in der Abfrage mit den möglichen Werten definieren, die für die Abfrage verwendet werden sollen. Beim ersten Ausführen der Abfrage wird automatisch eine Dropdownliste mit den Werten aufgefüllt, die Sie für die Variable angegeben haben.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    Fügen Sie diesen Codeausschnitt in einen neuen Absatz ein, und drücken Sie **UMSCHALT+EINGABETASTE**. Wählen Sie anschließend in der Dropdownliste **Temp** den Wert **65** aus.

8. Wählen Sie das Symbol **Balkendiagramm** aus, um die Anzeige zu ändern.  Wählen Sie anschließend **Einstellungen**, und nehmen Sie die folgenden Änderungen vor:

   * **Gruppen:**  Fügen Sie **targettemp** hinzu.  
   * **Werte:** 1. Entfernen Sie **date**.  2. Fügen Sie **temp_diff** hinzu.  3.  Ändern Sie den Aggregator von **SUM** in **AVG**.  

     Im folgenden Screenshot ist die Ausgabe dargestellt.

     ![Ausführen einer Spark-SQL-Anweisung mit dem Notebook: 2](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Ausführen einer Spark-SQL-Anweisung mit dem Notebook: 2")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Wie verwende ich externe Pakete mit dem Notebook?

Sie können das Zeppelin Notebook in einem Apache Spark-Cluster in HDInsight konfigurieren, um externe, von der Community bereitgestellte Pakete zu verwenden, die nicht im Lieferumfang des Clusters enthalten sind. Sie können das [Maven Repository](https://search.maven.org/) nach einer vollständigen Liste der verfügbaren Pakete durchsuchen. Sie können die Liste der verfügbaren Pakete auch aus anderen Quellen abrufen. Beispielsweise steht eine vollständige Liste der von der Community bereitgestellten Pakete auf [Spark-Pakete](https://spark-packages.org/)zur Verfügung.

In diesem Artikel wird beschrieben, wie Sie das Paket [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) mit Jupyter Notebook verwenden.

1. Öffnen Sie die Einstellungen des Interpreters. Wählen Sie in der Ecke oben rechts den Namen des angemeldeten Benutzers und dann **Interpreter** aus.

    ![Starten des Interpreters](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive-Ausgabe")

2. Scrollen Sie zu **livy2**, und wählen Sie dann **edit** aus.

    ![Ändern der Einstellungen des Interpreters: 1](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Ändern der Einstellungen des Interpreters: 1")

3. Navigieren Sie zum Schlüssel `livy.spark.jars.packages`, und legen Sie seinen Wert im Format `group:id:version` fest. Wenn Sie das Paket [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) verwenden möchten, müssen Sie den Wert des Schlüssels auf `com.databricks:spark-csv_2.10:1.4.0` festlegen.

    ![Ändern der Einstellungen des Interpreters: 2](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Ändern der Einstellungen des Interpreters: 2")

    Wählen Sie **Speichern** und dann **OK** aus, um den Livy-Interpreter neu zu starten.

4. Hier ist angegeben, wie Sie zum Wert des oben eingegebenen Schlüssels gelangen, falls dies für Sie interessant ist.

    a. Suchen Sie das Paket im Maven-Repository. In diesem Artikel verwendeten wir [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).

    b. Sammeln Sie im Repository die Werte für **GroupId**, **ArtifactId** und **Version**.

    ![Verwenden von externen Paketen mit Jupyter Notebook](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Verwenden von externen Paketen mit Jupyter Notebook")

    c. Verketten Sie die drei Werte, getrennt durch einen Doppelpunkt ( **:** ).

        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Wo werden Zeppelin Notebooks gespeichert?

Die Zeppelin Notebooks werden in den Clusterhauptknoten gespeichert. Wenn Sie den Cluster löschen, werden also auch die Notebooks gelöscht. Falls Sie Ihre Notebooks zur späteren Verwendung auf anderen Clustern beibehalten möchten, müssen Sie sie exportieren, nachdem Sie die Ausführung der Aufträge abgeschlossen haben. Wählen Sie zum Exportieren eines Notebooks das Symbol **Exportieren**. Dies ist in der Abbildung unten dargestellt.

![Herunterladen des Notebooks](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Herunterladen des Notebooks")

Das Notebook wird als JSON-Datei in Ihrem Downloadverzeichnis gespeichert.

## <a name="use-shiro-to-configure-access-to-zeppelin-interpreters-in-enterprise-security-package-esp-clusters"></a>Verwenden von Shiro zum Konfigurieren des Zugriffs auf Zeppelin-Interpreter in ESP-Clustern (Enterprise-Sicherheitspaket)
Wie bereits erwähnt, wird der `%sh`-Interpreter ab HDInsight 4.0 nicht unterstützt. Da der `%sh`-Interpreter potenzielle Sicherheitsprobleme bewirkt, z. B. Zugriff auf Keytabs mithilfe von Shellbefehlen, wurde er auch aus HDInsight 3.6 ESP-Clustern entfernt. Das bedeutet, dass der `%sh`-Interpreter beim Klicken auf **Neue Notiz erstellen** oder auf der Interpreter-Benutzeroberfläche standardmäßig nicht verfügbar ist. 

Privilegierte Domänenbenutzer können mithilfe der Datei `Shiro.ini` den Zugriff auf die Interpreter-Benutzeroberfläche steuern. Daher können nur diese Benutzer neue `%sh`-Interpreter erstellen und für jeden neuen `%sh`-Interpreter Berechtigungen festlegen. Führen Sie die folgenden Schritte aus, um den Zugriff mithilfe der Datei `shiro.ini` zu steuern:

1. Definieren Sie eine neue Rolle mit dem Namen einer vorhandenen Domänengruppe. Im folgenden Beispiel ist `adminGroupName` eine Gruppe privilegierter Benutzer in AAD. Verwenden Sie keine Sonderzeichen oder Leerzeichen im Gruppennamen. Die Zeichen nach `=` geben die Berechtigungen für diese Rolle an. `*` bedeutet, dass die Gruppe über vollständige Berechtigungen verfügt.

    ```
    [roles]
    adminGroupName = *
    ```

2. Fügen Sie die neue Rolle für den Zugriff auf Zeppelin-Interpreter hinzu. Im folgenden Beispiel erhalten alle Benutzer in `adminGroupName` Zugriff auf Zeppelin-Interpreter und können neue Interpreter erstellen. Sie können mehrere Rollen, getrennt durch Kommas, in die eckigen Klammern von `roles[]` einfügen. Benutzer, die über die erforderlichen Berechtigungen verfügen, können dann auf Zeppelin-Interpreter zugreifen.

    ```
    [urls]
    /api/interpreter/** = authc, roles[adminGroupName]
    ```

## <a name="livy-session-management"></a>Livy-Sitzungsverwaltung

Wenn Sie den ersten Codeabsatz in Ihrem Zeppelin-Notebook ausführen, wird in Ihrem HDInsight Spark-Cluster eine neue Livy-Sitzung erstellt. Diese Sitzung kann für alle Zeppelin Notebooks gemeinsam verwendet werden, die Sie erstellen. Falls die Livy-Sitzung aus irgendeinem Grund beendet wird (Clusterneustart usw.), können Sie keine Aufträge über das Zeppelin Notebook ausführen.

In diesem Fall müssen Sie die folgenden Schritte ausführen, bevor Sie mit dem Ausführen von Aufträgen über ein Zeppelin Notebook beginnen können.  

1. Starten Sie den Livy-Interpreter über das Zeppelin Notebook neu. Öffnen Sie zu diesem Zweck die Einstellungen des Interpreters, indem Sie oben rechts den Namen des angemeldeten Benutzers und dann **Interpreter** auswählen.

    ![Starten des Interpreters](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive-Ausgabe")

2. Scrollen Sie zu **livy2**, und wählen Sie dann **restart** aus.

    ![Neustarten des Livy-Interpreters](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Neustarten des Zeppelin-Interpreters")

3. Führen Sie eine Codezelle über ein vorhandenes Zeppelin Notebook aus. Im HDInsight-Cluster wird eine neue Livy-Sitzung erstellt.

## <a name="general-information"></a>Allgemeine Informationen

### <a name="validate-service"></a>Überprüfen des Diensts

Zum Überprüfen des Diensts über Ambari navigieren Sie zu `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary`, wobei CLUSTERNAME der Name Ihres Clusters ist.

Zum Überprüfen des Diensts über eine Befehlszeile stellen Sie eine SSH-Verbindung zum Hauptknoten her. Ändern Sie mit dem Befehl `sudo su zeppelin` den Benutzer in Zeppelin. Statusbefehle:

|Get-Help |BESCHREIBUNG |
|---|---|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh status`|Dienststatus|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh --version`|Dienstversion|
|`ps -aux | grep zeppelin`|Identifizieren der PID|

### <a name="log-locations"></a>Protokollspeicherorte

|Dienst |`Path` |
|---|---|
|Zeppelin-Server|/usr/hdp/current/zeppelin-server/|
|Serverprotokolle|/var/log/zeppelin|
|Configuration Interpreter, Shiro, site.xml, log4j|/usr/hdp/current/zeppelin-server/conf or /etc/zeppelin/conf|
|PID-Verzeichnis|/var/run/zeppelin|

### <a name="enable-debug-logging"></a>Debugprotokollierung aktivieren

1. Navigieren Sie zu `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary`, wobei CLUSTERNAME der Name Ihres Clusters ist.

1. Navigieren Sie zu **CONFIGS** > **Advanced zeppelin-log4j-properties** > **log4j_properties_content**.

1. Ändern Sie `log4j.appender.dailyfile.Threshold = INFO` in `log4j.appender.dailyfile.Threshold = DEBUG`.

1. Fügen Sie `log4j.logger.org.apache.zeppelin.realm=DEBUG`hinzu.

1. Speichern Sie die Änderungen, und starten Sie den Dienst neu.

## <a name="next-steps"></a>Nächste Schritte

[Übersicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Szenarien

* [Apache Spark mit BI: Durchführen interaktiver Datenanalysen mithilfe von Spark in HDInsight mit BI-Tools](apache-spark-use-bi-tools.md)
* [Apache Spark mit Machine Learning: Analysieren von Gebäudetemperaturen mithilfe von Spark in HDInsight und HVAC-Daten](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark mit Machine Learning: Vorhersage von Lebensmittelkontrollergebnissen mithilfe von Spark in HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Websiteprotokollanalyse mithilfe von Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Erstellen und Ausführen von Anwendungen

* [Erstellen einer eigenständigen Anwendung mit Scala](apache-spark-create-standalone-application.md)
* [Ausführen von Remoteaufträgen in einem Apache Spark-Cluster mithilfe von Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Tools und Erweiterungen

* [Verwenden des HDInsight-Tools-Plug-Ins für IntelliJ IDEA zum Erstellen und Übermitteln von Apache Spark Scala-Anwendungen](apache-spark-intellij-tool-plugin.md)
* [Verwenden des HDInsight-Tools-Plug-Ins für IntelliJ IDEA zum Remotedebuggen von Apache Spark-Anwendungen](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Kernel für Jupyter Notebook in Apache Spark-Clustern für HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Verwenden von externen Paketen mit Jupyter Notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installieren von Jupyter Notebook auf Ihrem Computer und Herstellen einer Verbindung zum Apache Spark-Cluster in Azure HDInsight (Vorschau)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Ressourcen verwalten

* [Verwalten von Ressourcen für den Apache Spark-Cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Nachverfolgen und Debuggen von Aufträgen in einem Apache Spark-Cluster unter HDInsight)](apache-spark-job-debugging.md)
