---
title: Apparaatgegevens analyseren in uw Azure IoT Central-toepassing | Microsoft Docs
description: Analyseer gegevens van apparaten in uw Azure IoT Central-toepassing.
author: ankitscribbles
ms.author: ankitgup
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 8f329b6377915a3df6e3f3e212cbd8d41f9ed80d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90017657"
---
# <a name="how-to-use-analytics-to-analyze-device-data"></a>Informatie over het gebruik van Analytics voor het analyseren van apparaatgegevens

*Dit artikel is van toepassing op operators, opbouwfuncties en beheerders.*

Azure IoT Central biedt uitgebreide analyse mogelijkheden voor het analyseren van historische trends en het correleren van verschillende teleelementen van uw apparaten. Ga naar **Analytics** in het linkerdeel venster om aan de slag te gaan.

## <a name="understanding-the-analytics-ui"></a>Uitleg over de gebruikers interface van Analytics
De gebruikers interface van Analytics bestaat uit drie hoofd onderdelen:
- **Paneel voor gegevens configuratie:** Begin met het configuratie venster en selecteer de apparaatgroep waarvoor u de gegevens wilt analyseren. Selecteer vervolgens de telemetrie die u wilt analyseren en selecteer de aggregatie methode voor elke telemetrie. **Splitsen op** besturings element helpt bij het groeperen van de gegevens met behulp van de eigenschappen van het apparaat als dimensies.

- **Besturings element tijd:** Tijd besturings element wordt gebruikt om de duur te selecteren waarvoor u de gegevens wilt analyseren. U kunt een van de uiteinden van de schuif regelaar tijd slepen om de tijds Panne te selecteren. Tijd beheer heeft ook een schuif regelaar voor de **interval grootte** waarmee de Bucket of de interval grootte wordt bepaald waarmee de gegevens worden samengevoegd. 

- **Besturings element voor grafieken:** Met het besturings element grafiek worden de gegevens gevisualiseerd als een lijn diagram. U kunt de zicht baarheid van specifieke regels in-of uitschakelen door interactie met de grafiek legenda. 


  ![Overzicht van Analytics-gebruikers interface](media/howto-create-analytics/analyticsui.png)


## <a name="querying-your-data"></a>Query's uitvoeren op uw gegevens

U moet beginnen met het kiezen van een **apparaatgroep** en de telemetrie die u wilt analyseren. Wanneer u klaar bent, selecteert u **analyseren** om uw gegevens te visualiseren.

- **Apparaatgroep:** Een [apparaatgroep](tutorial-use-device-groups.md) is een door de gebruiker gedefinieerde groep van uw apparaten. Bijvoorbeeld alle koel kasten in Oakland of alle versie 2,0-wind turbines.

- **Telemetrie:** Selecteer de telemetrie die u wilt analyseren en verkennen. U kunt meerdere webelementen selecteren om samen te analyseren. De standaard aggregatie methode is ingesteld op gemiddeld voor numeriek en aantal voor het gegevens type teken reeks. Ondersteunde aggregatie methoden voor numerieke gegevens typen zijn gemiddelde, maximum, minimum, aantal en, sum.  Ondersteunde aggregatie methoden voor het gegevens type teken reeks zijn aantal.

- **Splitsen op:** Met het besturings element splitsen op kunt u de gegevens groeperen met behulp van de eigenschappen van het apparaat als dimensies. De waarden van de eigenschappen apparaat en Cloud worden samen met de telemetrie gecombineerd als en wanneer deze door het apparaat worden verzonden. Als de eigenschap van de Cloud of het apparaat is bijgewerkt, wordt de telemetrie gegroepeerd op verschillende waarden in de grafiek weer te geven.

    > [!TIP]
    > Als u de gegevens voor elk apparaat afzonderlijk wilt weer geven, selecteert u apparaat-id in het besturings element ' splitsen op '.

## <a name="interacting-with-your-data"></a>Interactie met uw gegevens

Zodra u een query op uw gegevens hebt uitgevoerd, kunt u beginnen met visualiseren in het lijn diagram. U kunt telemetrie weer geven/verbergen, de tijds duur wijzigen, telemetrie weer geven in een gegevens raster.

