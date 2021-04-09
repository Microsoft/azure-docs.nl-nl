---
title: Leerling-modus-persoonlijker
description: Leer hoe u de leerling-modus kunt gebruiken om het vertrouwen in een model te verkrijgen zonder dat u code hoeft te wijzigen.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 531917d9c48915f71354b4cd35747ecd9d33a6f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100385027"
---
# <a name="use-apprentice-mode-to-train-personalizer-without-affecting-your-existing-application"></a>De leerling-modus gebruiken om uw persoonlijker te trainen zonder dat dit van invloed is op uw bestaande toepassing

Als gevolg van de aard van het leer proces van de **praktijk** kan een persoonlijk model alleen worden getraind in een productie omgeving. Wanneer u een nieuwe use-case implementeert, wordt het personalisatie model niet efficiënt uitgevoerd, omdat het voor het model voldoende is getraind.  De **leerling-modus** is een leer proces dat deze situatie vereenvoudigt en u kunt vertrouwen in het model, zonder dat de ontwikkelaar code hoeft te wijzigen.

[!INCLUDE [Important Blue Box - Apprentice mode pricing tier](./includes/important-apprentice-mode.md)]

## <a name="what-is-apprentice-mode"></a>Wat is de modus van de leerlingen?

Net als bij de manier waarop leerlingen van een model leren, en met ervaring, kan dat beter. De leerling-modus is een _manier_ om persoonlijker inzicht te krijgen door de resultaten van bestaande toepassings logica te bestuderen.

Personaler treinen door dezelfde uitvoer als de toepassing te mimicking. Als meer gebeurtenissen stroom kan Personaler de bestaande toepassing _opvangen_ zonder dat dit van invloed is op de bestaande logica en resultaten. Met metrische gegevens, die beschikbaar zijn via de Azure Portal en de API, kunt u inzicht krijgen in de prestaties van het model.

Zodra Personaler een bepaald niveau van kennis heeft geleerd en bereikt, kan de ontwikkelaar het gedrag van de leerling-modus in de online modus wijzigen. Op dat moment begint het personaliseren van de acties in de Rank-API.

## <a name="purpose-of-apprentice-mode"></a>Doel van de leerling-modus

In de les-modus kunt u vertrouwen in de Personaler-service en de mogelijkheden van de machine learning, en kunt u er zeker van zijn dat de service informatie ontvangt die kan worden geleerd, zonder risico voor online verkeer.

De twee belangrijkste redenen voor het gebruik van de modus leerlingen zijn:

* Het beperken van **koude start**: de praktijk modus helpt bij het beheren en beoordelen van de kosten van een ' nieuw ' model voor de leer tijd, wanneer het niet de beste actie retourneert en geen voldoende effectiviteit van ongeveer 60-80% bereikt.
* **Actie-en context functies valideren**: functies die in acties en context worden verzonden, zijn mogelijk ontoereikend of onnauwkeurig, te weinig, te veel, onjuist of te specifiek voor het trainen van personalisatie om het ideale effectiviteits tempo te bereiken. Gebruik [functie-evaluaties](concept-feature-evaluation.md) om problemen met functies te vinden en op te lossen.

## <a name="when-should-you-use-apprentice-mode"></a>Wanneer moet u de modus leerlingen gebruiken?

Gebruik de leerling-modus om uw persoonlijker te trainen om de effectiviteit te verbeteren door de volgende scenario's te gebruiken en de ervaring van uw gebruikers niet door persoonlijker te laten worden beïnvloed:

* U implementeert een persoonlijke toepassing in een nieuwe use-case.
* U hebt de functies die u in context of acties hebt verzonden, aanzienlijk gewijzigd.
* U hebt ingrijpend gewijzigd wanneer en hoe u beloningen berekent.

De leerling-modus is geen efficiënte manier om de impact Personaler te meten. Als u wilt meten hoe effectief Personaler de best mogelijke actie voor elke classificatie oproep kan kiezen, gebruikt u [offline-evaluaties](concepts-offline-evaluation.md).

