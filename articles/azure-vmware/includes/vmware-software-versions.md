---
title: VMware-software versies
description: Ondersteunde VMware-software versies voor de Azure VMware-oplossing.
ms.topic: include
ms.date: 03/31/2021
ms.openlocfilehash: a6441b55bbc6a8f694c50bbf022a6a2ae52d60bf
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106097031"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->


De VMware-software versies die worden gebruikt in nieuwe implementaties van Azure VMware-oplossing persoonlijke Clouds clusters zijn:

| Software              |    Versie   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6,7 U3l    | 
| ESXi                  |    6,7 U3l    | 
| vSAN                  |    6,7 U3l    |
| NSX-T <br />**Opmerking:** NSX-T is de enige ondersteunde versie van NSX.               |      3.1.1     |


Nieuwe clusters worden toegevoegd aan een bestaande privécloud, de momenteel uitgevoerde software versie wordt toegepast. Zie voor meer informatie de vereisten voor de [VMware-software versie](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html).

Met de software bundel upgrades van de privécloud wordt de software binnen één versie van de meest recente software bundel versie van VMware bewaard. De software versies van de privécloud kunnen afwijken van de meest recente versies van de afzonderlijke software onderdelen (ESXi, NSX-T, vCenter, vSAN). U vindt het algemene upgrade beleid en de processen voor de Azure VMware Solution platform-software die wordt beschreven in [updates voor privécloud en upgrades](../concepts-upgrades.md).

