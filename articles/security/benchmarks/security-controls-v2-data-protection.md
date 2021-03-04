---
title: Azure Security Bench Mark v2-gegevens beveiliging
description: Azure Security Bench Mark v2-gegevens beveiliging
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 50358eed580bbd83f25386feb0068a252060672b
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037112"
---
# <a name="security-control-v2-data-protection"></a>Beveiligings controle v2: gegevens beveiliging

Gegevens bescherming is van toepassing op controle van gegevens beveiliging op rest, onderweg en via geautoriseerde toegangs mechanismen. Dit omvat het detecteren, classificeren, beveiligen en bewaken van gevoelige gegevens assets met behulp van toegangs beheer, versleuteling en logboek registratie in Azure.

Voor een overzicht van de toepasselijke ingebouwde Azure Policy raadpleegt u [de details van het ingebouwde initiatief Azure Security Bench Mark-naleving van voor Schriften: gegevens beveiliging](../../governance/policy/samples/azure-security-benchmark.md#data-protection)

## <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: Detectie, classificatie en labeling van gevoelige gegevens

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| DP-1 | 13,1, 14,5, 14,7 | SC-28 |

Ontdek, classificeer en voorzie uw gevoelige gegevens zodat u de juiste besturings elementen kunt ontwerpen om ervoor te zorgen dat gevoelige informatie wordt opgeslagen, verwerkt en veilig wordt verzonden door de technologie systemen van de organisatie.

Gebruik Azure Information Protection (en de bijbehorende scantool) voor gevoelige informatie binnen Office-documenten in Azure, on-premises, in Office 365 en op andere locaties.

U kunt Azure SQL Information Protection gebruiken bij het classificeren en labelen van informatie die is opgeslagen in Azure SQL-databases.

- [Gevoelige gegevens labelen met Azure Information Protection](/azure/information-protection/what-is-information-protection) 

- [Azure SQL-gegevensdetectie implementeren](../../azure-sql/database/data-discovery-and-classification-overview.md)

**Verantwoordelijkheid**: Gedeeld

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Toepassings beveiliging en DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gegevens beveiliging](/azure/cloud-adoption-framework/organize/cloud-security-data-security) 

