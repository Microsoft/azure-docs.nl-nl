---
title: Belonings Score-persoonlijker
description: De belonings score geeft aan hoe goed de aanpassings keuze, RewardActionID, voor de gebruiker heeft geresulteerd. De waarde van de belonings score wordt bepaald door uw bedrijfs logica, op basis van waarnemingen van gebruikers gedrag. Personaler traint de machine learning modellen door de beloningen te evalueren.
ms.service: cognitive-services
ms.subservice: personalizer
ms.date: 02/20/2020
ms.topic: conceptual
ms.openlocfilehash: f3249ba2089c3d9650aa46f665353ad392d0e773
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94365564"
---
# <a name="reward-scores-indicate-success-of-personalization"></a>Belonings scores geven aan dat het succes van personalisatie is geslaagd

De belonings score geeft aan hoe goed de aanpassings keuze, [RewardActionID](/rest/api/cognitiveservices/personalizer/rank/rank#response), voor de gebruiker heeft geresulteerd. De waarde van de belonings score wordt bepaald door uw bedrijfs logica, op basis van waarnemingen van gebruikers gedrag.

Personaler traint de machine learning modellen door de beloningen te evalueren.

Meer informatie [over](how-to-settings.md#configure-rewards-for-the-feedback-loop) het configureren van de standaard belonings Score in de Azure portal voor uw persoonlijke resource.

## <a name="use-reward-api-to-send-reward-score-to-personalizer"></a>Belonings-API gebruiken om belonings score naar persoonlijker te verzenden

Beloningen worden naar persoonlijke voor keuren verzonden met behulp van de [belonings-API](/rest/api/cognitiveservices/personalizer/events/reward). Een beloning is doorgaans een getal van 0 tot 1. Een negatieve beloning, met de waarde-1, is mogelijk in bepaalde scenario's en mag alleen worden gebruikt als u ervaring hebt met het versterken van Learning (RL). Personaler traint het model om de hoogst mogelijke som van beloningen in de loop van de tijd te krijgen.

Er worden beloningen verzonden nadat het gebruikers gedrag is opgetreden. Dit kan later dagen duren. De maximale hoeveelheid tijd Personaler wacht totdat een gebeurtenis wordt beschouwd als een vergoeding of een standaard beloning is geconfigureerd met de [wacht tijd](#reward-wait-time) op het Azure Portal.

Als de belonings score voor een gebeurtenis niet binnen de **wacht tijd** van de beloning is ontvangen, wordt de **standaard beloning** toegepast. Normaal gesp roken is de **[standaard beloning](how-to-settings.md#configure-reward-settings-for-the-feedback-loop-based-on-use-case)** ingesteld op nul.


## <a name="behaviors-and-data-to-consider-for-rewards"></a>Gedrag en gegevens om rekening te houden met beloningen

Houd rekening met de volgende signalen en gedragingen voor de context van de belonings Score:

* Directe gebruikers invoer voor suggesties wanneer er opties zijn betrokken ("wilt u X?").
* Sessie lengte.
* Tijd tussen sessies.
* Sentiment analyse van de interacties van de gebruiker.
* Directe vragen en mini enquêtes waarbij de bot de gebruiker vraagt om feedback te geven over de bruikbaarheid, nauw keurigheid.
* Antwoord op waarschuwingen of vertraging op Reacties op waarschuwingen.

## <a name="composing-reward-scores"></a>Belonings scores opstellen

Er moet een belonings Score worden berekend in uw bedrijfs logica. De score kan worden weer gegeven als:

* Eén getal dat eenmaal is verzonden
* Een score die onmiddellijk wordt verzonden (zoals 0,8) en een extra Score die later wordt verzonden (meestal 0,2).

## <a name="default-rewards"></a>Standaard beloningen

Als er binnen de [wacht tijd](#reward-wait-time)van de beloning geen vergoeding wordt ontvangen, wordt de duur sinds de rang gesprek impliciet de **standaard beloning** toegepast op die positie-gebeurtenis.

## <a name="building-up-rewards-with-multiple-factors"></a>Het opbouwen van beloningen met meerdere factoren

Voor effectief aanpassen kunt u de belonings Score op basis van meerdere factoren samen stellen.

U kunt bijvoorbeeld deze regels Toep assen voor het personaliseren van een lijst met video-inhoud:

|Gebruikers gedrag|Waarde van gedeeltelijke Score|
|--|--|
|De gebruiker heeft op het bovenste item geklikt.|+ 0,5 beloning|
|De gebruiker heeft de werkelijke inhoud van dat item geopend.|+ 0,3 beloning|
|De gebruiker heeft 5 minuten van de inhoud of 30% gevolgd, afhankelijk van wat langer is.|+ 0,2 beloning|
|||

U kunt vervolgens de totale beloning verzenden naar de API.

## <a name="calling-the-reward-api-multiple-times"></a>De belonings-API meerdere keren aanroepen

U kunt ook de belonings-API aanroepen met dezelfde gebeurtenis-ID, waarbij verschillende belonings scores worden verzonden. Wanneer personalisatie deze voor delen ophaalt, bepaalt hij de definitieve beloning voor die gebeurtenis door deze samen te voegen zoals is opgegeven in de persoonlijke configuratie.

Aggregatie waarden:

*  **Eerst**: de eerste belonings Score die voor de gebeurtenis is ontvangen, en de rest wordt verwijderd.
* **Sum**: Hiermee worden alle belonings scores verzameld voor de gebeurtenis-en toegevoegd aan elkaar.

Alle beloningen voor een gebeurtenis, die na de **wacht tijd** van de beloning worden ontvangen, worden genegeerd en hebben geen invloed op de training van modellen.

Als u belonings scores opneemt, ligt uw definitieve beloning mogelijk buiten het verwachte score bereik. Dit maakt de service niet meer.

## <a name="best-practices-for-calculating-reward-score"></a>Aanbevolen procedures voor het berekenen van de belonings Score

* **Denk aan echte indica toren voor een geslaagde personalisatie**: het is gemakkelijk om op de slag te gaan met klikken, maar een goede beloning is gebaseerd op wat u uw *gebruikers wilt laten doen in plaats van* wat u wilt *doen*.  Een voor beeld: een vergoeding op klikken kan leiden tot het selecteren van inhoud die clickbait gevoelig is.

* **Gebruik een belonings score voor de goede persoonlijke instellingen**: door een voor stel van een film te personaliseren, zou u er zeker van zijn dat de gebruiker de film bekijkt en een hoge classificatie krijgt. Omdat de classificatie van films waarschijnlijk afhankelijk is van veel dingen (de kwaliteit van de werking, de stemming van de gebruiker), is het geen goed idee om te bepalen hoe goed *het persoonlijke karakter* heeft gewerkt. De gebruiker die de eerste paar minuten van de film bekijkt, kan echter een beter signaal zijn bij de effectiviteit van de persoonlijke voor keuren en een beloning van 1 na 5 minuten een beter signaal sturen.

* **Beloningen zijn alleen van toepassing op RewardActionID**: personaler past de voor delen toe om inzicht te krijgen in de effectiviteit van de actie die is opgegeven in RewardActionID. Als u ervoor kiest om andere acties weer te geven en de gebruiker erop klikt, moet de beloning nul zijn.

* **Houd rekening met onbedoelde gevolgen**: Maak belonings functies die leiden tot de verantwoordelijke uitkomsten met [ethiek en verantwoordelijk gebruik](ethics-responsible-use.md).

* **Incrementele beloningen gebruiken**: het toevoegen van gedeeltelijke voor delen voor kleinere gebruikers gedrag helpt persoonlijker te maken met betere beloningen. Met deze incrementele beloning kan het algoritme ervoor worden geprofiteerd dat het dichter bij de gebruiker komt in het uiteindelijke gewenste gedrag.
    * Als er een lijst met films wordt weer gegeven, kunt u, als de gebruiker de eerste keer aanwijst voor een tijdje, bepalen dat er een bepaalde gebruikers betrokkenheid heeft plaatsgevonden. Het gedrag kan tellen met een belonings Score van 0,1.
    * Als de gebruiker de pagina opent en vervolgens afsluit, kan de belonings score 0,2 zijn.

## <a name="reward-wait-time"></a>Wachttijd voor beloning

Personaler geeft de informatie van een classificatie oproep samen met de beloningen die worden verzonden in belonings gesprekken om het model te trainen. Deze kunnen zich op verschillende tijdstippen voordoen. Personaler wacht op een beperkte tijd, vanaf het moment dat de rang oproep heeft plaatsgevonden, zelfs als de rang oproep is gemaakt als inactieve gebeurtenis en later wordt geactiveerd.

Als de **wacht tijd** voor de beloning is verlopen en er geen belonings informatie is, wordt er een standaard beloning toegepast op die gebeurtenis voor training. De maximale wacht tijd is zes dagen.

## <a name="best-practices-for-reward-wait-time"></a>Aanbevolen procedures voor de wacht tijd van beloning

Volg deze aanbevelingen voor betere resultaten.

* Stel de berekenings tijd even lang zo kort als mogelijk, terwijl u voldoende tijd hebt om gebruikers feedback te krijgen.

* Kies niet een duur die korter is dan de tijd die nodig is om feedback te krijgen. Als er bijvoorbeeld een aantal van uw voor delen beschikbaar is nadat een gebruiker 1 minuut van een video heeft bekeken, moet het experiment ten minste dubbele waarde zijn.

## <a name="next-steps"></a>Volgende stappen

* [Bekrachtigend leren](concepts-reinforcement-learning.md)
* [Probeer de classificatie-API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank/console)
* [Probeer de belonings-API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)