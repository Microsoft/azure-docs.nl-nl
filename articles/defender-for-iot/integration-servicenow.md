---
title: Over de ServiceNow-integratie
titleSuffix: Azure Defender for IoT
description: De Defender voor IoT ICS-beheer toepassing voor ServiceNow biedt SOC analisten met multidimensionale zicht baarheid in de gespecialiseerde protocollen en IoT-apparaten die in industriële omgevingen worden geïmplementeerd, samen met gedrags analyse met ICS-functionaliteit om snel verdacht of afwijkend gedrag te detecteren.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/17/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: f2a4c3e79a762de19c6e8c029256cd70dedfe3dc
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/18/2021
ms.locfileid: "98558113"
---
# <a name="the-defender-for-iot-ics-management-application-for-servicenow"></a>De Defender voor IoT ICS-beheer toepassing voor ServiceNow

De Defender voor IoT ICS-beheer toepassing voor ServiceNow biedt SOC analisten met multidimensionale zicht baarheid in de gespecialiseerde protocollen en IoT-apparaten die in industriële omgevingen worden geïmplementeerd, samen met gedrags analyse met ICS-functionaliteit om snel verdacht of afwijkend gedrag te detecteren. Dit is een belang rijke evolutie van de lopende convergentie van IT en het ondersteunen van nieuwe IoT-initiatieven, zoals Smart machines en real-time intelligence.

De toepassing maakt ook zowel de reactie van de incidenten als van de respons van een zakelijke SOC mogelijk.

## <a name="about-defender-for-iot"></a>Over Defender voor IoT

Defender voor IoT levert het enige ICS-en IoT Cyber beveiliging-platform dat is gebouwd door Blue-team experts met een track record die een kritieke nationale infra structuur beschermt, en het enige platform met gepatenteerde en machine learning met octrooi detectie. Defender voor IoT biedt:

- Direct inzicht in de Internet verbinding van het apparaat is liggend, met een uitgebreid scala aan details over kenmerken.

- Met Internet verbinding met uitgebreide Inge sloten kennis van protocollen, apparaten, toepassingen en het gedrag ervan.

- Direct inzicht in beveiligings problemen en bekende bedreigingen met een nul dag.

- Een geautomatiseerde ICS-technologie voor het afhandelen van bedreigingen om de meest waarschijnlijke paden van gerichte ICS-aanvallen te voors pellen via eigen analyses.

> [!Note]
> Verwijzingen naar Cyberx verwijzen naar Azure Defender voor IoT.

## <a name="about-the-integration"></a>Over de integratie

De Defender voor IoT-integratie met ServiceNow biedt een nieuw niveau voor gecentraliseerde zicht baarheid, bewaking en beheer voor IoT en het landschap. Deze met brug geteste platforms maken het mogelijk om de zicht baarheid en het bedreigings beheer van apparaten te verduidelijken tot voorheen onbereikbare ICS & IoT

### <a name="threat-management"></a>Beveiligingsbeheer

De Defender voor IoT ICS-beheer toepassing helpt:

- Verminder de tijd die nodig is voor organisaties van industriële en kritieke infra structuur om Cyber bedreigingen te detecteren, te onderzoeken en te handelen.

- Ontvang real-time informatie over Risico's.

- Correleer Defender voor IoT-waarschuwingen met ServiceNow Threat bewaking-en incident management-werk stromen.

- Activeer ServiceNow-tickets en-werk stromen met andere services en toepassingen op het ServiceNow-platform.

Beveiligings Risico's voor Internet-verbinding delen en SCADA worden geïdentificeerd door Defender voor IoT-beveiligings engines, die onmiddellijke waarschuwings reacties bieden op malware-aanvallen, netwerk-en beveiligings beleid afwijkingen, evenals operationele en protocol afwijkingen. Zie [Alert Reporting](#alert-reporting)(Engelstalig) voor meer informatie over waarschuwings details die naar ServiceNow worden verzonden.

### <a name="device-visibility-and-management"></a>Zicht baarheid en beheer van apparaten

