---
title: Rapporten met trends en statistieken genereren
description: Krijg inzicht in netwerk activiteiten, statistieken en trends met behulp van Defender voor IoT trends en statistieken widgets.
ms.date: 2/21/2021
ms.topic: how-to
ms.openlocfilehash: b4539e20ff485f1b6be69fee8e6849298540adcb
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778998"
---
# <a name="sensor-trends-and-statistics-reports"></a>Sensor trends en statistieken rapporten

U kunt widget grafieken en cirkel diagrammen maken om inzicht te krijgen in netwerk trends en-statistieken. Widgets kunnen worden gegroepeerd onder door de gebruiker gedefinieerde Dash boards.

> [!NOTE]
> Beheerders-en beveiligings analisten kunnen trends en statistieken rapporten maken.

Het dash board bestaat uit widgets die de volgende typen informatie grafisch beschrijven:

- Verkeer per poort
- Hoofd verkeer per poort
- Kanaal bandbreedte
- Totale band breedte
- Actieve TCP-verbinding
- Bovenste band breedte per VLAN
- Apparaten:
  - Nieuwe apparaten
  - Apparaten bezet
  - Apparaten op leverancier
  - Apparaten op besturings systeem
  - Aantal apparaten per VLAN
  - Niet-verbonden apparaten
- Connectiviteit valt per uur
- Waarschuwingen voor incidenten per type
- Database tabel toegang
- Widgets van protocol sectie
- DELTAV
  - DeltaV Roc-verwerkings distributie
  - DeltaV Roc gebeurtenissen op naam
  - DeltaV gebeurtenissen op tijd
- AMS
  - AMS verkeer per server poort
  - AMS-verkeer via opdracht
- Ethernet-en IP-adres:
  - Ethernet-en IP-adres verkeer door overschrijvings service
  - Ethernet-en IP-adres verkeer door overschrijvings klasse
  - Ethernet-en IP-adres verkeer via opdracht
- OPC
  - OPC best beheer bewerkingen
  - OPC, bovenste I/O-bewerkingen
- Siemens S7:
  - S7 verkeer per besturings functie
  - S7 verkeer per subfunctie
- VLAN
  - Aantal apparaten per VLAN
  - Bovenste band breedte per VLAN
- 60870-5-104
  - IEC-60870-verkeer door ASDU
- BACNET
  - BACnet Services
- DNP3
  - DNP3-verkeer per functie
- SRTP:
  - SRTP verkeer per service code
  - SRTP-fouten per dag
- SuiteLink:
  - SuiteLink populairste labels
  - Gedrag van SuiteLink numerieke code
- IEC-60870-verkeer door ASDU
- DNP3-verkeer per functie
- MMS-verkeer per service
- Modbus-verkeer per functie
- OPC-UA-verkeer per service

> [!NOTE]
>  De tijd in de widgets wordt ingesteld op basis van de sensor tijd.

## <a name="create-reports"></a>Rapporten maken

Dash boards en widgets bekijken:

Selecteer **Trends & statistieken** in het menu aan de zijkant.

:::image type="content" source="media/how-to-generate-reports/investigation-screenshot.png" alt-text="Scherm opname van een onderzoek.":::

Standaard worden resultaten weer gegeven voor detecties in de afgelopen zeven dagen. U kunt filter hulpprogramma's gebruiken om dit bereik te wijzigen. Bijvoorbeeld een zoek opdracht met een vrije tekst.

## <a name="create-a-dashboard"></a>Een dash board maken

Maak een nieuw dash board door de vervolg keuzelijst **dash board** te selecteren. U kunt zoveel widgets maken en toevoegen aan een dash board.

U kunt aangepaste Dash boards maken met behulp van de volgende opties:

- Een widget toevoegen aan het dash board

- Een widget verwijderen uit het dash board

- Het filter van een widget wijzigen

- Het formaat van een widget wijzigen

- De locatie van een widget wijzigen

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/pin-a-dashboard.png" alt-text="De locatie van een widget wijzigen.":::

Een aangepast dash board maken:

1. Selecteer **trends en statistieken** in het linkerdeel venster.

1. Selecteer de vervolg keuzelijst **dash board selecteren** en selecteer de knop **dash board maken** .

1. Voer een beschrijvende naam in voor het nieuwe dash board en selecteer **maken**.

1. Selecteer **widget toevoegen** in de linkerbovenhoek van de pagina.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/widget-store.png" alt-text="Selecteer de widget in het widget-archief.":::

1. In de rechter bovenhoek van het venster zijn de **beveiligings**-en **operationele** objecten beschikbaar. U kunt kiezen uit verschillende categorieën en protocollen. Er wordt een korte beschrijving met een miniatuur afbeelding weer gegeven met elk object. Gebruik de schuif balk om alle beschik bare widgets weer te geven.

