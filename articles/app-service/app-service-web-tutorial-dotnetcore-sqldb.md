---
title: 'Tutorial: ASP.NET Core mit SQL-Datenbank'
description: Informationen zum Ausführen einer .NET Core-App in Azure App Service mit einer Verbindung mit einer SQL-Datenbank
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 08/06/2019
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: 9a3247298ed69cdefb3ce5021f0c4051052105f7
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80297799"
---
# <a name="tutorial-build-an-aspnet-core-and-sql-database-app-in-azure-app-service"></a>Tutorial: Erstellen einer ASP.NET Core- und SQL-Datenbank-App in Azure App Service

> [!NOTE]
> In diesem Artikel wird eine App in App Service unter Windows bereitgestellt. Informationen zur Bereitstellung in App Service unter _Linux_ finden Sie unter [Erstellen einer .NET Core- und SQL-Datenbank-Web-App in Azure App Service für Linux](./containers/tutorial-dotnetcore-sqldb-app.md).
>

[App Service](overview.md) bietet einen hochgradig skalierbaren Webhostingdienst mit Self-Patching in Azure. In diesem Tutorial wird gezeigt, wie Sie eine .NET Core-App erstellen und mit einer SQL-Datenbank verbinden. Wenn Sie diese Schritte ausgeführt haben, verfügen Sie über eine .NET Core MVC-App, die in App Service ausgeführt wird.

