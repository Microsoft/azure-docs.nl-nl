---
title: Overzicht van container Insights | Microsoft Docs
description: In dit artikel wordt container Insights beschreven waarmee de AKS container Insights-oplossing en de waarde die wordt geleverd, worden bewaakt door de status van uw AKS-clusters en Container Instances in azure te controleren.
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: a29890f569c47c48aae7d5d61badee7edeef58a0
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102441513"
---
# <a name="container-insights-overview"></a>Overzicht van container Insights

Container Insights is een functie die is ontworpen voor het bewaken van de prestaties van container werkbelastingen die zijn geïmplementeerd op:

- Managed Kubernetes-clusters die worden gehost op de [Azure Kubernetes-service (AKS)](../../aks/intro-kubernetes.md)
- Zelf beheerde Kubernetes-clusters die worden gehost op Azure met behulp van de [AKS-engine](https://github.com/Azure/aks-engine)
- [Azure Container Instances](../../container-instances/container-instances-overview.md)
- Zelf beheerde Kubernetes-clusters die worden gehost op [Azure stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) of on-premises
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)
- [Kubernetes voor Azure-Arc ingeschakeld](../../azure-arc/kubernetes/overview.md) (preview-versie)

Container Insights ondersteunt clusters met het besturings systeem Linux en Windows Server 2019. De container runtimes die worden ondersteund, zijn docker, Moby en een CRI compatibele runtime, zoals CRI-O en Containered.

Het bewaken van uw containers is van cruciaal belang, vooral wanneer u een productie cluster op schaal uitvoert met meerdere toepassingen.

Met container Insights krijgt u inzicht in de prestaties door geheugen-en processor metrieken te verzamelen van controllers, knoop punten en containers die beschikbaar zijn in Kubernetes via de API voor metrische gegevens. Er worden ook containerlogboeken verzameld.  Nadat u bewaking vanuit Kubernetes-clusters hebt ingeschakeld, worden metrische gegevens en logboeken automatisch voor u verzameld via een container versie van de Log Analytics-agent voor Linux. Metrische gegevens worden naar de metrische opslag opgeslagen en logboeken worden geschreven naar de logboeken archief die aan uw [log Analytics](../logs/log-query-overview.md) -werk ruimte is gekoppeld.

![Architectuur van container Insights](./media/container-insights-overview/azmon-containers-architecture-01.png)

## <a name="what-does-container-insights-provide"></a>Wat biedt container Insights?

Container Insights biedt een uitgebreide bewakings ervaring met behulp van verschillende functies van Azure Monitor. Met deze functies kunt u inzicht krijgen in de prestaties en status van uw Kubernetes-cluster met Linux-en Windows Server 2019-besturings systeem en de container werkbelastingen. U kunt met behulp van container Insights het volgende doen:

* Identificeer AKS-containers die worden uitgevoerd op het knoop punt en hun gemiddelde processor-en geheugen gebruik. Deze kennis kan u helpen bij het identificeren van knel punten in de resource.
* Identificeer het processor-en geheugen gebruik van container groepen en de containers die worden gehost in Azure Container Instances.
* Bepaal waar de container zich bevindt in een controller of een pod. Deze kennis kan u helpen de algehele prestaties van de controller of het Pod te bekijken.
* Bekijk het resource gebruik van werk belastingen die op de host worden uitgevoerd en die geen verband houden met de standaard processen die ondersteuning bieden voor de pod.
* Meer informatie over het gedrag van het cluster in gemiddelde en zwaarste belasting. Deze kennis kan u helpen de capaciteits behoeften te identificeren en de maximale belasting te bepalen die het cluster kan vastlopen.
* Configureer waarschuwingen om u proactief te informeren of op te nemen wanneer het CPU-en geheugen gebruik op knoop punten of containers de drempel waarden overschrijdt, of wanneer er een status wijziging in het cluster optreedt bij het samen vouwen van de infra structuur of de knooppunt status.
* Integreer met [Prometheus](https://prometheus.io/docs/introduction/overview/) om de metrische gegevens van de toepassing en werk belasting weer te geven die worden verzameld van knoop punten en Kubernetes met behulp van [query's](container-insights-log-search.md) om aangepaste waarschuwingen, Dash boards en gedetailleerde gedetailleerde analyses te maken.
* Bewaak de werkbelastingen van containers [die zijn geïmplementeerd](https://github.com/Azure/aks-engine) op de AKS-engine on-premises en AKS- [engine op Azure stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview).
* Bewaak de werk belasting van containers [die zijn geïmplementeerd in azure Red Hat open Shift](../../openshift/intro-openshift.md).

    >[!NOTE]
    >De ondersteuning voor Azure Red Hat open Shift is op dit moment een functie in Public Preview.
    >

* Bewaak de werkbelastingen van containers [die zijn geïmplementeerd in azure Arc enabled Kubernetes (preview)](../../azure-arc/kubernetes/overview.md).

De belangrijkste verschillen in het bewaken van een Windows Server-cluster in vergelijking met een Linux-cluster zijn als volgt:

- Windows heeft geen geheugen-RSS-metriek en is daarom niet beschikbaar voor Windows-knoop punten en-containers. De metrische [werkset](/windows/win32/memory/working-set) is beschikbaar.
- Informatie over capaciteit van schijf opslag is niet beschikbaar voor Windows-knoop punten.
- Alleen pod omgevingen worden bewaakt, niet-docker-omgevingen.
- Met de preview-versie worden Maxi maal 30 Windows Server-containers ondersteund. Deze beperking is niet van toepassing op Linux-containers.

Bekijk de volgende video over een dieper niveau waarmee u meer informatie kunt over het controleren van uw AKS-cluster met behulp van container Insights.

> [!VIDEO https://www.youtube.com/embed/XEdwGvS2AwA]

## <a name="how-do-i-access-this-feature"></a>Hebt u toegang tot deze functie Hoe kan ik?

U kunt toegang krijgen tot container Insights twee manieren, van Azure Monitor of rechtstreeks vanuit het geselecteerde AKS-cluster. Vanuit Azure Monitor hebt u een wereld wijd perspectief van alle geïmplementeerde containers, die worden gecontroleerd en die niet, zodat u uw abonnementen en resource groepen kunt zoeken en filteren, en vervolgens inzoomt op container Insights vanuit de geselecteerde container.  Als dat niet het geval is, kunt u de functie rechtstreeks vanuit een geselecteerde AKS-container openen via de pagina AKS.

![Overzicht van methoden voor toegang tot container inzichten](./media/container-insights-overview/azmon-containers-experience.png)

Als u geïnteresseerd bent in het bewaken en beheren van uw docker-en Windows-container hosts die buiten AKS worden uitgevoerd om de configuratie, controle en het gebruik van bronnen weer te geven, raadpleegt u de [container bewakings oplossing](./containers.md).

## <a name="next-steps"></a>Volgende stappen

Als u wilt beginnen met het bewaken van uw Kubernetes-cluster, raadpleegt u [hoe u container Insights kunt gebruiken](container-insights-onboard.md) om de vereisten en beschik bare methoden te begrijpen voor het inschakelen van
