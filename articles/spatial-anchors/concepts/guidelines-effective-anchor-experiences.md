---
title: Richt lijnen voor efficiënte anker ervaringen
description: Richt lijnen en overwegingen voor het effectief maken en vinden van ankers met behulp van ruimtelijke Azure-ankers.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: a6bc500516de7e554c38a335ea57519a39c8f602
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95487500"
---
# <a name="create-an-effective-anchor-experience-by-using-azure-spatial-anchors"></a>Een effectief anker maken met behulp van ruimtelijke bewerkings punten van Azure

Dit artikel bevat richt lijnen en overwegingen om u te helpen bij het effectief maken en vinden van ankers met behulp van ruimtelijke beankeringen van Azure.

## <a name="anchor-improvement-over-time"></a>Anker verbetering na verloop van tijd

Met behulp van Azure spatiale ankers proberen we elke keer dat u ankers vindt, de kwaliteit van toekomstige Zoek bewerkingen te verbeteren. We doen dit met behulp van de omgevings gegevens die worden verzameld om de visuele informatie op de gewenste ankers te verbeteren. Dit proces wordt uitgevoerd op de achtergrond en is een offline optimalisatie die door de Azure spatiale ankers-service wordt uitgevoerd om te optimaliseren voor uw omgeving. De extra gegevens die tijdens elke bewerking zijn verzameld, opbouwen een sterker beeld van de omgeving. Dit verbetert de kwaliteit en stelt u in staat om ankers beter te vinden door de omgevings wijziging, het door sturen van tijd en voor gebruikers die naar ankers van verschillende hoeken en perspectieven kijken.

## <a name="good-anchors"></a>Goede ankers

Terwijl Azure spatiale ankers de kwaliteit van ankers in de loop van de tijd proberen te verbeteren, is het ook belang rijk om de tijd te investeren in het onderbrengen of verfijnen van gebruikers in uw gebruikers ervaring (UX) om goede ankers te maken. Door te investeren in het maken van goede ankers vooraan, helpt u eind gebruikers om op betrouw bare wijze ankers te vinden:

- Op verschillende apparaten.
- Op verschillende tijdstippen.
- In verschillende belichtings omstandigheden.
- Van de gewenste perspectieven binnen de ruimte.

## <a name="static-and-dynamic-locations"></a>Statische en dynamische locaties

Als onderdeel van het ontwerpen van de anker ervaring kiest u de locaties. Zijn de locaties statisch en gedefinieerd door een beheerder van de ruimte? Of worden ze dynamisch en gedefinieerd door de gebruiker?

Een winkel manager kan een statische in-Store-ervaring hebben om gebruikers ertoe te bewegen om te bezoeken. Maar de ontwikkelaar van een bord spel met gemengde realiteit zou waarschijnlijk willen dat gebruikers kiezen waar ze moeten worden afgespeeld.

Voor statische locaties kunt u beheerders leren om de tijd te best Eden aan het nalaten van de ruimte met goede ankers.

Voor dynamische locaties moet u nadenken over hoe u gebruikers in uw UX leert of begeleidt om goede ankers te maken.

## <a name="stable-visual-features"></a>Stabiele visuele functies

Visuele tracking systemen die worden gebruikt op gemengde en uitbreide realiteit-apparaten zijn afhankelijk van de visuele functies van de omgeving. Om de meest betrouw bare ervaring te verkrijgen:

- *Maak* ankers op locaties met stabiele visuele functies (dat wil zeggen, functies die niet vaak veranderen).

- *Maak geen* ankers op grote lege Opper vlakken die geen kenmerken voor onderscheiding hebben.

- Maak *geen* ankers op sterk reflecterende materialen.

- Maak *geen* ankers op Opper vlakken waar het patroon wordt herhaald, zoals tapijt of achtergrond.

![Voor beelden van een goede omgeving voor ankers en een beschadigde omgeving voor ankers](./media/stable-visual.png)

