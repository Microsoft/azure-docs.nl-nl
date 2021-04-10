---
title: Aangepaste beleids regels voor schalen gebruiken met virtuele-machine schaal sets van Azure
description: Meer informatie over het gebruik van aangepaste schaal-in-beleids regels met virtuele-machine schaal sets van Azure die gebruikmaken van configuratie voor automatisch schalen om het aantal exemplaren te beheren
services: virtual-machine-scale-sets
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: scale-in-policy
ms.date: 02/26/2020
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: 10ed223311995e746beca24f7a09d795f7b3f0e6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105935700"
---
# <a name="use-custom-scale-in-policies-with-azure-virtual-machine-scale-sets"></a>Aangepaste beleids regels voor schalen gebruiken met virtuele-machine schaal sets van Azure

Een implementatie van een schaalset voor virtuele machines kan worden uitgebreid of geschaald op basis van een matrix met metrische gegevens, waaronder platform en door de gebruiker gedefinieerde aangepaste metrische gegevens. Terwijl bij een uitschalen nieuwe virtuele machines worden gemaakt op basis van het model van de schaalset, is een schaal van invloed op actieve virtuele machines met verschillende configuraties en/of functies als de werk belasting van de schaalset wordt gegroeid. 

De functie voor het inschalen van beleid biedt gebruikers een manier om de volg orde te configureren waarin de virtuele machines worden geschaald, door middel van drie configuraties voor inschalen: 

1. Standaard
2. NewestVM
3. OldestVM

### <a name="default-scale-in-policy"></a>Standaard beleid voor inschalen

Virtuele-machine schaal sets past dit beleid standaard toe om te bepalen welke exemplaren worden geschaald. Met het *standaard* beleid zijn vm's geselecteerd voor schalen in de volgende volg orde:

1. De verdeling van virtuele machines over beschikbaarheids zones (als de schaalset is geïmplementeerd in de zonegebonden-configuratie)
2. Virtuele machines verdelen over fout domeinen (beste poging)
3. Virtuele machine met de hoogste exemplaar-ID verwijderen

Gebruikers hoeven geen niveau van een inschaal beleid op te geven als ze alleen de standaard volgorde moeten volgen.

Houd er rekening mee dat bij het verdelen over beschikbaarheids zones of fout domeinen geen instanties over beschikbaarheids zones of fout domeinen worden verplaatst. De verdeling wordt bereikt door het verwijderen van virtuele machines uit de niet-sluitende beschikbaarheids zones of de fout domeinen tot de verdeling van virtuele machines evenwichtig wordt.

### <a name="newestvm-scale-in-policy"></a>NewestVM-beleid voor inschalen

Met dit beleid wordt de nieuwste gemaakte virtuele machine in de schaalset verwijderd, na het verdelen van Vm's over beschikbaarheids zones (voor zonegebonden-implementaties). Als u dit beleid inschakelt, is een configuratie wijziging vereist voor het model van de virtuele-machine schaalset.

### <a name="oldestvm-scale-in-policy"></a>OldestVM-beleid voor inschalen

Met dit beleid wordt de oudste gemaakte virtuele machine in de schaalset verwijderd, na het verdelen van Vm's over beschikbaarheids zones (voor zonegebonden-implementaties). Als u dit beleid inschakelt, is een configuratie wijziging vereist voor het model van de virtuele-machine schaalset.

## <a name="enabling-scale-in-policy"></a>Inschaal beleid inschakelen

Er wordt een beleid voor inschalen gedefinieerd in het model voor virtuele-machine schaal sets. Zoals vermeld in de bovenstaande secties, is er een beleids definitie voor de schaal in het beleid nodig bij het gebruik van het beleid ' NewestVM ' en ' OldestVM '. In de schaalset voor virtuele machines wordt automatisch het ' standaard ' scale-in-beleid gebruikt als er geen definitie van een inschaal beleid is gevonden in het model voor de schaalset. 

U kunt op de volgende manieren een scale-in-beleid definiëren in het model voor de virtuele-machine schaal:

### <a name="azure-portal"></a>Azure Portal
 
In de volgende stappen wordt het beleid voor de schaal baarheid gedefinieerd bij het maken van een nieuwe schaalset. 
 
1. Ga naar **schaal sets voor virtuele machines**.
1. Selecteer **+ toevoegen** om een nieuwe schaalset te maken.
1. Ga naar het tabblad **schalen** . 
1. Zoek de sectie **Scale-in Policy** .
1. Selecteer een niveau in de vervolg keuzelijst.
1. Wanneer u klaar bent met het maken van de nieuwe schaalset, selecteert u de knop **controleren + maken** .

### <a name="using-api"></a>API gebruiken

