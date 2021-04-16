---
title: Resourcegroep opgeven voor VM's in Azure DevTest Labs | Microsoft Docs
description: Meer informatie over het opgeven van een resourcegroep voor VM's in een lab in Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: c6f576a20fc8fada9dd515e8ba2a266761a3e586
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377485"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Geef een resourcegroep op voor virtuele labmachines in Azure DevTest Labs

Als labeigenaar kunt u de virtuele machines van uw lab configureren om te worden gemaakt in een specifieke resourcegroep. Deze functie helpt u in de volgende scenario's:

- U hebt minder resourcegroepen gemaakt door labs in uw abonnement.
- Uw labs te laten werken binnen een vaste set resourcegroepen die u configureert.
- Beperkingen en goedkeuringen die zijn vereist voor het maken van resourcegroepen binnen uw Azure-abonnement, kunt u gebruiken.
- Consolideer al uw labresources binnen één [](../governance/policy/overview.md) resourcegroep om het bijhouden van deze resources te vereenvoudigen en het toepassen van beleidsregels voor het beheren van resources op het niveau van de resourcegroep.

Met deze functie kunt u een script gebruiken om een nieuwe of bestaande resourcegroep op te geven binnen uw Azure-abonnement voor al uw lab-VM's. Momenteel ondersteunt Azure DevTest Labs functie via een API.

> [!NOTE]
> Alle abonnementslimieten zijn van toepassing wanneer u labs maakt in DevTest Labs. U kunt een lab zien als elke andere resource in uw abonnement. In het geval van resourcegroepen is de limiet [980 resourcegroepen per abonnement.](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) 

## <a name="use-azure-portal"></a>Azure Portal gebruiken
Volg deze stappen om een resourcegroep op te geven voor alle VM's die in het lab zijn gemaakt. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **Alle services** in het navigatiemenu aan de linkerkant. 
3. Selecteer **DevTest Labs** uit de lijst.
4. Selecteer uw lab in de lijst met **labs.**  
5. Selecteer **Configuratie en beleid** in de sectie **Instellingen** in het menu links. 
6. Selecteer **Labinstellingen** in het menu links. 
7. Selecteer **Alle virtuele machines in één resourcegroep.** 
8. Selecteer een bestaande resourcegroep in de vervolgkeuzelijst (of)  selecteer Nieuwe **maken,** voer een naam in voor de resourcegroep en selecteer **OK.** 

    ![Selecteer de resourcegroep voor alle lab-VM's](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>PowerShell gebruiken 
In het volgende voorbeeld ziet u hoe u een PowerShell-script gebruikt om alle virtuele labmachines in een nieuwe resourcegroep te maken.

```powershell
[CmdletBinding()]
Param(
    $subId,
    $labRg,
    $labName,
    $vmRg
)

az login | out-null

az account set --subscription $subId | out-null

$rgId = "/subscriptions/"+$subId+"/resourceGroups/"+$vmRg

"Updating lab '$labName' with vm rg '$rgId'..."

az resource update -g $labRg -n $labName --resource-type "Microsoft.DevTestLab/labs" --api-version 2018-10-15-preview --set properties.vmCreationResourceGroupId=$rgId

"Done. New virtual machines will now be created in the resource group '$vmRg'."
```

Roep het script aan met behulp van de volgende opdracht. ResourceGroup.ps1 is het bestand dat het voorgaande script bevat:

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Een sjabloon Azure Resource Manager gebruiken
Als u een Azure Resource Manager-sjabloon gebruikt om een lab te maken, gebruikt u de eigenschap **vmCreationResourceGroupId** in de sectie labeigenschappen van uw sjabloon, zoals wordt weergegeven in het volgende voorbeeld:

```json
        {
            "type": "microsoft.devtestlab/labs",
            "name": "[parameters('lab_name')]",
            "apiVersion": "2018-10-15-preview",
            "location": "eastus",
            "tags": {},
            "scale": null,
            "properties": {
                "vmCreationResourceGroupId": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>",
                "labStorageType": "Premium",
                "premiumDataDisks": "Disabled",
                "provisioningState": "Succeeded",
                "uniqueIdentifier": "000000000f-0000-0000-0000-00000000000000"
            },
            "dependsOn": []
        },
```


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>API voor het configureren van een resourcegroep voor lab-VM's
U hebt de volgende opties als labeigenaar wanneer u deze API gebruikt:

- Kies de **resourcegroep van het lab** voor alle virtuele machines.
- Kies een **andere bestaande resourcegroep** dan de resourcegroep van het lab voor alle virtuele machines.
- Voer een **nieuwe resourcegroepnaam** in voor alle virtuele machines.
- Blijf het bestaande gedrag gebruiken, waarbij een resourcegroep wordt gemaakt voor elke VM in het lab.
 
Deze instelling is van toepassing op nieuwe virtuele machines die in het lab zijn gemaakt. De oudere VM's in uw lab die in hun eigen resourcegroepen zijn gemaakt, blijven ongewijzigd. Omgevingen die in uw lab worden gemaakt, blijven in hun eigen resourcegroepen.

Deze API gebruiken:
- Gebruik **API-versie 2018-10-15-preview.**
- Als u een nieuwe resourcegroep opgeeft, moet u ervoor zorgen dat u **schrijfmachtigingen** hebt voor resourcegroepen in uw abonnement. Als u geen schrijfmachtigingen hebt, mislukt het maken van nieuwe virtuele machines in de opgegeven resourcegroep.
- Geef tijdens het gebruik van de API de volledige **resourcegroep-id door.** Bijvoorbeeld: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Zorg ervoor dat de resourcegroep zich in hetzelfde abonnement als het lab heeft. 


## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen: 

- [Beleidsregels instellen voor een lab](devtest-lab-set-lab-policy.md)
- [Veelgestelde vragen](devtest-lab-faq.md)
