---
title: Automatische exemplaarreparaties met virtuele-machineschaalsets van Azure
description: Meer informatie over het configureren van beleid voor automatische reparaties voor VM-exemplaren in een schaalset
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: instance-protection
ms.date: 02/28/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: 733f4602e43511924783f6bc8cb1bad29edb5ea0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762907"
---
# <a name="automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Automatische exemplaarreparaties voor virtuele-machineschaalsets van Azure

Het inschakelen van automatische exemplaarreparaties voor virtuele-machineschaalsets van Azure zorgt voor hoge beschikbaarheid voor toepassingen door een set gezonde exemplaren te onderhouden. Als blijkt dat een exemplaar in de schaalset [](./virtual-machine-scale-sets-health-extension.md) een slechte status heeft, zoals gerapporteerd door de toepassingstoestandsextensie of [load balancer-statustests,](../load-balancer/load-balancer-custom-probe-overview.md)voert deze functie automatisch een exemplaarherstel uit door het exemplaar met slechte status te verwijderen en een nieuwe instantie te maken ter vervanging ervan.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Vereisten voor het gebruik van automatische exemplaarreparaties

**Statuscontrole van toepassingen inschakelen voor schaalset**

Voor de schaalset moet toepassings health monitoring zijn ingeschakeld voor instanties. Dit kan worden gedaan met behulp [van de extensie Toepassingstoestand](./virtual-machine-scale-sets-health-extension.md) of [Statustests van load balancer.](../load-balancer/load-balancer-custom-probe-overview.md) Slechts één van deze kan tegelijk worden ingeschakeld. De toepassingsstatusextensie of de load balancer test pingen het toepassings-eindpunt dat is geconfigureerd op exemplaren van virtuele machines om de status van de toepassing te bepalen. Deze status wordt gebruikt door de orchestrator van de schaalset om de status van exemplaren te bewaken en zo nodig reparaties uit te voeren.

**Eindpunt configureren om de status op te geven**

Voordat u beleid voor automatische exemplaarreparaties inschakelen, moet u ervoor zorgen dat voor de exemplaren van de schaalset een toepassings-eindpunt is geconfigureerd om de status van de toepassing te kunnen gebruiken. Wanneer een exemplaar de status 200 (OK) retourneert op dit toepassings-eindpunt, wordt het exemplaar gemarkeerd als 'In orde'. In alle andere gevallen wordt het exemplaar gemarkeerd als 'Niet in orde', met inbegrip van de volgende scenario's:

- Wanneer er geen toepassings-eindpunt is geconfigureerd in de exemplaren van de virtuele machine om de status van de toepassing op te geven
- Wanneer het eindpunt van de toepassing onjuist is geconfigureerd
- Wanneer het eindpunt van de toepassing niet bereikbaar is

Voor exemplaren die zijn gemarkeerd als 'Niet in orde', worden automatische reparaties geactiveerd door de schaalset. Zorg ervoor dat het eindpunt van de toepassing correct is geconfigureerd voordat u het beleid voor automatische reparaties instelt om onbedoelde exemplaarreparaties te voorkomen, terwijl het eindpunt wordt geconfigureerd.

**Maximum aantal exemplaren in de schaalset**

Deze functie is momenteel alleen beschikbaar voor schaalsets met maximaal 200 exemplaren. De schaalset kan worden geïmplementeerd als één plaatsingsgroep of als een groep met meerdere plaatsingen, maar het aantal exemplaren mag niet hoger zijn dan 200 als automatische exemplaarreparaties is ingeschakeld voor de schaalset.

**API-versie**

Beleid voor automatische reparaties wordt ondersteund voor compute-API-versie 2018-10-01 of hoger.

**Beperkingen voor het wijzigen van resources of abonnementen**

Het wijzigen van resources of abonnementen wordt momenteel niet ondersteund voor schaalsets wanneer de functie voor automatische reparaties is ingeschakeld.

**Beperking voor Service Fabric-schaalsets**

Deze functie wordt momenteel niet ondersteund voor Service Fabric-schaalsets.

## <a name="how-do-automatic-instance-repairs-work"></a>Hoe werken automatische exemplaarreparaties?

