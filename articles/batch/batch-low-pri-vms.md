---
title: Werk belastingen uitvoeren op rendabele virtuele machines met lage prioriteit
description: Meer informatie over het inrichten van Vm's met lage prioriteit om de kosten van Azure Batch workloads te verminderen.
author: mscurrell
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: seodec18
ms.openlocfilehash: cafc7216e8112640f823ecee1aea055ab78b3fc6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102098465"
---
# <a name="use-low-priority-vms-with-batch"></a>Virtuele machines met lage prioriteit met Batch gebruiken

Azure Batch biedt virtuele machines met lage prioriteit (Vm's) om de kosten van batch-workloads te verlagen. Virtuele machines met lage prioriteit maken nieuwe typen batch werkbelasting mogelijk door een grote hoeveelheid reken kracht in te scha kelen die voor een zeer lage kosten kan worden gebruikt.

Vm's met lage prioriteit profiteren van de overschot capaciteit in Azure. Wanneer u virtuele machines met lage prioriteit in uw Pools opgeeft, kunt Azure Batch dit overschot gebruiken, indien beschikbaar.

Het afwegings niveau voor het gebruik van virtuele machines met lage prioriteit is dat deze Vm's niet altijd beschikbaar zijn om te worden toegewezen, of dat ze op elk gewenst moment kunnen worden voor rang, afhankelijk van de beschik bare capaciteit. Daarom zijn virtuele machines met lage prioriteit het meest geschikt voor werk belastingen voor batch en asynchrone verwerking waarbij de voltooiings tijd van de taak flexibel is en het werk wordt gedistribueerd over meerdere Vm's.

Vm's met lage prioriteit worden aangeboden tegen een aanzienlijk gereduceerde prijs vergeleken met toegewezen Vm's. Zie [batch-prijzen](https://azure.microsoft.com/pricing/details/batch/)voor meer informatie over prijzen.

> [!NOTE]
> [Er zijn nu virtuele machines beschikbaar](https://azure.microsoft.com/pricing/spot/) voor [virtuele machines met één exemplaar](../virtual-machines/spot-vms.md) en [VM-schaal sets](../virtual-machine-scale-sets/use-spot.md). Spot-Vm's zijn een evolutie van Vm's met lage prioriteit, maar verschillen in die prijzen kunnen variëren en een optionele maximum prijs kan worden ingesteld bij het toewijzen van spot-Vm's.
>
>Azure Batch-groepen zullen in de toekomst beginnen met het ondersteunen van spot-Vm's, met nieuwe versies van de [batch-api's en-hulpprogram ma's](./batch-apis-tools.md). Nadat ondersteuning van de steun-VM beschikbaar is, worden virtuele machines met lage prioriteit afgeschaft. deze worden gedurende ten minste twaalf maanden worden ondersteund met de huidige Api's en hulpprogram ma's van het hulp programma om voldoende tijd te hebben om de migratie naar de locatie van virtuele machines mogelijk te maken.
>
> Spot-Vm's worden alleen ondersteund voor configuratie Pools van virtuele machines. Als u gebruik wilt maken van spot-Vm's, moeten de configuratie groepen van de Cloud service worden [gemigreerd naar configuratie groepen van virtuele machines](batch-pool-cloud-service-to-virtual-machine-configuration.md).

## <a name="batch-support-for-low-priority-vms"></a>Batch ondersteuning voor Vm's met een lage prioriteit

Azure Batch biedt verschillende mogelijkheden die het gebruik van Vm's met lage prioriteit eenvoudig kunnen verbruiken en voor delen:

- Batch-Pools kunnen zowel specifieke Vm's als Vm's met lage prioriteit bevatten. Het aantal van elk type virtuele machine kan worden opgegeven wanneer een pool wordt gemaakt of op elk gewenst moment voor een bestaande pool wordt gewijzigd, met behulp van de bewerking voor expliciete verg Roten/verkleinen of het gebruik van automatisch schalen. Het verzenden van taken en taken kan ongewijzigd blijven, ongeacht de VM-typen in de groep. U kunt ook een pool zo configureren dat virtuele machines met lage prioriteit volledig worden uitgevoerd om taken zo goed mogelijk uit te voeren, maar worden toegewezen Vm's gebundeld als de capaciteit onder een minimum drempel daalt, om taken te blijven uitvoeren.
- Met batch-Pools wordt automatisch het doel nummer van Vm's met lage prioriteit verzameld. Als Vm's zijn vervallen of niet beschikbaar zijn, probeert batch de verloren capaciteit te vervangen en terug te keren naar het doel.
- Wanneer taken worden onderbroken, detecteert batch en worden taken automatisch opnieuw in de wachtrij geplaatst om opnieuw te worden uitgevoerd.
- Vm's met lage prioriteit hebben een afzonderlijke vCPU-quota die afwijkt van het quotum voor toegewezen Vm's. Het quotum voor virtuele machines met een lage prioriteit is hoger dan het quotum voor toegewezen virtuele machines, omdat virtuele machines met lage prioriteit minder kosten. Zie [quota en limieten](batch-quota-limit.md#resource-quotas)voor de batch-service voor meer informatie.

> [!NOTE]
> Vm's met lage prioriteit worden momenteel niet ondersteund voor batch-accounts die zijn gemaakt in de [modus gebruikers abonnement](accounts.md).

## <a name="considerations-and-use-cases"></a>Overwegingen en use cases

Veel batch-workloads zijn geschikt voor Vm's met een lage prioriteit. U kunt deze gebruiken wanneer taken zijn onderverdeeld in veel parallelle taken of wanneer u veel taken hebt die worden uitgeschaald en gedistribueerd over meerdere Vm's.

Enkele voor beelden van gebruiks scenario's voor batch verwerking zijn geschikt voor het gebruik van Vm's met lage prioriteit:

- **Ontwikkeling en tests**: met name als grootschalige oplossingen worden ontwikkeld, kunnen aanzienlijke besparingen worden gerealiseerd. Alle soorten tests kunnen profiteren, maar grootschalige belasting testen en regressie tests zijn geweldig.
- **Extra capaciteit op aanvraag**: virtuele machines met lage prioriteit kunnen worden gebruikt voor een aanvulling op vaste, specifieke vm's. Als dit beschikbaar is, kunnen taken worden geschaald en daarom sneller voor lagere kosten worden uitgevoerd. Als deze niet beschikbaar is, blijft de basis lijn van toegewezen Vm's beschikbaar.
- **Flexibele tijd** voor het uitvoeren van taken: als er flexibiliteit is in de tijd taken, kan de capaciteit worden toegestaan. met de toevoeging van Vm's met lage prioriteit worden vaak sneller en voor lagere kosten uitgevoerd.

Batch-Pools kunnen op een paar manieren worden geconfigureerd voor het gebruik van Vm's met lage prioriteit:

- Een pool kan alleen virtuele machines met lage prioriteit gebruiken. In dit geval herstelt batch alle afgebroken capaciteit als deze beschikbaar is. Deze configuratie is de goedkoopste manier om taken uit te voeren.
- Vm's met lage prioriteit kunnen worden gebruikt in combi natie met een vaste basis lijn van toegewezen Vm's. Het vaste aantal toegewezen Vm's garandeert dat er altijd enige capaciteit is om een taak voortgang te laten blijven.
- Een pool kan gebruikmaken van een dynamische combi natie van specifieke Vm's met een lage prioriteit, zodat de goedkope virtuele machines met lage prioriteit alleen worden gebruikt wanneer deze beschikbaar zijn, maar de volledige, toegewezen virtuele machines worden geschaald wanneer dit nodig is. Deze configuratie houdt een minimale hoeveelheid beschik bare capaciteit voor de voortgang van de taken.

Houd bij het plannen van uw gebruik van Vm's met lage prioriteit de volgende voor delen:

- Om het gebruik van de capaciteit van het surplus in azure te maximaliseren, kunnen geschikte taken worden uitgeschaald.
- Incidenteel Vm's zijn mogelijk niet beschikbaar of worden voor komen, wat resulteert in een gereduceerde capaciteit van taken en kan leiden tot onderbrekingen en opnieuw uitvoeren van taken.
- Taken met een kortere uitvoerings tijd werken meestal het beste met virtuele machines met lage prioriteit. Taken met langere taken kunnen meer worden beïnvloed als ze worden onderbroken. Als langlopende taken controle punten implementeren om de voortgang op te slaan wanneer ze worden uitgevoerd, kan dit invloed worden verminderd. 
- Langlopende MPI-taken die gebruikmaken van meerdere Vm's, zijn niet geschikt voor het gebruik van Vm's met lage prioriteit, omdat een afgeleide VM kan leiden tot de gehele taak die opnieuw moet worden uitgevoerd.
- Knoop punten met een lage prioriteit kunnen worden gemarkeerd als onbruikbaar als de [NSG-regels (netwerk beveiligings groep)](batch-virtual-network.md#network-security-groups-specifying-subnet-level-rules) onjuist zijn geconfigureerd.

## <a name="create-and-manage-pools-with-low-priority-vms"></a>Groepen maken en beheren met virtuele machines met lage prioriteit

Een batch-pool kan zowel toegewezen als virtuele machines met lage prioriteit bevatten (ook wel reken knooppunten genoemd). U kunt het doel aantal reken knooppunten instellen voor virtuele machines met een lage prioriteit. Het doel aantal knoop punten bepaalt het aantal Vm's dat u wilt hebben in de groep.

Als u bijvoorbeeld een groep wilt maken met behulp van virtuele Azure-machines (in dit geval Linux Vm's) met een doel van 5 toegewezen Vm's en 20 virtuele machines met lage prioriteit:

```csharp
ImageReference imageRef = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "16.04-LTS",
    version: "latest");

// Create the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration("batch.node.ubuntu 16.04", imageRef);

pool = batchClient.PoolOperations.CreatePool(
    poolId: "vmpool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    virtualMachineConfiguration: virtualMachineConfiguration);
```

U kunt het huidige aantal knoop punten ophalen voor zowel de virtuele machines met een lage prioriteit:

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

Groeps knooppunten hebben een eigenschap om aan te geven of het knoop punt een toegewezen virtuele machine met een lage prioriteit is:

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

Vm's kunnen af en toe worden afgebroken. Als dit het geval is, worden de taken die worden uitgevoerd op de knoop punten van de node-Vm's, opnieuw in de wachtrij geplaatst en opnieuw uitgevoerd.

Voor virtuele-machine configuratie groepen voert batch ook de volgende handelingen uit:

- De status van de virtuele machines voor de bewerkings-Vm's zijn bijgewerkt naar **vervallen**. 
- De virtuele machine wordt daad werkelijk verwijderd, waardoor alle gegevens die lokaal op de virtuele machine zijn opgeslagen, verloren gaan.
- Een bewerking voor het weer geven van een lijst met knoop punten in de groep zal nog steeds de knoop punten voor afhalen
- De groep probeert voortdurend het doel aantal knoop punten met lage prioriteit te bereiken dat beschikbaar is. Als er een vervangende capaciteit wordt gevonden, worden de Id's van de knoop punten bewaard, maar ze worden opnieuw geïnitialiseerd, waarna de status wordt **gemaakt** en **gestart** voordat ze beschikbaar zijn voor taak planning.
- Voorrangs tellingen zijn beschikbaar als een metrische waarde in het Azure Portal.

## <a name="scale-pools-containing-low-priority-vms"></a>Schaal groepen met virtuele machines met lage prioriteit schalen

Net als bij Pools die uitsluitend bestaan uit specifieke virtuele machines, is het mogelijk een groep met virtuele machines met lage prioriteit te schalen door de methode Resize aan te roepen of door automatisch schalen te gebruiken.

De bewerking voor het wijzigen van de pool heeft een tweede optionele para meter waarmee de waarde van **targetLowPriorityNodes** wordt bijgewerkt:

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

De formule voor het automatisch inschalen van de pool ondersteunt virtuele machines met lage prioriteit als volgt:

- U kunt de waarde van de door de service gedefinieerde variabele **$TargetLowPriorityNodes** ophalen of instellen.
- U kunt de waarde van de door de service gedefinieerde variabele ophalen **$CurrentLowPriorityNodes**.
- U kunt de waarde van de door de service gedefinieerde variabele ophalen **$PreemptedNodeCount**. Deze variabele retourneert het aantal knoop punten met de status in afstel en u kunt het aantal toegewezen knoop punten omhoog of omlaag schalen, afhankelijk van het aantal knoop punten dat niet beschikbaar is.

## <a name="configure-jobs-and-tasks"></a>Taken en taken configureren

Voor taken en taken is weinig extra configuratie vereist voor knoop punten met een lage prioriteit. Houd rekening met het volgende:

- De eigenschap JobManagerTask van een taak heeft een eigenschap **AllowLowPriorityNode** . Als deze eigenschap is ingesteld op True, kan taak beheer worden gepland op een knoop punt met een specifieke of lage prioriteit. Als deze eigenschap False is, wordt de taak taak beheer alleen gepland voor een specifiek knoop punt.
- De [omgevings variabele](batch-compute-node-environment-variables.md) AZ_BATCH_NODE_IS_DEDICATED is beschikbaar voor een taak toepassing, zodat u kunt bepalen of deze wordt uitgevoerd met een lage prioriteit of op een toegewezen knoop punt.

## <a name="view-metrics-for-low-priority-vms"></a>Metrische gegevens weer geven voor virtuele machines met lage prioriteit

Er zijn nieuwe metrische gegevens beschikbaar in de [Azure Portal](https://portal.azure.com) voor knoop punten met een lage prioriteit. Deze metrische gegevens zijn:

- Aantal Low-Priority knoop punten
- Low-Priority aantal kernen
- Aantal knoop punten in herhaling

Deze metrische gegevens weer geven in de Azure Portal

1. Ga in Azure Portal naar uw Batch-account.
2. Selecteer **metrische gegevens** in de sectie **bewaking** .
3. Selecteer de gewenste metrische gegevens in de lijst **metrische gegevens** .

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [Werkstroom van de batch-service en primaire resources](batch-service-workflow-features.md) als pools, knooppunten, jobs en taken.
- Meer informatie over de [Batch-API's en -hulpprogramma's](batch-apis-tools.md) die beschikbaar zijn voor het bouwen van Batch-oplossingen.
- Begin met het plannen van de overstap van Vm's met lage prioriteit om virtuele machines te plaatsen. Als u virtuele machines met lage prioriteit gebruikt met configuratie groepen voor **Cloud Services** , moet u in plaats daarvan migreren naar [ **virtuele-machine configuratie** groepen](nodes-and-pools.md#configurations) .