- [Infrastructuur- en eindpuntbeveiliging](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-2-protect-sensitive-data"></a>DP-2: Gevoelige gegevens beschermen

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| DP-2 | 13,2, 2,10 | SC-7, AC-4 |

Beveilig gevoelige gegevens door de toegang te beperken met behulp van Azure RBAC (op rollen gebaseerd toegangs beheer), op netwerk gebaseerde toegangs controle en specifieke besturings elementen in Azure-Services (zoals versleuteling in SQL en andere data bases). 

Consistent toegangsbeheer is alleen mogelijk als alle typen toegangsbeheer zijn afgestemd op de segmentatiestrategie van uw bedrijf. De segmentatiestrategie voor uw bedrijf moet ook worden gebaseerd op de locatie van gevoelige of bedrijfskritieke gegevens en systemen.

Voor het onderliggende platform, dat wordt beheerd door Microsoft, geldt dat alle klantinhoud als gevoelig wordt beschouwd en dat gegevens worden beschermd tegen verlies en blootstelling. Om ervoor te zorgen dat klantgegevens veilig blijven binnen Azure, heeft Microsoft enkele standaardmaatregelen en -mechanismen voor gegevensbeveiliging geïmplementeerd.

- [Azure RBAC (op rollen gebaseerd toegangsbeheer van Azure)](../../role-based-access-control/overview.md)

- [Informatie over beveiliging van klantgegevens in Azure](../fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Gedeeld

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Toepassings beveiliging en DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Gegevens beveiliging](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

- [Infrastructuur- en eindpuntbeveiliging](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: Controleren of er niet-geautoriseerde overdrachten van gevoelige gegevens hebben plaatsgevonden

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| DP-3 | 13,3 | WISSELSTROOM-4, SI-4 |

Monitor voor niet-geautoriseerde overdracht van gegevens naar locaties buiten de zicht baarheid en controle van de onderneming. Het gaat hier dan meestal om het controleren op afwijkende activiteiten (grote of ongebruikelijke overdrachten) die kunnen wijzen op niet-geautoriseerde gegevensexfiltratie. 

Azure Storage Advanced Threat Protection (ATP) en Azure SQL ATP kunnen een waarschuwing geven voor een afwijkende overdracht van gegevens die kan wijzen op een niet-geautoriseerde overdracht van gevoelige informatie. 

Azure Information Protection (AIP) biedt controlevoorzieningen voor informatie die is geclassificeerd en gelabeld. 

Als dit nodig is voor de naleving van preventie van gegevensverlies (DLP), kunt u een DLP-oplossing op een host gebruiken om detectie en/of preventieve controles af te dwingen om gegevensexfiltratie te voorkomen.

- [Azure Defender voor SQL](../../azure-sql/database/azure-defender-for-sql.md)

- [Azure Defender voor Storage](../../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center)

**Verantwoordelijkheid**: Gedeeld

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Beveiligingsbewerkingen](/azure/cloud-adoption-framework/organize/cloud-security) 

- [Toepassings beveiliging en DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Infrastructuur- en eindpuntbeveiliging](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Gevoelige gegevens tijdens een overdracht versleutelen

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| DP-4 | 14.4 | SC-8 |

Ter aanvulling van de toegangs controle moeten gegevens in transit worden beschermd tegen buiten-band-aanvallen (zoals het vastleggen van verkeer) met behulp van versleuteling om ervoor te zorgen dat aanvallers de gegevens niet eenvoudig kunnen lezen of wijzigen.

Hoewel dit optioneel is voor verkeer op particuliere netwerken, is dit van cruciaal belang voor verkeer op externe en open bare netwerken. Voor HTTP-verkeer moet u ervoor zorgen dat clients die verbinding maken met uw Azure-resources, TLS v 1.2 of hoger kunnen onderhandelen. Voor extern beheer gebruikt u SSH (voor Linux) of RDP/TLS (voor Windows) in plaats van een niet-versleuteld protocol. Verouderde SSL-, TLS-en SSH-versies en-protocollen en zwakke cijfers moeten worden uitgeschakeld.

Azure biedt standaard versleuteling voor gegevens in transit tussen Azure-data centers.

- [Meer informatie over versleuteling in transit met Azure](../fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Informatie over TLS-beveiliging](/security/engineering/solving-tls1-problem)

- [Dubbele versleuteling voor Azure-gegevens in transit](../fundamentals/double-encryption.md#data-in-transit)

**Verantwoordelijkheid**: Gedeeld

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Beveiligingsarchitectuur](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Infrastructuur- en eindpuntbeveiliging](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Toepassings beveiliging en DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Gegevens beveiliging](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

## <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Gevoelige data-at-rest versleutelen

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| DP-5 | 14.8 | SC-28, SC-12 |

Ter aanvulling van de toegangs controle moeten gegevens in rust worden beschermd tegen buiten-band-aanvallen (zoals toegang tot onderliggende opslag) met behulp van versleuteling. Dit helpt ervoor te zorgen dat aanvallers de gegevens niet eenvoudig kunnen lezen of wijzigen. 

Azure biedt standaard versleuteling voor Data-at-rest. Voor zeer gevoelige gegevens beschikt u over opties voor het implementeren van extra versleuteling in rust op alle Azure-resources waar beschikbaar. Azure beheert standaard uw versleutelings sleutels, maar Azure biedt opties voor het beheren van uw eigen sleutels (door de klant beheerde sleutels) voor bepaalde Azure-Services.

- [Meer informatie over versleuteling van data-at-rest in Azure](../fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services)

- [Door de klant beheerde versleutelings sleutels configureren](../../storage/common/customer-managed-keys-configure-key-vault.md)

- [Versleutelings model en sleutel beheer tabel](../fundamentals/encryption-models.md)

- [Gegevens bij dubbele versleuteling in de rest van Azure](../fundamentals/double-encryption.md#data-at-rest)

**Verantwoordelijkheid**: Gedeeld

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Beveiligingsarchitectuur](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Infrastructuur- en eindpuntbeveiliging](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Toepassings beveiliging en DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gegevens beveiliging](/azure/cloud-adoption-framework/organize/cloud-security-data-security)