De functie voor het automatisch herstellen van exemplaren is afhankelijk van de statuscontrole van afzonderlijke exemplaren in een schaalset. VM-exemplaren in een schaalset kunnen worden geconfigureerd om [](./virtual-machine-scale-sets-health-extension.md) de status van de toepassing te sturen met behulp van de toepassingsstatusextensie of [load balancer-statustests.](../load-balancer/load-balancer-custom-probe-overview.md) Als een exemplaar niet in orde blijkt te zijn, voert de schaalset een herstelactie uit door het exemplaar met slechte status te verwijderen en een nieuwe te maken ter vervanging ervan. Het meest recente virtuele-machineschaalsetmodel wordt gebruikt om het nieuwe exemplaar te maken. Deze functie kan worden ingeschakeld in het virtuele-machineschaalsetmodel met behulp van het object *automaticRepairsPolicy.*

### <a name="batching"></a>Batchverwerking

De automatische exemplaarherstelbewerkingen worden uitgevoerd in batches. Op een bepaald moment wordt niet meer dan 5% van de exemplaren in de schaalset hersteld via het beleid voor automatische reparaties. Dit voorkomt gelijktijdige verwijdering en het opnieuw maken van een groot aantal exemplaren als deze op hetzelfde moment niet in orde zijn.

### <a name="grace-period"></a>Respijtperiode

Wanneer een exemplaar een statuswijziging ondergaat vanwege een PUT-, PATCH- of POST-actie die is uitgevoerd op de schaalset (bijvoorbeeld opnieuw instellen, opnieuw uitvoeren, bijwerken, enzovoort), wordt een herstelactie op dat exemplaar alleen uitgevoerd nadat er op de respijtperiode is gewacht. Respijtperiode is de hoeveelheid tijd die nodig is om het exemplaar terug te laten keren naar een goede staat. De respijtperiode begint nadat de statuswijziging is voltooid. Dit voorkomt voortijdige of onbedoelde reparatiebewerkingen. De respijtperiode wordt gehonoreerd voor elk nieuw gemaakt exemplaar in de schaalset (inclusief de periode die is gemaakt als gevolg van een herstelbewerking). Respijtperiode wordt opgegeven in minuten in ISO 8601-indeling en kan worden ingesteld met behulp van de eigenschap *automaticRepairsPolicy.gracePeriod.* Respijtperiode kan tussen 30 minuten en 90 minuten variëren en heeft een standaardwaarde van 30 minuten.

### <a name="suspension-of-repairs"></a>Opschorten van reparaties 

Virtuele-machineschaalsets bieden de mogelijkheid om automatische exemplaarreparaties tijdelijk op te schorten, indien nodig. De *serviceState voor* automatische reparaties onder de eigenschap *orchestrationServices* in exemplaarweergave van virtuele-machineschaalset toont de huidige status van de automatische reparaties. Wanneer een schaalset wordt gekozen voor automatische reparaties, wordt de waarde van parameter *serviceState* ingesteld op *Wordt uitgevoerd.* Wanneer de automatische reparaties voor een schaalset worden opgeschort, wordt de parameter *serviceState* ingesteld op *Opgeschort.* Als *automaticRepairsPolicy* is gedefinieerd op een schaalset, maar de functie voor automatische reparaties niet is ingeschakeld, wordt de parameter *serviceState* ingesteld op *Niet actief.*

