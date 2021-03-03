---
title: Block Chain configureren Data Manager met behulp van Azure CLI-Azure Block Chain Service
description: Een Block Chain-Data Manager voor de Azure Block Chain-service maken en beheren met behulp van Azure CLI
ms.date: 03/30/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: 867a51b60afa56005bbb297b345f8a9260160ab8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101722633"
---
# <a name="configure-blockchain-data-manager-using-azure-cli"></a>Blockchain Data Manager configureren met behulp van Azure CLI

Configureer Block Chain Data Manager voor de Azure Block Chain-service om Block Chain-gegevens vast te leggen naar een Azure Event Grid onderwerp.

Als u een Block Chain Data Manager-exemplaar wilt configureren, doet u het volgende:

* Een instantie van Block chain manager maken
* Een invoer maken naar een Azure Block Chain Service-transactie knooppunt
* Een uitvoer maken naar een Azure Event Grid onderwerp
* Een blockchain-toepassing toevoegen
* Een exemplaar starten

## <a name="prerequisites"></a>Vereisten

* Installeer de nieuwste [Azure cli](/cli/azure/install-azure-cli) en meld u aan met `az login` .
* Volledige [Snelstartgids: gebruik Visual Studio code om verbinding te maken met een Azure Block Chain Service consortium-netwerk](connect-vscode.md). De *Standard* -laag van de Azure Block Chain-service wordt aanbevolen bij het gebruik van Block Chain Data Manager.
* Een [Event Grid-onderwerp](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) maken
* Meer informatie over [Gebeurtenis-handlers in Azure Event Grid](../../event-grid/event-handlers.md)

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account.

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook openen in een afzonderlijk browsertabblad door naar [https://shell.azure.com/bash](https://shell.azure.com/bash) te gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, hebt u voor deze snelstart versie 2.0.51 of hoger van Azure CLI nodig. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurecli-interactive
az group create --name myRG --location eastus
```

## <a name="create-instance"></a>Instantie maken

Een Block Chain Data Manager-exemplaar bewaakt een Azure Block Chain Service-transactie knooppunt. Een instantie legt alle onbewerkte blokkeringen en ruwe transactiegegevens vast van het transactieknooppunt. Block Chain Data Manager een **RawBlockAndTransactionMsg** -bericht publiceert dat een hoofd verzameling informatie is die wordt geretourneerd door web3. ETH [GetBlock](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#getblock) -en [getTransaction](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#gettransaction) -query's.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Blockchain Data Manager instance name> \
                   --resource-type Microsoft.blockchain/watchers \
                   --is-full-object \
                   --properties <watcher resource properties>
```

| Parameter | Beschrijving |
|-----------|-------------|
| resource-group | De naam van de resource groep waar de Block Chain-Data Manager-instantie moet worden gemaakt. |
| naam | De naam van het block Chain-Data Manager exemplaar. |
| resource-type | Het resource type voor een Block Chain Data Manager-exemplaar is **micro soft. Block Chain/** volgers. |
| is-full-object | Hiermee wordt aangegeven dat eigenschappen opties bevatten voor de Watcher-resource. |
| properties | JSON-indelings teken reeks met eigenschappen voor de Watcher-resource. Kan worden door gegeven als een teken reeks of een bestand.  |

### <a name="create-instance-examples"></a>Voor beelden van exemplaren maken

Voor beeld van JSON-configuratie voor het maken van een Block chain manager-exemplaar in de regio **VS-Oost** .

``` json
{
    "location": "eastus",
    "properties": {
    }
}
```

| Element | Beschrijving |
|---------|-------------|
| location | Regio waar de Watcher-resource moet worden gemaakt |
| properties | Eigenschappen die moeten worden ingesteld bij het maken van de Watcher-resource |

Maak een Block Chain-Data Manager instantie met de naam *mywatcher* met behulp van een JSON-teken reeks voor configuratie.

``` azurecli-interactive
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                     --is-full-object \
                     --properties '{"location":"eastus"}'
```

Maak een Block Chain-Data Manager instantie met de naam *mywatcher* met behulp van een JSON-configuratie bestand.

``` azurecli
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                    --is-full-object \
                    --properties @watcher.json
```

## <a name="create-input"></a>Invoer maken

Een invoer verbindt Block Chain Data Manager met een Azure Block Chain Service-transactie knooppunt. Alleen gebruikers met toegang tot het transactie knooppunt kunnen een verbinding maken.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Input name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <input resource properties>
```

| Parameter | Beschrijving |
|-----------|-------------|
| resource-group | De naam van de resource groep waar de invoer bron wordt gemaakt. |
| naam | De naam van de invoer. |
| naamruimte | Gebruik de naam ruimte van de **micro soft. Block Chain** -provider. |
| resource-type | Het resource type voor een Block Chain-Data Manager invoer **is** inputs. |
| schijf | Het pad naar de Watcher waaraan de invoer is gekoppeld. Bijvoorbeeld volgers **/mywatcher**. |
| is-full-object | Hiermee wordt aangegeven dat eigenschappen opties voor de invoer bron bevatten. |
| properties | JSON-indelings teken reeks met eigenschappen voor de invoer bron. Kan worden door gegeven als een teken reeks of een bestand. |

### <a name="input-examples"></a>Invoer voorbeelden

Configuratie-JSON-voor beeld voor het maken van een invoer bron in de regio *VS Oost* die is verbonden met \<Blockchain member\> .

``` json
{
    "location": "eastus",
    "properties": {
        "inputType": "Ethereum",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/blockchainMembers/<Blockchain member>/transactionNodes/transaction-node"
        }
    }
}
```

| Element | Beschrijving |
|---------|-------------|
| location | De regio waar de invoer bron wordt gemaakt. |
| Type | Het grootboek type van het lid van de Azure Block Chain-service. Momenteel wordt **Ethereum** ondersteund. |
| resourceId | Het transactie knooppunt waarmee de invoer is verbonden. Vervang \<Subscription ID\> , \<Resource group\> en door \<Blockchain member\> de waarden voor de bron van het transactie knooppunt. De invoer maakt verbinding met het standaard transactie knooppunt voor het lid van de Azure Block Chain-service. |

Maak een invoer met de naam *myInput* voor *mywatcher* met BEhulp van een JSON-teken reeks voor configuratie.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myInput \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus", "properties":{"inputType":"Ethereum","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/BlockchainMembers/<Blockchain member>/transactionNodes/transaction-node"}}}'
```

Maak een invoer met de naam *myInput* voor *mywatcher* met BEhulp van een JSON-configuratie bestand.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name input \
                   --namespace Microsoft.Blockchain \ --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @input.json
```

## <a name="create-output"></a>Uitvoer maken

Een uitgaande verbinding verzendt blockchain-gegevens naar Azure Event Grid. U kunt Block Chain-gegevens naar één bestemming verzenden of block Chain-gegevens verzenden naar meerdere bestemmingen. Block Chain Data Manager ondersteunt meerdere uitgaande verbindingen van Event Grid onderwerp voor een gegeven block Chain Data Manager exemplaar.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Output name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <output resource properties>
```

| Parameter | Beschrijving |
|-----------|-------------|
| resource-group | De naam van de resource groep waar de uitvoer resource moet worden gemaakt. |
| naam | De naam van de uitvoer. |
| naamruimte | Gebruik de naam ruimte van de **micro soft. Block Chain** -provider. |
| resource-type | Het resource type voor een Block Chain-Data Manager uitvoer is **uitvoer**. |
| schijf | Het pad naar de Watcher waaraan de uitvoer is gekoppeld. Bijvoorbeeld volgers **/mywatcher**. |
| is-full-object | Hiermee wordt aangegeven dat eigenschappen opties voor de uitvoer bron bevatten. |
| properties | JSON-indelings teken reeks met eigenschappen voor de uitvoer bron. Kan worden door gegeven als een teken reeks of een bestand. |

### <a name="output-examples"></a>Uitvoer voorbeelden

Voor beeld van JSON-configuratie om een uitvoer bron te maken in de regio *VS-Oost* die is verbonden met een event grid-onderwerp met de naam \<event grid topic\> .

``` json
{
    "location": "eastus",
    "properties": {
        "outputType": "EventGrid",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"
        }
    }
}
```

| Element | Beschrijving |
|---------|-------------|
| location | De regio waar de uitvoer bron wordt gemaakt. |
| Type | Type uitvoer. Momenteel wordt **EventGrid** ondersteund. |
| resourceId | De resource waarmee de uitvoer is verbonden. Vervang \<Subscription ID\> , \<Resource group\> en door \<Blockchain member\> de waarden voor de gebeurtenis grid-resource. |

Maak een uitvoer met de naam *MYOUTPUT* voor *mywatcher* die verbinding maakt met een event grid-onderwerp met BEhulp van een JSON-configuratie teken reeks.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"outputType":"EventGrid","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"}}}'
```

