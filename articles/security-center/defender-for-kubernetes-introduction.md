---
title: 'Azure Defender voor Kubernetes: de voordelen en functies'
description: Meer informatie over de voordelen en functies van Azure Defender voor Kubernetes.
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: a19e90a15991cdc03999bf43d5bece63325aab05
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916571"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>Inleiding tot Azure Defender for Kubernetes

Azure Kubernetes service (AKS) is de beheerde service van Microsoft voor het ontwikkelen, implementeren en beheren van toepassingen in containers.

Azure Security Center en AKS vormen de beste cloudeigen beveiligingsaanbieding voor Kubernetes, en samen bieden ze bescherming van de omgeving, workloadbeveiliging en runtime-beveiliging, zoals hieronder beschreven.

Schakel **Azure Defender voor Kubernetes** in voor het detecteren van bedreigingen voor uw Kubernetes-clusters.

Detectie van bedreigingen op hostniveau voor uw Linux AKS-knooppunten is beschikbaar als u [Azure Defender voor servers](defender-for-servers-introduction.md) inschakelt.

## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Releasestatus:|Algemene Beschik baarheid (GA)|
|Prijzen:|Voor **Azure Defender for Kubernetes** gelden de prijzen op de [pagina Prijzen](security-center-pricing.md)|
|Vereiste rollen en machtigingen:|**Beveiligingsbeheerder** kan waarschuwingen negeren.<br>**Beveiligingslezer** kan bevindingen bekijken.|
|Clouds:|![Ja](./media/icons/yes-icon.png) Commerciële clouds<br>![Ja](./media/icons/yes-icon.png) Nationaal/onafhankelijk (overheid van de VS, China, andere overheden)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>Wat zijn de voordelen van Azure Defender voor Kubernetes?

### <a name="run-time-protection"></a>Runtime-beveiliging

Dankzij doorlopende analyse van de volgende AKS-bronnen biedt Security Center realtimebeveiliging tegen bedreigingen voor uw containeromgevingen en worden er waarschuwingen gegenereerd voor bedreigingen en schadelijke activiteiten die worden gedetecteerd op de host- *en* AKS-clusterniveau. U kunt deze informatie gebruiken om snel beveiligingsproblemen op te lossen en om de beveiliging van uw containers te verbeteren.

Security Center biedt beveiliging tegen bedreigingen op verschillende niveaus: 

