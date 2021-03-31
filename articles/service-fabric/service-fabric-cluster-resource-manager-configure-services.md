---
title: Metrische gegevens en plaatsings instellingen opgeven
description: Meer informatie over het beschrijven van een Service Fabric-service door metrische gegevens, plaatsings beperkingen en andere plaatsings beleidsregels op te geven.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: d4dcd319000edb204ba188ed14b4c797dba5cd38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75610094"
---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>Instellingen van cluster resource manager voor Service Fabric Services configureren
Met de Service Fabric cluster resource manager kunt u de regels die van toepassing zijn op elke afzonderlijke benoemde service, nauw keurig beheren. Elke benoemde service kan regels opgeven voor de manier waarop deze in het cluster moet worden toegewezen. Elke benoemde service kan ook de set metrische gegevens definiëren die ze wil rapporteren, met inbegrip van het belang van die service. Het configureren van services onderbreekt in drie verschillende taken:

1. Plaatsings beperkingen configureren
2. Metrische gegevens configureren
3. Geavanceerd plaatsings beleid en andere regels configureren (minder algemeen)

## <a name="placement-constraints"></a>Plaatsings beperkingen
Plaatsings beperkingen worden gebruikt om te bepalen welke knoop punten in het cluster waarop een service daad werkelijk kan worden uitgevoerd. Meestal een specifiek benoemd service-exemplaar of alle services van een bepaald type die zijn beperkt om te worden uitgevoerd op een bepaald type knoop punt. Plaatsings beperkingen zijn uitbreidbaar. U kunt elke set eigenschappen per knooppunt type definiëren en deze vervolgens met beperkingen selecteren bij het maken van services. U kunt ook de plaatsings beperkingen van een service wijzigen terwijl deze wordt uitgevoerd. Hiermee kunt u reageren op wijzigingen in het cluster of aan de vereisten van de service. De eigenschappen van een bepaald knoop punt kunnen ook dynamisch worden bijgewerkt in het cluster. Meer informatie over plaatsings beperkingen en hoe u deze kunt configureren, vindt u in [dit artikel](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

## <a name="metrics"></a>Metrische gegevens
Metrische gegevens zijn de set resources die een bepaalde benoemde service nodig heeft. De metrische configuratie van een service omvat het aantal resources dat elke stateful replica of stateless exemplaar van die service standaard verbruikt. Metrische gegevens bestaan ook uit een gewicht waarmee wordt aangegeven hoe belang rijk is dat de metrische gegevens naar die service worden gebalanceerd. voor het geval zijn er afwegingen nood zakelijk.

## <a name="advanced-placement-rules"></a>Geavanceerde regels voor plaatsing
Er zijn andere typen plaatsings regels die nuttig zijn in minder algemene scenario's. Een aantal voorbeelden:
- Beperkingen bij geografisch gedistribueerde clusters
- Bepaalde toepassings architecturen

Andere plaatsings regels worden geconfigureerd via correlaties of beleids regels.

## <a name="next-steps"></a>Volgende stappen
- Metrische gegevens zijn de manier waarop de Service Fabric cluster resource manager het verbruik en de capaciteit in het cluster beheert. Raadpleeg [dit artikel](service-fabric-cluster-resource-manager-metrics.md) voor meer informatie over metrische gegevens en hoe u deze kunt configureren.
- Affiniteit is een modus die u voor uw services kunt configureren. Het is niet gebruikelijk, maar als u dit nodig hebt, kunt u [hier](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) meer informatie hierover vinden
- Er zijn veel verschillende plaatsings regels die kunnen worden geconfigureerd voor uw service voor het afhandelen van extra scenario's. U vindt [hier](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) meer informatie over deze verschillende plaatsings beleidsregels
- Begin vanaf het begin en [krijg een inleiding tot de service Fabric cluster resource manager](service-fabric-cluster-resource-manager-introduction.md)
- Raadpleeg het artikel over het [verdelen](service-fabric-cluster-resource-manager-balancing.md) van de taken in het cluster voor meer informatie over hoe de cluster resource manager de belasting beheert en balanceert.
- Cluster resource manager heeft veel opties voor het beschrijven van het cluster. Lees dit artikel over [het beschrijven van een service Fabric cluster](service-fabric-cluster-resource-manager-cluster-description.md) voor meer informatie.
