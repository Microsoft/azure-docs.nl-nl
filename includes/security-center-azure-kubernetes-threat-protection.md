---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 04/07/2021
ms.topic: include
ms.openlocfilehash: 73886b966676af75cc74484ccdc0632f080b041a
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029133"
---
Azure Defender biedt realtime bescherming van bedreigingen voor uw container omgevingen en genereert waarschuwingen voor verdachte activiteiten. U kunt deze informatie gebruiken om snel beveiligingsproblemen op te lossen en om de beveiliging van uw containers te verbeteren.

Azure Defender biedt beveiliging tegen bedreigingen op verschillende niveaus: 

* **Hostniveau (door Azure Defender voor servers)** : met behulp van dezelfde log Analytics-agent die Security Center gebruikt op andere vm's, bewaakt Azure Defender uw Linux Kubernetes-knoop punten voor verdachte activiteiten, zoals detectie van webshells en verbinding met bekende verdachte IP-adressen. De agent controleert ook op containerspecifieke analyses, zoals het maken van geprivilegieerde containers, verdachte toegang tot API-servers en SSH-servers (Secure Shell) die binnen een Docker-container worden uitgevoerd.

    Als u ervoor kiest om de agents niet op uw hosts te installeren, krijgt u maar een subset van de voordelen van bedreigingsbeveiliging en beveiligingswaarschuwingen. U ontvangt nog steeds waarschuwingen met betrekking tot netwerkanalyse en communicatie met schadelijke servers.

    >[!IMPORTANT]
    > Momenteel wordt de installatie van de Log Analytics-agent op Azure Kubernetes-Service clusters die worden uitgevoerd op schaal sets voor virtuele machines niet ondersteund.

    Zie de [naslag tabel met waarschuwingen](../articles/security-center/alerts-reference.md#alerts-containerhost)voor een lijst met waarschuwingen op cluster niveau.


* **Cluster niveau (door Azure Defender voor Kubernetes)** : op cluster niveau is beveiliging tegen bedreigingen gebaseerd op het analyseren van controle logboeken van Kubernetes. Schakel Azure Defender in om deze **agentloze** bewaking in te scha kelen. Als uw cluster on-premises of een andere Cloud provider is, schakelt u [Arc ingeschakeld Kubernetes en de Azure Defender-extensie](../articles/security-center/defender-for-kubernetes-azure-arc.md)in.

    Als u waarschuwingen op dit niveau wilt genereren, controleert Azure Defender de logboeken van uw clusters. Voorbeelden van gebeurtenissen op dit niveau zijn onder andere beschikbare Kubernetes-dashboards, het maken van rollen met hoge bevoegdheden en het maken van gevoelige koppelingen.

    >[!NOTE]
    > In azure Defender worden beveiligings waarschuwingen gegenereerd voor acties en implementaties die zich voordoen nadat u het Kubernetes-abonnement voor Defender voor u hebt ingeschakeld. 

    Zie de [naslag tabel met waarschuwingen](../articles/security-center/alerts-reference.md#alerts-akscluster)voor een lijst met waarschuwingen op cluster niveau.

Bovendien houdt ons wereldwijde team van beveiligingsonderzoekers het bedreigingslandschap voortdurend in de gaten. Ze voegen containerspecifieke waarschuwingen en beveiligingsproblemen toe zodra ze worden gedetecteerd.

> [!TIP]
> U kunt containerwaarschuwingen simuleren door de instructies in [dit blogbericht](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270) te volgen.