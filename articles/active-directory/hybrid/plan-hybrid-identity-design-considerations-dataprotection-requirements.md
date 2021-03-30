---
title: Hybride identiteits ontwerp-vereisten voor gegevens beveiliging Azure | Microsoft Docs
description: Wanneer u uw hybride identiteits oplossing plant, identificeert u de vereisten voor gegevens beveiliging voor uw bedrijf en welke opties beschikbaar zijn om aan deze vereisten te voldoen.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 40dc4baa-fe82-4ab6-a3e4-f36fa9dcd0df
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
ms.openlocfilehash: 056d0caf2bfc6e99d5d659a2561bc41844feb79a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "64918776"
---
# <a name="plan-for-enhancing-data-security-through-a-strong-identity-solution"></a>Plan voor het verbeteren van gegevens beveiliging via een oplossing met een sterke identiteit
De eerste stap bij het beveiligen van gegevens is om te bepalen wie toegang heeft tot deze gegevens. U moet ook beschikken over een identiteits oplossing die kan worden geïntegreerd met uw systeem om verificatie-en autorisatie mogelijkheden te bieden. Verificatie en autorisatie worden vaak verward met elkaar en hun rollen zijn onduidelijk. In werkelijkheid zijn ze verschillend, zoals in de afbeelding hieronder wordt weer gegeven:

![levens cyclus van mobiele apparaten](./media/plan-hybrid-identity-design-considerations/mobile-devicemgt-lifecycle.png)

**Fasen in het levenscyclusbeheer van mobiele apparaten**

Bij het plannen van uw hybride identiteits oplossing moet u inzicht hebben in de vereisten voor gegevens beveiliging voor uw bedrijf en welke opties beschikbaar zijn om aan deze vereisten te voldoen.

> [!NOTE]
> Wanneer u klaar bent met het plannen van gegevens beveiliging, controleert u de [vereisten voor multi-factor Authentication](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md) om ervoor te zorgen dat uw selecties met betrekking tot multi-factor Authentication-vereisten niet worden beïnvloed door de beslissingen die u in deze sectie hebt genomen.
> 
> 

## <a name="determine-data-protection-requirements"></a>Vereisten voor gegevens beveiliging bepalen
In de leeftijd van mobiliteit hebben de meeste bedrijven een gemeen schappelijk doel: hun gebruikers in staat stellen productief te zijn op hun mobiele apparaten, terwijl ze on-premises zijn, of op afstand vanaf elke locatie om de productiviteit te verg Roten. Bedrijven die dergelijke vereisten hebben, zijn ook betrokken bij het aantal bedreigingen dat moet worden verholpen om de Bedrijfs gegevens veilig te houden en de privacy van de gebruiker te hand haven. Elk bedrijf heeft mogelijk in dit opzicht andere vereisten. Er zijn verschillende nalevings regels die afhankelijk zijn van de bedrijfstak van het bedrijf, waardoor er verschillende ontwerp beslissingen ontstaan. 

Er zijn echter enkele beveiligings aspecten die moeten worden verkend en gevalideerd, ongeacht de branche.

## <a name="data-protection-paths"></a>Gegevens beveiligings paden
![gegevens beveiligings paden](./media/plan-hybrid-identity-design-considerations/data-protection-paths.png)

**Gegevens beveiligings paden**

In het bovenstaande diagram is het identiteits onderdeel het eerste dat moet worden gecontroleerd voordat de gegevens worden geopend. Deze gegevens kunnen echter in verschillende statussen zijn tijdens het openen van het bestand. Elk getal in dit diagram vertegenwoordigt een pad waarin gegevens op een bepaald moment kunnen worden gevonden. Deze getallen worden hieronder uitgelegd:

1. Gegevens beveiliging op het niveau van het apparaat.
2. Gegevens bescherming tijdens de overdracht.
3. Gegevens bescherming tijdens rest on-premises.
4. Gegevens bescherming tijdens rust in de Cloud.

