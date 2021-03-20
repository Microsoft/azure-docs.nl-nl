---
title: Over de Forescout-integratie
titleSuffix: Azure Defender for IoT
description: De integratie van Azure Defender voor IoT met het Forescout-platform biedt een nieuw niveau voor gecentraliseerde zicht baarheid, bewaking en controle voor de IoT en het landschap.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/17/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: faa53c770d0d6caac471e770c80b4dfd5c5ff603
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98558025"
---
# <a name="about-the-forescout-integration"></a>Over de Forescout-integratie

Azure Defender voor IoT levert een ICS-en IoT Cyber beveiliging-platform dat is gebouwd door Blue team experts, met een bijhouden van een belang rijke nationale infra structuur. Defender voor IoT is het enige platform met gepatenteerde ICS-Detectie en machine learning. Defender voor IoT biedt:

- Direct inzicht in de Internet verbinding van het apparaat is liggend, met een uitgebreid scala aan details over kenmerken.
- Met Internet verbinding met uitgebreide Inge sloten kennis van protocollen, apparaten, toepassingen en het gedrag ervan.
- Direct inzicht in beveiligings problemen en bedreigingen met bekende en Zero dagen.
- Een geautomatiseerde ICS-technologie voor het afhandelen van bedreigingen om de meest waarschijnlijke paden van gerichte ICS-aanvallen te voors pellen via eigen analyses.

De integratie van de Defender voor IoT met het Forescout-platform biedt een nieuw niveau voor gecentraliseerde zicht baarheid, bewaking en beheer voor IoT en het landschap.

Met deze met brug geschakelde platforms wordt de zicht baarheid en het beheer van apparaten geautomatiseerd op voorheen onbereikbare ICS-apparaten en bewerkte workflows

De integratie biedt SOC analisten inzicht in de bewaarden van de protocollen die in industriële omgevingen worden geïmplementeerd. Er zijn details beschikbaar voor items zoals firmware, apparaattypen, besturings systemen en scores voor risico analyse op basis van bedrijfs eigen Azure Defender voor IoT-technologieën.

> [!Note]
> Verwijzingen naar Cyberx verwijzen naar Azure Defender voor IoT.
## <a name="devices"></a>Apparaten

### <a name="device-visibility-and-management"></a>Zicht baarheid en beheer van apparaten

De inventaris van het apparaat wordt verrijkt met essentiële kenmerken die zijn gedetecteerd door het Defender voor IoT-platform. Dit zorgt ervoor dat u:

- Verkrijg uitgebreide en doorlopende zicht baarheid in de lands Cape van een enkel deel venster.
- Ontvang real-time informatie over Risico's.

:::image type="content" source="media/integration-forescout/forescout-device-inventory.png" alt-text="Inventaris van apparaten":::

:::image type="content" source="media/integration-forescout/forescout-device-details.png" alt-text="Apparaatgegevens":::

### <a name="device-control"></a>Apparaatbeheer

De Forescout-integratie helpt de tijd te verkorten die nodig is voor industriële en kritieke infrastructuur organisaties om Cyber bedreigingen te detecteren, te onderzoeken en te reageren.

- Gebruik Azure Defender voor IoT OT van het apparaat om de beveiligings cyclus te sluiten door Forescout-beleids acties te activeren. U kunt bijvoorbeeld automatisch een waarschuwings bericht sturen naar SOC-beheerders wanneer er specifieke protocollen worden gedetecteerd of wanneer de details van de firmware worden gewijzigd.

- Correleer Defender voor IoT-informatie met andere *Forescout eyeExtended* -modules die toezicht houden op bewaking, incident beheer en apparaatbesturing.

## <a name="system-requirements"></a>Systeemvereisten

- Azure Defender voor IoT versie 2,4 of hoger
- Forescout-versie 8,0 of hoger
- Een licentie voor de *Forescout eyeExtend* -module voor het Azure Defender voor IOT-platform.

