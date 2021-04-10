---
title: Automatische herstel van instanties met virtuele-machine schaal sets van Azure
description: Meer informatie over het configureren van het beleid voor automatische herstel bewerkingen voor VM-exemplaren in een schaalset
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: instance-protection
ms.date: 02/28/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: b43502e771415c0ca62d821c697516fae16e35ce
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934526"
---
# <a name="automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Automatische exemplaarreparaties voor virtuele-machineschaalsets van Azure

Het inschakelen van automatische herstel van instanties voor virtuele-machine schaal sets van Azure zorgt voor hoge Beschik baarheid voor toepassingen door een set gezonde instanties te behouden. Als een exemplaar in de schaalset een slechte status heeft, zoals gerapporteerd door de status [uitbreiding](./virtual-machine-scale-sets-health-extension.md) van de toepassing of de Health-tests van de [Load Balancer](../load-balancer/load-balancer-custom-probe-overview.md), voert deze functie automatisch een exemplaar van het herstel uit door het beschadigde exemplaar te verwijderen en een nieuwe te maken om dit te vervangen.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Vereisten voor het gebruik van automatische exemplaar reparaties

**Status controle van de toepassing inschakelen voor de schaalset**

De schaalset moet controle van de toepassings status hebben voor instanties ingeschakeld. U kunt dit doen met behulp van de status-of [Load Balancer](../load-balancer/load-balancer-custom-probe-overview.md)van de [toepassing](./virtual-machine-scale-sets-health-extension.md) . Slechts één van deze kan tegelijkertijd worden ingeschakeld. De uitbrei ding voor de toepassings status of de load balancer test Ping het toepassings eindpunt dat is geconfigureerd op de exemplaren van de virtuele machine om de status van de toepassings status te bepalen. Deze status wordt gebruikt door de orchestrator van de schaalset om de status van het exemplaar te controleren en herstel bewerkingen uit te voeren wanneer dat nodig is.

**Eind punt configureren om de status op te geven**

Voordat u automatisch beleid voor instanties herstellen inschakelt, moet u ervoor zorgen dat het toepassings eindpunt van de schaalset is geconfigureerd voor het verzenden van de status van de toepassings status. Wanneer een exemplaar de status 200 (OK) voor dit toepassings eindpunt retourneert, wordt het exemplaar als ' in orde ' gemarkeerd. In alle andere gevallen is het exemplaar gemarkeerd als ' beschadigd ', met inbegrip van de volgende scenario's:

- Wanneer er geen toepassings eindpunt is geconfigureerd in de exemplaren van de virtuele machine om de status van de toepassing te bieden
- Wanneer het eind punt van de toepassing onjuist is geconfigureerd
- Wanneer het eind punt van de toepassing niet bereikbaar is

Voor instanties die zijn gemarkeerd als ' beschadigd ', worden automatisch reparaties geactiveerd door de schaalset. Zorg ervoor dat het eind punt van de toepassing correct is geconfigureerd voordat u het automatische reparatie beleid inschakelt om onbedoelde exemplaar reparaties te voor komen, terwijl het eind punt wordt geconfigureerd.

**Maximum aantal exemplaren in de schaalset**

Deze functie is momenteel alleen beschikbaar voor schaal sets met Maxi maal 200 exemplaren. De schaalset kan worden geïmplementeerd als één plaatsings groep of een groep met meerdere locaties. het aantal exemplaren kan echter niet meer dan 200 zijn als automatische instantie reparaties is ingeschakeld voor de schaalset.

**API-versie**

Het beleid voor automatisch herstellen wordt ondersteund voor Compute API versie 2018-10-01 of hoger.

**Beperkingen voor het verplaatsen van resources of abonnementen**

Het verplaatsen van resources of abonnementen wordt momenteel niet ondersteund voor schaal sets wanneer de functie voor automatisch herstellen is ingeschakeld.

**Beperking voor service Fabric-schaal sets**

Deze functie wordt momenteel niet ondersteund voor service Fabric-schaal sets.

## <a name="how-do-automatic-instance-repairs-work"></a>Hoe werkt automatische exemplaar reparaties?

