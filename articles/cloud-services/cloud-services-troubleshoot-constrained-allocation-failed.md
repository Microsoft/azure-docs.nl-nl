---
title: Problemen met ConstrainedAllocationFailed oplossen bij het implementeren van een Cloud service in azure | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een ConstrainedAllocationFailed-uitzonde ring oplost bij het implementeren van een Cloud service in Azure.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.openlocfilehash: de344bbcd89158676bacf2a8aa1743d282700b9d
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100521072"
---
# <a name="troubleshoot-constrainedallocationfailed-when-deploying-a-cloud-service-to-azure"></a>Problemen met ConstrainedAllocationFailed oplossen bij het implementeren van een Cloud service in azure

In dit artikel lost u de toewijzings fouten op waarbij Azure Cloud Services niet kan worden geïmplementeerd vanwege beperkingen.

Microsoft Azure wijst de volgende handelingen uit:

- Cloud Services-instanties bijwerken

- Nieuwe web-of werk rollen instanties toevoegen

- Instanties implementeren in een Cloud service

U kunt af en toe fouten ontvangen tijdens deze bewerkingen, zelfs voordat u de limiet voor Azure-abonnementen bereikt.

> [!TIP]
> De informatie kan ook nuttig zijn bij het plannen van de implementatie van uw services.

## <a name="symptom"></a>Symptoom

Ga in Azure Portal naar uw Cloud service en selecteer in de zijbalk *bewerkings Logboeken (klassiek)* om de logboeken weer te geven.

Wanneer u de logboeken van uw Cloud service inspecteert, ziet u de volgende uitzonde ring:

|Uitzonderings type  |Foutbericht  |
|---------|---------|
|ConstrainedAllocationFailed     |De Azure-bewerking `{Operation ID}` is mislukt met de code compute. ConstrainedAllocationFailed. Details: toewijzing is mislukt; kan niet voldoen aan de beperkingen in de aanvraag. De aangevraagde nieuwe service-implementatie is gebonden aan een affiniteitsgroep, is gericht op een virtueel netwerk of er is een bestaande implementatie onder deze gehoste service. Met elk van deze voor waarden wordt de nieuwe implementatie beperkt tot specifieke Azure-resources. Probeer het later opnieuw of verklein de VM-grootte of het aantal rolinstanties. Indien mogelijk kunt u ook de hierboven genoemde beperkingen opheffen of naar een andere regio implementeren.|

## <a name="cause"></a>Oorzaak

Er is een probleem met de capaciteit van de regio of het cluster dat u implementeert. Deze gebeurtenis treedt op wanneer de resource-SKU die u hebt geselecteerd, niet beschikbaar is voor de opgegeven locatie.

> [!NOTE]
> Wanneer het eerste knoop punt van een Cloud service is geïmplementeerd, wordt het *vastgemaakt* aan een resource groep. Een resource groep kan één cluster of een groep clusters zijn.
>
> Na verloop van tijd kunnen de resources in deze resource groep volledig worden gebruikt. Als een Cloud service een toewijzings aanvraag voor aanvullende resources maakt wanneer er onvoldoende resources beschikbaar zijn in de vastgemaakte resource groep, leidt de aanvraag tot een [toewijzings fout](cloud-services-allocation-failures.md).

## <a name="solution"></a>Oplossing

In dit scenario moet u een andere regio of SKU selecteren om uw Cloud service te implementeren in. Voordat u uw Cloud service implementeert of bijwerkt, kunt u bepalen welke Sku's beschikbaar zijn in een regio of beschikbaarheids zone. Volg de onderstaande [Azure cli](#list-skus-in-region-using-azure-cli)-, [Power shell](#list-skus-in-region-using-powershell)-of [rest API](#list-skus-in-region-using-rest-api) -processen.

### <a name="list-skus-in-region-using-azure-cli"></a>Sku's in regio weer geven met behulp van Azure CLI

U kunt de opdracht [AZ VM List-sku's](https://docs.microsoft.com/cli/azure/vm.html#az_vm_list_skus) gebruiken.

- Gebruik de `--location` para meter voor het filteren van uitvoer naar de locatie die u gebruikt.
- Gebruik de `--size` para meter om te zoeken op een gedeeltelijke grootte naam.
- Zie de gids [fout oplossen voor niet-beschik bare SKU](../azure-resource-manager/templates/error-sku-not-available.md#solution-2---azure-cli) voor meer informatie.

    **Bijvoorbeeld:**

    ```azurecli
    az vm list-skus --location southcentralus --size Standard_F --output table
    ```

    **Voorbeeld resultaten:** ![ Azure CLI-uitvoer van het uitvoeren van de opdracht AZ VM List-sku's--location southcentralus--size Standard_F--Output Table, waarin de beschik bare Sku's worden weer gegeven.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-constrained-allocation-failed-1.png)

#### <a name="list-skus-in-region-using-powershell"></a>Sku's in regio weer geven met behulp van Power shell

U kunt de opdracht [Get-AzComputeResourceSku](https://docs.microsoft.com/powershell/module/az.compute/get-azcomputeresourcesku) gebruiken.

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

U kunt de bewerking [resource-sku's-List](https://docs.microsoft.com/rest/api/compute/resourceskus/list) gebruiken. Het retourneert beschik bare Sku's en regio's in de volgende indeling:

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
    <Rest_of_your_file_is_located_here...>
  ]
}
    
```

## <a name="next-steps"></a>Volgende stappen

Voor meer oplossingen voor toewijzings fouten en om beter te begrijpen hoe ze worden gegenereerd:

> [!div class="nextstepaction"]
> [Toewijzings fouten (Cloud Services)](cloud-services-allocation-failures.md)

Als uw Azure-probleem niet in dit artikel wordt behandeld, gaat u naar de Azure-forums op [MSDN en stack overflow](https://azure.microsoft.com/support/forums/). U kunt uw probleem in deze forums plaatsen of een bericht sturen naar [@AzureSupport op Twitter](https://twitter.com/AzureSupport). U kunt ook een Azure-ondersteuningsaanvraag indienen. Als u een ondersteuningsaanvraag wilt indienen, selecteert u op de pagina [Azure-ondersteuning](https://azure.microsoft.com/support/options/) *Ondersteuning krijgen*.
