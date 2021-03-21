---
title: Relaties tussen ankers en wegwijzers
description: Meer informatie over het conceptuele model achter anker relaties. Meer informatie over het verbinden van ankers binnen een Space en het gebruik van de nabije API om te voldoen aan een manier om een scenario te vinden.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: ce79fe88b33659241a226af7bbb7a966ede41abb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97696157"
---
# <a name="anchor-relationships-and-way-finding-in-azure-spatial-anchors"></a>Verankeringen en manier om te vinden in azure ruimtelijke ankers

Door anker relaties te gebruiken, kunt u verbonden ankers in een ruimte maken en vervolgens vragen stellen als deze:

* Zijn er ankers in de buurt?
* Hoe ver weg zijn ze?

## <a name="examples"></a>Voorbeelden

U kunt verbonden ankers gebruiken in gevallen als deze:

* Een werk nemer moet een taak uitvoeren die betrekking heeft op verschillende locaties in een industriële fabriek. De fabriek heeft ruimtelijke ankers op elke locatie. Een HoloLens-of mobiele app helpt de werk nemer van de ene locatie naar de volgende te begeleiden. In de app wordt eerst gevraagd naar de ruimtelijke ankers in de buurt en wordt de werk nemer vervolgens naar de volgende locatie geleid. De app toont de algemene richting en afstand tot de volgende locatie.

* Een museum maakt ruimtelijke ankers op open bare schermen. Deze ankers vormen samen een rond leiding door de belangrijkste open bare schermen van het Museum. Bezoekers kunnen de Mixed Reality-app van het Museum op hun mobiele apparaat openen. Vervolgens wijzen ze hun telefoon camera rond de ruimte om de algemene richting en afstand naar de andere open bare weer gaven op de rond leiding te zien. Als een gebruiker naar een openbaar scherm gaat, werkt de app de algemene richting en afstand bij om de gebruiker te helpen.

## <a name="set-up-way-finding"></a>Instellen van richting-zoeken

Een app die gebruikmaakt van een lijn-van-gezichts richting en afstand tussen ankers om hulp te bieden, is het gebruik van een manier om te *zoeken*. De manier waarop u kunt zoeken is anders dan bij het omschakelen van de navigatie. In de richting van de navigatie kunnen gebruikers worden geleid rond muren, via deuren en tussen vloeren. Met de manier van zoeken krijgt de gebruiker aanwijzingen over de algemene richting van het doel. Maar het afnemen of kennis van de ruimte helpt de gebruiker ook door de structuur te navigeren naar het doel.

Voor het bouwen van een manier om een oplossing te vinden, bereidt u eerst een ruimte voor de ervaring voor en ontwikkelt u een app waarmee gebruikers kunnen communiceren. Dit zijn de conceptuele stappen:

1. **De ruimte plannen**: Beslis welke locaties binnen de ruimte deel zullen uitmaken van de zoek ervaring. In onze scenario's kan de fabrieks supervisor of de Tour coördinator van het Museum bepalen welke locaties moeten worden meegenomen in de ervaring voor het vinden van de oplossing.
2. **Ankers verbinden**: Ga naar de gekozen locaties om ruimtelijke ankers te maken. U kunt dit in een beheer modus van de app voor eind gebruikers of in een andere app geheel doen. U maakt verbinding of koppelt elk anker aan de andere. De service onderhoudt deze relaties.
3. **De ervaring van de eind gebruiker starten**: gebruikers voeren de app uit om een anker te vinden dat zich op een van de gekozen locaties bevindt. Uw totale ontwerp moet bepalen op welke locaties gebruikers de ervaring kunnen invoeren.
4. **Nabijgelegen ankers zoeken**: nadat de gebruiker een anker heeft gevonden, kan de app in de buurt van ankers aanvragen. Met deze procedure wordt een pose tussen het apparaat en deze ankers geretourneerd.
5. **De gebruiker begeleidt**: de app kan gebruikmaken van de pose van elk van deze ankers om hulp te geven over de algemene richting en afstand van de gebruiker. De camera-feed in de app kan bijvoorbeeld een pictogram en pijl bevatten die elke mogelijke bestemming vertegenwoordigen, zoals in de volgende afbeelding wordt weer gegeven.
6. **Verfijn de richt lijnen**: zoals door de gebruiker wordt door gegeven, kan de app periodiek een nieuwe tussen het apparaat en het doel anker berekenen. Met de app worden de richt lijnen voor begeleiding die de gebruiker bij de bestemming aankomt, verder verfijnd.

    ![Een voor beeld van hoe een app kan worden weer gegeven met uitleg over de manier van zoeken](./media/meeting-spot.png)

## <a name="connect-anchors"></a>Ankers verbinden

Voor het bouwen van een manier om een oplossing te vinden, moet u eerst de ankers op de gekozen locaties plaatsen. In deze sectie wordt ervan uitgegaan dat de beheerder van de app dit werk al heeft voltooid.

### <a name="connect-anchors-in-a-single-session"></a>Ankers in één sessie verbinden

Ankers verbinden:

1. Doorstappen naar de eerste locatie en anker een maken met behulp van een CloudSpatialAnchorSession.
2. Door lopen naar de tweede locatie. Het onderliggende MR/AR-platform houdt de beweging bij.
3. Maak anker B met behulp van dezelfde CloudSpatialAnchorSession. Ankers A en B zijn nu verbonden. De spatiale ankers-service onderhoudt deze relatie.
4. Ga door met de procedure voor de resterende ankers.

### <a name="connect-anchors-in-multiple-sessions"></a>Ankers verbinden in meerdere sessies

U kunt ruimtelijke ankers verbinden met meerdere sessies. Met deze methode kunt u enkele ankers in één keer maken en koppelen en later meer ankers maken en koppelen.

Ankers verbinden met meerdere sessies:

1. De app maakt enkele ankers (ankers A en B) in één CloudSpatialAnchorSession.
2. Op een ander tijdstip zoekt de app een van deze ankers (bijvoorbeeld anker A) met behulp van een nieuwe CloudSpatialAnchorSession.
3. Door lopen naar een nieuwe locatie. Het onderliggende platform voor gemengde of uitgebreide realiteit houdt de verplaatsing bij.
4. Maak anker C met behulp van dezelfde CloudSpatialAnchorSession. Ankers A, B en C zijn nu verbonden. De spatiale ankers-service onderhoudt deze relatie.

U kunt door gaan met deze procedure voor meer ankers en meer sessies gedurende een periode.

### <a name="verify-anchor-connections"></a>Anker verbindingen controleren

De app kan controleren of twee ankers zijn verbonden door een query uit te geven voor naburige ankers, die u doet door de `NearAnchorCriteria` op a in te stellen `CloudSpatialAnchorWatcher` . Wanneer het resultaat van de query het doel ankerpunt bevat, wordt de koppeling van het anker gecontroleerd. Als de ankers niet zijn verbonden, kan de app deze opnieuw proberen verbinding te maken.

Hier volgen enkele redenen waarom ankers geen verbinding kunnen maken:

* Het onderliggende platform voor gemengde realiteit of uitgebreid-Reality gaat tijdens het proces van het verbinden van ankers verloren.
* Vanwege een netwerk fout tijdens de communicatie met de spatiale ankers-service, kan de anker verbinding niet worden opgeslagen.

### <a name="find-sample-code"></a>Voorbeeld code zoeken

Zie voor [beeld van ruimtelijke ankers voorbeeld-apps](https://github.com/Azure/azure-spatial-anchors-samples)om voorbeeld code te vinden die laat zien hoe u koppelt aan de voor-en nabije query's.
