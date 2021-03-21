---
title: Werken met een sensorapparaatkaart
description: De apparaattoewijzing biedt een grafische weer gave van de gedetecteerde netwerk apparaten. Gebruik de kaart voor het analyseren en beheren van apparaatgegevens, netwerk segmenten en het genereren van rapporten.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/7/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: edd1438a665e4917d5dd4cdcfba08d9cee01d3bb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100523835"
---
# <a name="investigate-sensor-detections-in-the-device-map"></a>Detectie van sensors in de apparaattoewijzing onderzoeken

De apparaattoewijzing biedt een grafische weer gave van de gedetecteerde netwerk apparaten. Gebruik de kaart voor het volgende:

  - Informatie over apparaten ophalen, analyseren en beheren.

  - Analyseer netwerk segmenten, bijvoorbeeld specifieke groepen van belang rijke of Purdue lagen.

  - Genereer rapporten, bijvoorbeeld details en samen vattingen van het apparaat exporteren.

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="Scherm opname van de apparaattoewijzing.":::

Voor toegang tot de kaart:

  - Selecteer **apparaattoewijzing** in het hoofd scherm van de console.

## <a name="map-search-and-layout-tools"></a>Hulp middelen voor kaarten zoeken en lay-out

De volgende hulpprogram ma's worden gebruikt om te werken in de kaart.

De gebruikersrol bepaalt welke hulpprogram ma's beschikbaar zijn in het venster met het kaart apparaat. Zie [gebruikers maken en beheren](how-to-create-and-manage-users.md) voor meer informatie over gebruikers rollen.

| Symbool | Beschrijving |
|---|---|
| :::image type="icon" source="media/how-to-work-with-maps/search-bar-icon-v2.png" border="false":::| Zoeken op IP-adres of MAC-adres voor een specifiek apparaat. Voer het IP-adres of MAC-adres in het tekstvak in. De kaart geeft het apparaat weer dat u hebt gezocht met apparaten die zijn verbonden. |
| Groeps markeringen en filters <br /> :::image type="content" source="media/how-to-work-with-maps/group-highlight-and-filters-v2.png" alt-text="Scherm opname van de groeps markeringen en filters."::: | Filter of markeer de kaart op basis van standaard groepen en aangepaste apparaatgroepen. |
| :::image type="icon" source="media/how-to-work-with-maps/collapse-view-icon.png" border="false"::: | Weer gave voor het samen vouwen van de IT, om een gerichte weer gave in te scha kelen op IP-apparaten en om deze apparaten te groeperen  |
| :::image type="icon" source="media/how-to-work-with-maps/device-management-icon.png" border="false"::: | De huidige inrichting van het apparaat in de kaart onderhouden. Als u bijvoorbeeld apparaten naar nieuwe locaties op de kaart sleept, blijven de apparaten op deze locaties wanneer de kaart wordt afgesloten. |
| :::image type="icon" source="media/how-to-work-with-maps/fit-to-screen-icon.png" border="false"::: | Aanpassen aan scherm |
| :::image type="icon" source="media/how-to-work-with-maps/layer-icon.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/layouts-icon-v2.png" border="false"::: | -De Purdue-laag weer geven die voor dit apparaat is geïdentificeerd, zoals automatisch, proces beheer, toezicht en onderneming <br /> -Verbindingen tussen apparaten weer geven.|
| :::image type="icon" source="media/how-to-work-with-maps/broadcast-icon.png" border="false"::: | Weer geven of verbergen tussen broadcast en multi cast. |
| :::image type="icon" source="media/how-to-work-with-maps/time-icon.png" border="false"::: | Filter de apparaten op de kaart op basis van de tijd die ze het laatst hebben gecommuniceerd met andere apparaten. |
| :::image type="icon" source="media/how-to-work-with-maps/notifications-icon.png" alt-text="meldingen" border="false"::: | Meldingen over een apparaat weer geven. Als er bijvoorbeeld een nieuw IP-adres is gedetecteerd voor een apparaat met behulp van een bestaande MAC-adressen |
| :::image type="icon" source="media/how-to-work-with-maps/export-import.png" alt-text="Exporteren" border="false"::: | Gegevens van het apparaat exporteren/importeren. |
| :::image type="icon" source="media/how-to-work-with-maps/properties-icon.png" alt-text="properties" border="false"::: | Basis apparaateigenschappen voor geselecteerde apparaten weer geven. |
| :::image type="icon" source="media/how-to-work-with-maps/zoom-in-icon-v2.png" alt-text="In" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/zoom-out-icon-v2.png" alt-text="-of uitzoomen" border="false"::: | In-of uitzoomen op apparaten in de kaart. |

## <a name="view-ot-elements-only"></a>Alleen elementen weer geven

Standaard worden de IT-apparaten automatisch geaggregeerd op basis van het subnet, zodat de kaart weergave gericht is op OT-en ICS-netwerken. De presentatie van de IT-netwerk elementen wordt samengevouwen tot een minimum, waardoor het totale aantal apparaten dat op de kaart wordt weer gegeven, kleiner wordt en een duidelijk beeld vormt van de netwerk elementen OT en ICS.

Elk subnet wordt weer gegeven als één entiteit op de apparaattoewijzing, inclusief een interactieve samen vouwen en uitbrei ding van de mogelijkheid om de details van een IT-subnet en terug te bekijken.

In de afbeelding hieronder ziet u een samengevouwen IT-subnet met 27 IT-netwerk elementen.

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="een IT-subnet samengevouwen met 27 IT-netwerk elementen":::

De functie voor het samen vouwen van IT-netwerken inschakelen:

- Zorg ervoor dat in het venster **systeem instellingen** de groepering van de functie voor het wisselen van IT-netwerken is ingeschakeld.

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="Venster systeem instellingen":::

Een IT-subnet uitbreiden:

1. Selecteer **subnetten** in het scherm systeem instellingen om onderscheid te maken tussen de IT-en OT-netwerken.

   > [!NOTE]
   > Het is raadzaam om elk subnet met zinvolle namen te benoemen, zodat de gebruiker eenvoudig kan worden geïdentificeerd om onderscheid te maken tussen het netwerk en het OT.

   :::image type="content" source="media/how-to-work-with-maps/subnet-list.png" alt-text="Configuratie van subnetten":::

