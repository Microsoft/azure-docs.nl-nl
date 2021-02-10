---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 06/30/2020
ms.topic: include
ms.openlocfilehash: 4c09057f606423dc92b3364e502e632a385bf83f
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007806"
---
Security Center biedt realtime-beveiliging tegen bedreigingen voor in containers geplaatste omgevingen. Ook worden waarschuwingen voor verdachte activiteiten gegenereerd. U kunt deze informatie gebruiken om snel beveiligingsproblemen op te lossen en om de beveiliging van uw containers te verbeteren.

Security Center biedt beveiliging tegen bedreigingen op verschillende niveaus: 

* **Hostniveau (door Azure Defender voor servers)** : met dezelfde Log Analytics-agent die Security Center gebruikt op andere VM's, controleert Azure Defender uw Linux AKS-knooppunten op verdachte activiteiten zoals detectie van webshells en verbinding met bekende verdachte IP-adressen. De agent controleert ook op containerspecifieke analyses, zoals het maken van geprivilegieerde containers, verdachte toegang tot API-servers en SSH-servers (Secure Shell) die binnen een Docker-container worden uitgevoerd.

    Als u ervoor kiest om de agents niet op uw hosts te installeren, krijgt u maar een subset van de voordelen van bedreigingsbeveiliging en beveiligingswaarschuwingen. U ontvangt nog steeds waarschuwingen met betrekking tot netwerkanalyse en communicatie met schadelijke servers.

    >[!IMPORTANT]
    > Momenteel wordt de installatie van de Log Analytics-agent op Azure Kubernetes-Service clusters die worden uitgevoerd op schaal sets voor virtuele machines niet ondersteund.

    Zie de [Referentietabel met waarschuwingen](../articles/security-center/alerts-reference.md#alerts-containerhost) voor een lijst met waarschuwingen op AKS-hostniveau.


* **AKS-cluster niveau (verschaft door Azure Defender voor Kubernetes)** op clusterniveau is de beveiliging tegen bedreigingen gebaseerd op het analyseren van de auditlogboeken van Kubernetes. Schakel Azure Defender in om deze **agentloze** bewaking in te scha kelen. Om waarschuwingen op dit niveau te genereren, bewaakt Security Center uw met AKS beheerde services met behulp van de door AKS opgehaalde logboeken. Voorbeelden van gebeurtenissen op dit niveau zijn onder andere beschikbare Kubernetes-dashboards, het maken van rollen met hoge bevoegdheden en het maken van gevoelige koppelingen.

    >[!NOTE]
    > Security Center genereert beveiligingswaarschuwingen voor Azure Kubernetes Service-acties en -implementaties die worden uitgevoerd nadat de Kubernetes-optie is ingeschakeld in de abonnementsinstellingen. 

    Zie de [Referentietabel met waarschuwingen](../articles/security-center/alerts-reference.md#alerts-akscluster) voor een lijst met waarschuwingen op AKS-clusterniveau.

Bovendien houdt ons wereldwijde team van beveiligingsonderzoekers het bedreigingslandschap voortdurend in de gaten. Ze voegen containerspecifieke waarschuwingen en beveiligingsproblemen toe zodra ze worden gedetecteerd.

> [!TIP]
> U kunt containerwaarschuwingen simuleren door de instructies in [dit blogbericht](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270) te volgen.