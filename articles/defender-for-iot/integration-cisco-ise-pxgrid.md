---
title: Over de Cisco ISE pxGrid-integratie
titleSuffix: Azure Defender for IoT
description: Het overbruggen van de mogelijkheden van Defender voor IoT met Cisco ISE pxGrid biedt beveiligings teams ongekende zicht baarheid van apparaten aan het bedrijfs ecosysteem.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/28/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 3f1fb099aa18ebec5a7e2063740556cf806302e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98558089"
---
# <a name="about-the-cisco-ise-pxgrid-integration"></a>Over de Cisco ISE pxGrid-integratie

Defender voor IoT levert het enige ICS-en IoT Cyber beveiliging-platform dat is gebouwd door Blue-team experts met een track record die een kritieke nationale infra structuur beschermt, en het enige platform met gepatenteerde en machine learning met octrooi detectie. Defender voor IoT biedt:

- Direct inzicht in de ICS-apparaten, beveiligings problemen en bekende en Zero-Day bedreigingen.

- Met Internet verbinding met uitgebreide Inge sloten kennis van protocollen, apparaten, toepassingen en het gedrag ervan.

- Een geautomatiseerde ICS-technologie voor het afhandelen van bedreigingen om de meest waarschijnlijke paden van gerichte ICS-aanvallen te voors pellen via eigen analyses.

## <a name="powerful-device-visibility-and-control"></a>Krachtige zicht baarheid en beheer van apparaten

De Defender voor IoT-integratie met Cisco ISE pxGrid biedt een nieuw niveau voor gecentraliseerde zicht baarheid, bewaking en controle voor het land.

Deze met brug geteste platforms maken automatische zicht baarheid en beveiliging van apparaten mogelijk tot voorheen onbereikbare ICS-en IIoT-apparaten.

### <a name="ics-and-iiot-device-visibility-comprehensive-and-deep"></a>Zicht baarheid van ICS en IIoT apparaat: uitgebreide en diepe

De gepatenteerde Defender voor IoT-technologieën garanderen uitgebreide en diepe ICS-en IIoT-detectie-en inventaris beheer voor bedrijfs gegevens.

Apparaattypen, fabrikanten, open poorten, serie nummers, firmware revisies, IP-adressen en MAC-adres gegevens en meer worden in realtime bijgewerkt. Defender voor IoT kan de zicht baarheid, detectie en analyse van deze basis lijn verder verbeteren door te integreren met kritieke gegevens bronnen in de onderneming. Bijvoorbeeld CMDB, DNS, firewalls en Web-Api's.

Daarnaast combineert het Defender voor IoT-platform passieve bewaking en optionele selectieve probing-technieken om de meest nauw keurige en gedetailleerde inventaris van apparaten in industriële en kritieke infrastructuur organisaties te bieden.

### <a name="bridged-capabilities"></a>Brug mogelijkheden

Door deze mogelijkheden te bridgen met Cisco ISE pxGrid, biedt beveiligings teams ongekende zicht baarheid van apparaten aan het bedrijfs ecosysteem.

Naadloze, robuuste integratie met Cisco ISE pxGrid garandeert niet dat het apparaat niet wordt gedetecteerd en er geen apparaatgegevens worden gemist.

:::image type="content" source="media/integration-cisco-isepxgrid-integration/endpoint-categories.png" alt-text="Voor beeld van de de OUI van de eindpunt categorie.":::

:::image type="content" source="media/integration-cisco-isepxgrid-integration/endpoints.png" alt-text="Voorbeeld eindpunten in het systeem":::  

:::image type="content" source="media/integration-cisco-isepxgrid-integration/attributes.png" alt-text="Scherm afbeelding van de kenmerken die zich in het systeem bevinden.":::  

### <a name="use-case-coverage-ise-policies-based-on-defender-for-iot-attributes"></a>Use-case-dekking: ISE-beleid op basis van de Defender voor IoT-kenmerken

Gebruik krachtige ISE-beleids regels op basis van Defender voor IoT diepe learning voor het afhandelen van ICS-en IoT-gebruiks Case vereisten.

Als u werkt met beleids regels, kunt u de beveiligings cyclus sluiten en uw netwerk in realtime aan te passen.

Klanten kunnen bijvoorbeeld Defender gebruiken voor IoT-ICS en IoT-kenmerken om beleid te maken dat de volgende use cases verwerkt, zoals:

- Maak een autorisatie beleid om bekende en geautoriseerde apparaten toe te staan in een gevoelige zone op basis van toegestane kenmerken, bijvoorbeeld specifieke firmware versie voor Rock well Automation PLCs.

- Beveiligings teams waarschuwen wanneer een ICS-apparaat wordt gedetecteerd in een niet-OT-zone.

- Computers met verouderde of niet-compatibele leveranciers herstellen.

- U moet apparaten in quarantaine plaatsen en blok keren als dat nodig is.

- Rapporten genereren over PLCs of RTUs die firmware uitvoeren met bekende beveiligings problemen (CVEs).

### <a name="about-this-article"></a>Over dit artikel

In dit artikel wordt beschreven hoe u pxGrid en het Defender voor IoT-platform instelt om ervoor te zorgen dat Defender voor IoT de kenmerken van Cisco ISE injecteert.

### <a name="getting-more-information"></a>Meer informatie ophalen

Zie voor meer informatie over de integratie vereisten voor Cisco ISE pxGrid <https://www.cisco.com/c/en/us/products/security/pxgrid.html>

## <a name="integration-system-requirements"></a>Systeem vereisten voor integratie

In dit artikel worden de vereisten voor het integratie systeem beschreven:

Defender voor IoT-vereisten

