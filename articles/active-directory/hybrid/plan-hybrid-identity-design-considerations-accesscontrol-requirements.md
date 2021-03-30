---
title: Vereisten voor toegangs beheer voor hybride identiteits ontwerp Azure | Microsoft Docs
description: Behandelt de pijlers van identiteit en Identificeer de toegangs vereisten voor bronnen voor gebruikers in een hybride omgeving.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: e3b3b984-0d15-4654-93be-a396324b9f5e
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84b786a1701892823554a83fa2015ac88d6eff4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "60295140"
---
# <a name="determine-access-control-requirements-for-your-hybrid-identity-solution"></a>Bepaal de vereisten voor het toegangs beheer voor uw hybride identiteits oplossing
Wanneer een organisatie zijn hybride identiteits oplossing ontwerpt, kunnen ze deze mogelijkheid ook gebruiken om de toegangs vereisten te controleren voor de resources die ze plannen om deze beschikbaar te maken voor gebruikers. De gegevens toegang heeft alle vier de rand-id, die:

* Beheer
* Verificatie
* Autorisatie
* Controleren

De volgende secties omvatten verificatie en autorisatie in meer details, beheer en controle maken deel uit van de levens cyclus van de hybride identiteit. Lees de taken voor het [beheer van hybride identiteiten bepalen](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) voor meer informatie over deze mogelijkheden.

> [!NOTE]
> Lees [de vier pijlers van Identity-Identity Management in de leeftijd van hybride it](https://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx) voor meer informatie over deze pijlers.
> 
> 

## <a name="authentication-and-authorization"></a>Verificatie en autorisatie
Er zijn verschillende scenario's voor verificatie en autorisatie. deze scenario's hebben specifieke vereisten waaraan moet worden voldaan door de hybride identiteits oplossing die het bedrijf gaat nemen. Scenario's met betrekking tot Business to Business (B2B)-communicatie kunnen een extra uitdaging voor IT-beheerders toevoegen, omdat ze moeten zorgen dat de verificatie-en autorisatie methode die door de organisatie wordt gebruikt, kan communiceren met hun zakelijke partners. Zorg ervoor dat tijdens het ontwerp proces voor de verificatie-en autorisatie vereisten de volgende vragen worden beantwoord:

* Kan uw organisatie alleen gebruikers verifiëren en autoriseren die zich op hun identiteits beheersysteem bevinden?
  * Zijn er plannen voor B2B-scenario's?
  * Zo ja, weet u al welke protocollen (SAML, OAuth, Kerberos of certificaten) worden gebruikt om beide bedrijven te verbinden?
* Wordt de hybride identiteits oplossing die u gaat gebruiken voor de ondersteuning van die protocollen?

Een ander belang rijk punt om rekening mee te houden is de verificatie opslagplaats die wordt gebruikt door gebruikers en partners, en het beheer model dat moet worden gebruikt. Houd rekening met de volgende twee kern opties:

* Gecentraliseerd: in dit model kunnen de referenties, beleids regels en beheer van de gebruiker lokaal of in de cloud worden gecentraliseerd.
* Hybride: in dit model worden de referenties van de gebruiker, het beleid en de beheer taken gecentraliseerd op locatie en gerepliceerd in de Cloud.

Welk model uw organisatie zal aannemen, is afhankelijk van de bedrijfs vereisten. u wilt de volgende vragen beantwoorden om te bepalen waar het identiteits beheersysteem zich bevindt en welke beheer modus er moet worden gebruikt:

* Beschikt uw organisatie momenteel over een on-premises identiteits beheer?
  * Zo ja, wilt u deze blijven gebruiken?
  * Zijn er regelgeving of nalevings vereisten die uw organisatie moet volgen om te bepalen waar het identiteits beheersysteem zich moet bevinden?
* Maakt uw organisatie gebruik van eenmalige aanmelding voor apps die zich lokaal of in de cloud bevinden?
  * Zo ja, is de invoering van een hybride identiteits model van invloed op dit proces?

## <a name="access-control"></a>Toegangsbeheer
Verificatie en autorisatie zijn kern elementen om toegang tot Bedrijfs gegevens via de validatie van de gebruiker mogelijk te maken. het is ook belang rijk dat u het toegangs niveau beheert dat deze gebruikers hebben en dat het niveau van toegangs beheerders de bronnen heeft die ze beheren. Uw hybride identiteits oplossing moet nauw keurige toegang kunnen bieden tot resources, delegering en toegangs beheer op basis van rollen. Zorg ervoor dat de volgende vraag wordt beantwoord met betrekking tot toegangs beheer:

* Heeft uw bedrijf meer dan één gebruiker met verhoogde bevoegdheden om uw identiteits systeem te beheren?
  * Zo ja, heeft elke gebruiker hetzelfde toegangs niveau nodig?
* Moet uw bedrijf toegang delegeren aan gebruikers voor het beheren van specifieke bronnen?
  * Zo ja, hoe vaak gebeurt dit?
* Moet uw bedrijf mogelijkheden voor toegangs beheer integreren tussen on-premises en Cloud bronnen?
* Moet uw bedrijf de toegang tot resources beperken op basis van bepaalde voor waarden?
* Heeft uw bedrijf een toepassing die aangepaste controle toegang tot bepaalde resources nodig heeft?
  * Zo ja, waar bevinden zich de apps (on-premises of in de Cloud)?
  * Zo ja, waar bevinden zich de doel resources (on-premises of in de Cloud)?

> [!NOTE]
> Zorg ervoor dat u elk antwoord noteert de logica achter het antwoord begrijpt. [Strategie voor gegevens beveiliging definiëren](plan-hybrid-identity-design-considerations-data-protection-strategy.md) gaat over de beschik bare opties en de voor-en nadelen van elke optie.  Door deze vragen te beantwoorden, selecteert u welke optie het beste past bij uw bedrijfs behoeften.
> 
> 

## <a name="next-steps"></a>Volgende stappen
[Vereisten voor reageren op incidenten vaststellen](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="see-also"></a>Zie ook
[Overzicht van ontwerp overwegingen](plan-hybrid-identity-design-considerations-overview.md)