Maak een uitvoer met de naam *MYOUTPUT* voor *mywatcher* die verbinding maakt met een event grid-onderwerp met BEhulp van een JSON-configuratie bestand.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @output.json
```

## <a name="add-blockchain-application"></a>Block Chain-toepassing toevoegen

Als u een Block Chain-toepassing toevoegt, Block Chain Data Manager de status van de gebeurtenis en eigenschap decoderen voor de toepassing. Anders worden alleen onbewerkte en onbewerkte transactie gegevens verzonden. Block Chain Data Manager detecteert ook contract adressen wanneer het contract wordt geïmplementeerd. U kunt meerdere Block Chain-toepassingen toevoegen aan een Block Chain Data Manager-exemplaar.


> [!IMPORTANT]
> Momenteel worden Block Chain-toepassingen die een type vaste- [matrix](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) of [toewijzings typen](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) declareren, niet volledig ondersteund. Eigenschappen die zijn gedeclareerd als matrix-of toewijzings typen, worden niet gedecodeerd in *ContractPropertiesMsg* -of *DecodedContractEventsMsg* -berichten.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Application name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <Application resource properties>
```

| Parameter | Beschrijving |
|-----------|-------------|
| resource-group | De naam van de resource groep waar de toepassings resource moet worden gemaakt. |
| naam | De naam van de toepassing. |
| naamruimte | Gebruik de naam ruimte van de **micro soft. Block Chain** -provider. |
| resource-type | Het resource type voor een Block Chain-Data Manager toepassing is **artefacten**. |
| schijf | Het pad naar de Watcher waaraan de toepassing is gekoppeld. Bijvoorbeeld volgers **/mywatcher**. |
| is-full-object | Hiermee wordt aangegeven dat eigenschappen opties voor de toepassings bron bevatten. |
| properties | JSON-indelings teken reeks met eigenschappen voor de toepassings bron. Kan worden door gegeven als een teken reeks of een bestand. |

