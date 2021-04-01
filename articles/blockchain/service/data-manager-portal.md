---
title: Block Chain configureren Data Manager met behulp van Azure Portal-Azure Block Chain-Service
description: Block Chain Data Manager voor de Azure Block Chain-service maken en beheren met behulp van de Azure Portal.
ms.date: 03/30/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: a151c28d31bf0bb7f21185fb161315d42f9563d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85200678"
---
# <a name="configure-blockchain-data-manager-using-the-azure-portal"></a>Blockchain Data Manager configureren met behulp van Azure Portal

Configureer Block Chain Data Manager voor de Azure Block Chain-service om Block Chain-gegevens vast te leggen en deze naar een Azure Event Grid onderwerp te verzenden.

Als u een Block Chain Data Manager-exemplaar wilt configureren, doet u het volgende:

* Een Block Chain Data Manager-exemplaar maken voor een Azure Block Chain Service-transactie knooppunt
* Uw Block Chain-toepassingen toevoegen

## <a name="prerequisites"></a>Vereisten

* Volledige [Snelstartgids: Maak een Block Chain-lid met behulp van de Azure Portal](create-member.md) of [Quick Start: een Azure Block Chain Service Block Chain-lid maken met behulp van Azure cli](create-member-cli.md). De *Standard* -laag van de Azure Block Chain-service wordt aanbevolen bij het gebruik van Block Chain Data Manager.
* Een [Event Grid-onderwerp](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) maken
* Meer informatie over [Gebeurtenis-handlers in Azure Event Grid](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Instantie maken

Een Blockchain Data Manager-instantie maakt verbinding met een Azure Blockchain Service-transactieknooppunt en bewaakt dit. Alleen gebruikers met toegang tot het transactie knooppunt kunnen een verbinding maken. Een instantie legt alle onbewerkte blokkeringen en ruwe transactiegegevens vast van het transactieknooppunt. Block Chain Data Manager een **RawBlockAndTransactionMsg** -bericht publiceert dat een hoofd verzameling informatie is die wordt geretourneerd door web3. ETH [GetBlock](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#getblock) -en [getTransaction](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#gettransaction) -query's.

Een uitgaande verbinding verzendt blockchain-gegevens naar Azure Event Grid. Wanneer u de instantie maakt, configureert u één uitgaande verbinding. Block Chain Data Manager ondersteunt meerdere uitgaande verbindingen van Event Grid onderwerp voor een gegeven block Chain Data Manager exemplaar. U kunt Block Chain-gegevens naar één bestemming verzenden of block Chain-gegevens verzenden naar meerdere bestemmingen. Als u een andere bestemming wilt toevoegen, voegt u extra uitgaande verbindingen toe aan het exemplaar.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Ga naar het lid van de Azure Block Chain-service waarmee u verbinding wilt maken met block Chain Data Manager. Selecteer **Blockchain Data Manager**.
1. Selecteer **Toevoegen**.

    ![Blockchain Data Manager toevoegen](./media/data-manager-portal/add-instance.png)

    Voer de volgende details in:

    Instelling | Beschrijving
    --------|------------
    Name | Voer een unieke naam in voor een verbonden Blockchain Data Manager. De naam van de Block Chain-Data Manager mag kleine letters en cijfers bevatten en mag Maxi maal 20 tekens lang zijn.
    Transactieknooppunt | Kies een transactie knooppunt. Alleen transactie knooppunten waarvoor u lees toegang hebt, worden weer gegeven.
    Verbindingsnaam | Voer een unieke naam in voor de uitgaande verbinding waarmee blockchain-transactiegegevens worden verzonden.
    Event grid-eindpunt | Kies een event grid-onderwerp in hetzelfde abonnement als de Block Chain-Data Manager instantie.

1. Selecteer **OK**.

    Het duurt minder dan een minuut om een Blockchain Data Manager-instantie te maken. Nadat de instantie is geïmplementeerd, wordt deze automatisch gestart. Een actief Block Chain Data Manager-exemplaar legt Block Chain-gebeurtenissen vast van het trans actie-knoop punt en verzendt gegevens naar de uitgaande verbindingen.

    De nieuwe instantie wordt weer gegeven in de lijst met block Chain-Data Manager instanties voor het lid van de Azure Block Chain-service.

    ![Lijst met instanties van Block Chain-gegevens leden](./media/data-manager-portal/instance-list.png)

## <a name="add-blockchain-application"></a>Block Chain-toepassing toevoegen

Als u een Block Chain-toepassing toevoegt, Block Chain Data Manager de status van de gebeurtenis en eigenschap decoderen voor de toepassing. Anders worden alleen onbewerkte en onbewerkte transactie gegevens verzonden. Block Chain Data Manager detecteert ook contract adressen wanneer het contract wordt geïmplementeerd. U kunt meerdere Block Chain-toepassingen toevoegen aan een Block Chain Data Manager-exemplaar.

> [!IMPORTANT]
> Momenteel worden Block Chain-toepassingen die een type vaste- [matrix](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) of [toewijzings typen](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) declareren, niet volledig ondersteund. Eigenschappen die zijn gedeclareerd als matrix-of toewijzings typen, worden niet gedecodeerd in *ContractPropertiesMsg* -of *DecodedContractEventsMsg* -berichten.

Voor Block Chain Data Manager is een ABI en een geïmplementeerd byte code-bestand vereist om de toepassing toe te voegen.

### <a name="get-contract-abi-and-bytecode"></a>ABI en byte code van het contract ophalen

In de ABI van het contract worden de slimme contractinterfaces gedefinieerd. Hierin wordt beschreven hoe u met het slimme contract communiceert. U kunt de [Azure Blockchain Development Kit voor Ethereum- extensie](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) gebruiken om de ABI van het contract naar het klembord te kopiëren.

1. Vouw in het deelvenster Visual Studio Code-verkenner de map **Build/contracten** van uw Solidity-project uit.
1. Klik met de rechter muisknop op het JSON-bestand met metagegevens van het contract. De bestandsnaam is de naam van het slimme contract gevolgd door de extensie **. json**.
1. Selecteer **ABI van het contract kopiëren**.

    ![Visual Studio Code-deelvenster met de selectie ABI van het contract kopiëren](./media/data-manager-portal/abi-devkit.png)

    De ABI van het contract wordt gekopieerd naar het klembord.

1. Sla de **abi**-matrix op als een JSON-bestand. Bijvoorbeeld *abi.json*. U gebruikt dit bestand in een latere stap.

Voor Blockchain Data Manager is de geïmplementeerde bytecode voor het slimme contract vereist. De geïmplementeerde bytecode wijkt af van de bytecode van het slimme contract. U gebruikt de Azure Blockchain Development Kit-extensie om de bytecode naar het klembord te kopiëren.

1. Vouw in het deelvenster Visual Studio Code-verkenner de map **Build/contracten** van uw Solidity-project uit.
1. Klik met de rechter muisknop op het JSON-bestand met metagegevens van het contract. De bestandsnaam is de naam van het slimme contract gevolgd door de extensie **. json**.
1. Selecteer **Bytecode van de transactie kopiëren**.

    ![Visual Studio Code-deelvenster met de selectie Bytecode van de transactie kopiëren](./media/data-manager-portal/bytecode-devkit.png)

    De bytecode wordt gekopieerd naar het klem bord.

1. Sla de **bytecode**-waarde op als een JSON-bestand. Bijvoorbeeld *bytecode.json*. U gebruikt dit bestand in een latere stap.

In het volgende voorbeeld ziet u *abi.json* en *bytecode.json*-bestanden in de VS Code-editor geopend. Uw bestanden moeten er vergelijkbaar uitzien.

![Voorbeeld van abi.json en bytecode. json-bestanden](./media/data-manager-portal/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>ABI en bytecode-URL van het contract maken

Blockchain Data Manager vereist dat de ABI- en bytecode-bestanden van het contract toegankelijk zijn voor een URL wanneer u een toepassing toevoegt. U kunt een Azure Storage-account gebruiken om een privé-toegankelijke URL op te geven.

#### <a name="create-storage-account"></a>Een opslagaccount maken

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Contractbestanden uploaden

1. Maak een nieuwe container voor het opslagaccount. Selecteer **Containers > Container**.

    ![Een container voor een opslagaccount maken](./media/data-manager-portal/create-container.png)

    | Veld | Beschrijving |
    |-------|-------------|
    | Naam  | Geef de container een naam. Bijvoorbeeld, *smartcontract* |
    | Openbaar toegangsniveau | Kies *Privé (geen anonieme toegang)* |

1. Selecteer **OK** om de container te maken.
1. Selecteer de container en selecteer vervolgens **Uploaden**.
1. Kies beide JSON-bestanden die u hebt gemaakt in de sectie [Contract ABI en bytecode ophalen](#get-contract-abi-and-bytecode).

    ![Blob uploaden](./media/data-manager-portal/upload-blobs.png)

    Selecteer **Uploaden**.

#### <a name="generate-url"></a>URL genereren

Genereer een gedeelde toegangshandtekening voor elke blob.

1. Selecteer de ABI JSON-blob.
1. Klik op **SAS genereren**
1. Stel de gewenste vervaldatum voor toegangshandtekeningen in en selecteer vervolgens **Genereren van het blob-SAS-token en de URL**.

    ![SAS-token genereren](./media/data-manager-portal/generate-sas.png)

1. Kopieer de **Blob-SAS-URL** en sla deze op in de volgende sectie.
1. Herhaal de stappen [URL genereren](#generate-url) voor de bytecode-JSON-blob.

### <a name="add-application-to-instance"></a>Toepassing toevoegen aan exemplaar

1. Selecteer uw Blockchain Data Manager-instantie in de lijst met instanties.
1. Selecteer **Blockchain-toepassingen**.
1. Selecteer **Toevoegen**.

    ![Een blockchain-toepassing toevoegen](./media/data-manager-portal/add-application.png)

    Voer de naam van de blockchain-toepassing en de ABI- en bytecode-URL's van het slimme contract in.

    Instelling | Beschrijving
    --------|------------
    Naam | Voer een unieke naam in voor de blockchain-toepassing die u wilt bijhouden.
    Contract-ABI | URL-pad naar het ABI-bestand van het contract. Zie [Contract-ABI en bytecode-URL maken](#create-contract-abi-and-bytecode-url)voor meer informatie.
    Contract-bytecode | URL-pad naar het bytecode-bestand. Zie [Contract-ABI en bytecode-URL maken](#create-contract-abi-and-bytecode-url)voor meer informatie.

1. Selecteer **OK**.

    Zodra de toepassing is gemaakt, wordt de toepassing weergegeven in de lijst met blockchain-toepassingen.

    ![Lijst van blockchain-toepassingen](./media/data-manager-portal/artifact-list.png)

U kunt het Azure Storage-account verwijderen of het gebruiken om meer blockchain-toepassingen te configureren. Als u het Azure Storage-account wilt verwijderen, kunt u de resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook het bijbehorende opslagaccount en eventuele andere resources die zijn gekoppeld aan de resourcegroep, verwijderd.

## <a name="stop-instance"></a>Exemplaar stoppen

Stop het block chain manager-exemplaar wanneer u wilt stoppen met het vastleggen van Block Chain-gebeurtenissen en het verzenden van gegevens naar de uitgaande verbindingen. Wanneer het exemplaar wordt gestopt, worden er geen kosten in rekening gebracht voor Block Chain Data Manager. Ga voor meer informatie naar het overzicht van [prijzen](https://azure.microsoft.com/pricing/details/blockchain-service).

1. Ga naar **overzicht** en selecteer **stoppen**.

    ![Exemplaar stoppen](./media/data-manager-portal/stop-instance.png)

## <a name="next-steps"></a>Volgende stappen

Probeer de volgende zelf studie om een Block Chain-transactie bericht Verkenner te maken met behulp van Block Chain Data Manager en Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Blockchain Data Manager gebruiken om gegevens te verzenden naar Azure Cosmos DB](data-manager-cosmosdb.md)