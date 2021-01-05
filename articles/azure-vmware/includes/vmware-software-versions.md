---
title: VMware-software versies
description: Ondersteunde VMware-software versies voor de Azure VMware-oplossing.
ms.topic: include
ms.date: 12/29/2020
ms.openlocfilehash: c6ba2904bab6c6f44001cafed1bd4cbdeb786373
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825094"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->


De huidige software versies van de VMware-software die worden gebruikt in azure VMware-oplossingen persoonlijke Cloud clusters zijn:

| Software              |    Versie   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6,7 U3    | 
| ESXi                  |    6,7 U3    | 
| vSAN                  |    6,7 U3    |
| NSX-T                 |      2.5     |


>[!NOTE]
>NSX-T is de enige ondersteunde versie van NSX.

Voor elk nieuw cluster in een privécloud komt de software versie overeen met wat momenteel wordt uitgevoerd. Voor elke nieuwe privécloud in een abonnement wordt de meest recente versie van de software stack geïnstalleerd. Zie voor meer informatie de vereisten voor de [VMware-software versie](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html).

Met de software bundel upgrades van de privécloud wordt de software binnen één versie van de meest recente software bundel versie van VMware bewaard. De software versies van de privécloud kunnen afwijken van de meest recente versies van de afzonderlijke software onderdelen (ESXi, NSX-T, vCenter, vSAN). U vindt het algemene upgrade beleid en de processen voor de Azure VMware Solution platform-software die wordt beschreven in [updates voor privécloud en upgrades](../concepts-upgrades.md).

