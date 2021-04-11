---
title: Concepten-duurzame software techniek in azure Kubernetes Services (AKS)
description: Meer informatie over duurzame software engineering in azure Kubernetes service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 03/29/2021
ms.openlocfilehash: c43c65dfa2f3930510bd59aaa24c798525bd691b
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011488"
---
# <a name="sustainable-software-engineering-principles-in-azure-kubernetes-service-aks"></a>Duurzame software techniek Principles in azure Kubernetes service (AKS)

De methoden van duurzame software techniek zijn een set competenties die u helpen bij het definiëren, bouwen en uitvoeren van duurzame toepassingen. Het algemene doel is het verminderen van de carbon footprint van elk aspect van uw toepassing. [De beginselen van duurzame software techniek][principles-sse] hebben een overzicht van de principes van duurzame software techniek.

Duurzame software techniek is een verschuiving in prioriteiten en focussen. In veel gevallen is de manier waarop de meeste software is ontworpen en uitgevoerd, snelle prestaties en lage latentie. Ondertussen is duurzame software techniek gericht op het verminderen van zoveel mogelijk kool-emissie. Overweeg het volgende:

* Het Toep assen van duurzame software techniek kan u snellere prestaties of een lagere latentie geven, bijvoorbeeld door de totale netwerk reis te verlagen. 
* Het verminderen van de uitstoot van kool kan leiden tot tragere prestaties of verhoogde latentie, zoals het vertragen van werk belastingen met een lage prioriteit. 

Voordat u duurzame software techniek-principes toepast op uw toepassing, kunt u de prioriteiten, behoeften en de berekenings verhouding van uw toepassing controleren.

## <a name="measure-and-optimize"></a>Meten en optimaliseren

Als u de carbon footprint van uw AKS-clusters wilt verlagen, moet u weten hoe de resources van uw cluster worden gebruikt. [Azure monitor][azure-monitor] bevat details over het resource gebruik van uw cluster, zoals geheugen en CPU-gebruik. Met deze gegevens wordt uw beslissing geïnformeerd over het beperken van de carbon footprint van uw cluster en wordt het effect van uw wijzigingen naleven. 

U kunt ook de [micro soft duurzaamheids Calculator][sustainability-calculator] installeren om de carbon footprint van al uw Azure-resources te bekijken.

## <a name="increase-resource-utilization"></a>Resource gebruik verg Roten

Een manier om de carbon footprint te verlagen, is om de niet-actieve tijd te verminderen. Als u uw tijd inactief maakt, moet u het gebruik van uw reken resources verg Roten. Bijvoorbeeld:
1. U hebt vier knoop punten in uw cluster, elk met een capaciteit van 50%. Daarom hebben alle vier van uw knoop punten een ongebruikte capaciteit van 50% resterend inactief. 
1. U hebt het cluster gereduceerd tot drie knoop punten, elk met een capaciteit van 67% met dezelfde werk belasting. U hebt de ongebruikte capaciteit op elk knoop punt tot 33% verminderd en uw gebruik verg root.

> [!IMPORTANT]
> Als u overweegt om de resources in uw cluster te wijzigen, controleert u of uw [systeem groepen][system-pools] voldoende bronnen hebben om de stabiliteit van de kern systeem onderdelen van uw cluster te behouden. Verminder **nooit** de resources van uw cluster tot het punt waar het cluster Insta Biel kan worden.

Nadat u het gebruik van het cluster hebt bekeken, kunt u overwegen de functies te gebruiken die worden aangeboden door [meerdere knooppunt groepen][multiple-node-pools]: 

* Knooppunt grootte

    Gebruik [knooppunt grootte][node-sizing] om knooppunt groepen met specifieke CPU-en geheugen profielen te definiëren, zodat u uw knoop punten kunt aanpassen aan de behoeften van uw werk belasting. Als u de grootte van uw knoop punten instelt op de behoeften van uw werk belasting, kunt u een paar knoop punten uitvoeren met een hoger gebruik. 

* Clusterschaling

    Configureer hoe het cluster wordt [geschaald][scale]. Gebruik de [horizontale pod autoscaler][scale-horizontal] en de [cluster-automatische schaalr][scale-auto] om uw cluster automatisch te schalen op basis van uw configuratie. Bepaal hoe uw cluster wordt geschaald om ervoor te zorgen dat alle knoop punten die op een hoog gebruik worden uitgevoerd, tegelijk worden gesynchroniseerd met wijzigingen in de werk belasting van uw cluster. 

* Spot groepen

    In gevallen waarin een werk belasting tolerant is om onverwachte onderbrekingen of beëindigingen te voor komen, kunt u gebruikmaken van [Spot groepen][spot-pools]. Spot groepen maken gebruik van niet-actieve capaciteit binnen Azure. Bijvoorbeeld, spot groepen kunnen goed werken voor batch taken of ontwikkel omgevingen.

