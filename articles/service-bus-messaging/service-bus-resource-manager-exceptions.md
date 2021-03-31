---
title: Uitzonde ringen voor Azure Service Bus Resource Manager | Microsoft Docs
description: Lijst met Service Bus-uitzonde ringen die door Azure Resource Manager en aanbevolen acties worden geoppereerd.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: a0b0338da0f002c7b667748ffd2bf5a40c91c580
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85336964"
---
# <a name="service-bus-resource-manager-exceptions"></a>Uitzonde ringen voor Service Bus Resource Manager

Dit artikel bevat een lijst met uitzonde ringen die worden gegenereerd bij het werken met Azure Service Bus met behulp van Azure Resource Manager-via-sjablonen of directe aanroepen.

> [!IMPORTANT]
> Dit document wordt regel matig bijgewerkt. Ga terug naar updates.

Hieronder vindt u de verschillende uitzonde ringen/fouten die worden weer gegeven via de Azure Resource Manager.

## <a name="error-bad-request"></a>Fout: ongeldige aanvraag

"Onjuiste aanvraag" impliceert dat de aanvraag die door de Resource Manager is ontvangen, niet is gevalideerd.

| Foutcode | Fout subcode | Foutbericht | Beschrijving | Aanbeveling |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Onjuiste aanvraag | 40000 | Subcode = 40000. De eigenschaps *naam* kan niet worden ingesteld bij het maken van een wachtrij, omdat de naam ruimte *name* van de naam ruimte de laag Basic gebruikt. Deze bewerking wordt alleen ondersteund in de laag Standard of Premium. | Op Azure Service Bus Basic-laag kunnen de onderstaande eigenschappen niet worden ingesteld of bijgewerkt: <ul> <li> RequiresDuplicateDetection </li> <li> AutoDeleteOnIdle </li> <li>RequiresSession</li> <li>DefaultMessageTimeToLive </li> <li> DuplicateDetectionHistoryTimeWindow </li> <li> EnableExpress </li> <li> ForwardTo </li> <li> Onderwerpen </li> </ul> | Overweeg om een upgrade uit te scha kelen van Basic naar Standard of Premium om deze functionaliteit te gebruiken. |
| Onjuiste aanvraag | 40000 | Subcode = 40000. De waarde voor de eigenschap requiresDuplicateDetection van een bestaande wachtrij (of onderwerp) kan niet worden gewijzigd. | Duplicaten detectie moet zijn ingeschakeld/uitgeschakeld op het moment dat de entiteit wordt gemaakt. De configuratie parameter duplicaten detectie kan niet worden gewijzigd nadat deze is gemaakt. | Als u duplicaten detectie wilt inschakelen voor een eerder gemaakte wachtrij/onderwerp, kunt u een nieuwe wachtrij/onderwerp maken met duplicaten detectie en vervolgens door sturen van de oorspronkelijke wachtrij naar de nieuwe wachtrij/onderwerp. |
| Onjuiste aanvraag | 40000 | Subcode = 40000. De opgegeven waarde 16384 is ongeldig. De eigenschap MaxSizeInMegabytes moet een van de volgende waarden hebben: 1024; 2048; 3072; 4096; 5120. | De MaxSizeInMegabytes-waarde is ongeldig. | Zorg ervoor dat de MaxSizeInMegabytes een van de volgende is: 1024, 2048, 3072, 4096, 5120. |
| Onjuiste aanvraag | 40000 | Subcode = 40000. Partitioneren kan niet worden gewijzigd voor wachtrij/onderwerp. | Partitioneren kan niet worden gewijzigd voor entiteit. | Maak een nieuwe entiteit (wachtrij of onderwerp) en schakel partities in. | 
| Onjuiste aanvraag | geen | De naam ruimte *name* van de naam ruimte bestaat niet. | De naam ruimte bestaat niet in uw Azure-abonnement. | Voer de onderstaande stappen uit om deze fout op te lossen <ul> <li> Zorg ervoor dat het Azure-abonnement juist is. </li> <li> Zorg ervoor dat de naam ruimte bestaat. </li> <li> Controleer of de naam van de naam ruimte juist is (geen spel fouten of null-teken reeksen). </li> </ul> | 
| Onjuiste aanvraag | 40400 | Subcode = 40400. De doel entiteit voor automatisch door sturen bestaat niet. | De bestemming voor de doel entiteit voor automatisch door sturen bestaat niet. | De doel entiteit (wachtrij of onderwerp) moet bestaan voordat de bron wordt gemaakt. Probeer het opnieuw nadat de doel entiteit is gemaakt. |
| Onjuiste aanvraag | 40000 | Subcode = 40000. De opgegeven vergrendelings tijd overschrijdt het toegestane maximum van ' 5 ' minuten. | De tijd waarvoor een bericht kan worden vergrendeld, moet tussen 1 minuut (mini maal) en 5 minuten (maximum) zijn. | Zorg ervoor dat de opgegeven vergrendelings tijd tussen 1 minuten en 5 minuten ligt. |
| Onjuiste aanvraag | 40000 | Subcode = 40000. De eigenschap DelayedPersistence en RequiresDuplicateDetection kunnen niet samen worden ingeschakeld. | Entiteiten waarvoor duplicaten detectie is ingeschakeld, moeten permanent zijn, zodat persistentie niet kan worden vertraagd. | Meer informatie over [Duplicaten detectie](duplicate-detection.md) |
| Onjuiste aanvraag | 40000 | Subcode = 40000. De waarde voor de eigenschap RequiresSession van een bestaande wachtrij kan niet worden gewijzigd. | Ondersteuning voor sessies moet zijn ingeschakeld op het moment dat de entiteit wordt gemaakt. Wanneer u eenmaal hebt gemaakt, kunt u geen sessies inschakelen/uitschakelen voor een bestaande entiteit (wachtrij of abonnement) | Een nieuwe wachtrij (of een nieuw abonnement) verwijderen en opnieuw maken met de eigenschap ' RequiresSession ' ingeschakeld. |
| Onjuiste aanvraag | 40000 | Subcode = 40000. URI_PATH bevat een of meer tekens die niet zijn toegestaan door Service Bus. Entiteits segmenten kunnen alleen letters, cijfers, punten (.), afbreek streepjes (-) en onderstrepings tekens (_) bevatten. | Entiteits segmenten kunnen alleen letters, cijfers, punten (.), afbreek streepjes (-) en onderstrepings tekens (_) bevatten. Alle andere tekens zorgen ervoor dat de aanvraag mislukt. | Zorg ervoor dat het URI-pad geen ongeldige tekens bevat. |


