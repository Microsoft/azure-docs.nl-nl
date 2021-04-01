---
title: Werken met kaarten voor binnen Azure Maps Maker (preview-versie)
description: In dit artikel worden concepten geïntroduceerd die van toepassing zijn op Azure Maps Creator-Services (preview-versie)
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4ab00317e71f832bb677c4c7587e2356a37cb7a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96903561"
---
# <a name="creator-preview-for-indoor-maps"></a>Creator (preview) voor kaarten in de binnenste


> [!IMPORTANT]
> Azure Maps Creator-services zijn momenteel beschikbaar als openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.


In dit artikel worden de concepten en hulpprogram ma's geïntroduceerd die van toepassing zijn op Azure Maps Maker. We raden u aan dit artikel te lezen voordat u begint met het gebruik van de Azure Maps Creator-API en SDK.

U kunt Creator gebruiken om toepassingen te ontwikkelen met kaart functies op basis van de kaart gegevens in de binnen. In dit artikel wordt het proces beschreven voor het uploaden, converteren, maken en gebruiken van uw kaart gegevens. De volledige werk stroom wordt geïllustreerd in het onderstaande diagram.

![Werk stroom voor gegevens van Maker kaart](./media/creator-indoor-maps/workflow.png)

## <a name="create-azure-maps-creator-preview"></a>Azure Maps Maker maken (preview) 

Als u Creator-Services (preview) wilt gebruiken, moet u Azure Maps Maker maken in een Azure Maps-account. Zie [Azure Maps Maker beheren](how-to-manage-creator.md)voor meer informatie over het maken van Azure Maps maker in azure Maps.

## <a name="upload-a-drawing-package"></a>Een tekenpakket uploaden

Creator (preview) verzamelt gegevens over de binnenste kaart door een geüpload teken pakket te converteren. Het teken pakket vertegenwoordigt een geconstrueerde of opnieuw gemodelleerde faciliteit. Zie [pakket vereisten](drawing-requirements.md)voor meer informatie over het opstellen van pakket vereisten.

Gebruik de [Upload-API voor Azure Maps gegevens (preview)](/rest/api/maps/data/uploadpreview) om een teken pakket te uploaden.  Wanneer het uploaden is gelukt, retourneert de API voor het uploaden van gegevens een gebruikers gegevens-id ( `udid` ). De `udid` wordt in de volgende stap gebruikt om het geüploade pakket te converteren naar binnenste kaart gegevens.

## <a name="convert-a-drawing-package"></a>Een tekenpakket converteren

De [Azure Maps conversie service](/rest/api/maps/conversion) converteert een geüpload tekening pakket naar een binnenste kaart gegevens. De conversie service valideert ook het pakket. Validatie problemen worden onderverdeeld in twee typen: fouten en waarschuwingen. Als er fouten worden gedetecteerd, mislukt het conversie proces. Als waarschuwingen moeten worden gedetecteerd, wordt de conversie voltooid. Het is echter raadzaam om alle waarschuwingen te controleren en op te lossen. Een waarschuwing betekent dat een deel van de conversie is genegeerd of automatisch is opgelost. Als u de waarschuwingen niet oplost, kan dit leiden tot fouten in de laatste processen. Zie [waarschuwingen en fouten in het tekening pakket](drawing-conversion-error-codes.md)voor meer informatie.

Als er een fout optreedt, biedt de conversie service een koppeling naar de [Azure Maps tekening fout visualiseren](drawing-error-visualizer.md) zelfstandige webtoepassing. U kunt de fout Visualer voor het tekenen van tekeningen gebruiken om [waarschuwingen en fouten in het tekening pakket](drawing-conversion-error-codes.md) te controleren die zijn opgetreden tijdens het conversie proces. Zodra u de fouten hebt opgelost, kunt u vervolgens proberen het pakket te uploaden en te converteren.

## <a name="create-indoor-map-data"></a>Toewijzings gegevens in een binnenshuis maken

Azure Maps Creator (preview) biedt drie services:

* [Gegevenssetservice](/rest/api/maps/dataset/createpreview).
De DataSet-service gebruiken om een gegevensset te maken op basis van geconverteerde gegevens voor tekenpakketen.
* [Tegelsetservice](/rest/api/maps/tileset/createpreview).
Gebruik de Tegelset-service om een op vector gebaseerde weergave van een gegevensset te maken. Toepassingen kunnen een tegelset gebruiken om een weergave op basis van een visuele tegel van de gegevensset te presenteren.
* [Functie status service](/rest/api/maps/featurestate). Gebruik de functie status service voor het ondersteunen van de stijl van dynamische kaarten. Met de stijl dynamische kaarten kunnen toepassingen realtime-gebeurtenissen weer geven op ruimten die worden meegeleverd met IoT-systeem.

### <a name="datasets"></a>Gegevenssets