2. Schakel in het venster **subnetten configuratie bewerken** het selectie vakje **ICS-subnet** uit voor elk subnet dat u wilt definiëren als een IT-subnet. De IT-subnetten worden samengevouwen in de apparaattoewijzing weer gegeven met de meldingen voor ICS-apparaten, zoals een controller of PLC, in IT-netwerken.

   :::image type="content" source="media/how-to-work-with-maps/edit-config.png" alt-text="Configuratie subnetten bewerken":::

3. Als u het IT-netwerk op de kaart wilt uitbreiden, klikt u met de rechter muisknop in het venster apparaten en selecteert u **netwerk uitbreiden**.

   :::image type="content" source="media/how-to-work-with-maps/expand-network.png" alt-text="Verg root uw weer gave van uw netwerk.":::

4. Er wordt een bevestigings venster weer gegeven waarin u wordt gewaarschuwd dat de wijziging van de indeling niet opnieuw kan worden uitgevoerd.

5. Selecteer **OK**. De IT-subnet elementen worden weer gegeven op de kaart.

   :::image type="content" source="media/how-to-work-with-maps/fixed-map.png" alt-text="OK":::

Een IT-subnet samen vouwen:

1.  Selecteer **apparaten** in het linkerdeel venster.

2. Selecteer in het venster apparaten het pictogram voor samen vouwen. Het getal in rood geeft aan hoeveel uitgebreide IT-subnetten momenteel op de kaart worden weer gegeven.

   :::image type="content" source="media/how-to-work-with-maps/devices-notifications.png" alt-text="Venster apparaat":::

3. Selecteer de subnetten die u wilt samen vouwen of selecteer **Alles samen vouwen**. Het geselecteerde subnet wordt samengevouwen weer gegeven op de kaart.

   :::image type="content" source="media/how-to-work-with-maps/close-all-subnets.png" alt-text="Alles samenvouwen":::

Het pictogram voor samen vouwen wordt bijgewerkt met het bijgewerkte aantal uitgebreide IT-subnetten.

## <a name="view-or-highlight-device-groups"></a>Apparaatgroepen weer geven of markeren

U kunt de kaart weergave aanpassen op basis van apparaatgroepen. Bijvoorbeeld groepen apparaten die zijn gekoppeld aan een specifiek OT, VLAN of subnet. Er zijn vooraf gedefinieerde groepen beschikbaar en aangepaste groepen kunnen worden gemaakt.

Groepen weer geven op:

  - **Markeren:** Markeer de apparaten die deel uitmaken van een specifieke groep in het blauw.

  - **Filteren:** Alleen apparaten weer geven die deel uitmaken van een specifieke groep.

:::image type="content" source="media/how-to-work-with-maps/port-standard.png" alt-text="Standaard weergave van uw poort":::

De volgende vooraf gedefinieerde groepen zijn beschikbaar:

| Groepsnaam | Beschrijving |
|--|--|
| **Bekende toepassingen** | Apparaten die gebruikmaken van gereserveerde poorten, zoals TCP.  |
| **niet-standaard poorten (standaard)** | Apparaten die gebruikmaken van niet-standaard poorten of poorten waaraan geen alias is toegewezen. |
| **OT protocollen (standaard)** | Apparaten die bekende verkeer verwerken. |
| **Autorisatie (standaard)** | Apparaten die tijdens het trainings proces zijn gedetecteerd in het netwerk of die officieel op het netwerk zijn toegelaten. |
| **Filters voor de inventarisatie van apparaten** | Apparaten gegroepeerd op basis van de filters worden opgeslagen in de inventarisatie tabel van het apparaat. |
| **Polling intervallen** | Apparaten gegroepeerd op polling-intervallen. De polling intervallen worden automatisch gegenereerd volgens cyclische kanalen of punten. Bijvoorbeeld 15,0 seconden, 3,0 seconden, 1,5 seconden of een wille keurig interval. Door deze informatie te bekijken, kunt u zien of systemen te snel of traag worden gecontroleerd. |
| **Programmering** | Technische stations en programmeer machines. |
| **Subnetten** | Apparaten die deel uitmaken van een specifiek subnet. |
| **VLAN** | Apparaten die zijn gekoppeld aan een specifieke VLAN-ID. |
| **Verbindingen tussen subnet** | Apparaten die communiceren van het ene subnet naar een ander subnet. |
| **Vastgemaakte waarschuwingen** | Apparaten waarvoor de gebruiker een waarschuwing heeft vastgemaakt. |
| **Aanvals vector simulaties** | Kwets bare apparaten gedetecteerd in aanvals vector rapporten. Als u deze apparaten op de kaart wilt weer geven, schakelt u het selectie vakje **weer geven op apparaat toewijzen** bij het genereren van de aanvals vector. :::image type="content" source="media/how-to-work-with-maps/add-attack-v2.png" alt-text="Aanvals vector-simulaties toevoegen":::. |
| **Voor het laatst gezien** | Apparaten gegroepeerd op het tijds bestek dat ze het laatst hebben gezien, bijvoorbeeld: één uur, zes uur, één dag, zeven dagen. |
| **Niet in Active Directory** | Alle niet-PLC-apparaten die niet communiceren met de Active Directory. |

Apparaten markeren of filteren:

1. Selecteer **apparaattoewijzing** in het menu aan de zijkant.

2. Selecteer het filter pictogram. :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="Menu":::

3. Selecteer in het deel venster groepen de groep die u wilt markeren of filteren op apparaten.

4. Selecteer **markeren** of **filteren**. Scha kelen tussen dezelfde selectie om de markering of het filter te verwijderen.

## <a name="define-custom-groups"></a>Aangepaste groepen definiëren

Naast het weer geven van vooraf gedefinieerde groepen kunt u aangepaste groepen definiëren. De groepen worden weer gegeven in de apparaattoewijzing, de inventarisatie van apparaten en rapporten van gegevens analyse.

> [!NOTE]
> U kunt ook groepen maken op basis van de inventaris van het apparaat.

Ga als volgt te werk om een groep te maken:

1. Selecteer **apparaten** in het menu aan de zijkant. De apparaattoewijzing wordt weer gegeven.

1. Selecteer :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="groeps instelling"::: om de instellingen van de groepen weer te geven.

1. Selecteer :::image type="content" source="media/how-to-work-with-maps/create-group-v2.png" alt-text="groepen"::: om een nieuwe aangepaste groep te maken.

