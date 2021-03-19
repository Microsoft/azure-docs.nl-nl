---
title: Azure-toepassing Insights-Gebruikersstromen de navigatie stromen analyseren
description: Analyseer hoe gebruikers navigeren tussen de pagina's en functies van uw web-app.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.openlocfilehash: a31ab24b96fa44787d08801a0680f94ff98bb5a2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87309266"
---
# <a name="analyze-user-navigation-patterns-with-user-flows-in-application-insights"></a>Analyseer gebruikers navigatie patronen met Gebruikersstromen in Application Insights

![Application Insights Gebruikersstromen-hulp programma](./media/usage-flows/flows.png)

Het hulp programma Gebruikersstromen visualiseert hoe gebruikers navigeren tussen de pagina's en functies van uw site. Het is handig voor het beantwoorden van vragen als:

* Hoe navigeren kunnen gebruikers een pagina op uw site verlaten?
* Wat kunnen gebruikers klikken op een pagina op uw site?
* Waar bevinden de locaties die gebruikers het meest voor komen van uw site?
* Zijn er locaties waar gebruikers dezelfde actie kunnen herhalen?

Het hulp programma Gebruikersstromen wordt gestart vanuit een initiële pagina weergave, aangepaste gebeurtenis of uitzonde ring die u opgeeft. Op basis van deze eerste gebeurtenis bevat Gebruikersstromen de gebeurtenissen die voor en na de gebruikers sessies hebben plaatsgevonden. Regels van de variërende dikte geven aan hoe vaak elk pad is gevolgd door gebruikers. De knoop punten voor het starten van speciale **sessies** laten zien waar de volgende knoop punten zijn begonnen met een sessie. Knoop punten bij het beëindigen van **sessies** laten zien hoeveel gebruikers geen pagina weergaven of aangepaste gebeurtenissen hebben verzonden na het voor gaande knoop punt, waarbij gebruikers waarschijnlijk uw site verlaten.

> [!NOTE]
> Uw Application Insights resource moet pagina weergaven of aangepaste gebeurtenissen bevatten voor het gebruik van het hulp programma Gebruikersstromen. [Meer informatie over het instellen van uw app voor het automatisch verzamelen van pagina weergaven met de Application Insights java script SDK](./javascript.md).
>
>

## <a name="start-by-choosing-an-initial-event"></a>Begin met het kiezen van een eerste gebeurtenis

![Kies een eerste gebeurtenis voor Gebruikersstromen](./media/usage-flows/initial-event.png)

Als u wilt beginnen met het beantwoorden van vragen met het Gebruikersstromen-hulp programma, kiest u een eerste pagina weergave, aangepaste gebeurtenis of uitzonde ring die als uitgangs punt voor de visualisatie fungeert:

1. Klik op de koppeling in de titel **Wat doen gebruikers doen na...?** of klik op de knop **bewerken** .
2. Selecteer een pagina weergave, aangepaste gebeurtenis of uitzonde ring in de vervolg keuzelijst voor de **eerste gebeurtenis** .
3. Klik op **Create Graph**.

In de kolom ' stap 1 ' van de visualisatie ziet u wat gebruikers het meest vaak hebben gedaan, net na de eerste gebeurtenis, van boven naar beneden geordend van de meeste naar minst frequent. In ' stap 2 ' en de volgende kolommen ziet u wat gebruikers daarna hebben gedaan, waardoor ze een foto maken van alle manieren waarop gebruikers via uw site hebben genavigeerd.

Standaard worden alleen de laatste 24 uur van pagina weergaven en aangepaste gebeurtenissen van uw site wille keurig gesampled met het hulp programma Gebruikersstromen. U kunt het tijds bereik verg Roten en de prestaties en nauw keurigheid voor wille keurige steek proeven in het menu bewerken wijzigen.

Als sommige pagina weergaven, aangepaste gebeurtenissen en uitzonde ringen niet relevant voor u zijn, klikt u op de **X** op de knoop punten die u wilt verbergen. Wanneer u de knoop punten hebt geselecteerd die u wilt verbergen, klikt u op de knop **grafiek maken** onder de visualisatie. Als u alle knoop punten wilt zien die u hebt verborgen, klikt u op de knop **bewerken** en bekijkt u de sectie **uitgesloten gebeurtenissen** .

Als er pagina weergaven of aangepaste gebeurtenissen ontbreken die u verwacht te zien in de visualisatie:

* Controleer de sectie **uitgesloten gebeurtenissen** in het menu **bewerken** .
* Gebruik de plus knoppen op **andere** knoop punten om minder frequente gebeurtenissen in de visualisatie op te neemt.
* Als de door u verwachte pagina weergave of aangepaste gebeurtenis niet regel matig wordt verzonden door gebruikers, probeert u het tijds bereik van de visualisatie in het menu **bewerken** te verg Roten.
* Zorg ervoor dat de pagina weergave, aangepaste gebeurtenis of uitzonde ring die u verwacht, is ingesteld om te worden verzameld door de Application Insights SDK in de bron code van uw site. [Meer informatie over het verzamelen van aangepaste gebeurtenissen.](./api-custom-events-metrics.md)

Als u meer stappen in de visualisatie wilt zien, gebruikt u de vervolg keuzelijst voor **gaande stappen** en **volgende stappen** boven de visualisatie.

## <a name="after-visiting-a-page-or-feature-where-do-users-go-and-what-do-they-click"></a>Wanneer worden er een pagina of functie bezocht, waar worden gebruikers naartoe?

