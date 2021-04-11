---
title: Cluster uitbreidingen-Azure Arc enabled Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Dit artikel bevat een conceptueel overzicht van de functionaliteit voor cluster uitbreidingen van Azure Arc enabled Kubernetes
ms.openlocfilehash: 4b9a3991b51ec45e7a64acd546c031d4241c97af
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451048"
---
# <a name="cluster-extensions-on-azure-arc-enabled-kubernetes"></a>Cluster uitbreidingen op Azure Arc enabled Kubernetes

[Helm-grafieken](https://helm.sh/) helpen u bij het beheren van Kubernetes-toepassingen door de bouw stenen te bieden die nodig zijn om zelfs de meest complexe Kubernetes-toepassingen te definiëren, te installeren en bij te werken. De functie cluster uitbreiding zoekt op de verpakkings onderdelen van helm. Dit doet u door een Azure Resource Manager gerichte ervaring te bieden voor de installatie en het levenscyclus beheer van cluster uitbreidingen, zoals Azure Monitor en Azure Defender voor Kubernetes. De functie cluster uitbreidingen biedt de volgende extra voor delen ten opzichte van en boven wat al beschikbaar is met helm-grafieken:

- Bekijk een inventarisatie van alle clusters en de uitbrei dingen die op deze clusters zijn geïnstalleerd.
- Gebruik Azure Policy om de implementatie van cluster uitbreidingen op schaal te automatiseren.
- Abonneer u op de release-treinen van elke extensie.
- Automatische upgrade voor uitbrei dingen instellen.
- Ondersteuning voor het maken van een uitbrei ding van het extensie-exemplaar en het beheer van de levens cyclus van updates en verwijderingen.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>Architectuur

[![Architectuur ](./media/conceptual-extensions.png) van cluster extensies](./media/conceptual-extensions.png#lightbox)

Het cluster extensie-exemplaar is gemaakt als een uitbrei ding Azure Resource Manager resource ( `Microsoft.KubernetesConfiguration/extensions` ) boven op de Azure-Kubernetes-resource (vertegenwoordigd door `Microsoft.Kubernetes/connectedClusters` ) in azure Resource Manager. Met de weer gave in Azure Resource Manager kunt u een beleid ontwerpen dat controleert op alle Azure-Arc-Kubernetes-resources met of zonder een specifieke cluster extensie. Wanneer u hebt vastgesteld welke clusters geen cluster uitbreidingen hebben met gewenste eigenschaps waarden, kunt u deze niet-compatibele resources herstellen met behulp van Azure Policy.

De `config-agent` uitvoering in uw cluster houdt nieuwe of bijgewerkte uitbreidings resources bij op de Azure-Kubernetes-resource. De `extensions-manager` uitvoering in uw cluster haalt de helm-grafiek op uit Azure container Registry of micro soft container Registry en installeert deze in het cluster. 

Zowel de `config-agent` als- `extensions-manager` onderdelen die worden uitgevoerd in de cluster ingang, versie-updates en het verwijderen van extensie-exemplaren.

> [!NOTE]
> * `config-agent` monitors voor nieuwe of bijgewerkte uitbreidings bronnen die beschikbaar zijn op de Kubernetes-resource die is ingeschakeld voor Arc. Daarom vereisen agents connectiviteit voor de gewenste status om naar het cluster te worden getrokken. Als agents geen verbinding kunnen maken met Azure, wordt het door geven van de gewenste status naar het cluster uitgesteld.
> * Beveiligde configuratie-instellingen voor een extensie worden Maxi maal 48 uur opgeslagen in de Azure Arc enabled Kubernetes-Services. Als het cluster niet meer is verbonden tijdens de 48 uur nadat de extensie resource is gemaakt in azure, wordt de extensie overgezet van een `Pending` status naar `Failed` State. Wij adviseren de clusters zo vaak als mogelijk online te brengen.

## <a name="next-steps"></a>Volgende stappen

* Gebruik onze Snelstartgids om [een Kubernetes-cluster te verbinden met Azure Arc](./quickstart-connect-cluster.md).
* [Implementeer cluster uitbreidingen](./extensions.md) op uw Azure Arc enabled Kubernetes-cluster.