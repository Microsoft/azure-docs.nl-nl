---
title: Kaarten genereren
description: In dit artikel wordt beschreven hoe u kaarten kunt genereren in azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: d0176fd337abd37f97a6dbe5cf7c68ccf94114d0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102173861"
---
# <a name="generate-maps"></a>Kaarten genereren

Met Azure FarmBeats kunt u de volgende kaarten genereren met behulp van satelliet afbeeldings-en sensor gegevens invoer. Met Maps kunt u de geografische locatie van uw farm bekijken en de juiste locatie voor uw apparaten identificeren.

  - **Kaart voor plaatsing van sensor**: bevat aanbevelingen over het aantal Sens oren dat moet worden gebruikt en waar u ze op een farm plaatst.
  - **Kaart voor satelliet indices**: toont de vegetatie-index en de water index voor een farm.
  - **Bodem vocht heatmap**: toont bodem vocht distributie door satelliet gegevens en sensor gegevens te weigeren.

## <a name="sensor-placement-map"></a>Kaart voor sensor plaatsing

Een FarmBeats sensor placement kaart helpt u bij het plaatsen van bodem vocht Sens oren. De kaart uitvoer bestaat uit een lijst met coördinaten voor de implementatie van sensors. De invoer van deze Sens oren wordt samen met satelliet afbeelding gebruikt voor het genereren van de bodem vocht heatmap.

Deze kaart wordt afgeleid door de Canopy te segmenteren zoals in het hele jaar over meerdere datums heen is gezien. Zelfs bare bodem en gebouwen maken deel uit van de Canopy. U kunt Sens oren verwijderen die niet vereist zijn op de locatie. Deze kaart is voor hulp en u kunt de positie en getallen enigszins aanpassen op basis van uw aangepaste kennis. Door Sens oren toe te voegen, worden de heatmap-resultaten niet Regress, maar is er sprake van een verslechterde heatmap nauw keurigheid als het sensor nummer wordt gereduceerd.

## <a name="before-you-begin"></a>Voordat u begint

Voldoen aan de volgende vereisten voordat u een sensor plaatsing kaart wilt genereren:

- De farm grootte moet meer dan één acre zijn.
- Het aantal beschik bare verklikker scènes in de Cloud moet meer dan zes zijn voor het geselecteerde datum bereik.
- Ten minste zes beschik bare verklikker scènes in de Cloud moeten een genormaliseerde NDVI (vegetatie index) hebben die groter is dan of gelijk is aan 0,3.

    > [!NOTE]
    > Met Sentinel kunnen slechts twee gelijktijdige threads per gebruiker worden toegestaan. Als gevolg hiervan kunnen taken in de wachtrij worden geplaatst. Dit kan langer duren.

### <a name="dependencies-on-sentinel"></a>Afhankelijkheden van Sentinel

De volgende afhankelijkheden zijn van toepassing op Sentinel:

- We zijn afhankelijk van de verklikker prestaties voor het downloaden van satelliet installatie kopieën. Controleer de status van de verklikker prestaties en de onderhouds [activiteiten](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome).
- Met Sentinel kunnen Maxi maal twee gelijktijdige threads per gebruiker worden [gedownload](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services) .
- Het genereren van precisie kaarten wordt beïnvloed door middel van de [verklikker dekking en de frequentie van herbezoek]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage).

## <a name="create-a-sensor-placement-map"></a>Een kaart voor het plaatsen van sensors maken
In deze sectie vindt u informatie over de procedures voor het maken van kaarten voor sensor plaatsing.

> [!NOTE]
> U kunt een sensor plaatsing kaart initiëren op de pagina **kaarten** of in de vervolg keuzelijst **precisie kaarten genereren** op de pagina **Details van Farm** .

Volg deze stappen.