Voer een PUT uit op de virtuele-machine schaalset met API 2019-03-01:

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["OldestVM"]  
        } 
    }    
} 
```
### <a name="azure-powershell"></a>Azure PowerShell

Maak een resource groep en maak vervolgens een nieuwe schaalset met het beleid voor de schaalset die is ingesteld als *OldestVM*.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "<VMSS location>"
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "<VMSS location>" `
  -VMScaleSetName "myScaleSet" `
  -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

In het volgende voor beeld wordt een beleid voor schaal in toegevoegd tijdens het maken van een nieuwe schaalset. Maak eerst een resource groep en maak vervolgens een nieuwe schaalset met het beleid voor schaal-in als *OldestVM*. 

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Sjabloon gebruiken

In uw sjabloon, onder eigenschappen, voegt u het volgende toe:

```json
"scaleInPolicy": {  
      "rules": ["OldestVM"]  
}
```

De bovenstaande blokken geven aan dat de VM-schaalset de oudste virtuele machine in een schaalset met gelijke taak verdeling verwijdert wanneer een inschalen wordt geactiveerd (door automatisch schalen of hand matig te verwijderen).

Wanneer een schaalset voor een virtuele machine niet wordt verdeeld over de zone, verwijdert de schaalset eerst Vm's uit de niet-sluitende zone (s). In de niet-sluitende zones maakt de schaalset gebruik van het hierboven opgegeven niveau voor de schaal aanpassing om te bepalen welke VM moet worden geschaald. In dit geval selecteert de schaalset in een niet-sluitende zone de oudste virtuele machine in die zone die moet worden verwijderd.

Voor een niet-zonegebonden virtuele-machine schaalset selecteert het beleid de oudste VM in de schaalset voor verwijdering.

Hetzelfde proces geldt voor het gebruik van ' NewestVM ' in het bovenstaande scale-in-beleid.

## <a name="modifying-scale-in-policies"></a>Inschaal beleid wijzigen

Het aanpassen van het scale-in-beleid volgt hetzelfde proces als het Toep assen van het beleid voor inschalen. Als u bijvoorbeeld in het bovenstaande voor beeld het beleid wilt wijzigen van ' OldestVM ' in ' NewestVM ', kunt u dit als volgt doen:

### <a name="azure-portal"></a>Azure Portal

U kunt het beleid voor de schaal baarheid van een bestaande schaalset wijzigen via de Azure Portal. 
 
1. In een bestaande schaalset voor virtuele machines selecteert u **schalen** in het menu aan de linkerkant.
1. Selecteer het tabblad **Scale-in Policy** .
1. Selecteer een niveau in de vervolg keuzelijst.
1. Selecteer **Opslaan** wanneer u klaar bent. 

### <a name="using-api"></a>API gebruiken

Voer een PUT uit op de virtuele-machine schaalset met API 2019-03-01:

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01 

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["NewestVM"]  
        } 
    }    
}
```
### <a name="azure-powershell"></a>Azure PowerShell

Het niveau van de schaal van een bestaande schaalset bijwerken:

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Hier volgt een voor beeld van het bijwerken van het beleid voor de schaal baarheid van een bestaande schaalset: 

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Sjabloon gebruiken

Wijzig in uw sjabloon onder ' Eigenschappen ' de sjabloon zoals hieronder en implementeer deze opnieuw: 

```json
"scaleInPolicy": {  
      "rules": ["NewestVM"]  
} 
```

Hetzelfde proces is van toepassing als u besluit om ' NewestVM ' te wijzigen in ' default ' of ' OldestVM '

## <a name="instance-protection-and-scale-in-policy"></a>Instantie beveiliging en inschaal beleid

