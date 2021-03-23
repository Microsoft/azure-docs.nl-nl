---
title: Poort- en VLAN-naamomzetting verbeteren
description: U kunt de naam van de poort en het VLAN op uw Sens oren aanpassen aan de resolutie van een apparaat.
ms.date: 12/13/2020
ms.topic: how-to
ms.openlocfilehash: de6fbe70d5a5359ad4e4c276642b9b9ed0cef00f
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784166"
---
# <a name="enhance-port-vlan-and-os-resolution"></a>De oplossing voor poort, VLAN en OS verbeteren

U kunt de namen van de poort en VLAN op uw Sens oren aanpassen tot een verrijkende oplossing voor het apparaat.

## <a name="customize-port-names"></a>Poort namen aanpassen

Azure Defender voor IoT wijst automatisch namen toe aan de meeste Universal gereserveerde poorten, zoals DHCP of HTTP. U kunt poort namen aanpassen voor andere poorten die Defender voor IoT detecteert. U kunt bijvoorbeeld een naam toewijzen aan een niet-gereserveerde poort, omdat deze poort ongebruikelijk hoge activiteiten weergeeft.

Deze namen worden weer gegeven wanneer:

  - U selecteert **Apparaatgroepen** in de apparaattoewijzing.

  - U maakt widgets die poort gegevens geven.

### <a name="view-custom-port-names-in-the-device-map"></a>Aangepaste poort namen weer geven in de apparaattoewijzing

Poorten die een naam bevatten die door gebruikers is gedefinieerd, worden weer gegeven in de apparaattoewijzing in de groep **bekende toepassingen** .

:::image type="content" source="media/how-to-enrich-asset-information/applications-v2.png" alt-text="Scherm opname van de apparaattoewijzing, met daarin de groep bekende toepassingen.":::

### <a name="view-custom-port-names-in-widgets"></a>Aangepaste poort namen weer geven in widgets

De poort namen die u hebt opgegeven, worden weer gegeven in de widgets die verkeer per poort dekken.

:::image type="content" source="media/how-to-enrich-asset-information/traffic-v2.png" alt-text="Verkeer bedekken.":::

Aangepaste poort namen definiëren:

1. Selecteer **systeem instellingen** en selecteer **standaard aliassen**.

2. Selecteer **poort alias toevoegen**.

    :::image type="content" source="media/how-to-enrich-asset-information/edit-aliases.png" alt-text="Poort alias toevoegen.":::

3. Voer het poort nummer in, selecteer **TCP/UDP**, of selecteer **beide** en voeg de naam toe.

4. Selecteer **Opslaan**.

## <a name="configure-vlan-names"></a>VLAN-namen configureren

U kunt inventaris gegevens van apparaten verrijken met VLAN-nummers en-tags. Naast gegevens verrijking kunt u het aantal apparaten per VLAN bekijken en de band breedte per VLAN-widgets bekijken.

Ondersteuning voor VLAN'S is gebaseerd op 802.1 q (Maxi maal VLAN-ID 4094).

Er zijn twee methoden beschikbaar voor het ophalen van VLAN-gegevens:

- **Automatisch gedetecteerd**: standaard detecteert de sensor automatisch vlan's. VLAN'S die zijn gedetecteerd met verkeer, worden weer gegeven op het configuratie scherm voor de VLAN, in rapporten voor gegevens analyse en in andere rapporten die VLAN-informatie bevatten. Ongebruikte VLAN'S worden niet weer gegeven. U kunt deze VLAN'S niet bewerken of verwijderen. 

  U moet een unieke naam toevoegen aan elk VLAN. Als u geen naam toevoegt, wordt het VLAN-nummer weer gegeven op alle locaties waar het VLAN wordt gerapporteerd.

- **Hand matig toegevoegd**: u kunt vlan's hand matig toevoegen. U moet een unieke naam toevoegen voor elk VLAN dat hand matig is toegevoegd, en u kunt deze VLAN'S bewerken of verwijderen.

VLAN-namen kunnen Maxi maal 50 ASCII-tekens bevatten.

> [!NOTE]
> VLAN-namen worden niet gesynchroniseerd tussen de sensor en de beheer console. U moet ook de naam opgeven in de beheer console.  
Voor Cisco-switches voegt u de volgende regel toe aan de configuratie van het bereik: `monitor session 1 destination interface XX/XX encapsulation dot1q` . In die opdracht *xx/xx* de naam en het nummer van de poort.

VLAN-namen configureren:

1. Selecteer **systeem instellingen** in het menu aan de zijkant.

2. Selecteer in het venster **systeem instellingen** de optie **VLAN**.

    :::image type="content" source="media/how-to-enrich-asset-information/edit-vlan.png" alt-text="Gebruik de systeem instellingen om uw VLAN'S te bewerken.":::

3. Voeg een unieke naam toe naast elke VLAN-ID.

## <a name="improve-device-operating-system-classification-data-enhancement"></a>De classificatie van het besturings systeem van het apparaat verbeteren: gegevens verbetering

Sens oren detecteren voortdurend automatisch nieuwe apparaten en wijzigingen aan eerder gedetecteerde apparaten, waaronder typen besturings systemen.

Onder bepaalde omstandigheden kunnen conflicten worden gedetecteerd in gedetecteerde besturings systemen. Dit kan bijvoorbeeld gebeuren als u een versie van het besturings systeem hebt die verwijst naar een bureau blad of server systeem. Als dit het geval is, ontvangt u een melding met optionele classificaties van besturings systemen.

:::image type="content" source="media/how-to-enrich-asset-information/enhance-data-screen.png" alt-text="Gegevens verbeteren.":::

Onderzoek de aanbevelingen om de classificatie van het besturings systeem te verrijken. Deze classificatie wordt weer gegeven in de inventarisatie van apparaten, rapporten voor gegevens analyse en andere weer gaven. U kunt de nauw keurigheid van waarschuwingen, bedreigingen en rapporten over risico analyses verbeteren door ervoor te zorgen dat deze informatie up-to-date is.

Voor toegang tot aanbevelingen van het besturings systeem:

1. Selecteer **systeem instellingen**.
1. Selecteer **gegevens uitbreiding**.

## <a name="next-steps"></a>Volgende stappen

Uitgebreide informatie over apparaten weer geven in verschillende rapporten:

- [Alle sensordetecties in een apparaatinventaris onderzoeken](how-to-investigate-sensor-detections-in-a-device-inventory.md)
- [Sensor trends en statistieken rapporten](how-to-create-trends-and-statistics-reports.md)
- [Sensor gegevens analyse query's](how-to-create-data-mining-queries.md)
