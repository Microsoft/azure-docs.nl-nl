---
title: Fouten met SKU niet beschikbaar
description: Beschrijft hoe u problemen met de fout SKU niet beschikbaar kunt oplossen bij het implementeren van resources met Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 04/14/2021
ms.openlocfilehash: 3baedf6a5c9f2dbfd3ddf666b458fac649fce2ac
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503895"
---
# <a name="resolve-errors-for-sku-not-available"></a>Fouten voor een niet-beschikbare SKU oplossen

In dit artikel wordt beschreven hoe u de **fout SkuNotAvailable kunt** oplossen. Als u geen geschikte SKU kunt vinden in die regio/zone of een alternatieve regio/zone die voldoet aan de behoeften van uw bedrijf, dient u een [SKU-aanvraag](/troubleshoot/azure/general/region-access-request-process) in bij Ondersteuning voor Azure.

## <a name="symptom"></a>Symptoom

Wanneer u een resource implementeert (meestal een virtuele machine), ontvangt u de volgende foutcode en het volgende foutbericht:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>'
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Oorzaak

U ontvangt deze foutmelding wanneer de resource-SKU die u hebt geselecteerd (zoals VM-grootte) niet beschikbaar is voor de locatie die u hebt geselecteerd.

Als u een Azure Spot-VM of een instantie van een spot-schaalset implementeert, is er geen capaciteit voor Azure Spot op deze locatie. Zie Spot-foutberichten [voor meer informatie.](../../virtual-machines/error-codes-spot.md)

## <a name="solution-1---powershell"></a>Oplossing 1 - PowerShell

Gebruik de opdracht [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) om te bepalen welke SKU's beschikbaar zijn in een regio/zone. Filter de resultaten op locatie. U moet de nieuwste versie van PowerShell hebben voor deze opdracht.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

De resultaten bevatten een lijst met SKU's voor de locatie en eventuele beperkingen voor die SKU. U ziet dat een SKU kan worden vermeld als `NotAvailableForSubscription` .

```output
ResourceType          Name           Locations   Zone      Restriction                      Capability           Value
------------          ----           ---------   ----      -----------                      ----------           -----
virtualMachines       Standard_A0    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2    centralus             NotAvailableForSubscription      MaxResourceVolumeMB  138240
virtualMachines       Standard_D1_v2 centralus   {2, 1, 3}                                  MaxResourceVolumeMB
```

Als u wilt filteren op locatie en SKU, gebruikt u:

```azurepowershell-interactive
$SubId = (Get-AzContext).Subscription.Id

$Region = "centralus" # change region here
$VMSku = "Standard_M" # change VM SKU here

$VMSKUs = Get-AzComputeResourceSku | where {$_.Locations.Contains($Region) -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains($VMSku)}

$OutTable = @()

foreach ($SkuName in $VMSKUs.Name)
        {
            $LocRestriction = if ((($VMSKUs | where Name -EQ $SkuName).Restrictions.Type | Out-String).Contains("Location")){"NotAvavalableInRegion"}else{"Available - No region restrictions applied" }
            $ZoneRestriction = if ((($VMSKUs | where Name -EQ $SkuName).Restrictions.Type | Out-String).Contains("Zone")){"NotAvavalableInZone: "+(((($VMSKUs | where Name -EQ $SkuName).Restrictions.RestrictionInfo.Zones)| Where-Object {$_}) -join ",")}else{"Available - No zone restrictions applied"}
            
            
            $OutTable += New-Object PSObject -Property @{
                                                         "Name" = $SkuName
                                                         "Location" = $Region
                                                         "Applies to SubscriptionID" = $SubId
                                                         "Subscription Restriction" = $LocRestriction
                                                         "Zone Restriction" = $ZoneRestriction
                                                         }
         }

$OutTable | select Name, Location, "Applies to SubscriptionID", "Region Restriction", "Zone Restriction" | Sort-Object -Property Name | FT
```

De opdracht retourneert resultaten zoals:

```output
Name                   Location  Applies to SubscriptionID            Region Restriction                         Zone Restriction                        
----                   --------  -------------------------            ------------------------                   ----------------     
Standard_M128          centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128-32ms     centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128-64ms     centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128dms_v2    centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx NotAvavalableInRegion                      NotAvavalableInZone: 1,2,3
```

## <a name="solution-2---azure-cli"></a>Oplossing 2 - Azure CLI

Gebruik de opdracht [az vm list-skus](/cli/azure/vm#az_vm_list_skus) om te bepalen welke SKU's beschikbaar zijn in een regio. Gebruik de `--location` parameter om uitvoer te filteren op locatie. Gebruik de `--size` parameter om te zoeken op een gedeeltelijke groottenaam. Gebruik de `--all` parameter om alle informatie weer te geven, inclusief grootten die niet beschikbaar zijn voor het huidige abonnement.

U moet Azure CLI versie 2.15.0 of hoger hebben. Gebruik om uw versie te `az --version` controleren. Werk indien nodig [de installatie bij.](/cli/azure/update-azure-cli)

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --all --output table
```

De opdracht retourneert resultaten zoals:

```output
ResourceType     Locations       Name              Zones    Restrictions
---------------  --------------  ----------------  -------  --------------
virtualMachines  southcentralus  Standard_F1       1,2,3    None
virtualMachines  southcentralus  Standard_F2       1,2,3    None
virtualMachines  southcentralus  Standard_F4       1,2,3    None
...
virtualMachines  southcentralus  Standard_F72s_v2  1,2,3    NotAvailableForSubscription, type: Zone, locations: southcentralus, zones: 1,2,3
...
```

## <a name="solution-3---azure-portal"></a>Oplossing 3 - Azure Portal

Gebruik de portal om te bepalen welke SKU's beschikbaar zijn in een [regio.](https://portal.azure.com) Meld u aan bij de portal en voeg een resource toe via de interface. Wanneer u de waarden in stelt, ziet u de beschikbare SKU's voor die resource. U hoeft de implementatie niet te voltooien.

Start bijvoorbeeld het proces voor het maken van een virtuele machine. Als u een andere beschikbare grootte wilt zien, selecteert **u Grootte wijzigen.**

![VM maken](./media/error-sku-not-available/create-vm.png)

U kunt filteren en door de beschikbare grootten bladeren.

![Beschikbare SKU's](./media/error-sku-not-available/available-sizes.png)

## <a name="solution-4---rest"></a>Oplossing 4 - REST

Gebruik de bewerking [Resource Skus - List](/rest/api/compute/resourceskus/list) om te bepalen welke SKU's beschikbaar zijn in een regio.

Deze retourneert beschikbare SKU's en regio's in de volgende indeling:

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