Het is nood zakelijk dat de hybride identiteits oplossing gebruikmaakt van zowel on-premises als Cloud identiteits beheer resources om de gebruiker te identificeren voordat deze toegang krijgt tot de gegevens. Wanneer u uw hybride identiteits oplossing plant, moet u ervoor zorgen dat de volgende vragen worden beantwoord volgens de vereisten van uw organisatie:

## <a name="data-protection-at-rest"></a>Gegevens bescherming bij rust
Ongeacht waar de gegevens zich in rust (apparaat, Cloud of on-premises) bevinden, is het belang rijk om een evaluatie uit te voeren om inzicht te krijgen in de behoeften van de organisatie in dit opzicht. Voor dit gebied moet u ervoor zorgen dat de volgende vragen worden gesteld:

* Moet uw bedrijf in rust gegevens beveiligen?
  * Zo ja, is de hybride identiteits oplossing in staat om te integreren met uw huidige on-premises infra structuur?
  * Zo ja, is de hybride identiteits oplossing in staat om te integreren met uw workloads in de Cloud?
* Kan het Cloud identiteits beheer de referenties van de gebruiker en andere gegevens die in de Cloud zijn opgeslagen, beveiligen?

## <a name="data-protection-in-transit"></a>Gegevens bescherming tijdens overdracht
Gegevens in de overdracht tussen het apparaat en het Data Center of tussen het apparaat en de Cloud moeten worden beveiligd. In transit is het echter niet nood zakelijk dat een communicatie proces met een onderdeel buiten uw Cloud service wordt uitgevoerd. het wordt intern verplaatst, ook als tussen twee virtuele netwerken. Voor dit gebied moet u ervoor zorgen dat de volgende vragen worden gesteld:

* Moet uw bedrijf onderweg gegevens beveiligen?
  * Zo ja, is de hybride identiteits oplossing in staat om te integreren met beveiligde besturings elementen zoals SSL/TLS?
* Houdt het Cloud Identity Management het verkeer naar en binnen het Directory archief (binnen en tussen de data centers) ondertekend?

## <a name="compliance"></a>Naleving
Voor Schriften, wetten en nalevings vereisten zijn afhankelijk van de branche waartoe uw bedrijf behoort. Bedrijven in hoog gereguleerde branches moeten problemen met betrekking tot het beheer van de identiteit oplossen die verband houden met nalevings problemen. Voor schriften zoals Sarbanes-Oxley (SOX), de Health Insurance Portity and Payment Act (HIPAA), de Gramm-uitloog-Bliley Act (GLBA) en de betalings kaart Industry Data Security Standard (PCI DSS) zijn strikt met betrekking tot identiteit en toegang. De hybride identiteits oplossing die uw bedrijf zal aannemen, moet beschikken over de kern mogelijkheden die voldoen aan de vereisten van een of meer van deze voor Schriften. Voor dit gebied moet u ervoor zorgen dat de volgende vragen worden gesteld:

* Is de hybride identiteits oplossing compatibel met de wettelijke vereisten voor uw bedrijf?
* Is de hybride identiteits oplossing gebouwd 
* in mogelijkheden waarmee uw bedrijf voldoet aan regelgevings vereisten? 

> [!NOTE]
> Zorg ervoor dat u elk antwoord noteert de logica achter het antwoord begrijpt. [Strategie voor gegevens beveiliging definiëren](plan-hybrid-identity-design-considerations-data-protection-strategy.md) gaat over de beschik bare opties en de voor-en nadelen van elke optie.  Als u deze vragen hebt beantwoord, selecteert u welke optie het beste past bij uw bedrijfs behoeften.
> 
> 

## <a name="next-steps"></a>Volgende stappen
 [Vereisten voor inhoudsbeheer vaststellen](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)

## <a name="see-also"></a>Zie ook
[Overzicht van ontwerp overwegingen](plan-hybrid-identity-design-considerations-overview.md)

