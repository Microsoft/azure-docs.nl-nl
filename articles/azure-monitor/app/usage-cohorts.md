---
title: Azure-toepassing-cohortes voor inzichten gebruiken | Microsoft Docs
description: Analyseer verschillende sets of gebruikers, sessies, gebeurtenissen of bewerkingen met een gemeen schappelijke waarde
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 04/10/2018
ms.reviewer: mbullwin
ms.openlocfilehash: f8edeb452f01af88f0305cd0a3e3a2332ec09e4e
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186946"
---
# <a name="application-insights-cohorts"></a>Application Insights cohortes

Een cohort is een set gebruikers, sessies, gebeurtenissen of bewerkingen die een gemeen schappelijke waarde hebben. In Azure-toepassing inzichten worden cohorts gedefinieerd door een Analytics-query. In gevallen waarin u een specifieke set gebruikers of gebeurtenissen herhaaldelijk moet analyseren, kunnen cohorts meer flexibiliteit bieden om precies de set te zien waarin u geïnteresseerd bent.

![Het deel venster cohorts](./media/usage-cohorts/001.png)

## <a name="cohorts-versus-basic-filters"></a>Cohortes versus basis filters

Cohortes worden op dezelfde manier gebruikt als filters. De definities van cohortes zijn gebaseerd op aangepaste analyse query's, zodat ze veel meer kunnen worden aangepast en complex zijn. In tegens telling tot filters kunt u cohorts opslaan zodat andere leden van uw team ze kunnen hergebruiken.

U kunt een cohort definiëren van gebruikers die alle hebben geprobeerd een nieuwe functie in uw app uit te proberen. U kunt deze cohort opslaan in uw Application Insights-resource. Het is eenvoudig om deze opgeslagen groep specifieke gebruikers in de toekomst te analyseren.

> [!NOTE]
> Nadat ze zijn gemaakt, zijn er cohorts beschikbaar van de hulpprogram ma's gebruikers, sessies, gebeurtenissen en Gebruikersstromen.

## <a name="example-engaged-users"></a>Voor beeld: ingeschakelde gebruikers

Uw team definieert een ingeschakelde gebruiker als iedereen die uw app vijf of meer keer in een bepaalde maand gebruikt. In deze sectie definieert u een cohort van deze ingeschakelde gebruikers.

1. Open het hulp programma cohorts.

2. Selecteer het tabblad **sjabloon galerie** . U ziet een verzameling sjablonen voor verschillende cohorts.

3. Selecteer **ingeschakelde gebruikers--op dagen gebruikt**.

    Er zijn drie para meters voor deze cohort:
    * **Activiteiten**, waar u kunt kiezen welke gebeurtenissen en pagina weergaven het ' gebruik ' tellen.
    * **Period**, de definitie van een maand.
    * **UsedAtLeastCustom**, het aantal keren dat gebruikers iets moeten gebruiken binnen een periode om te tellen als actief.

4. Wijzig **UsedAtLeastCustom** in **5 + dagen** en geef de standaard waarde van 28 **dagen aan.**

    ![Ingeschakelde gebruikers](./media/usage-cohorts/003.png)

    Nu vertegenwoordigt deze cohort alle gebruikers-Id's die met een aangepaste gebeurtenis of pagina weergave worden verzonden op 5 afzonderlijke dagen in de afgelopen 28.

5. Selecteer **Opslaan**.

   > [!TIP]
   > Geef uw cohort een naam, bijvoorbeeld ' ingeschakelde gebruikers (5 + dagen) '. Sla het bestand op in ' mijn rapporten ' of ' gedeelde rapporten ', afhankelijk van of u wilt dat andere personen die toegang hebben tot deze Application Insights resource, deze cohort kunnen zien.

6. Selecteer **terug naar galerie**.

### <a name="what-can-you-do-by-using-this-cohort"></a>Wat kunt u doen met behulp van deze cohort?

Open het hulp programma gebruikers. Kies in de vervolg keuzelijst **weer geven** de cohort die u hebt gemaakt onder **gebruikers die deel uitmaken**.

Het hulp programma gebruikers wordt nu gefilterd op deze cohort van gebruikers:

![Deel venster gebruikers gefilterd op een bepaalde cohort](./media/usage-cohorts/004.png)

Enkele belang rijke aandachtspunten:

* U kunt deze set niet via normale filters maken. De datum logica is geavanceerder.
* U kunt deze cohort verder filteren met behulp van de normale filters in het hulp programma gebruikers. Hoewel de cohort is gedefinieerd op Windows 28 dagen, kunt u het tijds bereik in het hulp programma gebruikers nog steeds aanpassen aan 30, 60 of 90 dagen.