### <a name="blockchain-application-examples"></a>Voor beelden van Block Chain-toepassingen

Configuratie-JSON-voor beeld om een toepassings bron te maken in de regio *VS-Oost* , die een slim contract bewaakt dat is gedefinieerd door de ABI en byte code van het contract.

``` json
{
    "location": "eastus",
    "properties": {
        "artifactType": "EthereumSmartContract",
        "content": {
            "abiFileUrl": "<ABI URL>",
            "bytecodeFileUrl": "<Bytecode URL>",
            "queryTargetTypes": [
                "ContractProperties",
                "ContractEvents"
            ]
        }
    }
}
```

| Element | Beschrijving |
|---------|-------------|
| location | De regio waar de toepassings resource moet worden gemaakt. |
| artifactType | Type toepassing. Momenteel wordt **EthereumSmartContract** ondersteund. |
| abiFileUrl | URL voor het JSON-bestand van het ABI voor Smart contract. Voor meer informatie over het verkrijgen van contract ABI en het maken van een URL raadpleegt u [contract Abi en byte code ophalen](data-manager-portal.md#get-contract-abi-and-bytecode) en [contract-Abi en byte code-URL maken](data-manager-portal.md#create-contract-abi-and-bytecode-url). |
| bytecodeFileUrl | URL voor het JSON-bestand van de geïmplementeerde byte code van het slimme contract. Zie voor meer informatie over het verkrijgen van de gedistribueerde gegevensbyte code voor het slimme contract en het maken van een URL [Get contract Abi en byte code](data-manager-portal.md#get-contract-abi-and-bytecode) en [Create contract Abi en byte code-URL](data-manager-portal.md#create-contract-abi-and-bytecode-url). Opmerking: voor Block Chain Data Manager is de **geïmplementeerde byte code** vereist. |
| queryTargetTypes | Gepubliceerde bericht typen. Opgeven van **ContractProperties** publiceert *ContractPropertiesMsg* -bericht type. Opgeven van **ContractEvents** publiceert *DecodedContractEventsMsg* -bericht type. Opmerking: *RawBlockAndTransactionMsg* -en *RawTransactionContractCreationMsg* -bericht typen worden altijd gepubliceerd. |

Maak een toepassing met de naam *mijn toepassing* voor *mywatcher* die een slim contract bewaakt dat is gedefinieerd door een JSON-teken reeks.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"artifactType":"EthereumSmartContract","content":{"abiFileUrl":"<ABI URL>","bytecodeFileUrl":"<Bytecode URL>","queryTargetTypes":["ContractProperties","ContractEvents"]}}}'
```

Maak een toepassing met de naam *mijn toepassing* voor *mywatcher* die een slim contract controleert dat is gedefinieerd met BEhulp van een JSON-configuratie bestand.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @artifact.json
```

