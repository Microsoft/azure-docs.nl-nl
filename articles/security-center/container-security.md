---
title: Containerbeveiliging in Azure Security Center | Microsoft Docs
description: Meer informatie over beveiligingsfuncties voor containers van Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/07/2021
ms.author: memildin
ms.openlocfilehash: 3b5204f1d390388c2dc9a10ac2ca0234f6b0499b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102101338"
---
# <a name="container-security-in-security-center"></a>Containerbeveiliging in Security Center

Azure Security Center is de eigen oplossing van Azure voor het beveiligen van uw containers.

Security Center kan de volgende containerresourcetypen beveiligen:

| Resourcetype | Beveiligingen die worden aangeboden door Security Center |
|:--------------------:|-----------|
| ![Kubernetes-service](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png)<br>**Azure Kubernetes Service-clusters (AKS)** | - Doorlopende evaluatie van de configuraties van uw AKS-clusters om inzicht te krijgen in onjuiste configuraties, en richtlijnen om eventuele gedetecteerde problemen op te lossen.<br>[Meer informatie over omgevingsbeveiliging via beveiligingsaanbevelingen](#environment-hardening).<br><br>- Beveiliging van AKS-clusters en Linux-knooppunten tegen bedreigingen. Met de optionele oplossing [Azure Defender for Kubernetes](defender-for-kubernetes-introduction.md) wordt voorzien in waarschuwingen voor verdachte activiteiten.<br>[Meer informatie over runtime-beveiliging voor AKS-knooppunten en -clusters](#run-time-protection-for-aks-nodes-and-clusters).|
| ![Containerhost](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png)<br>**Containerhosts**<br>(VM's met Docker) | - Doorlopende evaluatie van uw Docker-configuraties van uw AKS-clusters om inzicht te krijgen in onjuiste configuraties, en richtlijnen om eventuele gedetecteerde problemen op te lossen met de optionele [Azure Defender voor servers](defender-for-servers-introduction.md).<br>[Meer informatie over omgevingsbeveiliging via beveiligingsaanbevelingen](#environment-hardening).|
| ![Containerregister](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png)<br>**ACR-registers (ACR: Azure Container Registry)** | - Hulpprogramma's voor de evaluatie en het beheer van beveiligingsproblemen voor de installatiekopieën in uw op Azure Resource Manager gebaseerde ACR-registers met de optionele [Azure Defender voor containerregisters](defender-for-container-registries-introduction.md).<br>[Meer informatie over het scannen van installatiekopieën van uw containers op beveiligingsproblemen](#vulnerability-management---scanning-container-images). |
|||

In dit artikel wordt beschreven hoe u Security Center kunt gebruiken, samen met de optionele Azure Defender-plannen voor containerregisters, servers en Kubernetes, om de beveiliging van uw containers en de bijbehorende apps te verbeteren, bewaken en onderhouden.

U leert hoe u met behulp van Security Center kunt werken met de volgende kernaspecten van containerbeveiliging:

- [Beheer van beveiligingsproblemen - containerinstallatiekopieën scannen](#vulnerability-management---scanning-container-images)
- [Omgevingsbeveiliging](#environment-hardening)
- [Runtime-beveiliging voor AKS-knooppunten en -clusters](#run-time-protection-for-aks-nodes-and-clusters)

In de volgende schermopname ziet u de pagina voor de inventarisatie van activa en de verschillende containerresourcetypen die door Security Center worden beveiligd.

:::image type="content" source="./media/container-security/inventory-container-resources.png" alt-text="Container-gerelateerde resources op de pagina voor de inventarisatie van activa van Security Center" lightbox="./media/container-security/inventory-container-resources.png":::

## <a name="vulnerability-management---scanning-container-images"></a>Beheer van beveiligingsproblemen - containerinstallatiekopieën scannen

Als u de installatiekopieën in uw op Azure Resource Manager gebaseerde Azure-containerregisters wilt bewaken, schakelt u [Azure Defender voor containerregisters](defender-for-container-registries-introduction.md) in. Security Center scant alle installatiekopieën die in de afgelopen 30 dagen zijn opgehaald, naar uw register zijn gepusht of zijn geïmporteerd. De geïntegreerde scanner wordt verschaft door de toonaangevende leverancier voor het scannen op beveiligingsproblemen, Qualys.

Als er problemen worden gevonden door Qualys of Security Center, ontvangt u een melding in het [Azure Defender-dashboard](azure-defender-dashboard.md). Security Center biedt bij elk beveiligingsprobleem praktische aanbevelingen, maar ook een classificatie van de ernst en richtlijnen voor het oplossen van het probleem. Zie voor meer informatie over de aanbevelingen van Security Center voor containers de [Verwijzingenlijst met aanbevelingen](recommendations-reference.md#recs-compute).

Security Center filtert en classificeert de resultaten van de scanner. Wanneer een installatiekopie in orde is, wordt deze als zodanig gemarkeerd door Security Center. Security Center genereert alleen aanbevelingen voor de beveiliging ten aanzien van installatiekopieën waarvoor problemen moeten worden opgelost. Als er bij problemen alleen een melding wordt weergegeven, vermindert Security Center het potentieel voor ongewenste informatieve waarschuwingen.

## <a name="environment-hardening"></a>Omgevingsbeveiliging

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Doorlopende bewaking van uw Docker-configuratie

Azure Security Center identificeert niet-beheerde containers die worden gehost op IaaS Linux-VM's of andere Linux-machines waarop Docker-containers worden uitgevoerd. Security Center evalueert doorlopend de configuraties van deze containers. Vervolgens worden ze vergeleken met de [Docker-benchmark van het CIS (Center for Internet Security)](https://www.cisecurity.org/benchmark/docker/).

Security Center bevat de volledige regelset van de CIS Docker-benchmark en waarschuwt u als uw containers niet voldoen aan een van de controles. Als er onjuiste configuraties worden gevonden, worden in Security Center aanbevelingen voor de beveiliging gegenereerd. U kunt de aanbevelingen bekijken en problemen herstellen met behulp van de **pagina met aanbevelingen van Security Center**. De CIS-benchmarkcontroles worden niet uitgevoerd op door AKS beheerde instanties of door Databricks beheerde VM's.

Zie de sectie [Rekenkracht](recommendations-reference.md#recs-compute) van de naslagtabel met aanbevelingen voor meer informatie over de relevante aanbevelingen van Security Center die voor deze functie kunnen worden weergegeven.

Security Center biedt extra informatie over de containers op een virtuele machine wanneer u de beveiligingsproblemen van een machine wilt verkennen. Deze informatie omvat de Docker-versie en het aantal installatiekopieën dat op de host wordt uitgevoerd. 

Als u niet-beheerde containers wilt bewaken die worden gehost op IaaS Linux VM's, schakelt u de optionele [Azure Defender voor servers](defender-for-servers-introduction.md) in.


### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Continue bewaking van uw Kubernetes-clusters
Security Center werkt samen met Azure Kubernetes service (AKS), de beheerde containerindelingsservice van Microsoft voor het ontwikkelen, implementeren en beheren van toepassingen in containers.

AKS biedt beveiligingsmaatregelen voor en inzicht in het beveiligingspostuur van uw clusters. Security Center gebruikt deze functies om voortdurend de configuratie van uw AKS-clusters te controleren en beveiligings aanbevelingen te genereren die zijn afgestemd op de industrie normen.

Dit is een diagram op hoog niveau van de interactie tussen Azure Security Center, de Azure Kubernetes-service en Azure Policy:

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="Architectuur op hoog niveau van de interactie tussen Azure Security Center, Azure Kubernetes Service en Azure Policy" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::

In de door Security Center ontvangen en geanalyseerde items ziet u:

- auditlogboeken van de API-server
- onbewerkte beveiligingsgebeurtenissen van de Log Analytics-agent

    > [!NOTE]
    > Momenteel wordt de installatie van de Log Analytics-agent op Azure Kubernetes-Service clusters die worden uitgevoerd op schaal sets voor virtuele machines niet ondersteund.

- clusterconfiguratiegegevens van het AKS-cluster
- configuratie van de werk belasting van Azure Policy (via de **Azure Policy-invoeg toepassing voor Kubernetes**)

Zie de sectie [Rekenkracht](recommendations-reference.md#recs-compute) van de naslagtabel met aanbevelingen voor meer informatie over de relevante aanbevelingen van Security Center die voor deze functie kunnen worden weergegeven.


###  <a name="workload-protection-best-practices-using-kubernetes-admission-control"></a>Aanbevolen procedures voor workloadbeveiliging met behulp van Kubernetes Admission Control

Installeer de **Azure Policy-invoegtoepassing voor Kubernetes** om een set aanbevelingen te krijgen voor het beveiligen van de workloads van uw Kubernetes-containers. U kunt deze invoeg toepassing ook automatisch implementeren, zoals wordt uitgelegd in [automatische inrichting van de log Analytics agent en uitbrei dingen inschakelen](security-center-enable-data-collection.md#auto-provision-mma). Als automatische inrichting voor de invoegtoepassing is ingeschakeld, wordt de uitbreiding standaard ingeschakeld in alle bestaande en toekomstige clusters (die voldoen aan de installatievereisten van de invoegtoepassing).

Zoals beschreven in deze pagina [Azure Policy for Kubernetes](../governance/policy/concepts/policy-for-kubernetes.md), vormt de invoegtoepassing een uitbreiding op de open-source webhook voor de [Gatekeeper v3](https://github.com/open-policy-agent/gatekeeper) -toegangscontroller voor  [Open Policy Agent](https://www.openpolicyagent.org/). Toegangscontrollers van Kubernetes zijn invoegtoepassingen die de gebruikswijze van uw clusters afdwingen. De invoegtoepassing wordt geregistreerd als een webhook voor Kubernetes-toegangsbeheer en maakt het mogelijk om op een gecentraliseerde, consistente manier afdwinging en beveiliging op uw clusters op schaal toe te passen. 

Met de invoegtoepassing op uw AKS-cluster wordt elke aanvraag voor de Kubernetes API-server gecontroleerd op basis van de vooraf gedefinieerde set aanbevolen procedures voordat deze naar het cluster gaat. Vervolgens kunt u instellingen configureren om de aanbevolen procedures **af te dwingen** en ze te verplichten voor toekomstige workloads. 

U kunt er bijvoorbeeld voor zorgen dat containers met machtigingen niet moeten worden gemaakt, en toekomstige aanvragen hiervoor worden dan geblokkeerd.

Meer informatie vindt u in [Uw Kubernetes-workloads beveiligen](kubernetes-workload-protections.md).


## <a name="run-time-protection-for-aks-nodes-and-clusters"></a>Runtime-beveiliging voor AKS-knooppunten en -clusters

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]



## <a name="next-steps"></a>Volgende stappen

In dit overzicht hebt u informatie gekregen over de kernelementen van containerbeveiliging in Azure Security Center. Zie voor gerelateerd materiaal:

- [Inleiding tot Azure Defender for Kubernetes](defender-for-kubernetes-introduction.md)
- [Inleiding tot Azure Defender voor containerregisters](defender-for-container-registries-introduction.md)