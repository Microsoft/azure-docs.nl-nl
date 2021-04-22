---
title: Problemen met LocationNotFoundForRoleSize oplossen bij het implementeren van een cloudservice (klassiek) in Azure | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een LocationNotFoundForRoleSize-uitzondering kunt oplossen bij het implementeren van een cloudservice (klassiek) in Azure.
services: cloud-services
author: mamccrea
ms.author: mamccrea
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 54af2387ec0ff6c8f86f96821baad17736e8d85b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877962"
---
# <a name="troubleshoot-locationnotfoundforrolesize-when-deploying-a-cloud-service-classic-to-azure"></a>Problemen met LocationNotFoundForRoleSize oplossen bij het implementeren van een cloudservice (klassiek) in Azure

In dit artikel gaat u toewijzingsfouten oplossen waarbij de grootte van een virtuele machine (VM) niet beschikbaar is wanneer u een Azure Cloud-service (klassiek) implementeert.

Wanneer u exemplaren implementeert in een cloudservice (klassiek) of nieuwe web- of werkrol-exemplaren toevoegt, Microsoft Azure rekenbronnen toegewezen.

U kunt af en toe fouten ontvangen tijdens deze bewerkingen, zelfs voordat u de limiet voor het Azure-abonnement bereikt.

> [!TIP]
> De informatie kan ook nuttig zijn bij het plannen van de implementatie van uw services.

## <a name="symptom"></a>Symptoom

Navigeer Azure Portal cloudservice (klassiek) en selecteer in de zijbalk Bewerkingslogboek *(klassiek) om* de logboeken te bekijken.

![Afbeelding van de blade Bewerkingslogboek (klassiek).](./media/cloud-services-troubleshoot-location-not-found-for-role-size/cloud-services-troubleshoot-allocation-logs.png)

Wanneer u de logboeken van uw cloudservice (klassiek) inspecteert, ziet u de volgende uitzondering:

|Uitzonderingstype  |Foutbericht  |
|---------|---------|
|LocationNotFoundForRoleSize     |De bewerking ' `{Operation ID}` ' ' is mislukt: 'De aangevraagde VM-laag is momenteel niet beschikbaar in Regio ( `{Region ID}` ) voor dit abonnement. Probeer een andere laag of implementeer deze op een andere locatie.'.|

## <a name="cause"></a>Oorzaak

Er is een capaciteitsprobleem met de regio of het cluster waar u in implementeert. De *uitzondering LocationNotFoundForRoleSize* treedt op wanneer de resource-SKU die u hebt geselecteerd (VM-grootte) niet beschikbaar is voor de opgegeven regio.

## <a name="solution"></a>Oplossing

In dit scenario moet u een andere regio of SKU selecteren waarin u uw cloudservice (klassiek) wilt implementeren. Voordat u uw cloudservice (klassiek) implementeert of upgradet, kunt u bepalen welke SKU's beschikbaar zijn in een regio of beschikbaarheidszone. Volg de [onderstaande azure](#list-skus-in-region-using-azure-cli) [CLI-, PowerShell-](#list-skus-in-region-using-powershell) [REST API-processen.](#list-skus-in-region-using-rest-api)

### <a name="list-skus-in-region-using-azure-cli"></a>SKU's in regio's in een lijst met Azure CLI

U kunt de [az vm list-skus](/cli/azure/vm?view=azure-cli-latest gebruiken
#<a name="az_vm_list_skus-command"></a>az_vm_list_skus) opdracht.

- Gebruik de `--location` parameter om de uitvoer te filteren op de locatie die u gebruikt.
- Gebruik de `--size` parameter om te zoeken op een gedeeltelijke groottenaam.
- Zie de handleiding Resolve [error for SKU not available (Fout oplossen voor SKU niet beschikbaar) voor meer](../azure-resource-manager/templates/error-sku-not-available.md#solution-2---azure-cli) informatie.

    **Bijvoorbeeld:**

    ```azurecli
    az vm list-skus --location southcentralus --size Standard_F --output table
    ```

    **Voorbeeldresultaten:** ![ Azure CLI-uitvoer van het uitvoeren van de opdracht az vm list-skus --location southcentralus --size Standard_F --output table, waarin de beschikbare SKU's worden weergegeven.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-constrained-allocation-failed-1.png)

#### <a name="list-skus-in-region-using-powershell"></a>SKU's in regio's in een lijst met SKU's maken met behulp van PowerShell

U kunt de [opdracht Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) gebruiken.

- Filter de resultaten op locatie.
- U moet de nieuwste versie van PowerShell hebben voor deze opdracht.
- Zie de handleiding Resolve [error for SKU not available (Fout oplossen voor SKU niet beschikbaar) voor meer](../azure-resource-manager/templates/error-sku-not-available.md#solution-1---powershell) informatie.

**Bijvoorbeeld:**

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

**Enkele andere nuttige opdrachten:**

Filter de locaties die de grootte (Standard_DS14_v2):

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
```

Filter alle locaties uit die de grootte (V3) bevatten:

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

#### <a name="list-skus-in-region-using-rest-api"></a>SKU's in regio's met behulp van REST API

U kunt de bewerking [Resource-SKU's - Lijst](/rest/api/compute/resourceskus/list) gebruiken. Deze retourneert beschikbare SKU's en regio's in de volgende indeling:

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

Voor meer foutoplossingen voor toewijzingen en om beter inzicht te krijgen in hoe deze worden gegenereerd:

> [!div class="nextstepaction"]
> [Toewijzingsfouten - Cloudservice (klassiek)](cloud-services-allocation-failures.md)

Als uw Azure-probleem niet wordt opgelost in dit artikel, gaat u naar de Azure-forums op [MSDN en Stack Overflow](https://azure.microsoft.com/support/forums/). U kunt uw probleem in deze forums plaatsen of een bericht sturen naar [@AzureSupport op Twitter](https://twitter.com/AzureSupport). U kunt ook een Azure-ondersteuningsaanvraag indienen. Als u een ondersteuningsaanvraag wilt indienen, selecteert u op de pagina [Azure-ondersteuning](https://azure.microsoft.com/support/options/) *Ondersteuning krijgen*.
