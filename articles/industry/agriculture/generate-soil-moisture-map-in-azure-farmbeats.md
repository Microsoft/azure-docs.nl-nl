---
title: Bodem vocht heatmap genereren
description: Hierin wordt beschreven hoe u bodem vocht heatmap in azure FarmBeats genereert
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: dd8d688355e0f71ce77cdbc6012d788c7b16d825
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102173780"
---
# <a name="generate-soil-moisture-heatmap"></a>Bodem vocht heatmap genereren

Bodem vocht is het water dat in de ruimten tussen de bodem deeltjes wordt bewaard.Bodem vocht heatmap helpt u bij het begrijpen van de vocht gegevens op elke wille keurige diepte en met een hoge resolutie in uw farms. Om een nauw keurige en bruikbare bodem vocht heatmap te genereren, is een uniforme implementatie van Sens oren van dezelfde provider vereist. Verschillende providers hebben verschillen in de manier waarop bodem vocht wordt gemeten, samen met verschillen in kalibratie. De heatmap wordt gegenereerd voor een bepaalde diepte met behulp van de Sens oren die op die diepte zijn geïmplementeerd.

In dit artikel wordt het proces beschreven voor het genereren van een bodem vocht heatmap voor uw farm, met behulp van de Azure FarmBeats-Accelerator. In dit artikel leert u het volgende:

- [Farms maken](#create-a-farm)
- [Sens oren toewijzen aan Farms](#get-soil-moisture-sensor-data-from-partner)
- [Bodem vocht heatmap genereren](#generate-soil-moisture-heatmap)

## <a name="before-you-begin"></a>Voordat u begint

Zorg ervoor dat:  

- Een Azure-abonnement.
- Een actief exemplaar van Azure FarmBeats.
- Er zijn mini maal drie sensors voor bodem vochtigheid beschikbaar voor de farm.

## <a name="create-a-farm"></a>Een farm maken

Een farm is een geografisch gedeelte waarvan u een bodem vocht heatmap wilt maken. U kunt een farm maken met behulp van de [Farms-API](https://aka.ms/FarmBeatsDatahubSwagger) of in de [FarmsBeats-gebruikers interface](manage-farms-in-azure-farmbeats.md#create-farms)

## <a name="deploy-sensors"></a>Sens oren implementeren

U moet de bodem vocht Sens oren in de farm fysiek implementeren. U kunt bodem vocht Sens oren kopen bij een van onze goedgekeurde partners: [Davis-instrumenten](https://www.davisinstruments.com/product/enviromonitor-gateway/) en [Teralytic](https://teralytic.com/). U moet zich afstemmen met uw sensor provider om de fysieke installatie op uw farm uit te voeren.

## <a name="get-soil-moisture-sensor-data-from-partner"></a>Gegevens van de bodem vocht sensor ophalen van de partner

Naarmate Sens oren beginnen met streamen, de gegevens in het dash board van de partner gegevens, kunnen ze de gegevens in azure FarmBeats. Dit kan worden gedaan vanuit de partner toepassing.

Als u bijvoorbeeld Davis Sens oren hebt gekocht, meldt u zich aan bij uw weer Link-account en geeft u de vereiste referenties op om de gegevens streaming in te scha kelen in azure FarmBeats. Volg de instructies van [sensor gegevens ophalen](get-sensor-data-from-sensor-partner.md#get-sensor-data-from-sensor-partners)om de vereiste referenties op te halen.

Zodra u uw referenties hebt ingevoerd en **verzenden** hebt geselecteerd voor de partner toepassing, kunt u de gegevens laten stromen naar Azure FarmBeats.

### <a name="assign-soil-moisture-sensors-to-the-farm"></a>Bodem vocht Sens oren toewijzen aan de farm

Zodra u uw sensor account hebt gekoppeld aan Azure FarmBeats, moet u de bodem sensoren aan de gewenste farm toewijzen.

1.  Op de start pagina selecteert u **Farms** in het menu. de lijst pagina **Farms** wordt weer gegeven.
2.  Selecteer **MyFarm**  >  **apparaten toevoegen**.
3.  Het venster **apparaten toevoegen** wordt weer gegeven. Selecteer een apparaat dat is gekoppeld aan de bodem sensoren voor uw farm.

    ![Scherm opname van het venster apparaten toevoegen.](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Selecteer **apparaten toevoegen**.     

## <a name="generate-soil-moisture-heatmap"></a>Bodem vocht heatmap genereren

Deze stap bestaat uit het maken van een taak of een langlopende bewerking waarmee de bodem vocht heatmap wordt gegenereerd voor uw farm.

1.  Op de start pagina gaat u naar **Farms** in het navigatie menu links om de pagina Farms weer te geven.
2.  Selecteer **MyFarm**.
3.  Selecteer op de pagina **Farm Details** de optie **precisie toewijzing genereren**.
4.  Selecteer in de vervolg keuzelijst **bodem vocht**.
5.  Selecteer in het venster **bodem vocht** **deze week**.
6.  Voer in de **meting** **bodem vochtigheid selecteren** de meting in die u wilt gebruiken voor de kaart.
    Als u de sensor meting wilt vinden, selecteert u in **Sens oren** een aarde vocht sensor. Gebruik bij **sensor eigenschappen** de waarde voor de **meting naam** .

    ![Scherm opname van het vocht scherm van de bodem.](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)


7.  Selecteer **Maps genereren**.
    Er wordt een bevestigings bericht met taak details weer gegeven. Zie taak status in Jobs voor meer informatie.

    >[!NOTE]
    > Het duurt ongeveer drie tot vier uur voordat de taak is voltooid.

### <a name="download-the-soil-moisture-heatmap"></a>Down load de bodem vocht heatmap

Voer de volgende stappen uit:

1. Controleer op de pagina **taken** de **taak status** voor de taak die u in de laatste procedure hebt gemaakt.
2. Wanneer de taak status **geslaagd** wordt weer gegeven, selecteert u **kaarten** in het menu.
3. Zoek de kaart op de dag waarop deze is gemaakt in de indeling <bodem-moisture_MyFarm_YYYY-MM-DD>.
4. Selecteer een kaart in de kolom **naam** . er wordt een pop-upvenster weer gegeven met het voor beeld van de geselecteerde kaart.
5. Selecteer **Downloaden**. De kaart wordt gedownload en opgeslagen in de lokale map van uw computer.

    ![Een Farm Beats-project](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)

## <a name="next-steps"></a>Volgende stappen

Nu u een bodem vocht heatmap hebt gegenereerd, leert u hoe u de [plaatsing van sensors](generate-maps-in-azure-farmbeats.md#sensor-placement-map) en [historische telemetriegegevens](ingest-historical-telemetry-data-in-azure-farmbeats.md)kunt genereren. 