1. Selecteer een widget met behulp van de knop **klikken om toe te voegen** . De widget wordt direct op het dash board weer gegeven.

Een dash board verwijderen:

1. Selecteer de naam van het dash board in de vervolg keuzelijst.

1. Selecteer het pictogram **verwijderen** en selecteer vervolgens **OK**.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/garbage-icon.png" alt-text="Selecteer het pictogram verwijderen om het dash board te verwijderen.":::

Een dashboard naam bewerken:

1. Selecteer de naam van het dash board in de vervolg keuzelijst.

1. Selecteer het **bewerkings** pictogram.
  
  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/edit-name.png" alt-text="Selecteer het bewerkings pictogram om de naam van het dash board te bewerken.":::

1. Voer een nieuwe naam in voor het dash board en selecteer **Opslaan**.
 
Het standaard dashboard instellen:

1. Selecteer de naam van het dash board in de vervolg keuzelijst.

1. Selecteer het **ster** pictogram om het dash board te selecteren dat als standaard dashboard moet worden ingesteld.

   :::image type="content" source="media/how-to-create-trends-and-statistics-reports/default-dashboard.png" alt-text="Selecteer het ster pictogram om uw standaard dashboard te kiezen."::: 

Filter gegevens in een widget wijzigen:

1. Selecteer het **filter** pictogram.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/filter-widget.png" alt-text="Selecteer het filter pictogram om para meters voor uw widget in te stellen.":::

1. Bewerk de vereiste para meters.

1. Selecteer **OK**.

Een widget verwijderen:

- Selecteer het :::image type="icon" source="media/how-to-create-trends-and-statistics-reports/x-icon.png" border="false"::: pictogram.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/delete-widget.png" alt-text="Selecteer de X om de widget te verwijderen.":::

## <a name="creating-widgets"></a>Widgets maken 

In het widget archief kunt u widgets selecteren op categorie en protocol. U kunt de **beveiliging** of beschik  bare widgets weer geven door deze te selecteren.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/widget-store.png" alt-text="Selecteer uw widget in het widget-archief.":::

Elke widget bevat specifieke informatie over systeem verkeer, netwerk statistieken, protocol statistieken, apparaat en waarschuwings gegevens. Er wordt een bericht weer gegeven wanneer er geen gegevens voor een widget zijn.

U kunt een sectie verwijderen uit de cirkel, in een cirkel diagram, om de relatieve significantie van de resterende segmenten duidelijker weer te geven. Selecteer de naam van het segment in de legenda aan de onderkant van het scherm om dit te doen.

In de volgende secties ziet u voor beelden van gebruiks voorbeelden voor een aantal widgets.

### <a name="busy-devices-widget"></a>Widget bezette apparaten

Deze widget geeft een lijst van de bovenste vijf drukste-apparaten. In de **bewerkings** modus kunt u filteren op bekende protocollen.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/busy-device.png" alt-text="Een weer gave van de widget van het actieve apparaat.":::

### <a name="total-bandwidth-widget"></a>Widget totale band breedte

Deze widget houdt de band breedte in Mbps (megabits per seconde) bij. De band breedte wordt op de y-as aangegeven, met de datum die op de x-as wordt weer gegeven. Met de **bewerkings** modus kunt u de resultaten filteren op basis van de client, de server, de server poort of het subnet. Er wordt een knop Info weer gegeven wanneer u de muis aanwijzer boven de grafiek houdt.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/total-bandwidth.png" alt-text="Een weer gave van de widget totale band breedte.":::

### <a name="channels-bandwidth-widget"></a>Widget voor kanaal bandbreedte

Met deze widget worden de vijf meest voorkomende verkeers kanalen weer gegeven. U kunt filteren op adres en het aantal weer gegeven resultaten instellen. Selecteer de pijl-omlaag om meer kanalen weer te geven.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/channels-bandwidth.png" alt-text="Een weer gave van de widget kanaal bandbreedte.":::

### <a name="traffic-by-port-widget"></a>Widget verkeer per poort

Met deze widget wordt het verkeer per poort weer gegeven. dit wordt aangeduid met een cirkel diagram waarbij elke poort wordt aangeduid met een andere kleur. De hoeveelheid verkeer in elke poort is evenredig met de grootte van het gedeelte van de cirkel.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/traffic-by-port.png" alt-text="Een weer gave van de widget verkeer per poort.":::

### <a name="new-devices-widget"></a>Widget nieuwe apparaten