De functie voor automatische exemplaar herstel is afhankelijk van de status controle van afzonderlijke exemplaren in een schaalset. VM-exemplaren in een schaalset kunnen worden geconfigureerd voor het verzenden van de status van de toepassing met behulp van de status uitbreiding van de [toepassing](./virtual-machine-scale-sets-health-extension.md) of de Health-tests van de [Load Balancer](../load-balancer/load-balancer-custom-probe-overview.md). Als een exemplaar niet in orde is, wordt de herstel actie door de schaalset uitgevoerd door de beschadigde instantie te verwijderen en een nieuwe te maken om deze te vervangen. Het meest recente model voor virtuele-machine schaal sets wordt gebruikt om het nieuwe exemplaar te maken. Deze functie kan worden ingeschakeld in het model voor virtuele-machine schaal sets met behulp van het *automaticRepairsPolicy* -object.

### <a name="batching"></a>Batchverwerking

De automatische herstel bewerkingen voor instanties worden uitgevoerd in batches. Op elk gewenst moment worden niet meer dan 5% van de instanties in de schaalset hersteld via het beleid voor automatische reparaties. Dit helpt gelijktijdige verwijdering te voor komen en een groot aantal exemplaren opnieuw te maken als er een slechte status is gevonden.

### <a name="grace-period"></a>Respijtperiode

Wanneer een exemplaar een status wijzigings bewerking doorloopt vanwege een PUT-, PATCH-of POST-actie die is uitgevoerd op de schaalset (bijvoorbeeld het opnieuw instellen van de installatie kopie, opnieuw implementeren, bijwerken, enz.), wordt een herstel actie voor dat exemplaar pas uitgevoerd nadat de respijt periode is verstreken. Respijt periode is de hoeveelheid tijd die het exemplaar kan retour neren naar de status in orde. De respijt periode begint nadat de status wijziging is voltooid. Dit helpt voor komen dat voor tijdig of onbedoeld herstel bewerkingen worden uitgevoerd. De respijt periode wordt gerespecteerd voor een nieuw gemaakt exemplaar in de schaalset (inclusief de waarde die is gemaakt als gevolg van een herstel bewerking). De respijt periode is opgegeven in minuten in de ISO 8601-indeling en kan worden ingesteld met behulp van de eigenschap *automaticRepairsPolicy. gracePeriod*. De respijt periode kan tussen 30 minuten en 90 minuten liggen en heeft een standaard waarde van 30 minuten.

### <a name="suspension-of-repairs"></a>Schorsing van reparaties 

Virtuele-machine schaal sets bieden de mogelijkheid om de automatische herstel bewerkingen voor instanties tijdelijk te onderbreken, indien nodig. De *service State* voor automatische reparaties onder de eigenschap *orchestrationServices* in de instantie weergave van de virtuele-machine schaalset toont de huidige status van de automatische reparaties. Wanneer een schaalset wordt ingeschakeld voor automatische reparaties, wordt de waarde van para meter *service State* ingesteld op *actief*. Wanneer de automatische reparaties voor een schaalset worden onderbroken, wordt de para meter *service State* ingesteld op *opgeschort*. Als *automaticRepairsPolicy* is gedefinieerd voor een schaalset, maar de functie automatisch herstellen niet is ingeschakeld, wordt de para meter *service State* ingesteld op *niet actief*.

