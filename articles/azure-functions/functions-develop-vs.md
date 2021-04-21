---
title: Azure Functions ontwikkelen met Visual Studio
description: Meer informatie over het ontwikkelen en testen Azure Functions met behulp van Azure Functions Tools for Visual Studio 2019.
ms.custom: vs-azure, devx-track-csharp
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: 2cba0a9ad63e319af0a5eaa1c1c018c3b285c28a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765571"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Azure Functions ontwikkelen met Visual Studio  

Visual Studio kunt u C#-klassebibliotheekfuncties ontwikkelen, testen en implementeren in Azure. Als deze ervaring uw eerste ervaring is met Azure Functions, zie [Een inleiding tot Azure Functions](functions-overview.md).

Visual Studio biedt de volgende voordelen bij het ontwikkelen van uw functies: 

* Bewerk, bouw en voer functies uit op uw lokale ontwikkelcomputer. 
* Publiceer uw Azure Functions project rechtstreeks naar Azure en maak zo nodig Azure-resources. 
* Gebruik C#-kenmerken om functiebindingen rechtstreeks in de C#-code te declareeren.
* Vooraf gecompileerde C#-functies ontwikkelen en implementeren. Vooraf voldoende functies bieden betere koude startprestaties dan C#-functies op basis van scripts. 
* Codeer uw functies in C# en profiteer van alle voordelen van Visual Studio ontwikkeling. 

Dit artikel bevat informatie over het gebruik van Visual Studio het ontwikkelen van functies van de C#-klassebibliotheek en het publiceren ervan naar Azure. Voordat u dit artikel leest, kunt u de [Functions-quickstart voor Visual Studio.](functions-create-your-first-function-visual-studio.md) 

Tenzij anders vermeld, zijn de weergegeven procedures en voorbeelden Visual Studio 2019. 

## <a name="prerequisites"></a>Vereisten

- Azure Functions Tools. Als u Azure Function Tools wilt toevoegen, moet u de **Workload Azure-ontwikkeling** opnemen in Visual Studio installatie. Azure Functions Tools is beschikbaar in de Azure-ontwikkelworkload vanaf Visual Studio 2017.