![Gebruik Gebruikersstromen om inzicht te krijgen in waar gebruikers klikken](./media/usage-flows/one-step.png)

Als uw eerste gebeurtenis een pagina weergave is, is de eerste kolom (' stap 1 ') van de visualisatie een snelle manier om te begrijpen wat gebruikers direct na de pagina hebben bezocht. Probeer uw site te openen in een venster naast de visualisatie Gebruikersstromen. Vergelijk uw verwachtingen over hoe gebruikers met de pagina communiceren met de lijst met gebeurtenissen in de kolom ' stap 1 '. Vaak kan een UI-element op de pagina die niet van belang is voor uw team, zich op de meest gebruikte pagina bevindt. Het kan een geweldig uitgangs punt zijn voor ontwerp verbeteringen voor uw site.

Als uw eerste gebeurtenis een aangepaste gebeurtenis is, laat de eerste kolom zien wat gebruikers hebben gedaan nadat deze actie is uitgevoerd. Net als bij pagina weergaven kunt u overwegen of het waargenomen gedrag van uw gebruikers overeenkomt met de doel stellingen en verwachtingen van uw team. Als de geselecteerde begin gebeurtenis ' toegevoegd item aan de winkel wagen ' is, ziet u bijvoorbeeld of ' Ga naar de kassa ' en ' voltooide aankoop ' in de visualisatie kort daarna weer gegeven. Als gebruikers gedrag afwijkt van uw verwachtingen, kunt u de visualisatie gebruiken om te begrijpen hoe gebruikers het huidige ontwerp van uw site krijgen.

## <a name="where-are-the-places-that-users-churn-most-from-your-site"></a>Waar bevinden de locaties die gebruikers het meest voor komen van uw site?

Bekijk de gebeëindigde knoop punten van **sessies** die hoog worden weer gegeven in een kolom in de visualisatie, met name vroegtijdig in een stroom. Dit betekent dat veel gebruikers waarschijnlijk vanaf uw site hebben gereden na het voorafgaande pad van pagina's en UI-interacties. Soms wordt het verloop verwacht-nadat een aankoop is voltooid op een commerce-site, maar gewoonlijk is het verloop een teken van ontwerp problemen, slechte prestaties of andere problemen met uw site die kunnen worden verbeterd.

Houd er wel op dat knoop punten die door deze **sessie zijn beëindigd** alleen worden gebaseerd op de telemetrie die door deze Application Insights resource is verzameld. Als Application Insights geen telemetrie voor bepaalde gebruikers interacties ontvangt, kunnen gebruikers nog steeds op deze manieren verbinding hebben met uw site, nadat de Gebruikersstromen hulp programma de melding heeft gegeven dat de sessie is beëindigd.

## <a name="are-there-places-where-users-repeat-the-same-action-over-and-over"></a>Zijn er locaties waar gebruikers dezelfde actie kunnen herhalen?

Zoek naar een pagina weergave of een aangepaste gebeurtenis die door veel gebruikers wordt herhaald tijdens de volgende stappen in de visualisatie. Dit betekent meestal dat gebruikers terugkerende acties uitvoeren op uw site. Als u een herhaling vindt, overweeg dan het ontwerp van uw site te wijzigen of nieuwe functionaliteit toe te voegen om herhaling te verminderen. U kunt bijvoorbeeld bulksgewijze bewerkings functionaliteit toevoegen als u gebruikers vindt die herhaalde acties uitvoeren voor elke rij van een TABLE-element.

## <a name="common-questions"></a>Veelgestelde vragen

### <a name="does-the-initial-event-represent-the-first-time-the-event-appears-in-a-session-or-any-time-it-appears-in-a-session"></a>Vertegenwoordigt de eerste gebeurtenis de eerste keer dat de gebeurtenis wordt weer gegeven in een sessie of op elk moment dat deze in een sessie wordt weer gegeven?

De eerste gebeurtenis in de visualisatie vertegenwoordigt alleen de eerste keer dat een gebruiker die pagina weergave of aangepaste gebeurtenis tijdens een sessie heeft verzonden. Als gebruikers de eerste gebeurtenis meerdere keren in een sessie kunnen verzenden, wordt in de kolom ' stap 1 ' alleen weer gegeven hoe gebruikers zich gedragen na het *eerste* exemplaar van de oorspronkelijke gebeurtenis, niet alle exemplaren.

### <a name="some-of-the-nodes-in-my-visualization-are-too-high-level-for-example-a-node-that-just-says-button-clicked-how-can-i-break-it-down-into-more-detailed-nodes"></a>Enkele van de knoop punten in mijn visualisatie zijn te hoog. Bijvoorbeeld een knoop punt met de tekst ' knop geklikt '. Hoe kan ik deze opsplitsen in meer gedetailleerde knoop punten?

Gebruik de opties **splitsen** op in het menu **bewerken** :

1. Kies de gebeurtenis die u wilt opsplitsen in het menu **gebeurtenis** .
2. Kies een dimensie in het menu **dimensie** . Als u bijvoorbeeld een gebeurtenis met de naam ' knop geklikt ' hebt, kunt u een aangepaste eigenschap met de naam ' button name ' proberen.

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van gebruik](usage-overview.md)
* [Gebruikers, sessies en gebeurtenissen](usage-segmentation.md)
* [Bewaar](usage-retention.md)
* [Aangepaste gebeurtenissen toevoegen aan uw app](./api-custom-events-metrics.md)

