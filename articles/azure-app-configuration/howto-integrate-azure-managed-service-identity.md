---
title: Beheerde identiteiten gebruiken om App Configuration te openen
titleSuffix: Azure App Configuration
description: Verifiëren bij Azure App Configuration met behulp van beheerde identiteiten
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.custom: devx-track-csharp, fasttrack-edit
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 7a9eb992ff0cb98fdae2920da2beeda0bbd8941b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877530"
---
# <a name="use-managed-identities-to-access-app-configuration"></a>Beheerde identiteiten gebruiken om App Configuration te openen

Azure Active Directory [identiteiten vereenvoudigen geheimenbeheer](../active-directory/managed-identities-azure-resources/overview.md) voor uw cloudtoepassing. Met een beheerde identiteit kan uw code de service-principal gebruiken die is gemaakt voor de Azure-service die wordt uitgevoerd. U gebruikt een beheerde identiteit in plaats van een afzonderlijke referentie die is opgeslagen in Azure Key Vault of een lokale connection string.

Azure App Configuration en de .NET Core-, .NET Framework- en Java Spring-clientbibliotheken hebben ingebouwde ondersteuning voor beheerde identiteiten. Hoewel u deze niet hoeft te gebruiken, elimineert de beheerde identiteit de noodzaak van een toegangs token dat geheimen bevat. Uw code heeft alleen toegang tot App Configuration store met behulp van het service-eindpunt. U kunt deze URL rechtstreeks in uw code insluiten zonder een geheim weer te geven.

In dit artikel wordt beschreven hoe u kunt profiteren van de beheerde identiteit voor toegang tot App Configuration. Dit is gebaseerd op de web-app die is geïntroduceerd in de quickstarts. Voordat u doorgaat, [maakt u eerst een ASP.NET Core-app met App Configuration.](./quickstart-aspnet-core-app.md)

> [!NOTE]
> In dit artikel wordt Azure App Service als voorbeeld gebruikt, maar hetzelfde concept is van toepassing op andere Azure-service die beheerde identiteiten ondersteunt, bijvoorbeeld [Azure Kubernetes Service,](../aks/use-azure-ad-pod-identity.md) [Azure Virtual Machine](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)en [Azure Container Instances](../container-instances/container-instances-managed-identity.md). Als uw workload wordt gehost in een van deze services, kunt u ook gebruikmaken van de beheerde identiteitsondersteuning van de service.

In dit artikel wordt ook beschreven hoe u de beheerde identiteit kunt gebruiken in combinatie met de App Configuration van Key Vault identiteit. Met één beheerde identiteit hebt u naadloos toegang tot beide geheimen vanuit Key Vault en configuratiewaarden van App Configuration. Als u deze mogelijkheid wilt verkennen, moet u [eerst Use Key Vault References with ASP.NET Core](./use-key-vault-references-dotnet-core.md) voltooien.