1. Ga op de start pagina naar **kaarten** vanuit het navigatie menu links.
2. Selecteer **kaarten maken** en selecteer **positie van sensor** in de vervolg keuzelijst.

    ![Positie van sensor selecteren](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. Nadat u de **positie** van de sensor hebt geselecteerd, wordt het venster **sensor plaatsing** weer gegeven.

    ![Plaatsings venster sensor](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. Selecteer een farm in de vervolg keuzelijst **Farm** .
   Als u een farm wilt zoeken en selecteren, kunt u in de vervolg keuzelijst schuiven of de naam van de farm in het tekstvak invoeren.
5. Als u een kaart voor het afgelopen jaar wilt genereren, selecteert u **Aanbevolen**.
6. Als u een kaart voor een aangepast datum bereik wilt genereren, selecteert u de optie **datum bereik selecteren**. Voer de begin-en eind datum in voor het genereren van de sensor plaatsing kaart.
7. Selecteer **Maps genereren**.
 Er wordt een bevestigings bericht met taak details weer gegeven.

  Zie de sectie **taken weer geven** voor meer informatie over de taak status. Als de status van de taak *niet* wordt weer gegeven, wordt er een gedetailleerd fout bericht weer gegeven op de knop Info van de status *mislukt* . In dit geval herhaalt u de vorige stappen en probeert u het opnieuw.

  Als het probleem zich blijft voordoen, raadpleegt u de sectie [problemen oplossen](troubleshoot-azure-farmbeats.md) of neemt u contact op met het [Azure FarmBeats-forum voor ondersteuning](https://aka.ms/FarmBeatsMSDN) bij relevante Logboeken.

### <a name="view-and-download-a-sensor-placement-map"></a>Een kaart voor het plaatsen van sensors weer geven en downloaden

Volg deze stappen.

1. Ga op de start pagina naar **kaarten** vanuit het navigatie menu links.

    ![Selecteer toewijzingen in het navigatie menu links](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Selecteer **filter** in de linkernavigatiebalk van het venster.
  In het venster **filter** worden zoek criteria weer gegeven.

    ![Filter venster](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Selecteer waarden voor **type**, **datum** en **naam** in de vervolg keuzelijst. Selecteer vervolgens **Toep assen** om te zoeken naar de kaart die u wilt weer geven.
  De datum waarop de taak is gemaakt, wordt weer gegeven in de notatie type_farmname_YYYY-MM-DD.
4. Blader door de lijst met kaarten die beschikbaar zijn via de Navigatie balken aan het einde van de pagina.
5. Selecteer de kaart die u wilt weer geven. In een pop-upvenster wordt het voor beeld voor de geselecteerde kaart weer gegeven.
6. Selecteer **downloaden** en down load het geojson-bestand met sensor coördinaten.

    ![Voor beeld van sensor plaatsings kaart](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>Kaart voor satelliet indexen

In de volgende secties worden de procedures beschreven voor het maken en weer geven van een satelliet indices-toewijzing.

> [!NOTE]
> U kunt een kaart met satelliet-indexen initiëren via de pagina **kaarten** of vanuit de vervolg keuzelijst **precisie kaarten genereren** op de pagina **Details van Farm** .

FarmBeats biedt u de mogelijkheid om NDVI, Enhanced vegetatie index (EVI) en genormaliseerde diff (NDWI)-kaarten voor farms te genereren. Deze indices helpen te bepalen hoe het gewas momenteel groeit, of in het verleden is toegenomen, en de representatieve water niveaus op het wegdek.


> [!NOTE]
> Er is een verklikker afbeelding vereist voor de dagen waarvoor de kaart wordt gegenereerd.


## <a name="create-a-satellite-indices-map"></a>Een kaart met satelliet indices maken

Volg deze stappen.

1. Ga op de start pagina naar **kaarten** vanuit het navigatie menu links.
2. Selecteer **kaarten maken** en selecteer **satelliet-indexen** in de vervolg keuzelijst.

    ![Satelliet-Indexen selecteren in de vervolg keuzelijst](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. Nadat u **satelliet-indexen** hebt geselecteerd, wordt het venster **satelliet-indexen** weer gegeven.

    ![Venster met satelliet-indexen](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. Selecteer een farm in de vervolg keuzelijst.
   Als u een farm wilt zoeken en selecteren, kunt u in de vervolg keuzelijst schuiven of de naam van de farm invoeren.   
5. Als u een kaart wilt genereren voor de afgelopen week, selecteert u **deze week**.
6. Als u een kaart voor een aangepast datum bereik wilt genereren, selecteert u de optie **datum bereik selecteren**. Voer de begin-en eind datum in waarvoor u de toewijzing van de satelliet indexen wilt genereren.
7. Selecteer **Maps genereren**.
    Er wordt een bevestigings bericht met taak details weer gegeven.

    ![Bevestigings bericht voor kaart met satelliet-indexen](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    Zie de sectie **taken weer geven** voor meer informatie over de taak status. Als de status van de taak *niet* wordt weer gegeven, wordt er een gedetailleerd fout bericht weer gegeven op de knop Info van de status *mislukt* . In dit geval herhaalt u de vorige stappen en probeert u het opnieuw.

    Als het probleem zich blijft voordoen, raadpleegt u de sectie [problemen oplossen](troubleshoot-azure-farmbeats.md) of neemt u contact op met het [Azure FarmBeats-forum voor ondersteuning](https://aka.ms/FarmBeatsMSDN) bij relevante Logboeken.

### <a name="view-and-download-a-map"></a>Een kaart weer geven en downloaden

Volg deze stappen.

1. Ga op de start pagina naar **kaarten** vanuit het navigatie menu links.

    ![Kaarten selecteren](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Selecteer **filter** in de linkernavigatiebalk van het venster. In het venster **filter** worden zoek criteria weer gegeven.

    ![Filter venster bevat zoek criteria](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Selecteer waarden voor **type**, **datum** en **naam** in de vervolg keuzelijst. Selecteer vervolgens **Toep assen** om te zoeken naar de kaart die u wilt weer geven.
  De datum waarop de taak is gemaakt, wordt weer gegeven in de notatie type_farmname_YYYY-MM-DD.

4. Blader door de lijst met kaarten die beschikbaar zijn via de Navigatie balken aan het einde van de pagina.
5. Voor elke combi natie van naam en **datum** van het **bedrijf** zijn de volgende drie toewijzingen beschikbaar:
    - NDVI
    - EVI
    - NDWI
6. Selecteer de kaart die u wilt weer geven. In een pop-upvenster wordt het voor beeld voor de geselecteerde kaart weer gegeven.
7. Selecteer **down load** in de vervolg keuzelijst om de Download indeling te selecteren. De kaart wordt gedownload en opgeslagen in uw lokale map op uw computer.

    ![Preview van geselecteerde satelliet indexen](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="soil-moisture-heatmap"></a>Bodem vocht heatmap

Bodem vocht is het water dat zich in de ruimten tussen de bodem deeltjes bevindt.Het vocht heatmap van de bodem helpt u bij het begrijpen van de bodem vocht gegevens, met een hoge resolutie in uw farm. Voor het genereren van een nauw keurige en bruikbare bodem vocht heatmap is een uniforme implementatie van Sens oren vereist. Alle Sens oren moeten van dezelfde provider zijn. Verschillende providers hebben verschillen in de manier waarop bodem vocht wordt gemeten, samen met verschillen in kalibratie. De heatmap wordt gegenereerd voor een bepaalde diepte door gebruik te maken van de Sens oren die op die diepte zijn geïmplementeerd.

### <a name="before-you-begin"></a>Voordat u begint

Voldoen aan de volgende vereisten voordat u probeert een bodem vocht te heatmap te genereren:

- Ten minste drie bodem vocht Sens oren moeten worden geïmplementeerd. Maak geen bodem vocht heatmap voordat Sens oren worden geïmplementeerd en gekoppeld aan de farm.
- Het genereren van een bodem vocht heatmap wordt beïnvloed door verklikker bedekking, Cloud cover en Cloud Shadow. Ten minste één Cloud-Free Sentinel-scène moet beschikbaar zijn voor de afgelopen 120 dagen, van de dag waarop de bodem vochtigheid heatmap is aangevraagd.
- Ten minste de helft van de Sens oren die in de farm zijn geïmplementeerd, moet online zijn en gegevens moeten worden gestreamd naar het datahub.
- De heatmap moet worden gegenereerd met behulp van sensor metingen van dezelfde provider.


## <a name="create-a-soil-moisture-heatmap"></a>Een bodem vocht heatmap maken

Volg deze stappen.

1. Ga op de start pagina naar **kaarten** vanuit het navigatie menu links om de pagina **kaarten** weer te geven.
2. Selecteer **kaarten maken** en selecteer **bodem vocht** in de vervolg keuzelijst.

    ![Selecteer bodem vocht in de vervolg keuzelijst](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. Nadat u **bodem vocht** selecteert, wordt het **bodem vocht** venster weer gegeven.

    ![Venster bodem vocht](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. Selecteer een farm in de vervolg keuzelijst **Farm** .
   Als u uw farm wilt zoeken en selecteren, kunt u in de vervolg keuzelijst schuiven of de naam van de farm invoeren in de vervolg keuzelijst **Farm selecteren** .
5. Selecteer in de vervolg keuzelijst meting van de **bodem vochtigheid selecteren** de meting van de bodem vocht sensor (diepte) waarvoor u de kaart wilt genereren.
Als u de sensor meting wilt vinden, gaat u naar **Sens oren** en selecteert u een bodem vocht sensor. Gebruik vervolgens de waarde in **naam meten** onder het gedeelte **sensor eigenschappen** .
6. Selecteer een van de opties voor het genereren van een kaart voor **vandaag** of **deze week**.
7. Als u een kaart voor een aangepast datum bereik wilt genereren, selecteert u de optie **datum bereik selecteren**. Voer de begin-en eind datum in waarvoor u de bodem vocht heatmap wilt genereren.
8. Selecteer **Maps genereren**.
 Er wordt een bevestigings bericht met taak details weer gegeven.

   ![Bevestigings bericht voor kaart van bodem vochtigheid](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    Zie de sectie **taken weer geven** voor meer informatie over de taak status. Als de status van de taak *niet* wordt weer gegeven, wordt er een gedetailleerd fout bericht weer gegeven op de knop Info van de status *mislukt* . In dit geval herhaalt u de vorige stappen en probeert u het opnieuw.

    Als het probleem zich blijft voordoen, raadpleegt u de sectie [problemen oplossen](troubleshoot-azure-farmbeats.md) of neemt u contact op met het [Azure FarmBeats-forum voor ondersteuning](https://aka.ms/FarmBeatsMSDN) bij relevante Logboeken.

### <a name="view-and-download-a-map"></a>Een kaart weer geven en downloaden

Volg deze stappen.

1. Ga op de start pagina naar **kaarten** vanuit het navigatie menu links.

    ![Ga naar Maps](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Selecteer **filter** in de linkernavigatiebalk van het venster. In het venster **filter** wordt weer gegeven waar u naar kaarten kunt zoeken.

    ![Filter selecteren in het navigatie deel venster aan de linkerkant](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  Selecteer waarden voor **type**, **datum** en **naam** in de vervolg keuzelijst. Selecteer vervolgens **Toep assen** om te zoeken naar de kaart die u wilt weer geven. De datum waarop de taak is gemaakt, wordt weer gegeven in de notatie type_farmname_YYYY-MM-DD.
4. Selecteer het **Sorteer** pictogram naast de tabelkop teksten om te sorteren op basis van de **Farm**, **datum**, **gemaakt op**, **taak-id** en **taak type**.
5. Blader door de lijst met kaarten die beschikbaar zijn met behulp van de navigatie knoppen aan het einde van de pagina.
6. Selecteer de kaart die u wilt weer geven. In een pop-upvenster wordt het voor beeld voor de geselecteerde kaart weer gegeven.
7. Selecteer **down load** in de vervolg keuzelijst om de Download indeling te selecteren. De kaart wordt gedownload en opgeslagen in de opgegeven map.

    ![Voor beeld van bodem vocht heatmap](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
