---
title: Beheerde identiteiten gebruiken om App Configuration te openen
titleSuffix: Azure App Configuration
description: Verifiëren voor Azure-app configuratie met behulp van beheerde identiteiten
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.custom: devx-track-csharp, fasttrack-edit
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: b4b67a6bf4eea72b3a81bd01398cc60cc33c0d0a
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227038"
---
# <a name="use-managed-identities-to-access-app-configuration"></a>Beheerde identiteiten gebruiken om App Configuration te openen

Met Azure Active Directory [beheerde identiteiten](../active-directory/managed-identities-azure-resources/overview.md) wordt het beheer van geheimen voor uw Cloud toepassing vereenvoudigd. Met een beheerde identiteit kan uw code gebruikmaken van de service-principal die is gemaakt voor de Azure-service waarop deze wordt uitgevoerd. U gebruikt een beheerde identiteit in plaats van een afzonderlijke referentie die is opgeslagen in Azure Key Vault of een lokale connection string.

Azure-app-configuratie en de .NET core-, .NET Framework-en Java lente-client bibliotheken hebben beheerde identiteits ondersteuning ingebouwd. Hoewel u deze niet hoeft te gebruiken, elimineert de beheerde identiteit geen toegangs token dat geheimen bevat. De code heeft toegang tot de app-configuratie opslag door alleen het service-eind punt te gebruiken. U kunt deze URL rechtstreeks insluiten in uw code zonder geheim weer te geven.

In dit artikel wordt beschreven hoe u kunt profiteren van de beheerde identiteit om toegang te krijgen tot de app-configuratie. Dit is gebaseerd op de web-app die is geïntroduceerd in de quickstarts. Voordat u doorgaat, moet u eerst  [een ASP.net core-app met app-configuratie maken](./quickstart-aspnet-core-app.md) .

> [!NOTE]
> In dit artikel wordt Azure App Service als voor beeld gebruikt, maar hetzelfde concept is van toepassing op elke andere Azure-service die beheerde identiteit ondersteunt, bijvoorbeeld [Azure Kubernetes service](../aks/use-azure-ad-pod-identity.md), [Azure Virtual Machine](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)en [Azure container instances](../container-instances/container-instances-managed-identity.md). Als uw werk belasting wordt gehost in een van deze services, kunt u ook gebruikmaken van de beheerde identiteits ondersteuning van de service.

In dit artikel ziet u ook hoe u de beheerde identiteit kunt gebruiken in combi natie met de Key Vault verwijzingen van de app-configuratie. Met één beheerde identiteit kunt u probleemloos toegang krijgen tot beide geheimen van Key Vault en configuratie waarden van de app-configuratie. Als u deze functie wilt verkennen, kunt u het [gebruik van Key Vault verwijzingen met ASP.net core](./use-key-vault-references-dotnet-core.md) eerst volt ooien.

