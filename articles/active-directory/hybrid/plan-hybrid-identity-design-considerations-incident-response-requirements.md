---
title: Hybride identiteits ontwerp-vereisten voor respons op incidenten Azure | Microsoft Docs
description: Bepaal de bewakings-en rapportage mogelijkheden voor de hybride identiteits oplossing die door IT kan worden gebruikt om maat regelen te nemen om mogelijke bedreigingen te identificeren en te verhelpen
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: a3d2a459-599b-4b67-8e51-7369ee25082d
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c68be4e46693621f77cb25dd8f34f8e0b7d75dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89278408"
---
# <a name="determine-incident-response-requirements-for-your-hybrid-identity-solution"></a>Vereisten voor respons op incidenten bepalen voor uw hybride identiteits oplossing
Voor grote of middel groot organisaties is het mogelijk dat er een reactie op een [beveiligings incident](/previous-versions/tn-archive/cc700825(v=technet.10)) plaatsvindt ter ondersteuning van het onderhouds plan op het niveau van incidenten. Het identiteits beheersysteem is een belang rijk onderdeel van het proces voor reactie op incidenten, omdat het kan worden gebruikt om te helpen identificeren wie een specifieke actie heeft uitgevoerd op het doel. De hybride identiteits oplossing moet kunnen voorzien in bewakings-en rapportage mogelijkheden die kunnen worden gebruikt om maat regelen te nemen om een mogelijke bedreiging te identificeren en te verhelpen. In een typisch antwoord plan voor incidenten hebt u de volgende fasen als onderdeel van het plan:

1. Eerste beoordeling.
2. Incident communicatie.
3. Beschadigings controle en risico beperking.
4. Identificatie van de inbreuk en ernst.
5. Behoud van de bewijzen.
6. Kennisgeving aan de juiste partijen.
7. Systeem herstel.
8. Documentatie.
9. Schade en kosten beoordeling.
10. Wijzigingen verwerken en plannen.

Tijdens de identificatie van de aanval en ernst fase is het nood zakelijk om de systemen te identificeren die zijn aangetast, bestanden die zijn geopend en de gevoeligheid van deze bestanden te bepalen. Uw hybride identiteits systeem moet aan deze vereisten kunnen voldoen om u te helpen bij het identificeren van de gebruiker die deze wijzigingen heeft aangebracht. 

## <a name="monitoring-and-reporting"></a>Controle en rapportage
Vaak kan het identiteits systeem ook in de eerste beoordelings fase kunnen helpen, voornamelijk als het systeem ingebouwde controle-en rapportage mogelijkheden heeft. Tijdens de eerste beoordeling moet de IT-beheerder een verdachte activiteit kunnen identificeren, of het systeem moet het automatisch kunnen activeren op basis van een vooraf geconfigureerde taak. Veel activiteiten kunnen duiden op een mogelijke aanval, maar in andere gevallen kan een onjuist geconfigureerd systeem leiden tot een aantal valse positieven in een indringings detectie systeem. 

Het identiteits beheersysteem moet IT-beheerders helpen bij het identificeren en rapporteren van deze verdachte activiteiten. Normaal gesp roken kunnen deze technische vereisten worden vervuld door alle systemen te bewaken en een rapportage mogelijkheid te hebben waarmee mogelijke dreigingen kunnen worden gemarkeerd. Gebruik de onderstaande vragen om u te helpen bij het ontwerpen van uw hybride identiteits oplossing, waarbij u rekening houdt met de vereisten voor de reactie op incidenten:

* Heeft uw bedrijf een reactie op een beveiligings incident?
  * Zo ja, is het huidige systeem voor identiteits beheer gebruikt als onderdeel van het proces?
* Moet uw bedrijf verdachte aanmeldings pogingen van gebruikers op verschillende apparaten identificeren?
* Moet uw bedrijf mogelijk referenties van de gebruiker detecteren?
* Moet uw bedrijf de toegang tot en de actie van de gebruiker controleren?
* Moet uw bedrijf weten wanneer een gebruiker het wacht woord opnieuw instelt?

## <a name="policy-enforcement"></a>Beleidsafdwinging
Tijdens beschadigings controle en risico reductie-fase is het belang rijk om snel de daad werkelijke en mogelijke gevolgen van een aanval te verminderen. Deze actie die u op dit punt gaat uitvoeren, kan het verschil tussen een secundaire en een groot deel hiervan maken. Het exacte antwoord is afhankelijk van uw organisatie en de aard van de aanval die u aanneemt. Als de eerste evaluatie heeft geconcludeerd dat er is geknoeid met een account, moet u beleid afdwingen om dit account te blok keren. Dat is slechts één voor beeld waarin het identiteits beheersysteem wordt benut. Gebruik de onderstaande vragen om u te helpen bij het ontwerpen van uw hybride identiteits oplossing, waarbij rekening wordt gehouden met de manier waarop beleids regels worden afgedwongen om te reageren op een doorlopend incident:

* Heeft uw bedrijf beleid voor het blok keren van gebruikers, indien nodig, toegang tot het netwerk?
  * Zo ja, is de huidige oplossing geïntegreerd met het hybride identiteits beheersysteem dat u gaat nemen?
* Moet uw bedrijf voorwaardelijke toegang afdwingen voor gebruikers die zich in quarantaine bevinden? 

> [!NOTE]
> Zorg ervoor dat u elk antwoord noteert de logica achter het antwoord begrijpt. [Strategie voor gegevens beveiliging definiëren](plan-hybrid-identity-design-considerations-data-protection-strategy.md) gaat over de beschik bare opties en de voor-en nadelen van elke optie.  Als u deze vragen hebt beantwoord, selecteert u welke optie het beste past bij uw bedrijfs behoeften.
> 
> 

## <a name="next-steps"></a>Volgende stappen
[Strategie voor gegevens beveiliging definiëren](plan-hybrid-identity-design-considerations-data-protection-strategy.md)

## <a name="see-also"></a>Zie ook
[Overzicht van ontwerp overwegingen](plan-hybrid-identity-design-considerations-overview.md)