:::image type="content" source="media/how-to-work-with-maps/custom-group-v2.png" alt-text="Een aangepast groeps scherm maken":::

1. Voeg de naam van de groep toe. gebruik Maxi maal 30 tekens.

1. Selecteer de relevante apparaten als volgt:

   - Voeg de apparaten uit dit menu toe door ze te selecteren in de lijst (Selecteer de pijl knop),<br /> Of <br /> 
   - Voeg de apparaten uit dit menu toe door ze te kopiëren van een geselecteerde groep (Selecteer de pijl knop)

1. Selecteer **groep toevoegen** om bestaande groepen toe te voegen aan aangepaste groepen.

### <a name="add-devices-to-a-custom-group"></a>Apparaten toevoegen aan een aangepaste groep

U kunt apparaten toevoegen aan een aangepaste groep of een nieuwe aangepaste groep en het apparaat maken.

1. Klik met de rechter muisknop op een of meer apparaten op de kaart.

1. Selecteer **toevoegen aan groep**.

1. Voer een groeps naam in het veld groep in en selecteer +. De nieuwe groep wordt weer gegeven. Als de groep al bestaat, wordt deze toegevoegd aan de bestaande aangepaste groep.

   :::image type="content" source="media/how-to-work-with-maps/groups-section-v2.png" alt-text="Groepsnaam":::

1. Voeg apparaten toe aan een groep door stap 1-3 te herhalen.

## <a name="map-zoom-views"></a>Weergave zoom weergaven

Bij het werken met kaart weergaven kunt u forensische versnellen bij het analyseren van grote netwerken.

