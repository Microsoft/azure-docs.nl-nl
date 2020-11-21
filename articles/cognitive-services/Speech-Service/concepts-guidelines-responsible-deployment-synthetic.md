---
title: Richt lijnen voor de verantwoordelijke implementatie van synthetische spraak technologie
titleSuffix: Azure Cognitive Services
description: De algemene ontwerp richtlijnen van micro soft voor het gebruik van synthetische spraak technologie. Deze zijn ontwikkeld in studies die micro soft heeft uitgevoerd met behulp van Voice-talen, consumenten en personen met spraak immuunziekten om de verantwoordelijke ontwikkeling van synthetische spraak te begeleiden.
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: 7d80ffb575c6aa15695279584b58288cbc16be43
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024973"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>Richt lijnen voor de verantwoordelijke implementatie van synthetische spraak technologie
Hier volgen de algemene ontwerp richtlijnen van micro soft voor het gebruik van synthetische spraak technologie. Deze zijn ontwikkeld in studies die micro soft heeft uitgevoerd met behulp van stem talen, consumenten en personen met een speech immuunziekten om de verantwoordelijke ontwikkeling van synthetische spraak te begeleiden.

## <a name="general-considerations"></a>Algemene overwegingen
Voor de implementatie van synthetische spraak technologie gelden de volgende richt lijnen in de meeste scenario's.

### <a name="disclose-when-the-voice-is-synthetic"></a>Openbaar maken wanneer de stem synthetisch is
Het sluiten van een computer die niet wordt gegenereerd, minimaliseert niet alleen het risico op schadelijke resultaten van bedrog, maar verhoogt ook de vertrouwens relatie in de organisatie die de stem levert. Meer informatie over [het vrijgeven](concepts-disclosure-guidelines.md)van.

### <a name="select-appropriate-voice-types-for-your-scenario"></a>Selecteer de juiste spraak typen voor uw scenario
Denk voorzichtig te weten over de gebruiks context en de mogelijke nadelen van het gebruik van synthetische spraak. Het is bijvoorbeeld mogelijk dat synthetische stemmen met hoge betrouw baarheid niet geschikt zijn voor scenario's met een hoog risico, zoals voor persoonlijke berichten, financiële trans acties of complexe situaties waarvoor Human adaptiveity of empathie is vereist. Gebruikers kunnen ook verschillende verwachtingen hebben voor spraak typen. Bijvoorbeeld, wanneer luistert naar gevoelige nieuws dat door een synthetische stem wordt gelezen, geven sommige gebruikers de voor keur aan een meer empathetic en mensen zoals het lezen van het nieuws, terwijl andere voor keuren voor een meer monotone, onzuivere spraak. U kunt uw toepassing testen om gebruikers voorkeuren beter te begrijpen.

### <a name="be-transparent-about-capabilities-and-limitations"></a>Wees doorzichtig over de mogelijkheden en beperkingen
Gebruikers hebben waarschijnlijk meer verwachtingen bij het communiceren met hoogwaardige, synthetische spraak agenten. Als de systeem mogelijkheden niet voldoen aan deze verwachtingen, kan de vertrouwens relatie echter nadelig worden voor komen en kan dit leiden tot onaangename of zelfs schadelijke ervaringen.

### <a name="provide-optional-human-support"></a>Optionele menselijke ondersteuning bieden
In ambigue, transactionele scenario's (bijvoorbeeld een ondersteunings centrum voor aanroepen) vertrouwen gebruikers niet altijd een computer agent die op de juiste wijze reageert op hun aanvragen. Ondersteunings personeel kan nodig zijn in deze situaties, ongeacht de realistische kwaliteit van de stem of de capaciteit van het systeem.

## <a name="considerations-for-voice-talent"></a>Overwegingen voor spraak-talen
Wanneer u werkt met spraak-talen, zoals stem actors, is de onderstaande richt lijn van toepassing.

### <a name="obtain-meaningful-consent-from-voice-talent"></a>Nuttige toestemming verkrijgen van spraak-talen
Voice-talen verwachten de controle over hun spraak lettertype (hoe en wanneer het wordt gebruikt) en worden gecompenseerd wanneer het wordt gebruikt. Systeem eigen aren moeten daarom expliciete schriftelijke toestemming van Voice-talen verkrijgen en duidelijke contractuele specificaties hebben over use cases, de duur van het gebruik, de compensatie, enzovoort. Sommige stem talen bieden geen informatie over het potentieel schadelijke gebruik van de technologie en moeten worden ondergebracht door systeem eigen aars over de mogelijkheden van de technologie. Lees voor meer informatie over stem talen en toestemming onze [openbaar making voor Voice-talen](/legal/cognitive-services/speech-service/disclosure-voice-talent).


## <a name="considerations-for-those-with-speech-disorders"></a>Aandachtspunten voor degenen met Speech immuunziekten
Wanneer u werkt met individuen met spraak immuunziekten, kunt u de volgende richt lijnen gebruiken om synthetische spraak technologie te maken of te implementeren.

### <a name="provide-guidelines-to-establish-contracts"></a>Richt lijnen bieden voor het vaststellen van contracten
Bieden richt lijnen voor het opstellen van contracten met personen die synthetische stem gebruiken voor hulp bij het spreken. In het contract moet worden gebruikgemaakt van de partijen die eigenaar zijn van de stem, de gebruiks duur, de eigendoms overdrachts criteria, de procedures voor het verwijderen van het spraak lettertype en het voor komen van onbevoegde toegang. Daarnaast moet de contractuele overdracht van het eigendom van het spraak lettertype na overlijden aan gezins leden worden ingeschakeld als deze persoon toestemming heeft gegeven.

### <a name="account-for-inconsistencies-in-speech-patterns"></a>Account voor inconsistenties in spraak patronen
Voor personen met een speech-immuunziekten die hun eigen spraak letter typen vastleggen, kunnen inconsistenties in hun spraak patroon (slurring of het onvermogen om bepaalde woorden aan te spreken) het registratie proces bemoeilijken. In dergelijke gevallen moeten synthetische spraak technologie en opname sessies deze bevatten (dat wil zeggen, onderbrekingen en een extra aantal opname sessies).

### <a name="allow-modification-over-time"></a>Wijzigingen in de loop van de tijd toestaan
Personen met spraak immuunziekten willen updates aan hun synthetische stem aanbrengen om te voldoen aan veroudering (bijvoorbeeld een kind dat puberty heeft bereikt). Personen kunnen ook stilistische voor keuren hebben die in de loop der tijd veranderen en wellicht wijzigingen aanbrengen in de Toon hoogte, accent of andere spraak kenmerken.


## <a name="reference-docs"></a>Naslagdocumentatie

* [Openbaar making voor spraak-talen](/legal/cognitive-services/speech-service/disclosure-voice-talent)
* [Overzicht van beperking](concepts-gating-overview.md)
* [Vrijgeven](concepts-disclosure-guidelines.md)
* [Ontwerp patronen voor openbaar making](concepts-disclosure-patterns.md)

## <a name="next-steps"></a>Volgende stappen

* [Openbaar making voor spraak-talen](/legal/cognitive-services/speech-service/disclosure-voice-talent)
* [Vrijgeven](concepts-disclosure-guidelines.md)
* [Ontwerp patronen voor openbaar making](concepts-disclosure-patterns.md)