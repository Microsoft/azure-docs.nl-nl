---
title: 'Azure Defender voor Kubernetes: de voordelen en functies'
description: Meer informatie over de voordelen en functies van Azure Defender voor Kubernetes.
author: memildin
ms.author: memildin
ms.date: 02/07/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 0878686e203960a0b7f33c19cc64e82319997684
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590452"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>Inleiding tot Azure Defender for Kubernetes

Azure Kubernetes service (AKS) is de beheerde service van Microsoft voor het ontwikkelen, implementeren en beheren van toepassingen in containers.

Azure Security Center en AKS vormen een Cloud-native Kubernetes-beveiligings aanbieding met omgevings beveiliging, beveiliging van werk belastingen en runtime-beveiliging, zoals wordt beschreven in [Container Security in Security Center](container-security.md).

Schakel **Azure Defender voor Kubernetes** in voor het detecteren van bedreigingen voor uw Kubernetes-clusters.

Detectie van bedreigingen op hostniveau voor uw Linux AKS-knoop punten is beschikbaar als u [Azure Defender voor servers](defender-for-servers-introduction.md) en de log Analytics-agent inschakelt. Als uw AKS-cluster echter is geïmplementeerd op een schaalset voor virtuele machines, wordt de Log Analytics-agent momenteel niet ondersteund.

## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Releasestatus:|Algemene Beschik baarheid (GA)|
|Prijzen:|Voor **Azure Defender for Kubernetes** gelden de prijzen op de [pagina Prijzen](security-center-pricing.md)|
|Vereiste rollen en machtigingen:|**Beveiligingsbeheerder** kan waarschuwingen negeren.<br>**Beveiligingslezer** kan bevindingen bekijken.|
|Clouds:|![Ja](./media/icons/yes-icon.png) Commerciële clouds<br>![Ja](./media/icons/yes-icon.png) Nationaal/onafhankelijk (overheid van de VS, China, andere overheden)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>Wat zijn de voordelen van Azure Defender voor Kubernetes?

Azure Defender voor Kubernetes biedt **bedreigingen beveiliging op cluster niveau** door uw door aks beheerde services te bewaken via de logboeken die zijn opgehaald door Azure Kubernetes service (AKS).

Voor beelden van beveiligings gebeurtenissen die door Azure Defender voor Kubernetes-monitors worden weer gegeven, omvatten beschik bare Kubernetes-Dash boards, het maken van rollen met hoge bevoegdheden en het maken van gevoelige koppels. Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-akscluster)voor een volledige lijst met waarschuwingen op AKS-cluster niveau.

> [!TIP]
> U kunt containerwaarschuwingen simuleren door de instructies in [dit blogbericht](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270) te volgen.

Bovendien houdt ons wereldwijde team van beveiligingsonderzoekers het bedreigingslandschap voortdurend in de gaten. Ze voegen containerspecifieke waarschuwingen en beveiligingsproblemen toe zodra ze worden gedetecteerd.

>[!NOTE]
> Security Center genereert beveiligings waarschuwingen voor Azure Kubernetes-service acties en implementaties die plaatsvinden **nadat** u Azure Defender voor Kubernetes hebt ingeschakeld.




## <a name="azure-defender-for-kubernetes---faq"></a>Azure Defender voor Kubernetes: veelgestelde vragen

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>Kan ik nog wel AKS-beveiligingen krijgen zonder de Log Analytics-agent?

Het Kubernetes-abonnement **van Azure Defender** biedt beveiliging op cluster niveau. Als u ook de Log Analytics-agent van **Azure Defender voor servers** implementeert, krijgt u de bedreigings beveiliging voor uw knoop punten die bij dat plan worden meegeleverd. Meer informatie vindt [u in Inleiding tot Azure Defender voor servers](defender-for-servers-introduction.md).

We raden u aan beide te implementeren voor de meest volledige beveiliging.

Als u ervoor kiest om de agent niet op uw hosts te installeren, krijgt u maar een subset van de voordelen van bedreigingsbeveiliging en beveiligingswaarschuwingen. U ontvangt nog steeds waarschuwingen met betrekking tot netwerkanalyse en communicatie met schadelijke servers.

### <a name="does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes"></a>Kan ik met AKS aangepaste VM-extensies installeren op mijn AKS-knooppunten?
Als Azure Defender uw AKS-knooppunten moet bewaken, moet de Log Analytics-agent erop worden uitgevoerd. 

AKS is een beheerde service en aangezien de Log Analytics-agent een door Microsoft beheerde uitbreiding is, wordt deze ook ondersteund op AKS-clusters.

### <a name="if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too"></a>Als op mijn cluster al een Azure Monitor voor containers-agent wordt uitgevoerd, heb ik dan ook de Log Analytics-agent nodig?
Als Azure Defender uw AKS-knooppunten moet bewaken, moet de Log Analytics-agent erop worden uitgevoerd.

Als op uw clusters al de Azure Monitor voor containers-agent wordt uitgevoerd, kunt u de Log Analytics-agent ook installeren en kunnen de twee agents zonder problemen naast elkaar worden gebruikt.

[Meer informatie over de Azure Monitor voor containers-agent](../azure-monitor/containers/container-insights-manage-agent.md).


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over de Kubernetes-beveiliging van Security Center, inclusief Azure Defender voor Kubernetes. 

> [!div class="nextstepaction"]
> [Azure Defender inschakelen](security-center-pricing.md#enable-azure-defender)

Raadpleeg de volgende artikelen voor gerelateerd materiaal: 

- [Waarschuwingen streamen naar een SIEM-, SOAR- of IT Service Management-oplossing](export-to-siem.md)
- [Referentietabel met waarschuwingen](alerts-reference.md)