## <a name="various-viewing-perspectives"></a>Verschillende perspectieven voor weer gave

Wanneer u een anker maakt, moet u nadenken over de mensen die later proberen het anker te vinden.

Denk bijvoorbeeld aan een anker in het midden van een ruimte die twee deuren heeft. Waarschijnlijk wilt u gebruikers in staat stellen om de ruimte van een van beide deuren in te voeren. Wanneer u het anker maakt, moet u de positie van beide deurs scannen. U wijzigt perspectieven voor het vastleggen van omgevings gegevens rondom het anker zodat gebruikers het anker van beide deuren kunnen vinden.

In het algemeen moet u, wanneer u een anker maakt, het scannen vanuit de perspectieven van de mensen die proberen het te vinden. Als u bijvoorbeeld virtuele inhoud op een buiten-Sculpture plaatst, is het zinvol om de Sculpture te omzeilen en te scannen, terwijl u het anker maakt. Als uw anker zich in de hoek van een ruimte bevindt, is er slechts één richting om het te benaderen. Wanneer u dit anker maakt, kunt u het alleen vanuit dit perspectief scannen.

## <a name="multiple-anchors"></a>Meerdere ankers

Lighting kan een verschil maken in de visuele functies die een app detecteert. Ankers die zijn gemaakt in een sterk natuurlijk licht, zijn mogelijk moeilijk te vinden in kunst licht en vice versa.

Als u dit probleem ondervindt, kan het u helpen om twee ankers te maken. Maak op dezelfde plaats één anker in het daglicht en een ander in kunst matig licht. Uw app kan vervolgens een query uitvoeren voor beide ankers. Wanneer een van beide ankers zich bevindt, heeft de app een pose voor het anker.

Op dezelfde manier kunt u in omgevingen waar de visuele functies veranderen omdat de meeste objecten worden verplaatst, meerdere ankers kunnen helpen. Wanneer een anker te moeilijk te vinden is als gevolg van belang rijke wijzigingen in de omgeving, kunt u het anker vervangen door een nieuwe. U kunt dit bijvoorbeeld doen in een Retail Store waar de lay-out om de paar maanden wordt vernieuwd.

## <a name="targets-and-rooms"></a>Doelen en ruimtes

In veel gevallen is een anker een ingangs punt voor de ervaring van uw app. U kunt deze stap snel en betrouwbaar door lopen zodat gebruikers uw ervaring kunnen invoeren. Hoe gebruikers uw ankers vinden, is een belang rijke ontwerp stap. Het is handig om te denken over het vinden van ankers in termen van twee algemene scenario's: *doelen* en *ruimtes*.

### <a name="targets"></a>Doelen

In het doel scenario is de locatie van een anker duidelijk bekend. In een fictieve app voor het tekenen van gemengde realiteit plaatst een gebruiker bijvoorbeeld een virtueel canvas op de wand. Ze geeft de andere gebruikers in de ruimte om hun apparaten op dezelfde plek op de wand te wijzen om het anker te vinden en de ervaring te starten.

Een ander voor beeld van een doel scenario is mogelijk een aantekening in een koffie shop die de volgende zaken leest: "scannen op deals". De koffie shop heeft hier een anker geplaatst. Wanneer gebruikers het teken scannen, vinden ze het anker en voeren ze de uitgebreide ervaring in om deals op koffie te vinden.

In het doel scenario kunnen Foto's worden geholpen. Als u gebruikers een foto van het beoogde doel op hun apparaat weergeeft, kunnen ze snel bepalen wat er in de praktijk moet worden gescand. U kunt uw gebruikers bijvoorbeeld helpen binnen het gebied algemeen van een beoogd doel te komen door gebruik te maken van GPS. Wanneer de gebruiker zich aankomt, wordt in uw app een foto van het doel weer gegeven. De gebruiker ziet de ruimte rond, zoekt het doel en scant het anker.

