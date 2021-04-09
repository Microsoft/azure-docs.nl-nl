---
title: 'Azure AD Connect Cloud Sync dieper: hoe het werkt'
description: Dit onderwerp bevat gedetailleerde informatie over de werking van Cloud synchronisatie.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e1079ab4a523fbbc99c1e9190aef960b3f0723f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98613844"
---
# <a name="cloud-sync-deep-dive---how-it-works"></a>Cloud Sync dieper: hoe het werkt

## <a name="overview-of-components"></a>Overzicht van onderdelen

![Uitleg](media/concept-how-it-works/how-1.png)

Cloud synchronisatie is gebaseerd op de Azure AD-Services en heeft twee belang rijke onderdelen:

- **Inrichtings agent**: de Azure AD Connect Cloud-inrichtings agent is dezelfde agent als inkomend en gebouwd op dezelfde server-side technologie als app-proxy en Pass Through-verificatie. Het vereist en alleen uitgaande verbindingen en agents worden automatisch bijgewerkt. 
- **Provisioning-Service**: dezelfde inrichtings service als uitgaande inrichting en werkdag inrichtings toewijzing die gebruikmaakt van een model op basis van een planning. In het geval van Cloud synchronisatie worden de wijzigingen elke 2 minuten ingericht.


## <a name="initial-setup"></a>Eerste configuratie
Tijdens de eerste installatie worden er enkele dingen gedaan die ervoor zorgen dat de Cloud synchronisatie wordt uitgevoerd.  Deze zijn: 

- **Tijdens de installatie** van de agent kunt u de agent configureren voor de AD-domeinen die u wilt inrichten.  Deze configuratie registreert de domeinen in de Hybrid Identity-service en brengt een uitgaande verbinding tot stand met de service bus die naar aanvragen luistert.
- **Wanneer u inrichtings inschakelt**: u selecteert het AD-domein en schakelt inrichting in die elke 2 minuten wordt uitgevoerd. U kunt desgewenst de selectie van wachtwoord-hash-synchronisatie opheffen en e-mail meldingen definiëren. U kunt kenmerk transformatie ook beheren met behulp van Microsoft Graph-Api's.


## <a name="agent-installation"></a>Agentinstallatie
Hier volgt een overzicht van wat er gebeurt wanneer de Cloud inrichtings agent wordt geïnstalleerd.

- Eerst installeert het installatie programma de binaire bestanden van de agent en de Agent service die wordt uitgevoerd onder het Virtual service-account (netwerk SERVICE\AADProvisioningAgent).  Een virtueel service account is een speciaal type account dat geen wacht woord heeft en wordt beheerd door Windows.
- Het installatie programma start vervolgens de wizard.
- De wizard vraagt om Azure AD-referenties, verifieert vervolgens de verificatie en haalt een token op.
- De wizard vraagt vervolgens om de referenties van de huidige computer domein beheerder.
- Met deze referenties kunt u het algemeen beheerde service account van de agent (GMSA) voor dit domein maken of vinden en opnieuw gebruiken als het al bestaat.
- De Agent service wordt nu opnieuw geconfigureerd om te worden uitgevoerd onder de GMSA.
- De wizard vraagt nu om een domein configuratie samen met het account van de Enter prise admin (EA)/Domain-beheerder (DA) voor elk domein dat u door de agent wilt laten onderhouden.
- Het GMSA-account wordt vervolgens bijgewerkt met machtigingen waarmee het toegang kan krijgen tot elk domein dat hierboven wordt ingevoerd.
- Vervolgens wordt de agent registratie door de wizard geactiveerd
- De agent maakt een certificaat en gebruikt het Azure AD-token, registreert zichzelf en het certificaat met de registratie service van de Hybrid Identity service (zijn)
- De wizard activeert een AgentResourceGrouping-aanroep. Deze aanroep van zijn beheer service is om de agent toe te wijzen aan een of meer AD-domeinen in de configuratie.
- De wizard start de Agent service nu opnieuw.
- De agent roept een Boots trap-service bij opnieuw opstarten (en elke 10 minuten later) aan om te controleren op configuratie-updates.  De Boots trap-service valideert de identiteit van de agent.  Ook wordt de tijd van de laatste Boots trap bijgewerkt.  Dit is belang rijk omdat als er geen Boots trap-agents worden uitgevoerd, geen Service Bus-eind punten worden bijgewerkt en mogelijk geen aanvragen kunnen worden ontvangen. 


## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>Wat is System for Cross-domain Identity Management (SCIM)?

De [scim-specificatie](https://tools.ietf.org/html/draft-scim-core-schema-01) is een standaard die wordt gebruikt voor het automatiseren van het uitwisselen van gegevens over gebruikers-of groeps identiteiten tussen identiteits domeinen zoals Azure AD. SCIM wordt de standaard voor het inrichten en, wanneer het wordt gebruikt in combinatie met federatiestandaarden zoals SAML of OpenID Connect, biedt het beheerders een end-to-end, op standaarden gebaseerde oplossing voor toegangsbeheer.

De Azure AD Connect Cloud-inrichtings agent gebruikt SCIM met Azure AD voor het inrichten en ongedaan maken van de inrichting van gebruikers en groepen.

## <a name="synchronization-flow"></a>Synchronisatie stroom
![](media/concept-how-it-works/provisioning-4.png)Wanneer u de agent hebt geïnstalleerd en de inrichting hebt ingeschakeld, treedt de volgende stroom op.

1.  Na de configuratie roept de Azure AD-inrichtings service de Azure AD Hybrid service aan om een aanvraag toe te voegen aan de service bus. De agent onderhoudt voortdurend een uitgaande verbinding met het Service Bus naar aanvragen luistert en het systeem voor SCIM-aanvraag (Cross-Domain Identity Management) wordt direct opgehaald. 
2.  De agent splitst de aanvraag op in afzonderlijke query's op basis van het object type. 
3.  AD retourneert het resultaat van de agent en de agent filtert deze gegevens voordat deze naar Azure AD worden verzonden.  
4.  Agent retourneert het SCIM-antwoord naar Azure AD.  Deze antwoorden zijn gebaseerd op de filtering die is opgetreden in de agent.  De agent gebruikt scopes om de resultaten te filteren. 
5.  De inrichtings service schrijft de wijzigingen in azure AD.
6. Als dit een Delta synchronisatie is in plaats van een volledige synchronisatie, wordt de cookie/het water merk gebruikt. Nieuwe query's ontvangen wijzigingen van die cookie/dit water merk.

## <a name="supported-scenarios"></a>Ondersteunde scenario's:
De volgende scenario's worden ondersteund voor Cloud synchronisatie.


- **Bestaande hybride klant met een nieuw forest**: Azure AD Connect synchronisatie wordt gebruikt voor primaire forests. Cloud synchronisatie wordt gebruikt voor het inrichten vanuit een AD-forest (inclusief niet-verbonden). Raadpleeg [de zelf studie](tutorial-existing-forest.md)voor meer informatie.

 ![Bestaande hybride](media/tutorial-existing-forest/existing-forest-new-forest-2.png)
- **Nieuwe hybride klant**: Azure AD Connect synchronisatie wordt niet gebruikt. Cloud synchronisatie wordt gebruikt voor het inrichten vanuit een AD-forest.  Raadpleeg [de zelf studie](tutorial-single-forest.md)voor meer informatie.
 
 ![Nieuwe klanten](media/tutorial-single-forest/diagram-2.png)

- **Bestaande hybride klant**: Azure AD Connect synchronisatie wordt gebruikt voor primaire forests. Cloud synchronisatie is een pilot voor een kleine set gebruikers in de [primaire forests](tutorial-existing-forest.md).

 ![Bestaande pilot](media/tutorial-migrate-aadc-aadccp/diagram-2.png)

Zie [ondersteunde topologieën](plan-cloud-sync-topologies.md)voor meer informatie.



## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is Azure AD Connect--cloudsynchronisatie?](what-is-cloud-sync.md)