U kunt elke code-editor gebruiken om de stappen in deze zelfstudie uit te voeren. [Visual Studio Code](https://code.visualstudio.com/) is een uitstekende optie die beschikbaar is op de Windows-, macOS- en Linux-platforms.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een beheerde identiteit toegang verlenen tot App Configuration.
> * Configureer uw app voor het gebruik van een beheerde identiteit wanneer u verbinding maakt met App Configuration.
> * U kunt uw app desgewenst configureren voor het gebruik van een beheerde identiteit wanneer u verbinding maakt met Key Vault via App Configuration Key Vault referentie.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze zelfstudie te voltooien:

* [.NET Core SDK](https://dotnet.microsoft.com/download).
* [Azure Cloud Shell geconfigureerd.](../cloud-shell/quickstart.md)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Een beheerde identiteit toevoegen

Als u een beheerde identiteit in de portal wilt instellen, moet u eerst een toepassing maken en vervolgens de functie inschakelen.

1. Maak een App Services in de [Azure Portal](https://portal.azure.com) zoals u normaal doet. Ga naar het in de portal.

1. Schuif omlaag naar de **groep** Instellingen in het linkerdeelvenster en selecteer **Identiteit.**

1. Schakel op **het tabblad Systeem** toegewezen de optie **Status** in **op Aan** en selecteer **Opslaan.**

1. Antwoord **Ja wanneer** u wordt gevraagd om de door het systeem toegewezen beheerde identiteit in teschakelen.

    ![Stel beheerde identiteit in App Service in](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Verleen toegang tot de App-configuratie

1. Selecteer in [Azure Portal](https://portal.azure.com)alle **resources en** selecteer de App Configuration die u in de quickstart hebt gemaakt.

1. Klik op **Toegangsbeheer (IAM)** .

1. Selecteer op **het tabblad Toegang** controleren de optie **Toevoegen** in de gebruikersinterface van de **kaart Roltoewijzing** toevoegen.

1. Selecteer **onder Rol** de optie App Configuration **Gegevenslezer.** Selecteer **onder Toegang toewijzen aan** App Service onder Door het systeem toegewezen **beheerde identiteit.** 

1. Selecteer **onder Abonnement** uw Azure-abonnement. Selecteer de App Service resource voor uw app.

1. Selecteer **Opslaan**.

    ![Een beheerde identiteit toevoegen](./media/add-managed-identity.png)

1. Optioneel: als u ook toegang wilt verlenen Key Vault, volgt u de instructies in Een Key Vault [toewijzen.](../key-vault/general/assign-access-policy-portal.md)

## <a name="use-a-managed-identity"></a>Een beheerde identiteit gebruiken

1. Voeg een verwijzing toe naar het *pakket Azure.Identity:*

    ```bash
    dotnet add package Azure.Identity
    ```

1. Zoek het eindpunt naar uw App Configuration winkel. Deze URL wordt vermeld op het **tabblad Toegangssleutels** voor de store in Azure Portal.

1. Open *appsettings.jsop* en voeg het volgende script toe. Vervang *\<service_endpoint>* , inclusief de haakjes, door de URL naar uw App Configuration winkel.

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. Open *Program.cs* en voeg een verwijzing toe naar de `Azure.Identity` `Microsoft.Azure.Services.AppAuthentication` naamruimten en :

    ```csharp-interactive
    using Azure.Identity;
    ```

1. Als u alleen toegang wilt tot waarden die rechtstreeks zijn opgeslagen in App Configuration, werkt u de methode bij door de methode te vervangen (deze vindt u `CreateWebHostBuilder` `config.AddAzureAppConfiguration()` in het `Microsoft.Azure.AppConfiguration.AspNetCore` pakket).

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
    > Als u een door de gebruiker toegewezen beheerde identiteit wilt **gebruiken,** moet u de clientId opgeven bij het maken van [managedIdentityCredential.](/dotnet/api/azure.identity.managedidentitycredential)
    >```
    >config.AddAzureAppConfiguration(options =>
    >   options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential(<your_clientId>)));
    >```
    >Zoals uitgelegd in de veelgestelde vragen over beheerde identiteiten voor [Azure-resources,](../active-directory/managed-identities-azure-resources/managed-identities-faq.md#what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request)is er een standaard manier om op te lossen welke beheerde identiteit wordt gebruikt. In dit geval dwingt de Azure Identity-bibliotheek u af om de gewenste identiteit op te geven om in de toekomst posible runtime-problemen te voorkomen (bijvoorbeeld als er een nieuwe door de gebruiker toegewezen beheerde identiteit wordt toegevoegd of als de door het systeem toegewezen beheerde identiteit is ingeschakeld). U moet dus de clientId opgeven, zelfs als er slechts één door de gebruiker toegewezen beheerde identiteit is gedefinieerd en er geen door het systeem toegewezen beheerde identiteit is.


1. Als u zowel App Configuration als Key Vault wilt gebruiken, moet u *Program.cs* bijwerken zoals hieronder wordt weergegeven. Met deze code wordt aanroepen als onderdeel van om de configuratieprovider te laten weten welke referentie moet worden gebruikt bij `SetCredential` `ConfigureKeyVault` de Key Vault.

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

    U hebt nu toegang tot Key Vault referenties, net als elke andere App Configuration sleutel. De configuratieprovider gebruikt de om verificatie `ManagedIdentityCredential` uit te Key Vault en de waarde op te halen.

    > [!NOTE]
    > De `ManagedIdentityCredential` werkt alleen in Azure-omgevingen met services die ondersteuning bieden voor verificatie van beheerde identiteiten. Het werkt niet in de lokale omgeving. Gebruik om de code in zowel lokale als Azure-omgevingen te gebruiken, omdat deze terugvalt op een [`DefaultAzureCredential`](/dotnet/api/azure.identity.defaultazurecredential) aantal verificatieopties, waaronder beheerde identiteit.
    > 
    > Als u een door de gebruiker ondertekende beheerde **identiteit** wilt gebruiken met de wanneer deze is geïmplementeerd `DefaultAzureCredential` in Azure, geeft u de [clientId op.](/dotnet/api/overview/azure/identity-readme#specifying-a-user-assigned-managed-identity-with-the-defaultazurecredential)

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="deploy-from-local-git"></a>Implementeren vanuit lokale Git

De eenvoudigste manier om lokale Git-implementatie in teschakelen voor uw app met de Kudu-buildserver is door gebruik te [Azure Cloud Shell.](https://shell.azure.com)

### <a name="configure-a-deployment-user"></a>Een implementatiegebruiker configureren

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Lokale Git met Kudu inschakelen
Als u geen lokale Git-opslagplaats voor uw app hebt, moet u er een initialiseren. Als u een lokale Git-opslagplaats wilt initialiseren, voert u de volgende opdrachten uit vanuit de projectmap van uw app:

```cmd
git init
git add .
git commit -m "Initial version"
```

Als u lokale Git-implementatie voor uw app wilt inschakelen met de Kudu-buildserver, moet u [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/#az_webapp_deployment_source_config_local_git) uitvoeren in Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Met deze opdracht ziet u er ongeveer als volgt uit:

```json
{
  "url": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
}
```

### <a name="deploy-your-project"></a>Uw project implementeren

Voeg in _het lokale terminalvenster_ een externe Azure-opslagplaats toe aan uw lokale Git-opslagplaats. Vervang _\<url>_ door de URL van de externe Git-locatie die u hebt via Lokale Git [inschakelen met Kudu](#enable-local-git-with-kudu).

```bash
git remote add azure <url>
```

Push naar de externe Azure-instantie om uw app te implementeren met de volgende opdracht. Wanneer u om een wachtwoord wordt gevraagd, voert u het wachtwoord in dat u hebt gemaakt in [Een implementatiegebruiker configureren.](#configure-a-deployment-user) Gebruik niet het wachtwoord dat u gebruikt om u aan te melden bij de Azure Portal.

```bash
git push azure main
```

Mogelijk ziet u runtimespecifieke automatisering in de uitvoer, zoals MSBuild voor ASP.NET, voor Node.js `npm install` en `pip install` voor Python.

### <a name="browse-to-the-azure-web-app"></a>Bladeren naar de Azure-web-app

Blader naar uw web-app met behulp van een browser om te controleren of de inhoud is geïmplementeerd.

```bash
http://<app_name>.azurewebsites.net
```

## <a name="use-managed-identity-in-other-languages"></a>Beheerde identiteit gebruiken in andere talen

App-configuratie-providers voor .NET Framework en Java Spring hebben ook ingebouwde ondersteuning voor beheerde identiteit. U kunt het URL-eindpunt van uw winkel gebruiken in plaats van de volledige connection string wanneer u een van deze providers configureert.

U kunt bijvoorbeeld de console-app .NET Framework die u in de quickstart hebt gemaakt, bijwerken om de volgende instellingen op te geven in *hetApp.config* bestand:

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
In deze zelfstudie hebt u een beheerde Azure-identiteit toegevoegd om de toegang tot uw App Configuration te stroomlijnen en het referentiebeheer voor uw app te verbeteren. Voor meer informatie over het gebruik van App Configuration gaat u verder naar de Azure CLI-voorbeelden.

> [!div class="nextstepaction"]
> [CLI-voorbeelden](./cli-samples.md)