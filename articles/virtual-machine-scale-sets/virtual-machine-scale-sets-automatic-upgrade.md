---
title: Automatische upgrades van besturingssysteemafbeeldingen met virtuele-machineschaalsets van Azure
description: Meer informatie over het automatisch upgraden van de besturingssysteemafbeelding op VM-exemplaren in een schaalset
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: automatic-os-upgrade
ms.date: 06/26/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: 1e32ff4bc1c39e8a3385f8037f88cedbdc17d3a6
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375743"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Upgrade van Azure virtual machine-schaalsets automatische installatiekopieën van besturingssystemen

Als u automatische upgrades van besturingssysteemafbeeldingen in uw schaalset inschakelen, kunt u het updatebeheer schalen door de besturingssysteemschijf veilig en automatisch te upgraden voor alle exemplaren in de schaalset.

Automatische upgrade van het besturingssysteem heeft de volgende kenmerken:

- Na de configuratie wordt de meest recente afbeelding van het besturingssysteem die is gepubliceerd door uitgevers van afbeeldingen automatisch toegepast op de schaalset zonder tussenkomst van de gebruiker.
- Batches van exemplaren worden op een rolling manier geupgraded telkens wanneer een nieuwe afbeelding wordt gepubliceerd door de uitgever.
- Kan worden geïntegreerd met toepassings statustests en [toepassings health-extensie](virtual-machine-scale-sets-health-extension.md).
- Werkt voor alle VM-grootten en voor zowel Windows- als Linux-afbeeldingen.
- U kunt op elk moment kiezen voor automatische upgrades (upgrades van het besturingssysteem kunnen ook handmatig worden gestart).
- De besturingssysteemschijf van een VM wordt vervangen door de nieuwe besturingssysteemschijf die is gemaakt met de nieuwste versie van de afbeelding. Geconfigureerde extensies en aangepaste gegevensscripts worden uitgevoerd, terwijl persistente gegevensschijven worden bewaard.
- [Sequencing van extensies](virtual-machine-scale-sets-extension-sequencing.md) wordt ondersteund.
- Automatische upgrade van besturingssysteemafbeeldingen kan worden ingeschakeld voor een schaalset van elke grootte.

## <a name="how-does-automatic-os-image-upgrade-work"></a>Hoe werkt automatische upgrade van besturingssysteemafbeeldingen?

Een upgrade werkt door de besturingssysteemschijf van een VM te vervangen door een nieuwe schijf die is gemaakt met behulp van de nieuwste versie van de afbeelding. Geconfigureerde extensies en aangepaste gegevensscripts worden uitgevoerd op de besturingssysteemschijf, terwijl persistente gegevensschijven worden bewaard. Om de downtime van de toepassing te minimaliseren, vinden upgrades plaats in batches, met niet meer dan 20% van de upgrade van de schaalset op elk moment. U kunt ook een toepassings Azure Load Balancer-statustest of [toepassings health-extensie integreren.](virtual-machine-scale-sets-health-extension.md) Het is raadzaam om een toepassings-heartbeat te gebruiken en de upgrade voor elke batch in het upgradeproces te valideren.

Het upgradeproces werkt als volgt:
1. Voordat u het upgradeproces start, zorgt de orchestrator ervoor dat niet meer dan 20% van de exemplaren in de hele schaalset een slechte status hebben (om welke reden dan ook).
2. De upgrade-orchestrator identificeert de batch VM-exemplaren die moeten worden geupgraded, met elke batch met een maximum van 20% van het totale aantal exemplaren, afhankelijk van een minimale batchgrootte van één virtuele machine.
3. De besturingssysteemschijf van de geselecteerde batch met VM-exemplaren wordt vervangen door een nieuwe besturingssysteemschijf die is gemaakt op basis van de meest recente afbeelding. Alle opgegeven extensies en configuraties in het schaalsetmodel worden toegepast op het bijgewerkte exemplaar.
4. Voor schaalsets met geconfigureerde statustests voor toepassingen of de toepassings health-extensie wacht de upgrade maximaal vijf minuten totdat het exemplaar in orde is, voordat de volgende batch wordt geupgraded. Als de status van een exemplaar niet binnen vijf minuten na een upgrade wordt hersteld, wordt standaard de vorige besturingssysteemschijf voor het exemplaar hersteld.
5. De upgrade-orchestrator houdt ook het percentage exemplaren bij dat na een upgrade niet in orde is. De upgrade wordt gestopt als meer dan 20% van de bijgewerkte exemplaren tijdens het upgradeproces niet in orde is.
6. Het bovenstaande proces wordt voortgezet totdat alle exemplaren in de schaalset zijn bijgewerkt.

