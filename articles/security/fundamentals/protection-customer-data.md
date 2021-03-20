---
title: Beveiliging van klant gegevens in azure
description: Meer informatie over hoe Azure klant gegevens beveiligt via gegevens scheiding, gegevens redundantie en vernietiging van gegevens.
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
ms.date: 07/10/2020
ms.author: terrylan
ms.openlocfilehash: 14589e4efe22d89468b069bf6ff7e3d9babcc714
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87543789"
---
# <a name="azure-customer-data-protection"></a>Azure-klant gegevens beveiliging   
De toegang tot klant gegevens door micro soft Operations en het ondersteunings personeel wordt standaard geweigerd. Wanneer er toegang wordt verleend tot gegevens met betrekking tot een ondersteunings aanvraag, wordt deze alleen verleend met behulp van een just-in-time-model (JIT) met behulp van beleids regels die worden gecontroleerd en gecontroleerd aan de hand van het nalevings-en privacybeleid beleid.  De vereisten voor toegangs beheer worden vastgesteld door het volgende Azure-beveiligings beleid:

- Standaard geen toegang tot klant gegevens.
- Geen gebruikers-of beheerders accounts op virtuele machines van de klant (Vm's).
- Verleen de minimale bevoegdheid die vereist is voor het volt ooien van de taak; toegangs aanvragen controleren en registreren.

Ondersteunings medewerkers van Azure krijgen unieke zakelijke Active Directory accounts toegewezen door micro soft. Azure is afhankelijk van micro soft Corporate Active Directory, beheerd door micro soft Information Technology (MSIT), om de toegang tot belang rijke informatie systemen te beheren. Multi-factor Authentication is vereist en toegang wordt alleen verleend vanuit beveiligde consoles.

Alle toegangs pogingen worden gecontroleerd en kunnen worden weer gegeven via een eenvoudige set rapporten.

## <a name="data-protection"></a>Gegevensbeveiliging
Azure biedt klanten krachtige gegevens beveiliging, zowel standaard als klant opties.

**Gegevens** scheiding: Azure is een service met meerdere tenants, wat betekent dat meerdere klant implementaties en virtuele machines op dezelfde fysieke hardware zijn opgeslagen. Azure gebruikt logische isolatie om de gegevens van elke klant te scheiden van de gegevens van anderen. Schei ding biedt de schaal en de economische voor delen van multi tenant Services, terwijl klanten de gegevens van elkaar niet kunnen openen.

**At-rest-gegevens beveiliging**: klanten moeten ervoor zorgen dat gegevens die zijn opgeslagen in azure, worden versleuteld volgens hun normen. Azure biedt een breed scala aan versleutelings mogelijkheden en geeft klanten de flexibiliteit om de oplossing te kiezen die het beste voldoet aan hun behoeften. Azure Key Vault helpt klanten om de controle over de sleutels die door Cloud toepassingen en-services worden gebruikt, gemakkelijk te beheren om gegevens te versleutelen. Met Azure Disk Encryption kunnen klanten virtuele machines versleutelen. Azure Storage service versleuteling maakt het mogelijk om alle gegevens te versleutelen die in het opslag account van een klant worden geplaatst.

**In-transit gegevens bescherming**: micro soft biedt een aantal opties die door klanten kunnen worden gebruikt voor het intern beveiligen van gegevens in het Azure-netwerk en extern via internet naar de eind gebruiker.  Hieronder vallen communicatie via virtuele particuliere netwerken (waarbij IPsec/IKE-versleuteling wordt gebruikt), Transport Layer Security (TLS) 1,2 of hoger (via Azure-onderdelen, zoals Application Gateway of Azure front deur), de protocollen rechtstreeks op de virtuele machines van Azure (zoals Windows IPsec of SMB), en meer. 

Daarnaast is "versleuteling standaard" met behulp van MACsec (een IEEE-standaard op de Data Link-laag) ingeschakeld voor alle Azure-verkeer tussen Azure-Data Centers om de vertrouwelijkheid en integriteit van klant gegevens te waarborgen. 

**Gegevens redundantie**: micro soft helpt ervoor te zorgen dat gegevens beveiligd zijn als er sprake is van een cyberattack of fysieke schade aan een Data Center. Klanten kunnen kiezen voor:

- In-land/regio-opslag voor compatibiliteits-of latentie overwegingen.
- Buiten land/buiten regio opslag voor beveiligings-of nood herstel doeleinden.

Gegevens kunnen worden gerepliceerd binnen een geselecteerde geografische regio voor redundantie, maar kunnen niet buiten de service worden verzonden. Klanten hebben meerdere opties voor het repliceren van gegevens, inclusief het aantal exemplaren en het aantal en de locatie van de replicatie data centers.

Wanneer u uw opslag account maakt, selecteert u een van de volgende replicatie opties:

- **Lokaal redundante opslag (LRS)**: lokaal redundante opslag onderhoudt drie kopieën van uw gegevens. LRS wordt binnen één faciliteit in één regio driemaal gerepliceerd. LRS beschermt uw gegevens tegen normale hardwarefouten, maar niet van een storing van één faciliteit.
- **Zone-redundante opslag (ZRS)**: zone-redundante opslag onderhoudt drie kopieën van uw gegevens. ZRS wordt drie maal gerepliceerd over twee tot drie faciliteiten om een hogere duurzaamheid te bieden dan LRS. Replicatie vindt plaats binnen één regio of tussen twee regio's. ZRS helpt ervoor te zorgen dat uw gegevens duurzaam zijn in één regio.
- **Geografisch redundante opslag (GRS)**: geografisch redundante opslag is standaard ingeschakeld voor uw opslag account wanneer u deze maakt. GRS onderhoudt zes kopieën van uw gegevens. Met GRS worden uw gegevens drie keer binnen de primaire regio gerepliceerd. Uw gegevens worden ook drie keer in een secundaire regio op honderden kilo meters van de primaire regio gerepliceerd en bieden het hoogste duurzaamheids niveau. Als er een storing optreedt in de primaire regio, wordt Azure Storage een failover naar de secundaire regio uitgevoerd. GRS helpt ervoor te zorgen dat uw gegevens duurzaam zijn in twee verschillende regio's.

**Gegevens vernietiging**: wanneer klanten gegevens verwijderen of Azure verlaten, volgt micro soft strikte standaarden voor het overschrijven van opslag resources voordat ze opnieuw worden gebruikt, evenals de fysieke vernietiging van buiten gebruik gestelde hardware. Micro soft voert een volledige verwijdering uit van gegevens over het verzoek van de klant en de beëindiging van het contract.

## <a name="customer-data-ownership"></a>Eigendom van klant gegevens
Micro soft inspecteert, keurt of bewaakt geen toepassingen die klanten implementeren naar Azure. Daarnaast weet micro soft niet welk soort gegevens klanten in azure willen opslaan. Micro soft claimt geen eigendom van gegevens over de klant gegevens die in azure zijn ingevoerd.

## <a name="records-management"></a>Record beheer
Azure heeft interne records gemaakt: vereisten voor het bewaren van back-end-gegevens. Klanten zijn verantwoordelijk voor het identificeren van de vereisten voor de Bewaar periode van een eigen record. Voor records die zijn opgeslagen in azure, zijn klanten verantwoordelijk voor het extra heren van hun gegevens en het bewaren van inhoud buiten Azure voor een door de klant opgegeven Bewaar periode.

Met Azure kunnen klanten gegevens en controle rapporten van het product exporteren. De exports worden lokaal opgeslagen om de gegevens voor een door de klant gedefinieerde Bewaar periode te bewaren.

## <a name="electronic-discovery-e-discovery"></a>Elektronische detectie (e-discovery)
Azure-klanten zijn verantwoordelijk voor het naleven van de vereisten voor e-discovery in hun gebruik van Azure-Services. Als Azure-klanten hun klant gegevens moeten bewaren, kunnen ze de gegevens lokaal exporteren en opslaan. Daarnaast kunnen klanten hun gegevens van de Azure-afdeling voor klant ondersteuning aanvragen. Behalve dat klanten hun gegevens kunnen exporteren, voert Azure uitgebreide logboek registratie en bewaking intern uit.

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
- [Integriteit van Azure-infra structuur](infrastructure-integrity.md)