- **Tijds editor paneel:** Standaard worden gegevens van de afgelopen dag opgehaald. U kunt een van de uiteinden van de tijd schuifregelaar slepen om de tijds duur te wijzigen. U kunt ook het besturings element kalender gebruiken om een van de vooraf gedefinieerde tijds verzamelingen te selecteren of een aangepast tijds bereik te selecteren. Tijd beheer heeft ook een schuif regelaar voor de **interval grootte** waarmee de Bucket of de interval grootte wordt bepaald waarmee de gegevens worden samengevoegd.

    ![Tijd editor](media/howto-create-analytics/timeeditorpanel.png)

    - **Schuif regelaar voor het binnenste datum bereik**: gebruik de twee eindpunt besturings elementen door ze over de gewenste periode te slepen. Dit binnenste datum bereik wordt beperkt door de schuif regelaar voor het buitenste datum bereik.
    
   
    - **Besturings element voor schuif regelaar voor datum bereik**: gebruik de besturings elementen voor het eind punt om het buitenste datum bereik te selecteren. Dit is beschikbaar voor het besturings element binnen het bereik van datums.

    - **Knoppen voor het datum bereik verg Roten** of verkleinen: u kunt uw tijds periode verg Roten of verkleinen door een van de knoppen voor het gewenste interval te selecteren.

    - **Schuif regelaar voor interval grootte**: met deze knop kunt u op dezelfde tijds Panne in-en uitzoomen. Deze actie biedt meer nauw keurige controle over de verplaatsing tussen grote segmenten. U kunt deze gebruiken om gedetailleerde, weer gaven van uw gegevens met hoge resolutie weer te geven, zelfs omlaag tot milliseconden. Het standaard start punt van de schuif regelaar is ingesteld als de meest optimale weer gave van de gegevens uit uw selectie, waarmee de resolutie, de query snelheid en de granulatie worden gebalanceerd.
    
    - **Datum bereik kiezer**: met dit Webbe sturings element kunt u eenvoudig de gewenste datum en tijd selecteren. U kunt ook het besturings element gebruiken om over te scha kelen tussen verschillende tijd zones. Nadat u de wijzigingen hebt aangebracht die u wilt Toep assen op uw huidige werk ruimte, selecteert u opslaan.

    > [!TIP]
    > De interval grootte wordt dynamisch bepaald op basis van de geselecteerde tijds periode. Dankzij kleinere tijds duren kunt u de gegevens samen voegen tot zeer nauw keurige intervallen van Maxi maal een paar seconden.


- **Grafiek legenda:** Diagram legenda toont de geselecteerde telemetrie in de grafiek. U kunt de muis aanwijzer op elk item in de legenda aanwijzen om de focus te verplaatsen naar de grafiek. Wanneer u ' splitsen op ' gebruikt, wordt de telemetrie gegroepeerd op basis van de respectieve waarden van de geselecteerde dimensie. U kunt de zicht baarheid van elke specifieke telemetrie of de hele groep in-of uitschakelen door te klikken op de naam van de groep.  


- **Besturings element voor de indeling van de y-as:** de modus y-as doorloopt de beschik bare opties voor de y-as. Dit besturings element is alleen beschikbaar wanneer verschillende teleelementen worden gevisualiseerd. U kunt de y-as instellen door een van de drie modi te kiezen:

    - **Gestapeld:** Een grafiek voor elke telemetrie wordt gestapeld en elk van de grafieken heeft hun eigen y-as. Deze modus is ingesteld als standaard.
    - **Gedeeld:** Een grafiek voor elke telemetrie wordt op dezelfde y-as getekend.
    - **Overlap ping:** Gebruik deze om meerdere lijnen op dezelfde y-as te stapelen, waarbij de y-as wordt gewijzigd op basis van de geselecteerde regel.

  ![Gegevens rangschikken op de y-as met verschillende visualisatie modi](media/howto-create-analytics/yaxiscontrol.png)

- **Zoom besturings element:** Als u inzoomt, kunt u verder inzoomen op uw gegevens. Als u een periode vindt waarop u zich wilt richten in de resultatenset, gebruikt u de muis aanwijzer om het gebied te halen en sleept u het naar het gewenste eind punt. Klik vervolgens met de rechter muisknop op het geselecteerde gebied en klik op inzoomen.

  ![Inzoomen op de gegevens](media/howto-create-analytics/zoom.png)

Onder het beletsel teken staan er meer grafiek besturings elementen om te communiceren met de gegevens:

- **Raster weer geven:** De resultaten zijn beschikbaar in een tabel indeling, zodat u de specifieke waarde voor elk gegevens punt kunt weer geven.

- **Downloaden als CSV:** De resultaten zijn beschikbaar om te worden geëxporteerd als een bestand met door komma's gescheiden waarden (CSV). Het CSV-bestand bevat gegevens voor elk apparaat. De resultaten worden geëxporteerd met behulp van het opgegeven interval en tijds bestek. 

- **Een markering verwijderen:** Het besturings element drop markering biedt een manier om bepaalde gegevens punten in de grafiek te verankeren. Het is handig wanneer u gegevens wilt vergelijken voor meerdere regels in verschillende Peri Oden.

  ![De raster weergave voor uw analyse weer geven](media/howto-create-analytics/additionalchartcontrols.png)