Met deze widget wordt het staaf diagram voor nieuwe apparaten weer gegeven. Dit geeft aan hoeveel nieuwe apparaten op een bepaalde datum zijn gedetecteerd.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/new-devices.png" alt-text="Een weer gave van de widget nieuwe apparaten.":::

### <a name="protocol-dissection-widgets"></a>Widgets van protocol sectie

Met deze widget wordt een cirkel diagram weer gegeven met een overzicht van het verkeer per protocol, dat is ontstaat op basis van functie codes en-services. De grootte van elk segment van de cirkel is evenredig met de hoeveelheid verkeer ten opzichte van de andere segmenten.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/protocol-dissection.png" alt-text="Een weer gave van de object protocol-Dissection.":::

### <a name="active-tcp-connections-widget"></a>Widget actieve TCP-verbindingen

Met deze widget wordt een grafiek weer gegeven met het aantal actieve TCP-verbindingen in het systeem.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/active-tcp.png" alt-text="Een weer gave van de actieve widget TCP-verbindingen.":::

### <a name="incident-by-type-widget"></a>Widget incident per type

Deze widget geeft een cirkel diagram weer waarin het aantal incidenten per type wordt weer gegeven. Dit is het aantal waarschuwingen dat elke engine gedurende een vooraf gedefinieerde tijds periode heeft gegenereerd.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/incident-by-type.png" alt-text="Een weer gave van de widget incident by type.":::

## <a name="devices-by-vendor-widget"></a>Widget apparaten op leverancier

Deze widget geeft een cirkel diagram weer waarin het aantal apparaten per leverancier wordt weer gegeven. Het aantal apparaten voor een specifieke leverancier is evenredig met de grootte van het leveranciers deel van het apparaat van de schijf ten opzichte van andere leveranciers van apparaten.

## <a name="number-of-devices-per-vlan-widget"></a>Aantal apparaten per VLAN-widget

Deze widget geeft een cirkel diagram weer waarin het aantal gedetecteerde apparaten per VLAN wordt weer gegeven. De grootte van elk segment van de cirkel is evenredig met het aantal gedetecteerde apparaten ten opzichte van de andere segmenten.

Elk VLAN wordt weer gegeven met de VLAN-tag die is toegewezen door de sensor of naam die u hand matig hebt toegevoegd.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/number-of-devices.png" alt-text="Een weer gave van het aantal apparaten-widget.":::

### <a name="top-bandwidth-by-vlan-widget"></a>Widget bovenste band breedte per VLAN

Met deze widget wordt het bandbreedte gebruik per VLAN weer gegeven. Standaard toont de widget vijf VLAN'S met het hoogste bandbreedte gebruik.

U kunt de gegevens filteren op de periode die in de widget wordt weer gegeven. Selecteer de pijl-omlaag om meer resultaten weer te geven.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/top-bandwidth.png" alt-text="Een weer gave van de bovenste band breedte per VLAN-widget.":::

## <a name="system-report"></a>Systeem rapport

Het systeem rapport downloaden: 

1. Selecteer **Trends & statistieken** in het menu aan de zijkant.

1. Selecteer **systeem rapport** in de rechter bovenhoek. Het rapport wordt automatisch gedownload.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/system-report.png" alt-text="Selecteer de knop systeem rapport om een kopie van het systeem rapport te downloaden.":::

Het systeem rapport is een PDF-bestand met alle gegevens in het systeem:

  - Apparaten

  - Waarschuwingen

  - Informatie over netwerk beleid

## <a name="devices-in-a-system-report"></a>Apparaten in een systeem rapport 

Het systeem rapport bevat een lijst met alle apparaten en hun gegevens. Bijvoorbeeld type, naam en gebruikte protocollen. In het systeem rapport wordt ook een lijst met apparaten per leverancier weer gegeven.

## <a name="alerts-in-system-report"></a>Waarschuwingen in het systeem rapport 

Het systeem rapport bevat een lijst met alle waarschuwingen met informatie zoals datum en ernst.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/alerts-report.png" alt-text="Een weer gave van de waarschuwingen in de systeem rapporten.":::

## <a name="network-information-in-system-report"></a>Netwerk gegevens in systeem rapport 

Het systeem rapport bevat gedetailleerde informatie over uw netwerk basislijn. Bijvoorbeeld DNP3 functie code en open poorten per verbinding.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/dnp3.png" alt-text="Een weer gave van de functie DNP3 van het systeem rapport.":::

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/open-port.png" alt-text="Een weer gave van het rapport open poort per verbinding.":::

## <a name="see-also"></a>Zie ook

Rapportage van risico [beoordeling](how-to-create-risk-assessment-reports.md) 
 [Sensor gegevens analyse query's](how-to-create-data-mining-queries.md) 
 [Aanvals vector rapportage](how-to-create-attack-vector-reports.md)