## <a name="error-code-429"></a>Fout code: 429

Net als bij HTTP geeft "fout code 429" te veel aanvragen aan ". Het impliceert dat de specifieke resource (naam ruimte) wordt beperkt vanwege te veel aanvragen (of als gevolg van conflicterende bewerkingen) voor die bron.

| Foutcode | Fout subcode | Foutbericht | Beschrijving | Aanbeveling |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 50004 | Subcode = 50004. De aanvraag is beëindigd omdat de naam ruimte van *uw naam ruimte* wordt beperkt. | Deze fout is opgetreden wanneer het aantal binnenkomende aanvragen de beperking van de resource overschrijdt. | Wacht een paar seconden en probeer het opnieuw. <br/> <br/> Meer informatie over de [quota](service-bus-quotas.md) en [limieten voor Azure Resource Manager aanvragen](../azure-resource-manager/management/request-limits-and-throttling.md)|
| 429 | 40901 | Subcode = 40901. Er wordt een andere conflicterende bewerking uitgevoerd. | Er wordt een andere conflicterende bewerking uitgevoerd voor dezelfde resource/entiteit | Wacht tot de huidige bewerking in uitvoering is voltooid en probeer het opnieuw. |
| 429 | 40900 | Subcode = 40900. Conflicteren. U vraagt een bewerking aan die niet is toegestaan in de huidige status van de resource. | Deze voor waarde kan worden bereikt wanneer meerdere aanvragen tegelijkertijd worden uitgevoerd om de bewerkingen op dezelfde entiteit (wachtrij, onderwerp, abonnement of regel) tegelijk uit te voeren. | Wacht een paar seconden en probeer het opnieuw |
| 429 | 40901 | Er is een conflict opgetreden tussen de aanvraag op de entiteits *naam* en de andere aanvraag | Er wordt een andere conflicterende bewerking uitgevoerd voor dezelfde resource/entiteit | Wacht tot de vorige bewerking is voltooid en probeer het opnieuw. |
| 429 | 40901 | Er wordt een andere update aanvraag uitgevoerd voor de entiteits *naam entiteit*. | Er wordt een andere conflicterende bewerking uitgevoerd voor dezelfde resource/entiteit | Wacht tot de vorige bewerking is voltooid en probeer het opnieuw. |
| 429 | geen | Er is een bron conflict opgetreden. Er wordt mogelijk een andere conflicterende bewerking uitgevoerd. Als dit een nieuwe poging is voor een mislukte bewerking, is het opruimen van de achtergrond nog in behandeling. Probeer het later opnieuw. | Deze voor waarde kan worden bereikt wanneer er een bewerking in behandeling is voor dezelfde entiteit. | Wacht tot de vorige bewerking is voltooid en probeer het opnieuw. |


