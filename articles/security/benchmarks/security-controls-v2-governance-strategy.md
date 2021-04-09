---
title: Azure Security Bench Mark v2-governance en strategie
description: Azure Security Bench Mark v2 governance en strategie
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 277033e41ec7e02b89eca8cf74fe6854acb51cc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727019"
---
# <a name="security-control-v2-governance-and-strategy"></a>Beveiligings controle v2: governance en strategie

Governance en strategie biedt richt lijnen voor het garanderen van een samenhangende beveiligings strategie en een gedocumenteerde governance aanpak om beveiligings verzekeringen te hand haven, inclusief het instellen van rollen en verantwoordelijkheden voor de verschillende Cloud beveiligings functies, uniforme technische strategie en ondersteunings beleid en-standaarden.

## <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Strategie voor asset-management en gegevensbescherming definiëren

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| GS-1 | 2, 13 | SC, AC |

Zorg ervoor dat u een duidelijke strategie documenteert en communiceert voor continue bewaking en bescherming van systemen en gegevens. Ken hogere prioriteiten toe aan de detectie, beoordeling, beveiliging en bewaking van bedrijfskritieke gegevens en systemen.

Deze strategie moet gedocumenteerde richtlijnen, beleid en standaarden bevatten voor de volgende elementen: 

- Standaard voor gegevensclassificatie in overeenstemming met de bedrijfsrisico's

- Inzicht van beveiligingsorganisaties in risico's en asset-inventaris

- Goedkeuring door beveiligingsorganisaties van Azure-services voor gebruik

- Beveiliging van assets op grond van hun levenscyclus

- Vereiste strategie voor toegangsbeheer in overeenstemming met gegevensclassificatie van organisatie

- Gebruik van voorzieningen voor gegevensbescherming van Azure en derden

- Vereisten voor gegevensversleutelings van gegevens in-transit en at-rest

- Juiste cryptografische standaarden

Raadpleeg de volgende bronnen voor meer informatie:
- [Azure Security Architecture Recommendation - Storage, data, and encryption](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json) (Aanbeveling voor Azure-beveiligingsarchitectuur: opslag, gegevens en versleuteling)

- [Azure Security Fundamentals - Azure Data security, encryption, and storage](../fundamentals/encryption-overview.md) (Basisprincipes van Azure-beveiliging: Azure-gegevensbeveiliging, -versleuteling en -opslag)

- [Cloud Adoption Framework - Azure data security and encryption best practices](../fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json) (Cloud Adoption Framework: best practices voor Azure-gegevensbeveiliging en -versleuteling)

- [Azure Security Benchmark - Asset management](security-controls-v2-asset-management.md) (Azure Security Benchmark: assetmanagement)

