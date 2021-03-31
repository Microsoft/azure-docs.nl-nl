---
title: Integriteit van Azure-infra structuur
description: Meer informatie over integriteit van Azure-infra structuur en de stappen die micro soft nodig heeft om deze te beveiligen, zoals virus scans op software component builds.
services: security
documentationcenter: na
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
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: b0df14dfcf66f53e01989a27a95afa5e19fcb2fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87543636"
---
# <a name="azure-infrastructure-integrity"></a>Integriteit van Azure-infra structuur

## <a name="software-installation"></a>Software-installatie
Alle onderdelen in de software stack die in de Azure-omgeving zijn geïnstalleerd, worden aangepast op basis van het proces micro soft Security Development Lifecycle (SDL). Alle software onderdelen, waaronder installatie kopieën van besturings systemen en SQL Database, worden geïmplementeerd als onderdeel van het proces voor wijzigings beheer en release beheer. Het besturings systeem dat op alle knoop punten wordt uitgevoerd, is een aangepaste versie van Windows Server 2008 of Windows Server 2012. De exacte versie wordt gekozen door de infrastructuur controller (FC) op basis van de rol die het besturings systeem wil afspelen. Daarnaast staat het besturings systeem van de host niet toe dat er onbevoegde software onderdelen worden geïnstalleerd.

Sommige Azure-onderdelen worden als Azure-klanten geïmplementeerd op een gast-VM die wordt uitgevoerd op een gast besturingssysteem.

## <a name="virus-scans-on-builds"></a>Virus scans op builds
De builds van het Azure-software onderdeel (inclusief OS) moeten een virus scan ondergaan die gebruikmaakt van het Endpoint Protection Antivirus programma. Met elke virus scan wordt een logboek gemaakt in de bijbehorende build-map, waarin wordt beschreven wat er is gescand en wat de resultaten van de scan zijn. De virus scan maakt deel uit van de bron code van de build voor elk onderdeel in Azure. Code wordt niet verplaatst naar productie zonder een schone en geslaagde virus scan. Als er problemen worden opgemerkt, wordt de build bevroren en gaat u vervolgens naar de beveiligings teams in micro soft-beveiliging om te bepalen waar de "Rogue" code van de build is ingevoerd.

## <a name="closed-and-locked-environment"></a>Gesloten en vergrendelde omgeving
Standaard hebben er geen gebruikers accounts voor Azure-infrastructuur knooppunten en gast-Vm's gemaakt. Daarnaast worden standaard Windows-beheerders accounts ook uitgeschakeld. Beheerders van Azure Live-ondersteuning kunnen, met de juiste verificatie, zich aanmelden bij deze machines en het Azure-productie netwerk beheren voor nood herstel.

## <a name="azure-sql-database-authentication"></a>Azure SQL Database-verificatie
Net als bij elke implementatie van SQL Server moet het beheer van gebruikers accounts nauw keurig worden beheerd. Azure SQL Database ondersteunt alleen SQL Server-verificatie. Om het gegevens beveiligings model van een klant aan te vullen, moeten gebruikers accounts met sterke wacht woorden worden gebruikt en moeten ze ook worden geconfigureerd met specifieke rechten.

## <a name="acls-and-firewalls-between-the-microsoft-corporate-network-and-an-azure-cluster"></a>Acl's en firewalls tussen het bedrijfs netwerk van micro soft en een Azure-cluster
Toegangs beheer lijsten (Acl's) en firewalls tussen het service platform en het micro soft Corporate Network beveiligen SQL Database exemplaren van toegang voor niet-geautoriseerde Insider. Verder hebben alleen gebruikers van IP-adresbereiken van het micro soft-bedrijfs netwerk toegang tot het eind punt van het Windows Fabric platform-beheer.

## <a name="acls-and-firewalls-between-nodes-in-a-sql-database-cluster"></a>Acl's en firewalls tussen knoop punten in een SQL Database cluster
Als extra beveiliging, als onderdeel van de ingrijpende diep gaande strategie, Acl's en een firewall zijn geïmplementeerd tussen knoop punten in een SQL Database cluster. Alle communicatie binnen het Windows Fabric platform cluster en alle actieve code wordt vertrouwd.

## <a name="custom-monitoring-agents"></a>Aangepaste bewakings agenten
SQL Database maakt gebruik van aangepaste bewakings agenten (MAs), ook wel watchdog genoemd, om de status van het SQL Database cluster te controleren.

## <a name="web-protocols"></a>Webprotocols

### <a name="role-instance-monitoring-and-restart"></a>Bewaking van rolinstantie en opnieuw starten
Azure zorgt ervoor dat alle geïmplementeerde, uitgevoerde rollen (Internet gerichte web-of back-end-werk rollen) onderhevig zijn aan de status controle, om ervoor te zorgen dat ze effectief en efficiënt de services leveren waarvoor ze zijn ingericht. Als een rol een slechte status krijgt, wordt door een kritieke fout in de toepassing die wordt gehost of een onderliggend configuratie probleem binnen de rolinstantie zelf, de FC het probleem in de rolinstantie gedetecteerd en wordt een herstel status geïnitieerd.

### <a name="compute-connectivity"></a>Verbinding voor compute
Azure zorgt ervoor dat de geïmplementeerde toepassing of service bereikbaar is via standaard protocollen op het web. Virtuele exemplaren van Internet gerichte webrollen hebben een externe Internet verbinding en zijn rechtstreeks bereikbaar voor webgebruikers. Voor het beveiligen van de gevoeligheid en integriteit van de bewerkingen die werk rollen uitvoeren namens de op openbaar toegankelijke webrollen gebaseerde virtuele instanties, hebben virtuele exemplaren van werk rollen van de back-upverwerking externe Internet verbinding, maar ze kunnen niet rechtstreeks worden geopend door externe webgebruikers.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over de mogelijkheden van micro soft voor het beveiligen van de Azure-infra structuur:

- [Azure-faciliteiten,-locaties en fysieke beveiliging](physical-security.md)
- [Beschik baarheid van Azure-infra structuur](infrastructure-availability.md)
- [Azure Information System-onderdelen en-grenzen](infrastructure-components.md)
- [Azure-netwerk architectuur](infrastructure-network.md)
- [Productie netwerk van Azure](production-network.md)
- [Azure SQL Database beveiligings functies](infrastructure-sql.md)
- [Azure-productie bewerkingen en-beheer](infrastructure-operations.md)
- [Bewaking van Azure-infra structuur](infrastructure-monitoring.md)
- [Azure-klant gegevens beveiliging](protection-customer-data.md)
