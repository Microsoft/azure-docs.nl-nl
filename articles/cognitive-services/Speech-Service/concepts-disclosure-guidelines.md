---
title: Richt lijnen voor het ontwerpen van vrijgeven
titleSuffix: Azure Cognitive Services
description: Inleiding tot de richt lijnen voor het vrijgeven van informatie en het beoordelen van het niveau van de publicatie
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: shlo
ms.openlocfilehash: 472d55f79033d60c4f40e60b55e0f7fc2ea4517e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101716649"
---
# <a name="disclosure-design-guidelines"></a>Richtlijnen voor het ontwerpen van openbaarmakingen
Meer informatie over het bouwen en onderhouden van vertrouwen met klanten door transparante informatie te krijgen over de synthetische aard van uw stem ervaring.

## <a name="what-is-disclosure"></a>Wat is een openbaar making?

Openbaar making is een manier om gebruikers te laten weten dat ze zich&#39;herinteractie met of het Luis teren naar een stem die synthetisch wordt gegenereerd.

## <a name="why-is-disclosure-necessary"></a>Waarom is de openbaar making nood zakelijk?

De nood zaak om de synthetische oorsprong van een door de computer gegenereerde spraak openbaar te maken, is relatief nieuw. In het verleden waren de door de computer gegenereerde stemmen uiteraard duidelijk dat: niemand zou deze ooit voor een echte persoon zouden afwegen. Elke dag gaat echter het reële aantal synthetische stemmen beter en ze worden steeds meer onderscheiden van mensen stemmen.

## <a name="goals"></a>Doelstellingen
Dit zijn de principes die u moet overwegen bij het ontwerpen van synthetische spraak ervaring:

**Vertrouwens relatie versterken**
<br>Ontwerp met de bedoeling dat de Turing-test niet kan worden gereduceerd zonder de ervaring te degraderen. Laat de gebruikers in op het feit dat ze interactie hebben met een synthetische stem, terwijl ze naadloos kunnen samen werken met de ervaring.

**Aanpassen aan gebruiks context**
<br>Begrijpen wanneer, waar en hoe uw gebruikers met de synthetische stem communiceren, om het juiste type openbaar making op het juiste moment te bieden.

**Duidelijke verwachtingen instellen**
<br>Gebruikers toestaan de mogelijkheden van de agent eenvoudig te detecteren en te begrijpen. Bied mogelijkheden om meer te weten te komen over synthetische spraak technologie op aanvraag.

**Profiteer van fout**
<br>Gebruik momenten van de fout om de mogelijkheden van de agent te versterken.

## <a name="how-to-use-this-guide"></a>Het gebruik van deze handleiding

Deze hand leiding helpt u te bepalen welke uitschaffings patronen het meest geschikt zijn voor uw synthetische spraak ervaring. We bieden vervolgens voor beelden van hoe en wanneer ze ze gebruiken. Elk van deze patronen is ontworpen om de transparantie te maximaliseren met gebruikers over synthetische spraak, terwijl hij waar voor menselijke gecentreerde ontwerp blijft.

Gezien het grote deel van ontwerp richtlijnen voor spraak ervaringen, zijn we hier specifiek gericht op:

1. [**Beoordeling van de openbaar making**](#disclosure-assessment): een proces om te bepalen welk type openbaar making wordt aanbevolen voor uw synthetische spraak ervaring

2. [**Openbaar**](concepts-disclosure-patterns.md)Making: voor beelden van uitgevende patronen die kunnen worden toegepast op uw synthetische spraak ervaring

3. [**Wanneer moet u**](concepts-disclosure-patterns.md#when-to-disclose)het volgende vermelden: optimaal moment om te vermelden tijdens de gebruikers reis

## <a name="disclosure-assessment"></a>Beoordeling van vrijgeven
Houd uw gebruikers&#39; verwachtingen over een interactie en de context waarin ze de stem zullen ondervinden. Als de context duidelijk maakt dat een synthetische stem &quot; uitspreekt, &quot; kan de openbaar making mini maal of onnodig zijn. De belangrijkste typen context die van invloed zijn op de openbaar making zijn onder ander type, scenario type en blootstellings niveau. U kunt ook zien wie er zou kunnen Luis teren.

### <a name="understand-context"></a>Context begrijpen

Gebruik dit werk blad om de context van uw synthetische spraak ervaring te bepalen. U kunt dit in de volgende stap gebruiken, waarbij u het niveau van de uitgave kunt bepalen.

|                                    | Gebruiks context                                                                                                                                                                                                                                                                                                                                                       | Mogelijke Risico's & uitdagingen                                                                                                                                                                                                                                                                                                                                                                       |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1. persona-type**               | **Als een van de volgende opties van toepassing is, past uw persoona aan onder de categorie Human-achtige Persona:**<br><br><ul><li> Persoona geeft een echte mens weer, of het nu gaat om een fictieve vertegenwoordiging of niet. (bijvoorbeeld foto of een door de computer gegenereerde rendering van een echte persoon)<br><br><li> De synthetische stem is gebaseerd op de stem van een zeer herken bare echte persoon (bijvoorbeeld beroemdheden, politieke afbeelding) | Hoe meer mensen u met Human-achtige representaties aan uw persoonlijke voor keur geeft, hoe groter de kans dat een gebruiker deze kan koppelen aan een echte persoon of dat ze van mening zijn dat de inhoud wordt gesp roken door een echte persoon in plaats van door de computer gegenereerd. </ul>                                                                                                                                                                      |
| **2. type scenario**            | **Als een van de volgende van toepassing is, past uw spraak ervaring aan onder de categorie ' gevoelig ':**<br><br><ul><li> Hiermee worden persoonlijke gegevens van de gebruiker opgehaald of weer gegeven <br><br> <li> Verzendt tijd gevoelige nieuws/informatie (bijvoorbeeld nood waarschuwing)<br><br><li> Streeft ernaar dat echte mensen met elkaar kunnen communiceren (bijvoorbeeld persoonlijke e-mail berichten/teksten lezen)<br><br> <li> Biedt medische/gezondheids bijstand </ul>            | Het gebruik van synthetische stem is mogelijk niet geschikt voor de mensen die het gebruiken wanneer de onderwerpen zijn gerelateerd aan gevoelige, persoonlijke of dringende zaken. Ze kunnen ook uitgaan van hetzelfde niveau van empathie en contextuele bewustzijn als een echt menselijk. |
| **3. blootstellings niveau** |**Uw spraak ervaring is waarschijnlijk het beste onder de categorie hoog als:** <br><br><ul><li>De gebruiker kan de synthetische spraak regel matig of gedurende een lange periode gebruiken </ul>                                                                                                                                                                             | Het belang van doorzichtigheid en het bouwen van vertrouwen met gebruikers is zelfs groter bij het tot stand brengen van lange termijn relaties.                                                                                                                                                                                                                                                                      |

### <a name="determine-disclosure-level"></a>Verschaffings niveau bepalen

Gebruik het volgende diagram om te bepalen of uw synthetische spraak ervaring een hoge of lage openbaar making vereist op basis van uw gebruiks context.

  ![Diagram voor uitschaffings beoordeling](media/responsible-ai/disclosure-guidelines/flowchart.png)

## <a name="see-also"></a>Zie ook

* [Ontwerppatronen van openbaarmakingen](concepts-disclosure-patterns.md)
* [Openbaar making voor spraak-talen](/legal/cognitive-services/speech-service/disclosure-voice-talent?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)
* [Richt lijnen voor de verantwoordelijke implementatie van synthetische spraak technologie](concepts-guidelines-responsible-deployment-synthetic.md)