Er kunnen drie detail weergaven voor apparaten worden weer gegeven:

  - [Vogel vlucht](#birds-eye-view)

  - [Type apparaat en verbinding weer geven](#device-type-and-connection-view)

  - [Detailweergave](#detailed-view)

### <a name="birds-eye-view"></a>Vogel vlucht

In deze weer gave ziet u een overzicht van de apparaten die als volgt worden weer gegeven:

  - Rode stippen geven apparaten aan met een waarschuwing (en)

  - Gemarkeerd punten geven aan dat apparaten zijn gemarkeerd als belang rijk

  - Zwarte stippen geven apparaten zonder waarschuwingen aan

:::image type="content" source="media/how-to-work-with-maps/colored-dots-v2.png" alt-text="Vogel weer gave":::

### <a name="device-type-and-connection-view"></a>Type apparaat en verbinding weer geven 

In deze weer gave worden apparaten weer gegeven als pictogrammen op de kaart, zodat apparaten met waarschuwingen, apparaattypen en verbonden apparaten kunnen worden gemarkeerd.

  - Apparaten met waarschuwingen worden weer gegeven met een rode ring

  - Apparaten zonder waarschuwingen worden weer gegeven met een grijze ring

  - Apparaten die als een ster worden weer gegeven, zijn gemarkeerd als belang rijk

Het pictogram apparaattype wordt weer gegeven met verbonden apparaten.

:::image type="content" source="media/how-to-work-with-maps/colored-rings.png" alt-text="verbindings weergave":::

### <a name="detailed-view"></a>Detailweergave 

In de gedetailleerde weer gave worden de labels en indica toren voor apparaten en apparaten en de volgende informatie gegeven:

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="Detailweergave":::

### <a name="control-the-zoom-view"></a>De zoom weergave bepalen

Welke kaart weergave wordt weer gegeven, is afhankelijk van het zoom niveau van de kaart. Scha kelen tussen de kaart weergaven wordt uitgevoerd door de zoom niveaus te wijzigen.

:::image type="content" source="media/how-to-work-with-maps/zoom-in-out.png" alt-text="De zoom weergave bepalen":::

### <a name="enable-simplified-zoom-views"></a>Vereenvoudigde zoom weergaven inschakelen

Beheerders die beveiligings analisten en RO-gebruikers de mogelijkheid wilt geven om toegang te krijgen tot vogel-en apparaat-en type verbindings weergaven, moeten de vereenvoudigde weergave optie inschakelen.

Vereenvoudigde kaart weergaven inschakelen:

  - Selecteer **systeem instellingen** en schakel vervolgens de optie **vereenvoudigde kaart weergave** in.

:::image type="content" source="media/how-to-work-with-maps/simplify-view-v2.png" alt-text="Kaart weergave vereenvoudigen":::

## <a name="learn-more-about-devices"></a>Meer informatie over apparaten

Er zijn tal van hulpprogram ma's beschikbaar voor meer informatie over apparaten formulier de apparaattoewijzing:

- [Labels en indica toren van apparaten](#device-labels-and-indicators)

- [Snelle weer gave van apparaten](#device-quick-views)

- [Apparaateigenschappen weer geven en beheren](#view-and-manage-device-properties)

- [Apparaattypen weer geven](#view-device-types)

- [Backplane](#backplane-properties)

- [Een tijd lijn van gebeurtenissen voor het apparaat weer geven](#view-a-timeline-of-events-for-the-device)

- [Programmeer Details en-wijzigingen analyseren](#analyze-programming-details-and-changes)

### <a name="device-labels-and-indicators"></a>Labels en indica toren van apparaten

De volgende labels en indica toren kunnen worden weer gegeven op apparaten op de kaart:

| Label apparaat | Beschrijving |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/host-v2.png" alt-text="IP-hostnaam"::: | Hostnaam en IP-adres van IP-adres, of subnet adressen |
| :::image type="content" source="media/how-to-work-with-maps/amount-alerts-v2.png" alt-text="Aantal waarschuwingen"::: | Aantal waarschuwingen dat is gekoppeld aan het apparaat |
| :::image type="icon" source="media/how-to-work-with-maps/type-v2.png" border="false"::: | Pictogram apparaattype, bijvoorbeeld opslag, PLC of historian. |
| :::image type="content" source="media/how-to-work-with-maps/grouped-v2.png" alt-text="gegroepeerde apparaten"::: | Aantal apparaten dat is gegroepeerd in een subnet in een IT-netwerk. In dit voor beeld 8. |
| :::image type="content" source="media/how-to-work-with-maps/not-authorized-v2.png" alt-text="Leer periode voor apparaten"::: | Een apparaat dat na de leer periode is gedetecteerd en niet is geautoriseerd als een netwerk apparaat. |
| Ononderbroken lijn | Logische verbinding tussen apparaten |
| :::image type="content" source="media/how-to-work-with-maps/new-v2.png" alt-text="Nieuw apparaat"::: | Het nieuwe apparaat dat is gedetecteerd na het leren is voltooid. |

### <a name="device-quick-views"></a>Snelle weer gave van apparaten

Eigenschappen en verbindingen van het apparaat openen via de kaart.

Ga als volgt te werk om het menu snelle eigenschappen te openen:

  - Selecteer het menu snelle eigenschappen van de snelle eigenschappen :::image type="content" source="media/how-to-work-with-maps/properties.png" alt-text="menu":::.

#### <a name="quick-device-properties"></a>Snelle apparaateigenschappen

Selecteer een of meer apparaten terwijl het scherm snelle eigenschappen is geopend om de hooglichten van deze apparaten weer te geven:

:::image type="content" source="media/how-to-work-with-maps/device-information.png" alt-text="Snelle apparaateigenschappen":::

#### <a name="quick-connection-properties"></a>Eigenschappen van snelle verbinding

Selecteer een verbinding terwijl het scherm snelle eigenschappen is geopend voor een overzicht van de protocollen die worden gebruikt in deze verbinding en wanneer deze het laatst zijn gezien:

:::image type="content" source="media/how-to-work-with-maps/connection-details-v2.png" alt-text="Eigenschappen van snelle verbinding":::

## <a name="view-and-manage-device-properties"></a>Apparaateigenschappen weer geven en beheren

U kunt de eigenschappen van het apparaat weer geven voor elk apparaat dat op de kaart wordt weer gegeven. Bijvoorbeeld de naam van het apparaat, het type of het besturings systeem of de firmware of de leverancier.

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="Apparaateigenschappen weer geven en beheren":::

De volgende informatie kan hand matig worden bijgewerkt. Gegevens die hand matig zijn ingevoerd, hebben voor rang op de informatie die door Defender voor IoT wordt gedetecteerd.

  - Naam

  - Type

  - Besturingssysteem

  - Purdue-laag

  - Beschrijving

| Item | Beschrijving |
|--|--|
| Algemene informatie | De basis informatie die nodig is. |
| Name | De apparaatnaam. <br /> De sensor detecteert standaard de apparaatnaam zoals deze in het netwerk is gedefinieerd. Bijvoorbeeld een naam die is gedefinieerd in de DNS-server. <br /> Als er geen namen zijn gedefinieerd, wordt het IP-adres van het apparaat weer gegeven in dit veld. <br /> U kunt de naam van een apparaat hand matig wijzigen. Geef uw apparaten duidelijke namen die overeenkomen met de functionaliteit ervan. |
| Type | Het apparaattype dat door de sensor is gedetecteerd. <br /> Zie [apparaattypen weer geven](#view-device-types)voor meer informatie. |
| Leverancier | De leverancier van het apparaat. Dit wordt bepaald door de voorloop tekens van het MAC-adres van het apparaat. Dit veld is alleen-lezen. |
| Besturingssysteem | Het besturings systeem van het apparaat is gedetecteerd door de sensor. |
| Purdue-laag | De Purdue-laag geïdentificeerd door de sensor voor dit apparaat, met inbegrip van: <br /> -Automatische <br /> -Proces beheer <br /> -Toezicht <br /> - Enterprise |
| Beschrijving | Een gratis tekst veld. <br /> Meer informatie over het apparaat toevoegen. |
| Kenmerken | Aanvullende informatie die tijdens de leer periode op het apparaat is gedetecteerd en niet tot andere categorieën behoort, wordt weer gegeven in de sectie kenmerken. <br /> De informatie is RO. |
| Instellingen | U kunt de apparaatinstellingen hand matig wijzigen om valse positieven te voor komen: <br /> - **Geautoriseerd apparaat**: tijdens de leer periode worden alle apparaten die in het netwerk worden gedetecteerd geïdentificeerd als geautoriseerde apparaten. Wanneer een apparaat na de leer periode wordt gedetecteerd, wordt het standaard weer gegeven als een niet-geautoriseerd apparaat. U kunt deze definitie hand matig wijzigen. <br /> - **Bekend als scanner**: Schakel deze optie in als u weet dat dit apparaat wordt aangeduid als scanner en er geen waarschuwing moet worden gesteld. <br /> - **Programmerings apparaat**: Schakel deze optie in als u weet dat dit apparaat een programma wordt genoemd en wordt gebruikt om programma wijzigingen door te voeren. Als u deze identificeert als een programmeer apparaat, voor komt u waarschuwingen voor het Program meren van wijzigingen die afkomstig zijn van deze asset. |
| Aangepaste groepen | De aangepaste groepen in de apparaattoewijzing waarvan dit apparaat deel uitmaakt. |
| Staat | De beveiliging en de autorisatie status van het apparaat: <br /> -De status is `Secured` wanneer er geen waarschuwingen zijn <br /> -Wanneer er waarschuwingen over het apparaat zijn, wordt het aantal waarschuwingen weer gegeven <br /> -De status `Unauthorized` wordt weer gegeven voor apparaten die na de leer periode zijn toegevoegd aan het netwerk. U kunt het apparaat hand matig definiëren, zoals `Authorized Device` in de instellingen <br /> -In het geval het adres van dit apparaat is gedefinieerd als een dynamisch adres, `DHCP` wordt het toegevoegd aan de status. |


| Netwerk | Beschrijving |
|--|--|
| Interfaces | De apparaatfuncties. Een RO-veld. |
| Protocollen | De protocollen die door het apparaat worden gebruikt. Een RO-veld. |
| Firmware | Als er backplane informatie beschikbaar is, worden er geen firmware gegevens weer gegeven. |
| Adres | Het IP-adres van het apparaat. |
| Wel | Het serie nummer van het apparaat. |
| Module adres | Het model en de sleuf nummer of-ID van het apparaat. |
| Model | Het model nummer van het apparaat. |
| Firmwareversie | Het versie nummer van de firmware. |

De apparaatgegevens weer geven:

1. Selecteer **apparaten** in het menu aan de zijkant.

2. Klik met de rechter muisknop op een apparaat en selecteer **Eigenschappen weer geven**. De venster Eigenschappen van het apparaat wordt weer gegeven.

3. Selecteer op de vereiste waarschuwing onder aan dit venster om gedetailleerde informatie over waarschuwingen voor dit apparaat weer te geven.

### <a name="view-device-types"></a>Apparaattypen weer geven

Het apparaattype wordt automatisch geïdentificeerd door de sensor tijdens het detectie proces voor apparaten. U kunt het type hand matig wijzigen.

:::image type="content" source="media/how-to-work-with-maps/type-of-device.png" alt-text="Apparaattypen weer geven":::

De volgende tabel bevat alle typen van het systeem:

| Categorie | Apparaattype |
|--|--|
| DELEN | Technisch station <br /> PLC <br />Historian <br />HMI <br />Kopieer <br />DCS-controller <br />RTU <br />Industrieel verpakkings systeem <br />Industrieel schalen <br />Industriële robot <br />Sleuf <br />Meter <br />Variabel frequentie station  <br />Robot-controller <br />Servo-station <br />Pneumatisch apparaat <br />Baldakijn |
| IT | Domeincontroller <br />DB-server <br />Werkstation <br />Server <br />Terminal station <br />Storage <br />Smartphone <br />Tablet <br />Back-upserver |
| IoT | IP-camera <br />Printer  <br />Perforatie Clock <br />Geldautomaat <br />Slimme TV <br />Game console <br />DVR <br />Configuratie scherm van deur <br />LOGISCHE <br />Thermostaat <br />Brand wekker <br />Slim licht <br />Slimme switch <br />Fire detector <br />IP-telefoon <br />Alarm systeem <br />Alarm Siren <br />Bewegingsherkenning <br />Korte <br />Vochtigheids sensor <br />Streepjescodescanner <br />Uninterruptible Power Supply <br />Bezoekers tellersysteem <br />Intercom <br />Streep |
| Netwerk | Draadloos Toegangs punt <br />Router <br />Switch <br />Firewall <br />VPN Gateway <br />NTP-server <br />WiFi-ananas <br />Fysieke locatie <br />I/O-adapter <br /> Protocol conversie |

De apparaatgegevens weer geven:

1.  Selecteer **apparaten** in het menu aan de zijkant.

2. Klik met de rechter muisknop op een apparaat en selecteer **Eigenschappen weer geven**. De venster Eigenschappen van het apparaat wordt weer gegeven.

3. Selecteer de vereiste waarschuwing om gedetailleerde informatie over waarschuwingen voor dit apparaat weer te geven.

### <a name="backplane-properties"></a>Eigenschappen van backplane

Als een PLC meerdere modules bevat, gescheiden in rekken en sleuven, kunnen de kenmerken verschillen tussen de module kaarten. Als het IP-adres en het MAC-adres bijvoorbeeld hetzelfde zijn, kan de firmware verschillen.

U kunt de optie backplane gebruiken om meerdere controllers/kaarten en hun geneste apparaten als één entiteit met verschillende definities te controleren. Elke sleuf in de weer gave backplane vertegenwoordigt de onderliggende apparaten: de apparaten die erachter zijn gedetecteerd.

:::image type="content" source="media/how-to-work-with-maps/backplane-image-v2.png" alt-text="Eigenschappen van backplane":::

:::image type="content" source="media/how-to-work-with-maps/backplane-details-v2.png" alt-text="Eigenschappen van backplane-apparaat":::

Een Backplane kan Maxi maal 30 controller kaarten en Maxi maal 30 rack eenheden bevatten. Het totale aantal apparaten dat is opgenomen in de verschillende niveaus kan Maxi maal 200 apparaten zijn.

Het deel venster backplane wordt weer gegeven in het venster Eigenschappen apparaat wanneer Details van backplane worden gedetecteerd.

Elke sleuf wordt weer gegeven met het aantal onderliggende apparaten en het pictogram waarin het module type wordt weer gegeven.

| Pictogram | Moduletype |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/power.png" alt-text="Voeding"::: | Voeding |
| :::image type="content" source="media/how-to-work-with-maps/analog.png" alt-text="Analoge I/O"::: | Analoge I/O |
| :::image type="content" source="media/how-to-work-with-maps/comms.png" alt-text="Communicatie adapter"::: | Communicatie adapter |
| :::image type="content" source="media/how-to-work-with-maps/digital.png" alt-text="Digitale I/O"::: | Digitale I/O |
| :::image type="content" source="media/how-to-work-with-maps/computer-processor.png" alt-text="CPU"::: | CPU |
| :::image type="content" source="media/how-to-work-with-maps/HMI-icon.png" alt-text="HMI"::: | HMI |
| :::image type="content" source="media/how-to-work-with-maps/average.png" alt-text="Algemeen"::: | Algemeen |

Wanneer u een sleuf selecteert, worden de details van de sleuf weer gegeven:

:::image type="content" source="media/how-to-work-with-maps/slot-selection-v2.png" alt-text="een sleuf selecteren":::

Als u de onderliggende apparaten achter de sleuf wilt weer geven, selecteert u **weer geven op kaart**. De sleuf wordt weer gegeven in de apparaattoewijzing met alle onderliggende modules en apparaten die ermee zijn verbonden.

:::image type="content" source="media/how-to-work-with-maps/map-appearance-v2.png" alt-text="WEER GEVEN OP KAART":::

## <a name="view-a-timeline-of-events-for-the-device"></a>Een tijd lijn van gebeurtenissen voor het apparaat weer geven

Een tijd lijn weer geven van gebeurtenissen die zijn gekoppeld aan een apparaat.

De tijd lijn weer geven:

1. Klik met de rechter muisknop op een apparaat in de kaart.

2. Selecteer **gebeurtenissen weer geven**. Het venster gebeurtenis tijdlijn wordt geopend met informatie over gebeurtenissen die zijn gedetecteerd voor het geselecteerde apparaat.

Zie de [tijd lijn](#event-timeline) van de gebeurtenis voor meer informatie.

## <a name="analyze-programming-details-and-changes"></a>Programmeer Details en-wijzigingen analyseren

Verbeter forensische door programma gebeurtenissen die worden uitgevoerd op uw netwerk apparaten weer te geven en code wijzigingen te analyseren. Deze informatie helpt u bij het detecteren van verdachte programmeer activiteiten, bijvoorbeeld:

  - Menselijke fout: een technicus programmeert het verkeerde apparaat.

  - Beschadigde programmeer automatisering: Program meren wordt ten onrechte uitgevoerd vanwege een automatiserings fout.

  - Gehacke systemen: niet-geautoriseerde gebruikers die zijn aangemeld bij een programmeer apparaat.

U kunt een geprogrammeerd apparaat weer geven en door verschillende programma wijzigingen bladeren die door andere apparaten worden uitgevoerd.

De code weer geven die is toegevoegd, gewijzigd, verwijderd of opnieuw geladen door het programmeer apparaat. Zoeken naar wijzigingen in het programma op basis van bestands typen, datums of tijdstippen van belang.

### <a name="when-to-review-programming-activity"></a>Wanneer moet u de programmeer activiteit controleren? 

Mogelijk moet u de programmeer activiteit controleren:

  - Na een waarschuwing over niet-geautoriseerde programmering

  - Na een geplande update naar controllers

  - Wanneer een proces of machine niet goed werkt (om te zien wie de laatste update heeft uitgevoerd en wanneer)

:::image type="content" source="media/how-to-work-with-maps/differences.png" alt-text="Wijzigings logboek Program meren":::

Met andere opties kunt u:

  - Markeer gebeurtenissen die van belang zijn met een ster.

  - Down load een *. txt-bestand met de huidige code.

### <a name="about-authorized-vs-unauthorized-programming-events"></a>Over geautoriseerde versus ongeoorloofde programmerings gebeurtenissen 

Niet-geautoriseerde programmeer gebeurtenissen worden uitgevoerd door apparaten die niet zijn geleerd of hand matig zijn gedefinieerd als programmeer apparaten. Geautoriseerde programmerings gebeurtenissen worden uitgevoerd door apparaten die zijn opgelost of hand matig zijn gedefinieerd als programmeer apparaten.

In het venster programmeer analyse worden zowel geautoriseerde als ongeoorloofde programmerings gebeurtenissen weer gegeven.

### <a name="accessing-programming-details-and-changes"></a>Toegang tot programmeer Details en wijzigingen

Open het venster programmeer analyse vanuit de:

- [Tijd lijn van gebeurtenis](#event-timeline)

- [Onbevoegde programmeer waarschuwingen](#unauthorized-programming-alerts)

### <a name="event-timeline"></a>Tijd lijn van gebeurtenis

Gebruik de tijd lijn van de gebeurtenis om een tijd lijn weer te geven met gebeurtenissen waarin programmeer wijzigingen zijn gedetecteerd.

:::image type="content" source="media/how-to-work-with-maps/timeline.png" alt-text="Een weer gave van de tijd lijn van de gebeurtenis.":::

### <a name="unauthorized-programming-alerts"></a>Onbevoegde programmeer waarschuwingen

Waarschuwingen worden geactiveerd wanneer onbevoegde programmeer apparaten programmeer activiteiten uitvoeren.

:::image type="content" source="media/how-to-work-with-maps/unauthorized.png" alt-text="Onbevoegde programmeer waarschuwingen":::

> [!NOTE]
> U kunt ook elementaire programmeer informatie weer geven in het apparaat venster Eigenschappen en de inventaris van apparaten.

### <a name="working-in-the-programming-timeline-window"></a>Werken in het venster tijd lijn Program meren

In deze sectie wordt beschreven hoe u programmeer bestanden weergeeft en versies vergelijkt. Zoeken naar specifieke bestanden die naar een geprogrammeerd apparaat zijn verzonden. Zoeken naar bestanden op basis van:

  - Datum

  - Bestands type

:::image type="content" source="media/how-to-work-with-maps/timeline-view.png" alt-text="venster tijd lijn Program meren":::

|Type programmeer tijdlijn | Beschrijving |
|--|--|
| Geprogrammeerd apparaat | Geeft details over het apparaat dat is geprogrammeerd, met inbegrip van de hostnaam en het bestand. |
| Recente gebeurtenissen | Hiermee worden de 50 meest recente gebeurtenissen weer gegeven die zijn gedetecteerd door de sensor. <br />Als u een gebeurtenis wilt markeren, plaatst u de muis aanwijzer erop en klikt u op de ster. :::image type="icon" source="media/how-to-work-with-maps/star.png" border="false"::: <br /> De laatste 50 gebeurtenissen kunnen worden weer gegeven. |
| Bestanden | Hier worden de bestanden weer gegeven die zijn gedetecteerd voor de gekozen datum en de bestands grootte op het geprogrammeerde apparaat. <br /> Standaard is het maximum aantal beschik bare bestanden voor weer gave per apparaat 300. <br /> De maximale bestands grootte voor elk bestand is standaard 15 MB. |
| Bestands status :::image type="icon" source="media/how-to-work-with-maps/status-v2.png" border="false"::: | Bestands namen geven de status van het bestand op het apparaat aan, zoals: <br /> **Toegevoegd**: het bestand is toegevoegd aan het eind punt op de geselecteerde datum of tijd. <br /> **Bijgewerkt**: het bestand is bijgewerkt op de geselecteerde datum of tijd. <br /> **Verwijderd**: dit bestand is verwijderd. <br /> **Geen label**: het bestand is niet gewijzigd.   |
| Programmeer apparaat | Het apparaat dat de programmeer wijziging heeft aangebracht. Meerdere apparaten hebben mogelijk programmeer wijzigingen uitgevoerd op een apparaat dat is geprogrammeerd. De hostnaam, de datum of het tijdstip van de wijziging en de aangemelde gebruiker worden weer gegeven. |
| :::image type="icon" source="media/how-to-work-with-maps/current.png" border="false"::: | Hier wordt het huidige bestand weer gegeven dat is geïnstalleerd op het apparaat dat is geprogrammeerd. |
| :::image type="icon" source="media/how-to-work-with-maps/download-text.png" border="false"::: | Down load een tekst bestand van de code die wordt weer gegeven. |
| :::image type="icon" source="media/how-to-work-with-maps/compare.png" border="false"::: | Het huidige bestand vergelijken met het bestand dat op een geselecteerde datum is gedetecteerd. |

### <a name="choose-a-file-to-review"></a>Kies een bestand om te bekijken

In deze sectie wordt beschreven hoe u een bestand kiest om te controleren.

Kies een bestand om te controleren:

1. Een gebeurtenis selecteren in het deel venster **recente gebeurtenissen**

2. Selecteer een bestand in het deel venster bestand. Het bestand wordt weer gegeven in het huidige deel venster.

:::image type="content" source="media/how-to-work-with-maps/choose-file.png" alt-text="Selecteer het bestand waarmee u wilt werken.":::

### <a name="compare-files"></a>Bestanden vergelijken

In deze sectie wordt beschreven hoe u programmeer bestanden kunt vergelijken.

Vergelijken:

1. Selecteer een gebeurtenis in het deel venster recente gebeurtenissen.

2. Selecteer een bestand in het deel venster bestand. Het bestand wordt weer gegeven in het huidige deel venster. U kunt dit bestand vergelijken met andere bestanden.

3. Selecteer de indicator Compare.

   :::image type="content" source="media/how-to-work-with-maps/compare.png" alt-text="Indicator vergelijken":::

   In het venster worden alle datums weer gegeven waarop het geselecteerde bestand is gevonden op het apparaat dat is geprogrammeerd. Het bestand is mogelijk op meerdere programmeer apparaten bijgewerkt op het apparaat dat is geprogrammeerd.

   Het aantal gedetecteerde verschillen wordt weer gegeven in de rechter bovenhoek van het venster. Mogelijk moet u omlaag schuiven om de verschillen weer te geven.

   :::image type="content" source="media/how-to-work-with-maps/scroll.png" alt-text="omlaag schuiven naar uw selectie":::

   Het getal wordt berekend op basis van aangrenzende regels met gewijzigde tekst. Als er bijvoorbeeld acht opeenvolgende regels code zijn gewijzigd (verwijderd, bijgewerkt of toegevoegd), wordt deze berekend als één verschil.

   :::image type="content" source="media/how-to-work-with-maps/program-timeline.png" alt-text="De tijdlijn weergave van uw programma.":::

4. Selecteer een datum. Het op de geselecteerde datum gedetecteerde bestand wordt weer gegeven in het venster.

5. Het bestand dat u in het deel venster recente gebeurtenissen/bestanden hebt geselecteerd, wordt altijd aan de rechter kant weer gegeven.

### <a name="device-programming-information-other-locations"></a>Informatie over de programmering van apparaten: andere locaties

Naast het controleren van de details in de programmeer tijdlijn, hebt u toegang tot programmeer informatie op het apparaat venster Eigenschappen en de inventaris van het apparaat.

| Apparaattype | Beschrijving |
|--|--|
| Apparaateigenschappen | Het venster Apparaateigenschappen bevat informatie over de laatste programmeer gebeurtenis die is gedetecteerd op de device\. :::image type="content" source="media/how-to-work-with-maps/information-from-device-v2.png" alt-text="De eigenschappen van uw apparaat"::: |
| De inventaris van het apparaat | De inventaris van het apparaat geeft aan of het apparaat een programmeer device\. is :::image type="content" source="media/how-to-work-with-maps/inventory-v2.png" alt-text="De inventaris van apparaten"::: |

## <a name="manage-device-information-from-the-map"></a>Apparaatgegevens van de kaart beheren

De sensor werkt de apparaten niet rechtstreeks op het netwerk bij of beïnvloedt deze niet. Wijzigingen die hier worden aangebracht, zijn alleen van invloed op de analyse van het apparaat.

### <a name="delete-devices"></a>Apparaten verwijderen

Mogelijk wilt u een apparaat verwijderen als de informatie die u hebt geleerd niet relevant is. Bijvoorbeeld:

  - Een contractant van een partner bij een technisch werk station maakt tijdelijk verbinding om configuratie-updates uit te voeren. Wanneer de taak is voltooid, wordt het apparaat verwijderd.

  - Als gevolg van wijzigingen in het netwerk zijn sommige apparaten niet meer verbonden.

Als u het apparaat niet verwijdert, wordt het door de sensor gewoon gecontroleerd. Na 60 dagen wordt er een melding weer gegeven waarin u wordt geadviseerd om deze te verwijderen.

Er wordt mogelijk een waarschuwing weer gegeven waarin wordt aangegeven dat het apparaat niet reageert als een ander apparaat er toegang toe probeert te krijgen. In dit geval is het mogelijk dat uw netwerk onjuist is geconfigureerd.

Het apparaat wordt verwijderd uit de apparaattoewijzing, de inventarisatie van apparaten en rapporten van gegevens analyse. Andere informatie, bijvoorbeeld: informatie die is opgeslagen in widgets, wordt behouden.

Het apparaat moet ten minste tien minuten inactief zijn om het te kunnen verwijderen.

Een apparaat verwijderen van de apparaattoewijzing:

1. Selecteer **apparaten** in het menu aan de zijkant.

2. Klik met de rechter muisknop op een apparaat en selecteer **verwijderen**.

### <a name="merge-devices"></a>Apparaten samen voegen

Onder bepaalde omstandigheden moet u mogelijk apparaten samen voegen. Dit kan vereist zijn als de sensor afzonderlijke netwerk entiteiten detecteert die zijn gekoppeld aan één uniek apparaat. Bijvoorbeeld:

  - Een PLC met vier netwerk kaarten.

  - Een laptop met WIFI en een fysieke kaart.
  
  - Een werk station met twee of meer netwerk kaarten.

Bij het samen voegen vraagt u de sensor de apparaateigenschappen van twee apparaten te combi neren in één. Wanneer u dit doet, worden de venster Eigenschappen-en sensor rapporten van het apparaat bijgewerkt met de nieuwe eigenschappen van het apparaat.

Als u bijvoorbeeld twee apparaten samenvoegt, elk met een IP-adres, worden beide IP-adressen weer gegeven als afzonderlijke interfaces in het apparaat venster Eigenschappen. U kunt alleen geautoriseerde apparaten samen voegen.

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="Apparaat venster Eigenschappen":::

De gebeurtenis tijdlijn geeft de samenvoeg gebeurtenis weer.

:::image type="content" source="media/how-to-work-with-maps/events-time.png" alt-text="De tijd lijn van de gebeurtenis met samengevoegde gebeurtenissen.":::

U kunt het samen voegen van apparaten niet ongedaan maken. Als u per ongeluk twee apparaten hebt samengevoegd, verwijdert u het apparaat en wacht u totdat de sensor opnieuw wordt gedetecteerd.

Apparaten samen voegen:

1. Selecteer twee apparaten (Houd SHIFT ingedrukt) en klik vervolgens met de rechter muisknop op een ervan.

2. Selecteer **samen voegen** om de apparaten samen te voegen. Het kan tot twee minuten duren voordat de samen voeging is voltooid.

3. Kies in het dialoog venster kenmerken van het samen voegen van apparaten instellen de naam van een apparaat.

   :::image type="content" source="media/how-to-work-with-maps/name-the-device-v2.png" alt-text="het dialoog venster kenmerken":::

4. Selecteer **Opslaan**.

### <a name="authorize-and-unauthorize-devices"></a>Apparaten machtigen en intrekken

Tijdens de leer periode worden alle apparaten die in het netwerk worden gedetecteerd geïdentificeerd als geautoriseerde apparaten. Het **geautoriseerde** label wordt niet weer gegeven op deze apparaten in de apparaattoewijzing.

Wanneer een apparaat na de leer periode wordt gedetecteerd, wordt het weer gegeven als een niet-gemachtigd apparaat. Naast het bekijken van niet-geautoriseerde apparaten in de kaart, kunt u ze ook weer geven in de inventaris van het apparaat.

:::image type="content" source="media/how-to-work-with-maps/inventory-icon.png" alt-text="Inventaris van apparaten":::

**Nieuw apparaat versus niet-geautoriseerd**

Nieuwe apparaten die na de leer periode zijn gedetecteerd, worden weer gegeven met een `New` en- `Unauthorized` label.

Als u een apparaat verplaatst op de kaart of de apparaateigenschappen hand matig wijzigt, `New` wordt het label verwijderd uit het pictogram apparaat.

#### <a name="unauthorized-devices---attack-vectors-and-risk-assessment-reports"></a>Niet-geautoriseerde apparaten-rapporten over aanvals vectoren en risico analyse

Niet-geautoriseerde apparaten zijn opgenomen in rapporten over risico analyse en aanvals vectoren.

- **Aanvals vector rapporten:** Apparaten die als niet-geautoriseerd zijn gemarkeerd, worden opgelost in de aanvals vector als verdachte Rogue-apparaten die een bedreiging voor het netwerk kunnen zijn.

   :::image type="content" source="media/how-to-work-with-maps/attack-vector-reports.png" alt-text="Weergave uw aanvals vector rapporten":::

- **Rapporten over risico analyse:** Apparaten die als niet-geautoriseerd zijn gemarkeerd, zijn:

  - Geïdentificeerd in rapporten voor risico analyse

Apparaten hand matig autoriseren of autoriseren:

1. Klik met de rechter muisknop op het apparaat op de kaart en selecteer **toestemming** geven

### <a name="mark-devices-as-important"></a>Apparaten markeren als belang rijk

U kunt belang rijke netwerk apparaten markeren als belang rijk, bijvoorbeeld bedrijfs kritieke servers. Deze apparaten zijn gemarkeerd met een ster op de kaart. De ster is afhankelijk van het zoom niveau van de kaart.

:::image type="icon" source="media/how-to-work-with-maps/star-one.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-two.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-3.png" border="false":::

### <a name="important-devices---attack-vectors-and-risk-assessment-reports"></a>Belang rijke apparaten-rapporten over aanvals vectoren en risico analyse

Belang rijke apparaten worden berekend bij het genereren van rapporten over risico analyse en aanvals vectoren.

  - Aanvals vector rapporten apparaten die zijn gemarkeerd als belang rijk, worden omgezet in de aanvals vector als aanvals doelen. 

  - Rapporten over risico analyse: apparaten die zijn gemarkeerd als belang rijk, worden berekend wanneer u de beveiligings Score opgeeft in het rapport risico beoordeling.

## <a name="generate-activity-reports-from-the-map"></a>Activiteiten rapporten genereren op basis van de kaart

Genereer een activiteiten rapport voor een geselecteerd apparaat over de 1, 6, 12 of 24 uur. De volgende informatie is beschikbaar:

  - Categorie: basis detectie-informatie op basis van verkeers scenario's.

  - Bron-en doel apparaten

  - Gegevens: beschadigde aanvullende gegevens.

  - De datum en tijd waarop het voor het laatst is gezien.

U kunt het rapport opslaan als een micro soft Excel-of Word-bestand.

:::image type="content" source="media/how-to-work-with-maps/historical-information.png" alt-text="Recente activiteit":::

Een activiteiten rapport voor een apparaat genereren:

1. Klik met de rechter muisknop op een apparaat in de kaart.

2. Selecteer een activiteiten rapport.

   :::image type="content" source="media/how-to-work-with-maps/activity-report.png" alt-text="Bekijk een rapport van uw activiteit.":::

## <a name="generate-attack-vector-reports-from-the-map"></a>Aanvals vector rapporten genereren op basis van de kaart

Simuleer een aanvals vector rapport om te leren of een apparaat op de kaart die u selecteert een kwetsbaar aanvals doel is.

Aanvals vector rapporten bieden een grafische weer gave van de beveiligings keten van apparaten die kunnen worden misbruikt. Deze beveiligings problemen kunnen een aanvaller toegang geven tot belang rijke netwerk apparaten. De aanvals vector Simulator berekent aanvals vectoren in realtime en analyseert alle aanvals vectoren per specifiek doel.

Een apparaat weer geven in een aanvals vector-rapporten:

1. Klik met de rechter muisknop op een apparaat in de kaart.

2. Selecteer **aanvals vectoren simuleren**. Het dialoog venster aanvals vector wordt geopend met het apparaat dat u als het aanvals doel selecteert.

   :::image type="content" source="media/how-to-work-with-maps/simulation.png" alt-text="Simulatie van aanvals vector toevoegen":::

3. Voeg de resterende para meters toe aan het dialoog venster en selecteer **simulatie toevoegen**.

## <a name="export-device-information-from-the-map"></a>Informatie over het apparaat van de kaart exporteren

De volgende apparaatgegevens exporteren van de kaart.

  - Details van apparaat (micro soft Excel)

  - Een samen vatting van apparaten (micro soft Excel)

  - Een Word-bestand met groepen (micro soft Word)

Exporteren:

1. Selecteer het pictogram exporteren vanuit de kaart.

1. Selecteer een export optie.

## <a name="see-also"></a>Zie ook

[Sensor detecties onderzoeken in een inventaris van een apparaat](how-to-investigate-sensor-detections-in-a-device-inventory.md)
