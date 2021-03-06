---
title: Verwenden des Moduls Spatial IO von Azure Maps | Microsoft Azure Maps
description: Erfahren Sie, wie Sie das Modul Spatial IO verwenden, das vom Azure Maps Web SDK bereitgestellt wird. Dieses Modul bietet zuverlässige Features, die es Entwicklern erleichtern, räumliche Daten in das Azure Maps Web SDK zu integrieren.
author: farah-alyasari
ms.author: v-faalya
ms.date: 02/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: c309473529666d369e8accd1617021249867fb19
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370393"
---
# <a name="how-to-use-the-azure-maps-spatial-io-module"></a>Verwenden des Moduls Spatial IO von Azure Maps

Das Azure Maps Web SDK bietet das Modul **Spatial IO** , das räumliche Daten mittels JavaScript oder TypeScript in das Azure Maps Web SDK integriert. Die stabilen Features in diesem Modul ermöglichen Entwicklern Folgendes:

- [Lesen und Schreiben von Daten in übliche räumliche Dateien](spatial-io-read-write-spatial-data.md). Folgende Dateiformate werden unterstützt: KML, KMZ, GPX, GeoRSS, GML und CSV-Dateien mit Spalten mit räumlichen Informationen.
- [Verbinden mit OGC-Diensten (Open Geospatial Consortium) und Integrieren in das Azure Maps Web SDK. Überlagern von Web Mapping Services (WMS) und Web Map Tile Services (WMTS) als Ebenen auf der Karte](spatial-io-add-ogc-map-layer.md).
- [Abfragen von Daten in einem WFS-Dienst (Web Feature Service)](spatial-io-connect-wfs-service.md).
- [Überlagern komplexer Datasets mit Stilinformationen und deren automatisches Rendering.](spatial-io-add-simple-data-layer.md)
- [Nutzen sehr schneller Klassen zum Lesen und Schreiben von XML-Dateien und Dateien mit Trennzeichen](spatial-io-core-operations.md).

