---
title: Ontwerp patronen voor openbaar making
titleSuffix: Azure Cognitive Services
description: Ontwerp patronen en aanbevolen procedures voor openbaar making.
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: shlo
ms.openlocfilehash: aec553b473e0fb2688224e8a8d027f7e9f3a6025
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "101716615"
---
# <a name="disclosure-design-patterns"></a>Ontwerppatronen van openbaarmakingen
Nu u&#39;dat u het juiste [niveau van openbaar making](concepts-disclosure-guidelines.md#disclosure-assessment) hebt vastgesteld voor uw synthetische spraak ervaring,&#39;een goede tijd om mogelijke ontwerp patronen te verkennen.
## <a name="overview"></a>Overzicht
Er is een spectrum van ontwerp patronen voor het vrijgeven van informatie die u kunt Toep assen op uw synthetische spraak ervaring. Als het resultaat van de evaluatie van de uitgave ' hoge openbaar making ' is, raden we [**expliciete openbaar making**](#explicit-disclosure)aan, wat betekent dat de oorsprong van de synthetische stem uiterst goed wordt gecommuniceerd. [**Impliciete openbaar making**](#implicit-disclosure) omvat hints en interactie patronen die stem ervaring ondervinden, ongeacht of de vereiste verschaffings niveaus hoog of laag zijn.
![Spectrum van uitschaffings patronen](media/responsible-ai/disclosure-patterns/affordances.png)






| Expliciete verschaffings patronen                                                                                                                                                                                    | Impliciete uitschaffings patronen                                                                 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
|[Transparante Inleiding](#transparent-introduction)<br> [Mondelinge transparante Inleiding](#verbal-transparent-introduction)<br>  [Expliciete regelnaam](#explicit-byline)<br>  [Aanpassing en kalibratie](#customization-and-calibration)<br> [Vrijgeven van ouders](#parental-disclosure)<br> [Mogelijkheden bieden voor meer informatie over hoe de stem is gemaakt](#providing-opportunities-to-learn-more-about-how-the-voice-was-made) | [Capaciteits deling](#capability-disclosure)<br>[Impliciete hints en feedback](#implicit-cues--feedback)<br> [Spreek transparantie](#conversational-transparency) |



Gebruik de volgende grafiek om rechtstreeks naar de patronen te verwijzen die van toepassing zijn op uw synthetische stem. Enkele van de andere voor waarden in deze grafiek zijn mogelijk ook van toepassing op uw scenario:<br/>



| Als uw synthetische spraak ervaring... | Aanbevelingen | Ontwerppatronen |
| --- | --- | --- |
| Hoge vrijgeven vereist  | Gebruik ten minste één expliciet patroon en impliciete hints vooraan om gebruikers te helpen bij het bouwen van koppelingen. |[Expliciete openbaar making](#explicit-disclosure)<br>[Impliciete vrijgeven](#implicit-disclosure)  |
| Vereist een lage vrijgave | De openbaar making kan mini maal of onnodig zijn, maar kan wel van een aantal impliciete patronen profiteren. | [Capaciteits deling](#capability-disclosure)<br>[Spreek transparantie](#conversational-transparency)  |
| Heeft een hoog niveau van betrokkenheid | Bouw op lange termijn en bied meerdere ingangs punten voor de publicatie van de gebruikers reis. Het is raadzaam om een onboarding-ervaring te hebben. | [Transparante Inleiding](#transparent-introduction)<br>[Aanpassing en kalibratie](#customization-and-calibration)<br>[Capaciteits deling](#capability-disclosure) |
| Omvat onderliggende items als de primaire doel groep | Doel ouders als de primaire doel groep en zorg ervoor dat ze de openbaar making naar kinderen effectief kunnen communiceren.  | [Vrijgeven van ouders](#parental-disclosure)<br>[Mondelinge transparante Inleiding](#verbal-transparent-introduction)<br> [Impliciete vrijgeven](#implicit-disclosure)<br> [Spreek transparantie](#conversational-transparency)  |
| Bevat blinde gebruikers of mensen met een beperkt gezichts vermogen als de primaire doel groep  | Behorende bij alle gebruikers, en zorg ervoor dat alle vormen van een visuele openbaar making alternatieve tekst of geluids effecten hebben. Voldoen aan de toegankelijkheids normen voor contrast verhouding en weergave grootte. Gebruik auditieve aanwijzingen voor het communiceren van informatie.  | [Mondelinge transparante Inleiding](#verbal-transparent-introduction) <br>[Auditieve hints](#implicit-cues--feedback)<br>[Haptic-hints](#implicit-cues--feedback)<br>[Spreek transparantie](#conversational-transparency)<br>[Toegankelijkheids standaarden](https://www.microsoft.com/accessibility) |
| Is scherm-kleiner, apparaat minder of stem alleen als primaire of enige modus van interactie | Gebruik auditieve aanwijzingen voor het communiceren van informatie. | [Mondelinge transparante Inleiding](#verbal-transparent-introduction) <br> [Auditieve hints](#implicit-cues--feedback)  |
| Bevat mogelijk meerdere gebruikers/listeners (bijvoorbeeld persoonlijke assistenten in meerdere gezin)  | Wees mindful van verschillende gebruikers contexten en niveaus van inzichten en bied meerdere mogelijkheden voor openbaar making in de gebruikers reis.  | [Transparante inleiding (retour gebruiker)](#transparent-introduction)<br> [Mogelijkheden bieden voor meer informatie over hoe de stem is gemaakt](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)<br> [Spreek transparantie](#conversational-transparency)  |



## <a name="explicit-disclosure"></a>Expliciete openbaar making
Als uw synthetische stem ervaring een hoge openbaar making vereist, kunt u het beste ten minste één van de volgende expliciete patronen gebruiken om de synthetische aard duidelijk te vermelden.
### <a name="transparent-introduction"></a>Transparante Inleiding

Voordat de spraak ervaring begint, introduceert we de digitale assistent door volledig doorzichtig te zijn over de oorsprong van zijn stem en de mogelijkheden ervan. Het beste moment om dit patroon te gebruiken, is bij het voorbereiden van een nieuwe gebruiker of bij het introduceren van nieuwe functies aan een terugkerende gebruiker. Bij het implementeren van impliciete hints tijdens een inleiding kunnen gebruikers een geestelijke model maken over de synthetische aard van de digitale agent.

#### <a name="first-time-user-experience"></a>Gebruikers ervaring voor de eerste keer

![Transparante Inleiding tijdens de eerste uitvoering](media/responsible-ai/disclosure-patterns/transparent-intro-first.png) <br>
*De synthetische stem wordt geïntroduceerd tijdens het onboarden van een nieuwe gebruiker.*

Aanbevelingen
- Beschrijf of de stem kunst matig is (bijvoorbeeld &quot; digitaal &quot; )
- Beschrijf wat de agent kan doen
- De stem&#39;s-oorsprong expliciet aangeven
- Een ingangs punt bieden voor meer informatie over de synthetische stem

#### <a name="returning-user-experience"></a>Gebruikers ervaring retour neren

Als een gebruiker de voorbereidings ervaring overs laat, kunt u door gaan met het aanbieden van toegangs punten tot de transparante Inleiding tot de gebruiker de stem voor de eerste keer activeert.
<br/>

![Transparante Inleiding tijdens het retour neren van gebruikers ervaring](media/responsible-ai/disclosure-patterns/transparent-intro-return.png)<br/>
*Geef een consistent ingangs punt op voor de synthetische spraak ervaring. Hiermee staat u toe dat de gebruiker terugkeert naar de voorbereidings ervaring wanneer deze de stem voor het eerst op een wille keurig punt in het traject van de gebruiker activeren.*


### <a name="verbal-transparent-introduction"></a>Mondelinge transparante Inleiding

Een gesp roken prompt waarin de oorsprong van de digitale assistent&#39;s Voice duidelijk genoeg is om te worden vrijgegeven. Dit patroon is het meest geschikt voor scenario's met hoge openbaar making waarbij Voice de enige modus van interactie is die beschikbaar is.
<br/>

![Mondelinge uitgesp roken doorzichtige Inleiding](media/responsible-ai/disclosure-patterns/spoken-prompt-1.png)
<br/>*Gebruik een transparante inleiding wanneer er even in de gebruikers ervaring wordt gebruikgemaakt van een persoon&#39;s-stem.*


![Mondelinge Inleiding in de eerste persoon](media/responsible-ai/disclosure-patterns/spoken-prompt-2.png)<br/>
*Voor extra transparantie kan de voice actor de oorsprong van de synthetische stem in de eerste persoon openbaar maken.*

### <a name="explicit-byline"></a>Expliciete regelnaam

Gebruik dit patroon als de gebruiker interactie heeft met een audio speler of een interactief onderdeel om de stem te activeren.


![Expliciete bericht regel in een nieuws media scenario](media/responsible-ai/disclosure-patterns/explicit-byline.png) <br/>
*Een expliciete regelnaam is de toewijzing van waar de Voice vandaan komt.*

Aanbevelingen

- Ingangs punt van aanbieding voor meer informatie over de gesynthesizerde Voice

### <a name="customization-and-calibration"></a>Aanpassing en kalibratie

Gebruikers controle bieden over de manier waarop de digitale assistent reageert (bijvoorbeeld hoe de spraak geluiden).  Wanneer een gebruiker communiceert met een systeem op basis van hun eigen voor waarden en met specifieke doel stellingen in de praktijk, dan hebben ze al begrepen dat deze&#39;s die geen echte persoon is.

#### <a name="user-control"></a>Gebruikers besturings element

Bied keuzen die een zinvolle en merkbaar effect hebben op de synthetische spraak ervaring.

![Gebruikers voorkeuren](media/responsible-ai/disclosure-patterns/customization-user-control.png)<br/>
*Gebruikers voorkeuren bieden de mogelijkheid om hun ervaring aan te passen en te verbeteren.*

Aanbevelingen

- Gebruikers toestaan de stem aan te passen (bijvoorbeeld de taal en het type spraak)
- Geef gebruikers een manier om het systeem te leren om te reageren op de unieke spraak (bijvoorbeeld spraak kalibratie, aangepaste opdrachten)
- Optimaliseren voor door de gebruiker gegenereerde of contextuele interacties (bijvoorbeeld herinneringen)

#### <a name="persona-customization"></a>Aangepaste persona

Bied manieren om de digitale assistent&#39;s Voice aan te passen. Als de stem is gebaseerd op een beroemdheden of een zeer herken bare persoon, overweeg dan om zowel visuele als gesp roken introducties te gebruiken wanneer gebruikers de stem bekijken.

![Stem aanpassen](media/responsible-ai/disclosure-patterns/customization-voice-type.png)<br/>
*Met de mogelijkheid om te kiezen uit een reeks stemmen kan de kunst matige aard worden genoteerd.*

Aanbevelingen
- Gebruikers toestaan om het geluid van elke stem te bekijken
- Een authentieke inleiding voor elke stem gebruiken
- Ingangs punten voor aanbiedingen voor meer informatie over de gesynthesizerde Voice

### <a name="parental-disclosure"></a>Vrijgeven van ouders

Naast het voldoen aan COPPA-voor Schriften, bieden we een openbaar making aan ouders als uw primaire doel groep jonge kinderen is en uw blootstellings niveau hoog is. Voor gevoelige toepassingen kunt u de ervaring beperking voordat een volwassene het gebruik van de synthetische stem heeft bevestigd. Stimuleer ouders om het bericht aan hun kinderen te communiceren.

![Openbaar making voor ouders](media/responsible-ai/disclosure-patterns/parental-disclosure.png)<br/>
*Een transparante inleiding die is geoptimaliseerd voor ouders, zorgt ervoor dat een volwassene op de hoogte is gesteld van de synthetische aard van de stem voordat een kind ermee werkt.*

Aanbevelingen

- Ouders als primaire doel groep voor openbaar making
- Ouders aanmoedigen om openbaar making naar hun kinderen te communiceren
- Ingangs punten voor aanbiedingen voor meer informatie over de gesynthesizerde Voice
- U kunt de ervaring het beste met een eenvoudige &quot; veiligheids &quot; vraag door geven aan ouders, zodat ze de openbaar making hebben gelezen

### <a name="providing-opportunities-to-learn-more-about-how-the-voice-was-made"></a>Mogelijkheden bieden voor meer informatie over hoe de stem is gemaakt

U kunt context gevoelige ingangs punten aanbieden aan een pagina, pop-up of externe site met meer informatie over de synthetische spraak technologie. U kunt bijvoorbeeld een koppeling laten weer gegeven om meer te weten te komen tijdens het voorbereiden of wanneer de gebruiker tijdens het gesprek meer informatie vraagt.

![Toegangs punt voor meer informatie](media/responsible-ai/disclosure-patterns/learn-more-entry-point.png)<br/>
*Voor beeld van een ingangs punt om de kans te bieden om meer te weten te komen over de geleerde stem.*

Zodra een gebruiker meer informatie heeft aangevraagd over de synthetische stem, is het primaire doel om hen te informeren over de oorsprong van de synthetische stem en om transparant te zijn voor de technologie.

![Gebruikers meer informatie geven over synthetische stem](media/responsible-ai/disclosure-patterns/learn-more.png)<br/>
*Meer informatie vindt u in de Help-site van een externe site.*

Aanbevelingen

- Vereenvoudig complexe concepten en Vermijd het gebruik van Legalese en technisch jargon
- Bury deze inhoud niet in de privacyverklaring voor privacy en gebruiks voorwaarden
- Beknopte inhoud voor komen en afbeeldingen gebruiken indien beschikbaar

## <a name="implicit-disclosure"></a>Impliciete vrijgeven

Consistentie is de sleutel om de vrijgave impliciet te bereiken tijdens de gebruikers traject. Consistent gebruik van visuele en auditieve hints op alle apparaten en modi van interactie kan helpen bij het bouwen van koppelingen tussen impliciete patronen en expliciete openbaar making.

![Consistentie van impliciete hints](media/responsible-ai/disclosure-patterns/consistency.png)

### <a name="implicit-cues--feedback"></a>Impliciete hints & feedback

Anthropomorphism kunnen op verschillende manieren worden gemanifesteerd, van de werkelijke visuele weer gave van de agent tot de spraak, geluiden, patronen van licht, stuiterende vormen of zelfs de trillingen van een apparaat. Wanneer u uw persoon definieert, maakt u gebruik van impliciete hints en feedback patronen in plaats van een zeer menselijke achtige avatar. Dit is een manier om de nood zaak voor een meer expliciete openbaar making te minimaliseren.

![Visuele hints en feedback](media/responsible-ai/disclosure-patterns/visual-affordances.png)<br/>
*Deze hints helpen de agent te anthropomorphizeen zonder dat ze niet te voor menselijke handen zijn. Ze kunnen ook worden toegepast op hun eigen manier wanneer ze consistent in de loop van de tijd worden gebruikt.*

Houd rekening met de verschillende modi van interacties van uw ervaring bij het opnemen van de volgende soorten hints:

| Visuele aanwijzingen                                                                                                                                                               | Auditieve hints                                                      | Haptic-hints |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-------------|
|  Avatar <br>Responsieve realtime-hints (zoals animaties)<br> Niet-scherm aanwijzingen (bijvoorbeeld verlichting en patronen op een apparaat)<br>  | Sonicon (bijvoorbeeld een korte, onderscheidende klank, reeks muziek notities) | Trill   |

### <a name="capability-disclosure"></a>Capaciteits deling

De openbaar making kan impliciet worden gerealiseerd door nauw keurige verwachtingen in te stellen voor de mogelijkheden van de digitale assistent. Geef voorbeeld opdrachten op, zodat gebruikers kunnen leren omgaan met de digitale assistent en context contextuele hulp kunnen bieden om meer te weten te komen over de synthetische stem tijdens de eerste fasen van de ervaring.

![Voor beeld van standaard reacties op een gesprek dat u kunt bevragen.](media/responsible-ai/disclosure-patterns/capability-disclosure.png)<br/>

### <a name="conversational-transparency"></a>Spreek transparantie

Wanneer conversaties in onverwachte paden terechtkomen, overweeg dan om standaard reacties te formuleren waarmee u verwachtingen kunt resetten, de transparantie moet versterken en gebruikers naar geslaagde paden kunnen worden gestuurd. Er zijn ook mogelijkheden voor het gebruik van expliciete informatie over conversaties.

![Onverwachte paden verwerken](media/responsible-ai/disclosure-patterns/conversational-transparency-1.png)<br/>

<br/>
Niet-taak-of &quot; persoonlijke &quot; vragen die gericht zijn op de agent, zijn een goede tijd om gebruikers te herinneren van de synthetische aard van de agent en hen te laten instemmen om deze op de juiste wijze te laten werken of om ze om te leiden naar een echte persoon.

![Taak vragen afhandelen](media/responsible-ai/disclosure-patterns/conversational-transparency-2.png)<br/>

## <a name="when-to-disclose"></a>Wanneer te vermelden

Er zijn veel mogelijkheden voor openbaar making tijdens de gebruikers traject. Ontwerp voor het eerste gebruik, het tweede gebruik, een ne gebruik..., maar ook &quot; &quot; een fout in het markeren van de transparantie, zoals wanneer het systeem een fout maakt of wanneer de gebruiker een beperking van de agent&#39;s-mogelijkheden detecteert.

![Verkoop kansen voor het hele gebruikers traject](media/responsible-ai/disclosure-patterns/touchpoints.png)<br/>

Voor beeld van een standaard gebruikers reis met een digitale assistent die verschillende verkoop kansen markeert.

### <a name="up-front"></a>Vooraf

Het beste moment voor vrijgeven is de eerste keer dat een persoon communiceert met de synthetische stem.In een persoonlijk voicemail scenario zou dit tijdens het voorbereidings proces kunnen zijn, of de eerste keer dat de gebruiker de gebruikers ervaring het eerst vergeeft. In andere scenario's is het mogelijk de eerste keer dat een synthetische spraak inhoud op een website leest of de eerste keer dat een gebruiker met een virtueel teken communiceert.

- [Transparante Inleiding](#transparent-introduction)
- [Capaciteits deling](#capability-disclosure)
- [Aanpassing en kalibratie](#customization-and-calibration)
- [Impliciete hints](#implicit-cues--feedback)

### <a name="upon-request"></a>Op aanvraag

Gebruikers moeten toegang hebben tot extra informatie, controle voorkeuren en op elk moment tijdens de gebruikers traject transparante communicatie kunnen krijgen.

- [Mogelijkheden bieden voor meer informatie over hoe de stem is gemaakt](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Aanpassing en kalibratie](#customization-and-calibration)
- [Spreek transparantie](#conversational-transparency)

### <a name="continuously"></a>Voortdurend

Gebruik de impliciete ontwerp patronen die de gebruikers ervaring voortdurend verbeteren.

- [Capaciteits deling](#capability-disclosure)
- [Impliciete hints](#implicit-cues--feedback)

### <a name="when-the-system-fails"></a>Wanneer het systeem is mislukt

Gebruik openbaar making als een kans om zonder problemen te werken.

- [Spreek transparantie](#conversational-transparency)
- [Mogelijkheden bieden voor meer informatie over hoe de stem is gemaakt](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Voor mensen](#conversational-transparency)

## <a name="additional-resources"></a>Aanvullende bronnen
- [Richt lijnen voor micro soft bot](https://www.microsoft.com/research/uploads/prod/2018/11/Bot_Guidelines_Nov_2018.pdf)
- [Richt lijnen voor Cortana-ontwerp](/cortana/voice-commands/voicecommand-design-guidelines)
- [Richt lijnen voor spraak ontwerp van micro soft Windows UWP](/windows/uwp/design/input/speech-interactions)
- [Richt lijnen voor spraak opdrachten voor micro soft Windows Mixed Reality](/windows/mixed-reality/voice-design#top-things-users-should-know-about-speech-in-mixed-reality)

## <a name="see-also"></a>Zie ook

* [Openbaar making voor spraak-talen](/legal/cognitive-services/speech-service/disclosure-voice-talent?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)
* [Richt lijnen voor de verantwoordelijke implementatie van synthetische spraak technologie](concepts-guidelines-responsible-deployment-synthetic.md)
* [Vrijgeven](concepts-disclosure-guidelines.md)