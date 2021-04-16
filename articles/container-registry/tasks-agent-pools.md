---
title: Toegewezen pool gebruiken om taken uit te voeren - Taken
description: Stel een toegewezen rekengroep (agentpool) in het register in om een Azure Container Registry uitvoeren.
ms.topic: article
ms.date: 10/12/2020
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 21db066b3f18106938d11fbd8e2cfe688c1ef276
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389550"
---
# <a name="run-an-acr-task-on-a-dedicated-agent-pool"></a>Een ACR-taak uitvoeren op een toegewezen agentpool

Stel een door Azure beheerde VM-pool *(agentpool)* in om het uitvoeren van uw Azure Container Registry [in][acr-tasks] een toegewezen rekenomgeving mogelijk te maken. Nadat u een of meer pools in het register hebt geconfigureerd, kunt u een pool kiezen om een taak uit te voeren in plaats van de standaardrekenomgeving van de service.

Een agentpool biedt:

- **Ondersteuning voor virtuele netwerken:** wijs een agentgroep toe aan een Azure VNet, zodat u toegang hebt tot resources in het VNet, zoals een containerregister, sleutelkluis of opslag.
- **Schaal naar behoefte:** verhoog het aantal exemplaren in een agentpool voor rekenintensieve taken of schaal naar nul. Facturering is gebaseerd op pooltoewijzing. Zie Prijzen voor [meer informatie.](https://azure.microsoft.com/pricing/details/container-registry/)
- **Flexibele opties:** kies uit verschillende [poollagen en](#pool-tiers) schaalopties om te voldoen aan de workloadbehoeften van uw taak.
- **Azure-beheer:** taakgroepen worden gepatcht en onderhouden door Azure en bieden gereserveerde toewijzing zonder dat de afzonderlijke VM's hoeven te worden onderhouden.

Deze functie is beschikbaar in de **servicelaag Premium** Container Registry. Zie SKU's voor meer informatie over [registerservicelagen Azure Container Registry limieten.][acr-tiers]

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-versie en er gelden [enkele beperkingen.](#preview-limitations) Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.
>

## <a name="preview-limitations"></a>Preview-beperkingen

- Taakagentpools ondersteunen momenteel Linux-knooppunten. Windows-knooppunten worden momenteel niet ondersteund.
- Taakagentgroepen zijn beschikbaar in preview in de volgende regio's: VS - west 2, VS - zuid-centraal, VS - oost 2, VS - oost, VS - centraal, Europa - west, Europa - noord, Canada - centraal, USGov Arizona, USGov Texas en USGov Virginia.
- Voor elk register is het standaardquotum voor het totale aantal vCPU's (kernen) 16 voor alle standaardagentpools en 0 voor geïsoleerde agentpools. Open een [ondersteuningsaanvraag][open-support-ticket] voor aanvullende toewijzing.
- U kunt een taak die wordt uitgevoerd op een agentpool momenteel niet annuleren.

## <a name="prerequisites"></a>Vereisten

* Voor het gebruik van de Azure CLI-stappen in dit artikel is Azure CLI versie 2.3.1 of hoger vereist. Zie [Azure CLI installeren][azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren. Of voer uit in [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Als u nog geen containerregister hebt, maakt [u][create-reg-cli] er een (Premium-laag vereist) in een preview-regio.

## <a name="pool-tiers"></a>Poollagen

Agentpoollagen bieden de volgende resources per exemplaar in de pool.

|Laag    | Type  |  CPU  |Geheugen (GB)  |
|---------|---------|---------|---------|
|S1     |  Standard    | 2       |    3     |
|S2     |  Standard    | 4       |    8     |
|S3     |  Standard    | 8       |   16     |
|I6     |  Geïsoleerd    | 64     |   216     |


## <a name="create-and-manage-a-task-agent-pool"></a>Een taakagentpool maken en beheren

### <a name="set-default-registry-optional"></a>Standaardregister instellen (optioneel)

Als u de volgende Azure CLI-opdrachten wilt vereenvoudigen, stelt u het standaardregister in door de [opdracht az configure uit te][az-configure] voeren:

```azurecli
az configure --defaults acr=<registryName>
```

In de volgende voorbeelden wordt ervan uitgenomen dat u het standaardregister hebt ingesteld. Zo niet, geef dan een `--registry <registryName>` parameter door in elke `az acr` opdracht.

### <a name="create-agent-pool"></a>Agentpool maken

Maak een agentpool met behulp van [de opdracht az acr agentpool create.][az-acr-agentpool-create] In het volgende voorbeeld wordt een laag S2-pool (4 CPU/exemplaar) gemaakt. De pool bevat standaard 1 exemplaar.

```azurecli
az acr agentpool create \
    --name myagentpool \
    --tier S2
```

> [!NOTE]
> Het maken van een agentpool en andere poolbeheerbewerkingen duurt enkele minuten.

### <a name="scale-pool"></a>Pool schalen

Schaal de poolgrootte omhoog of omlaag met de [opdracht az acr agentpool update.][az-acr-agentpool-update] In het volgende voorbeeld wordt de pool geschaald naar 2 exemplaren. U kunt schalen naar 0 exemplaren.

```azurecli
az acr agentpool update \
    --name myagentpool \
    --count 2
```

## <a name="create-pool-in-a-virtual-network"></a>Pool maken in een virtueel netwerk

### <a name="add-firewall-rules"></a>Firewallregels toevoegen

Taakagentgroepen vereisen toegang tot de volgende Azure-services. De volgende firewallregels moeten worden toegevoegd aan bestaande netwerkbeveiligingsgroepen of door de gebruiker gedefinieerde routes.

| Richting | Protocol | Bron         | Bronpoort | Doel          | Dest-poort | Gebruikt    |
|-----------|----------|----------------|-------------|----------------------|-----------|---------|
| Uitgaand  | TCP      | VirtualNetwork | Alle         | AzureKeyVault        | 443       | Standaard |
| Uitgaand  | TCP      | VirtualNetwork | Alle         | Storage              | 443       | Standaard |
| Uitgaand  | TCP      | VirtualNetwork | Alle         | EventHub             | 443       | Standaard |
| Uitgaand  | TCP      | VirtualNetwork | Alle         | AzureActiveDirectory | 443       | Standaard |
| Uitgaand  | TCP      | VirtualNetwork | Alle         | AzureMonitor         | 443       | Standaard |

> [!NOTE]
> Als voor uw taken aanvullende resources van het openbare internet zijn vereist, voegt u de bijbehorende regels toe. Er zijn bijvoorbeeld aanvullende regels nodig om een docker-build-taak uit te voeren die de basisafbeeldingen uit Docker Hub haalt of een NuGet-pakket herstelt.

### <a name="create-pool-in-vnet"></a>Pool maken in VNet

In het volgende voorbeeld wordt een agentpool gemaakt in *het subnet mysubnet* van het netwerk *myvnet*:

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

## <a name="run-task-on-agent-pool"></a>Taak uitvoeren in agentpool

De volgende voorbeelden laten zien hoe u een agentpool opgeeft bij het in de wachtrij staan van een taak.

> [!NOTE]
> Als u een agentpool wilt gebruiken in een ACR-taak, moet u ervoor zorgen dat de pool ten minste één exemplaar bevat.
>

### <a name="quick-task"></a>Snelle taak

Maak een snelle taak in de wachtrij voor de agentpool met behulp van [de opdracht az acr build][az-acr-build] en geef de parameter `--agent-pool` door:

```azurecli
az acr build \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --file Dockerfile \
    https://github.com/Azure-Samples/acr-build-helloworld-node.git#main
```

### <a name="automatically-triggered-task"></a>Automatisch geactiveerde taak

Maak bijvoorbeeld een geplande taak in de agentpool met [az acr task create][az-acr-task-create]en door de parameter door te `--agent-pool` geven.

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

Voer az [acr task run][az-acr-task-run]uit om de taakinstallatie te controleren:

```azurecli
az acr task run \
    --name mytask
```

### <a name="query-pool-status"></a>Status van querygroep

Voer [az acr agentpool show][az-acr-agentpool-show]uit om het aantal runs te vinden dat momenteel is gepland in de agentpool.

```azurecli
az acr agentpool show \
    --name myagentpool \
    --queue-count
```

## <a name="next-steps"></a>Volgende stappen

Bekijk de reeks zelfstudies voor meer voorbeelden van builds van container-ACR-taken en onderhoud [in de cloud.](container-registry-tutorial-quick-task.md)



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
