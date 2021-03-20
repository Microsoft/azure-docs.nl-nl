---
title: Geef een resource groep voor Vm's op in Azure DevTest Labs | Microsoft Docs
description: Meer informatie over het opgeven van een resource groep voor Vm's in een lab in Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 7b72048405d3025ca21b324b6ad3168dd0c9ac95
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85483360"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Geef een resource groep op voor Lab-virtuele machines in Azure DevTest Labs

Als eigenaar van een lab kunt u de virtuele lab-machines configureren die moeten worden gemaakt in een specifieke resource groep. Deze functie helpt u bij de volgende scenario's:

- U hebt minder resource groepen gemaakt door Labs in uw abonnement.
- Laat uw Labs binnen een vaste set met resource groepen die u configureert.
- Tijdelijke beperkingen en goed keuringen die zijn vereist voor het maken van resource groepen binnen uw Azure-abonnement.
- Consolideer al uw Lab-resources in één resource groep om het bijhouden van die resources en het Toep assen van [beleid](../governance/policy/overview.md) voor het beheren van resources op het niveau van de resource groep te vereenvoudigen.

Met deze functie kunt u een script gebruiken om een nieuwe of bestaande resource groep in uw Azure-abonnement op te geven voor al uw Lab-Vm's. Azure DevTest Labs ondersteunt deze functie momenteel via een API.

> [!NOTE]
> Alle abonnements limieten zijn van toepassing wanneer u Labs maakt in DevTest Labs. Beschouw een Lab als andere resources in uw abonnement. In het geval van resource groepen is de limiet [980 resource groepen per abonnement](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits). 

## <a name="use-azure-portal"></a>Azure Portal gebruiken
Volg deze stappen om een resource groep op te geven voor alle Vm's die in het lab zijn gemaakt. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **alle services** in het navigatie menu links. 
3. Selecteer **DevTest Labs** uit de lijst.
4. Selecteer in de lijst met Labs uw **Lab**.  
5. Selecteer **configuratie en beleid** in de sectie **instellingen** in het menu links. 
6. Selecteer **Lab-instellingen** in het menu links. 
7. Selecteer **alle virtuele machines in één resource groep**. 
8. Selecteer een bestaande resource groep in de vervolg keuzelijst (of) Selecteer **nieuwe maken**, voer een **naam** in voor de resource groep en selecteer **OK**. 

    ![Selecteer de resource groep voor alle Lab-Vm's](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>PowerShell gebruiken 
In het volgende voor beeld ziet u hoe u een Power shell-script gebruikt voor het maken van alle virtuele lab-machines in een nieuwe resource groep.

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

Roep het script aan met behulp van de volgende opdracht. ResourceGroup.ps1 is het bestand dat het voor gaande script bevat:

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Een Azure Resource Manager sjabloon gebruiken
Als u een Azure Resource Manager sjabloon gebruikt om een lab te maken, gebruikt u de eigenschap **vmCreationResourceGroupId** in het gedeelte Lab-eigenschappen van uw sjabloon, zoals wordt weer gegeven in het volgende voor beeld:

```json
        {
            "type": "microsoft.devtestlab/labs",
            "name": "[parameters('lab_name')]",
            "apiVersion": "2018_10_15_preview",
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


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>API voor het configureren van een resource groep voor Lab-Vm's
U hebt de volgende opties als eigenaar van het lab wanneer u deze API gebruikt:

- Kies de **resource groep van het lab** voor alle virtuele machines.
- Kies een **bestaande resource groep** , anders dan de resource groep van het lab voor alle virtuele machines.
- Voer een nieuwe naam in voor de **resource groep** voor alle virtuele machines.
- Ga verder met het bestaande gedrag, waarbij een resource groep voor elke virtuele machine in het lab wordt gemaakt.
 
Deze instelling is van toepassing op nieuwe virtuele machines die zijn gemaakt in het lab. De oudere virtuele machines in uw Lab die in hun eigen resource groepen zijn gemaakt, blijven ongewijzigd. Omgevingen die in uw Lab worden gemaakt, blijven behouden in hun eigen resource groepen.

Deze API gebruiken:
- Gebruik API-versie **2018_10_15_preview**.
- Als u een nieuwe resource groep opgeeft, moet u ervoor zorgen dat u **schrijf machtigingen hebt voor resource groepen** in uw abonnement. Als u geen schrijf machtigingen hebt, zullen het maken van nieuwe virtuele machines in de opgegeven resource groep mislukken.
- Geef tijdens het gebruik van de API de **volledige resource groep-ID** door. Bijvoorbeeld: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Zorg ervoor dat de resource groep zich in hetzelfde abonnement bevindt als het lab. 


## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen: 

- [Beleids regels instellen voor een Lab](devtest-lab-set-lab-policy.md)
- [Veelgestelde vragen](devtest-lab-faq.md)
