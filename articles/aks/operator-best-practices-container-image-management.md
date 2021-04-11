---
title: Aanbevolen procedures voor de operator-container installatie kopie beheer in azure Kubernetes Services (AKS)
description: Meer informatie over de aanbevolen procedures voor cluster operators voor het beheren en beveiligen van container installatie kopieën in azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 998d8602b6aa0e71a04f75aff1c29551ba09c8a3
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105116"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Aanbevolen procedures voor het beheer van container installatie kopieën en beveiliging in azure Kubernetes service (AKS)

Beveiliging van container en container installatie kopieën is een belang rijke prioriteit tijdens het ontwikkelen en uitvoeren van toepassingen in azure Kubernetes service (AKS). Containers met verouderde basis installatie kopieën of niet-patchde toepassings Runtimes veroorzaken een beveiligings risico en mogelijke aanvals vector. 

Beperk Risico's door hulpprogram ma's voor scannen en herstellen in uw containers te integreren en uit te voeren tijdens build en runtime. De eerdere versie van het beveiligings probleem of de verouderde basis installatie kopie, hoe veiliger uw cluster. 

In dit artikel staat *"containers"* voor zowel:
* De container installatie kopieën die zijn opgeslagen in een container register.
* De actieve containers.

Dit artikel is gericht op het beveiligen van uw containers in AKS. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Problemen met de installatie kopie scannen en oplossen.
> * Container installatie kopieën automatisch activeren en opnieuw implementeren wanneer een basis installatie kopie wordt bijgewerkt.

U kunt ook de aanbevolen procedures voor [cluster beveiliging][best-practices-cluster-security] en voor [pod-beveiliging][best-practices-pod-security]lezen.

U kunt ook [container beveiliging in Security Center][security-center-containers] gebruiken om uw containers te helpen scannen op beveiligings problemen. [Azure container Registry integratie][security-center-acr] met Security Center helpt u bij het beveiligen van uw installatie kopieën en het REGI ster tegen beveiligings problemen.

## <a name="secure-the-images-and-run-time"></a>De installatie kopieën en uitvoerings tijd beveiligen

> **Richtlijnen voor best practices** 
>
> Scan de container installatie kopieën op beveiligings problemen. Alleen gevalideerde installatie kopieën implementeren. Werk de basis installatie kopieën en runtime van de toepassing regel matig bij. Werk belastingen in het AKS-cluster opnieuw te implementeren.

Bij het aannemen van op containers gebaseerde workloads moet u de beveiliging van installatie kopieën en runtime controleren die worden gebruikt voor het bouwen van uw eigen toepassingen. Hoe kunt u voor komen dat beveiligings problemen in uw implementaties worden geïntroduceerd? 
* Neem in de implementatie werk stroom een proces op waarmee container installatie kopieën worden gescand met behulp van hulpprogram ma's zoals [Twistlock][twistlock] of [zeeblauw][aqua].
* Alleen geverifieerde installatie kopieën mogen worden geïmplementeerd.

![Container installatie kopieën scannen en herstellen, valideren en implementeren](media/operator-best-practices-container-security/scan-container-images-simplified.png)

U kunt bijvoorbeeld een pijp lijn voor continue integratie en continue implementatie (CI/CD) gebruiken om de scans, verificatie en implementaties van de installatie kopie te automatiseren. Azure Container Registry bevat deze mogelijkheden voor het scannen van beveiligings problemen.

## <a name="automatically-build-new-images-on-base-image-update"></a>Automatisch nieuwe installatie kopieën bouwen bij het bijwerken van de basis installatie kopie

> **Richtlijnen voor best practices** 
>
> Wanneer u basis installatie kopieën voor toepassings installatie kopieën gebruikt, moet u Automation gebruiken om nieuwe installatie kopieën te bouwen wanneer de basis installatie kopie wordt bijgewerkt. Omdat bijgewerkte basis installatie kopieën doorgaans beveiligings oplossingen bevatten, moet u eventuele downstream-toepassings container installatie kopieën bijwerken.

Telkens wanneer een basis installatie kopie wordt bijgewerkt, moet u ook alle downstream container installatie kopieën bijwerken. Integreer dit bouw proces in pijp lijnen voor validatie en implementatie, zoals [Azure-pijp lijnen][azure-pipelines] of Jenkins. Deze pijp lijnen zorgen ervoor dat uw toepassingen blijven worden uitgevoerd op de bijgewerkte, op updates gebaseerde installatie kopieën. Zodra de installatie kopieën van de toepassings container zijn gevalideerd, kunnen de AKS-implementaties worden bijgewerkt om de nieuwste, beveiligde installatie kopieën uit te voeren.

Azure Container Registry taken kunnen ook automatisch container installatie kopieën bijwerken wanneer de basis installatie kopie wordt bijgewerkt. Met deze functie bouwt u enkele basis installatie kopieën en houdt u deze bij met oplossingen voor fouten en beveiliging.

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