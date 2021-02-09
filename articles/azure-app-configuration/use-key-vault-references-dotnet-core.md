---
title: Zelfstudie voor het gebruik van Azure App Configuration Key Vault-verwijzingen in een ASP.NET Core-app | Microsoft Docs
description: In deze zelfstudie leert u hoe u Key Vault-verwijzingen van Azure App Configuration kunt gebruiken vanuit een ASP.NET Core-app
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/08/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 37bc7fbcd366455668d5316e45ffbf79127a49f3
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99981219"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Zelfstudie: Key Vault-referenties gebruiken in een ASP.NET Core-app

In deze zelfstudie leert u hoe u de Azure App Configuration-service kunt gebruiken in combinatie met Azure Key Vault. App Configuration en Key Vault zijn complementaire services die naast elkaar worden gebruikt in de meeste toepassingsimplementaties.

Met App Configuration kunt u de services gezamenlijk gebruiken door sleutels te maken die verwijzen naar waarden die zijn opgeslagen in Key Vault. Wanneer App Configuration dergelijke sleutels maakt, worden de URI's van Key Vault-waarden opgeslagen in plaats van de waarden zelf.

In uw toepassing wordt gebruikgemaakt van de clientprovider van App Configuration om Key Vault-verwijzingen op te halen, net zoals bij andere sleutels die zijn opgeslagen in App Configuration. In dit geval zijn de waarden die zijn opgeslagen in App Configuration URI's die verwijzen naar de waarden in de Key Vault. Het zijn geen Key Vault-waarden of -referenties. Omdat de clientprovider de sleutels als Key Vault-verwijzingen herkent, wordt Key Vault gebruikt om de waarden op te halen.

Uw toepassing is verantwoordelijk voor het correct verifiëren van zowel App Configuration als Key Vault. De twee services communiceren niet rechtstreeks.

In deze zelfstudie wordt uitgelegd hoe u Key Vault-verwijzingen in uw code kunt implementeren. Dit is gebaseerd op de web-app die is geïntroduceerd in de quickstarts. Volg eerst [Een ASP.NET Core-app maken met App Configuration](./quickstart-aspnet-core-app.md) voordat u verder gaat.

