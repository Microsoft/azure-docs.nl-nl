---
title: Problemen met LocationNotFoundForRoleSize oplossen bij het implementeren van een Cloud service (klassiek) in azure | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een LocationNotFoundForRoleSize-uitzonde ring oplost bij het implementeren van een Cloud service (klassiek) naar Azure.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 2ed889bea715ff5a26bf8e918789429e57fa31b2
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109659"
---
# <a name="troubleshoot-locationnotfoundforrolesize-when-deploying-a-cloud-service-classic-to-azure"></a>Problemen met LocationNotFoundForRoleSize oplossen bij het implementeren van een Cloud service (klassiek) naar Azure

In dit artikel lost u de toewijzings fouten op waarbij de grootte van een virtuele machine (VM) niet beschikbaar is wanneer u een Azure-Cloud service (klassiek) implementeert.

Bij het implementeren van exemplaren in een Cloud service (klassiek) of het toevoegen van nieuwe web-of worker-rolinstanties, Microsoft Azure het toewijzen van reken resources.

U kunt af en toe fouten ontvangen tijdens deze bewerkingen, zelfs voordat u de limiet voor Azure-abonnementen bereikt.

> [!TIP]
> De informatie kan ook nuttig zijn bij het plannen van de implementatie van uw services.

## <a name="symptom"></a>Symptoom

Ga in Azure Portal naar uw Cloud service (klassiek) en selecteer in de zijbalk het *bewerkings logboek (klassiek)* om de logboeken weer te geven.

![Afbeelding toont de Blade bewerkings logboek (klassiek).](./media/cloud-services-troubleshoot-location-not-found-for-role-size/cloud-services-troubleshoot-allocation-logs.png)

Wanneer u de logboeken van uw Cloud service (klassiek) inspecteert, ziet u de volgende uitzonde ring:

|Uitzonderings type  |Foutbericht  |
|---------|---------|
|LocationNotFoundForRoleSize     |De bewerking `{Operation ID}` is mislukt: de aangevraagde VM-laag is momenteel niet beschikbaar in de regio ( `{Region ID}` ) voor dit abonnement. Probeer een andere laag of implementeer deze op een andere locatie.|

## <a name="cause"></a>Oorzaak

Er is een probleem met de capaciteit van de regio of het cluster dat u implementeert. De uitzonde ring *LocationNotFoundForRoleSize* treedt op wanneer de resource-SKU die u hebt geselecteerd (VM-grootte) niet beschikbaar is voor de opgegeven regio.

## <a name="solution"></a>Oplossing

In dit scenario moet u een andere regio of SKU selecteren om uw Cloud service (klassiek) te implementeren naar. Voordat u uw Cloud service (klassiek) implementeert of bijwerkt, kunt u bepalen welke Sku's beschikbaar zijn in een regio of beschikbaarheids zone. Volg de onderstaande [Azure cli](#list-skus-in-region-using-azure-cli)-, [Power shell](#list-skus-in-region-using-powershell)-of [rest API](#list-skus-in-region-using-rest-api) -processen.

### <a name="list-skus-in-region-using-azure-cli"></a>Sku's in regio weer geven met behulp van Azure CLI

U kunt de [AZ VM List-sku's] (/CLI/Azure/VM? View = Azure-cli-jongste) gebruiken
#<a name="az_vm_list_skus-command"></a>az_vm_list_skus) opdracht.

- Gebruik de `--location` para meter voor het filteren van uitvoer naar de locatie die u gebruikt.
- Gebruik de `--size` para meter om te zoeken op een gedeeltelijke grootte naam.
- Zie de gids [fout oplossen voor niet-beschik bare SKU](../azure-resource-manager/templates/error-sku-not-available.md#solution-2---azure-cli) voor meer informatie.

    **Bijvoorbeeld:**

    ```azurecli
    az vm list-skus --location southcentralus --size Standard_F --output table
    ```

    **Voorbeeld resultaten:** ![ Azure CLI-uitvoer van het uitvoeren van de opdracht AZ VM List-sku's--location southcentralus--size Standard_F--Output Table, waarin de beschik bare Sku's worden weer gegeven.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-constrained-allocation-failed-1.png)

#### <a name="list-skus-in-region-using-powershell"></a>Sku's in regio weer geven met behulp van Power shell

U kunt de opdracht [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) gebruiken.

- De resultaten filteren op locatie.
- U moet de meest recente versie van Power shell voor deze opdracht hebben.
- Zie de gids [fout oplossen voor niet-beschik bare SKU](../azure-resource-manager/templates/error-sku-not-available.md#solution-1---powershell) voor meer informatie.

**Bijvoorbeeld:**

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

**Enkele andere handige opdrachten:**

De locaties die de grootte bevatten (Standard_DS14_v2) uitfilteren:

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
```

Alle locaties met een grootte filteren (v3):

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

#### <a name="list-skus-in-region-using-rest-api"></a>Sku's in regio weer geven met REST API

U kunt de bewerking [resource-sku's-List](/rest/api/compute/resourceskus/list) gebruiken. Het retourneert beschik bare Sku's en regio's in de volgende indeling:

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
      <<The rest of your file is located here>>
  ]
}
    
```

## <a name="next-steps"></a>Volgende stappen

Voor meer oplossingen voor toewijzings fouten en om beter te begrijpen hoe ze worden gegenereerd:

> [!div class="nextstepaction"]
> [Toewijzings fouten-Cloud service (klassiek)](cloud-services-allocation-failures.md)

Als uw Azure-probleem niet in dit artikel wordt behandeld, gaat u naar de Azure-forums op [MSDN en stack overflow](https://azure.microsoft.com/support/forums/). U kunt uw probleem in deze forums plaatsen of een bericht sturen naar [@AzureSupport op Twitter](https://twitter.com/AzureSupport). U kunt ook een Azure-ondersteuningsaanvraag indienen. Als u een ondersteuningsaanvraag wilt indienen, selecteert u op de pagina [Azure-ondersteuning](https://azure.microsoft.com/support/options/) *Ondersteuning krijgen*.