> [!NOTE]
>Het verg Roten van het gebruik kan ook overtollige knoop punten verminderen, waardoor de energie die wordt verbruikt door [resource reserveringen op elk knoop punt][resource-reservations]vermindert.

Controleer ten slotte de CPU-en geheugen *aanvragen* en *limieten* in de Kubernetes-manifesten van uw toepassingen. 
* Naarmate u minder geheugen en CPU-waarden hebt, zijn er meer geheugen en CPU beschikbaar voor het cluster om andere workloads uit te voeren. 
* Naarmate u meer werk belastingen met lagere CPU en geheugen uitvoert, wordt uw cluster meer flexibel toegewezen, waardoor uw gebruik wordt verg root. 

Bij het verminderen van de CPU en het geheugen voor uw toepassingen, kan het gedrag van uw toepassingen worden verslechterd of Insta Biel als u de CPU-en geheugen waarden te laag instelt. Voordat u de CPU-en geheugen *aanvragen* en *limieten* wijzigt, voert u een aantal benchmark tests uit om te controleren of de waarden op de juiste wijze zijn ingesteld. Verlaag deze waarden nooit tot het punt van instabiliteit van de toepassing.

## <a name="reduce-network-travel"></a>Netwerk reizen verminderen

Door het verminderen van aanvragen en reacties van de reis afstand naar en van uw cluster kunt u de uitstoot van Carbon emissies en elektriciteit door netwerk apparaten reduceren. Nadat u uw netwerk verkeer hebt bekeken, kunt u overwegen clusters te maken [in regio's][regions] dichter bij de bron van uw netwerk verkeer. U kunt [Azure Traffic Manager][azure-traffic-manager] gebruiken om verkeer door te sturen naar de dichtstbijzijnde cluster-en [proximity-plaatsings groepen][proiximity-placement-groups] en de afstand tussen Azure-resources te verminderen.

> [!IMPORTANT]
> Als u overweegt wijzigingen aan te brengen in het netwerk van uw cluster, vermindert u nooit netwerk reizen tegen de kosten van de vereisten voor de werk belasting van de vergadering. Als er bijvoorbeeld [beschikbaarheids zones][availability-zones] worden gebruikt in uw cluster, kunnen er beschikbaarheids zones nodig zijn om de werkbelasting vereisten te verwerken.

## <a name="demand-shaping"></a>Demand Shaping

Indien mogelijk moet u de vraag naar de resources van uw cluster verplaatsen naar tijden of regio's waar u overtollige capaciteit kunt gebruiken. U kunt bijvoorbeeld het volgende overwegen:
* De tijd of regio voor het uitvoeren van een batch-taak wijzigen.
* Gebruik van [Spot groepen][spot-pools]. 
* Herstructureer uw toepassing voor het gebruik van een wachtrij om actieve werk belastingen uit te stellen waarvoor geen directe verwerking nodig is.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de functies van AKS die in dit artikel worden genoemd:

* [Meerdere knooppunt groepen][multiple-node-pools]
* [Knooppunt grootte][node-sizing]
* [Een cluster schalen][scale]
* [Horizontale automatische schaalaanpassing van pods][scale-horizontal]
* [Automatische schaalaanpassing van clusters][scale-auto]
* [Spot groepen][spot-pools]
* [Systeem groepen][system-pools]
* [Resource reserveringen][resource-reservations]
* [Nabijheidsplaatsingsgroepen][proiximity-placement-groups]
* [Beschikbaarheidszones][availability-zones]

[availability-zones]: availability-zones.md
[azure-monitor]: ../azure-monitor/containers/container-insights-overview.md
[azure-traffic-manager]: ../traffic-manager/traffic-manager-overview.md
[proiximity-placement-groups]: reduce-latency-ppg.md
[regions]: faq.md#which-azure-regions-currently-provide-aks
[resource-reservations]: concepts-clusters-workloads.md#resource-reservations
[scale]: concepts-scale.md
[scale-auto]: concepts-scale.md#cluster-autoscaler
[scale-horizontal]: concepts-scale.md#horizontal-pod-autoscaler
[spot-pools]: spot-node-pool.md
[multiple-node-pools]: use-multiple-node-pools.md
[node-sizing]: use-multiple-node-pools.md#specify-a-vm-size-for-a-node-pool
[sustainability-calculator]: https://azure.microsoft.com/blog/microsoft-sustainability-calculator-helps-enterprises-analyze-the-carbon-emissions-of-their-it-infrastructure/
[system-pools]: use-system-pools.md
[principles-sse]: https://docs.microsoft.com/learn/modules/sustainable-software-engineering-overview/