Als nieuw gemaakte instanties voor het vervangen van de beschadigde fouten in een schaalset blijven intact blijven, zelfs nadat u herhaaldelijk herstel bewerkingen hebt uitgevoerd, wordt de *service State* door het platform bijgewerkt voor automatische herstel naar *onderbroken*. U kunt de automatische herstel bewerking opnieuw *uitvoeren* door de waarde van *service State* in te stellen op automatisch herstellen. Gedetailleerde instructies vindt u in de sectie over [het weer geven en bijwerken van de service status van het beleid voor automatisch herstellen](#viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy) voor uw schaalset. 

Het proces voor automatische exemplaar reparaties werkt als volgt:

1. Met de status uitbreiding van de [toepassings status](./virtual-machine-scale-sets-health-extension.md) of [Load Balancer](../load-balancer/load-balancer-custom-probe-overview.md) wordt het toepassings eindpunt gepingd in elke virtuele machine in de schaalset om de status van de toepassings status voor elk exemplaar op te halen.
2. Als het eind punt reageert met de status 200 (OK), wordt het exemplaar als ' in orde ' gemarkeerd. In alle andere gevallen (waaronder als het eind punt onbereikbaar is), is het exemplaar gemarkeerd als ' beschadigd '.
3. Wanneer een exemplaar beschadigd is, wordt door de schaalset een herstel actie geactiveerd door het beschadigde exemplaar te verwijderen en een nieuwe te maken om dit te vervangen.
4. Reparaties van instanties worden uitgevoerd in batches. Op een gegeven moment wordt er niet meer dan 5% van het totale aantal exemplaren in de schaalset hersteld. Als een schaalset minder dan 20 exemplaren heeft, worden de herstel bewerkingen uitgevoerd voor één slechte instantie tegelijk.
5. Het bovenstaande proces wordt voortgezet totdat alle beschadigde instanties in de schaalset zijn gerepareerd.

## <a name="instance-protection-and-automatic-repairs"></a>Instantie beveiliging en automatische reparaties

Als een exemplaar in een schaalset wordt beveiligd door een van de [beveiligings beleidsregels](./virtual-machine-scale-sets-instance-protection.md)toe te passen, worden er geen automatische herstel bewerkingen uitgevoerd voor dat exemplaar. Dit geldt voor het beveiligings beleid: *beveiligen tegen schalen* en *beveiligen tegen schaal* acties. 

## <a name="terminatenotificationandautomaticrepairs"></a>Melding en automatische reparaties beëindigen

Als de functie voor het [beëindigen van meldingen](./virtual-machine-scale-sets-terminate-notification.md) is ingeschakeld voor een schaalset, wordt tijdens het automatisch herstellen het verwijderen van een beschadigd exemplaar gevolgd door de configuratie voor het beëindigen van meldingen. Er wordt een Terminate-melding verzonden via de Azure meta data-service – geplande gebeurtenissen, en het verwijderen van instanties wordt uitgesteld voor de duur van de geconfigureerde time-out voor vertraging. Het maken van een nieuw exemplaar om het onjuiste te vervangen, wacht echter niet totdat de time-out voor de vertraging is voltooid.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Automatisch herstel beleid inschakelen bij het maken van een nieuwe schaalset

Voor het inschakelen van automatisch reparatie beleid tijdens het maken van een nieuwe schaalset, moet u ervoor zorgen dat aan alle [vereisten](#requirements-for-using-automatic-instance-repairs) voor het aanmelden bij deze functie wordt voldaan. Het eind punt van de toepassing moet correct worden geconfigureerd voor instanties van een schaalset om te voor komen dat onbedoelde reparaties worden geactiveerd terwijl het eind punt wordt geconfigureerd. Voor nieuwe schaal sets worden de reparaties van instanties pas uitgevoerd nadat de duur van de respijt periode is verstreken. Als u het automatisch herstellen van exemplaren in een schaalset wilt inschakelen, gebruikt u *automaticRepairsPolicy* -object in het model voor virtuele-machine schaal sets.

U kunt deze Quick Start- [sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-automatic-repairs-slb-health-probe) ook gebruiken om een schaalset voor virtuele machines te implementeren met Load Balancer Health probe en automatische exemplaar reparaties ingeschakeld met een respijt periode van 30 minuten.

### <a name="azure-portal"></a>Azure Portal
 
De volgende stappen voor het inschakelen van automatisch herstel beleid bij het maken van een nieuwe schaalset.
 
1. Ga naar **schaal sets voor virtuele machines**.
1. Selecteer **+ toevoegen** om een nieuwe schaalset te maken.
1. Ga naar het tabblad **status** . 
1. Zoek de sectie **status** .
1. Schakel de optie **toepassings status bewaken** in.
1. Zoek de sectie **beleid voor automatisch herstellen** .
1. Schakel de optie **automatisch herstellen** **in** .
1. In **respijt periode (min)**, geeft u de respijt periode op in minuten, toegestane waarden tussen 30 en 90 minuten. 
1. Wanneer u klaar bent met het maken van de nieuwe schaalset, selecteert u de knop **controleren + maken** .

### <a name="rest-api"></a>REST-API

In het volgende voor beeld ziet u hoe u het automatisch herstellen van exemplaren in een schaalset-model inschakelt. Gebruik API-versie 2018-10-01 of hoger.

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

De functie voor het automatisch herstellen van instanties kan worden ingeschakeld tijdens het maken van een nieuwe schaalset met behulp van de cmdlet [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) . In dit voorbeeld script wordt uitgelegd hoe u een schaalset en gekoppelde resources maakt met behulp van het configuratie bestand: [een volledige schaalset voor virtuele machines maken](./scripts/powershell-sample-create-complete-scale-set.md). U kunt automatisch beleid voor het herstellen van instanties configureren door de para meters *EnableAutomaticRepair* en *AutomaticRepairGracePeriod* toe te voegen aan het configuratie object voor het maken van de schaalset. In het volgende voor beeld wordt de functie ingeschakeld met een respijt periode van 30 minuten.

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

In het volgende voor beeld wordt het beleid voor automatisch herstellen ingeschakeld tijdens het maken van een nieuwe schaalset met *[AZ vmss Create](/cli/azure/vmss#az-vmss-create)*. Maak eerst een resource groep en maak vervolgens een nieuwe schaalset met de respijt periode voor automatisch herstel beleid ingesteld op 30 minuten.

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

In het bovenstaande voor beeld wordt een bestaande load balancer en status test gebruikt voor het bewaken van de toepassings status van instanties. Als u liever een toepassings status extensie wilt gebruiken voor bewaking, kunt u een schaalset maken, de uitbrei ding van de toepassings status configureren en vervolgens het beleid voor automatische exemplaar reparatie inschakelen met de *Update AZ vmss*, zoals wordt uitgelegd in de volgende sectie.

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Automatisch herstel beleid inschakelen bij het bijwerken van een bestaande schaalset

Voordat u automatisch herstel beleid inschakelt in een bestaande schaalset, moet u ervoor zorgen dat aan alle [vereisten](#requirements-for-using-automatic-instance-repairs) voor het aanmelden bij deze functie is voldaan. Het eind punt van de toepassing moet correct worden geconfigureerd voor instanties van een schaalset om te voor komen dat onbedoelde reparaties worden geactiveerd terwijl het eind punt wordt geconfigureerd. Als u het automatisch herstellen van exemplaren in een schaalset wilt inschakelen, gebruikt u *automaticRepairsPolicy* -object in het model voor virtuele-machine schaal sets.

Nadat u het model van een bestaande schaalset hebt bijgewerkt, moet u ervoor zorgen dat het meest recente model wordt toegepast op alle exemplaren van de schaal. Raadpleeg de instructies voor het [up-to-date zetten van vm's met het nieuwste model voor schaal sets](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

### <a name="azure-portal"></a>Azure Portal

U kunt het automatische reparatie beleid van een bestaande schaalset wijzigen via de Azure Portal. 
 
1. Ga naar een bestaande schaalset voor virtuele machines.
1. Onder **instellingen** in het menu aan de linkerkant selecteert u **status en herstellen**.
1. Schakel de optie **toepassings status bewaken** in.
1. Zoek de sectie **beleid voor automatisch herstellen** .
1. Schakel de optie **automatisch herstellen** **in** .
1. In **respijt periode (min)**, geeft u de respijt periode op in minuten, toegestane waarden tussen 30 en 90 minuten. 
1. Selecteer **Opslaan** wanneer u klaar bent. 

### <a name="rest-api"></a>REST-API

In het volgende voor beeld wordt het beleid met een respijt periode van 40 minuten ingeschakeld. Gebruik API-versie 2018-10-01 of hoger.

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

Gebruik de cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) om de configuratie van de functie voor het automatisch herstellen van exemplaren in een bestaande schaalset te wijzigen. In het volgende voor beeld wordt de respijt periode bijgewerkt naar 40 minuten.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Hier volgt een voor beeld van het bijwerken van het beleid voor automatische exemplaar reparaties van een bestaande schaalset met *[AZ vmss update](/cli/azure/vmss#az-vmss-update)*.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-automatic-repairs true \
  --automatic-repairs-grace-period 30
```

## <a name="viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy"></a>De service status van het beleid voor automatische exemplaar reparaties weer geven en bijwerken

### <a name="rest-api"></a>REST-API 

Gebruik de [weer gave exemplaar ophalen](/rest/api/compute/virtualmachinescalesets/getinstanceview) met API-versie 2019-12-01 of hoger voor de schaalset van de virtuele machine om de *service State* voor automatische reparaties te bekijken onder de eigenschap *orchestrationServices*. 

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

Gebruik de *setOrchestrationServiceState* -API met API-versie 2019-12-01 of hoger op een schaalset voor virtuele machines om de status van automatische reparaties in te stellen. Als de schaalset wordt gekozen voor de functie automatisch herstellen, kunt u deze API gebruiken om automatisch herstellen voor uw schaalset uit te stellen of te hervatten. 

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

Gebruik de cmdlet [Get-instance-View](/cli/azure/vmss#az-vmss-get-instance-view) om de *service State* voor automatische exemplaar reparaties weer te geven. 

```azurecli-interactive
az vmss get-instance-view \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```

Gebruik de cmdlet [set-Orchestration-service-State](/cli/azure/vmss#az-vmss-set-orchestration-service-state) om de *service State* bij te werken voor automatische herstel van instanties. Als de schaalset wordt gekozen voor de functie voor automatisch herstellen, kunt u deze cmdlet gebruiken om automatisch herstellen voor schaal sets te onderbreken of hervatten. 

```azurecli-interactive
az vmss set-orchestration-service-state \
    --service-name AutomaticRepairs \
    --action Resume \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```
### <a name="azure-powershell"></a>Azure PowerShell

Gebruik de cmdlet [Get-AzVmss](/powershell/module/az.compute/get-azvmss) met para meter *InstanceView* om de *service State* voor automatische exemplaar reparaties weer te geven.

```azurepowershell-interactive
Get-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -InstanceView
```

Gebruik Set-AzVmssOrchestrationServiceState cmdlet om de *service State* voor automatische exemplaar reparaties bij te werken. Als de schaalset wordt gekozen voor de functie voor automatisch herstellen, kunt u deze cmdlet gebruiken om automatisch herstellen voor schaal sets te onderbreken of hervatten.

```azurepowershell-interactive
Set-AzVmssOrchestrationServiceState `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -ServiceName "AutomaticRepairs" `
    -Action "Suspend"
```

## <a name="troubleshoot"></a>Problemen oplossen

**Het inschakelen van het beleid voor automatisch herstellen is mislukt**

Als er een fout bericht ' onjuiste aanvraag ' wordt weer gegeven met de tekst ' kan geen automaticRepairsPolicy vinden voor het object van het type ' Eigenschappen ' ', controleert u de API-versie die wordt gebruikt voor de schaalset van de virtuele machine. API-versie 2018-10-01 of hoger is vereist voor deze functie.

**Exemplaar niet gerepareerd, zelfs wanneer beleid is ingeschakeld**

Het exemplaar kan zich in de respijt periode bevindt. Dit is de tijd die moet worden gewacht na een status wijziging in het exemplaar voordat de reparaties worden uitgevoerd. Dit is om te voor komen dat voor tijdig of onopzettelijke reparaties worden hersteld. De reparatie actie moet plaatsvinden zodra de respijt periode voor het exemplaar is voltooid.

**De status van de toepassing weer geven voor instanties van schaal sets**

U kunt de [weer gave-API voor instantie ophalen](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) voor instanties in een schaalset voor virtuele machines gebruiken om de status van de toepassings status weer te geven. Met Azure PowerShell kunt u de cmdlet [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) gebruiken met de vlag *-InstanceView* . De status van de toepassing wordt gegeven in de eigenschap *vmHealth*.

In de Azure Portal ziet u ook de integriteits status. Ga naar een bestaande schaalset, selecteer **instanties** in het menu aan de linkerkant en Bekijk de kolom **status** voor de status van elk exemplaar van de schaalset. 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het configureren van de status extensie van de [toepassing](./virtual-machine-scale-sets-health-extension.md) of de [Load Balancer](../load-balancer/load-balancer-custom-probe-overview.md) voor uw schaal sets.
