---
title: Concepten-duurzame software techniek in azure Kubernetes Services (AKS)
description: Meer informatie over duurzame software engineering in azure Kubernetes service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: 700723041855fdae4f994480d180292fdfd12e15
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97346891"
---
# <a name="sustainable-software-engineering-principles-in-azure-kubernetes-service-aks"></a>Duurzame software techniek Principles in azure Kubernetes service (AKS)

De methoden van duurzame software techniek zijn een set competenties die u helpen bij het definiëren, bouwen en uitvoeren van duurzame toepassingen. Het algemene doel is het verminderen van de carbon footprint van elk aspect van uw toepassing. [De beginselen van duurzame software techniek][principles-sse] hebben een overzicht van de principes van duurzame software techniek.

Een belang rijk idee van een duurzame software techniek is dat het een verschuiving in prioriteiten is en de focus heeft. In veel gevallen is software ontworpen en uitgevoerd op een manier die zich richt op snelle prestaties en een lage latentie. Duurzame software techniek richt zich op het verminderen van zoveel mogelijk kool emissies. In sommige gevallen kan het Toep assen van duurzame software techniek leiden tot snellere prestaties of een lagere latentie, zoals bij het verlagen van de totale netwerk reis. In andere gevallen kan het verminderen van de uitstoot van emissies leiden tot tragere prestaties of een grotere latentie, zoals het vertragen van het uitstellen van werk belastingen met een lage prioriteit. Voordat u overweegt de ontwikkeling van duurzame software techniek toe te passen op uw toepassing, kunt u de prioriteiten, behoeften en de berekenings verhouding van uw toepassing controleren.

## <a name="measure-and-optimize"></a>Meten en optimaliseren

Als u de carbon footprint van uw AKS-clusters wilt verlagen, moet u weten hoe de resources van uw cluster worden gebruikt. [Azure monitor][azure-monitor] bevat details over het resource gebruik van uw cluster, zoals geheugen en CPU-gebruik. Deze gegevens kunnen bijdragen aan uw beslissingen om de carbon footprint van uw cluster te verminderen en het effect van uw wijzigingen te bekijken. U kunt ook de [micro soft duurzaamheids Calculator][sustainability-calculator] installeren om de carbon footprint van al uw Azure-resources te bekijken.

## <a name="increase-resource-utilization"></a>Resource gebruik verg Roten

Een manier om de carbon footprint te verlagen, is om de duur van de reken resources te verminderen. Als u uw tijd inactief maakt, moet u het gebruik van uw reken resources verg Roten. Als u bijvoorbeeld vier knoop punten in uw cluster hebt, die elke vier van de capaciteit van 50% hebben, is er een ongebruikte capaciteit van 50% voor het resterende aantal inactief. Als u het cluster naar drie knoop punten hebt gereduceerd, wordt de drie knoop punten uitgevoerd met een capaciteit van 67%, waardoor de ongebruikte capaciteit op elk knoop punt wordt verminderd tot 33% en het gebruik ervan wordt verhoogd.

> [!IMPORTANT]
> Als u overweegt om wijzigingen aan te brengen in de resources in uw cluster, controleert u of uw [systeem groepen][system-pools] voldoende bronnen hebben om de stabiliteit van de kern systeem onderdelen van uw cluster te behouden. Verminder nooit de resources van uw cluster tot het punt waar het cluster Insta Biel kan worden.

Nadat u het gebruik van het cluster hebt bekeken, kunt u overwegen de functies te gebruiken die worden aangeboden door [meerdere knooppunt Pools][multiple-node-pools]. U kunt [knooppunt grootte][node-sizing] gebruiken om knooppunt groepen met specifieke CPU-en geheugen profielen te definiëren, zodat u uw knoop punten kunt aanpassen aan de behoeften van uw werk belasting. Door de grootte van uw knoop punten aan uw werkbelasting vereisten te wijzigen, kunt u een aantal knoop punten met een hoger gebruik uitvoeren. U kunt ook configureren hoe uw cluster wordt [geschaald][scale] en de [horizontale pod autoscaler][scale-horizontal] en de cluster-automatische [schaal][scale-auto] functie gebruiken om uw cluster automatisch te schalen op basis van uw configuratie. U kunt bepalen hoe uw cluster kan worden geschaald, zodat u al uw knoop punten met een hoog gebruik houdt, terwijl u de werk belasting van uw cluster wijzigt. In gevallen waarin een werk belasting tolerant is om onverwachte onderbrekingen of beëindigingen te voor komen, kunt u gebruik maken van [Spot groepen][spot-pools] om te profiteren van niet-actieve capaciteit in Azure. Bijvoorbeeld, spot groepen kunnen goed werken voor batch taken of ontwikkel omgevingen.

Het verg Roten van het gebruik kan ook overtollige knoop punten verminderen, waardoor de energie die wordt verbruikt door [resource reserveringen op elk knoop punt][resource-reservations]vermindert.

Bekijk ook de CPU-en geheugen *aanvragen* en *limieten* in de Kubernetes-manifesten van uw toepassingen. Als u de waarden voor geheugen en CPU verlaagt, zijn er meer geheugen en CPU beschikbaar voor het cluster om andere workloads uit te voeren. Naarmate u meer werk belastingen met lagere CPU en geheugen uitvoert, wordt uw cluster meer flexibel toegewezen, waardoor uw gebruik wordt verg root. Wanneer u de CPU en het geheugen voor uw toepassingen verlaagt, kan het gedrag van uw toepassingen worden verslechterd of Insta Biel als u deze waarden te laag instelt. Voordat u de CPU-en geheugen *aanvragen* en *limieten* wijzigt, kunt u overwegen een aantal benchmark tests uit te voeren om te begrijpen of deze waarden juist zijn ingesteld. U hoeft deze waarden bovendien nooit te verlagen tot het punt wanneer uw toepassing Insta Biel wordt.

## <a name="reduce-network-travel"></a>Netwerk reizen verminderen

Het verminderen van de afstands aanvragen en de reacties van en naar uw cluster zijn doorgaans minder energie verbruikt door netwerk apparaten en vermindert de uitstoot van kool. Nadat u uw netwerk verkeer hebt bekeken, kunt u overwegen clusters te maken [in regio's][regions] dichter bij de bron van uw netwerk verkeer. U kunt ook [Azure Traffic Manager][azure-traffic-manager] gebruiken om het routeren van verkeer naar de dichtstbijzijnde cluster en [proximity placement groups][proiximity-placement-groups] te helpen de afstand tussen Azure-resources te verminderen.

> [!IMPORTANT]
> Als u overweegt wijzigingen aan te brengen in het netwerk van uw cluster, vermindert u nooit netwerk reizen tegen de kosten van de vereisten voor de werk belasting van de vergadering. Als u bijvoorbeeld [beschikbaarheids zones][availability-zones] gebruikt, wordt er meer netwerk verkeer op uw cluster uitgevoerd, maar het gebruik van deze functie kan nodig zijn om de werkbelasting vereisten te verwerken.

## <a name="demand-shaping"></a>Demand Shaping

Indien mogelijk moet u de vraag naar de resources van uw cluster verplaatsen naar tijden of regio's waar u overtollige capaciteit kunt gebruiken. U kunt bijvoorbeeld de tijd of regio voor een batch-taak wijzigen om uit te voeren of gebruik te gaan van [Spot groepen][spot-pools]. Houd ook rekening met het herstructureren van uw toepassing voor het gebruik van een wachtrij om actieve werk belastingen uit te stellen waarvoor geen directe verwerking nodig is.

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
[azure-monitor]: ../azure-monitor/insights/container-insights-overview.md
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