Als nieuwe exemplaren voor het vervangen van de slechte exemplaren in een schaalset ook na herhaaldelijk uitvoeren van herstelbewerkingen niet in orde blijven, werkt het platform als veiligheidsmaatregel de *serviceState* bij voor automatische reparaties naar *Opgeschort.* U kunt de automatische reparaties opnieuw hervatten door de waarde van *serviceState* voor automatische reparaties in te stellen op *Wordt uitgevoerd.* Gedetailleerde instructies worden gegeven in de sectie over het weergeven en bijwerken van de [servicetoestand](#viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy) van automatische reparaties beleid voor uw schaalset. 

Het proces voor automatische exemplaarreparaties werkt als volgt:

1. [Toepassingsstatusextensie](./virtual-machine-scale-sets-health-extension.md) of [statustests](../load-balancer/load-balancer-custom-probe-overview.md) van load balancer pingen het toepassings-eindpunt binnen elke virtuele machine in de schaalset om de status van de toepassing voor elk exemplaar op te halen.
2. Als het eindpunt reageert met de status 200 (OK), wordt het exemplaar gemarkeerd als 'In orde'. In alle andere gevallen (inclusief als het eindpunt onbereikbaar is), wordt het exemplaar gemarkeerd als 'Niet in orde'.
3. Wanneer een exemplaar niet in orde blijkt te zijn, activeert de schaalset een herstelactie door het exemplaar dat niet in orde is te verwijderen en een nieuwe te maken ter vervanging ervan.
4. Exemplaarreparaties worden uitgevoerd in batches. Op een bepaald moment wordt niet meer dan 5% van het totale aantal exemplaren in de schaalset hersteld. Als een schaalset minder dan 20 exemplaren heeft, worden de reparaties uitgevoerd voor één exemplaar met een slechte status per keer.
5. Het bovenstaande proces gaat door totdat alle exemplaren met een slechte status in de schaalset zijn hersteld.

## <a name="instance-protection-and-automatic-repairs"></a>Exemplaarbeveiliging en automatische reparaties

Als een exemplaar in een schaalset wordt beveiligd door een van de beveiligingsbeleidsregels toe te [passen,](./virtual-machine-scale-sets-instance-protection.md)worden automatische reparaties niet uitgevoerd op dat exemplaar. Dit geldt voor zowel het beveiligingsbeleid: *Beveiligen tegen inschalen als* *Beveiligen tegen schaalsetacties.* 

## <a name="terminatenotificationandautomaticrepairs"></a>Melding beëindigen en automatische reparaties

Als de [functie melding beëindigen](./virtual-machine-scale-sets-terminate-notification.md) is ingeschakeld voor een schaalset, volgt de verwijdering van een beschadigd exemplaar tijdens een automatische herstelbewerking de configuratie van de melding beëindigen. Er wordt een melding over beëindigen verzonden via Azure Metadata Service , geplande gebeurtenissen, en het verwijderen van exemplaren wordt vertraagd voor de duur van de geconfigureerde time-out voor vertraging. Het maken van een nieuw exemplaar ter vervanging van de niet in orde is, wacht echter niet tot de time-out voor de vertraging is voltooid.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Automatisch herstelbeleid inschakelen bij het maken van een nieuwe schaalset

Voor het inschakelen van beleid voor automatische reparaties [](#requirements-for-using-automatic-instance-repairs) tijdens het maken van een nieuwe schaalset, moet u ervoor zorgen dat aan alle vereisten voor het kiezen voor deze functie wordt voldaan. Het eindpunt van de toepassing moet correct worden geconfigureerd voor schaalset-exemplaren om te voorkomen dat onbedoelde reparaties worden uitgevoerd terwijl het eindpunt wordt geconfigureerd. Voor nieuw gemaakte schaalsets worden exemplaarreparaties alleen uitgevoerd nadat ze hebben gewacht op de duur van de respijtperiode. Als u het automatisch herstellen van exemplaren in een schaalset wilt inschakelen, gebruikt u het object *automaticRepairsPolicy* in het model van de virtuele-machineschaalset.

U kunt deze [quickstart-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-automatic-repairs-slb-health-probe) ook gebruiken om een virtuele-machineschaalset te implementeren met load balancer statustest en automatische exemplaarreparaties ingeschakeld met een respijtperiode van 30 minuten.

### <a name="azure-portal"></a>Azure Portal
 
De volgende stappen voor het inschakelen van beleid voor automatische reparaties bij het maken van een nieuwe schaalset.
 
1. Ga naar **Virtuele-machineschaalsets.**
1. Selecteer **+ Toevoegen om** een nieuwe schaalset te maken.
1. Ga naar het **tabblad** Status. 
1. Ga naar **de sectie** Status.
1. Schakel de **optie Toepassingstoestand bewaken** in.
1. Ga naar **de sectie Beleid voor automatische reparatie.**
1. Schakel **de optie** Automatische **reparaties** in.
1. Geef **in Respijtperiode (min.)** de respijtperiode in minuten op. Toegestane waarden liggen tussen 30 en 90 minuten. 
1. Wanneer u klaar bent met het maken van de nieuwe schaalset, selecteert u **de knop Beoordelen en** maken.

### <a name="rest-api"></a>REST-API

In het volgende voorbeeld ziet u hoe u automatisch exemplaarherstel kunt inschakelen in een schaalsetmodel. Gebruik API-versie 2018-10-01 of hoger.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT30M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

De functie voor het automatisch herstellen van exemplaren kan worden ingeschakeld tijdens het maken van een nieuwe schaalset met behulp van de cmdlet [New-AzVmssConfig.](/powershell/module/az.compute/new-azvmssconfig) Dit voorbeeldscript beloopt het maken van een schaalset en de bijbehorende resources met behulp van het [configuratiebestand: Een volledige virtuele-machineschaalset maken.](./scripts/powershell-sample-create-complete-scale-set.md) U kunt beleid voor automatische exemplaarreparaties configureren door de parameters *EnableAutomaticRepair* en *AutomaticRepairGracePeriod* toe te voegen aan het configuratieobject voor het maken van de schaalset. In het volgende voorbeeld wordt de functie met een respijtperiode van 30 minuten in gebruik.

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

In het volgende voorbeeld wordt het beleid voor automatische reparaties mogelijk gemaakt tijdens het maken van een nieuwe schaalset *[met az vmss create.](/cli/azure/vmss#az_vmss_create)* Maak eerst een resourcegroep en maak vervolgens een nieuwe schaalset met automatische respijtperiode voor het herstelbeleid ingesteld op 30 minuten.

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --load-balancer <existingLoadBalancer> \
  --health-probe <existingHealthProbeUnderLoaderBalancer> \
  --automatic-repairs-grace-period 30
```

In het bovenstaande voorbeeld wordt een bestaande load balancer en statustest gebruikt voor het bewaken van de status van toepassingen van exemplaren. Als u liever een toepassings-statusextensie voor bewaking gebruikt, kunt u een schaalset maken, de statusextensie van de toepassing configureren en vervolgens het beleid voor automatische exemplaarreparaties inschakelen met behulp van *az vmss update,* zoals wordt uitgelegd in de volgende sectie.

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Automatisch herstelbeleid inschakelen bij het bijwerken van een bestaande schaalset

Voordat u beleid voor automatische reparaties in een [](#requirements-for-using-automatic-instance-repairs) bestaande schaalset inschakelen, moet u ervoor zorgen dat aan alle vereisten voor het kiezen voor deze functie wordt voldaan. Het eindpunt van de toepassing moet correct zijn geconfigureerd voor instanties van schaalsets om te voorkomen dat onbedoelde reparaties worden uitgevoerd terwijl het eindpunt wordt geconfigureerd. Als u het automatisch herstellen van exemplaren in een schaalset wilt inschakelen, gebruikt u het object *automaticRepairsPolicy* in het virtuele-machineschaalsetmodel.

Nadat u het model van een bestaande schaalset heeft bijgewerkt, moet u ervoor zorgen dat het meest recente model wordt toegepast op alle exemplaren van de schaal. Raadpleeg de instructies voor het [up-to-date](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)brengen van VM's met het meest recente schaalsetmodel.

### <a name="azure-portal"></a>Azure Portal

U kunt het beleid voor automatische reparaties van een bestaande schaalset wijzigen via de Azure Portal. 
 
1. Ga naar een bestaande virtuele-machineschaalset.
1. Selecteer **onder Instellingen** in het menu aan de linkerkant de optie Status en **herstel.**
1. Schakel de **optie Toepassingstoestand bewaken** in.
1. Ga naar **de sectie Beleid voor automatische reparatie.**
1. Schakel **de optie** Automatische reparaties **in.**
1. Geef **in Respijtperiode (min.)** de respijtperiode in minuten op. Toegestane waarden liggen tussen 30 en 90 minuten. 
1. Selecteer **Opslaan** wanneer u klaar bent. 

### <a name="rest-api"></a>REST-API

In het volgende voorbeeld wordt het beleid met een respijtperiode van 40 minuten mogelijk. Gebruik API-versie 2018-10-01 of hoger.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT40M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

Gebruik de [cmdlet Update-AzVmss](/powershell/module/az.compute/update-azvmss) om de configuratie van de functie voor automatisch exemplaarherstel in een bestaande schaalset te wijzigen. In het volgende voorbeeld wordt de respijtperiode bijgewerkt naar 40 minuten.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Hier volgt een voorbeeld voor het bijwerken van het beleid voor automatische exemplaarreparaties van een bestaande schaalset met *[az vmss update.](/cli/azure/vmss#az_vmss_update)*

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-automatic-repairs true \
  --automatic-repairs-grace-period 30
```

## <a name="viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy"></a>De servicetoestand van het beleid voor automatische exemplaarreparaties weergeven en bijwerken

### <a name="rest-api"></a>REST-API 

Gebruik [Get Instance View](/rest/api/compute/virtualmachinescalesets/getinstanceview) met API-versie 2019-12-01 of hoger voor de virtuele-machineschaalset om de *serviceState* voor automatische reparaties te bekijken onder de eigenschap *orchestrationServices*. 

```http
GET '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version=2019-12-01'
```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Running"
    }
  ]
}
```

Gebruik *setOrchestrationServiceState* API met API-versie 2019-12-01 of hoger op een virtuele-machineschaalset om de status van automatische reparaties in te stellen. Zodra de schaalset is gekozen voor de functie voor automatische reparaties, kunt u deze API gebruiken om automatische reparaties voor uw schaalset op te schorten of te hervatten. 

 ```http
 POST '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/setOrchestrationServiceState?api-version=2019-12-01'
 ```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Suspend"
    }
  ]
}
```

