---
title: 'Azure Defender voor Kubernetes: de voordelen en functies'
description: Meer informatie over de voordelen en functies van Azure Defender voor Kubernetes.
author: memildin
ms.author: memildin
ms.date: 04/07/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: c500c7b7afb36ffbe04fb63551c3a7d17c1347d9
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029077"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>Inleiding tot Azure Defender for Kubernetes

Azure Defender voor Kubernetes is het Azure Defender-abonnement dat u wilt beveiligen voor uw Kubernetes-clusters, waar ze ook worden uitgevoerd. 

We kunnen clusters beschermen in:

- **Azure Kubernetes service (AKS)** -beheerde service van micro soft voor het ontwikkelen, implementeren en beheren van toepassingen in containers

- **On-premises en omgevingen met meerdere clouds** : met een [uitbrei ding voor Arc ingeschakelde Kubernetes](defender-for-kubernetes-azure-arc.md)

Azure Security Center en AKS vormen een Cloud-native Kubernetes-beveiligings aanbieding met omgevings beveiliging, beveiliging van werk belastingen en runtime-beveiliging, zoals wordt beschreven in [Container Security in Security Center](container-security.md).

Detectie van bedreigingen op hostniveau voor uw Linux AKS-knoop punten is beschikbaar als u [Azure Defender voor servers](defender-for-servers-introduction.md) en de log Analytics-agent inschakelt. Als uw cluster echter wordt geïmplementeerd op een schaalset voor virtuele machines, wordt de Log Analytics-agent momenteel niet ondersteund.



## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Releasestatus:|Algemene Beschik baarheid (GA)|
|Prijzen:|**Azure Defender voor Kubernetes** wordt gefactureerd zoals wordt weer gegeven op [Security Center prijzen](https://azure.microsoft.com/pricing/details/security-center/)|
|Vereiste rollen en machtigingen:|**Beveiligingsbeheerder** kan waarschuwingen negeren.<br>**Beveiligingslezer** kan bevindingen bekijken.|
|Clouds:|![Ja](./media/icons/yes-icon.png) Commerciële clouds<br>![Ja](./media/icons/yes-icon.png) Nationaal/onafhankelijk (overheid van de VS, China, andere overheden)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>Wat zijn de voordelen van Azure Defender voor Kubernetes?

Azure Defender voor Kubernetes biedt **bedreigingen beveiliging op cluster niveau** door de logboeken van uw clusters te bewaken.

Voor beelden van beveiligings gebeurtenissen die door Azure Defender voor Kubernetes-monitors worden weer gegeven, omvatten beschik bare Kubernetes-Dash boards, het maken van rollen met hoge bevoegdheden en het maken van gevoelige koppels. Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-akscluster)voor een volledige lijst met waarschuwingen op cluster niveau.

> [!TIP]
> U kunt containerwaarschuwingen simuleren door de instructies in [dit blogbericht](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270) te volgen.

Bovendien houdt ons wereldwijde team van beveiligingsonderzoekers het bedreigingslandschap voortdurend in de gaten. Ze voegen containerspecifieke waarschuwingen en beveiligingsproblemen toe zodra ze worden gedetecteerd.

>[!NOTE]
> In azure Defender worden beveiligings waarschuwingen gegenereerd voor acties en implementaties die zich voordoen nadat u het Kubernetes-abonnement voor Defender voor u hebt ingeschakeld.




## <a name="azure-defender-for-kubernetes---faq"></a>Azure Defender voor Kubernetes: veelgestelde vragen

### <a name="can-i-still-get-cluster-protections-without-the-log-analytics-agent"></a>Kan ik nog steeds cluster beveiligingen krijgen zonder de Log Analytics agent?

Het Kubernetes-abonnement **van Azure Defender** biedt beveiliging op cluster niveau. Als u ook de Log Analytics-agent van **Azure Defender voor servers** implementeert, krijgt u de bedreigings beveiliging voor uw knoop punten die bij dat plan worden meegeleverd. Meer informatie vindt [u in Inleiding tot Azure Defender voor servers](defender-for-servers-introduction.md).

We raden u aan beide te implementeren voor de meest volledige beveiliging.

Als u ervoor kiest om de agent niet op uw hosts te installeren, krijgt u maar een subset van de voordelen van bedreigingsbeveiliging en beveiligingswaarschuwingen. U ontvangt nog steeds waarschuwingen met betrekking tot netwerkanalyse en communicatie met schadelijke servers.

### <a name="does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes"></a>Kan ik met AKS aangepaste VM-extensies installeren op mijn AKS-knooppunten?
Als Azure Defender uw AKS-knooppunten moet bewaken, moet de Log Analytics-agent erop worden uitgevoerd. 

AKS is een beheerde service en aangezien de Log Analytics-agent een door Microsoft beheerde uitbreiding is, wordt deze ook ondersteund op AKS-clusters.

### <a name="if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too"></a>Als op mijn cluster al een Azure Monitor voor containers-agent wordt uitgevoerd, heb ik dan ook de Log Analytics-agent nodig?
Voor Azure Defender om uw knoop punten te bewaken, moet de Log Analytics-agent worden uitgevoerd.

Als op uw clusters al de Azure Monitor voor containers-agent wordt uitgevoerd, kunt u de Log Analytics-agent ook installeren en kunnen de twee agents zonder problemen naast elkaar worden gebruikt.

[Meer informatie over de Azure Monitor voor containers-agent](../azure-monitor/containers/container-insights-manage-agent.md).


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over de Kubernetes-beveiliging van Security Center, inclusief Azure Defender voor Kubernetes. 

> [!div class="nextstepaction"]
> [Azure Defender inschakelen](enable-azure-defender.md)

Raadpleeg de volgende artikelen voor gerelateerd materiaal: 

- [Waarschuwingen streamen naar een SIEM-, SOAR- of IT Service Management-oplossing](export-to-siem.md)
- [Referentietabel met waarschuwingen](alerts-reference.md)
