---
title: Een CI/CD-pijp lijn instellen met de taak voor het bouwen van Azure Cosmos DB-emulator
description: Zelfstudie over het instellen van build- en releasewerkstroom in Azure DevOps met behulp van de build-taak van Cosmos DB Emulator
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 01/28/2020
ms.author: dech
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: a5b8842718aa2d9f90ac06283abc5fe2fdd925cb
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95996998"
---
# <a name="set-up-a-cicd-pipeline-with-the-azure-cosmos-db-emulator-build-task-in-azure-devops"></a>Een CI/CD-pijp lijn instellen met de taak Azure Cosmos DB emulator bouwen in azure DevOps
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

De Azure Cosmos DB Emulator is een lokale omgeving waarin de Azure Cosmos DB-service wordt geëmuleerd voor ontwikkelingsdoeleinden. Met behulp van de emulator kunt u uw toepassing lokaal ontwikkelen en testen, kosteloos en zonder een Azure-abonnement te maken. 

Met de functie voor het maken van Azure Cosmos DB emulator voor Azure DevOps kunt u hetzelfde doen in een CI-omgeving. Gebruik de build-taak voor het uitvoeren van tests met de emulator als onderdeel van uw build- en releasewerkstromen. De taak initialiseer een Docker-container waarin de emulator al wordt uitgevoerd en biedt een eindpunt dat kan worden gebruikt door de rest van de build-definitie. U kunt zo veel instanties van de emulator maken en starten als u nodig hebt. Elke instantie wordt uitgevoerd in een afzonderlijke container. 

In dit artikel leert u hoe u in Azure DevOps een CI-pijplijn instelt voor een ASP.NET-toepassing die de build-taak van Cosmos DB Emulator gebruikt voor het uitvoeren van tests. U kunt een soortgelijke aanpak gebruiken om een CI-pijplijn in te stellen voor een Node.js- of Python-toepassing. 

## <a name="install-the-emulator-build-task"></a>Build-taak van emulator installeren

