---
title: Over de Splunk-integratie
titleSuffix: Azure Defender for IoT
description: Om een gebrek aan zicht baarheid van de beveiliging en tolerantie van de netwerken te verhelpen, hebben Defender voor IoT de Defender voor IoT-, IIoT-en ICS-toepassing voor risico bewaking ontwikkeld voor Splunk, een systeem eigen integratie tussen Defender voor IoT en Splunk waarmee een uniforme benadering van de IT-mede werkers kan worden beveiligd.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/4/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 91d877d644b4b5ca7231f5f81f9163a0fd3cbe25
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98557985"
---
# <a name="defender-for-iot-and-ics-threat-monitoring-application-for-splunk"></a>Defender voor IoT-en ICS-toepassing voor risico bewaking voor Splunk

Met Defender voor IoT worden IIoT-, ICS-en SCADA-Risico's beperkt met gepatenteerde, met Internet betrouw bare zelf studie-engines die onmiddellijk inzicht geven in de ICS-apparaten, beveiligings lekken en bedreigingen in minder dan een image-uur en zonder dat ze gebruikmaken van agents, regels of hand tekeningen, gespecialiseerde vaardig heden of voorafgaande kennis van de omgeving.

Om een gebrek aan zicht baarheid van de beveiliging en tolerantie van de netwerken te verhelpen, hebben Defender voor IoT de Defender voor IoT-, IIoT-en ICS-toepassing voor risico bewaking ontwikkeld voor Splunk, een systeem eigen integratie tussen Defender voor IoT en Splunk waarmee een uniforme benadering van de IT-mede werkers kan worden beveiligd.

> [!Note]
> Verwijzingen naar Cyberx verwijzen naar Azure Defender voor IoT.

## <a name="about-the-splunk-application"></a>Over de Splunk-toepassing

De toepassing biedt SOC analisten met multidimensionale zicht baarheid in de gespecialiseerde protocollen en IIoT-apparaten die in industriële omgevingen worden geïmplementeerd, samen met gedrags analyse met ICS-functionaliteit om snel verdacht of afwijkend gedrag te detecteren. De toepassing maakt ook zowel de reactie van de incidenten als van de respons van een zakelijke SOC mogelijk. Dit is een belang rijke evolutie van de voortdurende convergentie van IT en het voor de ondersteuning van nieuwe IIoT-initiatieven, zoals Smart machines en real-time intelligence.

Splunk-toepassing kan lokaal worden geïnstalleerd of worden uitgevoerd in een Cloud. De integratie met Defender voor IoT ondersteunt beide implementaties.

## <a name="about-the-integration"></a>Over de integratie

Dankzij de integratie van Defender voor IoT en Splunk via de systeem eigen toepassing kunnen gebruikers het volgende doen:

- Verminder de tijd die nodig is voor organisaties van industriële en kritieke infra structuur om Cyber bedreigingen te detecteren, te onderzoeken en te handelen.

- Ontvang real-time informatie over Risico's.

- Correleer Defender voor IoT-waarschuwingen met Splunk Enter prise Security Threat Intelligence-opslag plaatsen.

- Bewaak en reageer op een enkel deel venster van het glas.

