---
title: Beheer van het productie netwerk van Azure-Microsoft Azure
description: In dit artikel wordt beschreven hoe micro soft het productie netwerk van Azure beheert en exploiteert om de Azure-data centers te beveiligen.
services: security
documentationcenter: n
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2019
ms.author: terrylan
ms.openlocfilehash: d41fe409b4a44a4c2af3670d76dd3a83a300feae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "68727117"
---
# <a name="management-and-operation-of-the-azure-production-network"></a>Beheer en werking van het productie netwerk van Azure    
In dit artikel wordt beschreven hoe micro soft het productie netwerk van Azure beheert en exploiteert om de Azure-data centers te beveiligen.

## <a name="monitor-log-and-report"></a>Bewaken, registreren en rapporteren

Het beheer en de werking van het productie netwerk van Azure is een gecoördineerde inspanning tussen de Operations-teams van Azure en Azure SQL Database. De teams gebruiken verschillende hulpprogram ma's voor prestatie controle van systeem en toepassingen in de omgeving. En gebruiken ze de juiste hulpprogram ma's voor het bewaken van netwerk apparaten, servers, services en toepassings processen.

Om ervoor te zorgen dat de services die worden uitgevoerd in de Azure-omgeving veilig worden uitgevoerd, kunnen de Operations-teams meerdere bewakings-, logboek registratie-en rapportage niveaus implementeren, met inbegrip van de volgende acties:

- In het hoofd zakelijk verzamelt micro soft Monitoring Agent (MMA) bewakings-en diagnostische logboek gegevens van vele locaties, waaronder de infrastructuur controller (FC) en het basis besturingssysteem (OS), en schrijft u deze naar logboek bestanden. De agent duwt uiteindelijk een gedigesteerde subset van de gegevens naar een vooraf geconfigureerd Azure-opslag account. Daarnaast leest de beknopte bewerkings-en diagnostische service verschillende bewakings-en diagnostische logboek gegevens en geeft deze een overzicht van de informatie. De bewakings-en diagnostische service schrijft de gegevens naar een geïntegreerd logboek. Azure maakt gebruik van de aangepaste Azure-beveiligings bewaking, een uitbrei ding van het Azure-bewakings systeem. Het bevat onderdelen voor het observeren, analyseren en rapporteren van gebeurtenissen die relevant zijn voor beveiliging van verschillende punten in het platform.

- Het Azure SQL Database Windows Fabric-platform biedt beheer, implementatie, ontwikkeling en operationele toezicht Services voor Azure SQL Database. Het platform biedt gedistribueerde, implementatie services voor meerdere stappen, status controle, automatisch herstellen en compatibiliteit met Service versies. Het biedt de volgende services:

   - Mogelijkheden voor service modellering met een hoogwaardige ontwikkel omgeving (datacenter clusters zijn duur en schaars).
   - Implementatie-en upgrade werk stromen met één klik voor service-Boots trap en onderhoud.
   - Status rapportage met geautomatiseerde reparatie werk stromen om zelf herstel mogelijk te maken.
   - Real-time bewaking, waarschuwingen en fout opsporing van faciliteiten op de knoop punten van een gedistribueerd systeem.
   - Gecentraliseerde verzameling van operationele gegevens en gegevens voor gedistribueerde analyse van de hoofd oorzaak en service Insight.
   - Operationele hulp middelen voor implementatie, wijzigings beheer en bewaking.
   - De Azure SQL Database Windows Fabric platform-en watchdog-scripts worden doorlopend uitgevoerd en in realtime gecontroleerd.

Als er afwijkingen optreden, wordt het reactie proces van incidenten gevolgd door het Azure incident sorteren-team geactiveerd. Het juiste ondersteunings personeel van Azure wordt op de hoogte gesteld van het incident. Het bijhouden en oplossen van problemen wordt gedocumenteerd en beheerd in een gecentraliseerd ticket systeem. Metrische gegevens over de uptime van het systeem zijn beschikbaar onder de GEHEIMHOUDINGs overeenkomst (Non-Disclosure Agreement) en op aanvraag.

## <a name="corporate-network-and-multi-factor-access-to-production"></a>Bedrijfs netwerk en multi-factor Access to Production
De gebruikers basis van het bedrijfs netwerk bevat ondersteunings personeel voor Azure. Het bedrijfs netwerk ondersteunt interne bedrijfs functies en biedt toegang tot interne toepassingen die worden gebruikt voor Azure-klanten ondersteuning. Het bedrijfs netwerk is zowel logisch als fysiek gescheiden van het productie netwerk van Azure. Azure-personeel heeft toegang tot het bedrijfs netwerk met behulp van Azure-werk stations en laptops. Alle gebruikers moeten een Azure Active Directory (Azure AD)-account, met inbegrip van een gebruikers naam en wacht woord, hebben om toegang te krijgen tot bedrijfs netwerk bronnen. Toegang tot het bedrijfs netwerk maakt gebruik van Azure AD-accounts die worden verleend aan alle mede werkers, aannemers en leveranciers van micro soft en worden beheerd door micro soft Information Technology. Unieke gebruikers-id's onderscheiden mede werkers op basis van hun werkgelegenheids status bij micro soft.

Toegang tot interne Azure-toepassingen wordt gecontroleerd via verificatie met Active Directory Federation Services (AD FS). AD FS is een service die wordt gehost door micro soft Information Technology en die verificatie van zakelijke netwerk gebruikers biedt via het Toep assen van een beveiligde token en gebruikers claims. AD FS kunnen interne Azure-toepassingen gebruikers verifiëren voor het micro soft-bedrijfs Active Directory-domein. Gebruikers moeten zich verifiëren met behulp van multi-factor Authentication om toegang te krijgen tot het productie netwerk vanuit de bedrijfs netwerk omgeving.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over de mogelijkheden van micro soft voor het beveiligen van de Azure-infra structuur:

- [Azure-faciliteiten,-locaties en fysieke beveiliging](physical-security.md)
- [Beschik baarheid van Azure-infra structuur](infrastructure-availability.md)
- [Azure Information System-onderdelen en-grenzen](infrastructure-components.md)
- [Azure-netwerk architectuur](infrastructure-network.md)
- [Productie netwerk van Azure](production-network.md)
- [Azure SQL Database beveiligings functies](infrastructure-sql.md)
- [Bewaking van Azure-infra structuur](infrastructure-monitoring.md)
- [Integriteit van Azure-infra structuur](infrastructure-integrity.md)
- [Azure-klant gegevens beveiliging](protection-customer-data.md)