Deze filters ondersteunen meer geavanceerde vragen die niet in de opbouw functie voor query's kunnen worden uitgedrukt. Een voor beeld is _mensen die de afgelopen 28 dagen zijn betrokken. Hoe hebben diezelfde mensen zich in de afgelopen 60 dagen gedragen?_

## <a name="example-events-cohort"></a>Voor beeld: gebeurtenissen cohort

U kunt ook cohorts van gebeurtenissen maken. In deze sectie definieert u een cohort van de gebeurtenissen en pagina weergaven. Vervolgens ziet u hoe u deze kunt gebruiken in de andere hulpprogram ma's. In deze cohort kan een set gebeurtenissen worden gedefinieerd die door uw team worden beschouwd als _actief gebruik_ of een set die betrekking heeft op een bepaalde nieuwe functie.

1. Open het hulp programma cohorts.

2. Selecteer het tabblad **sjabloon galerie** . U ziet een verzameling sjablonen voor verschillende cohorts.

3. Selecteer **gebeurtenissen** selecteren.

    ![Scherm opname van gebeurtenissen selecteren](./media/usage-cohorts/006.png)

4. Selecteer in de vervolg keuzelijst **activiteiten** de gebeurtenissen die u in de cohort wilt maken.

5. Sla de cohort op en geef deze een naam.

## <a name="example-active-users-where-you-modify-a-query"></a>Voor beeld: actieve gebruikers waar u een query wijzigt

De vorige twee cohortes zijn gedefinieerd met behulp van vervolg keuzelijsten. U kunt ook cohorts definiëren door analyse query's te gebruiken voor de totale flexibiliteit. Om te zien hoe u een cohort van gebruikers maakt vanuit het Verenigd Konink rijk.

![Bewegende afbeelding doorlopend gebruik van het hulp programma cohorts](./media/usage-cohorts/cohorts0001.gif)

1. Open het hulp programma cohorts, selecteer het tabblad **sjabloon galerie** en selecteer **lege gebruikers cohort**.

    ![Nieuwe gebruikers cohort](./media/usage-cohorts/001.png)

    Er zijn drie secties:
   * Een sectie met een afkorting tekst, waar u de cohort uitvoeriger beschrijft voor anderen in uw team.

   * Een sectie para meters, waar u uw eigen para meters, zoals **activiteiten** en andere vervolg keuzelijsten van de vorige twee voor beelden, maakt.

   * Een query sectie, waarin u de cohort definieert met behulp van een analyse query.

     In de sectie query schrijft u [een Analytics-query](/azure/kusto/query). Met de query wordt de bepaalde set rijen geselecteerd waarmee de cohort wordt beschreven die u wilt definiëren. Het hulp programma cohorts voegt vervolgens impliciet een ' | samenvatten per user_Id-component voor de query. Deze gegevens worden weer gegeven onder de query in een tabel, zodat u er zeker van kunt zijn dat uw query resultaten oplevert.

     > [!NOTE]
     > Als u de query niet ziet, probeert u het formaat van de sectie aan te geven om deze hoger te maken en de query te tonen. Met animatie. gif aan het begin van deze sectie wordt het gedrag voor het wijzigen van de grootte geïllustreerd.

2. Kopieer en plak de volgende tekst in de query-editor:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. Selecteer **Query uitvoeren**. Als er geen gebruikers-Id's worden weer gegeven in de tabel, wijzigt u in een land/regio waarin uw toepassing gebruikers heeft.

4. Sla de cohort op en noem deze.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

_Ik heb een cohort van gebruikers gedefinieerd vanuit een bepaald land/regio. Wanneer ik deze cohort in het hulp programma gebruikers vergelijkt om alleen een filter in te stellen voor dat land/deze regio, zie ik verschillende resultaten. Waarom?_

Cohort en filters verschillen. Stel dat u een cohort hebt van gebruikers uit het Verenigd Konink rijk (gedefinieerd als het vorige voor beeld) en vergelijkt u met de resultaten voor het instellen van het filter ' land of regio = Verenigd Konink rijk '.

* De cohort-versie toont alle gebeurtenissen van gebruikers die een of meer gebeurtenissen van het Verenigd Konink rijk in het huidige tijds bereik hebben verzonden. Als u op land of regio splitst, ziet u waarschijnlijk veel landen en regio's.
* De filters-versie toont alleen gebeurtenissen uit het Verenigd Konink rijk. Maar als u op land of regio splitst, ziet u alleen het Verenigd Konink rijk.

## <a name="learn-more"></a>Lees meer

* [Querytaal voor analytische gegevens](../log-query/log-analytics-tutorial.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Gebruikers, sessies, gebeurtenissen](usage-segmentation.md)
* [Gebruikersstromen](usage-flows.md)
* [Overzicht van gebruik](usage-overview.md)