- Defender voor IoT versie 2,5

Cisco-vereisten

- pxGrid-versie 2,0

- Cisco ISE-versie 2,4

Netwerkvereisten

- Controleer of het apparaat Defender voor IoT toegang heeft tot de Cisco ISE management-interface.

- Controleer of u CLI-toegang hebt tot alle Defender voor IoT-apparaten in uw onderneming.

> [!NOTE]
> Alleen apparaten met MAC-adressen worden gesynchroniseerd met Cisco ISE pxGrid.

## <a name="cisco-ise-pxgrid-setup"></a>Cisco ISE pxGrid-installatie

In dit artikel wordt beschreven hoe u:

- Communicatie met pxGrid instellen

- Abonneren op het onderwerp van het eindpunt apparaat

- Certificaten genereren

- PxGrid-instellingen definiëren

## <a name="set-up-communication-with-pxgrid"></a>Communicatie met pxGrid instellen

In dit artikel wordt beschreven hoe u communicatie met pxGrid instelt.

Communicatie instellen:

1. Meld u aan bij Cisco ISE.

1. Selecteer een **beheer** > **systeem** en **implementatie**.

1. Selecteer het vereiste knoop punt. Schakel op het tabblad Algemene instellingen het **selectie vakje pxGrid** in.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/pxgrid.png" alt-text="Zorg ervoor dat het selectie vakje pxgrid is geselecteerd.":::

1. Selecteer het tabblad **configuratie van profile ring** .

1. Schakel het **selectie vakje pxGrid** in. Voeg een beschrijving toe.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/profile-configuration.png" alt-text="Scherm afbeelding van de beschrijving toevoegen":::

## <a name="subscribe-to-the-endpoint-device-topic"></a>Abonneren op het onderwerp van het eindpunt apparaat

Controleer of het ISE pxGrid-knoop punt is geabonneerd op het onderwerp van het eindpunt apparaat. Ga naar **beheer** > **pxGrid Services**- > **webclients**. Daar kunt u controleren of ISE is geabonneerd op het onderwerp van het eindpunt apparaat.

## <a name="generate-certificates"></a>Certificaten genereren

In dit artikel wordt beschreven hoe u certificaten kunt genereren.

Genereren:

1. Selecteer **beheer**  >  **pxGrid Services** en selecteer vervolgens **certificaten**.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/certificates.png" alt-text="Selecteer het tabblad Certificaten om een certificaat te genereren.":::

1. Selecteer in het veld **Ik** wil **een enkel certificaat genereren (zonder aanvraag voor certificaat ondertekening)**.

1. Vul de resterende velden in en selecteer **maken**.

1. Maak de client certificaat sleutel en het server certificaat en converteer deze naar de indeling voor Java-sleutel opslag. U moet deze kopiëren naar elke Defender voor IoT-sensor in uw netwerk.

## <a name="define-pxgrid-settings"></a>PxGrid-instellingen definiëren

Instellingen definiëren:

1. Selecteer **beheer**  >  **pxGrid Services** en selecteer vervolgens **instellingen**.

1. Schakel het **automatisch goed keuren van nieuwe certificaat accounts** in en **toestaan dat accounts op basis van wacht woorden worden gemaakt.**

  :::image type="content" source="media/integration-cisco-isepxgrid-integration/allow-these.png" alt-text="Zorg ervoor dat beide selectie vakjes zijn geselecteerd.":::

## <a name="set-up-the-defender-for-iot-appliances"></a>De Defender voor IoT-apparaten instellen

In dit artikel wordt beschreven hoe u Defender voor IoT-apparaten instelt voor communicatie met pxGrid. De configuratie moet worden uitgevoerd op alle Defender voor IoT-apparaten waarmee gegevens worden geinjecteerd naar Cisco ISE.

Apparaten instellen:

1. Meld u aan bij de sensor-CLI.

1. Kopiëren `trust.jks` en, die eerder zijn gemaakt op de sensor. Kopiëren naar: `/var/cyberx/properties/` .

1. Bewerken `/var/cyberx/properties/pxgrid.properties` :

    1. Voeg een sleutel en vertrouwens relatie toe en sla bestands namen en wacht woorden op.

    2. Voeg de hostnaam van de pxGrid-instantie toe.

    3. `Enabled=true`

1. Start het apparaat opnieuw op.

1. Herhaal deze stappen voor elk apparaat in uw onderneming waarmee gegevens worden ingevoegd.

## <a name="view-and-manage-detections-in-cisco-ise"></a>Detecties weer geven en beheren in Cisco ISE

1. Eindpunt detecties worden weer gegeven op het tabblad ISE context **zichtbaarheids**  >  **eindpunt** .

1. **Beleids**  >  **Profiler** selecteren  >    >  **regels** toevoegen  >  **+ voor waarde**  >  **nieuwe voor waarde maken**.

1. Selecteer **kenmerk** en gebruik de IOT-apparaten woordenboeken om een profilerings regel te maken op basis van de kenmerken die zijn geïnjecteerd. De volgende kenmerken worden geïnjecteerd.

    - Apparaattype

    - Hardware-revisie

    - IP-adres

    - MAC-adres

    - Name

    - Product-id

    - Protocol

    - Serienummer

    - Software revisie

    - Leverancier

Alleen apparaten met MAC-adressen worden gesynchroniseerd.

## <a name="troubleshooting-and-logs"></a>Probleem oplossing en Logboeken

Logboeken vindt u in:

- `/var/cyberx/logs/pxgrid.log`

- `/var/cyberx/logs/core.log`

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [door sturen van waarschuwings gegevens](how-to-forward-alert-information-to-partners.md).