In diesem Leitfaden erfahren Sie, wie Sie das Modul Spatial IO in eine Webanwendung integrieren und verwenden können.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie das Modul Spatial IO einsetzen können, müssen Sie [ein Azure Maps-Konto einrichten](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#create-an-account-with-azure-maps) und [den primären Abonnementschlüssel für Ihr Konto ](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#get-the-primary-key-for-your-account) abrufen.

## <a name="installing-the-spatial-io-module"></a>Installieren des Moduls Spatial IO

Sie können das Azure Maps-Modul Spatial IO mit einer der beiden folgenden Optionen laden:

* Das global gehostete Azure CDN für das Azure Maps-Modul Spatial IO. Für diese Option fügen Sie dem Element `<head>` der HTML-Datei einen Verweis auf den JavaScript-Code hinzu.

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

* Der Quellcode für [azure-maps-spatial-io](https://www.npmjs.com/package/azure-maps-spatial-io) kann lokal geladen und dann mit Ihrer App gehostet werden. Dieses Paket enthält außerdem TypeScript-Definitionen. Verwenden Sie für diese Option den folgenden Befehl, um das Paket zu installieren:

    ```sh
    npm install azure-maps-spatial-io
    ```

    Fügen Sie dann im Element `<head>` des HTML-Dokuments einen Verweis auf den JavaScript-Code hinzu:

    ```html
    <script src="node_modules/azure-maps-spatial-io/dist/atlas-spatial.min.js"></script>
    ```

## <a name="using-the-spatial-io-module"></a>Verwenden des Moduls Spatial IO

1. Erstellen Sie eine neue HTML-Datei.

2. Laden Sie das Azure Maps Web SDK, und initialisieren Sie das Kartensteuerelement. Unter [Verwenden des Azure Maps-Kartensteuerelements](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control) erhalten Sie weitere Informationen. Nach diesem Schritt sollte Ihre HTML-Datei so aussehen:

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <script type='text/javascript'>

            var map;

            function GetMap() {
                //Initialize a map instance.
                map = new atlas.Map('myMap', {
                    view: 'Auto',

                    //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });

                //Wait until the map resources are ready.
                map.events.add('ready', function() {

                    // Write your code here to make sure it runs once the map resources are ready

                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

2. Laden Sie das Azure Maps-Modul Spatial IO. Verwenden Sie für diese Übung das CDN für das Azure Maps-Modul Spatial IO. Fügen Sie den nachstehenden Verweis dem Element `<head>` Ihrer HTML-Datei hinzu:

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

3. Initialisieren Sie eine `datasource`, und fügen Sie die Datenquelle der Karte hinzu. Initialisieren Sie eine `layer`, und fügen Sie die Datenquelle der Kartenebene hinzu. Rendern Sie anschließend sowohl die Datenquelle als auch die Ebene. Bevor Sie nach unten scrollen, um im nächsten Schritt den vollständigen Code einzusehen, überlegen Sie sich, an welcher Stelle Sie die Codeausschnitte für Datenquelle und Ebene am besten platzieren. Denken Sie daran, dass wir, bevor wir die Karte programmgesteuert bearbeiten, abwarten sollten, bis die Kartenressourcen bereit sind.

    ```javascript
    var datasource, layer;
    ```

    and

    ```javascript
    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);
    
    //Add a simple data layer for rendering the data.
    layer = new atlas.layer.SimpleDataLayer(datasource);
    map.layers.add(layer);
    ```

4. Im Ergebnis sollte Ihre HTML-Datei wie der folgende Code aussehen. Dieses Beispiel zeigt, wie eine XML-Datei über eine URL gelesen werden kann. Laden Sie danach die Featuredaten der Datei in die Karte, und zeigen Sie sie an. 

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title>Spatial IO Module Example</title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <!-- Add reference to the Azure Maps Spatial IO module. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

        <script type='text/javascript'>
            var map, datasource, layer;

            function GetMap() {
                //Initialize a map instance.
                map = new atlas.Map('myMap', {
                    view: 'Auto',

                    //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });

                //Wait until the map resources are ready.
                map.events.add('ready', function() {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Add a simple data layer for rendering the data.
                    layer = new atlas.layer.SimpleDataLayer(datasource);
                    map.layers.add(layer);

                    //Read an XML file from a URL or pass in a raw XML string.
                    atlas.io.read('superCoolKmlFile.xml').then(r => {
                        if (r) {
                            //Add the feature data to the data source.
                            datasource.add(r);

                            //If bounding box information is known for data, set the map view to it.
                            if (r.bbox) {
                                map.setCamera({
                                    bounds: r.bbox,
                                    padding: 50
                                });
                            }
                        }
                    });
                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

5. Denken Sie daran, `<Your Azure Maps Key>` durch Ihren Primärschlüssel zu ersetzen. Öffnen Sie Ihre HTML-Datei. Das Ergebnis ist vergleichbar mit der folgenden Abbildung:

    <center>

    ![Beispiel für räumliche Daten](./media/how-to-use-spatial-io-module/spatial-data-example.png)

    </center>

## <a name="next-steps"></a>Nächste Schritte

Das hier vorgestellte Feature ist nur eines der vielen, die im Modul Spatial IO zur Verfügung stehen. Lesen Sie die nachstehenden Anleitungen, um zu erfahren, wie Sie andere Möglichkeiten des Moduls Spatial IO nutzen können:

> [!div class="nextstepaction"]
> [Hinzufügen einer einfachen Datenebene](spatial-io-add-simple-data-layer.md)

> [!div class="nextstepaction"]
> [Lesen und Schreiben räumlicher Daten](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer OGC-Kartenebene](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Herstellen einer Verbindung mit einem WFS-Dienst](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Nutzen von Kernvorgängen](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Details zu unterstützten Datenformaten](spatial-io-supported-data-format-details.md)

Weitere Informationen finden Sie in der Dokumentation zum Azure Maps-Modul Spatial IO:

> [!div class="nextstepaction"]
> [Spatial IO-Paket von Azure Maps](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/)