Virtuele-machine schaal sets bieden twee soorten [instantie beveiliging](./virtual-machine-scale-sets-instance-protection.md#types-of-instance-protection):

1. Beveiligen tegen schalen
2. Beveiligen tegen schaal acties

Een beveiligde virtuele machine wordt niet verwijderd door een inschaal actie, ongeacht het toegepaste beleid voor de schaal aanpassing. Als VM_0 (oudste VM in de schaalset) bijvoorbeeld is beveiligd tegen schalen, en als de schaalset ' OldestVM ' is ingeschakeld, wordt VM_0 niet in aanmerking genomen voor het schalen in, zelfs als dit de oudste VM in de schaalset is. 

Een beveiligde virtuele machine kan op elk gewenst moment hand matig worden verwijderd door de gebruiker, ongeacht het ingestelde beleid voor inschalen in de schaalset. 

## <a name="usage-examples"></a>Gebruiksvoorbeelden 

In de onderstaande voor beelden ziet u hoe u met een schaalset voor virtuele machines Vm's selecteert die moeten worden verwijderd wanneer een ingrijpende gebeurtenis wordt geactiveerd. Voor virtuele machines met de hoogste exemplaar-Id's wordt aangenomen dat de nieuwste Vm's in de schaalset zijn en er wordt ervan uitgegaan dat de Vm's met de kleinste exemplaar-Id's de oudste Vm's in de schaalset zijn. 

### <a name="oldestvm-scale-in-policy"></a>OldestVM-beleid voor inschalen

| Gebeurtenis                 | Exemplaar-Id's in zone 1  | Exemplaar-Id's in Zone2  | Exemplaar-Id's in Zone3  | Selectie inschalen                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Eerste               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Inschalen              | 3, 4, 5, 10            | ***2***, 6, 9, 11      | 1, 7, 8                | Kies tussen Zone 1 en 2, zelfs als Zone 3 de oudste virtuele machine heeft. Verwijder VM2 uit Zone 2, omdat dit de oudste virtuele machine in die zone is.   |
| Inschalen              | ***3***, 4, 5, 10      | 6, 9, 11               | 1, 7, 8                | Kies Zone 1 zelfs als Zone 3 de oudste virtuele machine heeft. Verwijder VM3 uit Zone 1, omdat dit de oudste virtuele machine in die zone is.                  |
| Inschalen              | 4, 5, 10               | 6, 9, 11               | ***1***, 7, 8          | Zones zijn evenwichtig. Verwijder VM1 in Zone 3, omdat dit de oudste VM in de schaalset is.                                               |
| Inschalen              | ***4***, 5, 10         | 6, 9, 11               | 7, 8                   | Kies tussen Zone 1 en Zone 2. Verwijder VM4 in Zone 1, omdat dit de oudste VM in de twee zones is.                              |
| Inschalen              | 5, 10                  | ***6***, 9, 11         | 7, 8                   | Kies Zone 2 zelfs als Zone 1 de oudste virtuele machine heeft. Verwijder VM6 in Zone 1, omdat dit de oudste virtuele machine in die zone is.                    |
| Inschalen              | ***5***, 10            | 9, 11                  | 7, 8                   | Zones zijn evenwichtig. Verwijder VM5 in Zone 1, omdat dit de oudste VM in de schaalset is.                                                |

Voor niet-zonegebonden virtuele-machine schaal sets selecteert het beleid de oudste VM in de schaalset voor verwijdering. Een ' beveiligde ' VM wordt overgeslagen voor verwijdering.

### <a name="newestvm-scale-in-policy"></a>NewestVM-beleid voor inschalen

| Gebeurtenis                 | Exemplaar-Id's in zone 1  | Exemplaar-Id's in Zone2  | Exemplaar-Id's in Zone3  | Selectie inschalen                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Eerste               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Inschalen              | 3, 4, 5, 10            | 2, 6, 9, ***11***      | 1, 7, 8                | Kies tussen Zone 1 en 2. Verwijder VM11 uit Zone 2, omdat het de nieuwste VM in de twee zones is.                                |
| Inschalen              | 3, 4, 5, ***10***      | 2, 6, 9                | 1, 7, 8                | Kies Zone 1 omdat het meer Vm's heeft dan de andere twee zones. Verwijder VM10 uit Zone 1, omdat dit de nieuwste virtuele machine in die zone is.          |
| Inschalen              | 3, 4, 5                | 2, 6, ***9***          | 1, 7, 8                | Zones zijn evenwichtig. Verwijder VM9 in Zone 2, omdat dit de nieuwste VM in de schaalset is.                                                |
| Inschalen              | 3, 4, 5                | 2, 6                   | 1, 7, ***8***          | Kies tussen Zone 1 en Zone 3. Verwijder VM8 in Zone 3, omdat dit de nieuwste virtuele machine in die zone is.                                      |
| Inschalen              | 3, 4, ***5***          | 2, 6                   | 1, 7                   | Kies Zone 1 zelfs als Zone 3 de nieuwste virtuele machine heeft. Verwijder VM5 in Zone 1, omdat dit de nieuwste virtuele machine in die zone is.                    |
| Inschalen              | 3, 4                   | 2, 6                   | 1, ***7***             | Zones zijn evenwichtig. Verwijder VM7 in Zone 3, omdat dit de nieuwste VM in de schaalset is.                                                |

Voor niet-zonegebonden virtuele-machine schaal sets selecteert het beleid de nieuwste VM in de schaalset voor verwijdering. Een ' beveiligde ' VM wordt overgeslagen voor verwijdering. 

## <a name="troubleshoot"></a>Problemen oplossen

1. Kan scaleInPolicy niet inschakelen als er een fout bericht ' onjuiste aanvraag ' wordt weer gegeven met de melding ' kan geen lid ' scaleInPolicy ' vinden voor een object van het type ' Eigenschappen ' ' en controleer vervolgens de API-versie die wordt gebruikt voor de schaalset van de virtuele machine. API-versie 2019-03-01 of hoger is vereist voor deze functie.

2. Verkeerde selectie van Vm's voor schalen: Raadpleeg de bovenstaande voor beelden. Als uw schaalset voor virtuele machines een zonegebonden-implementatie is, wordt het scale-in-beleid eerst toegepast op de niet-sluitende zones en vervolgens op de schaalset zodra de zone is verdeeld. Als de volg orde van de schaal niet consistent is met de bovenstaande voor beelden, verhoogt u een query met het team van de virtuele-machine schaalset voor het oplossen van problemen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [implementeren van uw toepassing](virtual-machine-scale-sets-deploy-app.md) op virtuele-machine schaal sets.