## <a name="start-instance"></a>Exemplaar starten

Wanneer deze wordt uitgevoerd, controleert een Block chain manager-instantie Block Chain-gebeurtenissen van de gedefinieerde invoer en verzendt deze gegevens naar de gedefinieerde uitvoer.

``` azurecli
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Parameter | Beschrijving |
|-----------|-------------|
| actie | Gebruik **Start** om de Watcher uit te voeren. |
| ids | Watcher-Resource-ID. Vervang \<Subscription ID\> , \<Resource group\> en door \<Watcher name\> de waarden voor de Watcher-resource.|

### <a name="start-instance-example"></a>Voor beeld van start-exemplaar

Start een Block Chain-Data Manager instantie met de naam *mywatcher*.

``` azurecli-interactive
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="stop-instance"></a>Exemplaar stoppen

Stop een Block Chain Data Manager-exemplaar.

``` azurecli
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Parameter | Beschrijving |
|-----------|-------------|
| actie | Gebruik **stoppen** om de Watcher te stoppen. |
| ids | De naam van de Watcher. Vervang \<Subscription ID\> , \<Resource group\> en door \<Watcher name\> de waarden voor de Watcher-resource. |

### <a name="stop-watcher-example"></a>Stoppen van Watcher-voor beeld

Stop een instantie met de naam *mywatcher*.

``` azurecli-interactive
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="delete-instance"></a>Exemplaar verwijderen

Verwijder een Block Chain Data Manager-exemplaar.

``` azurecli
az resource delete \
                   --resource-group <Resource group> \
                   --name <Watcher name> \
                   --resource-type Microsoft.Blockchain/watchers
```

| Parameter | Beschrijving |
|-----------|-------------|
| resource-group | De naam van de resource groep van de Watcher die u wilt verwijderen. |
| naam | De naam van de Watcher die moet worden verwijderd. |
| resource-type | Het resource type voor een Block Chain Data Manager Watcher is **micro soft. Block Chain/** volgers. |

### <a name="delete-instance-example"></a>Voor beeld van instantie verwijderen

Verwijder een instantie met de naam *mywatcher* in de resource groep *myRG* .

``` azurecli-interactive
az resource delete \
                   --resource-group myRG \
                   --name mywatcher \
                   --resource-type Microsoft.blockchain/watchers
```

## <a name="next-steps"></a>Volgende stappen

Probeer de volgende zelf studie om een Block Chain-transactie bericht Verkenner te maken met behulp van Block Chain Data Manager en Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Blockchain Data Manager gebruiken om gegevens te verzenden naar Azure Cosmos DB](data-manager-cosmosdb.md)