[:::image type="content" source="media/integration-splunk/splunk-mainpage-v2.png" alt-text="Hoofd pagina van het hulp programma Splunk.":::](media/integration-splunk/splunk-mainpage-v2.png#lightbox)

:::image type="content" source="media/integration-splunk/alerts.png" alt-text="De pagina waarschuwingen in Splunk.":::

Met de toepassing kunnen Splunk-beheerders waarschuwingen die door Defender voor IoT worden verzonden, analyseren en de volledige beveiliging van beveiligings implementaties controleren, inclusief gegevens zoals:

- Welk van de vijf analyse-engines heeft de waarschuwing gedetecteerd.

- Welk protocol de waarschuwing heeft gegenereerd.

- De waarschuwing die is gegenereerd door Defender voor de IoT-sensor.

- Het Ernst niveau van de waarschuwing.

- De bron en het doel van de communicatie.

## <a name="requirements"></a>Vereisten

### <a name="version-requirements"></a>Versie vereisten

De volgende versies zijn vereisten.

- Defender voor IoT versie 2,4 en hoger.

- Splunkbase-versie 11 en hoger.

- Splunk Enter prise-versie 7,2 en hoger.
  
## <a name="download-the-application"></a>De toepassing downloaden

Down load de *Splunk-toepassing voor het controleren van Cyber-ICS-toepassingen* in de [Splunkbase](https://splunkbase.splunk.com/app/4313/).

## <a name="splunk-permission-requirements"></a>Splunk-machtigings vereisten

De volgende Splunk-machtiging is vereist:

- Een gebruiker met machtigingen voor de gebruikersrol *beheerder* .

## <a name="send-defender-for-iot-alerts-to-splunk"></a>Defender voor IoT-waarschuwingen verzenden naar Splunk

Defender voor IoT-waarschuwingen biedt informatie over een uitgebreid scala aan beveiligings gebeurtenissen, waaronder:

- Afwijkingen van geleerde basislijn netwerk activiteit.

- Detectie van malware.

- Detecties op basis van verdachte operationele wijzigingen.

- Afwijkingen van het netwerk.

- Protocol afwijkingen van Protocol specificaties.

:::image type="content" source="media/integration-splunk/address-scan.png" alt-text="Het scherm detecties.":::

U kunt Defender voor IoT zo configureren dat waarschuwingen worden verzonden naar de Splunk-server, waar waarschuwings gegevens worden weer gegeven in het Splunk Enter prise-dash board.

:::image type="content" source="media/integration-splunk/alerts-and-details.png" alt-text="Alle waarschuwingen en de bijbehorende gegevens weer geven.":::

De volgende waarschuwings gegevens worden verzonden naar de Splunk-server.

- De datum en tijd van de waarschuwing.

- De Defender voor IoT-engine die de gebeurtenis heeft gedetecteerd: schending van het Protocol, schending van het beleid, malware, anomalie of operationele engine.

- De titel van de waarschuwing.

- Het waarschuwings bericht.

- De ernst van de waarschuwing: waarschuwing, secundair, primair of kritiek.

- De naam van het bron apparaat.

- Het IP-adres van het bron apparaat.

- De naam van het doel apparaat.

- Het IP-adres van het doel apparaat.

- De Defender voor IoT-platform-IP-adres (host).

- De naam van de Defender voor IoT-platform apparaat (bron type).

Voorbeeld uitvoer wordt hieronder weer gegeven:

| Tijd | Gebeurtenis |
|--|--|
| 7/23/15<br />9:28:31.000 PM | **Waarschuwing Defender voor IOT-platform**: een apparaat is gestopt door een PLC-opdracht<br /><br />**Type**: operationele schending <br /><br />**Ernst**: primair <br /><br />**Bron naam**: my_device1 <br /><br />**Bron-IP**: 192.168.110.131 <br /><br />**Doel naam**: my_device2<br /><br /> **Doel-IP**: 10.140.33.238 <br /><br />**Bericht**: een netwerk apparaat is gestopt met een stop PLC-opdracht. Dit apparaat werkt pas nadat een start opdracht is verzonden. 192.168.110.131 is gestopt door 10.140.33.238 (een Siemens S7-apparaat) met behulp van een PLC-stop opdracht.<br /><br />**Host**: 192.168.90.43 <br /><br />**Source type**: Sensor_Agent |

## <a name="define-alert-forwarding-rules"></a>Regels voor het door sturen van waarschuwingen definiëren

Gebruik Defender voor IoT- *doorstuur regels* voor het verzenden van waarschuwings gegevens naar Splunk-servers.

Er zijn opties beschikbaar om de waarschuwings regels aan te passen op basis van:

- Specifieke protocollen gedetecteerd.

- De ernst van de gebeurtenis.

- Defender voor IoT-engine die gebeurtenissen detecteert.

Een doorstuur regel maken:

1. Selecteer **door sturen** in het linkerdeel venster van de sensor of on-premises beheer console.

    :::image type="content" source="media/integration-splunk/forwarding.png" alt-text="Selecteer de knop voor het door sturen van waarschuwingen maken.":::

1. Selecteer **regels voor door sturen maken**. In het venster **doorstuur regel maken** definieert u de regel parameters.

    :::image type="content" source="media/integration-splunk/forwarding-rule.png" alt-text="Maak de regels voor uw doorstuur regel.":::

    | Parameter | Beschrijving |
    |--|--|
    | **Naam** | De naam van de doorstuur regel. |
    | **Ernst selecteren** | Het minimale beveiligings niveau incident dat moet worden doorgestuurd. Als bijvoorbeeld secundair is geselecteerd, worden kleine waarschuwingen en waarschuwingen boven dit niveau van de ernst doorgestuurd. |
    | **Protocollen** | Standaard zijn alle protocollen geselecteerd. Als u een specifiek protocol wilt selecteren, selecteert u **specifiek** en selecteert u het protocol waarvoor deze regel wordt toegepast. |
    | **Zoekprogramma's** | Standaard zijn alle beveiligings engines betrokken. Als u een specifieke beveiligings Engine wilt selecteren waarvoor deze regel wordt toegepast, selecteert u **specifiek** en selecteert u de engine. |
    | **Systeem meldingen** | Online/offline-status van sensor door sturen. Deze optie is alleen beschikbaar als u bent aangemeld bij de centrale Manager. |                                            |

1. Om ervoor te zorgen dat Defender voor IoT informatie over de activa naar Splunk verzendt, selecteert u **actie** en selecteert **u vervolgens verzenden naar Splunk server**.

1. Voer de volgende Splunk-para meters in.

    :::image type="content" source="media/integration-splunk/parameters.png" alt-text="De Splunk-para meters die u moet invoeren op dit scherm.":::

    | Parameter | Beschrijving |
    |--|--|
    | **Host** | Splunk-server adres |
    | **Poort** | 8089 |
    | **Gebruikersnaam** | Gebruikers naam Splunk-server |
    | **Wachtwoord** | Splunk-server wachtwoord |

1. Selecteer **verzenden**

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [door sturen van waarschuwings gegevens](how-to-forward-alert-information-to-partners.md).