## <a name="who-should-use-apprentice-mode"></a>Wie moet de leerlingen-modus gebruiken?

De leerling-modus is handig voor ontwikkel aars, gegevens wetenschappers en besluit vormers:

* **Ontwikkel aars** kunnen de leerling-modus gebruiken om ervoor te zorgen dat de prioriteit en beloning-api's correct worden gebruikt in de toepassing en dat functies die worden verzonden naar personaler vanuit de toepassing geen fouten of niet-relevante functies zoals een time stamp-of userid-element bevatten.

* **Gegevens wetenschappers** kunnen de leerling-modus gebruiken om te controleren of de functies van kracht zijn om de personaler modellen te trainen, waardoor de belonings tijden niet te lang of kort zijn.

* **Besluit vormers** kunnen de leerlingen-modus gebruiken om de mogelijkheden van personalisatie te beoordelen om de resultaten te verbeteren (d.w.z. beloningen) in vergelijking met de bestaande bedrijfs logica. Hierdoor kunnen ze een weloverwogen beslissing nemen die invloed heeft op de gebruikers ervaring, waarbij echte omzet en gebruikers tevredenheid in de praktijk zijn.

## <a name="comparing-behaviors---apprentice-mode-and-online-mode"></a>Gedrag van de modus leerlingen en online modus vergelijken

De volgende manieren van het verschil in de modus leerlingen van de online modus is.

|Gebied|Leerlingmodus|Online modus|
|--|--|--|
|Gevolgen voor de gebruikers ervaring|U kunt het bestaande gebruikers gedrag gebruiken om uw persoonlijker te trainen door het te laten weten wat uw **standaard actie** zou hebben en wat de beslag is. Dit betekent dat de gebruikers ervaring en de bedrijfs resultaten hiervan niet worden beïnvloed.|De bovenste actie weer geven die wordt geretourneerd door de positie aanroep om het gedrag van de gebruiker te beïnvloeden.|
|Leer snelheid|Personaler leert langzaamer in de modus van de werk wijze dan bij het leren in de online modus. De praktijk modus kan alleen leren door de door uw **standaard actie** verkregen beloningen te bestuderen, waardoor de snelheid van het leren wordt beperkt omdat er geen onderzoek kan worden uitgevoerd.|Gaat sneller, omdat deze zowel het huidige model kan exploiteren als om nieuwe trends te verkennen.|
|Doel treffend onderwijs "plafond"|Personaler kan ongeveer even nauw keurig zijn, en nooit de prestaties van uw basis bedrijfs logica overschrijden (de berekenings snelheid die wordt behaald door de **standaard actie** van elke classificatie oproep). Dit aanpassings plafond wordt gereduceerd door te verkennen. Met een onderzoek van 20% is het echter zeer onwaarschijnlijker dat de prestaties van de leerlingen-modus meer dan 80% bedragen en 60% een redelijk doel is waarmee u de online modus kunt afbeelden.|Personaler moet de basis lijn van toepassingen overschrijden, en in de loop van de tijd waarop het afloopt, moet u de evaluatie van offline-evaluatie en-onderdelen uitvoeren om verbeteringen voor het model te blijven ontvangen. |
|Absolute API-waarde voor rewardActionId|De ervaring van de gebruiker wordt niet beïnvloed, omdat _rewardActionId_ altijd de eerste actie is die u in de rang aanvraag verzendt. Met andere woorden, de positie-API doet niets zichtbaar voor uw toepassing tijdens de modus van de leerling. Belonings-Api's in uw toepassing mogen niet wijzigen hoe de belonende API tussen de ene modus en de andere wordt gebruikt.|De gebruikers ervaring wordt gewijzigd door de _rewardActionId_ die personaler kiest voor uw toepassing. |
|Evaluaties|Personaler houdt een vergelijking bij van de belonings totalen die uw standaard bedrijfs logica krijgt, en de persoonlijke voor uitbetalingen worden opgehaald als in de online modus op dat moment. Er is een vergelijking beschikbaar in de Azure Portal voor die resource|Evalueer de effectiviteit van personalisatie door [offline-evaluaties](concepts-offline-evaluation.md)uit te voeren, waarmee u de totale persoonlijke voor delen kunt vergelijken met de mogelijke voor delen van de basis lijn van de toepassing.|

