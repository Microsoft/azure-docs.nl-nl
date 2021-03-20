---
title: Blockchain Data Manager gebruiken om Azure Cosmos DB-Azure Blockchain Service bij te werken
description: Gebruik Blockchain Data Manager voor Azure Blockchain Service om blockchain-gegevens te verzenden naar Azure Cosmos DB
ms.date: 03/08/2020
ms.topic: tutorial
ms.reviewer: chroyal
ms.openlocfilehash: 69790787bc888448f2f40178bd12ee7058cc5892
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91283447"
---
# <a name="tutorial-use-blockchain-data-manager-to-send-data-to-azure-cosmos-db"></a>Zelfstudie: Blockchain Data Manager gebruiken om gegevens te verzenden naar Azure Cosmos DB

In deze zelfstudie gebruikt u Blockchain Data Manager voor de Azure Blockchain Service om blockchain-transactiegegevens op te nemen in Azure Cosmos DB. Blockchain Data Manager legt blockchain-grootboekgegevens vast in en transformeert en levert deze aan Azure Event Grid-onderwerpen. Vanuit Azure Event Grid gebruikt u een Azure Logic App-connector om documenten te maken in een Azure Cosmos DB-Database. Wanneer u klaar bent met de zelfstudie, kunt u blockchain-transactiegegevens in Azure Cosmos DB Data Explorer verkennen.

