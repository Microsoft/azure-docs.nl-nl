---
title: Azure Security Bench Mark v2-gegevens beveiliging
description: Azure Security Bench Mark v2-gegevens beveiliging
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8e12a74ee689ba7b013b1c4a6881deac78680be0
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701045"
---
# <a name="security-control-v2-data-protection"></a>Beveiligings controle v2: gegevens beveiliging

Gegevens bescherming is van toepassing op controle van gegevens beveiliging op rest, onderweg en via geautoriseerde toegangs mechanismen. Dit omvat het detecteren, classificeren, beveiligen en bewaken van gevoelige gegevens assets met behulp van toegangs beheer, versleuteling en logboek registratie in Azure.

## <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: detectie, classificeren en label gevoelige gegevens

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| DP-1 | 13,1, 14,5, 14,7 | SC-28 |

Ontdek, classificeer en voorzie uw gevoelige gegevens zodat u de juiste besturings elementen kunt ontwerpen om ervoor te zorgen dat gevoelige informatie wordt opgeslagen, verwerkt en veilig wordt verzonden door de technologie systemen van de organisatie. 

Gebruik Azure Information Protection (en de bijbehorende Scan Tool) voor gevoelige informatie in Office-documenten op Azure, on-premises, op Office 365 en op andere locaties. 

U kunt Azure SQL Information Protection gebruiken om te helpen bij het classificeren en labelen van informatie die is opgeslagen in Azure SQL-data bases.

- [Label gevoelige informatie met behulp van Azure Information Protection](/azure/information-protection/what-is-information-protection) 

- [Azure SQL-gegevens detectie implementeren](../../azure-sql/database/data-discovery-and-classification-overview.md)

**Verantwoordelijkheid**: gedeeld

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Toepassings beveiliging en DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)  

- [Gegevens beveiliging](/azure/cloud-adoption-framework/organize/cloud-security-data-security) 