### <a name="azure-cli"></a>Azure CLI 

Gebruik [de cmdlet get-instance-view](/cli/azure/vmss#az_vmss_get_instance_view) om de *serviceState te bekijken voor* automatische exemplaarreparaties. 

```azurecli-interactive
az vmss get-instance-view \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```

Gebruik [de cmdlet set-orchestration-service-state](/cli/azure/vmss#az_vmss_set_orchestration_service_state) om *de serviceState* bij te werken voor automatische exemplaarreparaties. Zodra de schaalset is gekozen voor de functie voor automatisch herstellen, kunt u deze cmdlet gebruiken om automatische reparaties voor uw schaalset op te schorten of te hervatten. 

```azurecli-interactive
az vmss set-orchestration-service-state \
    --service-name AutomaticRepairs \
    --action Resume \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```
### <a name="azure-powershell"></a>Azure PowerShell

Gebruik [de cmdlet Get-AzVmss](/powershell/module/az.compute/get-azvmss) met de parameter *InstanceView* om de *ServiceState* weer te geven voor automatische exemplaarreparaties.

```azurepowershell-interactive
Get-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -InstanceView
```

Gebruik Set-AzVmssOrchestrationServiceState cmdlet om de *serviceState bij te werken* voor automatische exemplaarreparaties. Zodra de schaalset is gekozen voor de functie voor automatisch herstellen, kunt u deze cmdlet gebruiken om automatische reparaties voor uw schaalset op te schorten of te hervatten.

```azurepowershell-interactive
Set-AzVmssOrchestrationServiceState `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -ServiceName "AutomaticRepairs" `
    -Action "Suspend"
```

## <a name="troubleshoot"></a>Problemen oplossen

**Het inschakelen van beleid voor automatische reparaties is mislukt**

Als u een 'BadRequest'-fout krijgt met het bericht 'Kan het lid 'automaticRepairsPolicy' niet vinden op object van het type 'properties', controleert u de API-versie die wordt gebruikt voor de virtuele-machineschaalset. API-versie 2018-10-01 of hoger is vereist voor deze functie.

**Exemplaar wordt niet hersteld, zelfs niet wanneer beleid is ingeschakeld**

Het exemplaar kan zich in de respijtperiode hebben. Dit is de hoeveelheid tijd die moet worden gewacht na een statuswijziging op het exemplaar voordat er reparaties worden uitgevoerd. Dit is om voortijdige of onbedoelde reparaties te voorkomen. De herstelactie moet plaatsvinden zodra de respijtperiode voor het exemplaar is voltooid.

**Status van toepassing weergeven voor exemplaren van schaalsets**

U kunt de [GET Instance View API gebruiken](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) voor exemplaren in een virtuele-machineschaalset om de status van de toepassing weer te geven. Met Azure PowerShell kunt u de cmdlet [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) gebruiken met de *vlag -InstanceView.* De status van de toepassing wordt opgegeven onder de eigenschap *vmHealth.*

In de Azure Portal ziet u ook de status. Ga naar een bestaande schaalset, selecteer **Exemplaren** in het  menu aan de linkerkant en bekijk de kolom Status voor de status van elk exemplaar van de schaalset. 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [configureren van application health-extensies](./virtual-machine-scale-sets-health-extension.md) of [load balancer-statustests](../load-balancer/load-balancer-custom-probe-overview.md) voor uw schaalsets.