### <a name="getting-more-forescout-information"></a>Meer informatie over Forescout ophalen

Zie het [Forescout Resource Center](https://www.forescout.com/company/resources/#resource_filter_group)voor meer informatie over het Forescout-platform.

## <a name="system-setup"></a>Systeem installatie

In dit artikel wordt beschreven hoe u communicatie kunt instellen tussen het Defender voor IoT-platform en het Forescout-platform.

### <a name="set-up-the-defender-for-iot-platform"></a>Het Defender voor IoT-platform instellen

Genereer een toegangs token in Defender voor IoT om te zorgen voor communicatie vanuit Defender voor IoT naar Forescout.

Met toegangs tokens kunnen externe systemen toegang krijgen tot gegevens die zijn gedetecteerd door Defender voor IoT en acties uitvoeren met die gegevens via de externe REST API, via SSL-verbindingen. U kunt toegangs tokens genereren om toegang te krijgen tot de Azure Defender voor IoT-REST API.

Een token genereren:

1. Meld u aan bij de Defender voor IoT-sensor die wordt doorzocht op Forescout.

1. Selecteer **systeem instellingen** en selecteer vervolgens **toegangs tokens** in de sectie **Algemeen** . Het dialoog venster **toegangs tokens** wordt geopend.
   :::image type="content" source="media/integration-forescout/generate-access-tokens-screen.png" alt-text="Toegangstokens":::
1. Selecteer **nieuw token genereren** in het dialoog venster **toegangs tokens** .
1. Voer een beschrijving van het token in het dialoog venster **nieuw toegangs token** in.
   :::image type="content" source="media/integration-forescout/new-forescout-token.png" alt-text="Nieuw toegangs token":::
1. Selecteer **Next**. Het token wordt weer gegeven in het dialoog venster. :::image type="content" source="media/integration-forescout/forescout-access-token-display-screen.png" alt-text="Token weer geven":::
   > [!NOTE]
   > *Registreer het token op een veilige plaats. U hebt deze nodig bij het configureren van het Forescout-platform*.
1. Selecteer **Finish**.

   :::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="Toevoegen van token volt ooien":::

### <a name="set-up-the-forescout-platform"></a>Het Forescout-platform instellen

U kunt het Forescout-platform instellen om te communiceren met een Defender voor IoT-sensor.

Instellen:

1. Installeer *de Forescout eyeExtend-module voor cyberx* op het Forescout-platform.

1. Meld u aan bij de bestaans console en selecteer **Opties** in het menu **extra** . Het dialoog venster **Opties** wordt geopend.

1. Navigeer naar en selecteer de map **modules** .

1. Selecteer in  het deel venster modules **cyberx-platform**. Het deel venster Defender voor IoT-platform wordt geopend.

   :::image type="content" source="media/integration-forescout/settings-for-module.png" alt-text="Instellingen voor Azure Defender voor IoT-module":::

   Voer de volgende informatie in:

   - **Server adres** : Voer het IP-adres in van de Defender voor IOT-sensor die wordt doorzocht op het Forescout-apparaat.
   - **Toegangs token** : Voer het toegangs token in dat is gegenereerd voor de Defender voor IOT-sensor dat verbinding maakt met het Forescout-apparaat. Zie [het platform Defender voor IOT instellen voor het genereren van](#set-up-the-defender-for-iot-platform)een token.

1. Selecteer **Toepassen**.

Als u wilt dat het Forescout-platform met een andere sensor communiceert:

1. Maak een nieuw toegangs token in de relevante Defender voor IoT-sensor.

1. In het dialoog venster **Forescout modules**  >  **cyberx platform** :

   - De weer gegeven informatie verwijderen.
   
   - Voer het nieuwe IP-adres van de sensor en de nieuwe toegangs token gegevens in.

### <a name="verify-communication"></a>Communicatie verifiëren

Nadat u Defender voor IoT en Forescout hebt geconfigureerd, opent u het dialoog venster toegangs tokens van de sensor in Defender voor IoT.

Als **N/A** wordt weer gegeven in het veld **gebruikt** voor deze token, werkt de verbinding tussen de sensor en het Forescout-apparaat niet.

**Wordt gebruikt** geeft aan wanneer een externe aanroep met dit token voor het laatst is ontvangen.

:::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="Hiermee wordt gecontroleerd of het token is ontvangen":::

## <a name="view-defender-for-iot-detections-in-forescout"></a>Defender voor IoT-detecties in Forescout weer geven

De kenmerken van een apparaat weer geven:

1. Meld u aan bij het Forescout-platform en navigeer vervolgens naar de **inventaris van activa**.

1. Navigeer naar het **Cyber**-x-platform. De volgende apparaatfuncties worden weer gegeven voor apparaten die zijn gedetecteerd door Defender voor IoT.

   | Item | Beschrijving |
   |--|--|
   | Geautoriseerd door Azure Defender voor IoT | Een apparaat dat in uw netwerk is gedetecteerd door Defender voor IoT tijdens de netwerk Leer periode. |
   | Firmware | De firmware details van het apparaat. Bijvoorbeeld model en Details van versie. |
   | Naam | De naam van het apparaat. |
   | Besturingssysteem | Het besturings systeem van het apparaat. |
   | Type | Het type apparaat. Bijvoorbeeld een PLC, historian of technisch station. |
   | Leverancier | De leverancier van het apparaat. Bijvoorbeeld Rock well Automation. |
   | Risico niveau | Het risico niveau dat wordt berekend door Defender voor IoT. |
   | Protocollen | De protocollen die zijn gedetecteerd in het verkeer dat door het apparaat is gegenereerd. |

:::image type="content" source="media/integration-forescout/device-firmware-attributes-in-forescout.png" alt-text="De firmware kenmerken weer geven.":::

:::image type="content" source="media/integration-forescout/vendor-attributes-in-forescout.png" alt-text="De kenmerken van de leverancier weer geven.":::

### <a name="viewing-more-details"></a>Meer details weer geven

Extra apparaatgegevens weer geven voor apparaten die door Defender voor IoT worden gestuurd. Bijvoorbeeld Forescout-naleving en beleids gegevens.

Als u dit wilt doen, klikt u met de rechter muisknop op een apparaat in de sectie **device Inventory-hosts** . Het dialoog venster host Details wordt geopend met aanvullende informatie.

:::image type="content" source="media/integration-forescout/details-dialog-box-in-forescout.png" alt-text="Details van host":::

## <a name="create-azure-defender-for-iot-policies-in-forescout"></a>Azure Defender voor IoT-beleid maken in Forescout

Forescout-beleid kan worden gebruikt om het beheer en het beheer van apparaten die door Defender voor IoT worden gedetecteerd, te automatiseren. Bijvoorbeeld:

- Automatisch de SOC-beheerders e-mailen wanneer specifieke firmware versies worden gedetecteerd.

- Voeg specifieke Defender voor IoT-apparaten toe aan een Forescout-groep voor verdere verwerking in incident-en beveiligings werk stromen, bijvoorbeeld met andere SIEM-integraties.

Maak een aangepast Forescout-beleid, met Defender voor IoT, met behulp van voorwaarde eigenschappen.

Om toegang te krijgen tot Defender voor IoT-eigenschappen:

1. Ga naar de **structuur eigenschappen** in het dialoog venster **beleids voorwaarden** .

1. Vouw de map **cyberx platform** in de **structuur eigenschappen** uit. De volgende eigenschappen van Defender voor IoT zijn beschikbaar.

:::image type="content" source="media/integration-forescout/forescout-property-tree.png" alt-text="Eigenschappen":::

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [door sturen van waarschuwings gegevens](how-to-forward-alert-information-to-partners.md).
