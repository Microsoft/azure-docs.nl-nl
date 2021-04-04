---
title: Ontwerp van de vereisten voor het beheer van de hybride identiteit Azure | Microsoft Docs
description: Biedt inzicht in het bepalen van de vereisten voor het beheer van inhoud van uw bedrijf. Normaal gesp roken wanneer een gebruiker een eigen apparaat heeft, kunnen ze ook meerdere referenties hebben die worden verwisseld op basis van de toepassing die ze gebruiken. Het is belang rijk om te onderscheiden welke inhoud is gemaakt met behulp van persoonlijke referenties, evenals de gegevens die zijn gemaakt met bedrijfs referenties. Uw identiteits oplossing moet kunnen communiceren met Cloud Services om een naadloze ervaring te bieden aan de eind gebruiker, terwijl hun privacy wordt gegarandeerd en de beveiliging wordt verhoogd tegen lekkage van gegevens.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: dd1ef776-db4d-4ab8-9761-2adaa5a4f004
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57990fda7475b95bd6582fa5a495ac8d24aa55d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94408529"
---
# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>Vereisten voor inhoudbeheer bepalen voor uw hybride identiteits oplossing
Meer informatie over de vereisten voor het beheer van inhoud voor uw bedrijf is mogelijk van invloed op uw beslissing over welke hybride identiteits oplossing u wilt gebruiken. Met de verspreiding van meerdere apparaten en de mogelijkheid van gebruikers om hun eigen apparaten ([BYOD](/mem/intune/fundamentals/byod-technology-decisions)) te kunnen gebruiken, moet het bedrijf zijn eigen gegevens beveiligen, maar moet ook de privacy van de gebruiker intact blijven. Normaal gesp roken wanneer een gebruiker een eigen apparaat heeft, kunnen ze ook meerdere referenties hebben die worden verwisseld op basis van de toepassing die ze gebruiken. Het is belang rijk om te onderscheiden welke inhoud is gemaakt met behulp van persoonlijke referenties, evenals de gegevens die zijn gemaakt met bedrijfs referenties. Uw identiteits oplossing moet kunnen communiceren met Cloud Services om een naadloze ervaring te bieden aan de eind gebruiker, terwijl hun privacy wordt gegarandeerd en de beveiliging wordt verhoogd tegen lekkage van gegevens. 

Uw identiteits oplossing wordt gebruikt door verschillende technische controles om inhouds beheer te bieden, zoals wordt weer gegeven in de afbeelding hieronder:

![beveiligings controles](./media/plan-hybrid-identity-design-considerations/securitycontrols.png)

**Beveiligings controles die uw identiteits beheersysteem benutten**

In het algemeen gebruiken de vereisten voor inhouds beheer uw systeem voor identiteits beheer op de volgende gebieden:

* Privacy: het identificeren van de gebruiker die eigenaar is van een resource en het Toep assen van de juiste besturings elementen voor behoud van integriteit.
* Gegevens classificatie: de gebruiker of groep en het toegangs niveau voor een object identificeren op basis van de classificatie. 
* Preventie van gegevens lekkage: beveiligings controles die verantwoordelijk zijn voor het beveiligen van gegevens om lekkage te voor komen, moet communiceren met het identiteits systeem om de identiteit van de gebruiker te valideren. Dit is ook belang rijk voor controle spoor doeleinden.

> [!NOTE]
> Lees de [gegevens classificatie voor Cloud voorbereiding](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) voor meer informatie over aanbevolen procedures en richt lijnen voor gegevens classificatie.
> 
> 

Bij het plannen van uw hybride identiteits oplossing moet u ervoor zorgen dat de volgende vragen worden beantwoord volgens de vereisten van uw organisatie:

* Heeft uw bedrijf beveiligings controles voor het afdwingen van privacy voor gegevens?
  * Zo ja, kunnen de beveiligings besturings elementen worden geïntegreerd met de hybride identiteits oplossing die u wilt aannemen?
* Maakt uw bedrijf gebruik van gegevens classificatie?
  * Zo ja, is de huidige oplossing in staat om te integreren met de hybride identiteits oplossing die u gaat nemen?
* Heeft uw bedrijf momenteel een oplossing voor gegevens lekkage? 
  * Zo ja, is de huidige oplossing in staat om te integreren met de hybride identiteits oplossing die u gaat nemen?
* Moet uw bedrijf de toegang tot resources controleren?
  * Zo ja, wat voor soort resources?
  * Zo ja, welk informatie niveau hebt u nodig?
  * Zo ja, waar het audit logboek moet worden opgeslagen? On-premises of in de Cloud?
* Moet uw bedrijf e-mail berichten met gevoelige gegevens (burger servicenummers, creditcard nummers, enzovoort) versleutelen?
* Moet uw bedrijf alle documenten en inhoud versleutelen die worden gedeeld met externe zakelijke partners?
* Moet uw bedrijf bedrijfs beleid afdwingen voor bepaalde soorten e-mail berichten (niet allen beantwoorden, niet door sturen)?

> [!NOTE]
> Zorg ervoor dat u elk antwoord noteert de logica achter het antwoord begrijpt. [Strategie voor gegevens beveiliging definiëren](plan-hybrid-identity-design-considerations-data-protection-strategy.md) gaat over de beschik bare opties en de voor-en nadelen van elke optie.  Als u deze vragen hebt beantwoord, selecteert u welke optie het beste past bij uw bedrijfs behoeften.
> 
> 

## <a name="next-steps"></a>Volgende stappen
[Vereisten voor toegangsbeheer vaststellen](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Zie ook
[Overzicht van ontwerp overwegingen](plan-hybrid-identity-design-considerations-overview.md)