---
title: Inzicht verkrijgen in apparaten die worden gedetecteerd door een specifieke sensor
description: De apparaat-inventaris toont een uitgebreid scala aan kenmerken van apparaten die een sensor detecteert.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/06/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: f3058953e702f40fa1500441e382898b0314ddbb
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97839739"
---
# <a name="investigate-sensor-detections-in-a-device-inventory"></a>Sensor detecties onderzoeken in een inventaris van een apparaat

De apparaat-inventaris toont een uitgebreid scala aan kenmerken van apparaten die een sensor detecteert. Opties zijn beschikbaar voor:

 - U kunt de informatie eenvoudig filteren.

 - Gegevens exporteren naar een CSV-bestand.

 - Details van Windows-REGI ster importeren.

 - Groepen maken voor weer gave in de apparaattoewijzing.

## <a name="view-device-attributes-in-the-device-inventory"></a>Kenmerken van apparaten in de inventaris van apparaten weer geven

De volgende kenmerken worden weer gegeven in de inventarisatie tabel van het apparaat.

| Parameter | Beschrijving |
|--|--|
| Naam | De naam van het apparaat als de sensor dat deze heeft gedetecteerd. |
| Type | Het type apparaat. |
| Leverancier | De naam van de leverancier van het apparaat, zoals gedefinieerd in het MAC-adres. |
| Besturingssysteem | Het besturings systeem van het apparaat. |
| Firmware | De firmware van het apparaat. |
| IP-adres | Het IP-adres van het apparaat. |
| VLAN | Het VLAN van het apparaat. Zie [VLAN-namen definiëren](how-to-manage-the-on-premises-management-console.md#define-vlan-names)voor meer informatie over het instrueren van de sensor om vlan's te detecteren. (How-to-define-Management-Console-Network-Settings. MD # define-VLAN-names). |
| MAC-adres | Het MAC-adres van het apparaat. |
| Protocollen | De protocollen die door het apparaat worden gebruikt. |
| Niet-bevestigde waarschuwingen | Het aantal niet-bevestigde waarschuwingen dat is gekoppeld aan dit apparaat. |
| Is geautoriseerd | De autorisatie status die is gedefinieerd door de gebruiker:<br />- **Waar**: het apparaat is geautoriseerd.<br />- **Onwaar**: het apparaat is niet geautoriseerd. |
| Staat bekend als scanner | Als scan apparaat door de gebruiker gedefinieerd. |
| Is programmerings apparaat | Gedefinieerd als een geautoriseerd programmeer apparaat door de gebruiker. <br />- **Waar**: het apparaat voert programmeer activiteiten uit voor PLCs, RTUs en controllers, die relevant zijn voor de technische stations. <br />- **Onwaar**: het apparaat is geen programmeer apparaat. |
| Groepen | De groepen waarvan dit apparaat deel uitmaakt. |
| Laatste activiteit | De laatste activiteit die door het apparaat is uitgevoerd. |
| Discovered | Wanneer dit apparaat voor het eerst in het netwerk werd weer gegeven. |

De inventaris van apparaten weer geven:

1. Selecteer **apparaten** in het linkerdeel venster. Het deel venster **apparaten** wordt aan de rechter kant geopend.

2. Selecteer in het deel venster **apparaten** de optie :::image type="icon" source="media/how-to-work-with-asset-inventory-information/device-pane-icon.png" border="false"::: .

Als u kolommen wilt verbergen en weer geven, past u de tabel inventarisatie van apparaat aan:

1. Selecteer in het menu in de rechter bovenhoek van de inventarisatie van apparaten :::image type="icon" source="media/how-to-work-with-asset-inventory-information/settings-icon.png" border="false"::: .

    :::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-settings-screens-v2.png" alt-text="Scherm inventaris instellingen van apparaat.":::

2. Selecteer in het venster **instellingen voor apparaat-inventarisatie** de kolommen die u wilt weer geven in de inventarisatie tabel van het apparaat.

3. Wijzig de locatie van de kolommen in de tabel met behulp van pijlen.

4. Selecteer **Opslaan**. Het venster **instellingen voor apparaat-inventarisatie** wordt gesloten en de nieuwe instellingen worden weer gegeven in de tabel.

### <a name="create-temporary-device-inventory-filters"></a>Tijdelijke filters voor de inventaris van apparaten maken

U kunt een filter instellen waarmee wordt gedefinieerd welke gegevens in de tabel worden weer gegeven. U kunt bijvoorbeeld besluiten dat u alleen de gegevens van het PLC-apparaat wilt weer geven.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/devices-learning-v2.png" alt-text="Apparaten leren.":::

Het filter wordt niet opgeslagen wanneer u de inventaris verlaat.

### <a name="save-device-inventory-filters"></a>Inventaris filters voor apparaten opslaan

U kunt een filter of een combi natie van filters opslaan die u nodig hebt en deze opnieuw Toep assen in de inventaris van het apparaat. Grotere filters maken op basis van een bepaald apparaattype, of meer smalle filters op basis van een specifiek type en een specifiek protocol.

De filters die u opslaat, worden ook opgeslagen als apparaatgroepen. Deze functie biedt een extra granulatie niveau voor het weer geven van netwerk apparaten op de kaart.

Filters maken:

1. Selecteer in de kolom die u wilt filteren :::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-icon.png" border="false"::: .

2. Selecteer in het dialoog venster **filteren** het filter type:

   - **Is gelijk aan**: de exacte waarde op basis waarvan u de kolom wilt filteren. Als u bijvoorbeeld de kolom Protocol filtert op basis van **gelijk** aan en `value=ICMP` , wordt in de kolom alleen apparaten weer gegeven die het ICMP-protocol gebruiken.

   - **Bevat**: de waarde die is opgenomen onder andere waarden in de kolom. Als u bijvoorbeeld de kolom Protocol filtert op basis van en, **bevat** `value=ICMP` de kolom apparaten die het ICMP-protocol gebruiken als onderdeel van de lijst met protocollen die door het apparaat worden gebruikt.

3. Als u de kolom informatie wilt ordenen volgens alfabetische volg orde, selecteert u :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false"::: . Rang Schik de volg orde door de :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: pijlen en te selecteren :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false"::: .

4. Als u een nieuw filter wilt opslaan, definieert u het filter en selecteert u **Opslaan als**.

5. Als u de filter definities wilt wijzigen, wijzigt u de definities en selecteert u **wijzigingen opslaan**.

Filters weer geven:

- Open het linkerdeel venster en Bekijk de filters die u hebt opgeslagen:

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-from-left-pane-v2.png" alt-text="Bekijk de filters vanuit het deel venster aan de linkerkant.":::

### <a name="view-filtered-information-as-a-map-group"></a>Gefilterde gegevens weer geven als een toewijzings groep

Wanneer u overschakelt naar de kaart weergave, worden de gefilterde apparaten gemarkeerd en gefilterd. De filter groep die u hebt opgeslagen, wordt weer gegeven in het menu aan de zijkant onder de groep filters van de **apparaats inventaris** .

:::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-in-the-map-view-v2.png" alt-text="Filters weer geven in de kaart weergave.":::

## <a name="learn-windows-registry-details"></a>Meer informatie over Windows-REGI ster

Naast het leren van een apparaat, kunt u IT-apparaten detecteren, waaronder micro soft Windows-werk stations en-servers. Deze apparaten worden ook weer gegeven in de inventaris van apparaten. Nadat u apparaten hebt leren, kunt u de inventaris van het apparaat verrijken met gedetailleerde Windows-informatie, zoals:

- Windows-versie geïnstalleerd

- Geïnstalleerde toepassingen

- Informatie op patch niveau

- Poorten openen

- Meer robuuste informatie over versies van het besturings systeem

Er zijn twee opties beschikbaar om deze informatie op te halen:

- Actieve polling met behulp van geplande WMI-scans. 

- Lokale inspecties door een script op het apparaat te distribueren en uit te voeren. Als u werkt met lokale scripts, worden de Risico's van het uitvoeren van WMI-polling op een eind punt omzeild. Het is ook nuttig voor gereguleerde netwerken met waterval en eenrichtings elementen.

In dit artikel wordt beschreven hoe u het REGI ster van het Windows-eind punt lokaal kunt onderzoeken met een script. Deze informatie wordt gebruikt voor het genereren van waarschuwingen, meldingen, rapporten voor gegevens analyse, risico beoordelingen en aanvals vector rapporten.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/data-mining-screen.png" alt-text="Scherm opname van gegevens analyse.":::

U kunt de volgende Windows-besturings systemen onderzoeken:

- Windows XP

- Windows 2000

- Windows NT

- Windows 7

- Windows 10

- Windows Server 2003/2008/2012/2016

### <a name="before-you-begin"></a>Voordat u begint

Als u het script wilt gebruiken, moet u aan de volgende vereisten voldoen:

- Er zijn beheerders machtigingen vereist om het script uit te voeren op het apparaat.

- De sensor moet het Windows-apparaat al hebben geleerd. Dit betekent dat als het apparaat al bestaat, de informatie wordt opgehaald door het script.

- Een sensor bewaken het netwerk waarmee de Windows-PC is verbonden.

### <a name="acquire-the-script"></a>Het script ophalen

Als u het script wilt ontvangen, [neemt u contact op met de klant ondersteuning](mailto:support.microsoft.com).

### <a name="deploy-the-script"></a>Het script implementeren

U kunt het script eenmaal implementeren of doorlopende query's plannen met behulp van standaard geautomatiseerde implementatie methoden en-hulpprogram ma's.

### <a name="about-the-script"></a>Over het script

- Het script wordt uitgevoerd als een hulp programma en niet een geïnstalleerd programma. Het uitvoeren van het script heeft geen invloed op het eind punt.

- De bestanden die door het script worden gegenereerd, blijven op het lokale station totdat u ze verwijdert.

- De bestanden die door het script worden gegenereerd, bevinden zich naast elkaar. U kunt ze niet scheiden.

- Als u het script opnieuw op dezelfde locatie uitvoert, worden deze bestanden overschreven.

Het script uitvoeren:  

1. Kopieer het script naar een lokaal station en pak het uit. De volgende bestanden worden weer gegeven:

    - start.bat

    - settings.jsop

    - data. bin

    - run.bat

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/files-in-file-explorer.png" alt-text="Weer gave van de bestanden in Verkenner.":::

2. Voer het `run.bat` bestand uit.

3. Nadat het REGI ster is gecontroleerd, wordt het bestand met de CX-moment opname weer gegeven met de register gegevens.

4. De bestands naam geeft de systeem naam en de datum en tijd van de moment opname. Een voor beeld van een bestands naam is `CX-snaphot_SystemName_Month_Year_Time` .

### <a name="import-device-details"></a>Details van apparaat importeren

Informatie die op elk eind punt is geleerd, moet worden geïmporteerd in de sensor.

Bestanden die zijn gegenereerd op basis van de query's kunnen in één map worden geplaatst die toegankelijk is vanuit Sens oren. Gebruik standaard, geautomatiseerde methoden en hulpprogram ma's om de bestanden van elk Windows-eind punt te verplaatsen naar de locatie waar u ze naar de sensor wilt importeren.

Bestands namen niet bijwerken.

Importeren:

1. Selecteer **Instellingen importeren** in het dialoog venster **Windows-configuratie importeren** .

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/import-windows-configuration-v2.png" alt-text="Importeer uw Windows-configuraties.":::

2. Selecteer **toevoegen** en selecteer vervolgens alle bestanden (CTRL + A).

3. Selecteer **Sluiten**. De register gegevens van het apparaat worden geïmporteerd. Als er een probleem is opgetreden bij het uploaden van een van de bestanden, wordt u geïnformeerd over het uploaden van het bestand.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-new-file.png" alt-text="Het uploaden van de toegevoegde bestanden is voltooid.":::

## <a name="export-device-inventory-information"></a>Inventaris gegevens van het apparaat exporteren

U kunt inventaris gegevens van apparaten exporteren naar een Excel-bestand. De huidige gegevens worden overschreven door geïmporteerde gegevens.

Een CSV-bestand exporteren:

- Selecteer in het menu in de rechter bovenhoek van de inventarisatie van apparaten :::image type="icon" source="media/how-to-work-with-asset-inventory-information/csv-excel-export-icon.png" border="false"::: . Het CSV-rapport wordt gegenereerd en gedownload.

## <a name="see-also"></a>Zie tevens

[Onderzoek alle Enter prise sensor-detecties in een inventaris van een apparaat](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)

[Werken met site kaart weergaven](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views)
