---
title: Aangepaste velden in Azure Monitor (preview) | Microsoft Docs
description: Met de functie voor aangepaste velden van Azure Monitor kunt u uw eigen Doorzoek bare velden maken op basis van records in een Log Analytics-werk ruimte die aan de eigenschappen van een verzameld record worden toegevoegd.  In dit artikel wordt het proces voor het maken van een aangepast veld beschreven en vindt u een gedetailleerd overzicht van een voorbeeld gebeurtenis.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/23/2019
ms.openlocfilehash: e7ddeaf1273a9e3b5608336b24c07fad3c3f4525
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031035"
---
# <a name="create-custom-fields-in-a-log-analytics-workspace-in-azure-monitor-preview"></a>Aangepaste velden in een Log Analytics-werk ruimte maken in Azure Monitor (preview)

> [!NOTE]
> In dit artikel wordt beschreven hoe u tekst gegevens kunt parseren in een Log Analytics werkruimte die wordt verzameld. Het is raadzaam om tekst gegevens in een query filter te parseren nadat deze zijn verzameld volgens de richt lijnen die worden beschreven in [tekst gegevens parseren in azure monitor](./parse-text.md). Het biedt verschillende voor delen ten opzichte van het gebruik van aangepaste velden.

> [!IMPORTANT]
> Aangepaste velden verg Roten de hoeveelheid gegevens die in de Log Analytics werkruimte wordt verzameld, waardoor uw kosten kunnen worden verhoogd. Zie [gebruik en kosten beheren met Azure monitor logboeken](./manage-cost-storage.md#pricing-model) voor meer informatie.

Met de functie voor **aangepaste velden** van Azure monitor kunt u bestaande records in uw log Analytics-werk ruimte uitbreiden door uw eigen Doorzoek bare velden toe te voegen.  Aangepaste velden worden automatisch ingevuld op basis van gegevens die zijn geëxtraheerd uit andere eigenschappen in dezelfde record.

![Diagram toont een oorspronkelijke record die is gekoppeld aan een gewijzigde record in een Log Analytics-werk ruimte met eigenschaps waarde-paren die zijn toegevoegd aan de oorspronkelijke eigenschap in de gewijzigde record.](media/custom-fields/overview.png)

De voorbeeld record hieronder bevat bijvoorbeeld nuttige gegevens die in de beschrijving van de gebeurtenis worden overspoeld. Als u deze gegevens uitpakt in een afzonderlijke eigenschap, wordt deze beschikbaar voor acties als sorteren en filteren.

![Voorbeeld extractie](media/custom-fields/sample-extract.png)

> [!NOTE]
> In de preview-versie bent u beperkt tot 100 aangepaste velden in uw werk ruimte.  Deze limiet wordt uitgebreid wanneer deze functie algemene Beschik baarheid bereikt.

## <a name="creating-a-custom-field"></a>Een aangepast veld maken
Wanneer u een aangepast veld maakt, moet Log Analytics begrijpen welke gegevens moeten worden gebruikt om de waarde ervan in te vullen.  Er wordt gebruikgemaakt van een technologie van micro soft Research met de naam FlashExtract om snel deze gegevens te identificeren.  In plaats van dat u expliciete instructies hoeft op te geven, Azure Monitor informatie over de gegevens die u wilt extra heren uit de voor beelden die u opgeeft.

In de volgende secties vindt u de procedure voor het maken van een aangepast veld.  Onder in dit artikel vindt u een overzicht van een extractie van een voor beeld.

> [!NOTE]
> Het aangepaste veld wordt gevuld als records die overeenkomen met de opgegeven criteria worden toegevoegd aan de werk ruimte Log Analytics, zodat deze alleen wordt weer gegeven op records die worden verzameld nadat het aangepaste veld is gemaakt.  Het aangepaste veld wordt niet toegevoegd aan records die zich al in het gegevens archief bevinden wanneer het wordt gemaakt.
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>Stap 1: records identificeren die het aangepaste veld bevatten
De eerste stap is het identificeren van de records waarmee het aangepaste veld wordt opgehaald.  U begint met een [standaard logboek query](./log-query-overview.md) en selecteert vervolgens een record om te fungeren als het model dat Azure monitor leert van.  Wanneer u opgeeft dat u gegevens wilt extra heren in een aangepast veld, wordt de **wizard veld uitpakken** geopend, waarin u de criteria kunt valideren en verfijnen.

1. Ga naar **Logboeken** en gebruik een [query om de records op te halen](./log-query-overview.md) die het aangepaste veld zullen bevatten.
2. Selecteer een record die Log Analytics gebruikt als model voor het extra heren van gegevens om het aangepaste veld in te vullen.  U kunt de gegevens die u wilt extra heren uit deze record identificeren en Log Analytics gebruikt deze informatie om te bepalen wat de logica is om het aangepaste veld voor alle vergelijk bare records te vullen.
3. Vouw de record eigenschappen uit, klik op het weglatings teken links van de bovenste eigenschap van de record en selecteer **velden extra heren uit**.
4. De **wizard voor het uitpakken van velden** wordt geopend en de record die u hebt geselecteerd, wordt weer gegeven in de **hoofd voorbeeld** kolom.  Het aangepaste veld wordt gedefinieerd voor records met dezelfde waarden in de eigenschappen die zijn geselecteerd.  
5. Als de selectie niet precies is wat u wilt, selecteert u extra velden om de criteria te verfijnen.  Als u de veld waarden voor de criteria wilt wijzigen, moet u annuleren en selecteert u een andere record die overeenkomt met de criteria die u wilt.

### <a name="step-2---perform-initial-extract"></a>Stap 2: Voer de eerste extractie uit.
Zodra u de records met het aangepaste veld hebt geïdentificeerd, identificeert u de gegevens die u wilt extra heren.  Log Analytics gebruikt deze informatie om Vergelijk bare patronen in vergelijk bare records te identificeren.  In de stap hierna kunt u de resultaten valideren en meer informatie geven over Log Analytics voor gebruik in de analyse.

1. Markeer de tekst in de voorbeeld record die u wilt vullen van het aangepaste veld.  Vervolgens wordt er een dialoog venster weer gegeven waarin u een naam en gegevens type voor het veld kunt opgeven en de eerste extractie kunt uitvoeren.  De tekens **\_ CF** worden automatisch toegevoegd.
2. Klik op **extra heren** om een analyse van de verzamelde records uit te voeren.  
3. In de secties **samen vatting** en **Zoek resultaten** worden de resultaten van het uittreksel weer gegeven, zodat u de nauw keurigheid ervan kunt controleren.  **Samen vatting** geeft de criteria weer die worden gebruikt voor het identificeren van records en een aantal voor elk van de geïdentificeerde gegevens waarden.  **Zoek resultaten** bieden een gedetailleerde lijst met records die voldoen aan de criteria.

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>Stap 3: Controleer de nauw keurigheid van het uitpakken en maak het aangepaste veld
Wanneer u de eerste extractie hebt uitgevoerd, worden de resultaten van Log Analytics weer gegeven op basis van de gegevens die al zijn verzameld.  Als de resultaten nauw keurig lijken, kunt u het aangepaste veld maken zonder verdere werkzaamheden.  Als dat niet het geval is, kunt u de resultaten verfijnen zodat Log Analytics de logica kunt verbeteren.

1. Als de waarden in de eerste extractie niet juist zijn, klikt u op het **bewerkings** pictogram naast een onnauwkeurige record en selecteert u **deze markering wijzigen** om de selectie te wijzigen.
2. De vermelding wordt gekopieerd naar de sectie **aanvullende voor beelden** onder het **hoofd voorbeeld**.  U kunt de markering hier aanpassen om Log Analytics inzicht te krijgen in de selectie die moet worden aangebracht.
3. Klik op **extra heren** om deze nieuwe informatie te gebruiken om alle bestaande records te evalueren.  De resultaten kunnen worden gewijzigd voor andere records dan het account dat u zojuist hebt gewijzigd op basis van deze nieuwe informatie.
4. Ga door met het toevoegen van correcties totdat alle records in het uittreksel correct de gegevens identificeren om het nieuwe aangepaste veld in te vullen.
5. Klik op **extract opslaan** wanneer u tevreden bent met de resultaten.  Het aangepaste veld is nu gedefinieerd, maar wordt niet toegevoegd aan een record.
6. Wacht tot nieuwe records die overeenkomen met de opgegeven criteria worden verzameld en voer de zoek opdracht in logboek opnieuw uit. Nieuwe records moeten het aangepaste veld bevatten.
7. Gebruik het aangepaste veld, zoals elke andere record eigenschap.  U kunt dit gebruiken om gegevens samen te voegen en te groeperen en deze zelfs te gebruiken voor het maken van nieuwe inzichten.

## <a name="viewing-custom-fields"></a>Aangepaste velden weer geven
U kunt een lijst met alle aangepaste velden in uw beheer groep weer geven in het menu **Geavanceerde instellingen** van uw log Analytics werk ruimte in de Azure Portal.  Selecteer **gegevens** en vervolgens **aangepaste velden** voor een lijst met alle aangepaste velden in uw werk ruimte.  

![Aangepaste velden](media/custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Een aangepast veld verwijderen
Er zijn twee manieren om een aangepast veld te verwijderen.  De eerste is de optie **verwijderen** voor elk veld bij het weer geven van de volledige lijst zoals hierboven is beschreven.  De andere methode is om een record op te halen en op de knop aan de linkerkant van het veld te klikken.  Het menu bevat een optie voor het verwijderen van het aangepaste veld.

## <a name="sample-walkthrough"></a>Walkthrough voor voorbeeld
In het volgende gedeelte wordt een volledig voor beeld van het maken van een aangepast veld door lopen.  In dit voor beeld wordt de service naam in Windows-gebeurtenissen geëxtraheerd die duidt op een status wijziging van de service.  Dit is afhankelijk van gebeurtenissen die door service besturings beheer zijn gemaakt tijdens het opstarten van het systeem op Windows-computers.  Als u dit voor beeld wilt volgen, moet u [informatie gebeurtenissen verzamelen voor het systeem logboek](../agents/data-sources-windows-events.md).

We voeren de volgende query in om alle gebeurtenissen te retour neren van service besturings beheer met de gebeurtenis-ID 7036. Dit is de gebeurtenis die aangeeft dat een service wordt gestart of gestopt.

![Scherm afbeelding toont een query voor een gebeurtenis bron en-ID.](media/custom-fields/query.png)

Vervolgens selecteert u een record met gebeurtenis-ID 7036 en vouwt u deze uit.

![Bron record](media/custom-fields/source-record.png)

Aangepaste velden worden gedefinieerd door te klikken op de ellips naast de bovenste eigenschap.

![Velden extra heren](media/custom-fields/extract-fields.png)

De **wizard voor het uitpakken van velden** wordt geopend en de velden Eventlog en **Event** **gebeurtenis** zijn geselecteerd in de **hoofd voorbeeld** kolom.  Dit geeft aan dat het aangepaste veld wordt gedefinieerd voor gebeurtenissen uit het systeem logboek met de gebeurtenis-ID 7036.  Dit is voldoende, dus we hoeven geen andere velden te selecteren.

![Hoofd voorbeeld](media/custom-fields/main-example.png)

We markeren de naam van de service in de eigenschap **RenderedDescription** en gebruiken de **service** om de naam van de service te identificeren.  Het aangepaste veld wordt **Service_CF** genoemd. Het veld type in dit geval is een teken reeks, dus we kunnen dat ongewijzigd laten.

![Veld Titel](media/custom-fields/field-title.png)

We zien dat de service naam correct is geïdentificeerd voor sommige records, maar niet voor andere.   In de **Zoek resultaten** ziet u dat een deel van de naam voor de **WMI-prestatie adapter** niet is geselecteerd.  In de **samen vatting** ziet u dat er in het installatie programma voor de ene record **modules** is geïdentificeerd in plaats van **Windows modules**.  

![Scherm opname van de onderdelen van de service naam die in het deel venster Zoek resultaten zijn gemarkeerd en een onjuiste service naam die in de samen vatting is gemarkeerd.](media/custom-fields/search-results-01.png)

De record voor de **WMI-prestatie adapter** wordt gestart.  U klikt op het bewerkings pictogram en **wijzigt vervolgens deze markering**.  

![Markering wijzigen](media/custom-fields/modify-highlight.png)

We verg Roten het hooglicht om het woord **WMI** toe te voegen en vervolgens het uitpakken opnieuw uit te voeren.  

![Extra voor beeld](media/custom-fields/additional-example-01.png)

We kunnen zien dat de vermeldingen voor de **WMI-prestatie adapter** zijn gecorrigeerd en dat log Analytics ook die gegevens gebruiken om de records voor het **installatie programma** van de Windows-module te corrigeren.

![Scherm opname van de volledige service naam die is gemarkeerd in het deel venster Zoek resultaten en de juiste service namen die in de samen vatting zijn gemarkeerd.](media/custom-fields/search-results-02.png)

We kunnen nu een query uitvoeren waarmee wordt gecontroleerd of **Service_CF** is gemaakt, maar nog niet is toegevoegd aan een record. Dat komt doordat het aangepaste veld niet werkt voor bestaande records, zodat er moet worden gewacht tot nieuwe records worden verzameld.

![Aantal initialen](media/custom-fields/initial-count.png)

Nadat de nieuwe gebeurtenissen zijn verzameld, kunnen we zien dat het veld **Service_CF** nu wordt toegevoegd aan de records die voldoen aan de criteria.

![Eind resultaten](media/custom-fields/final-results.png)

We kunnen nu het aangepaste veld gebruiken, net als elke andere record eigenschap.  Ter illustratie maken we een query die wordt gegroepeerd op het nieuwe **Service_CF** veld om te controleren welke services het meest actief zijn.

![Groeperen op query](media/custom-fields/query-group.png)

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [logboek query's](./log-query-overview.md) voor het bouwen van query's met aangepaste velden voor criteria.
* Bewaak [aangepaste logboek bestanden](../agents/data-sources-custom-logs.md) die u parseert met behulp van aangepaste velden.