De orchestrator voor de upgrade van het besturingssysteem van de schaalset controleert de algehele status van de schaalset voordat elke batch wordt geupgraded. Tijdens het upgraden van een batch kunnen er andere gelijktijdige geplande of ongeplande onderhoudsactiviteiten zijn die van invloed kunnen zijn op de status van uw schaalset-exemplaren. Als in dergelijke gevallen meer dan 20% van de exemplaren van de schaalset niet in orde is, stopt de upgrade van de schaalset aan het einde van de huidige batch.

> [!NOTE]
>Automatische upgrade van het besturingssysteem werkt de referentie-SKU voor de schaalset niet bij. Als u de SKU (zoals Ubuntu 16.04-LTS naar 18.04-LTS) wilt wijzigen, moet u het schaalsetmodel rechtstreeks bijwerken met de gewenste SKU voor afbeeldingen. [](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model) Uitgever en aanbieding van afbeeldingen kunnen niet worden gewijzigd voor een bestaande schaalset.  

## <a name="supported-os-images"></a>Ondersteunde besturingssysteemafbeeldingen
Alleen bepaalde platformafbeeldingen van het besturingssysteem worden momenteel ondersteund. Aangepaste afbeeldingen [worden ondersteund als](virtual-machine-scale-sets-automatic-upgrade.md#automatic-os-image-upgrade-for-custom-images) de schaalset aangepaste afbeeldingen gebruikt via [Shared Image Gallery](../virtual-machines/shared-image-galleries.md).

De volgende platform-SKU's worden momenteel ondersteund (en er worden periodiek meer toegevoegd):

| Publisher               | Aanbieding voor besturingssysteem      |  Sku               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18.04-LTS          |
| OpenLogic               | CentOS        | 7,5                |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Smalldisk |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-with-Containers |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter-Smalldisk |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter-with-Containers |
| MicrosoftWindowsServer  | WindowsServer | Datacenter-Core-1903-with-Containers-smalldisk |


## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Vereisten voor het configureren van de automatische upgrade van de besturingssysteemafbeelding

- De *versie-eigenschap* van de afbeelding moet worden ingesteld op *de meest recente*.
- Gebruik toepassings statustests [of toepassings health-extensie](virtual-machine-scale-sets-health-extension.md) voor niet-Service Fabric schaalsets.
- Gebruik Compute API versie 2018-10-01 of hoger.
- Zorg ervoor dat externe resources die zijn opgegeven in het schaalsetmodel beschikbaar zijn en worden bijgewerkt. Voorbeelden zijn SAS-URI voor het opstarten van nettoladingen in eigenschappen van VM-extensies, nettolading in opslagaccount, verwijzing naar geheimen in het model en meer.
- Voor schaalsets die gebruikmaken van virtuele Windows-machines, moet vanaf compute-API-versie 2019-03-01 de eigenschap *virtualMachineProfile.osProfile.windowsConfiguration.enableAutomaticUpdates* zijn ingesteld op *false* in de modeldefinitie van de schaalset. De bovenstaande eigenschap maakt in-VM-upgrades mogelijk waarbij 'Windows Update' patches van het besturingssysteem worden toegepast zonder de besturingssysteemschijf te vervangen. Als automatische upgrades voor besturingssysteemafbeeldingen zijn ingeschakeld op uw schaalset, is een extra update via 'Windows Update' niet vereist.

### <a name="service-fabric-requirements"></a>Service Fabric vereisten

Als u een Service Fabric, controleert u of aan de volgende voorwaarden wordt voldaan:
-   Service Fabric [duurzaamheidsniveau](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) is Silver of Gold, en niet Brons (met uitzondering van stateless knooppunttypen, die wel ondersteuning bieden voor automatische upgrades van het besturingssysteem).
-   De Service Fabric-extensie voor de modeldefinitie van de schaalset moet TypeHandlerVersion 1.1 of hoger hebben.
-   Het duurzaamheidsniveau moet hetzelfde zijn op het Service Fabric cluster en Service Fabric voor de definitie van het schaalsetmodel.
- Er is geen aanvullende statustest of het gebruik van de toepassingstoestandsextensie vereist.

Zorg ervoor dat de duurzaamheidsinstellingen niet overeenkomen op de Service Fabric cluster en Service Fabric extensie, omdat een niet-overeenkomend upgradefouten tot gevolg heeft. Duurzaamheidsniveaus kunnen worden gewijzigd volgens de richtlijnen die op deze [pagina worden beschreven.](../service-fabric/service-fabric-cluster-capacity.md#changing-durability-levels)


## <a name="automatic-os-image-upgrade-for-custom-images"></a>Automatische upgrade van besturingssysteemafbeeldingen voor aangepaste afbeeldingen

Automatische upgrade van besturingssysteemafbeeldingen wordt ondersteund voor aangepaste afbeeldingen die zijn geïmplementeerd [via Shared Image Gallery](../virtual-machines/shared-image-galleries.md). Andere aangepaste afbeeldingen worden niet ondersteund voor automatische upgrades van besturingssysteemafbeeldingen.

### <a name="additional-requirements-for-custom-images"></a>Aanvullende vereisten voor aangepaste afbeeldingen
- Het installatie- en configuratieproces voor de automatische upgrade van de installatie van de installatie van besturingssysteem is hetzelfde voor alle schaalsets, zoals beschreven in de [configuratiesectie](virtual-machine-scale-sets-automatic-upgrade.md#configure-automatic-os-image-upgrade) van deze pagina.
- Exemplaren van schaalsets die zijn geconfigureerd voor automatische upgrades van besturingssysteemafbeeldingen, worden bijgewerkt naar [](../virtual-machines/shared-image-galleries.md#replication) de nieuwste versie van de Shared Image Gallery-afbeelding wanneer een nieuwe versie van de afbeelding wordt gepubliceerd en gerepliceerd naar de regio van die schaalset. Als de nieuwe afbeelding niet wordt gerepliceerd naar de regio waar de schaal is geïmplementeerd, worden de schaalset-exemplaren niet bijgewerkt naar de nieuwste versie. Met regionale replicatie van afbeeldingen kunt u de implementatie van de nieuwe afbeelding voor uw schaalsets bepalen.
- De nieuwe versie van de afbeelding mag niet worden uitgesloten van de nieuwste versie voor die galerie-afbeelding. Versies van afbeeldingen die zijn uitgesloten van de nieuwste versie van de galerie-afbeelding, worden niet uitgerold naar de schaalset via automatische upgrade van de besturingssysteemafbeelding.

> [!NOTE]
>Het kan tot drie uur duren voordat een schaalset de eerste implementatie van de upgrade van de afbeelding activeert nadat de schaalset voor het eerst is geconfigureerd voor automatische upgrades van het besturingssysteem. Dit is een een time-delay per schaalset. Volgende implementaties van afbeeldingen worden binnen 30-60 minuten op de schaalset geactiveerd.


## <a name="configure-automatic-os-image-upgrade"></a>Automatische upgrade van besturingssysteemafbeelding configureren
Als u de automatische upgrade van de besturingssysteemafbeelding wilt configureren, moet u ervoor zorgen dat de eigenschap *automaticOSUpgradePolicy.enableAutomaticOSUpgrade* is ingesteld op *true* in de modeldefinitie van de schaalset.

### <a name="rest-api"></a>REST-API
In het volgende voorbeeld wordt beschreven hoe u automatische upgrades van het besturingssysteem kunt instellen voor een schaalsetmodel:

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
Gebruik de [cmdlet Update-AzVmss](/powershell/module/az.compute/update-azvmss) om automatische upgrades van besturingssysteemafbeeldingen voor uw schaalset te configureren. In het volgende voorbeeld worden automatische upgrades geconfigureerd voor de schaalset met de *naam myScaleSet* in de resourcegroep met de *naam myResourceGroup:*

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -AutomaticOSUpgrade $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Gebruik [az vmss update om automatische upgrades](/cli/azure/vmss#az-vmss-update) voor besturingssysteemafbeeldingen voor uw schaalset te configureren. Gebruik Azure CLI 2.0.47 of hoger. In het volgende voorbeeld worden automatische upgrades geconfigureerd voor de schaalset met de *naam myScaleSet* in de resourcegroep met de *naam myResourceGroup:*

```azurecli-interactive
az vmss update --name myScaleSet --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```

> [!NOTE]
>Nadat u automatische upgrades van besturingssysteemafbeeldingen voor uw schaalset hebt geconfigureerd, moet u ook de schaalset-VM's naar het meest recente schaalsetmodel brengen als uw schaalset gebruikmaakt van het [upgradebeleid Handmatig.](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)

## <a name="using-application-health-probes"></a>Toepassingstests gebruiken

Tijdens een upgrade van het besturingssysteem worden VM-exemplaren in een schaalset batch voor batch bijgewerkt. De upgrade mag alleen worden voortgezet als de klanttoepassing in orde is op de bijgewerkte VM-exemplaren. Het is raadzaam dat de toepassing statussignalen levert aan de upgrade-engine van het besturingssysteem van de schaalset. Tijdens upgrades van het besturingssysteem overweegt het platform standaard de status van de VM-energie- en extensie-inrichting om te bepalen of een VM-exemplaar in orde is na een upgrade. Tijdens de upgrade van het besturingssysteem van een VM-exemplaar wordt de besturingssysteemschijf op een VM-exemplaar vervangen door een nieuwe schijf op basis van de meest recente versie van de afbeelding. Nadat de upgrade van het besturingssysteem is voltooid, worden de geconfigureerde extensies uitgevoerd op deze VM's. De toepassing wordt alleen als in orde beschouwd wanneer alle extensies op het exemplaar zijn ingericht.

Een schaalset kan eventueel worden geconfigureerd met toepassingstests om het platform nauwkeurige informatie te bieden over de lopende status van de toepassing. Statustests van toepassingen zijn aangepaste Load Balancer tests die worden gebruikt als een statussignaal. De toepassing die wordt uitgevoerd op een VM-exemplaar van een schaalset kan reageren op externe HTTP- of TCP-aanvragen die aangeven of deze in orde zijn. Zie Understand load balancer probes (Meer informatie over Load Balancer tests) voor meer informatie over hoe [aangepaste load balancer werken.](../load-balancer/load-balancer-custom-probe-overview.md) Statustests van toepassingen worden niet ondersteund voor Service Fabric schaalsets. Voor niet-Service Fabric-schaalsets zijn Load Balancer toepassings statustests of [application health-extensie vereist.](virtual-machine-scale-sets-health-extension.md)

Als de schaalset is geconfigureerd voor het gebruik van meerdere plaatsingsgroepen, moeten tests met een [Standard Load Balancer](../load-balancer/load-balancer-overview.md) worden gebruikt.

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Een aangepaste test Load Balancer test als toepassings statustest voor een schaalset
Maak als best practice een test load balancer voor de status van de schaalset. Hetzelfde eindpunt voor een bestaande HTTP-test of TCP-test kan worden gebruikt, maar een statustest kan ander gedrag vereisen dan een traditionele load balancer-test. Een traditionele test voor load balancer kan bijvoorbeeld een slechte status retourneren als de belasting van het exemplaar te hoog is, maar dat is niet geschikt voor het bepalen van de status van het exemplaar tijdens een automatische upgrade van het besturingssysteem. Configureer de test met een hoge testsnelheid van minder dan twee minuten.

Er kan als volgt naar de load balancer-test worden verwezen in het *networkProfile* van de schaalset en kan als volgt worden gekoppeld aan een interne of openbare load balancer:

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
> Wanneer u Automatische upgrades van het besturingssysteem gebruikt met Service Fabric, wordt de nieuwe besturingssysteemafbeelding door Updatedomein uitgerold om hoge beschikbaarheid te behouden van de services die worden uitgevoerd in Service Fabric. Als u automatische upgrades van het besturingssysteem in Service Fabric moet uw clusterknooppunttype worden geconfigureerd voor het gebruik van de Silver-duurzaamheidslaag of hoger. Voor de duurzaamheidslaag Brons wordt automatische upgrade van het besturingssysteem alleen ondersteund voor staatloze knooppunttypen. Raadpleeg deze documentatie voor meer informatie over de duurzaamheidskenmerken van Service Fabric [clusters.](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)

### <a name="keep-credentials-up-to-date"></a>Referenties up-to-date houden
Als uw schaalset referenties gebruikt voor toegang tot externe resources, zoals een VM-extensie die is geconfigureerd voor het gebruik van een SAS-token voor het opslagaccount, controleert u of de referenties zijn bijgewerkt. Als referenties, inclusief certificaten en tokens, zijn verlopen, mislukt de upgrade en blijft de eerste batch VM's in de status Mislukt.

De aanbevolen stappen voor het herstellen van VM's en het opnieuw inschakelen van automatische upgrade van het besturingssysteem als er een resourceverificatiefout is, zijn:

* Regenerer het token (of andere referenties) dat is doorgegeven aan uw extensie(s).
* Zorg ervoor dat alle referenties die vanuit de VM worden gebruikt om met externe entiteiten te praten, up-to-date zijn.
* Werk de extensie(s) in het schaalsetmodel bij met nieuwe tokens.
* Implementeer de bijgewerkte schaalset, waarmee alle VM-exemplaren worden bijgewerkt, inclusief de mislukte exemplaren.

## <a name="using-application-health-extension"></a>Application Health-extensie gebruiken
De Toepassings health-extensie wordt geïmplementeerd in een exemplaar van een virtuele-machineschaalset en rapporteert over de VM-status vanuit het exemplaar van de schaalset. U kunt de extensie configureren om te controleren op een toepassings-eindpunt en de status van de toepassing op dat exemplaar bij te werken. Deze exemplaarstatus wordt gecontroleerd door Azure om te bepalen of een exemplaar in aanmerking komt voor upgradebewerkingen.

Als de extensie status rapporteert vanuit een VM, kan de extensie worden gebruikt in situaties waarin externe [](../load-balancer/load-balancer-custom-probe-overview.md)tests zoals statustests van toepassingen (die gebruikmaken van aangepaste Azure Load Balancer-tests) niet kunnen worden gebruikt.

Er zijn meerdere manieren om de Toepassings health-extensie te implementeren in uw schaalsets, zoals beschreven in de voorbeelden in [dit artikel.](virtual-machine-scale-sets-health-extension.md#deploy-the-application-health-extension)

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>De geschiedenis van automatische upgrades van besturingssysteemafbeeldingen bekijken
U kunt de geschiedenis van de meest recente upgrade van het besturingssysteem die op uw schaalset is uitgevoerd, controleren met Azure PowerShell, Azure CLI 2.0 of de REST API's. U kunt de geschiedenis van de laatste vijf upgradepogingen van het besturingssysteem in de afgelopen twee maanden bekijken.

### <a name="rest-api"></a>REST-API
In het volgende voorbeeld wordt [REST API](/rest/api/compute/virtualmachinescalesets/getosupgradehistory) om de status te controleren voor de schaalset met de naam *myScaleSet* in de resourcegroep met de *naam myResourceGroup:*

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2019-12-01`
```

De GET-aanroep retourneert eigenschappen die vergelijkbaar zijn met de volgende voorbeelduitvoer:

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
Gebruik de [cmdlet Get-AzVmss](/powershell/module/az.compute/get-azvmss) om de upgradegeschiedenis van het besturingssysteem voor uw schaalset te controleren. In het volgende voorbeeld ziet u hoe u de upgradestatus van het besturingssysteem controleert voor een schaalset met de naam *myScaleSet* in de resourcegroep met de *naam myResourceGroup:*

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Gebruik [az vmss get-os-upgrade-history](/cli/azure/vmss#az-vmss-get-os-upgrade-history) om de upgradegeschiedenis van het besturingssysteem voor uw schaalset te controleren. Gebruik Azure CLI 2.0.47 of hoger. In het volgende voorbeeld ziet u hoe u de upgradestatus van het besturingssysteem controleert voor een schaalset met de naam *myScaleSet* in de resourcegroep met de *naam myResourceGroup:*

```azurecli-interactive
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myScaleSet
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>Hoe kan ik de nieuwste versie van een besturingssysteem van een platform krijgen?

U kunt de beschikbare versies van de afbeelding voor ondersteunde SKU's voor automatische besturingssysteemupgrade verkrijgen met behulp van de onderstaande voorbeelden:

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

## <a name="manually-trigger-os-image-upgrades"></a>Handmatig upgrades van besturingssysteemafbeeldingen activeren
Als de automatische upgrade van de besturingssysteemafbeelding is ingeschakeld in uw schaalset, hoeft u updates van afbeeldingen niet handmatig te activeren op uw schaalset. De orchestrator voor het upgraden van het besturingssysteem past automatisch de nieuwste beschikbare versie van de afbeelding toe op uw schaalset-exemplaren zonder handmatige tussenkomst.

Voor specifieke gevallen waarin u niet wilt wachten tot de orchestrator de meest recente afbeelding heeft toegepast, kunt u een upgrade van de besturingssysteemafbeelding handmatig activeren met behulp van de onderstaande voorbeelden.

> [!NOTE]
> Handmatige trigger van upgrades van besturingssysteemafbeeldingen biedt geen mogelijkheden voor automatisch terugdraaien. Als een exemplaar de status niet herstelt na een upgradebewerking, kan de vorige besturingssysteemschijf niet worden hersteld.

### <a name="rest-api"></a>REST-API
Gebruik de [api-aanroep Upgrade](/rest/api/compute/virtualmachinescalesetrollingupgrades/startosupgrade) van het besturingssysteem starten om een rolling upgrade om alle exemplaren van de virtuele-machineschaalset te verplaatsen naar de nieuwste versie van het besturingssysteem van de afbeelding. Exemplaren met de nieuwste versie van het besturingssysteem worden niet beïnvloed. In het volgende voorbeeld ziet u hoe u een rolling upgrade van het besturingssysteem kunt starten op een schaalset met de *naam myScaleSet* in de resourcegroep met de *naam myResourceGroup:*

```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osRollingUpgrade?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Gebruik de cmdlet [Start-AzVmssRollingOSUpgrade](/powershell/module/az.compute/Start-AzVmssRollingOSUpgrade) om de upgradegeschiedenis van het besturingssysteem voor uw schaalset te controleren. In het volgende voorbeeld ziet u hoe u een rolling upgrade van het besturingssysteem kunt starten op een schaalset met de *naam myScaleSet* in de resourcegroep met de *naam myResourceGroup:*

```azurepowershell-interactive
Start-AzVmssRollingOSUpgrade -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Gebruik [az vmss rolling-upgrade start om](/cli/azure/vmss/rolling-upgrade#az-vmss-rolling-upgrade-start) de upgradegeschiedenis van het besturingssysteem voor uw schaalset te controleren. Gebruik Azure CLI 2.0.47 of hoger. In het volgende voorbeeld ziet u hoe u een rolling upgrade van het besturingssysteem kunt starten op een schaalset met de *naam myScaleSet* in de resourcegroep met de *naam myResourceGroup:*

```azurecli-interactive
az vmss rolling-upgrade start --resource-group "myResourceGroup" --name "myScaleSet" --subscription "subscriptionId"
```

## <a name="deploy-with-a-template"></a>Implementeren met een sjabloon

U kunt sjablonen gebruiken voor het implementeren van een schaalset met automatische upgrades van het besturingssysteem voor ondersteunde installatie afbeeldingen, zoals [Ubuntu 16.04-LTS.](https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json)

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png" alt="Button to Deploy to Azure." /></a>

## <a name="next-steps"></a>Volgende stappen
Bekijk de GitHub-opslagplaats voor meer voorbeelden over het gebruik van automatische upgrades van besturingssystemen met [schaalsets.](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade)