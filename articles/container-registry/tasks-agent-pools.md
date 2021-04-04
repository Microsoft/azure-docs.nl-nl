---
title: Toegewezen pool gebruiken om taken uit te voeren
description: Stel een toegewezen Compute pool (agent groep) in het REGI ster in om een Azure Container Registry taak uit te voeren.
ms.topic: article
ms.date: 10/12/2020
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: eeb9a71854f52da5c1a9f4befae93c377ad67b05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920304"
---
# <a name="run-an-acr-task-on-a-dedicated-agent-pool"></a>Een ACR-taak uitvoeren op een toegewezen agent groep

Stel een door Azure beheerde VM-groep (*agent groep*) in om het uitvoeren van uw [Azure container Registry-taken][acr-tasks] in een specifieke reken omgeving mogelijk te maken. Nadat u een of meer groepen hebt geconfigureerd in het REGI ster, kunt u een groep kiezen voor het uitvoeren van een taak in plaats van de standaard Compute-omgeving van de service.

Een agent groep biedt:

- **Ondersteuning voor virtuele netwerken** : wijs een agent groep toe aan een Azure-vnet, zodat u toegang hebt tot resources in het VNet, zoals container register, sleutel kluis of opslag.
- **Schaal naar behoefte** : Verhoog het aantal exemplaren in een agent groep voor Compute-intensieve taken of schaal op nul. De facturering is gebaseerd op de toewijzing van een groep. Zie [prijzen](https://azure.microsoft.com/pricing/details/container-registry/)voor meer informatie.
- **Flexibele opties** : Kies uit verschillende [Pools lagen](#pool-tiers) en schaal opties om te voldoen aan de behoeften van uw taak werkbelasting.
- **Azure-beheer** : taak groepen worden patches en onderhouden door Azure, waarbij gereserveerde toewijzing wordt geboden zonder dat u de afzonderlijke vm's hoeft te onderhouden.

Deze functie is beschikbaar in de service tier van het **Premium** -container register. Zie [Azure container Registry sku's][acr-tiers]voor meer informatie over de service lagen en limieten voor het REGI ster.

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-versie en er [zijn enkele beperkingen van toepassing](#preview-limitations). Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.
>

## <a name="preview-limitations"></a>Preview-beperkingen

- Taak agent-Pools bieden momenteel ondersteuning voor Linux-knoop punten. Windows-knoop punten worden momenteel niet ondersteund.
- Taak agent groepen zijn beschikbaar als preview in de volgende regio's: VS-West 2, Zuid-Centraal VS, VS-Oost 2, VS-Oost, VS-midden, USGov Arizona, USGov Texas en USGove Virginia.
- Voor elk REGI ster is het standaard quotum voor de totale vCPU (kern geheugen) 16 voor alle Standard agent-groepen en is 0 voor geïsoleerde agent groepen. Open een [ondersteunings aanvraag][open-support-ticket] voor aanvullende toewijzing.
- Het is niet mogelijk om een taak die wordt uitgevoerd op een agent groep te annuleren.

## <a name="prerequisites"></a>Vereisten

* Als u de stappen van Azure CLI in dit artikel wilt gebruiken, is Azure CLI versie 2.3.1 of later vereist. Zie [Azure CLI installeren][azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren. Of voer uit in [Azure Cloud shell](../cloud-shell/quickstart.md).
* Als u nog geen container register hebt, [maakt u er][create-reg-cli] een (vereist een Premium-laag) in een preview-regio.

## <a name="pool-tiers"></a>Groeps lagen

De lagen van een agent groep bieden de volgende resources per instantie in de pool.

|Laag    | Type  |  CPU  |Geheugen (GB)  |
|---------|---------|---------|---------|
|S1     |  Standard    | 2       |    3     |
|S2     |  Standard    | 4       |    8     |
|S3     |  Standard    | 8       |   16     |
|I6     |  alleenstaand    | 64     |   216     |


## <a name="create-and-manage-a-task-agent-pool"></a>Een taak agent groep maken en beheren

### <a name="set-default-registry-optional"></a>Standaard register instellen (optioneel)

Als u de Azure CLI-opdrachten die volgen, wilt vereenvoudigen, stelt u het standaard register in door de opdracht [AZ configure][az-configure] uit te voeren:

```azurecli
az configure --defaults acr=<registryName>
```

In de volgende voor beelden wordt ervan uitgegaan dat u het standaard register hebt ingesteld. Als dat niet het geval is, geeft u een `--registry <registryName>` para meter door in elke `az acr` opdracht.

### <a name="create-agent-pool"></a>Agent groep maken

Maak een agent groep met behulp van de opdracht [AZ ACR agent pool Create][az-acr-agentpool-create] . In het volgende voor beeld wordt een laag S2-pool (4 CPU/instance) gemaakt. Standaard bevat de groep 1 exemplaar.

```azurecli
az acr agentpool create \
    --name myagentpool \
    --tier S2
```

> [!NOTE]
> Het duurt enkele minuten om een agent groep te maken en andere beheer bewerkingen voor Pools uit te voeren.

### <a name="scale-pool"></a>Schaal groep

Schaal de pool grootte omhoog of omlaag met de opdracht [AZ ACR agent pool update][az-acr-agentpool-update] . In het volgende voor beeld wordt de pool op twee exemplaren geschaald. U kunt schalen naar 0-instanties.

```azurecli
az acr agentpool update \
    --name myagentpool \
    --count 2
```

## <a name="create-pool-in-a-virtual-network"></a>Een groep maken in een virtueel netwerk

### <a name="add-firewall-rules"></a>Firewall regels toevoegen

Taak agent-Pools hebben toegang tot de volgende Azure-Services nodig. De volgende firewall regels moeten worden toegevoegd aan bestaande netwerk beveiligings groepen of door de gebruiker gedefinieerde routes.

| Richting | Protocol | Bron         | Bronpoort | Doel          | Doel poort | Gebruikt    |
|-----------|----------|----------------|-------------|----------------------|-----------|---------|
| Uitgaand  | TCP      | VirtualNetwork | Alle         | AzureKeyVault        | 443       | Standaard |
| Uitgaand  | TCP      | VirtualNetwork | Alle         | Storage              | 443       | Standaard |
| Uitgaand  | TCP      | VirtualNetwork | Alle         | EventHub             | 443       | Standaard |
| Uitgaand  | TCP      | VirtualNetwork | Alle         | AzureActiveDirectory | 443       | Standaard |
| Uitgaand  | TCP      | VirtualNetwork | Alle         | AzureMonitor         | 443       | Standaard |

> [!NOTE]
> Als uw taken extra resources van het open bare Internet vereisen, voegt u de bijbehorende regels toe. Er zijn bijvoorbeeld aanvullende regels nodig voor het uitvoeren van een docker-taak waarmee de basis installatie kopieën worden opgehaald uit docker hub of een NuGet-pakket wordt hersteld.

### <a name="create-pool-in-vnet"></a>Groep maken in VNet

In het volgende voor beeld wordt een agent groep gemaakt in het subnet *mysubnet* van de *myvnet* netwerk:

```azurecli
# Get the subnet ID
subnetId=$(az network vnet subnet show \
        --resource-grop myresourcegroup \
        --vnet-name myvnet \
        --name mysubnetname \
        --query id --output tsv)

az acr agentpool create \
    --name myagentpool \
    --tier S2 \
    --subnet-id $subnetId
```

## <a name="run-task-on-agent-pool"></a>Taak uitvoeren in agent groep

In de volgende voor beelden ziet u hoe u een agent groep opgeeft bij het in de wachtrij plaatsen van een taak.

> [!NOTE]
> Als u een agent groep wilt gebruiken in een ACR-taak, moet u ervoor zorgen dat de pool ten minste één exemplaar bevat.
>

### <a name="quick-task"></a>Snelle taak

Een snelle taak in een wachtrij plaatsen op de agent pool met behulp van de opdracht [AZ ACR build][az-acr-build] en de `--agent-pool` para meter door geven:

```azurecli
az acr build \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --file Dockerfile \
    https://github.com/Azure-Samples/acr-build-helloworld-node.git#main
```

### <a name="automatically-triggered-task"></a>Automatisch geactiveerde taak

U kunt bijvoorbeeld een geplande taak maken op de agent pool met [AZ ACR Task Create][az-acr-task-create], waarbij de `--agent-pool` para meter wordt door gegeven.

```azurecli
az acr task create \
    --name mytask \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --schedule "0 21 * * *" \
    --file Dockerfile \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --commit-trigger-enabled false
```

Voer [AZ ACR Task run][az-acr-task-run]uit om de configuratie van de taak te controleren:

```azurecli
az acr task run \
    --name mytask
```

### <a name="query-pool-status"></a>Status van de query pool

Als u wilt zoeken naar het aantal uitvoeringen dat momenteel is gepland voor de agent pool, voert u [AZ ACR agent pool show][az-acr-agentpool-show]uit.

```azurecli
az acr agentpool show \
    --name myagentpool \
    --queue-count
```

## <a name="next-steps"></a>Volgende stappen

Bekijk de [zelf studie over ACR-taken](container-registry-tutorial-quick-task.md)voor meer voor beelden van builds en onderhoud van container installatie kopieën in de Cloud.



[acr-tasks]:           container-registry-tasks-overview.md
[acr-tiers]:           container-registry-skus.md
[azure-cli]:           /cli/azure/install-azure-cli
[open-support-ticket]: https://aka.ms/acr/support/create-ticket
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[az-configure]: /cli/azure#az-configure
[az-acr-agentpool-create]: /cli/azure/acr/agentpool#az-acr-agentpool-create
[az-acr-agentpool-update]: /cli/azure/acr/agentpool#az-acr-agentpool-update
[az-acr-agentpool-show]: /cli/azure/acr/agentpool#az-acr-agentpool-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[create-reg-cli]: container-registry-get-started-azure-cli.md