[![Schermopname toont details van de blockchain-transactie.](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Een Blockchain Data Manager-exemplaar maken
> * Een blockchain-toepassing toevoegen om transactiegegevens en -gebeurtenissen te decoderen
> * Een Azure Cosmos DB-account en -database maken om transactiegegevens op te slaan
> * Een Azure Logic App maken om een Azure Event Grid-onderwerp te koppelen aan Azure Cosmos DB
> * Een transactie naar een Blockchain-grootboek verzenden
> * De gedecodeerde transactiegegevens in Azure Cosmos DB weergeven

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* [Quickstart: Een blockchainlid maken met behulp van de Azure-portal](create-member.md) of [Quickstart: Een blockchainlid in Azure Blockchain Service maken met behulp van de Azure CLI voltooien](create-member-cli.md)
* [Quickstart: Visual Studio Code gebruiken om verbinding te maken met een Azure Blockchain Service-consortiumnetwerk](connect-vscode.md). De Snelstartgids helpt u bij het installeren van de [Azure Blockchain Development Kit voor Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) en het instellen van uw blockchain-ontwikkelomgeving.
* Voltooi de [Zelfstudie: Visual Studio Code gebruiken om slimme contracten te maken, bouwen en implementeren](send-transaction.md). In de zelfstudie wordt uitgelegd hoe u een voorbeeld van een slim contract maakt.
* Een [Event Grid-onderwerp](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) maken
* Meer informatie over [Gebeurtenis-handlers in Azure Event Grid](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Instantie maken

Een Blockchain Data Manager-instantie maakt verbinding met een Azure Blockchain Service-transactieknooppunt en bewaakt dit. Een instantie legt alle onbewerkte blokkeringen en ruwe transactiegegevens vast van het transactieknooppunt. Een uitgaande verbinding verzendt blockchain-gegevens naar Azure Event Grid. Wanneer u de instantie maakt, configureert u één uitgaande verbinding.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Ga naar het lid van Azure Blockchain Service dat u hebt gemaakt in de vereiste [Quickstart: Maak een blockchain-lid met behulp van de Azure Portal](create-member.md). Selecteer **Blockchain Data Manager**.
1. Selecteer **Toevoegen**.

    ![Blockchain Data Manager toevoegen](./media/data-manager-cosmosdb/add-instance.png)

    Voer de volgende details in:

    Instelling | Voorbeeld | Beschrijving
    --------|---------|------------
    Naam | mywatcher | Voer een unieke naam in voor een verbonden Blockchain Data Manager.
    Transactieknooppunt | myblockchainmember | Kies het standaard transactieknooppunt van het lid van Azure Blockchain Service dat u in de vereiste hebt gemaakt.
    Verbindingsnaam | cosmosdb | Voer een unieke naam in voor de uitgaande verbinding waarmee blockchain-transactiegegevens worden verzonden.
    Event grid-eindpunt | myTopic | Kies een event grid-onderwerp dat u in de vereiste hebt gemaakt. Opmerking: De Blockchain Data Manager-instantie en het event grid-onderwerp moeten zich in hetzelfde abonnement bevinden.

1. Selecteer **OK**.

    Het duurt minder dan een minuut om een Blockchain Data Manager-instantie te maken. Nadat de instantie is geïmplementeerd, wordt deze automatisch gestart. Een actieve Blockchain Data Manager-instantie legt blockchain-gebeurtenissen vast vanuit het transactieknooppunt en verzendt gegevens naar het event grid.

## <a name="add-application"></a>Toepassing toevoegen

Voeg de blockchain-toepassing **helloblockchain** toe, zodat Blockchain Data Manager de gebeurtenis- en eigenschapsstatus decodeert. Blockchain Data Manager vereist het ABI en bytecode-bestand van het slimme contract om de toepassing toe te voegen.

### <a name="get-contract-abi-and-bytecode"></a>ABI en bytecode van het contract ophalen

In de ABI van het contract worden de slimme contractinterfaces gedefinieerd. Hierin wordt beschreven hoe u met het slimme contract communiceert. U kunt de [Azure Blockchain Development Kit voor Ethereum- extensie](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) gebruiken om de ABI van het contract naar het klembord te kopiëren.

1. Vouw in het deelvenster Visual Studio Code-verkenner de map **build/contracten** uit van het Solidity-project **helloblockchain** dat u hebt gemaakt in de [Zelfstudie Vereisten: Gebruik Visual Studio Code om slimme contracten te maken, bouwen en implementeren](send-transaction.md).
1. Klik met de rechter muisknop op het JSON-bestand met metagegevens van het contract. De bestandsnaam is de naam van het slimme contract gevolgd door de extensie **. json**.
1. Selecteer **ABI van het contract kopiëren**.

    ![Visual Studio Code-deelvenster met de selectie ABI van het contract kopiëren](./media/data-manager-cosmosdb/abi-devkit.png)

    De ABI van het contract wordt gekopieerd naar het klembord.

1. Sla de **abi**-matrix op als een JSON-bestand. Bijvoorbeeld *abi.json*. U gebruikt dit bestand in een latere stap.

Voor Blockchain Data Manager is de geïmplementeerde bytecode voor het slimme contract vereist. De geïmplementeerde bytecode wijkt af van de bytecode van het slimme contract. U gebruikt de Azure Blockchain Development Kit-extensie om de bytecode naar het klembord te kopiëren.

1. Vouw in het deelvenster Visual Studio Code-verkenner de map **Build/contracten** van uw Solidity-project uit.
1. Klik met de rechter muisknop op het JSON-bestand met metagegevens van het contract. De bestandsnaam is de naam van het slimme contract gevolgd door de extensie **. json**.
1. Selecteer **Bytecode van de transactie kopiëren**.

    ![Visual Studio Code-deelvenster met de selectie Bytecode van de transactie kopiëren](./media/data-manager-cosmosdb/bytecode-devkit.png)

    De bytecode wordt gekopieerd naar het klem bord.

1. Sla de **bytecode**-waarde op als een JSON-bestand. Bijvoorbeeld *bytecode.json*. U gebruikt dit bestand in een latere stap.

In het volgende voorbeeld ziet u *abi.json* en *bytecode.json*-bestanden in de VS Code-editor geopend. Uw bestanden moeten er vergelijkbaar uitzien.

![Voorbeeld van abi.json en bytecode. json-bestanden](./media/data-manager-cosmosdb/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>ABI en bytecode-URL van het contract maken

Blockchain Data Manager vereist dat de ABI- en bytecode-bestanden van het contract toegankelijk zijn voor een URL wanneer u een toepassing toevoegt. U kunt een Azure Storage-account gebruiken om een privé-toegankelijke URL op te geven.

#### <a name="create-storage-account"></a>Een opslagaccount maken

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Contractbestanden uploaden

1. Maak een nieuwe container voor het opslagaccount. Selecteer **Containers > Container**.

    ![Een container voor een opslagaccount maken](./media/data-manager-cosmosdb/create-container.png)

    | Instelling | Beschrijving |
    |---------|-------------|
    | Naam  | Geef de container een naam. Bijvoorbeeld, *smartcontract* |
    | Openbaar toegangsniveau | Kies *Privé (geen anonieme toegang)* |

1. Selecteer **OK** om de container te maken.
1. Selecteer de container en selecteer vervolgens **Uploaden**.
1. Kies beide JSON-bestanden die u hebt gemaakt in de sectie [Contract ABI en bytecode ophalen](#get-contract-abi-and-bytecode).

    ![Blob uploaden](./media/data-manager-cosmosdb/upload-blobs.png)

    Selecteer **Uploaden**.

#### <a name="generate-url"></a>URL genereren

Genereer een gedeelde toegangshandtekening voor elke blob.

1. Selecteer de ABI JSON-blob.
1. Klik op **SAS genereren**
1. Stel de gewenste vervaldatum voor toegangshandtekeningen in en selecteer vervolgens **Genereren van het blob-SAS-token en de URL**.

    ![SAS-token genereren](./media/data-manager-cosmosdb/generate-sas.png)

1. Kopieer de **Blob-SAS-URL** en sla deze op in de volgende sectie.
1. Herhaal de stappen [URL genereren](#generate-url) voor de bytecode-JSON-blob.

### <a name="add-helloblockchain-application-to-instance"></a>Helloblockchain-toepassing toevoegen aan instantie

1. Selecteer uw Blockchain Data Manager-instantie in de lijst met instanties.
1. Selecteer **Blockchain-toepassingen**.
1. Selecteer **Toevoegen**.

    ![Een blockchain-toepassing toevoegen](./media/data-manager-cosmosdb/add-application.png)

    Voer de naam van de blockchain-toepassing en de ABI- en bytecode-URL's van het slimme contract in.

    Instelling | Beschrijving
    --------|------------
    Naam | Voer een unieke naam in voor de blockchain-toepassing die u wilt bijhouden.
    Contract-ABI | URL-pad naar het ABI-bestand van het contract. Zie [Contract-ABI en bytecode-URL maken](#create-contract-abi-and-bytecode-url)voor meer informatie.
    Contract-bytecode | URL-pad naar het bytecode-bestand. Zie [Contract-ABI en bytecode-URL maken](#create-contract-abi-and-bytecode-url)voor meer informatie.

1. Selecteer **OK**.

    Zodra de toepassing is gemaakt, wordt de toepassing weergegeven in de lijst met blockchain-toepassingen.

    ![Lijst van blockchain-toepassingen](./media/data-manager-cosmosdb/artifact-list.png)

U kunt het Azure Storage-account verwijderen of het gebruiken om meer blockchain-toepassingen te configureren. Als u het Azure Storage-account wilt verwijderen, kunt u de resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook het bijbehorende opslagaccount en eventuele andere resources die zijn gekoppeld aan de resourcegroep, verwijderd.

## <a name="create-azure-cosmos-db"></a>Een Azure Cosmos DB-exemplaar maken

[!INCLUDE [cosmos-db-create-storage-account](../../../includes/cosmos-db-create-dbaccount.md)]

### <a name="add-a-database-and-container"></a>Een database en een container toevoegen

U kunt Data Explorer in de Azure-portal gebruiken om een database en een container te maken.

1. Selecteer **Data Explorer** op de linkernavigatiebalk op uw accountpagina van Azure Cosmos DB en selecteer **Nieuwe container**.
1. Geef in het deelvenster **Container toevoegen** de instellingen voor de nieuwe container op.

    ![Containerinstellingen toevoegen](./media/data-manager-cosmosdb/add-container.png)

    | Instelling | Beschrijving
    |---------|-------------|
    | Database-id | Voer **blockchain-data** in als naam voor de nieuwe database. |
    | Doorvoer | Laat de doorvoer op **400** aanvraageenheden per seconde (RU/s). U kunt de doorvoer later opschalen als u de latentie wilt beperken.|
    | Container-id | Voer **Berichten** in als de naam voor de nieuwe container. |
    | Partitiesleutel | Gebruik **/MessageType** als de partitiesleutel. |

1. Selecteer **OK**. In Data Explorer worden de nieuwe database en container weergegeven die u hebt gemaakt.

## <a name="create-logic-app"></a>Een logische app maken

Azure Logic Apps helpt u bij het plannen en automatiseren van bedrijfsprocessen en werkstromen wanneer u systemen en services moet integreren. U kunt een logische app gebruiken om Event Grid te verbinden met Azure Cosmos DB.

1. Selecteer in [Azure Portal](https://portal.azure.com)**Een resource maken** > **Integratie** > **Logische app**.
1. Geef details op over waar u uw logische app moet maken. Selecteer **Maken** als u klaar bent.

    Zie [Automatische werkstromen maken met Azure Logic Apps](../../logic-apps/quickstart-create-first-logic-app-workflow.md) voor meer informatie over het maken van logische apps.

1. Nadat Azure uw app heeft geïmplementeerd, selecteert u de resource van de logische app.
1. Selecteer in de Logic Apps Designer onder **Sjablonen** **Lege logische app**.

### <a name="add-event-grid-trigger"></a>Event Grid-trigger toevoegen

Elke logische app moet beginnen met een trigger, die wordt geactiveerd wanneer er een bepaalde gebeurtenis plaatsvindt of wanneer er aan een bepaalde voorwaarde is voldaan. Telkens wanneer de trigger wordt geactiveerd, maakt de Logic Apps-engine een exemplaar van een logische app dat wordt gestart en de werkstroom uitvoert. Gebruik een Azure Event Grid-trigger om blockchain-transactiegegevens van Event Grid naar Cosmos DB te verzenden.

1. Zoek en selecteer in de Logic Apps Designer de **Azure Event Grid**-connector.
1. Selecteer in het tabblad **Triggers** **Wanneer een resourcegebeurtenis optreedt**.
1. Maak een API-verbinding met uw Event Grid-onderwerp.

    ![Event Grid-triggerinstellingen](./media/data-manager-cosmosdb/event-grid-trigger.png)

    | Instelling | Beschrijving
    |---------|-------------|
    | Abonnement | Kies het abonnement dat het Event Grid-onderwerp bevat. |
    | Resourcetype | Kies **Microsoft.EventGrid.Topics**. |
    | Resourcenaam | Kies de naam van het Event Grid-onderwerp waarin Blockchain Data Manager berichten over transactiegegevens verzendt. |

### <a name="add-cosmos-db-action"></a>Cosmos DB-actie toevoegen

Voeg een actie toe om een document in Cosmos DB te maken voor elke transactie. Gebruik het transactiebericht-type als de partitiesleutel om de berichten te categoriseren.

1. Selecteer **Nieuwe stap**.
1. In **Kies een actie** zoekt u naar **Azure Cosmos DB**.
1. Kies **Azure Cosmos DB > Acties > Document bijwerken**.
1. Maak een API-verbinding met uw Cosmos DB-database.

    ![Cosmos DB-verbindingsinstellingen](./media/data-manager-cosmosdb/cosmosdb-connection.png)

    | Instelling | Beschrijving
    |---------|-------------|
    | Verbindingsnaam | Kies het abonnement dat het Event Grid-onderwerp bevat. |
    | DocumentDB-account | Kies het DocumentDB-account dat u hebt gemaakt in het gedeelte [Azure Cosmos DB-account maken](#create-azure-cosmos-db). |

1. Voer de **Database-ID** en **Verzamelings-ID** in voor uw Azure Cosmos DB die u eerder hebt gemaakt in de sectie [Een database en container toevoegen](#add-a-database-and-container).

1. Selecteer de instelling **Document**. Selecteer in de pop-out *Dynamische inhoud toevoegen* de optie **Expressie** en kopieer en plak de volgende expressie:

    ```
    addProperty(triggerBody()?['data'], 'id', utcNow())
    ```

    Met de expressie wordt het gegevensgedeelte van het bericht opgehaald en wordt de ID ingesteld op een timestamp-waarde.

1. Selecteer **Nieuwe parameter toevoegen** en kies **Waarde partitiesleutel**.
1. Stel de **Waarde voor de partitiesleutel** in op `"@{triggerBody()['data']['MessageType']}"`. De waarde moet tussen dubbele aanhalingstekens worden geplaatst.

    ![Logic Apps Designer met Cosmos DB-instellingen](./media/data-manager-cosmosdb/create-action.png)

    Met de waarde wordt de partitiesleutel ingesteld op het type transactiebericht.

1. Selecteer **Opslaan**.

De logische app bewaakt het Event Grid-onderwerp. Wanneer een nieuw transactiebericht wordt verzonden vanuit Blockchain Data Manager, maakt de logische app een document in Cosmos DB.

## <a name="send-a-transaction"></a>Een transactie verzenden

Vervolgens verzendt u een transactie naar het blockchain-grootboek om te testen wat u hebt gemaakt. Gebruik de **SendRequest**-functie van het **HelloBlockchain**-contract die u hebt gemaakt in de vereiste [Zelfstudie: Gebruik Visual Studio Code om slimme contracten te maken, bouwen en implementeren](send-transaction.md).

1. Gebruik de Azure Blockchain Development Kit-pagina voor interactie met slimme contracten om de **SendRequest**-functie via een transactie aan te roepen. Klik met de rechter muisknop op **HelloBlockchain.sol** en kies **Pagina voor de interactie van een Smart-contract weergeven** in het menu.

    ![Pagina voor interactie met slimme contracten weergeven kiezen in het menu](./media/data-manager-cosmosdb/contract-interaction.png)

1. Kies de actie **SendRequest** voor het contract en voer **Hello, Blockchain!** in als de parameter **requestMessage**. Selecteer **Uitvoeren** om de functie **SendRequest** aan te roepen via een transactie.

    ![SendRequest-actie uitvoeren](./media/data-manager-cosmosdb/sendrequest-action.png)

Met de functie SendRequest worden de velden **RequestMessage** en **Status** ingesteld. De huidige status voor **RequestMessage** is het argument dat u hebt doorgegeven via **Hello, Blockchain**. De waarde van het veld **Status** blijft **Aanvraag**.

## <a name="view-transaction-data"></a>Transactiegegevens weergeven

Nu u uw Blockchain Data Manager hebt verbonden met Azure Cosmos DB, kunt u de blockchain-transactieberichten bekijken in Cosmos DB Data Explorer.

1. Ga naar de Cosmos DB Data Explorer-weergave. Bijvoorbeeld **cosmosdb-blockchain > Data Explorer > blockchain-gegevens > Berichten > items**.

    ![Cosmos DB Data Explorer](./media/data-manager-cosmosdb/data-explorer.png)

    Data Explorer geeft een lijst van de blockchain-gegevensberichten die zijn gemaakt in de Cosmos DB-database.

1. Blader door de berichten door item-ID te selecteren en het bericht te zoeken met de overeenkomende transactie-hash.

    [![Schermopname toont details van de blockchain-transactie van een geselecteerd item.](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

    Het bericht van de onbewerkte transactie bevat details over de transactie. De informatie over de eigenschap is echter versleuteld.

    Omdat u het HelloBlockchain-slimme contract hebt toegevoegd aan het Blockchain Data Manager-exemplaar, wordt er ook een **ContractProperties**-berichttype verzonden dat gedecodeerde eigenschapsinformatie bevat.

1. Zoek het **ContractProperties**-bericht voor de transactie. Dit moet het volgende bericht in de lijst zijn.

    [![Details van blockchain-transactie](./media/data-manager-cosmosdb/properties-msg.png)](./media/data-manager-cosmosdb/properties-msg.png#lightbox)

    De **DecodedProperties**-matrix bevat de eigenschappen van de transactie.

Gefeliciteerd! U hebt een transactiebericht-verkenner gemaakt met behulp van Blockchain Data Manager en Azure Cosmos DB.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de resourcegroep en alle gerelateerde resources verwijderen wanneer u deze niet meer nodig hebt. Een resourcegroep verwijderen:

1. Ga in de Azure Portal naar **Resourcegroep** in het linkernavigatievenster en selecteer de resourcegroep die u wilt verwijderen.
1. Selecteer **Resourcegroep verwijderen**. Controleer de verwijdering door de naam van de resourcegroep in te voeren en **Verwijderen** te selecteren.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het integreren met blockchain-grootboeken.

> [!div class="nextstepaction"]
> [De Ethereum Blockchain-connector gebruiken met Azure Logic Apps](ethereum-logic-app.md)