![Afbeelding van een anker, waarin een foto van het doel op het mobiele apparaat van een gebruiker wordt weer gegeven](./media/start-here-edit.png)

### <a name="rooms"></a>Kleed

In het room-scenario voeren gebruikers een spatie in om hier ergens een anker te weten. Gebruikers scannen de ruimte met hun apparaat en vinden snel het anker.

Voor deze ervaring moet u doorgaans goed geregelde ankers maken, zoals beschreven in verschillende perspectieven voor weer gaven. Als u de ruimte van veel perspectieven hebt gescand tijdens het maken van het anker, kunnen gebruikers bijna overal scannen wanneer ze proberen het te vinden.

![Afbeelding van hoe een gebruiker een ruimte kan scannen om een anker te vinden](./media/scan-room.png)

In wezen besteedt u meer tijd aan het scannen van de ruimte wanneer u het anker maakt, zodat gebruikers het anker snel kunnen scannen en zoeken. Wanneer u uw ervaring maakt, moet u rekening houden met deze belang rijke afweging.

Het voor beeld van de app voor het tekenen van gemengde realiteit die we eerder hebben besproken, werkt niet goed als een room-scenario. De gebruiker die het anker plaatst, wil dat anderen snel lid worden van de ervaring. De gebruikers willen niet wachten op het starten van de ervaring totdat de ruimte goed wordt gescand. Omdat alle gebruikers precies weten waar ze hun apparaat moeten aanwijzen om de ankers te vinden, werkt dit voor beeld beter als een doel scenario.

## <a name="anchor-location"></a>Anker locatie

Visuele tracking systemen zijn afhankelijk van de visuele functies in een omgeving. Hoe meer visuele functies er in een scan worden opgenomen, hoe hoger de kans is dat u een anker kunt vinden.

Volg de algemene richt lijnen in deze sectie om een UX te maken die een nuttige scan van de omgeving aanmoedigt.

Als de gebruiker niet binnen een paar seconden een anker heeft gevonden, moet de app de gebruikers aanmoedigen om het apparaat te verplaatsen om meer perspectieven vast te leggen. De app kan ook gebruikers aanmoedigen om zichzelf over de omgeving te bewegen om vanuit meer perspectieven naar het anker te zoeken. De meer functie perspectieven die het apparaat ziet, hoe beter, naarmate er meer kans bestaat dat een anker zich bevindt, en verzamelt ook meer omgevings gegevens die worden gebruikt om de kwaliteit van het anker te verbeteren.

Voor doel scenario's vraagt u de gebruiker om het doel te verplaatsen zodat deze vanuit verschillende perspectieven kan worden weer gegeven. Met andere woorden, vraag de gebruiker om het doel op te nemen vanaf nieuwe perspectieven totdat het anker zich bevindt.

Voor room-scenario's vraagt u de gebruiker de ruimte langzaam te scannen. Vraag de gebruiker bijvoorbeeld om 180 graden of zelfs 360 graden van de ruimte vast te leggen. Of vraag de gebruiker om de ruimte vanuit een nieuw perspectief weer te geven.

De meest zinvolle methode is om te scannen over de hele kamer. Met een scan over de ruimte worden meer visuele functies van de omgeving vastgelegd dan bijvoorbeeld een scan van een in de buurt geplaatste wand. Een scan van een nabije muur legt niet zoveel nuttige visuele functies van de omgeving vast.

Het is niet handig om het apparaat herhaaldelijk te verplaatsen van naast elkaar wanneer u naar een anker zoekt. Hiermee worden dezelfde punten uit hetzelfde perspectief vastgelegd.

## <a name="experience-tests"></a>Testen van ervaring

In dit artikel hebben we algemene richt lijnen besproken. Met ruimtelijke ankers schrijft u apps die communiceren met de echte wereld. Daarom moet u tijd best Eden aan het testen van de anker scenario's van uw app in echte omgevingen. Dit geldt met name voor omgevingen die aangeven waar u verwacht dat uw gebruikers de app gebruiken.
