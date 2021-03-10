---
title: Aanbevolen procedures voor de operator-container installatie kopie beheer in azure Kubernetes Services (AKS)
description: Meer informatie over de aanbevolen procedures voor cluster operators voor het beheren en beveiligen van container installatie kopieën in azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 1d2f5465356a94b9ad7014e75aa6fe1515411a81
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564914"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Aanbevolen procedures voor het beheer van container installatie kopieën en beveiliging in azure Kubernetes service (AKS)

Wanneer u toepassingen ontwikkelt en uitvoert in azure Kubernetes service (AKS), is de beveiliging van uw containers en container installatie kopieën een belang rijke overweging. Containers met verouderde basis installatie kopieën of niet-patchde toepassings Runtimes veroorzaken een beveiligings risico en mogelijke aanvals vector. Als u deze Risico's wilt minimaliseren, integreert u hulpprogram ma's waarmee u problemen in uw containers tijdens het bouwen en tijdens de uitvoering kunt onderzoeken en oplossen. Het eerder in het proces dat het beveiligings probleem of de verouderde basis installatie kopie wordt onderschept, wordt het cluster beter beveiligd. In dit artikel worden zowel de container installatie kopieën *die zijn opgeslagen* in een container register als de actieve containers.

Dit artikel is gericht op het beveiligen van uw containers in AKS. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Beveiligings problemen scannen en oplossen
> * Container installatie kopieën automatisch activeren en opnieuw implementeren wanneer een basis installatie kopie wordt bijgewerkt

U kunt ook de aanbevolen procedures voor [cluster beveiliging][best-practices-cluster-security] en voor [pod-beveiliging][best-practices-pod-security]lezen.

U kunt ook [container beveiliging in Security Center][security-center-containers] gebruiken om uw containers te helpen scannen op beveiligings problemen.  Er is ook [Azure container Registry integratie][security-center-acr] met Security Center om uw installatie kopieën en het REGI ster te beschermen tegen beveiligings problemen.

## <a name="secure-the-images-and-run-time"></a>De installatie kopieën en uitvoerings tijd beveiligen

**Richt lijnen voor best practices** : scan de container installatie kopieën op beveiligings problemen en implementeer alleen installatie kopieën die zijn geslaagd voor validatie. Werk de basis installatie kopieën en runtime van de toepassing regel matig bij en implementeer vervolgens de werk belastingen in het AKS-cluster.

Een probleem met de invoering van op containers gebaseerde workloads is het verifiëren van de beveiliging van installatie kopieën en runtime die wordt gebruikt om uw eigen toepassingen te bouwen. Hoe zorgt u ervoor dat u geen beveiligings problemen in uw implementaties introduceert? De implementatie werk stroom moet een proces bevatten voor het scannen van container installatie kopieën met behulp van hulpprogram ma's zoals [Twistlock][twistlock] of [zeeblauw][aqua]. vervolgens mogen alleen geverifieerde installatie kopieën worden geïmplementeerd.

![Container installatie kopieën scannen en herstellen, valideren en implementeren](media/operator-best-practices-container-security/scan-container-images-simplified.png)

In een praktijk voorbeeld kunt u een pijp lijn voor continue integratie en continue implementatie (CI/CD) gebruiken om de scans, verificatie en implementaties van de installatie kopie te automatiseren. Azure Container Registry bevat deze mogelijkheden voor het scannen van beveiligings problemen.

## <a name="automatically-build-new-images-on-base-image-update"></a>Automatisch nieuwe installatie kopieën bouwen bij het bijwerken van de basis installatie kopie

**Richt lijnen voor best practices** : als u basis installatie kopieën voor toepassings installatie kopieën gebruikt, moet u Automation gebruiken om nieuwe installatie kopieën te bouwen wanneer de basis installatie kopie wordt bijgewerkt. Aangezien deze basis installatie kopieën doorgaans beveiligings oplossingen bevatten, moet u eventuele downstream-toepassings container installatie kopieën bijwerken.

Telkens wanneer een basis installatie kopie wordt bijgewerkt, moeten alle downstream container installatie kopieën ook worden bijgewerkt. Dit bouw proces moet worden geïntegreerd in de validatie-en implementatie pijplijnen, zoals [Azure-pijp lijnen][azure-pipelines] of Jenkins. Met deze pijp lijnen zorgt u ervoor dat uw toepassingen worden uitgevoerd op de bijgewerkte, op updates gebaseerde installatie kopieën. Zodra de installatie kopieën van de toepassings container zijn gevalideerd, kunnen de AKS-implementaties worden bijgewerkt om de nieuwste, beveiligde installatie kopieën uit te voeren.

Azure Container Registry taken kunnen ook automatisch container installatie kopieën bijwerken wanneer de basis installatie kopie wordt bijgewerkt. Met deze functie kunt u een klein aantal basis installatie kopieën maken en ze regel matig bijwerken met fout-en beveiligings oplossingen.

Zie voor meer informatie over updates van basis installatie [kopieën automatiseren samen stellen van de basis installatie kopie met Azure container Registry taken][acr-base-image-update].

## <a name="next-steps"></a>Volgende stappen

Dit artikel is gericht op het beveiligen van uw containers. Raadpleeg de volgende artikelen voor meer informatie over het implementeren van sommige van deze gebieden:

* [Automatische installatie kopieën automatiseren met de update van de basis installatie kopie met Azure Container Registry taken][acr-base-image-update]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
[security-center-containers]: ../security-center/container-security.md
[security-center-acr]: ../security-center/defender-for-container-registries-introduction.md