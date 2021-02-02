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
ms.openlocfilehash: 715c09ef65358b21e78cfde204b4819db0c7875d
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99428416"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>Richt lijnen voor de verantwoordelijke implementatie van synthetische spraak technologie

## <a name="general-considerations-to-keep-in-mind-when-implementing-ai-systems"></a>Algemene overwegingen waarmee u rekening moet houden bij het implementeren van AI-systemen 

In dit artikel vindt u meer informatie over synthetische spraak en aangepaste neurale Voice en de belangrijkste aandachtspunten voor het op de verantwoording maken van deze technologie. Over het algemeen zijn er echter verschillende zaken die u moet overwegen om voorzichtig te zijn bij het bepalen van het gebruik en de implementatie van AI-producten en-functies: 

* Wordt dit product of deze functie goed in mijn scenario uitgevoerd? Voordat u AI in uw scenario implementeert, moet u testen hoe de gegevens daad werkelijk worden gebruikt en kunt u ervoor zorgen dat deze de nauw keurigheid kan leveren die u nodig hebt. 
* Zijn de nodige voor het identificeren en reageren van fouten? Producten en functies met AI-functionaliteit zullen niet altijd 100% nauw keurig zijn, dus houd rekening met de manier waarop u eventuele fouten opspoort en beantwoordt. 

## <a name="general-guidelines-for-using-synthetic-voice-technology"></a>Algemene richt lijnen voor het gebruik van synthetische spraak technologie 
Hier volgen de algemene ontwerp richtlijnen van micro soft voor het gebruik van synthetische spraak technologie. Deze zijn ontwikkeld in studies die micro soft heeft uitgevoerd met behulp van stem talen, consumenten en personen met een speech immuunziekten om de verantwoordelijke ontwikkeling van synthetische spraak te begeleiden.

Voor de implementatie van synthetische spraak technologie gelden de volgende richt lijnen in de meeste scenario's.

### <a name="disclose-when-the-voice-is-synthetic"></a>Openbaar maken wanneer de stem synthetisch is
Het sluiten van een computer die niet wordt gegenereerd, minimaliseert niet alleen het risico op schadelijke resultaten van bedrog, maar verhoogt ook de vertrouwens relatie in de organisatie die de stem levert. Meer informatie over [het vrijgeven](concepts-disclosure-guidelines.md)van.

Micro soft vereist dat klanten de synthetische aard van de aangepaste Neural-stem door geven aan de gebruikers. 
* Zorg ervoor dat er voldoende informatie wordt verstrekt aan de doel groepen, met name wanneer u spraak van een bekende persoon gebruikt. de mensen nemen hun arresten op basis van de persoon die deze levert, ongeacht of ze dit goed of niet bewust zijn.  Het is bijvoorbeeld mogelijk dat de publicatie op het begin van een uitzending mondelinge wordt gedeeld. Ga voor meer informatie naar de [uitschaffings patronen](concepts-disclosure-patterns.md).   
* Overweeg een goede openbaar making aan ouders of andere partijen met gebruiks voorbeelden die zijn ontworpen voor minder jarigen en kinderen. als uw use case is bedoeld voor minder jarigen of kinderen, moet u er zeker van zijn dat de ouders of juridische voogden inzicht kunnen krijgen in de informatie over het gebruik van synthetische media en de juiste beslissing te nemen voor de minder jarigen of kinderen. 

### <a name="select-appropriate-voice-types-for-your-scenario"></a>Selecteer de juiste spraak typen voor uw scenario
Denk voorzichtig te weten over de gebruiks context en de mogelijke nadelen van het gebruik van synthetische spraak. Het is bijvoorbeeld mogelijk dat synthetische stemmen met hoge betrouw baarheid niet geschikt zijn voor scenario's met een hoog risico, zoals voor persoonlijke berichten, financiële trans acties of complexe situaties waarvoor Human adaptiveity of empathie is vereist. Gebruikers kunnen ook verschillende verwachtingen hebben voor spraak typen. Bijvoorbeeld, wanneer luistert naar gevoelige nieuws dat door een synthetische stem wordt gelezen, geven sommige gebruikers de voor keur aan een meer empathetic en mensen zoals het lezen van het nieuws, terwijl andere voor keuren voor een meer monotone, onzuivere spraak. U kunt uw toepassing testen om gebruikers voorkeuren beter te begrijpen.

### <a name="be-transparent-about-capabilities-and-limitations"></a>Wees doorzichtig over de mogelijkheden en beperkingen
Gebruikers hebben waarschijnlijk meer verwachtingen bij het communiceren met hoogwaardige, synthetische spraak agenten. Als de systeem mogelijkheden niet voldoen aan deze verwachtingen, kan de vertrouwens relatie echter nadelig worden voor komen en kan dit leiden tot onaangename of zelfs schadelijke ervaringen.

### <a name="provide-optional-human-support"></a>Optionele menselijke ondersteuning bieden
In ambigue, transactionele scenario's (bijvoorbeeld een ondersteunings centrum voor aanroepen) vertrouwen gebruikers niet altijd een computer agent die op de juiste wijze reageert op hun aanvragen. Ondersteunings personeel kan nodig zijn in deze situaties, ongeacht de realistische kwaliteit van de stem of de capaciteit van het systeem.

## <a name="considerations-for-voice-talent"></a>Overwegingen voor spraak-talen
Wanneer u werkt met spraak-talen, zoals stem actors, is de onderstaande richt lijn van toepassing.

### <a name="obtain-meaningful-consent-from-voice-talent"></a>Nuttige toestemming verkrijgen van spraak-talen
Stem talen moeten controle hebben over hun spraak model (hoe en waar ze worden gebruikt) en worden gecompenseerd voor het gebruik ervan. Micro soft vereist aangepaste Voice-klanten voor het verkrijgen van expliciete schriftelijke toestemming van hun Voice-talen voor het maken van een synthetische stem en de overeenkomst met spraak talen die de duur, het gebruik en eventuele beperkingen van de inhoud bepalen.  Als u een synthetische stem van een bekende persoon maakt, moet u de persoon achter de stem een manier bieden om de inhoud te bewerken of goed te keuren.

Sommige spraak talen zijn niet op de hoogte van het potentieel schadelijke gebruik van de technologie en moeten worden belicht door systeem eigen aren over de mogelijkheden van de technologie. Micro soft vereist dat klanten de openbaar making van micro soft [voor stem talen](/legal/cognitive-services/speech-service/disclosure-voice-talent) delen via spraak-talen rechtstreeks of via een geautoriseerde vertegenwoordiger van stem talen, waarin wordt beschreven hoe synthetische stemmen zijn ontwikkeld en in combi natie met tekst-naar-spraak Services kunnen worden gebruikt.

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