- [Infrastructuur- en eindpuntbeveiliging](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-2-protect-sensitive-data"></a>DP-2: gevoelige gegevens beveiligen

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| DP-2 | 13,2, 2,10 | SC-7, AC-4 |

Beveilig gevoelige gegevens door de toegang te beperken met behulp van Azure RBAC (op rollen gebaseerd toegangs beheer), op netwerk gebaseerde toegangs controle en specifieke besturings elementen in Azure-Services (zoals versleuteling in SQL en andere data bases). 

Om ervoor te zorgen dat het toegangs beheer consistent is, moeten alle typen toegangs beheer worden afgestemd op de strategie van uw bedrijfs segmentatie. De strategie voor bedrijfs segmentatie moet ook worden geïnformeerd over de locatie van gevoelige of bedrijfs kritieke gegevens en systemen.

Voor het onderliggende platform, dat wordt beheerd door micro soft, behandelt micro soft alle klant inhoud als gevoelig en bescherming tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure beveiligd blijven, heeft micro soft enkele standaard besturings elementen voor gegevens bescherming geïmplementeerd.

- [Azure RBAC (op rollen gebaseerd toegangsbeheer van Azure)](../../role-based-access-control/overview.md)

- [Informatie over beveiliging van klanten in azure](../fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: gedeeld

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Toepassings beveiliging en DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Gegevens beveiliging](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

- [Infrastructuur- en eindpuntbeveiliging](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: controleren op niet-geautoriseerde overdracht van gevoelige gegevens

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| DP-3 | 13,3 | WISSELSTROOM-4, SI-4 |

Monitor voor niet-geautoriseerde overdracht van gegevens naar locaties buiten de zicht baarheid en controle van de onderneming. Dit omvat doorgaans bewaking voor afwijkende activiteiten (grote of ongebruikelijke overdrachten) die kunnen wijzen op niet-geautoriseerde gegevens exfiltration. 

Azure Storage Advanced Threat Protection (ATP) en Azure SQL ATP kunnen een waarschuwing geven over afwijkende overdracht van informatie die kan wijzen op niet-geautoriseerde overdracht van gevoelige informatie. 

Azure Information Protection (beheerders rechten) biedt bewakings mogelijkheden voor informatie die is geclassificeerd en gelabeld. 

Als dat nodig is voor de naleving van preventie van gegevens verlies (DLP), kunt u een DLP-oplossing op basis van een host gebruiken om detectie en/of preventieve controles af te dwingen om gegevens exfiltration te voor komen.

- [Azure SQL-ATP inschakelen](../../azure-sql/database/threat-detection-overview.md)

- [Azure Storage ATP inschakelen](../../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center)

**Verantwoordelijkheid**: gedeeld

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Beveiligings bewerkingen](/azure/cloud-adoption-framework/organize/cloud-security) 

- [Toepassings beveiliging en DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Infrastructuur- en eindpuntbeveiliging](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: gevoelige gegevens tijdens de overdracht versleutelen

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| DP-4 | 14,4 | SC-8 |

Ter aanvulling van de toegangs controle moeten gegevens die onderweg zijn, worden beschermd tegen buiten-band-aanvallen (zoals het vastleggen van verkeer) met behulp van versleuteling om ervoor te zorgen dat aanvallers de gegevens niet eenvoudig kunnen lezen of wijzigen. 

Hoewel dit optioneel is voor verkeer op particuliere netwerken, is dit van cruciaal belang voor verkeer op externe en open bare netwerken. Voor HTTP-verkeer moet u ervoor zorgen dat clients die verbinding maken met uw Azure-resources, TLS v 1.2 of hoger kunnen onderhandelen. Voor extern beheer gebruikt u SSH (voor Linux) of RDP/TLS (voor Windows) in plaats van een niet-versleuteld protocol. Verouderde SSL-, TLS-en SSH-versies en-protocollen en zwakke cijfers moeten worden uitgeschakeld.  

Azure biedt standaard versleuteling voor gegevens in transit tussen Azure-data centers. 

- [Meer informatie over versleuteling in transit met Azure](../fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Informatie over TLS-beveiliging](/security/engineering/solving-tls1-problem)

- [Dubbele versleuteling voor Azure-gegevens in transit](../fundamentals/double-encryption.md#data-in-transit)

**Verantwoordelijkheid**: gedeeld

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Beveiligingsarchitectuur](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Infrastructuur- en eindpuntbeveiliging](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Toepassings beveiliging en DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Gegevens beveiliging](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

## <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: gevoelige gegevens in rust versleutelen

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| DP-5 | 14,8 | SC-28, SC-12 |

Ter aanvulling van de toegangs controle moeten gegevens in rust worden beschermd tegen buiten-band-aanvallen (zoals toegang tot onderliggende opslag) met behulp van versleuteling. Dit helpt ervoor te zorgen dat aanvallers de gegevens niet eenvoudig kunnen lezen of wijzigen. 

Azure biedt standaard versleuteling voor Data-at-rest. Voor zeer gevoelige gegevens beschikt u over opties voor het implementeren van extra versleuteling in rust op alle Azure-resources waar beschikbaar. Azure beheert standaard uw versleutelings sleutels, maar Azure biedt opties voor het beheren van uw eigen sleutels (door de klant beheerde sleutels) voor bepaalde Azure-Services.

- [Meer informatie over versleuteling in de rest van Azure](../fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services)

- [Door de klant beheerde versleutelings sleutels configureren](../../storage/common/customer-managed-keys-configure-key-vault.md)

- [Versleutelings model en sleutel beheer tabel](../fundamentals/encryption-models.md)

- [Gegevens bij dubbele versleuteling in de rest van Azure](../fundamentals/double-encryption.md#data-at-rest)

**Verantwoordelijkheid**: gedeeld

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Beveiligingsarchitectuur](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Infrastructuur- en eindpuntbeveiliging](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Toepassings beveiliging en DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gegevens beveiliging](/azure/cloud-adoption-framework/organize/cloud-security-data-security)