## <a name="error-code-not-found"></a>Fout code: niet gevonden

Deze klasse van fouten geeft aan dat de bron niet is gevonden.

| Foutcode | Fout subcode | Foutbericht | Beschrijving | Aanbeveling |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Niet gevonden | geen | Entiteit *' entiteit naam '* is niet gevonden. | De entiteit waartegen de bewerking niet is gevonden. | Controleer of de entiteit bestaat en probeer het opnieuw. |
| Niet gevonden | geen | Niet gevonden. De bewerking bestaat niet. | De bewerking die u probeert uit te voeren, bestaat niet. | Controleer de bewerking en probeer het opnieuw. |
| Niet gevonden | geen | De binnenkomende aanvraag wordt niet herkend als een naam ruimte beleid voor het opslaan van een aanvraag. | De hoofd tekst van de binnenkomende aanvraag is null en kan daarom niet worden uitgevoerd als een put-aanvraag. | Controleer de hoofd tekst van de aanvraag om er zeker van te zijn dat deze niet null is. | 
| Niet gevonden | geen | De bericht entiteit *' entiteits naam '* is niet gevonden. | De entiteit waarvoor u de bewerking probeert uit te voeren, is niet gevonden. | Controleer of de entiteit bestaat en probeer het opnieuw. |

## <a name="error-code-internal-server-error"></a>Fout code: interne server fout

Deze klasse van fouten geeft aan dat er een interne server fout is opgetreden

| Foutcode | Fout subcode | Foutbericht | Beschrijving | Aanbeveling |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Interne server fout | 50000 | Subcode = 50.000. Interne server fout| Kan om verschillende redenen plaatsvinden. Enkele symptomen zijn: <ul> <li> De client aanvraag/de hoofd tekst is beschadigd en resulteert in een fout. </li> <li> Er is een time-out opgetreden voor de client aanvraag vanwege het verwerken van problemen met de service. </li> </ul> | Om dit probleem op te lossen <ul> <li> Zorg ervoor dat de aanvragen para meters niet null of misvormd zijn. </li> <li> Voer de aanvraag opnieuw uit. </li> </ul> |

## <a name="error-code-unauthorized"></a>Fout code: niet geautoriseerd

Deze klasse van fouten geeft aan dat er geen autorisatie is om de opdracht uit te voeren.

| Foutcode | Fout subcode | Foutbericht | Beschrijving | Aanbeveling |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Niet geautoriseerd | geen | Ongeldige bewerking op de secundaire naam ruimte. Secundaire naam ruimte heeft het kenmerk alleen-lezen. | De bewerking is uitgevoerd op basis van de secundaire naam ruimte, die is ingesteld als een alleen-lezen naam ruimte. | Voer de opdracht opnieuw uit op de primaire naam ruimte. Meer informatie over [secundaire naam ruimte](service-bus-geo-dr.md) |
| Niet geautoriseerd | geen | MissingToken: de autorisatie-header is niet gevonden. | Deze fout treedt op wanneer de autorisatie null of onjuiste waarden heeft. | Zorg ervoor dat de token waarde die wordt vermeld in de autorisatie-header juist is en niet null is. |