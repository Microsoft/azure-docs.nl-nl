---
title: Niet-beschik bare SKU-fouten
description: Hierin wordt beschreven hoe u problemen met de SKU niet beschik bare fout bij het implementeren van resources met Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: 5b0bbd653907c109eca526af86979013b3137cfa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98737147"
---
# <a name="resolve-errors-for-sku-not-available"></a>Fouten voor een niet-beschikbare SKU oplossen

In dit artikel wordt beschreven hoe u de **SkuNotAvailable** -fout kunt oplossen. Als u geen geschikte SKU in die regio/zone of een alternatieve regio/zone kunt vinden die voldoet aan uw bedrijfs behoeften, moet u een [SKU-aanvraag](/troubleshoot/azure/general/region-access-request-process) indienen bij Azure-ondersteuning.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Symptoom

Wanneer u een resource implementeert (meestal een virtuele machine), wordt de volgende fout code en fout bericht weer gegeven:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>'
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Oorzaak

Deze fout wordt weer gegeven wanneer de resource-SKU die u hebt geselecteerd (zoals VM-grootte) niet beschikbaar is voor de locatie die u hebt geselecteerd.

Als u een Azure spot VM of een instantie van een steun schaalset implementeert, is er geen capaciteit voor Azure-steun op deze locatie. Zie [Spot fout berichten](../../virtual-machines/error-codes-spot.md)voor meer informatie.

## <a name="solution-1---powershell"></a>Oplossing 1-Power shell

Gebruik de opdracht [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) om te bepalen welke sku's beschikbaar zijn in een regio/zone. De resultaten filteren op locatie. U moet de meest recente versie van Power shell voor deze opdracht hebben.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

De resultaten omvatten een lijst met Sku's voor de locatie en eventuele beperkingen voor die SKU. U ziet dat een SKU kan worden weer gegeven als `NotAvailableForSubscription` .

```output
ResourceType          Name           Locations   Zone      Restriction                      Capability           Value
------------          ----           ---------   ----      -----------                      ----------           -----
virtualMachines       Standard_A0    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2    centralus             NotAvailableForSubscription      MaxResourceVolumeMB  138240
virtualMachines       Standard_D1_v2 centralus   {2, 1, 3}                                  MaxResourceVolumeMB
```

Enkele aanvullende voor beelden:

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

Als u ' FC ' aan het einde toevoegt, worden er meer details geretourneerd.

## <a name="solution-2---azure-cli"></a>Oplossing 2-Azure CLI

Gebruik de opdracht om te bepalen welke Sku's beschikbaar zijn in een regio `az vm list-skus` . Gebruik de `--location` para meter voor het filteren van uitvoer naar de locatie die u gebruikt. Gebruik de `--size` para meter om te zoeken op een gedeeltelijke grootte naam.

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

De opdracht retourneert resultaten als:

```output
ResourceType     Locations       Name              Zones    Capabilities    Restrictions
---------------  --------------  ----------------  -------  --------------  --------------
virtualMachines  southcentralus  Standard_F1                ...             None
virtualMachines  southcentralus  Standard_F2                ...             None
virtualMachines  southcentralus  Standard_F4                ...             None
...
```

## <a name="solution-3---azure-portal"></a>Oplossing 3-Azure Portal

Gebruik de [Portal](https://portal.azure.com)om te bepalen welke sku's beschikbaar zijn in een regio. Meld u aan bij de portal en voeg een resource toe via de-interface. Wanneer u de waarden instelt, ziet u de beschik bare Sku's voor die bron. U hoeft de implementatie niet te volt ooien.

U kunt bijvoorbeeld het proces voor het maken van een virtuele machine starten. Als u andere beschik bare grootte wilt zien, selecteert u **grootte wijzigen**.

![VM maken](./media/error-sku-not-available/create-vm.png)

U kunt filteren en door de beschik bare grootten bladeren.

![Beschikbare SKU's](./media/error-sku-not-available/available-sizes.png)

## <a name="solution-4---rest"></a>Oplossing 4-REST

Als u wilt bepalen welke Sku's beschikbaar zijn in een regio, gebruikt u de bewerking [resource-sku's-List](/rest/api/compute/resourceskus/list) .

Het retourneert beschik bare Sku's en regio's in de volgende indeling:

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    ...
  ]
}
```