Een opmerking over de effectiviteit van de leerlingen-modus:

* De effectiviteit van personalisatie in de modus van de werk omgeving komt zelden voor in de buurt van 100% van de basis lijn van de toepassing. en deze nooit overschrijden.
* Aanbevolen procedures zijn niet te proberen om 100% Attain te bereiken. een bereik van 60% – 80% moet afhankelijk van de use-case zijn gericht.

## <a name="using-apprentice-mode-to-train-with-historical-data"></a>De leerling-modus gebruiken om te trainen met historische gegevens

Als u een aanzienlijke hoeveelheid historische gegevens hebt, kunt u deze gebruiken om de gegevens van de persoonlijker te trainen. u hebt nu de leerlingen-modus om het opnieuw te gebruiken voor het opnieuw afspelen van de informatie via Personaler.

Stel de Personaler in in de modus van de leerling en maak een script waarmee u de volg orde van de acties en context kenmerken van de historische gegevens aanroept. Roep de belonings-API aan op basis van uw berekeningen van de records in deze gegevens. U hebt ongeveer 50.000 historische gebeurtenissen nodig om enkele resultaten te zien, maar 500.000 wordt aanbevolen voor een betere betrouw baarheid in de resultaten.

Bij het uitvoeren van een training uit historische gegevens wordt aanbevolen dat de gegevens die zijn verzonden in (functies voor context en acties, hun indeling in de JSON die wordt gebruikt voor rang aanvragen en de berekening van de beloning in deze trainings gegevensverzameling), overeenkomen met de gegevens (functies en berekening van beloningen) die beschikbaar zijn in de bestaande toepassing.

Offline en achterliggende gegevens zijn vaak onvolledig en noisier en verschillen in de indeling. Hoewel het mogelijk is om historische gegevens te trainen, is het mogelijk dat de resultaten van dit doen onduidelijk zijn en niet een goede voor spelling van hoe goed Personaler u leert, met name als de functies verschillen tussen vroegere gegevens en de bestaande toepassing.

Normaal gesp roken is Personaler, in vergelijking tot de training met historische gegevens, het wijzigen van gedrag in de modus van de leerlingen en het leren van een bestaande toepassing is een efficiëntere weg naar een effectief model, met minder arbeid, data engineering en opschoon werkzaamheden.

## <a name="using-apprentice-mode-versus-ab-tests"></a>De leerling-modus gebruiken in plaats van een/B-test

Het is alleen handig om A/B-tests uit te voeren op persoonlijke handelingen nadat deze zijn gevalideerd en de online modus is leren. In de praktijk modus wordt alleen de **standaard actie** gebruikt. Dit betekent dat alle gebruikers de beheer ervaring effectief zien.

Zelfs als Personaler alleen de _behandeling_ is, is dezelfde uitdaging aanwezig bij het valideren van de gegevens voor het aanpassen van de persoonlijke voor keuren. In plaats daarvan kan de modus in de praktijk worden gebruikt, met 100% van het verkeer en met alle gebruikers die het besturings element (onaangetast) krijgen.

Als u een use-case gebruikt met Personaler en online leren, kunt u met een/B-experimenten gefundeerde cohort en een weten schappelijke vergelijking van de resultaten uitvoeren die complexer zijn dan de signalen die voor beloningen worden gebruikt. Een voor beeld van een vraag van een A/B-test kan worden beantwoord: `In a retail website, Personalizer optimizes a layout and gets more users to _check out_ earlier, but does this reduce total revenue per transaction?`

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [actieve en inactieve gebeurtenissen](concept-active-inactive-events.md)