- **Hostniveau (door Azure Defender voor servers)** : met dezelfde Log Analytics-agent die Security Center gebruikt op andere VM's, controleert Azure Defender uw Linux AKS-knooppunten op verdachte activiteiten zoals detectie van webshells en verbinding met bekende verdachte IP-adressen. De agent controleert ook op containerspecifieke analyses, zoals het maken van geprivilegieerde containers, verdachte toegang tot API-servers en SSH-servers (Secure Shell) die binnen een Docker-container worden uitgevoerd.

    Zie de [Referentietabel met waarschuwingen](alerts-reference.md#alerts-containerhost) voor een lijst met waarschuwingen op AKS-hostniveau.

    >[!IMPORTANT]
    > Als u ervoor kiest om de agents niet op uw hosts te installeren, krijgt u maar een subset van de voordelen van bedreigingsbeveiliging en beveiligingswaarschuwingen. U ontvangt nog steeds waarschuwingen met betrekking tot netwerkanalyse en communicatie met schadelijke servers.

- **AKS-cluster niveau (verschaft door Azure Defender voor Kubernetes)** op clusterniveau is de beveiliging tegen bedreigingen gebaseerd op het analyseren van de auditlogboeken van Kubernetes. Schakel Azure Defender in om deze **agentloze** bewaking in te scha kelen. Om waarschuwingen op dit niveau te genereren, bewaakt Security Center uw met AKS beheerde services met behulp van de door AKS opgehaalde logboeken. Voorbeelden van gebeurtenissen op dit niveau zijn onder andere beschikbare Kubernetes-dashboards, het maken van rollen met hoge bevoegdheden en het maken van gevoelige koppelingen.

    Zie de [Referentietabel met waarschuwingen](alerts-reference.md#alerts-akscluster) voor een lijst met waarschuwingen op AKS-clusterniveau.

    >[!NOTE]
    > Security Center genereert beveiligingswaarschuwingen voor Azure Kubernetes Service-acties en -implementaties die worden uitgevoerd nadat de Kubernetes-optie is ingeschakeld in de abonnementsinstellingen. 

Bovendien houdt ons wereldwijde team van beveiligingsonderzoekers het bedreigingslandschap voortdurend in de gaten. Ze voegen containerspecifieke waarschuwingen en beveiligingsproblemen toe zodra ze worden gedetecteerd.

> [!TIP]
> U kunt containerwaarschuwingen simuleren door de instructies in [dit blogbericht](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270) te volgen.



## <a name="how-does-security-centers-kubernetes-protection-work"></a>Hoe werkt de Kubernetes-beveiliging van Security Center?

Hieronder ziet u een diagram op hoog niveau van de interactie tussen Azure Security Center, Azure Kubernetes Service en Azure Policy.

In de door Security Center ontvangen en geanalyseerde items ziet u:

- auditlogboeken van de API-server
- onbewerkte beveiligingsgebeurtenissen van de Log Analytics-agent
- clusterconfiguratiegegevens van het AKS-cluster
- werkbelastingconfiguratie van Azure Policy (via de **Azure Policy-invoegtoepassing voor Kubernetes**). [Lees meer over aanbevolen procedures voor workloadbeveiliging met behulp van Kubernetes Admission Control](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="Architectuur op hoog niveau van de interactie tussen Azure Security Center, Azure Kubernetes Service en Azure Policy" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::




## <a name="azure-defender-for-kubernetes---faq"></a>Azure Defender voor Kubernetes: veelgestelde vragen

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>Kan ik nog wel AKS-beveiligingen krijgen zonder de Log Analytics-agent?

Zoals hierboven vermeld, biedt het optionele **Azure Defender for Kubernetes**-abonnement beveiliging op clusterniveau. De Log Analytics-agent van **Azure Defender voor servers** beveiligt uw knooppunten. 

We raden u aan beide te implementeren voor de meest volledige beveiliging.

Als u ervoor kiest om de agent niet op uw hosts te installeren, krijgt u maar een subset van de voordelen van bedreigingsbeveiliging en beveiligingswaarschuwingen. U ontvangt nog steeds waarschuwingen met betrekking tot netwerkanalyse en communicatie met schadelijke servers.


### <a name="does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes"></a>Kan ik met AKS aangepaste VM-extensies installeren op mijn AKS-knooppunten?

Als Azure Defender uw AKS-knooppunten moet bewaken, moet de Log Analytics-agent erop worden uitgevoerd. 

AKS is een beheerde service en aangezien de Log Analytics-agent een door Microsoft beheerde uitbreiding is, wordt deze ook ondersteund op AKS-clusters.



### <a name="if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too"></a>Als op mijn cluster al een Azure Monitor voor containers-agent wordt uitgevoerd, heb ik dan ook de Log Analytics-agent nodig?

Als Azure Defender uw AKS-knooppunten moet bewaken, moet de Log Analytics-agent erop worden uitgevoerd.

Als op uw clusters al de Azure Monitor voor containers-agent wordt uitgevoerd, kunt u de Log Analytics-agent ook installeren en kunnen de twee agents zonder problemen naast elkaar worden gebruikt.

[Meer informatie over de Azure Monitor voor containers-agent](../azure-monitor/insights/container-insights-manage-agent.md).


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over de Kubernetes-beveiliging van Security Center, inclusief Azure Defender voor Kubernetes. 

Raadpleeg de volgende artikelen voor gerelateerd materiaal: 

- [Azure Defender inschakelen](security-center-pricing.md#enable-azure-defender)
- [Waarschuwingen streamen naar een SIEM-, SOAR- of IT Service Management-oplossing](export-to-siem.md)
- [Referentietabel met waarschuwingen](alerts-reference.md)