- Andere resources die u nodig hebt, zoals een Azure Storage account, worden tijdens het publicatieproces in uw abonnement gemaakt.

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> In Visual Studio 2017 installeert de workload Azure-ontwikkeling Azure Functions Tools als een afzonderlijke extensie. Wanneer u de installatie van Visual Studio 2017 bijwerkt, [](#check-your-tools-version) moet u ervoor zorgen dat u de meest recente versie van de Azure Functions gebruikt. In de volgende secties ziet u hoe u de extensie Azure Functions Tools in Visual Studio 2017 controleert en (indien nodig) bijwerkt. 
>
> Sla deze secties over als u Visual Studio 2019 gebruikt.

### <a name="check-your-tools-version-in-visual-studio-2017"></a><a name="check-your-tools-version"></a>Controleer de versie van uw hulpprogramma's in Visual Studio 2017

1. Kies **extensies en** updates in het menu **Extra.** Vouw **Geïnstalleerde**  >  **hulpprogramma's** uit en kies **Azure Functions en WebTakenProgramma's.**

    ![De versie van de Functions-hulpprogramma's controleren](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

1. Noteer de **geïnstalleerde versie** en vergelijk deze versie met de nieuwste versie die wordt vermeld in de [release-opmerkingen](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md). 

1. Als uw versie ouder is, werkt u uw hulpprogramma's bij in Visual Studio zoals wordt weergegeven in de volgende sectie.

### <a name="update-your-tools-in-visual-studio-2017"></a>Uw hulpprogramma's bijwerken in Visual Studio 2017

1. Vouw in **het dialoogvenster Extensies** en updates **updates** Visual Studio  >  **Marketplace** uit, kies Azure Functions en **Webtakenprogramma's** en selecteer **Bijwerken.**

    ![De versie van de Functions-hulpprogramma's bijwerken](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

1. Nadat de hulpprogramma's zijn gedownload, selecteert u Sluiten **en** sluit Visual Studio de hulpprogramma's te activeren met VSIX Installer.

1. Kies wijzigen in VSIX Installer **om de** hulpprogramma's bij te werken. 

1. Nadat de update is voltooid, kiest u **Sluiten** en start u de Visual Studio.

> [!NOTE]  
> In Visual Studio 2019 en hoger wordt de extensie Azure Functions tools bijgewerkt als onderdeel van Visual Studio.  

## <a name="create-an-azure-functions-project"></a>Een Azure Functions-project maken

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

Nadat u een nieuw project Azure Functions, maakt de projectsjabloon een C#-project, installeert het NuGet-pakket en `Microsoft.NET.Sdk.Functions` stelt het doelkader in. Het nieuwe project heeft de volgende bestanden:

* **host.js:** hiermee kunt u de Functions-host configureren. Deze instellingen zijn van toepassing bij het lokaal uitvoeren van en in Azure. Zie voor meer informatie [host.jsnaslaginformatie.](functions-host-json.md)

* **local.settings.jsop**: Onderhoudt instellingen die worden gebruikt bij het lokaal uitvoeren van functies. Deze instellingen worden niet gebruikt bij het uitvoeren in Azure. Zie Local [settings file (Bestand met lokale instellingen) voor meer informatie.](#local-settings-file)

    >[!IMPORTANT]
    >Omdat de local.settings.jsbestand geheimen kan bevatten, moet u deze uitsluiten van het broncodebeheer van uw project. Zorg ervoor **dat de instelling Naar uitvoermap** kopiëren voor dit bestand is ingesteld op Kopiëren indien **nieuwer.** 

Zie Functions-klassebibliotheekproject [voor meer informatie.](functions-dotnet-class-library.md#functions-class-library-project)

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Visual Studio uploadt niet automatisch de instellingen in local.settings.jswanneer u het project publiceert. Als u er zeker van wilt zijn dat deze instellingen ook aanwezig zijn in uw functie-app in Azure, uploadt u deze nadat u uw project hebt gepubliceerd. Zie Instellingen voor [functie-apps voor meer informatie.](#function-app-settings) De waarden in een `ConnectionStrings` verzameling worden nooit gepubliceerd.

Uw code kan ook de instellingenwaarden van de functie-app lezen als omgevingsvariabelen. Zie [Omgevingsvariabelen voor meer informatie.](functions-dotnet-class-library.md#environment-variables)

## <a name="configure-your-build-output-settings"></a>De uitvoerinstellingen voor de build configureren

Wanneer u een Azure Functions-project bouwt, optimaliseren de buildhulpprogramma's de uitvoer, zodat slechts één kopie van alle assemblies die worden gedeeld met de functions-runtime, behouden blijft. Het resultaat is een geoptimaliseerde build die zoveel mogelijk ruimte bespaart. Wanneer u echter naar een recentere versie van uw projectassemblage gaat, weten de buildhulpprogramma's mogelijk niet dat deze assemblies moeten worden bewaard. Om ervoor te zorgen dat deze assemblies behouden blijven tijdens het optimalisatieproces, kunt u ze opgeven met behulp van elementen in het `FunctionsPreservedDependencies` projectbestand (.csproj) :

```xml
  <ItemGroup>
    <FunctionsPreservedDependencies Include="Microsoft.AspNetCore.Http.dll" />
    <FunctionsPreservedDependencies Include="Microsoft.AspNetCore.Http.Extensions.dll" />
    <FunctionsPreservedDependencies Include="Microsoft.AspNetCore.Http.Features.dll" />
  </ItemGroup>
```

## <a name="configure-the-project-for-local-development"></a>Het project configureren voor lokale ontwikkeling

De Functions-runtime maakt intern gebruik Azure Storage account. Voor alle andere triggertypen dan HTTP en webhooks stelt u de sleutel in op een geldig `Values.AzureWebJobsStorage` Azure Storage-connection string. Uw functie-app kan ook de [Azure Storage Emulator gebruiken](../storage/common/storage-use-emulator.md) voor de verbindingsinstelling `AzureWebJobsStorage` die vereist is voor het project. Als u de emulator wilt gebruiken, stelt u de waarde van `AzureWebJobsStorage` in op `UseDevelopmentStorage=true` . Wijzig deze instelling in een werkelijk opslagaccount connection string implementatie.

De opslagaccount instellen op connection string:

1. Selecteer Visual Studio **Cloud**  >  **Explorer weergeven.**

2. Vouw **in Cloud Explorer** **Opslagaccounts uit** en selecteer vervolgens uw opslagaccount. Kopieer op **het tabblad** Eigenschappen de waarde van de **primaire verbindingsreeks.**

2. Open in uw project de local.settings.json file en stel de waarde van de sleutel in op de `AzureWebJobsStorage` connection string u hebt gekopieerd.

3. Herhaal de vorige stap om unieke sleutels toe te voegen aan de matrix voor andere `Values` verbindingen die vereist zijn voor uw functies. 

## <a name="add-a-function-to-your-project"></a>Een functie toevoegen aan uw project

In C#-klassebibliotheekfuncties worden de bindingen die worden gebruikt door de functie gedefinieerd door kenmerken toe te passen in de code. Wanneer u uw functietriggers maakt op basis van de opgegeven sjablonen, worden de triggerkenmerken voor u toegepast. 

1. Klik **Solution Explorer** met de rechtermuisknop op het project-knooppunt en selecteer **Nieuw**  >  **item toevoegen.** 

2. Selecteer **Azure Function,** voer een **Naam in** voor de klasse en selecteer vervolgens **Toevoegen.**

3. Kies uw trigger, stel de bindingseigenschappen in en selecteer **ok.** In het volgende voorbeeld ziet u de instellingen voor het maken van een wachtrijopslagtriggerfunctie. 

    ![Een triggerfunctie voor Queue Storage maken](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    In dit triggervoorbeeld wordt een connection string met een sleutel met de naam `QueueStorage` . Definieer connection string instelling in het [local.settings.jsbestand](functions-run-local.md#local-settings-file).

4. Bekijk de zojuist toegevoegde klasse. U ziet een statische `Run()` methode die wordt toegeschreven aan het kenmerk `FunctionName` . Dit kenmerk geeft aan dat de methode het toegangspunt voor de functie is.

    De volgende C#-klasse vertegenwoordigt bijvoorbeeld een eenvoudige triggerfunctie voor Queue Storage:

    ```csharp
    using System;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.Extensions.Logging;

    namespace FunctionApp1
    {
        public static class Function1
        {
            [FunctionName("QueueTriggerCSharp")]
            public static void Run([QueueTrigger("myqueue-items", 
                Connection = "QueueStorage")]string myQueueItem, ILogger log)
            {
                log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
            }
        }
    }
    ```

Er wordt een bindingsspecifieke kenmerk toegepast op elke bindingsparameter die wordt opgegeven voor de methode van het toegangspunt. Het kenmerk gebruikt de bindingsgegevens als parameters. In het vorige voorbeeld is voor de eerste parameter een kenmerk toegepast, waarmee een triggerfunctie `QueueTrigger` queue storage wordt aangegeven. De naam van de wachtrij connection string naam van de instelling worden als parameters doorgegeven aan het `QueueTrigger` kenmerk . Zie Azure [Queue Storage-bindingen voor](functions-bindings-storage-queue-trigger.md)meer informatie Azure Functions .

Gebruik de bovenstaande procedure om meer functies toe te voegen aan uw functie-app-project. Elke functie in het project kan een andere trigger hebben, maar een functie moet precies één trigger hebben. Zie triggers en [bindingsconcepten Azure Functions meer informatie.](functions-triggers-bindings.md)

## <a name="add-bindings"></a>Bindingen toevoegen

Net als bij triggers worden invoer- en uitvoerbindingen aan uw functie toegevoegd als bindingskenmerken. Voeg bindingen als volgt toe aan een functie:

1. Zorg ervoor dat u het [project hebt geconfigureerd voor lokale ontwikkeling.](#configure-the-project-for-local-development)

2. Voeg het juiste NuGet-extensiepakket toe voor de specifieke binding. 

   Zie C#-klassebibliotheek met Visual Studio voor [Visual Studio.](./functions-bindings-register.md#local-csharp) Zoek de bindingsspecifieke NuGet-pakketvereisten in het referentieartikel voor de binding. Zoek bijvoorbeeld pakketvereisten voor de trigger Event Hubs in het [Event Hubs-bindingsverwijzingsartikel.](functions-bindings-event-hubs.md)

3. Als er app-instellingen zijn die de binding nodig heeft, voegt u deze toe aan de `Values` verzameling in het lokale [instellingsbestand](functions-run-local.md#local-settings-file). 

   De functie gebruikt deze waarden wanneer deze lokaal wordt uitgevoerd. Wanneer de functie wordt uitgevoerd in de functie-app in Azure, worden de instellingen van [de functie-app gebruikt.](#function-app-settings)

4. Voeg het juiste bindingskenmerk toe aan de methodehandtekening. In het volgende voorbeeld activeert een wachtrijbericht de functie en maakt de uitvoerbinding een nieuw wachtrijbericht met dezelfde tekst in een andere wachtrij.

    ```csharp
    public static class SimpleExampleWithOutput
    {
        [FunctionName("CopyQueueMessage")]
        public static void Run(
            [QueueTrigger("myqueue-items-source", Connection = "AzureWebJobsStorage")] string myQueueItem, 
            [Queue("myqueue-items-destination", Connection = "AzureWebJobsStorage")] out string myQueueItemCopy,
            ILogger log)
        {
            log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
            myQueueItemCopy = myQueueItem;
        }
    }
    ```
   De verbinding met Queue Storage wordt verkregen via de `AzureWebJobsStorage` instelling. Zie het referentieartikel voor de specifieke binding voor meer informatie. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>Functies testen

Met Azure Functions Core-hulpprogramma's kunt u Azure Functions-projecten uitvoeren op uw lokale ontwikkelcomputer. Zie Werken met Azure Functions Core Tools [voor meer informatie.](functions-run-local.md) U wordt gevraagd deze hulpprogramma's te installeren wanneer u voor het eerst een functie start vanuit Visual Studio. 

Uw functie testen in Visual Studio:

1. Druk op F5. Accepteer desgevraagd de aanvraag van Visual Studio om Azure Functions Core (CLI)-hulpprogramma's te downloaden en installeren. Mogelijk moet u ook een firewall-uitzondering inschakelen, zodat de hulpprogramma's HTTP-aanvragen kunnen afhandelen.

2. Terwijl het project wordt uitgevoerd, test u uw code zoals u een geïmplementeerde functie zou testen. 

   Zie Strategies for testing your code in Azure Functions (Strategieën voor [het testen van uw code in Azure Functions).](functions-test-a-function.md) Wanneer u een Visual Studio in de foutopsporingsmodus, worden onderbrekingspunten zoals verwacht getroffen.

<!---
For an example of how to test a queue triggered function, see the [queue triggered function quickstart tutorial](functions-create-storage-queue-triggered-function.md#test-the-function).  
-->


## <a name="publish-to-azure"></a>Publiceren naar Azure

Wanneer u vanuit Visual Studio publiceert, wordt een van de volgende twee implementatiemethoden gebruikt:

* [Web Deploy:](functions-deployment-technologies.md#web-deploy-msdeploy)verpakt en implementeert Windows-apps op elke IIS-server.
* [Zip-implementatie met uitvoeren vanaf pakket ingeschakeld: aanbevolen](functions-deployment-technologies.md#zip-deploy)voor Azure Functions implementaties.

Gebruik de volgende stappen om uw project te publiceren naar een functie-app in Azure.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>Instellingen voor functie-app

Omdat Visual Studio instellingen niet automatisch uploadt wanneer u het project publiceert, moeten alle instellingen die u in de local.settings.jstoevoegt, ook toevoegen aan de functie-app in Azure.

De eenvoudigste manier om de vereiste instellingen naar uw functie-app in Azure te uploaden, is door de koppeling Azure App Service beheren **te** selecteren die wordt weergegeven nadat u uw project hebt gepubliceerd.

:::image type="content" source="./media/functions-develop-vs/functions-vstools-app-settings.png" alt-text="Instellingen in het venster Publiceren":::

Als u deze koppeling **selecteert, wordt het dialoogvenster** Toepassingsinstellingen voor de functie-app weergegeven, waarin u nieuwe toepassingsinstellingen kunt toevoegen of bestaande kunt wijzigen.

![Toepassingsinstellingen](./media/functions-develop-vs/functions-vstools-app-settings2.png)

**Lokaal** geeft een instellingswaarde weer in local.settings.json-file en Extern **geeft** een huidige instellingswaarde weer in de functie-app in Azure. Kies **Instelling toevoegen om** een nieuwe app-instelling te maken. Gebruik de **koppeling Waarde invoegen uit Lokaal** om een instellingswaarde naar het veld Extern **te** kopiëren. Wijzigingen in behandeling worden naar het lokale instellingenbestand en de functie-app geschreven wanneer u **OK selecteert.**

> [!NOTE]
> Standaard wordt de local.settings.jsin het bestand niet ingecheckt bij broncodebeheer. Dit betekent dat als u een lokaal Functions-project kloont vanuit broncodebeheer, het project geen local.settings.jsin het bestand. In dit geval moet u handmatig de local.settings.jsmaken in het bestand  in de hoofdmap van het project, zodat het dialoogvenster Toepassingsinstellingen werkt zoals verwacht. 

U kunt toepassingsinstellingen ook op een van de volgende manieren beheren:

* [Gebruik de Azure Portal](functions-how-to-use-azure-function-app-settings.md#settings).
* [Gebruik de `--publish-local-settings` optie publiceren in de Azure Functions Core Tools](functions-run-local.md#publish).
* [Gebruik de Azure CLI.](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set)

## <a name="monitoring-functions"></a>Functies bewaken

De aanbevolen manier om de uitvoering van uw functies te bewaken, is door uw functie-app te integreren met Azure-toepassing Insights. Wanneer u een functie-app maakt in Azure Portal, wordt deze integratie standaard voor u uitgevoerd. Wanneer u echter uw functie-app maakt tijdens Visual Studio publiceren, wordt de integratie in uw functie-app in Azure niet uitgevoerd. Zie Enable Application Insights integration (Integratie van Application Insights inschakelen) voor meer informatie over het maken van verbinding Application Insights met [uw functie Application Insights app.](configure-monitoring.md#enable-application-insights-integration)

Zie Monitor Application Insights (Bewaking van Azure Functions) voor meer informatie over [bewaking met behulp Azure Functions.](functions-monitoring.md)

## <a name="next-steps"></a>Volgende stappen

Zie Werken met Azure Functions Core Tools voor meer informatie [over Azure Functions Core Tools.](functions-run-local.md)

Zie voor meer informatie over het ontwikkelen van functies als .NET-klassebibliotheken [Azure Functions C#-referentie voor ontwikkelaars.](functions-dotnet-class-library.md) Dit artikel bevat ook koppelingen naar voorbeelden van het gebruik van kenmerken om de verschillende typen bindingen te declaren die worden ondersteund door Azure Functions.    