U kunt elke code-editor gebruiken om de stappen in deze zelfstudie uit te voeren. [Visual Studio Code](https://code.visualstudio.com/) is bijvoorbeeld een platformoverschrijdende code-editor die beschikbaar is voor Windows-, macOS- en Linux-besturingssystemen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een App Configuration-sleutel maken die verwijst naar een waarde die is opgeslagen in Key Vault.
> * Toegang tot de waarde van deze sleutel vanuit een ASP.NET Core-toepassing.

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelfstudie begint, moet u de [.NET Core SDK](https://dotnet.microsoft.com/download) installeren.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Een kluis maken

1. Selecteer de optie **Een resource maken** in de linkerbovenhoek van Azure Portal:

    ![Schermopname van de optie Een resource maken, in de Microsoft Azure-portal.](./media/quickstarts/search-services.png)
1. Typ **Key Vault** in het zoekvak.
1. Selecteer in de lijst met resultaten **Key Vaults** aan de linkerkant.
1. Selecteer **Toevoegen** in **Key Vaults**.
1. Geef rechts in **Key Vault maken** de volgende gegevens op:
    - Selecteer **Abonnement** om een abonnement te kiezen.
    - Selecteer in **Resourcegroep** **Nieuwe maken** en voer een naam in voor de resourcegroep.
    - In **Key Vault-naam** is een unieke naam vereist. Voer voor deze zelfstudie **Contoso-vault2** in.
    - Kies een locatie in de vervolgkeuzelijst **Regio**.
1. Zorg ervoor dat de andere opties in **Key Vault maken** de standaardwaarden hebben.
1. Selecteer **Maken**.

Vanaf nu is uw Azure-account als enige gemachtigd om bewerkingen op deze nieuwe Key Vault uit te voeren.

![Schermopname van uw sleutelkluis.](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Een geheim toevoegen aan Key Vault

Als u een geheim wilt toevoegen aan de Key Vault, hoeft u maar een paar extra stappen uit te voeren. In dit geval voegt u een bericht toe dat u kunt gebruiken om het ophalen van Key Vault te testen. Het bericht wordt **Bericht** genoemd en u slaat er de waarde Hello from Key Vault in op.

1. Selecteer vanuit de eigenschappenpagina's van de Key Vault **Geheimen**.
1. Selecteer **Genereren/importeren**.
1. Voer in het deelvenster **Een geheim maken** een van de volgende waarden in:
    - **Uploadopties**: Voer **Handmatig** in.
    - **Naam**: **Bericht** invoeren.
    - **Waarde**: Voer **Hello from Key Vault** in.
1. Zorg ervoor dat de andere eigenschappen van **Een geheim maken** de standaardwaarden hebben.
1. Selecteer **Maken**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Een sleutelkluisverwijzing toevoegen aan App Configuration

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Alle resources** en selecteer vervolgens de instantie van het App Configuration-archief dat u in de quickstart hebt gemaakt.

1. Selecteer **Configuratieverkenner**.

1. Selecteer **+ Maken** > **Sleutelkluisverwijzing** en geef de volgende waarden op:
    - **Sleutel**: Selecteer **TestApp:Settings:KeyVaultMessage**.
    - **Label**: Laat deze waarde leeg.
    - **Abonnement**, **Resourcegroep** en **Sleutelkluis**: Voer de waarden in die overeenkomen met de waarden in de sleutelkluis die u in de vorige sectie hebt gemaakt.
    - **Geheim**: Selecteer het geheim genaamd **Bericht** dat u in de vorige sectie hebt gemaakt.

## <a name="connect-to-key-vault"></a>Verbinding maken met Key Vault

1. In deze zelfstudie gebruikt u een service-principal voor verificatie voor Key Vault. Om deze service-principal te maken gebruikt u de Azure CLI-opdracht [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac):

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Met deze bewerking wordt een reeks sleutel-waardeparen geretourneerd:

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Voer de volgende opdracht uit om de service-principal toegang te geven tot uw Key Vault:

    ```cmd
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
    ```

1. Voeg omgevingsvariabelen toe om de waarden van *clientId*, *clientSecret* en *tenantId* op te slaan.

    #### <a name="windows-command-prompt"></a>[Windows-opdrachtprompt](#tab/cmd)

    ```cmd
    setx AZURE_CLIENT_ID <clientId-of-your-service-principal>
    setx AZURE_CLIENT_SECRET <clientSecret-of-your-service-principal>
    setx AZURE_TENANT_ID <tenantId-of-your-service-principal>
    ```

    #### <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```PowerShell
    $Env:AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    $Env:AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    $Env:AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    #### <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    export AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    export AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    export AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    ---

    > [!NOTE]
    > Deze Key Vault-referenties worden alleen in uw toepassing gebruikt. Uw toepassing wordt rechtstreeks bij Key Vault geverifieerd met deze referenties. Ze worden nooit doorgegeven aan de App Configuration-service.

1. Start de terminal opnieuw om deze nieuwe omgevingsvariabelen te laden.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Uw code bijwerken om een Key Vault-referentie te gebruiken

1. Voeg een verwijzing toe naar de vereiste NuGet-pakketten door de volgende opdracht uit te voeren:

    ```dotnetcli
    dotnet add package Azure.Identity
    ```

1. Open *Program.cs* en voeg verwijzingen toe naar de volgende vereiste pakketten:

    ```csharp
    using Azure.Identity;
    ```

1. Werk de methode `CreateWebHostBuilder` bij voor het gebruik van App Configuration door de methode `config.AddAzureAppConfiguration` aan te roepen. Voeg de optie `ConfigureKeyVault` toe en geef de juiste referenties door aan uw sleutelkluis.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>();
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>());
    ```

1. Bij het initialiseren van de verbinding met App Configuration hebt u de verbinding met Key Vault ingesteld door de methode `ConfigureKeyVault` aan te roepen. Na de initialisatie kunt u de waarden van Key Vault-verwijzingen op dezelfde manier weergeven als de waarden van gewone App Configuration-sleutels.

    Als u dit proces in actie wilt zien, opent u *index.cshtml* in de map **Views** > **Home**. Vervang de inhoud door de volgende code:

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"]px;
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    U hebt toegang tot de waarde van de Key Vault-verwijzing **TestApp:Settings:KeyVaultMessage** op dezelfde manier als voor de configuratiewaarde van **TestApp:Settings:Message**.

## <a name="build-and-run-the-app-locally"></a>De app lokaal compileren en uitvoeren

1. Compileer de app met behulp van de .NET Core CLI door de volgende opdracht uit te voeren in de opdrachtshell:

    ```dotnetcli
    dotnet build
    ```

1. Nadat het compileren is voltooid, gebruikt u de volgende opdracht om de web-app lokaal uit te voeren:

    ```dotnetcli
    dotnet run
    ```

1. Open een browservenster en ga naar `http://localhost:5000`. Dit is de standaard-URL voor de web-app die lokaal wordt gehost.

    ![Quickstart voor lokaal starten van app](./media/key-vault-reference-launch-local.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie maakt u een App Configuration-sleutel die verwijst naar een waarde die is opgeslagen in Key Vault. Als u wilt weten hoe u een door Azure beheerde service-identiteit kunt toevoegen die de toegang tot App Configuration en Key Vault stroomlijnt, gaat u verder met de volgende zelfstudie.

> [!div class="nextstepaction"]
> [Integratie van beheerde identiteit](./howto-integrate-azure-managed-service-identity.md)