De ServiceNow-configuratie beheer database (CMDB) is verrijkt en aangevuld met een uitgebreide set kenmerken van apparaten die worden gepusht door het Defender voor IoT-platform. Dit zorgt voor uitgebreide en doorlopende zicht baarheid van het apparaat en biedt u de mogelijkheid om te controleren en te reageren vanuit een enkel deel venster. Zie [Defender voor IOT-detecties in ServiceNow weer geven](#view-defender-for-iot-detections-in-servicenow)voor meer informatie over de kenmerken van apparaten die naar ServiceNowSee worden verzonden.

## <a name="system-requirements-and-architecture"></a>Systeem vereisten en-architectuur

In dit artikel wordt het volgende beschreven:

- **Software vereisten**  
- **Architectuur**

## <a name="software-requirements"></a>Softwarevereisten

- ServiceNow Service Management-versie 3.0.2

- Defender voor IoT-patch 2.8.11.1 of hoger

> [!Note]
> Als u al werkt met een Defender voor IoT-en ServiceNow-integratie en u een upgrade uitvoert met behulp van de on-premises beheer console, moeten eerdere gegevens die zijn ontvangen van Defender voor IoT Sens oren worden gewist van ServiceNow.

## <a name="architecture"></a>Architectuur

### <a name="on-premises-management-console-architecture"></a>Architectuur van on-premises beheer console

De on-premises beheer console biedt een uniforme bron voor alle apparaten en waarschuwings gegevens die worden verzonden naar ServiceNow.

U kunt een on-premises beheer console instellen om te communiceren met één exemplaar van ServiceNow. De on-premises beheer console pusht sensor gegevens naar de Defender voor IoT-toepassing met behulp van REST API.

Als u uw systeem instelt voor gebruik met een on-premises beheer console, schakelt u de ServiceNow-synchronisatie, doorstuur regels en proxy configuraties in Sens oren uit als deze zijn ingesteld.

Deze configuraties moeten worden ingesteld voor de on-premises beheer console. In dit artikel worden configuratie-instructies beschreven.

### <a name="sensor-architecture"></a>Sensor architectuur

Als u uw omgeving wilt instellen om directe communicatie tussen Sens oren en ServiceNow op te geven, definieert u voor elke sensor de ServiceNow Sync, doorstuur regels en proxy configuratie (als er een proxy is vereist).

Het verdient aanbeveling uw integratie in te stellen met behulp van de on-premises beheer console om te communiceren met ServiceNow.

## <a name="create-access-tokens-in-servicenow"></a>Toegangs tokens maken in ServiceNow

In dit artikel wordt beschreven hoe u een toegangs token maakt in ServiceNow. Het token is nodig om te communiceren met Defender voor IoT.

U hebt de **client-id** en het **client geheim** nodig bij het maken van Defender voor IOT-doorstuur regels, waarmee waarschuwings gegevens worden doorgestuurd naar ServiceNow, en bij het configureren van Defender voor IOT om kenmerken van apparaten te pushen naar ServiceNow-tabellen.

## <a name="set-up-defender-for-iot-to-communicate-with-servicenow"></a>Defender voor IoT instellen om te communiceren met ServiceNow

In dit artikel wordt beschreven hoe u Defender voor IoT kunt instellen om te communiceren met ServiceNow.

### <a name="send-defender-for-iot-alert-information"></a>Informatie over de waarschuwing voor het verzenden van Defender voor IoT

In dit artikel wordt beschreven hoe u Defender voor IoT kunt configureren om waarschuwings gegevens te pushen naar ServiceNow-tabellen. Zie [Alert Reporting](#alert-reporting)(Engelstalig) voor meer informatie over waarschuwings gegevens die worden verzonden.

Verdedigings voor IOT-waarschuwingen worden weer gegeven in ServiceNow als beveiligings incidenten.

Definieer een regel voor het *door sturen* van Defender voor IOT om waarschuwings gegevens naar ServiceNow te verzenden.

De regel definiëren:

1. Selecteer **door sturen** in het linkerdeel venster van Defender voor IOT.  

1. Selecteer de :::image type="content" source="media/integration-servicenow/plus.png" alt-text="knop met het plus teken."::: . Het dialoog venster regel voor door sturen maken wordt geopend.  

    :::image type="content" source="media/integration-servicenow/forwarding-rule.png" alt-text="Doorstuur regel maken":::

1. Voeg een regel naam toe.

1. Criteria definiëren waaronder met Defender voor IoT de doorstuur regel wordt geactiveerd. Bij het werken met criteria voor doorstuur regels kunt u het volume van de informatie die wordt verzonden vanuit Defender voor IoT naar ServiceNow herkennen en beheren. De volgende opties zijn beschikbaar:

    - **Ernst niveaus:** Dit is het incident van het minimale beveiligings niveau dat moet worden doorgestuurd. Als bijvoorbeeld **secundair** is geselecteerd, worden kleine waarschuwingen en elke waarschuwing die hoger is dan dit Ernst niveau, doorgestuurd. Niveaus worden vooraf gedefinieerd door Defender voor IoT.

    - **Protocollen:** Activeer alleen de doorstuur regel als het verkeer dat is gedetecteerd, meer dan een specifiek protocol heeft uitgevoerd. Selecteer de vereiste protocollen in de vervolg keuzelijst of kies alle.

    - **Engines:** Selecteer de vereiste engines of kies ze allemaal. Waarschuwingen van geselecteerde engines worden verzonden.

1. Controleer of **rapport waarschuwings meldingen** is geselecteerd.

1. Selecteer in de sectie acties de optie **toevoegen** en selecteer vervolgens **ServiceNow**.

    :::image type="content" source="media/integration-servicenow/select-servicenow.png" alt-text="Selecteer ServiceNow in de opties voor de vervolg keuzelijst.":::

1. Voer de ServiceNow-actie parameters in:

    :::image type="content" source="media/integration-servicenow/parameters.png" alt-text="Vul de para meters voor de ServiceNow-actie in":::

1. Stel in het deel venster **acties** de volgende para meters in:

  | Parameter | Beschrijving |
  |--|--|
  | Domain | Voer het IP-adres van de ServiceNow-server in. |
  | Gebruikersnaam | Voer de gebruikers naam van de ServiceNow-server in. |
  | Wachtwoord | Voer het wacht woord voor de ServiceNow-server in. |
  | Client-id | Voer de client-ID in die u hebt ontvangen voor Defender voor IoT op de pagina **toepassings registers** in ServiceNow. |
  | Clientgeheim | Voer de client geheim teken reeks in die u hebt gemaakt voor Defender voor IoT op de pagina **toepassings registers** in ServiceNow. |
  | Rapport type | **Incidenten**: een lijst met waarschuwingen die worden weer gegeven in ServiceNow door sturen met een incident-id en een korte beschrijving van elke waarschuwing.<br /><br />**Defender voor IOT-toepassing**: Hiermee kunt u volledige waarschuwings gegevens door sturen, met inbegrip van de sensor Details, de engine, de bron en doel adressen. De informatie wordt doorgestuurd naar de Defender voor IoT op de ServiceNow-toepassing. |

1. Selecteer **SAVE** (Opslaan). Verdedigings voor IOT-waarschuwingen worden weer gegeven als incidenten in ServiceNow.

### <a name="send-defender-for-iot-device-attributes"></a>Defender voor IoT-kenmerken van het apparaat verzenden

In dit artikel wordt beschreven hoe u Defender voor IoT kunt configureren om een uitgebreid scala aan kenmerken van apparaten te pushen naar ServiceNow-tabellen. Zie **_inventaris informatie_* _ voor meer informatie over het type informatie dat naar ServiceNow is gepusht.

Als u kenmerken wilt verzenden naar ServiceNow, moet u uw on-premises beheer console toewijzen aan een ServiceNow-exemplaar. Dit zorgt ervoor dat het platform Defender voor IoT kan communiceren en verificatie kan uitvoeren met het exemplaar.

Een ServiceNow-exemplaar toevoegen:

1. Meld u aan bij uw Defender voor IoT on-premises beheer console.

1. Selecteer _ *systeem instellingen** en klik vervolgens **ServiceNow** in de sectie over de integratie van de on-premises beheer console.

      :::image type="content" source="media/integration-servicenow/servicenow.png" alt-text="Selecteer de knop ServiceNow.":::

1. Voer de volgende synchronisatie parameters in het dialoog venster ServiceNow-synchronisatie in.

    :::image type="content" source="media/integration-servicenow/sync.png" alt-text="Het dialoog venster ServiceNow-synchronisatie.":::

     Parameter | Beschrijving |
    |--|--|
    | Synchronisatie inschakelen | De synchronisatie inschakelen en uitschakelen na het definiëren van para meters. |
    | Synchronisatie frequentie (minuten) | Standaard worden gegevens elke 60 minuten gepusht naar ServiceNow. Het minimum is 5 minuten. |
    | ServiceNow-exemplaar | Voer de URL van het ServiceNow-exemplaar in. |
    | Client-id | Voer de client-ID in die u hebt ontvangen voor Defender voor IoT op de pagina **toepassings registers** in ServiceNow. |
    | Clientgeheim | Voer de client geheim teken reeks in die u hebt gemaakt voor Defender voor IoT op de pagina **toepassings registers** in ServiceNow. |
    | Gebruikersnaam | Voer de gebruikers naam voor dit exemplaar in. |
    | Wachtwoord | Voer het wacht woord voor dit exemplaar in. |

1. Selecteer **SAVE** (Opslaan).

## <a name="verify-communication"></a>Communicatie verifiëren

Controleer of de on-premises beheer console is verbonden met het ServiceNow-exemplaar door de *laatste synchronisatie* datum te controleren.

:::image type="content" source="media/integration-servicenow/sync-confirmation.png" alt-text="Controleer of de communicatie heeft plaatsgevonden door de laatste synchronisatie te bekijken.":::

## <a name="set-up-the-integrations-using-the-https-proxy"></a>De integraties instellen met behulp van de HTTPS-proxy

Bij het instellen van de Defender voor IoT-en ServiceNow-integratie, communiceren de on-premises beheer console en de ServiceNow-server met behulp van poort 443. Als de ServiceNow-server zich achter de proxy bevindt, kan de standaard poort niet worden gebruikt.

Defender voor IoT ondersteunt een HTTPS-proxy in de ServiceNow-integratie door de wijziging van de standaard poort die wordt gebruikt voor de integratie in te scha kelen.

De proxy configureren:

1. Algemene eigenschappen bewerken in een on-premises beheer console:  
    `sudo vim /var/cyberx/properties/global.properties`

2. Voeg de volgende para meters toe:

    - `servicenow.http_proxy.enabled=1`

    - `servicenow.http_proxy.ip=1.179.148.9`

    - `servicenow.http_proxy.port=59125`

3. Opslaan en afsluiten.

4. Voer de volgende opdracht uit: `sudo monit restart all`

Na de configuratie worden alle ServiceNow-gegevens doorgestuurd via de geconfigureerde proxy.

## <a name="download-the-defender-for-iot-application"></a>De Defender voor IoT-toepassing downloaden

In dit artikel wordt beschreven hoe u de toepassing kunt downloaden.

Voor toegang tot de app Defender voor IoT:

1. Ga naar <https://store.servicenow.com/>

2. Zoeken naar `Defender for IoT` of `CyberX IoT/ICS Management` .

   :::image type="content" source="media/integration-servicenow/search-results.png" alt-text="Zoek naar Cyberx op de zoek balk.":::

3. Selecteer de toepassing.

   :::image type="content" source="media/integration-servicenow/cyberx-app.png" alt-text="Selecteer de toepassing in de lijst.":::

4. Selecteer **app aanvragen.**

   :::image type="content" source="media/integration-servicenow/sign-in.png" alt-text="Meld u met uw referenties aan bij de toepassing.":::

5. Meld u aan en down load de toepassing.

## <a name="view-defender-for-iot-detections-in-servicenow"></a>Defender voor IoT-detecties in ServiceNow weer geven

In dit artikel worden de kenmerken en waarschuwings gegevens voor apparaten beschreven die worden weer gegeven in ServiceNow.

Kenmerken van apparaten weer geven:

1. Meld u aan bij ServiceNow.

2. Navigeer naar **cyberx-platform**.

3. Navigeer naar **inventarisatie** of **waarschuwing**.

   [:::image type="content" source="media/integration-servicenow/alert-list.png" alt-text="Inventarisatie of waarschuwing":::](media/integration-servicenow/alert-list.png#lightbox)

## <a name="inventory-information"></a>Inventaris informatie

De configuratie beheer database (CMDB) is verrijkt en aangevuld met gegevens die door Defender voor IoT naar ServiceNow worden verzonden. Door ServiceNow toe te voegen of bij te werken in tabellen van de CMDB-configuratie-items, kunnen met Defender voor IoT de ServiceNow-werk stromen en bedrijfs regels worden geactiveerd.

De volgende informatie is beschikbaar:

- Kenmerken van apparaten, bijvoorbeeld de MAC, het besturings systeem, de leverancier of het protocol dat is gedetecteerd.

- Firmware-informatie, bijvoorbeeld de versie van de firmware en het serie nummer.

- Informatie over verbonden apparaten, bijvoorbeeld de richting van het verkeer tussen de bron en de bestemming.

### <a name="devices-attributes"></a>Kenmerken van apparaten

In dit artikel worden de kenmerken van apparaten beschreven die naar ServiceNow zijn gepusht.

| Item | Beschrijving |
|--|--|
| Apparaat | De naam van de sensor die het verkeer heeft gedetecteerd. |
| Id | De apparaat-ID die is toegewezen door Defender voor IoT. |
| Naam | De apparaatnaam. |
| IP-adres | Het IP-adres of de adressen van het apparaat. |
| Type | Het apparaattype, bijvoorbeeld een switch, PLC, historian of domein controller. |
| MAC-adres | Het MAC-adres van het apparaat of de adressen. |
| Besturingssysteem | Het besturings systeem van het apparaat. |
| Leverancier | De leverancier van het apparaat. |
| Protocollen | De protocollen die zijn gedetecteerd in het verkeer dat door het apparaat is gegenereerd. |
| Eigenaar | Voer de naam van de eigenaar van het apparaat in. |
| Locatie | Voer de fysieke locatie van het apparaat in. |

Apparaten weer geven die zijn verbonden met een apparaat in deze weer gave.

Verbonden apparaten weer geven:

1. Selecteer een apparaat en selecteer vervolgens het **toestel** dat wordt vermeld in voor dat apparaat.

    :::image type="content" source="media/integration-servicenow/appliance.png" alt-text="Selecteer het gewenste apparaat in de lijst.":::

1. Selecteer **verbonden apparaten** in het dialoog venster **Details van apparaat** .

### <a name="firmware-details"></a>Firmware Details

In dit artikel wordt beschreven hoe de firmware gegevens van het apparaat worden gepusht naar ServiceNow.

| Item | Beschrijving |
|--|--|
| Apparaat | De naam van de sensor die het verkeer heeft gedetecteerd. |
| Apparaat | De apparaatnaam. |
| Adres | Het IP-adres van het apparaat. |
| Module adres | Het model en de sleuf nummer of-ID van het apparaat. |
| Wel | Het serie nummer van het apparaat. |
| Model | Het model nummer van het apparaat. |
| Versie | Het versie nummer van de firmware. |
| Aanvullende gegevens | Meer gegevens over de firmware, zoals gedefinieerd door de leverancier, bijvoorbeeld het type apparaat. |

### <a name="connection-details"></a>Verbindingsdetails

In dit artikel worden de verbindings gegevens van het apparaat beschreven die naar ServiceNow zijn gepusht.

:::image type="content" source="media/integration-servicenow/connections.png" alt-text="De verbindings gegevens van het apparaat":::

| Item | Beschrijving |
|--|--|
| Apparaat | De naam van de sensor die het verkeer heeft gedetecteerd. |
| Richting | De richting van het verkeer. <br /> <br /> - **Een** van de manieren geeft aan dat de doel server en de bron de client zijn. <br /> <br /> - **Twee richting** betekent dat zowel de bron als de bestemming servers zijn of dat de client onbekend is. |
| Bron apparaat-ID | Het IP-adres van het apparaat dat is gecommuniceerd met het aangesloten apparaat. |
| Naam van bron apparaat | De naam van het apparaat dat met het verbonden apparaat is gecommuniceerd. |
| Doel apparaat-ID | Het IP-adres van het verbonden apparaat. |
| Naam van het doel apparaat | De naam van het verbonden apparaat. |

## <a name="alert-reporting"></a>Rapportage van waarschuwingen

Er worden waarschuwingen geactiveerd wanneer verdedigings voor IOT-engines wijzigingen in het netwerk verkeer en gedrag die uw aandacht nodig hebben, kunnen detecteren. Zie [about alert engines](#about-alert-engines)(Engelstalig) voor meer informatie over de soorten waarschuwingen die door elke engine worden gegenereerd.

In dit artikel worden de waarschuwings gegevens van het apparaat beschreven die naar ServiceNow zijn gepusht.

| Item | Beschrijving |
|--|--|
| Gemaakt | De datum en tijd waarop de waarschuwing is gegenereerd. |
| Engine | De engine die de gebeurtenis heeft gedetecteerd. |
| Titel | De titel van de waarschuwing. |
| Beschrijving | De beschrijving van de waarschuwing. |
| Protocol | Het protocol dat in het verkeer is gedetecteerd. |
| Severity | De ernst van de waarschuwing die is gedefinieerd door Defender voor IoT. |
| Apparaat | De naam van de sensor die het verkeer heeft gedetecteerd. |
| Bron naam | De naam van de bron. |
| IP-adres van bron| Het IP-adres van de bron. |
| Doel naam | De naam van het doel. |
| IP-adres van doel | Het doel-IP-adres. |
| Toegewezen gebruiker | Voer de naam in van de persoon die is toegewezen aan het ticket. |

### <a name="updating-alert-information"></a>Waarschuwings gegevens bijwerken

Selecteer de vermelding in de kolom gemaakt om waarschuwings gegevens in een formulier weer te geven. U kunt waarschuwings Details bijwerken en de waarschuwing toewijzen aan een persoon om deze te controleren en te verwerken.

[:::image type="content" source="media/integration-servicenow/alert.png" alt-text="Bekijk de informatie van de waarschuwing.":::](media/integration-servicenow/alert.png#lightbox)

### <a name="about-alert-engines"></a>Informatie over waarschuwings-engines

In dit artikel wordt het soort waarschuwingen beschreven dat elke engine activeert.

| Waarschuwingstype | Beschrijving |
|--|--|
| Waarschuwingen voor beleids schendingen | Wordt geactiveerd wanneer de engine voor beleids overtreding een afwijking detecteert van het eerder geleerde verkeer. Bijvoorbeeld: <br /><br />-Er is een nieuw apparaat gedetecteerd. <br /><br />-Er wordt een nieuwe configuratie op een apparaat gedetecteerd. <br /><br />-Een apparaat dat niet als een programmeer apparaat is gedefinieerd, voert een wijziging in het programma uit. <br /><br />-Er is een firmware versie gewijzigd. |
| Waarschuwingen over Protocol schendingen | Wordt geactiveerd wanneer de engine van de protocol overtreding een pakket structuren of veld waarden detecteert die niet voldoen aan de protocol specificatie. |
| Operationele waarschuwingen | Wordt geactiveerd wanneer de operationele engine netwerk operationele incidenten of het defecte apparaat detecteert. Een netwerk apparaat is bijvoorbeeld gestopt met een stop PLC opdracht of een interface op een sensor heeft het bewakings verkeer gestopt. |
| Waarschuwingen voor schadelijke software | Wordt geactiveerd wanneer de malware-engine schadelijke netwerk activiteit detecteert, bijvoorbeeld bekende aanvallen zoals Conficker. |
| Anomalie waarschuwingen | Wordt geactiveerd wanneer de afwijkende engine een afwijking detecteert. Een apparaat voert bijvoorbeeld netwerk scans uit, maar is niet gedefinieerd als een scan apparaat. |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [door sturen van waarschuwings gegevens](how-to-forward-alert-information-to-partners.md).