![In App Service ausgeführte App](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

Folgendes wird vermittelt:

> [!div class="checklist"]
> * Erstellen einer SQL-Datenbank in Azure
> * Herstellen einer Verbindung einer .NET Core-App mit SQL-Datenbank
> * Bereitstellen der Anwendung in Azure
> * Aktualisieren des Datenmodells und erneutes Bereitstellen der App
> * Streamen von Diagnoseprotokollen aus Azure
> * Verwalten der App im Azure-Portal

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* [Installation von Git](https://git-scm.com/)
* [Installation des .NET Core SDK](https://dotnet.microsoft.com/download)

## <a name="create-local-net-core-app"></a>Erstellen einer lokalen .NET Core-App

In diesem Schritt richten Sie das lokale .NET Core-Projekt ein.

### <a name="clone-the-sample-application"></a>Klonen der Beispielanwendung

Wechseln Sie im Terminalfenster mit `cd` in ein Arbeitsverzeichnis.

Führen Sie die folgenden Befehle aus, um das Beispielrepository zu klonen und in seinen Stamm zu wechseln.

```bash
git clone https://github.com/azure-samples/dotnetcore-sqldb-tutorial
cd dotnetcore-sqldb-tutorial
```

Das Beispielprojekt enthält eine einfache CRUD-App (create-read-update-delete, erstellen-lesen-aktualisieren-löschen), die auf [Entity Framework Core](https://docs.microsoft.com/ef/core/) basiert.

### <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie die folgenden Befehle aus, um die erforderlichen Pakete zu installieren, Datenbankmigrationen auszuführen und die Anwendung zu starten.

```bash
dotnet tool install -g dotnet-ef --version 3.1.1
dotnet-ef database update
dotnet run
```

Navigieren Sie in einem Browser zu `http://localhost:5000`. Wählen Sie den Link **Neu erstellen**, und erstellen Sie einige _Aufgaben_-Elemente.

![Erfolgreiche Verbindung mit SQL-Datenbank](./media/app-service-web-tutorial-dotnetcore-sqldb/local-app-in-browser.png)

Sie können .NET Core jederzeit beenden, indem Sie im Terminal `Ctrl+C` drücken.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-production-sql-database"></a>Erstellen der SQL-Datenbank für die Produktion

In diesem Schritt erstellen Sie eine SQL-Datenbank in Azure. Wenn Ihre App in Azure bereitgestellt ist, nutzt sie diese Clouddatenbank.

Für SQL-Datenbank wird in diesem Tutorial [Dokumentation zu Azure SQL-Datenbank](/azure/sql-database/) verwendet.

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>Erstellen eines logischen SQL-Datenbankservers

Erstellen Sie in Cloud Shell mit dem Befehl [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create) einen logischen SQL-Datenbankserver.

Ersetzen Sie den *\<server_name>* -Platzhalter durch einen eindeutigen Namen für die SQL-Datenbank. Dieser Name wird als Teil des SQL-Datenbank-Endpunkts (`<server_name>.database.windows.net`) verwendet, daher muss er für alle logischen Server in Azure eindeutig sein. Der Name darf nur Kleinbuchstaben, Ziffern und Bindestriche (-) enthalten, und er muss zwischen 3 und 50 Zeichen lang sein. Ersetzen Sie auch *\<db_username>* und *\<db_password>* mit einem Benutzernamen und einem Kennwort Ihrer Wahl. 


```azurecli-interactive
az sql server create --name <server_name> --resource-group myResourceGroup --location "West Europe" --admin-user <db_username> --admin-password <db_password>
```

Nach dem Erstellen des logischen SQL-Datenbankservers zeigt die Azure-Befehlszeilenschnittstelle Informationen wie im folgenden Beispiel an:

```json
{
  "administratorLogin": "sqladmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/<server_name>",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "<server_name>",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
```

### <a name="configure-a-server-firewall-rule"></a>Konfigurieren einer Serverfirewallregel

Erstellen Sie mit dem Befehl [`az sql server firewall create`](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az-sql-server-firewall-rule-create) eine [Azure SQL-Datenbank-Firewallregel auf Serverebene](../sql-database/sql-database-firewall-configure.md). Wenn sowohl Start- als auch End-IP auf 0.0.0.0 festgelegt ist, wird die Firewall nur für andere Azure-Ressourcen geöffnet. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server_name> --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Sie können Ihre Firewallregel auch noch restriktiver gestalten und [nur die ausgehenden IP-Adressen verwenden, die Ihre App verwendet](overview-inbound-outbound-ips.md#find-outbound-ips).
>

### <a name="create-a-database"></a>Erstellen einer Datenbank

Erstellen Sie mit dem Befehl [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-create) eine Datenbank mit der [Leistungsstufe „S0“](../sql-database/sql-database-service-tiers-dtu.md) auf dem Server.

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server_name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Erstellen der Verbindungszeichenfolge

Ersetzen Sie die folgende Zeichenfolge mit *\<server_name>* , *\<db_username>* und *\<db_password>* , die Sie zuvor verwendet haben.

```
Server=tcp:<server_name>.database.windows.net,1433;Database=coreDB;User ID=<db_username>;Password=<db_password>;Encrypt=true;Connection Timeout=30;
```

Dies ist die Verbindungszeichenfolge für Ihre .NET Core-App. Kopieren Sie sie für die spätere Verwendung.

## <a name="deploy-app-to-azure"></a>Bereitstellen von Apps in Azure

In diesem Schritt stellen Sie die mit der SQL-Datenbank verbundene .NET Core-Anwendung für App Service bereit.

### <a name="configure-local-git-deployment"></a>Konfigurieren der lokalen Git-Bereitstellung

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Wie erstelle ich einen Plan?

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>Erstellen einer Web-App

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)] 

### <a name="configure-connection-string"></a>Konfigurieren der Verbindungszeichenfolge

Verwenden Sie zum Festlegen von Verbindungszeichenfolgen für Ihre Azure-App den Befehl [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) in Cloud Shell. Ersetzen Sie im folgenden Befehl sowohl *\<app name>* als auch den *\<connection_string>* -Parameter mit der Verbindungszeichenfolge, die Sie zuvor erstellt haben.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection="<connection_string>" --connection-string-type SQLServer
```

In ASP.NET Core können Sie diese Verbindungszeichenfolge (`MyDbConnection`) genau wie eine Verbindungszeichenfolge in *appsettings.json* mit dem Standardmuster verwenden. In diesem Fall wird `MyDbConnection` ebenfalls in der Datei *appsettings.json* definiert. Bei der Ausführung in App Service hat die in App Service definierte Verbindungszeichenfolge Vorrang vor der in der Datei *appsettings.json* definierten Verbindungszeichenfolge. Im Rahmen der lokalen Entwicklung verwendet der Code den Wert *appsettings.json*. Bei der Bereitstellung verwendet der gleiche Code dann allerdings den App Service-Wert.

Informationen dazu, wie im Code auf die Verbindungszeichenfolge verwiesen wird, finden Sie unter [Herstellen der Verbindung mit SQL-Datenbank in der Produktion](#connect-to-sql-database-in-production).

### <a name="configure-environment-variable"></a>Konfigurieren einer Umgebungsvariablen

Legen Sie als Nächstes für die `ASPNETCORE_ENVIRONMENT`-App-Einstellung _Production_ fest. Diese Einstellung teilt Ihnen mit, ob die Ausführung in Azure stattfindet, da Sie SQLite für Ihre lokale Entwicklungsumgebung und die SQL-Datenbank für Ihre Azure-Umgebung verwenden.

Im folgenden Beispiel wird die App-Einstellung `ASPNETCORE_ENVIRONMENT` in der Azure-App konfiguriert. Ersetzen Sie den Platzhalter *\<app_name>* .

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings ASPNETCORE_ENVIRONMENT="Production"
```

Informationen dazu, wie im Code auf die Umgebungsvariable verwiesen wird, finden Sie unter [Herstellen der Verbindung mit SQL-Datenbank in der Produktion](#connect-to-sql-database-in-production).

### <a name="connect-to-sql-database-in-production"></a>Herstellen der Verbindung mit SQL-Datenbank in der Produktion

Öffnen Sie in Ihrem lokalen Repository „Startup.cs“, und suchen Sie den folgenden Code:

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

Ersetzen sie ihn durch den folgenden Code, der die Umgebungsvariablen verwendet, die Sie zuvor konfiguriert haben.

```csharp
// Use SQL Database if in Azure, otherwise, use SQLite
if(Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlite("Data Source=localdatabase.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

Wenn dieser Code erkennt, dass er in der Produktion ausgeführt wird (was die Azure-Umgebung angibt), verwendet er die Verbindungszeichenfolge, die Sie zum Herstellen der Verbindung mit SQL-Datenbank konfiguriert haben.

Der `Database.Migrate()`-Aufruf hilft Ihnen, wenn er in Azure ausgeführt wird, da er automatisch die Datenbanken erstellt, die Ihre .NET Core-App basierend auf ihrer Migrationskonfiguration benötigt. 

> [!IMPORTANT]
> Befolgen Sie bei Produktions-Apps, die aufskaliert werden müssen, die bewährten Methoden unter [Razor-Seiten mit EF Core in ASP.NET Core: Migrationen (4 von 8)](/aspnet/core/data/ef-rp/migrations#applying-migrations-in-production).
> 

Speichern Sie Ihre Änderungen, und committen Sie sie in Ihr Git-Repository. 

```bash
git add .
git commit -m "connect to SQLDB in Azure"
```

### <a name="push-to-azure-from-git"></a>Übertragen von Git an Azure mithilfe von Push

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 98, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (92/92), done.
Writing objects: 100% (98/98), 524.98 KiB | 5.58 MiB/s, done.
Total 98 (delta 8), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: .
remote: Updating submodules.
remote: Preparing deployment for commit id '0c497633b8'.
remote: Generating deployment script.
remote: Project file path: ./DotNetCoreSqlDb.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-azure-app"></a>Navigieren zur Azure-App

Navigieren Sie in Ihrem Webbrowser zur bereitgestellten App.

```bash
http://<app_name>.azurewebsites.net
```

Fügen Sie einige Aufgaben hinzu.

![In App Service ausgeführte App](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

**Glückwunsch!** Sie führen eine datengesteuerte .NET Core-App in App Service aus.

## <a name="update-locally-and-redeploy"></a>Lokales Aktualisieren und erneutes Bereitstellen

In diesem Schritt nehmen Sie eine Änderung am Datenbankschema vor und veröffentlichen sie in Azure.

### <a name="update-your-data-model"></a>Aktualisieren Sie des Datenmodells

Öffnen Sie _Models\Todo.cs_ im Code-Editor. Fügen Sie der `ToDo`-Klasse die folgende Eigenschaft hinzu:

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Lokales Ausführen von Code First-Migrationen

Führen Sie einige Befehle aus, um Aktualisierungen an Ihrer lokalen Datenbank vorzunehmen.

```bash
dotnet ef migrations add AddProperty
```

Aktualisieren Sie die lokale Datenbank:

```bash
dotnet ef database update
```

### <a name="use-the-new-property"></a>Verwenden der neuen Eigenschaft

Nehmen Sie einige Änderungen an Ihrem Code vor, um die `Done`-Eigenschaft zu verwenden. Der Einfachheit halber ändern Sie in diesem Lernprogramm nur Ansichten `Index` und `Create`, um die Eigenschaft in Aktion zu sehen.

Öffnen Sie _Controllers\TodosController.cs_.

Suchen Sie die `Create([Bind("ID,Description,CreatedDate")] Todo todo)`-Methode, und fügen Sie `Done` zur Liste der Eigenschaften im `Bind`-Attribut hinzu. Anschließend sieht Ihre `Create()`-Methodensignatur wie im folgenden Code aus:

```csharp
public async Task<IActionResult> Create([Bind("ID,Description,CreatedDate,Done")] Todo todo)
```

Öffnen Sie _Views\Todos\Create.cshtml_.

Im Razor-Code sollten Sie ein `<div class="form-group">`-Element für `Description` sowie ein weiteres `<div class="form-group">`-Element für `CreatedDate` sehen. Fügen Sie direkt nach diesen beiden Elementen ein weiteres `<div class="form-group">`-Element für `Done` hinzu:

```csharp
<div class="form-group">
    <label asp-for="Done" class="col-md-2 control-label"></label>
    <div class="col-md-10">
        <input asp-for="Done" class="form-control" />
        <span asp-validation-for="Done" class="text-danger"></span>
    </div>
</div>
```

Öffnen Sie _Views\Todos\Index.cshtml_.

Suchen Sie nach dem leeren `<th></th>`-Element. Fügen Sie direkt oberhalb dieses Elements den folgenden Razor-Code hinzu:

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

Suchen Sie nach dem `<td>`-Element, das die `asp-action`-Taghilfsprogramme enthält. Fügen Sie direkt oberhalb dieses Elements den folgenden Razor-Code hinzu:

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

Mehr ist nicht erforderlich, um die Änderungen in den Ansichten `Index` und `Create` anzuzeigen.

### <a name="test-your-changes-locally"></a>Lokales Testen der Änderungen

Führen Sie die App lokal aus.

```bash
dotnet run
```

Navigieren Sie in Ihrem Browser zu `http://localhost:5000/`. Sie können jetzt eine Aufgabe hinzufügen und die Option **Fertig** aktivieren. Die Aufgabe sollte dann auf der Startseite als erledigt angezeigt werden. Beachten Sie, dass in der Ansicht `Edit` das Feld `Done` nicht angezeigt wird, da Sie die Ansicht `Edit` nicht geändert haben.

### <a name="publish-changes-to-azure"></a>Veröffentlichen von Änderungen in Azure

```bash
git add .
git commit -m "added done field"
git push azure master
```

Navigieren Sie nach Abschluss des `git push`-Vorgangs zu Ihrer App Service-App, versuchen Sie, eine Aufgabe hinzuzufügen, und aktivieren Sie **Fertig**.

![Azure-App nach Code First-Migration](./media/app-service-web-tutorial-dotnetcore-sqldb/this-one-is-done.png)

Ihre gesamten vorhandenen Aufgaben werden weiterhin angezeigt. Wenn Sie die .NET Core-App erneut veröffentlichen, gehen in SQL-Datenbank vorhandene Daten nicht verloren. Außerdem wird durch Entity Framework Core-Migrationen nur das Datenschema geändert, die vorhandenen Daten bleiben unverändert.

## <a name="stream-diagnostic-logs"></a>Streamen von Diagnoseprotokollen

Wenn die ASP.NET Core-App in Azure App Service ausgeführt wird, können Sie die Konsolenprotokolle an Cloud Shell umleiten. Auf diese Weise erhalten Sie die gleichen Diagnosemeldungen, die Ihnen beim Debuggen von Anwendungsfehlern helfen.

Im Beispielprojekt wird die Anleitung unter [Protokollierung in ASP.NET Core.](https://docs.microsoft.com/aspnet/core/fundamentals/logging#azure-app-service-provider) mit zwei Konfigurationsänderungen bereits befolgt:

- Enthält einen Verweis auf `Microsoft.Extensions.Logging.AzureAppServices` in *DotNetCoreSqlDb.csproj*.
- Ruft `loggerFactory.AddAzureWebAppDiagnostics()` in *Program.cs* auf.

Zum Festlegen der [Protokollebene](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-level) für ASP.NET Core in App Service von der Standardebene `Error` auf `Information` verwenden Sie den Befehl [`az webapp log config`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-config) in Cloud Shell.

```azurecli-interactive
az webapp log config --name <app_name> --resource-group myResourceGroup --application-logging true --level information
```

> [!NOTE]
> Die Protokollebene des Projekts ist in *appsettings.json* bereits auf `Information` festgelegt.
> 

Verwenden Sie zum Starten des Streamings von Protokolldateien den Befehl [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-tail) in Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
```

Nachdem das Protokollstreaming gestartet wurde, aktualisieren Sie die Azure-App im Browser, um Webdatenverkehr zu generieren. Sie können jetzt Konsolenprotokolle sehen, die auf das Terminal umgeleitet werden. Falls Sie nicht sofort Konsolenprotokolle sehen, können Sie es nach 30 Sekunden noch einmal versuchen.

Um das Protokollstreaming jederzeit zu beenden, geben Sie `Ctrl`+`C` ein.

Weitere Informationen zum Anpassen der ASP.NET Core-Protokolle finden Sie unter [Protokollierung in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="manage-your-azure-app"></a>Verwalten der Azure-App

Um die von Ihnen erstellte App anzuzeigen, suchen Sie im [Azure-Portal](https://portal.azure.com) nach **App Services**, und wählen Sie diese Option aus.

![Auswählen von „App Services“ im Azure-Portal](./media/app-service-web-tutorial-dotnetcore-sqldb/app-services.png)

Wählen Sie auf der Seite **App Services** den Namen Ihrer Azure-App aus.

![Portalnavigation zur Azure-App](./media/app-service-web-tutorial-dotnetcore-sqldb/access-portal.png)

Standardmäßig wird im Portal die Seite **Übersicht** Ihrer App angezeigt. Diese Seite bietet einen Überblick über den Status Ihrer App. Hier können Sie auch einfache Verwaltungsaufgaben wie Durchsuchen, Beenden, Neustarten und Löschen durchführen. Links auf der Seite werden die verschiedenen Konfigurationsseiten gezeigt, die Sie öffnen können.

![App Service-Seite im Azure-Portal](./media/app-service-web-tutorial-dotnetcore-sqldb/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Nächste Schritte

Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen einer SQL-Datenbank in Azure
> * Herstellen einer Verbindung einer .NET Core-App mit SQL-Datenbank
> * Bereitstellen der Anwendung in Azure
> * Aktualisieren des Datenmodells und erneutes Bereitstellen der App
> * Streamen von Protokollen von Azure auf Ihr Terminal
> * Verwalten der App im Azure-Portal

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Ihrer App einen benutzerdefinierten DNS-Namen zuordnen.

> [!div class="nextstepaction"]
> [Zuordnen eines vorhandenen benutzerdefinierten DNS-Namens zu Azure App Service](app-service-web-tutorial-custom-domain.md)
