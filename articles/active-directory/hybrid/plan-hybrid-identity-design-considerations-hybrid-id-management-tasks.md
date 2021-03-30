---
title: Ontwerp van hybride identiteiten-beheer taken Azure | Microsoft Docs
description: Azure AD controleert de specifieke voor waarden die u kiest bij het verifiëren van de gebruiker en voordat toegang tot de toepassing met voorwaardelijk toegangs beheer wordt toegestaan.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 65f80aea-0426-4072-83e1-faf5b76df034
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: c416bf19acb1736eeed679c16dbd87de1cc98537
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90986523"
---
# <a name="plan-for-hybrid-identity-lifecycle"></a>De levens cyclus van hybride identiteit plannen
Identiteit is een van de basis principes van uw bedrijfs mobiliteit en toegangs strategie voor toepassingen. Of u zich aanmeldt bij uw mobiele apparaat of SaaS-app, uw identiteit is de sleutel om toegang te krijgen tot alles. Op het hoogste niveau omvat een oplossing voor identiteits beheer samen voegen en synchroniseren tussen uw identiteits opslagplaatsen, waaronder het automatiseren en centraliseren van het proces van het inrichten van resources. De identiteits oplossing moet een gecentraliseerde identiteit in on-premises en Cloud zijn en ook een vorm van identiteits Federatie gebruiken om gecentraliseerde verificatie te onderhouden en veilig te delen en samen te werken met externe gebruikers en bedrijven. Bronnen variëren van besturings systemen en toepassingen tot mensen in of gekoppeld aan een organisatie. Organisatie structuur kan worden gewijzigd om het inrichtings beleid en de procedures te kunnen aanpassen.

Het is ook belang rijk dat u een identiteits oplossing hebt die uw gebruikers in staat stelt om hen te voorzien van self-service ervaringen zodat ze productief blijven. Uw identiteits oplossing is robuuster als Hiermee eenmalige aanmelding voor gebruikers over alle resources die ze nodig hebben, toegankelijk is. Beheerders op alle niveaus kunnen gestandaardiseerde procedures voor het beheren van gebruikers referenties gebruiken. Sommige beheer niveaus kunnen worden gereduceerd of geëlimineerd, afhankelijk van de breedte van de oplossing voor het inrichtings beheer. Daarnaast kunt u beheer mogelijkheden, hand matig of automatisch, onder verschillende organisaties veilig distribueren. Een domein beheerder kan bijvoorbeeld alleen de personen en bronnen in dat domein gebruiken. Deze gebruiker kan beheerders-en inrichtings taken uitvoeren, maar is niet gemachtigd om configuratie taken uit te voeren, zoals het maken van werk stromen.

## <a name="determine-hybrid-identity-management-tasks"></a>Taken voor het beheren van hybride identiteiten bepalen
Het distribueren van beheer taken in uw organisatie verbetert de nauw keurigheid en de effectiviteit van het beheer en verbetert het evenwicht tussen de werk belasting van een organisatie. Hieronder vindt u de draai tabel die een robuust identiteits beheersysteem definieert.

 ![overwegingen voor identiteits beheer](./media/plan-hybrid-identity-design-considerations/Identity_management_considerations.png)

Als u hybride identiteits beheer taken wilt definiëren, moet u inzicht hebben in enkele essentiële kenmerken van de organisatie die een hybride identiteit zullen aannemen. Het is belang rijk om inzicht te krijgen in de huidige opslag plaatsen die worden gebruikt voor identiteits bronnen. Door deze kern elementen te weten, hebt u de basis vereisten en op basis van dat u meer gedetailleerde vragen kunt stellen waarmee u een betere ontwerp beslissing krijgt voor uw identiteits oplossing.  

Zorg er bij het definiëren van deze vereisten voor dat ten minste de volgende vragen worden beantwoord

* Inrichtings opties: 
  
  * Ondersteunt de hybride identiteits oplossing een krachtig account voor toegangs beheer en inrichtings systeem?
  * Hoe worden gebruikers, groepen en wacht woorden beheerd?
  * Reageert het beheer van de identiteits levenscyclus? 
    * Hoe lang duurt het om een wacht woord?
* Licentie beheer: 
  
  * Verwerkt de hybride identiteits oplossing licentie beheer?
    * Zo ja, welke mogelijkheden zijn er beschikbaar?
  * Wordt met de oplossing op groep gebaseerd licentie beheer verwerkt? 
  
    * Zo ja, is het mogelijk om een beveiligings groep aan toe te wijzen? 
    * Zo ja, wordt in de Cloud Directory automatisch licenties toegewezen aan alle leden van de groep? 
    * Wat gebeurt er als een gebruiker vervolgens wordt toegevoegd aan of verwijderd uit de groep, dan wordt een licentie automatisch toegewezen of verwijderd, indien van toepassing? 
* Integratie met andere id-providers van derden:
  * Kan deze hybride oplossing worden geïntegreerd met id-providers van derden om eenmalige aanmelding te implementeren?
  * Is het mogelijk om alle verschillende id-providers in een coherent identiteits systeem samen te voegen?
  * Zo ja, hoe en wat zijn ze en welke mogelijkheden zijn beschikbaar?

## <a name="synchronization-management"></a>Synchronisatie beheer
Een van de doel stellingen van een identiteits Manager, waarmee alle id-providers kunnen worden gesynchroniseerd en gesynchroniseerd. U behoudt de gegevens gesynchroniseerd op basis van een gezaghebbende Master-ID-provider. In een scenario met een hybride identiteit beheert u met een gesynchroniseerd beheer model alle gebruikers-en apparaat-id's in een on-premises server en synchroniseert u de accounts en, desgewenst, wacht woorden in de Cloud. De gebruiker voert hetzelfde wacht woord on-premises in als in de Cloud en bij het aanmelden wordt het wacht woord gecontroleerd door de identiteits oplossing. Dit model maakt gebruik van een hulp programma voor Directory synchronisatie.

![met Directory-synchronisatie ](./media/plan-hybrid-identity-design-considerations/Directory_synchronization.png) kunt u de synchronisatie van uw hybride identiteits oplossing het beste ontwerpen door ervoor te zorgen dat de volgende vragen worden beantwoord:
*    Wat zijn de synchronisatie oplossingen die beschikbaar zijn voor de hybride identiteits oplossing?
*    Wat zijn de mogelijkheden voor eenmalige aanmelding?
*    Wat zijn de opties voor identiteits Federatie tussen B2B en B2C?

## <a name="next-steps"></a>Volgende stappen
[De strategie voor het implementeren van hybride identiteits beheer bepalen](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)

## <a name="see-also"></a>Zie ook
[Overzicht van ontwerp overwegingen](plan-hybrid-identity-design-considerations-overview.md)

