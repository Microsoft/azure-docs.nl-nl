---
title: Beveiligingscontroles voor Azure Spring Cloud Service
description: Gebruik beveiligings mechanismen die zijn ingebouwd in azure lente-Cloud service.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 62a0bd19f6b10bbe6561f5587ed85d4d1e5880b3
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878165"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Beveiligingscontroles voor Azure Spring Cloud Service

**Dit artikel is van toepassing op:** ✔️ Java ✔️ C#

Beveiligings controles zijn ingebouwd in azure lente-Cloud service.

Een beveiligings controle is een kwaliteit of functie van een Azure-service die bijdraagt aan de mogelijkheid van de service om beveiligings problemen te voor komen, te detecteren en op te lossen.  Voor elk besturings element gebruiken we *Ja* of *Nee* om aan te geven of deze momenteel aanwezig is voor de service.  We gebruiken *n.v.t.* voor een besturings element dat niet van toepassing is op de service. 

**Beveiligings controles voor gegevens beveiliging**

| Beveiligingsmaatregelen | Ja/Nee | Notities | Documentatie |
|:-------------|:-------|:-------------------------------|:----------------------|
| Versleuteling aan server zijde op rest: door micro soft beheerde sleutels | Ja | Door de gebruiker geüploade bron en artefacten, configuratie server instellingen, app-instellingen en gegevens in permanente opslag worden opgeslagen in Azure Storage, die de inhoud automatisch versleutelt in rust.<br><br>Config server cache, runtime binaire bestanden die zijn gebouwd op basis van de geüploade bron en toepassings logboeken tijdens de levens duur van de toepassing worden opgeslagen op Azure Managed disk, waarmee de inhoud automatisch wordt versleuteld in rust.<br><br>Container installatie kopieën die zijn gebaseerd op de door de gebruiker geüploade bron worden opgeslagen in Azure Container Registry, die de installatie kopie-inhoud automatisch versleutelt in rust. | [Azure Storage-versleuteling voor inactieve gegevens](../storage/common/storage-service-encryption.md)<br><br>[Versleuteling aan de server zijde van Azure Managed disks](../virtual-machines/disk-encryption.md)<br><br>[Opslag van container installatie kopieën in Azure Container Registry](../container-registry/container-registry-storage.md) |
| Versleuteling in tijdelijke | Ja | Open bare eind punten van gebruikers-apps gebruiken standaard HTTPS voor binnenkomend verkeer. |  |
| Versleutelde API-aanroepen | Ja | Beheer aanroepen voor het configureren van Azure lente-Cloud service vindt plaats via Azure Resource Manager-aanroepen via HTTPS. | [Azure Resource Manager](../azure-resource-manager/index.yml) |

**Beveiligings beheer van netwerk toegang**

| Beveiligingsmaatregelen | Ja/Nee | Notities | Documentatie |
|:-------------|:-------|:-------------------------------|:----------------------|
| Servicetag | Ja | Gebruik **AzureSpringCloud** service tag om uitgaande netwerk toegangs beheer voor [netwerk beveiligings groepen](../virtual-network/network-security-groups-overview.md#security-rules) of [Azure firewall](../firewall/service-tags.md)te definiëren om verkeer naar Azure lente-Cloud toepassingen toe te staan.<br><br>*Opmerking:* Momenteel alleen nieuwe Azure lente-Cloud service-exemplaar gemaakt na 2020/07/14 ondersteunt **AzureSpringCloud** -service label. | [Servicetags](../virtual-network/service-tags-overview.md) |

## <a name="next-steps"></a>Volgende stappen

* [Snelstart: Uw eerste Azure Spring Cloud-toepassing implementeren](spring-cloud-quickstart.md)