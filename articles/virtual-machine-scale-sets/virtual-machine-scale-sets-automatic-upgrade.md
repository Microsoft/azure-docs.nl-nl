---
title: Automatische installatie kopieën van besturings systemen met virtuele-machine schaal sets van Azure
description: Meer informatie over het automatisch upgraden van de installatie kopie van het besturings systeem op VM-exemplaren in een schaalset
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 06/26/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: ff1a29577c0778d6ef88d3523c726f7a48739cdc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98684607"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Upgrade van Azure virtual machine-schaalsets automatische installatiekopieën van besturingssystemen

Door automatische installatie kopieën van besturings systemen in te scha kelen in uw schaalset kunt u het update beheer vereenvoudigen door de besturingssysteem schijf veilig en automatisch bij te werken voor alle exemplaren in de schaalset.

Automatische upgrade van het besturings systeem heeft de volgende kenmerken:

- Na de configuratie wordt de meest recente installatie kopie van het besturings systeem, gepubliceerd door installatie kopieën van uitgevers, automatisch toegepast op de schaalset zonder tussen komst van de gebruiker.
- Voert een upgrade uit van batches van instanties wanneer een nieuwe installatie kopie wordt gepubliceerd door de uitgever.
- Kan worden geïntegreerd met de status tests van toepassingen en de [uitbrei ding van de toepassings status](virtual-machine-scale-sets-health-extension.md).
- Werkt voor alle VM-grootten en voor zowel Windows-als Linux-installatie kopieën.
- U kunt op elk gewenst moment automatische upgrades afmelden (OS-upgrades kunnen ook hand matig worden gestart).
- De besturingssysteem schijf van een virtuele machine wordt vervangen door de nieuwe besturingssysteem schijf die is gemaakt met de nieuwste versie van de installatie kopie. Geconfigureerde extensies en aangepaste gegevens scripts worden uitgevoerd, terwijl persistente gegevens schijven behouden blijven.
- [Extensie volgorde bepaling](virtual-machine-scale-sets-extension-sequencing.md) wordt ondersteund.
- Automatische upgrade van installatie kopie van besturings systeem kan worden ingeschakeld op een schaalset van elke grootte.

## <a name="how-does-automatic-os-image-upgrade-work"></a>Hoe werkt de automatische upgrade van de besturingssysteem installatie kopie?

Een upgrade werkt door de besturingssysteem schijf van een virtuele machine te vervangen door een nieuwe schijf die is gemaakt met de nieuwste versie van de installatie kopie. Geconfigureerde extensies en aangepaste gegevens scripts worden uitgevoerd op de besturingssysteem schijf, terwijl persistente gegevens schijven behouden blijven. Om de uitval tijd van de toepassing te minimaliseren, worden upgrades uitgevoerd in batches, met niet meer dan 20% van de schaalset op elk gewenst moment bijwerken. U kunt ook een Azure Load Balancer toepassings status test of een [toepassings status uitbreiding](virtual-machine-scale-sets-health-extension.md)integreren. We raden u aan een toepassings heartbeat te integreren en de upgrade voor elke batch in het upgrade proces te valideren.

Het upgrade proces werkt als volgt:
1. Voordat u met het upgrade proces begint, zorgt u ervoor dat niet meer dan 20% van de instanties in de gehele schaalset een slechte status heeft (om welke reden dan ook).
2. De upgrade Orchestrator identificeert de batch van de VM-exemplaren die moeten worden bijgewerkt, met een batch van Maxi maal 20% van het totale aantal exemplaren, afhankelijk van een minimale Batch grootte van één virtuele machine.
3. De besturingssysteem schijf van de geselecteerde serie VM-exemplaren wordt vervangen door een nieuwe besturingssysteem schijf die is gemaakt op basis van de nieuwste installatie kopie. Alle opgegeven extensies en configuraties in het model voor schaal sets worden toegepast op het bijgewerkte exemplaar.
4. Voor schaal sets met geconfigureerde status tests voor toepassingen of de uitbrei ding van de toepassings status, wacht de upgrade tot 5 minuten voordat het exemplaar in orde is, voordat u doorgaat met het bijwerken van de volgende batch. Als een exemplaar niet binnen vijf minuten na een upgrade de status ervan herstelt, wordt de vorige besturingssysteem schijf voor het exemplaar standaard teruggezet.
5. De upgrade Orchestrator houdt ook rekening met het percentage instanties dat een upgrade wordt uitgevoerd. De upgrade wordt gestopt als er meer dan 20% van de bijgewerkte exemplaren een slechte status krijgt tijdens het upgrade proces.
6. Het bovenstaande proces wordt voortgezet totdat alle exemplaren in de schaalset zijn bijgewerkt.