- [Azure Security Benchmark - Data Protection](security-controls-v2-data-protection.md) (Azure Security Benchmark: gegevensbeveiliging)

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Alle belanghebbenden](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Segmentatiestrategie van bedrijf definiëren

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| GS-2 | 4, 9, 16 | AC, CA, SC |

Stel een bedrijfs strategie in om de toegang tot assets te segmenteren met een combi natie van identiteits-, netwerk-, toepassings-, abonnements-, beheer groep-en andere besturings elementen.

Zorg dat u een nauwgezette afweging maakt tussen de noodzaak om de beveiliging van de rest te scheiden en de noodzaak om systemen die met elkaar moeten kunnen communiceren en toegang moeten hebben tot gegevens, in staat te stellen om hun dagelijkse werklast uit te voeren.

Zorg ervoor dat de segmentatiestrategie consistent wordt geïmplementeerd voor alle typen besturingselementen, zoals voor netwerkbeveiliging, identiteits- en toegangsmodellen, toepassingsbevoegdheden/toegangsmodellen evenals besturingselementen voor door mensen uitgevoerde processen.

- [Richtlijnen voor segmentatiestrategie in Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Richtlijnen voor segmentatiestrategie in Azure (document)](/security/compass/governance#enterprise-segmentation-strategy)

- [Netwerksegmentatie afstemmen op segmentatiestrategie van bedrijf](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Alle belanghebbenden](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Strategie voor het beheer van beveiligingspostuur definiëren

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| GS-3 | 20, 3, 5 | RA, CM, SC |

U kunt de Risico's voor uw afzonderlijke assets en de omgeving die ze hosten, voortdurend meten en beperken. Ken hogere prioriteiten toe aan hoogwaardige assets en assets die zeer kwetsbaar zijn voor aanvallen, zoals gepubliceerde toepassingen, punten voor binnenkomend en uitgaand netwerkverkeer, gebruikers- en beheerderseindpunten enzovoort.

- [Azure Security Benchmark: beveiligingspostuur en beveiligingsproblemen beheren](security-controls-v2-posture-vulnerability-management.md)

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Alle belanghebbenden](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Organisatierollen, verantwoordelijkheden en aansprakelijkheden afstemmen

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| GS-4 | N.v.t. | PL, PM |

Zorg ervoor dat u een duidelijke strategie voor rollen en verantwoordelijkheden in uw beveiligings organisatie documenteert en communiceert. Ken een hoge prioriteiten toe aan het verschaffen van duidelijkheid over wie aansprakelijk is voor beveiligingsbeslissingen. Bied opleidingen aan iedereen over het gedeelde verantwoordelijkheidsmodel en biedt technische teams trainingen over technologie ter beveiliging van de cloud.

- [Best practice voor Azure-beveiliging 1: personen: Teams trainen inzake het cloudbeveiligingstraject](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Best practice voor Azure-beveiliging 2: personen: Teams trainen inzake cloudbeveiligingstechnologie](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure Security Best Practice 3 - proces: Aansprakelijkheid toewijzen voor beslissingen over cloudbeveiliging](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Alle belanghebbenden](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-5-define-network-security-strategy"></a>GS-5: Strategie voor netwerkbeveiliging definiëren

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| GS-5 | 9 | CA, SC |

Stel een Azure-netwerk beveiligings benadering in als onderdeel van de algehele strategie voor het toegangs beheer van uw organisatie.

Deze strategie moet gedocumenteerde richtlijnen, beleid en standaarden bevatten voor de volgende elementen:

- Gecentraliseerd netwerkbeheer en verantwoordelijkheid voor beveiliging

- Model voor segmentatie van virtueel netwerk afgestemd op segmentatiestrategie van bedrijf

- Herstelstrategie voor verschillende scenario's met bedreigingen en aanvallen

- Strategie voor internetrand en inkomend en uitgaand verkeer

- Strategie voor hybride cloud- en on-premises interconnectiviteit

- Up-to-date netwerk beveiligings artefacten (zoals netwerk diagrammen, referentie netwerk architectuur)

Raadpleeg de volgende bronnen voor meer informatie:

- [Azure Security Best Practice 11: architectuur. Eén uniforme beveiligingsstrategie](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark: netwerkbeveiliging](security-controls-v2-network-security.md)

- [Overzicht van Azure-netwerkbeveiliging](../fundamentals/network-overview.md)

- [Strategie voor architectuur van bedrijfsnetwerk](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Alle belanghebbenden](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Strategie voor het gebruik van identiteiten en uitgebreide toegang definiëren

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| GS-6 | 16, 4 | AC, AU, SC |

Stel een Azure Identity and privileged Access benaderingen in als onderdeel van de algehele strategie voor beveiligings beheer van uw organisatie.

Deze strategie moet gedocumenteerde richtlijnen, beleid en standaarden bevatten voor de volgende elementen:

- Een gecentraliseerd identiteits- en verificatiesysteem en bijbehorende interconnectiviteit met andere interne en externe identiteitssystemen

- Krachtige verificatiemethoden in verschillende gebruiksscenario's en onder verschillende voorwaarden

- Bescherming van gebruikers met zeer uitgebreide bevoegdheden

- Bewaking en verwerking van afwijkende gebruikersactiviteiten

- Proces voor het beoordelen en op elkaar afstemmen van de identiteit en toegangsrechten van gebruikers

Raadpleeg de volgende bronnen voor meer informatie:

- [Azure Security Benchmark: Identity management](security-controls-v2-identity-management.md) (Azure Security Benchmark: identiteitsbeheer)

- [Azure Security Benchmark - Privileged access](security-controls-v2-privileged-access.md) (Azure Security Benchmark: uitgebreide toegang)

- [Azure Security Best Practice 11: architectuur. Eén uniforme beveiligingsstrategie](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Overzicht van beveiliging met Azure-identiteitsbeheer](../fundamentals/identity-management-overview.md)

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Alle belanghebbenden](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Strategie voor logboekregistratie en respons op bedreigingen definiëren

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| GS-7 | 19 | IR, AU, RA, SC |

Stel een strategie voor logboek registratie en reactie op bedreigingen in om bedreigingen snel te detecteren en op te lossen tijdens het voldoen aan nalevings vereisten. Stel prioriteiten om analisten te voorzien van waarschuwingen van hoge kwaliteit en naadloze ervaringen, zodat ze zich kunnen concentreren op bedreigingen in plaats van integratie en handmatige stappen. 

Deze strategie moet gedocumenteerde richtlijnen, beleid en standaarden bevatten voor de volgende elementen: 

- De rol en verantwoordelijkheden van de organisatie voor beveiligings bewerkingen (SecOps) 

- Een goed gedefinieerd proces voor het reageren op incidenten dat is afgestemd op het NIST of een ander framework binnen de branche 

- Logboekregistratie en retentie om ondersteuning te bieden voor de detectie van bedreigingen, het reageren op incidenten en het naleven van regelgeving

- Informatie en correlatiegegevens over bedreigingen die centraal beschikbaar zijn via SIEM, systeemeigen Azure-mogelijkheden en andere bronnen 

- Communicatie- en meldingenplan met uw klanten, leveranciers en openbaar belanghebbenden

- Gebruik van systeemeigen Azure-platforms en platforms van derden voor het afhandelen van incidenten, zoals logboekregistratie en detectie van bedreigingen, forensische onderzoeken en het oplossen en uitschakelen van aanvallen

- Processen voor het afhandelen van incidenten en activiteiten na incidenten, zoals geleerde lessen en het bewaren van bewijsmateriaal

Raadpleeg de volgende bronnen voor meer informatie:
- [Azure Security Benchmark: logboekregistratie en detectie van bedreigingen](security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark: respons op incidenten](security-controls-v2-incident-response.md)

- [Azure Security Best Practice 4: proces. Processen voor respons op incidenten bijwerken voor de cloud](/azure/cloud-adoption-framework/security/security-top-10#3-process-assign-accountability-for-cloud-security-decisions)

- [Handleiding framework voor Azure-acceptatie, logboekregistratie en rapportagebeslissingen](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Schaalaanpassing, beheer en bewaking van Azure Enterprise](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Alle belanghebbenden](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-8-define-backup-and-recovery-strategy"></a>GS-8: de strategie voor back-up en herstel bepalen

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| GS-8 | 10 | CP (consistentie en partitietolerantie) |

Stel een Azure-strategie voor back-up en herstel in voor uw organisatie. 

Deze strategie moet gedocumenteerde richtlijnen, beleid en standaarden bevatten voor de volgende elementen: 

- Definities van de beoogde herstel tijd (RTO) en Recovery Point Objective (RPO) in overeenstemming met uw bedrijfs tolerantie doelstellingen

- Het ontwerp van redundantie in uw toepassingen en de configuratie van de infra structuur

- Beveiliging van back-ups met behulp van toegangs beheer en gegevens versleuteling

Raadpleeg de volgende bronnen voor meer informatie:
- [Azure Security Bench Mark-back-up en herstel](security-controls-v2-backup-recovery.md)

- [Azure Well-Architecture Framework: back-up en herstel na nood gevallen voor Azure-toepassingen](/azure/architecture/framework/resiliency/backup-and-recovery)

- [Azure-acceptatie Framework-bedrijfs continuïteit en herstel na nood gevallen](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Alle belanghebbenden](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)