Een gegevensset is een verzameling functies van een binnenste kaart. De functie voor het toewijzen van materialen vertegenwoordigen faciliteiten die zijn gedefinieerd in een geconverteerde teken pakket. Nadat u een gegevensset met de [DataSet-service](/rest/api/maps/dataset/createpreview)hebt gemaakt, kunt u een wille keurig aantal [tilesets](#tilesets) of [functie-statesets](#feature-statesets)maken.

Met de [DataSet-service](/rest/api/maps/dataset/createpreview) kunnen ontwikkel aars op elk gewenst moment faciliteiten toevoegen aan of verwijderen uit een bestaande gegevensset. Zie voor meer informatie over het bijwerken van een bestaande gegevensset met behulp van de API de toevoeg opties in de [gegevensset-service](/rest/api/maps/dataset/createpreview). Zie [gegevens onderhoud](#data-maintenance)voor een voor beeld van het bijwerken van een gegevensset.

### <a name="tilesets"></a>Tilesets

Een tegelset is een verzameling van vector gegevens die een set uniforme raster tegels vertegenwoordigt. Ontwikkel aars kunnen de [tegelset-service](/rest/api/maps/tileset/createpreview) gebruiken om tilesets te maken op basis van een gegevensset.

Als u verschillende inhouds stadia wilt weer geven, kunt u meerdere tilesets maken op basis van dezelfde gegevensset. U kunt bijvoorbeeld één tegelset maken met meubels en apparatuur, en een andere tegelset zonder meubilair en apparatuur.  U kunt ervoor kiezen om één tegelset te genereren met de meest recente gegevens updates en één zonder de meest recente gegevens updates.

Naast de vector gegevens biedt de tegelset meta gegevens voor optimalisatie van kaart weergave. Bijvoorbeeld: de meta gegevens van de tegelset bevatten het zoom niveau min en Max voor de tegelset. De meta gegevens bieden ook een selectie kader waarmee de geografische grootte van de tegelset wordt gedefinieerd. In het begrenzingsvak kan een toepassing het juiste middel punt programmatisch instellen. Zie voor meer informatie over de meta gegevens van de tegelset de [List-API tile](/rest/api/maps/tileset/listpreview).

Zodra een tegelset is gemaakt, kan deze worden opgehaald door de [service rendering v2](#render-v2-service).

Als een tegelset verouderd is en niet langer handig is, kunt u de tegelset verwijderen. Zie  [gegevens onderhoud](#data-maintenance)voor meer informatie over het verwijderen van tilesets.

>[!NOTE]
>Een tegelset is onafhankelijk van de gegevensset waaruit deze is gemaakt. Als u tilesets maakt op basis van een gegevensset en vervolgens die gegevensset bijwerkt, wordt de tilesets niet bijgewerkt. Als u wijzigingen in een gegevensset wilt weer geven, moet u nieuwe tilesets maken. Op dezelfde manier geldt dat als u een tegelset verwijdert, dit geen invloed heeft op de gegevensset.

### <a name="feature-statesets"></a>Functie statesets

Functie-statesets zijn verzamelingen van dynamische eigenschappen (*statussen*) die zijn toegewezen aan functies van gegevensset, zoals kamers of apparatuur. Een voor beeld van een *status* kan een Tempe ratuur of bezetting zijn. Elke *status* is een sleutel/waarde-paar met de naam van de eigenschap, de waarde en de tijds tempel van de laatste update.

Met de [functie status service](/rest/api/maps/featurestate/createstatesetpreview) kunt u een functie statusset voor een gegevensset maken en beheren. De statusset wordt gedefinieerd door een of meer *statussen*. Aan elke functie, zoals een ruimte, kan één *status* zijn gekoppeld.

De waarde van elke *status* in een statusset kan worden bijgewerkt of opgehaald door IOT-apparaten of andere toepassingen.  Als u bijvoorbeeld de API voor de [functie status update](/rest/api/maps/featurestate/updatestatespreview)gebruikt, kunnen apparaten die ruimte vrijmaken de status wijziging van een kamer systematisch plaatsen.

Een toepassing kan een functie statusset gebruiken om de functies in een faciliteit dynamisch te renderen volgens hun huidige status en de bijbehorende kaart stijl. Zie voor meer informatie over het gebruik van functie statesets voor stijl functies in een rendering-kaart de module over de [Web-SDK](#indoor-maps-module)in de binnenshuis.

>[!NOTE]
>Net als bij tilesets heeft het wijzigen van een gegevensset geen invloed op de bestaande functie statusset en het verwijderen van een functie statusset heeft geen invloed op de gegevensset waaraan deze is gekoppeld.

## <a name="using-indoor-maps"></a>Kaarten van binnen gebruiken

### <a name="render-v2-service"></a>V2-service weer geven

De Azure Maps [render v2-service voor het ophalen van de tegel-API (preview)](/rest/api/maps/renderv2/getmaptilepreview) is uitgebreid ter ondersteuning van de maker-tilesets (preview-versie).

Rendering v2-service: met de tegel-API van de kaart ophalen kunnen toepassingen tilesets aanvragen. De tilesets kan vervolgens worden geïntegreerd in een kaart besturings element of SDK. Voor een voor beeld van een kaart besturings element dat gebruikmaakt van de rendering v2-service, raadpleegt u [module voor binnenste kaarten](#indoor-maps-module).

### <a name="web-feature-service-api"></a>API voor web-functie Service

Gegevens sets kunnen worden opgevraagd met behulp van de [WFS-API (Web Feature Service)](/rest/api/maps/wfs). WFS volgt de [Open GEOSPATIAL consortium API-functies](http://docs.opengeospatial.org/DRAFTS/17-069r1.html). Met de WFS-API kunt u in de gegevensset zelf een query uitvoeren op functies. U kunt WFS bijvoorbeeld gebruiken om alle middel grote Vergader zalen van een bepaalde faciliteit en een basis niveau te vinden.

### <a name="indoor-maps-module"></a>Plattegrondmodule

De [Azure Maps Web-SDK](./index.yml) bevat de module kaarten van de kaart. Deze module biedt uitgebreide functionaliteiten voor de Azure Maps *map control* bibliotheek. In de module voor de binnenste kaarten worden de kaarten weer gegeven die zijn gemaakt in de maker (preview-versie). Het integreert widgets als *vloer kiezer*, waarmee gebruikers de verschillende vloeren kunnen visualiseren.

Met de module binnenste kaarten kunt u webtoepassingen maken voor het integreren van kaart gegevens met andere [Azure Maps Services](./index.yml). De meest voorkomende toepassings instellingen zijn bijvoorbeeld het toevoegen van kennis aan kaarten van andere kaarten, zoals de weg, afbeelding, weer en door voer.

De module binnenste kaarten biedt ook ondersteuning voor dynamische kaart stijlen. Voor een stapsgewijze procedure voor het implementeren van dynamische opmaak van functie statusset in een toepassing raadpleegt u [de module over het binnenste overzicht gebruiken](how-to-use-indoor-module.md)

### <a name="azure-maps-integration"></a>Integratie van Azure Maps

Wanneer u begint met het ontwikkelen van oplossingen voor kaarten over een binnenste kaart, kunt u manieren ontdekken om bestaande Azure Maps mogelijkheden te integreren. Het bijhouden van activa of veiligheids scenario's kan bijvoorbeeld worden geïmplementeerd met behulp van de [Azure Maps geofence API](/rest/api/maps/spatial/postgeofence) met de plattegronden van de maker. De geofence-API kan worden gebruikt om bijvoorbeeld te bepalen of een werk nemer specifieke gebieden in de ruimten invoert of verlaat. Meer informatie over het verbinden van Azure Maps met IoT telemetrie is [hier](tutorial-iot-hub-maps.md)beschikbaar.

### <a name="data-maintenance"></a>Gegevens onderhoud

 Met Azure Maps Maker-lijst (preview-versie), update en delete API kunt u uw gegevens sets, tilesets en functie-statesets weer geven, bijwerken en verwijderen.

>[!NOTE]
>Wanneer u een lijst met items bekijkt en besluit deze te verwijderen, moet u rekening houden met de impact van die verwijdering op alle afhankelijke API'S of toepassingen. Als u bijvoorbeeld een-tegelset verwijdert die momenteel door een toepassing wordt gebruikt door middel van de [weer gave-API voor de tegel v2-kaart ophalen](/rest/api/maps/renderv2/getmaptilepreview), zou het verwijderen van die tegelset resulteren in een toepassings fout waardoor die tegelset wordt weer gegeven.

### <a name="example-updating-a-dataset"></a>Voor beeld: een gegevensset bijwerken

In het volgende voor beeld ziet u hoe u een gegevensset bijwerkt, een nieuwe tegelset maakt en een oude tegelset verwijdert.

1. Volg de stappen in het [uploaden van een tekening pakket](#upload-a-drawing-package) en het [converteren van een teken](#convert-a-drawing-package) pakket secties om het nieuwe teken pakket te uploaden en te converteren.

2. Gebruik de [DataSet Create API](/rest/api/maps/dataset/createpreview) om de geconverteerde gegevens toe te voegen aan de bestaande gegevensset.

3. Gebruik de [tegelset API maken](/rest/api/maps/tileset/createpreview) om een nieuwe tegelset uit de bijgewerkte gegevensset te genereren. Sla de nieuwe tilesetId voor stap 4 op.

4. Werk de tegelset-id in uw toepassing bij om de bijgewerkte campus-gegevensset in te scha kelen. Als de oude tegelset niet meer in gebruik is, kunt u deze verwijderen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelf studie: een koppeling maken (preview) in het binnenste](tutorial-creator-indoor-maps.md)