De versie van het besturings systeem voor het upgraden van de schaalset controleert de algehele status van de schaalset voordat elke batch wordt geüpgraded. Tijdens het bijwerken van een batch kunnen er andere gelijktijdige geplande of niet-geplande onderhouds activiteiten optreden die van invloed kunnen zijn op de status van de instanties van uw schaalset. In dergelijke gevallen, als meer dan 20% van de instanties van de schaalset een slechte status heeft, stopt de upgrade van de schaalset aan het einde van de huidige batch.

> [!NOTE]
>Bij automatische upgrade van het besturings systeem wordt de SKU van de referentie-installatie kopie niet bijgewerkt op de schaalset. Als u de SKU wilt wijzigen (zoals Ubuntu 16,04-LTS naar 18,04-LTS), moet u het [model voor de schaalset](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model) rechtstreeks bijwerken met de gewenste installatie kopie-SKU. Installatie kopie van uitgever en aanbieding kan niet worden gewijzigd voor een bestaande schaalset.  

## <a name="supported-os-images"></a>Ondersteunde installatie kopieën van besturings systemen
Er worden momenteel alleen bepaalde installatie kopieën van besturings systemen ondersteund. Aangepaste installatie kopieën [worden ondersteund](virtual-machine-scale-sets-automatic-upgrade.md#automatic-os-image-upgrade-for-custom-images) als de schaalset aangepaste installatie kopieën gebruikt via de [Galerie met gedeelde afbeeldingen](../virtual-machines/shared-image-galleries.md).

De volgende platform-Sku's worden momenteel ondersteund (en worden regel matig toegevoegd):

| Publisher               | OS-aanbieding      |  Sku               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18.04-LTS          |
| OpenLogic               | CentOS        | 7,5                |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2016-Data Center    |
| MicrosoftWindowsServer  | WindowsServer | 2016-Data Center-Smalldisk |
| MicrosoftWindowsServer  | WindowsServer | 2016-Data Center-met-containers |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2019-Data Center-Smalldisk |
| MicrosoftWindowsServer  | WindowsServer | 2019-Data Center-met-containers |
| MicrosoftWindowsServer  | WindowsServer | Data Center-core-1903-with-containers-smalldisk |


## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Vereisten voor het configureren van automatische upgrade van de installatie van een besturings systeem

- De eigenschap *Version* van de installatie kopie moet worden ingesteld op *meest recente*.
- Gebruik de status tests van de toepassing of de [uitbrei ding van de toepassings status](virtual-machine-scale-sets-health-extension.md) voor niet-service Fabric schaal sets.
- Gebruik Compute API versie 2018-10-01 of hoger.
- Zorg ervoor dat de opgegeven externe resources in het model voor de schaalset beschikbaar en bijgewerkt zijn. Voor beelden zijn een SAS-URI voor de Boots trap ping in eigenschappen van VM-extensies, Payload in het opslag account, verwijzing naar geheimen in het model en meer.
- Voor schaal sets met virtuele Windows-machines, beginnend met Compute API versie 2019-03-01, moet de eigenschap *virtualMachineProfile. osProfile. windowsConfiguration. enableAutomaticUpdates* worden ingesteld op *False* in de model definitie van de schaalset. Met de bovenstaande eigenschap kunt u in-VM-upgrades gebruiken waarbij ' Windows Update ' patches van besturings systemen toepast zonder de besturingssysteem schijf te vervangen. Als automatische installatie kopieën van besturings systemen zijn ingeschakeld voor uw schaalset, is een extra update via "Windows Update" niet vereist.

### <a name="service-fabric-requirements"></a>Service Fabric vereisten

Als u Service Fabric gebruikt, moet u ervoor zorgen dat aan de volgende voor waarden wordt voldaan:
-   Service Fabric [duurzaamheids niveau](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) is zilver of goud en niet bronzen.
-   De Service Fabric-extensie voor de model definitie van de schaalset moet TypeHandlerVersion 1,1 of hoger hebben.
-   Duurzaamheids niveau moet hetzelfde zijn op het Service Fabric cluster en Service Fabric extensie in de model definitie van de schaalset.
- Een extra status test of het gebruik van de uitbrei ding van de toepassings status is niet vereist.

Zorg ervoor dat de duurzaamheids instellingen niet overeenkomen met het Service Fabric cluster en de uitbrei ding Service Fabric, omdat een niet-overeenkomend resultaat is opgetreden tijdens de upgrade. Duurzaamheids niveaus kunnen worden gewijzigd volgens de richt lijnen die op [Deze pagina](../service-fabric/service-fabric-cluster-capacity.md#changing-durability-levels)worden beschreven.


## <a name="automatic-os-image-upgrade-for-custom-images"></a>Automatische upgrade van besturings systeem installatie kopie voor aangepaste installatie kopieën

Automatische upgrade van besturings systeem installatie kopie wordt ondersteund voor aangepaste installatie kopieën die worden geïmplementeerd via de [Galerie met gedeelde afbeeldingen](../virtual-machines/shared-image-galleries.md). Andere aangepaste installatie kopieën worden niet ondersteund voor automatische upgrades van installatie kopieën van besturings systemen.

### <a name="additional-requirements-for-custom-images"></a>Aanvullende vereisten voor aangepaste installatie kopieën
- Het installatie-en configuratie proces voor de automatische upgrade van de installatie kopie van het besturings systeem is hetzelfde voor alle schaal sets, zoals beschreven in de [sectie configuratie](virtual-machine-scale-sets-automatic-upgrade.md#configure-automatic-os-image-upgrade) van deze pagina.
- Exemplaren van schaal sets die zijn geconfigureerd voor automatische upgrades van besturings systeem installatie kopieën worden bijgewerkt naar de nieuwste versie van de afbeelding van de galerie met gedeelde afbeeldingen wanneer een nieuwe versie van de installatie kopie wordt gepubliceerd en wordt [gerepliceerd](../virtual-machines/shared-image-galleries.md#replication) naar de regio van die schaalset. Als de nieuwe installatie kopie niet wordt gerepliceerd naar de regio waar de schaal wordt geïmplementeerd, worden de exemplaren van de schaalset niet bijgewerkt naar de meest recente versie. Met regionale afbeeldings replicatie kunt u de implementatie van de nieuwe installatie kopie voor uw schaal sets beheren.
- De nieuwe versie van de installatie kopie mag niet worden uitgesloten van de nieuwste versie van de galerie afbeelding. Installatie kopieën die zijn uitgesloten van de meest recente versie van de galerie afbeelding, worden niet meegenomen naar de schaalset via automatische upgrade van de installatie kopie van het besturings systeem.

> [!NOTE]
>Het kan tot drie uur duren voor een schaalset om de implementatie van de eerste installatie kopie te activeren nadat de schaalset voor het eerst is geconfigureerd voor automatische upgrades van besturings systemen. Dit is een eenmalige vertraging per schaalset. Volgende implementaties van installatie kopieën worden binnen 30-60 minuten geactiveerd op de schaalset.


## <a name="configure-automatic-os-image-upgrade"></a>Automatische upgrade van installatie kopie van besturings systeem configureren
Als u de automatische upgrade van de installatie kopie van het besturings systeem wilt configureren, controleert u of de eigenschap *automaticOSUpgradePolicy. enableAutomaticOSUpgrade* is ingesteld op *True* in de model definitie van de schaalset.

### <a name="rest-api"></a>REST-API
In het volgende voor beeld wordt beschreven hoe u automatische besturingssysteem upgrades instelt voor een schaalset-model:

```
PUT or PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version=2019-12-01`
```

```json
{
  "properties": {
    "upgradePolicy": {
      "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade":  true
      }
    }
  }
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Gebruik de cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) om automatische upgrades van de besturingssysteem installatie kopie voor uw schaalset te configureren. In het volgende voor beeld worden automatische upgrades ingesteld voor de schaalset met de naam *myScaleSet* in de resource groep met de naam *myResourceGroup*:

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -AutomaticOSUpgrade $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Gebruik [AZ vmss update](/cli/azure/vmss#az-vmss-update) om automatische upgrades van besturings systemen te configureren voor uw schaalset. Gebruik Azure CLI 2.0.47 of hoger. In het volgende voor beeld worden automatische upgrades ingesteld voor de schaalset met de naam *myScaleSet* in de resource groep met de naam *myResourceGroup*:

```azurecli-interactive
az vmss update --name myScaleSet --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```

> [!NOTE]
>Na het configureren van de automatische upgrade van besturings systeem installatie kopieën voor uw schaalset, moet u ook de virtuele machines van de schaalset naar het meest recente model van de schaalset brengen als uw schaalset gebruikmaakt van het [beleid](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)' hand matig '.

## <a name="using-application-health-probes"></a>Status tests van toepassingen gebruiken

Tijdens een upgrade van het besturings systeem worden VM-exemplaren in een schaalset één batch tegelijk geüpgraded. De upgrade moet alleen door gaan als de toepassing van de klant in orde is op de bijgewerkte VM-exemplaren. U wordt aangeraden de toepassing status signalen te bieden voor de upgrade-engine van het besturings systeem voor de schaalset. Tijdens de upgrade van het besturings systeem wordt het platform standaard beschouwd als de status van de VM-energie status en-extensie om te bepalen of een VM-exemplaar in orde is na een upgrade. Tijdens de upgrade van het besturings systeem van een VM-exemplaar wordt de besturingssysteem schijf op een VM-exemplaar vervangen door een nieuwe schijf op basis van de nieuwste versie van de installatie kopie. Nadat de upgrade van het besturings systeem is voltooid, worden de geconfigureerde uitbrei dingen uitgevoerd op deze Vm's. De toepassing wordt alleen in orde geacht als alle uitbrei dingen van het exemplaar zijn ingericht.

Een schaalset kan eventueel worden geconfigureerd met toepassings status tests om het platform te voorzien van nauw keurige informatie over de doorlopende status van de toepassing. De status tests van toepassingen zijn aangepaste Load Balancer tests die worden gebruikt als een status signaal. De toepassing die wordt uitgevoerd op een VM-instantie van een schaalset kan reageren op externe HTTP-of TCP-aanvragen die aangeven of deze in orde zijn. Zie voor meer informatie over hoe aangepaste Load Balancer tests werken om te [begrijpen Load Balancer tests](../load-balancer/load-balancer-custom-probe-overview.md). De status tests van toepassingen worden niet ondersteund voor Service Fabric schaal sets. Voor niet-Service Fabric schaal sets is Load Balancer toepassings status tests of een [toepassings status uitbreiding](virtual-machine-scale-sets-health-extension.md)vereist.

Als de schaalset is geconfigureerd voor het gebruik van meerdere plaatsings groepen, moeten tests met een [Standard Load Balancer](../load-balancer/load-balancer-overview.md) worden gebruikt.

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Een aangepaste Load Balancer test configureren als de status test van de toepassing in een schaalset
Maak als best practice een load balancer test expliciet voor de status van de schaalset. Hetzelfde eind punt voor een bestaande HTTP-test of TCP-test kan worden gebruikt, maar een status test kan een ander gedrag van een traditionele Load Balancer-test vereisen. Een traditionele load balancer test kan bijvoorbeeld een slechte status retour neren als de belasting van het exemplaar te hoog is, maar dat niet geschikt zou zijn voor het bepalen van de status van het exemplaar tijdens een automatische upgrade van het besturings systeem. Stel de test zodanig in dat deze een hoge Zoek frequentie heeft van minder dan twee minuten.

Naar de Load Balancer-test kan worden verwezen in de *networkProfile* van de schaalset en kan als volgt worden gekoppeld aan een interne of open bare Load Balancer:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
}
```

> [!NOTE]
> Wanneer u automatische besturingssysteem upgrades met Service Fabric gebruikt, wordt de nieuwe installatie kopie van het besturings systeem bijgewerkt op basis van het update domein om hoge Beschik baarheid te behouden van de services die worden uitgevoerd in Service Fabric. Als u automatische besturingssysteem upgrades wilt gebruiken in Service Fabric moet uw cluster zijn geconfigureerd voor het gebruik van de Silver duurzaamheid-laag of hoger. Raadpleeg [deze documentatie](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)voor meer informatie over de duurzaamheids kenmerken van service Fabric clusters.

### <a name="keep-credentials-up-to-date"></a>Referenties up-to-date houden
Als uw schaalset referenties gebruikt voor toegang tot externe bronnen, zoals een VM-extensie die is geconfigureerd voor het gebruik van een SAS-token voor opslag account, moet u ervoor zorgen dat de referenties worden bijgewerkt. Als er referenties, inclusief certificaten en tokens, zijn verlopen, mislukt de upgrade en wordt de eerste batch van Vm's in een mislukte status gelaten.

De aanbevolen stappen voor het herstellen van Vm's en het opnieuw inschakelen van de automatische upgrade van het besturings systeem als er een fout is opgetreden bij de verificatie van de bron:

* Genereer het token (of andere referenties) opnieuw die is door gegeven aan uw extensie (s).
* Zorg ervoor dat de referenties die in de virtuele machine worden gebruikt om te communiceren met externe entiteiten up-to-date zijn.
* Een of meer uitbrei dingen in het model met de schaalset bijwerken met nieuwe tokens.
* Implementeer de bijgewerkte schaalset, waarmee alle VM-exemplaren met inbegrip van de mislukte taken worden bijgewerkt.

## <a name="using-application-health-extension"></a>De toepassings status uitbreiding gebruiken
De uitbrei ding voor de toepassings status wordt geïmplementeerd binnen een exemplaar van een virtuele-machine schaalset en rapporten over de VM-status in het exemplaar van de schaalset. U kunt de uitbrei ding voor het testen van het eind punt van een toepassing configureren en de status van de toepassing op dat exemplaar bijwerken. Deze status van het exemplaar wordt gecontroleerd door Azure om te bepalen of een exemplaar in aanmerking komt voor upgrade bewerkingen.

Als de uitbrei ding rapporteert over de status van binnen een virtuele machine, kan de uitbrei ding worden gebruikt in situaties waarin externe tests, zoals het controleren van de status van toepassingen (die gebruikmaken van aangepaste Azure Load Balancer [tests](../load-balancer/load-balancer-custom-probe-overview.md)), niet kunnen worden gebruikt.

Er zijn meerdere manieren om de toepassings status extensie te implementeren op uw schaal sets, zoals beschreven in de voor beelden in [dit artikel](virtual-machine-scale-sets-health-extension.md#deploy-the-application-health-extension).

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>De geschiedenis van automatische upgrades van installatie kopieën van besturings systemen ophalen
U kunt de geschiedenis controleren van de meest recente upgrade van het besturings systeem dat is uitgevoerd op uw schaalset met Azure PowerShell, Azure CLI 2,0 of de REST-Api's. U kunt in de afgelopen twee maanden geschiedenis ontvangen voor de laatste vijf besturingssysteem upgrade pogingen.

### <a name="rest-api"></a>REST-API
In het volgende voor beeld wordt [rest API](/rest/api/compute/virtualmachinescalesets/getosupgradehistory) gebruikt om de status te controleren voor de schaalset met de naam *myScaleSet* in de resource groep met de naam *myResourceGroup*:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2019-12-01`
```

De GET-aanroep retourneert eigenschappen die vergelijkbaar zijn met de volgende voorbeeld uitvoer:

```json
{
    "value": [
        {
            "properties": {
        "runningStatus": {
          "code": "RollingForward",
          "startTime": "2018-07-24T17:46:06.1248429+00:00",
          "completedTime": "2018-04-21T12:29:25.0511245+00:00"
        },
        "progress": {
          "successfulInstanceCount": 16,
          "failedInstanceCount": 0,
          "inProgressInstanceCount": 4,
          "pendingInstanceCount": 0
        },
        "startedBy": "Platform",
        "targetImageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "2016.127.20180613"
        },
        "rollbackInfo": {
          "successfullyRolledbackInstanceCount": 0,
          "failedRolledbackInstanceCount": 0
        }
      },
      "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
      "location": "westeurope"
    }
  ]
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Gebruik de cmdlet [Get-AzVmss](/powershell/module/az.compute/get-azvmss) om de upgrade geschiedenis van het besturings systeem voor uw schaalset te controleren. In het volgende voor beeld wordt beschreven hoe u de upgrade status van het besturings systeem bekijkt voor een schaalset met de naam *myScaleSet* in de resource groep met de naam *myResourceGroup*:

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Gebruik [AZ vmss Get-OS-upgrade-History](/cli/azure/vmss#az-vmss-get-os-upgrade-history) om de upgrade geschiedenis van het besturings systeem voor uw schaalset te controleren. Gebruik Azure CLI 2.0.47 of hoger. In het volgende voor beeld wordt beschreven hoe u de upgrade status van het besturings systeem bekijkt voor een schaalset met de naam *myScaleSet* in de resource groep met de naam *myResourceGroup*:

```azurecli-interactive
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myScaleSet
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>Hoe kunt u de nieuwste versie van een platform installatie kopie van het besturings systeem ophalen?

U kunt de beschik bare installatie kopieën ophalen voor de automatische ondersteunde Sku's voor upgrades van besturings systemen met behulp van de onderstaande voor beelden:

### <a name="rest-api"></a>REST-API
```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
Get-AzVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
```azurecli-interactive
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="manually-trigger-os-image-upgrades"></a>Upgrades van OS-installatie kopieën hand matig activeren
Als automatische installatie kopie van het besturings systeem op uw schaalset is ingeschakeld, hoeft u geen installatie kopieën van afbeeldingen hand matig te activeren in uw schaalset. De upgrade orchestrator van het besturings systeem past automatisch de meest recente versie van de installatie kopie toe op de instanties van uw schaalset zonder hand matige tussen komst.

Voor specifieke gevallen waarin u niet wilt wachten totdat de Orchestrator de meest recente installatie kopie heeft toegepast, kunt u een installatie kopie van het besturings systeem hand matig activeren met behulp van de onderstaande voor beelden.

> [!NOTE]
> De hand matige trigger van upgrades van installatie kopieën biedt geen automatische terugdraai mogelijkheden. Als een exemplaar de status niet herstelt na een upgrade bewerking, kan de vorige besturingssysteem schijf niet worden hersteld.

### <a name="rest-api"></a>REST-API
Gebruik de aanroep van de [OS upgrade](/rest/api/compute/virtualmachinescalesetrollingupgrades/startosupgrade) -API starten om een rolling upgrade te starten om alle exemplaren van virtuele-machine schaal sets te verplaatsen naar de meest recente versie van het besturings systeem van de installatie kopie. Exemplaren die al de meest recente beschik bare versie van het besturings systeem uitvoeren, worden niet beïnvloed. In het volgende voor beeld wordt beschreven hoe u een rolling upgrade van een besturings systeem kunt starten op een schaalset met de naam *myScaleSet* in de resource groep met de naam *myResourceGroup*:

```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osRollingUpgrade?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Gebruik de cmdlet [Start-AzVmssRollingOSUpgrade](/powershell/module/az.compute/Start-AzVmssRollingOSUpgrade) om de upgrade geschiedenis van het besturings systeem voor uw schaalset te controleren. In het volgende voor beeld wordt beschreven hoe u een rolling upgrade van een besturings systeem kunt starten op een schaalset met de naam *myScaleSet* in de resource groep met de naam *myResourceGroup*:

```azurepowershell-interactive
Start-AzVmssRollingOSUpgrade -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Gebruik [AZ vmss rolling-upgrade start](/cli/azure/vmss/rolling-upgrade#az-vmss-rolling-upgrade-start) om de upgrade geschiedenis van het besturings systeem voor uw schaalset te controleren. Gebruik Azure CLI 2.0.47 of hoger. In het volgende voor beeld wordt beschreven hoe u een rolling upgrade van een besturings systeem kunt starten op een schaalset met de naam *myScaleSet* in de resource groep met de naam *myResourceGroup*:

```azurecli-interactive
az vmss rolling-upgrade start --resource-group "myResourceGroup" --name "myScaleSet" --subscription "subscriptionId"
```

## <a name="deploy-with-a-template"></a>Implementeren met een sjabloon

U kunt sjablonen gebruiken om een schaalset te implementeren met automatische upgrades van het besturings systeem voor ondersteunde installatie kopieën, zoals [Ubuntu 16,04-LTS](https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png" alt="Button to Deploy to Azure." /></a>

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de [github opslag plaats](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade)voor meer voor beelden over het gebruik van automatische besturingssysteem upgrades met schaal sets.