U kunt elke code-editor gebruiken om de stappen in deze zelfstudie uit te voeren. [Visual Studio Code](https://code.visualstudio.com/) is een uitstekende optie die beschikbaar is op de Windows-, macOS- en Linux-platforms.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Verleen een beheerde identiteit toegang tot de app-configuratie.
> * Configureer uw app voor het gebruik van een beheerde identiteit wanneer u verbinding maakt met de app-configuratie.
> * U kunt uw app eventueel configureren voor het gebruik van een beheerde identiteit wanneer u verbinding maakt met Key Vault via een app-configuratie Key Vault verwijzing.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze zelfstudie te voltooien:

* [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* [Azure Cloud shell geconfigureerd](../cloud-shell/quickstart.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Een beheerde identiteit toevoegen

Als u een beheerde identiteit in de portal wilt instellen, maakt u eerst een toepassing en schakelt u de functie in.

1. Maak een App Services-exemplaar in het [Azure Portal](https://portal.azure.com) zoals u dat gewend bent. Ga naar de portal.

1. Schuif omlaag naar de **instellingen** groep in het linkerdeel venster en selecteer **identiteit**.

1. Schakel op het tabblad **toegewezen systeem** de optie **status** in **op** aan en selecteer **Opslaan**.

1. Antwoord **Ja** wanneer u wordt gevraagd door het systeem toegewezen beheerde identiteit in te scha kelen.

    ![Stel beheerde identiteit in App Service in](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Verleen toegang tot de App-configuratie

1. Selecteer in het [Azure Portal](https://portal.azure.com) **alle resources** en selecteer de app-configuratie opslag die u hebt gemaakt in de Quick Start.

1. Klik op **Toegangsbeheer (IAM)** .

1. Klik op het tabblad **toegang controleren** op **toevoegen** in de gebruikers interface van de **roltoewijzing toevoegen** .

1. Onder **rol** selecteert u **app Configuration Data Reader**. Onder **toegang toewijzen aan** selecteert u **app service** onder door het **systeem toegewezen beheerde identiteit**.

1. Selecteer uw Azure-abonnement onder **abonnement**. Selecteer de App Service Resource voor uw app.

1. Selecteer **Opslaan**.

    ![Een beheerde identiteit toevoegen](./media/add-managed-identity.png)

1. Optioneel: als u ook toegang tot Key Vault wilt verlenen, volgt u de instructies in [een Key Vault toegangs beleid toewijzen](../key-vault/general/assign-access-policy-portal.md).

## <a name="use-a-managed-identity"></a>Een beheerde identiteit gebruiken

1. Voeg een verwijzing naar het pakket *Azure. Identity* toe:

    ```bash
    dotnet add package Azure.Identity
    ```

1. Zoek het eind punt naar het configuratie archief van uw app. Deze URL wordt weer gegeven op het tabblad **toegangs sleutels** voor de Store in de Azure Portal.

1. Open *appsettings.jsop* en voeg het volgende script toe. Vervang *\<service_endpoint>* , inclusief de haakjes, door de URL naar de app-configuratie opslag.

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. Open *programma. cs* en voeg een verwijzing naar de `Azure.Identity` `Microsoft.Azure.Services.AppAuthentication` naam ruimten en toe:

    ```csharp-interactive
    using Azure.Identity;
    ```

1. Als u alleen toegang wilt krijgen tot de waarden die rechtstreeks in de app-configuratie zijn opgeslagen, werkt u de `CreateWebHostBuilder` methode bij door de methode te vervangen `config.AddAzureAppConfiguration()` (deze vindt u in het `Microsoft.Azure.AppConfiguration.AspNetCore` pakket).

    > [!IMPORTANT]
    > `CreateHostBuilder` wordt vervangen door `CreateWebHostBuilder` in .NET Core 3.0.  Selecteer de juiste syntaxis op basis van uw omgeving.

    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((hostingContext, config) =>
               {
                   var settings = config.Build();
                   config.AddAzureAppConfiguration(options =>
                       options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
               })
               .UseStartup<Startup>();
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                });
            })
            .UseStartup<Startup>());
    ```
    ---

    > [!NOTE]
    > In het geval dat u een door de **gebruiker toegewezen beheerde identiteit** wilt gebruiken, moet u de clientId opgeven bij het maken van de [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential).
    >```
    >config.AddAzureAppConfiguration(options =>
    >   options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential(<your_clientId>)));
    >```
    >Zoals uitgelegd in de [Veelgestelde vragen over beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/managed-identities-faq.md#what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request), is er een standaard manier om de beheerde identiteit op te lossen. In dit geval wordt u afgedwongen om de gewenste identiteit op te geven om aantal-runtime problemen in de toekomst te voor komen (bijvoorbeeld als een nieuwe door de gebruiker toegewezen beheerde identiteit wordt toegevoegd of als de door het systeem toegewezen beheerde identiteit is ingeschakeld). Daarom moet u de clientId opgeven, zelfs als er slechts één door de gebruiker toegewezen beheerde identiteit is gedefinieerd, en er geen door het systeem toegewezen beheerde identiteit is.


1. Als u zowel app-configuratie waarden als Key Vault referenties wilt gebruiken, moet u *programma. cs* bijwerken zoals hieronder wordt weer gegeven. Deze code roept `SetCredential` als onderdeel van `ConfigureKeyVault` de configuratie provider aan welke referentie moet worden gebruikt bij het verifiëren van Key Vault.

    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((hostingContext, config) =>
               {
                   var settings = config.Build();
                   var credentials = new ManagedIdentityCredential();

                   config.AddAzureAppConfiguration(options =>
                   {
                       options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                              .ConfigureKeyVault(kv =>
                              {
                                 kv.SetCredential(credentials);
                              });
                   });
               })
               .UseStartup<Startup>();
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    var credentials = new ManagedIdentityCredential();

                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                               .ConfigureKeyVault(kv =>
                               {
                                   kv.SetCredential(credentials);
                               });
                    });
                });
            })
            .UseStartup<Startup>());
    ```
    ---

    U hebt nu toegang tot Key Vault verwijzingen, net zoals elke andere app-configuratie sleutel. De configuratie provider gebruikt de `ManagedIdentityCredential` om te verifiëren Key Vault en de waarde op te halen.

    > [!NOTE]
    > De functie `ManagedIdentityCredential` werkt alleen in azure-omgevingen met services die ondersteuning bieden voor beheerde identiteits verificatie. Het werkt niet in de lokale omgeving. Gebruik deze [`DefaultAzureCredential`](/dotnet/api/azure.identity.defaultazurecredential) code om te werken in de lokale en Azure-omgeving, omdat deze terugvallen op enkele verificatie opties, waaronder beheerde identiteit.
    > 
    > Als u een door de **gebruiker asigned beheerde identiteit** wilt gebruiken met de `DefaultAzureCredential` Wanneer geïmplementeerd naar Azure, [geeft u de clientId](/dotnet/api/overview/azure/identity-readme#specifying-a-user-assigned-managed-identity-with-the-defaultazurecredential)op.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="deploy-from-local-git"></a>Implementeren vanuit lokale Git

De eenvoudigste manier om lokale Git-implementatie voor uw app in te scha kelen met de kudu-buildserver is door gebruik te maken van [Azure Cloud shell](https://shell.azure.com).

### <a name="configure-a-deployment-user"></a>Een implementatiegebruiker configureren

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Lokale Git met Kudu inschakelen
Als u geen lokale Git-opslag plaats voor uw app hebt, moet u er een initialiseren. Voor het initialiseren van een lokale Git-opslag plaats voert u de volgende opdrachten uit vanuit de projectmap van uw app:

```cmd
git init
git add .
git commit -m "Initial version"
```

Als u lokale Git-implementatie voor uw app wilt inschakelen met de kudu-buildserver, voert u [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/#az-webapp-deployment-source-config-local-git) in Cloud shell uit.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Met deze opdracht krijgt u iets te zien dat vergelijkbaar is met de volgende uitvoer:

```json
{
  "url": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
}
```

### <a name="deploy-your-project"></a>Uw project implementeren

Voeg in het _lokale terminal venster_ een externe Azure-Data Bank toe aan uw lokale Git-opslag plaats. Vervang door _\<url>_ de URL van de externe Git die u hebt ontvangen van [lokale Git inschakelen met kudu](#enable-local-git-with-kudu).

```bash
git remote add azure <url>
```

Push naar de externe Azure-instantie om uw app te implementeren met de volgende opdracht. Wanneer u wordt gevraagd om een wacht woord, voert u het wacht woord in dat u hebt gemaakt in [een implementatie gebruiker configureren](#configure-a-deployment-user). Gebruik niet het wacht woord dat u gebruikt om u aan te melden bij de Azure Portal.

```bash
git push azure main
```

U kunt runtime-specifieke Automation zien in de uitvoer, zoals MSBuild voor ASP.NET, `npm install` voor Node.js en `pip install` voor python.

### <a name="browse-to-the-azure-web-app"></a>Bladeren naar de Azure-web-app

Blader naar uw web-app met behulp van een browser om te controleren of de inhoud is geïmplementeerd.

```bash
http://<app_name>.azurewebsites.net
```

## <a name="use-managed-identity-in-other-languages"></a>Beheerde identiteit gebruiken in andere talen

App-configuratie-providers voor .NET Framework en Java Spring hebben ook ingebouwde ondersteuning voor beheerde identiteit. Wanneer u een van deze providers configureert, kunt u het URL-eind punt van uw Store gebruiken in plaats van de volledige connection string.

U kunt bijvoorbeeld de .NET Framework console-app die in de Quick start is gemaakt, bijwerken om de volgende instellingen op te geven in het *App.config* -bestand:

```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" endpoint="${Endpoint}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="Endpoint" value ="Set via an environment variable - for example, dev, test, staging, or production endpoint." />
    </appSettings>
```

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen
In deze zelf studie hebt u een door Azure beheerde identiteit toegevoegd om de toegang tot de app-configuratie te stroom lijnen en het referentie beheer voor uw app te verbeteren. Voor meer informatie over het gebruik van App Configuration gaat u verder naar de Azure CLI-voorbeelden.

> [!div class="nextstepaction"]
> [CLI-voorbeelden](./cli-samples.md)