U kunt de build-taak pas gebruiken als u deze eerst installeert in uw Azure DevOps-organisatie. Zoek de extensie **Azure Cosmos DB Emulator** in de [Marketplace](https://marketplace.visualstudio.com/items?itemName=azure-cosmosdb.emulator-public-preview) en klik op **Get it free.**

:::image type="content" source="./media/tutorial-setup-ci-cd/addExtension_1.png" alt-text="Build-taak van Azure Cosmos DB Emulator zoeken en installeren in de Marketplace van Azure DevOps":::

Kies vervolgens de organisatie waarin u de extensie installeren. 

> [!NOTE]
> Als u een uitbrei ding wilt installeren in een Azure DevOps-organisatie, moet u een account eigenaar of beheerder van een project verzameling zijn. Als u niet bevoegd bent, maar wel lid bent van het account, kunt u extensies aanvragen. [Meer informatie.](/azure/devops/marketplace/faq-extensions?preserve-view=true&view=vsts)

:::image type="content" source="./media/tutorial-setup-ci-cd/addExtension_2.png" alt-text="Een Azure DevOps-organisatie kiezen voor het installeren van een uitbrei ding":::

## <a name="create-a-build-definition"></a>Een build-definitie maken

Nu de uitbrei ding is geïnstalleerd, meldt u zich aan bij uw Azure DevOps-organisatie en gaat u naar het project in het dash board projecten. U kunt een [build-pipeline](/azure/devops/pipelines/get-started-designer?preserve-view=true&tabs=new-nav&view=vsts) aan uw project toevoegen of een bestaande build-pipeline wijzigen. Als u al een build-pipeline hebt, kunt u verdergaan met[Build-taak van de emulator toevoegen aan een build-definitie](#addEmulatorBuildTaskToBuildDefinition).

1. Om een nieuwe build-definitie te maken, gaat u naar het tabblad **Builds** in Azure DevOps. Selecteer **+ Nieuw.** \> **Nieuwe build-pipeline**

   :::image type="content" source="./media/tutorial-setup-ci-cd/CreateNewBuildDef_1.png" alt-text="Een nieuwe build-pipeline maken":::

2. Selecteer de gewenste **bron**, **teamproject**, **opslagplaats**, en de **standaardvertakking voor handmatige en geplande builds**. Nadat u vereiste opties hebt gekozen, selecteert u **Doorgaan**

   :::image type="content" source="./media/tutorial-setup-ci-cd/CreateNewBuildDef_2.png" alt-text="Selecteer het teamproject, de opslagplaats en de vertakking voor de build-pipeline":::

3. Selecteer ten slotte de gewenste sjabloon voor de build-pipeline. We selecteren de sjabloon **ASP.NET** voor deze zelfstudie. U hebt nu een build-pijp lijn die u kunt instellen voor het gebruik van de Azure Cosmos DB emulator-opbouw taak. 

> [!NOTE]
> Voor de agent groep die moet worden geselecteerd voor deze CI moet docker voor Windows zijn geïnstalleerd, tenzij de installatie hand matig wordt uitgevoerd in een eerdere taak als onderdeel van de CI. Zie het artikel [micro soft hosted agents](/azure/devops/pipelines/agents/hosted?preserve-view=true&tabs=yaml&view=azure-devops) voor een selectie van agent groepen; We raden u aan om te beginnen met `Hosted VS2017` .

De gehoste VS2019-agent groep wordt momenteel niet ondersteund door de Azure Cosmos DB-emulator. De emulator wordt echter al met VS2019 geïnstalleerd en u gebruikt deze door de emulator te starten met de volgende Power shell-cmdlets. Als u problemen ondervindt bij het gebruik van de VS2019, kunt u contact met het [Azure DevOps](https://developercommunity.visualstudio.com/spaces/21/index.html) -team bereiken voor hulp:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
Start-CosmosDbEmulator
```

## <a name="add-the-task-to-a-build-pipeline"></a><a name="addEmulatorBuildTaskToBuildDefinition"></a>De taak toevoegen aan een build-pipeline

1. Voordat u een taak toevoegt aan de build-pipeline, moet u een agenttaak toevoegen. Navigeer naar uw build-pipeline, selecteer **...** en kies **Een agenttaak toevoegen**.

1. Selecteer vervolgens het **+** pictogram naast de agent taak om de emulator-build-taak toe te voegen. Zoek naar **cosmos** in het zoekvak, selecteer **Azure Cosmos DB Emulator** en voeg deze toe aan de agent-taak. De build-taak start een container waarop al een exemplaar van de Cosmos DB-emulator wordt uitgevoerd. De Azure Cosmos DB Emulator-taak moet vóór alle andere taken worden geplaatst die ervan uitgaan dat de emulator wordt uitgevoerd.

   :::image type="content" source="./media/tutorial-setup-ci-cd/addExtension_3.png" alt-text="De Emulator-build-taak toevoegen aan de build-definitie":::

In deze zelfstudie voegt u de taak toe aan het begin om ervoor te zorgen dat de emulator beschikbaar is voordat onze tests worden uitgevoerd.

### <a name="add-the-task-using-yaml"></a>De taak toevoegen met behulp van YAML

Deze stap is optioneel en is alleen vereist als u de CI/CD-pijp lijn instelt met behulp van een YAML-taak. In dergelijke gevallen kunt u de YAML-taak definiëren, zoals wordt weer gegeven in de volgende code:

```yml
- task: azure-cosmosdb.emulator-public-preview.run-cosmosdbemulatorcontainer.CosmosDbEmulator@2
  displayName: 'Run Azure Cosmos DB Emulator'

- script: yarn test
  displayName: 'Run API tests (Cosmos DB)'
  env:
    HOST: $(CosmosDbEmulator.Endpoint)
    # Hardcoded key for emulator, not a secret
    AUTH_KEY: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
    # The emulator uses a self-signed cert, disable TLS auth errors
    NODE_TLS_REJECT_UNAUTHORIZED: '0'
```

## <a name="configure-tests-to-use-the-emulator"></a>Tests configureren voor gebruik van de emulator

Nu gaan we onze tests configureren voor het gebruik van de emulator. De build-taak van de emulator exporteert een omgevingsvariabele, 'CosmosDbEmulator.Endpoint', waarnaar taken verderop in de build-pijplijn eventueel aanvragen kunnen versturen. 

In deze zelfstudie gebruiken we de [taak VSTest](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/VsTestV2/README.md) om moduletests uit te voeren die zijn geconfigureerd via een **.runsettings**-bestand. Raadpleeg de [documentatie](/visualstudio/test/configure-unit-tests-by-using-a-dot-runsettings-file?preserve-view=true&view=vs-2017) voor meer informatie over het configureren van de moduletests. Het volledige voor beeld van een toepassings code dat u in dit document gebruikt, is beschikbaar op [github](https://github.com/Azure-Samples/documentdb-dotnet-todo-app)

Hieronder ziet u een voorbeeld van een **.runsettings**-bestand met parameters die moeten worden doorgegeven aan de moduletests van een toepassing. De gebruikte variabele `authKey` is de [bekende sleutel](./local-emulator.md#authenticate-requests) voor de emulator. `authKey` is de sleutel die wordt verwacht door de build-taak en deze moet dan ook zijn gedefinieerd in uw **.runsettings**-bestand.

```csharp
<RunSettings>
    <TestRunParameters>
    <Parameter name="endpoint" value="https://localhost:8081" />
    <Parameter name="authKey" value="C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==" />
    <Parameter name="database" value="ToDoListTest" />
    <Parameter name="collection" value="ItemsTest" />
  </TestRunParameters>
</RunSettings>
```

Als u een CI/CD-pijplijn instelt voor een toepassing die gebruikmaakt van de Azure Cosmos DB-API voor MongoDB, omvat de verbindingsreeks standaard poortnummer 10255. Deze poort is momenteel echter niet geopend. Gebruik daarom als alternatief poort 10250 om de verbinding tot stand te brengen. De verbindingsreeks van de Azure Cosmos DB-API voor MongoDB is dezelfde, alleen is het ondersteunde poortnummer 10250 in plaats van 10255.

Via de eigenschap `TestContext` in het testproject van de toepassing wordt er verwezen naar de parameters bij `TestRunParameters`. Hier volgt een voorbeeld van een test die wordt uitgevoerd met Cosmos DB.

```csharp
namespace todo.Tests
{
    [TestClass]
    public class TodoUnitTests
    {
        public TestContext TestContext { get; set; }

        [TestInitialize()]
        public void Initialize()
        {
            string endpoint = TestContext.Properties["endpoint"].ToString();
            string authKey = TestContext.Properties["authKey"].ToString();
            Console.WriteLine("Using endpoint: ", endpoint);
            DocumentDBRepository<Item>.Initialize(endpoint, authKey);
        }
        [TestMethod]
        public async Task TestCreateItemsAsync()
        {
            var item = new Item
            {
                Id = "1",
                Name = "testName",
                Description = "testDescription",
                Completed = false,
                Category = "testCategory"
            };

            // Create the item
            await DocumentDBRepository<Item>.CreateItemAsync(item);
            // Query for the item
            var returnedItem = await DocumentDBRepository<Item>.GetItemAsync(item.Id, item.Category);
            // Verify the item returned is correct.
            Assert.AreEqual(item.Id, returnedItem.Id);
            Assert.AreEqual(item.Category, returnedItem.Category);
        }

        [TestCleanup()]
        public void Cleanup()
        {
            DocumentDBRepository<Item>.Teardown();
        }
    }
}
```

Navigeer naar de uitvoeringsopties in de taak VSTest. Geef bij **Settings file** aan dat de tests worden geconfigureerd via een **.runsettings**-bestand. Voeg `-endpoint $(CosmosDbEmulator.Endpoint)` toe voor de optie **Override test run parameters**. Hiermee stelt u in dat de testtaak moet verwijzen naar het eindpunt van de build-taak van de emulator, en niet naar het eindpunt dat is gedefinieerd in het **.runsettings**-bestand.  

:::image type="content" source="./media/tutorial-setup-ci-cd/addExtension_5.png" alt-text="Variabele voor eindpunt vervangen door eindpunt van build-taak van emulator":::

## <a name="run-the-build"></a>De build uitvoeren

Nu gaan we de build **Opslaan en in de wachtrij zetten**. 

:::image type="content" source="./media/tutorial-setup-ci-cd/runBuild_1.png" alt-text="Scherm afbeelding toont een build met Save & Queue selected.":::

Als de build is gestart, ziet u dat de taak van Cosmos DB Emulator de Docker-installatiekopie gaat downloaden met de emulator geïnstalleerd. 

:::image type="content" source="./media/tutorial-setup-ci-cd/runBuild_4.png" alt-text="Scherm afbeelding toont de Cosmos D B-emulator taak die wordt opgetrokken.":::

Als de build is voltooid, kijkt u of de tests zijn gelukt, en of ze allemaal vanuit de build-taak zijn uitgevoerd met de Cosmos DB Emulator.

:::image type="content" source="./media/tutorial-setup-ci-cd/buildComplete_1.png" alt-text="Scherm afbeelding toont de voortgangs waarde op het tabblad samen vatting.":::

## <a name="next-steps"></a>Volgende stappen

Zie [De Azure Cosmos DB Emulator gebruiken voor lokaal ontwikkelen en testen](./local-emulator.md) voor meer informatie over het gebruik van de emulator voor lokaal ontwikkelen en testen.

Zie [de Azure Cosmos DB-emulator certificaten exporteren voor gebruik met Java, python en Node.js](./local-emulator-export-ssl-certificates.md) voor informatie over het exporteren van emulator TLS/SSL-certificaten.
