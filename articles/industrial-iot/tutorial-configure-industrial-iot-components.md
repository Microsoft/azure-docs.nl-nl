---
title: De industriële IoT-onderdelen van Azure configureren
description: In deze zelf studie leert u hoe u de standaard waarden van de configuratie kunt wijzigen.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 348276a71b2227063374da6455445118fcb00fcf
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787685"
---
# <a name="tutorial-configure-the-industrial-iot-components"></a>Zelf studie: de industriële IoT-onderdelen configureren

Met het implementatie script worden alle onderdelen automatisch geconfigureerd om met elkaar te werken met behulp van de standaard waarden. De instellingen van de standaard waarden kunnen echter worden gewijzigd om te voldoen aan uw vereisten.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De configuratie van de onderdelen aanpassen


Hier volgen enkele van de meest relevante aanpassings instellingen voor de onderdelen:
* IoT Hub
    * Netwerken → open bare toegang: Internet toegang configureren, bijvoorbeeld IP-filters
    * Netwerken → VPN-verbindingen: een eind punt maken dat niet toegankelijk is via internet en intern kan worden gebruikt door andere Azure-Services of on-premises apparaten (bijvoorbeeld via een VPN-verbinding)
    * IoT Edge: de configuratie van de edge-apparaten beheren die zijn verbonden met de OPC UA-servers 
* Cosmos DB
    * Gegevens globaal repliceren: gegevens redundantie configureren
    * Firewall en virtuele netwerken: Internet-en VNET-toegang configureren, en IP-filters
    * Verbindingen met privé-eind punten: een eind punt maken dat niet toegankelijk is via Internet 
* Key Vault
    * Geheimen: platform instellingen beheren
    * Toegangs beleid: beheer welke toepassingen en gebruikers toegang hebben tot de gegevens in de Key Vault en welke bewerkingen (bijvoorbeeld lezen, schrijven, lijst, verwijderen) mogen worden uitgevoerd op het netwerk, de firewall, het VNET en het persoonlijke eind punt
* Azure Active Directory (AAD) → App-registraties
    * <APP_NAME>-Web → verificatie: antwoord-Uri's beheren. Dit is de lijst met Uri's die als landings pagina's kunnen worden gebruikt nadat de verificatie is geslaagd. Het implementatie script kan dit mogelijk niet automatisch configureren onder bepaalde scenario's, zoals het ontbreken van AAD-beheerders rechten. U kunt Uri's toevoegen of wijzigen bij het wijzigen van de hostnaam van de web-app, bijvoorbeeld het poort nummer dat wordt gebruikt door de localhost voor fout opsporing
* App Service
    * Configuratie: de omgevings variabelen beheren waarmee de services of gebruikers interface worden beheerd
* Virtuele machine
    * Netwerken: ondersteunde netwerken en firewall regels configureren
    * Seriële console: SSH-toegang om inzichten te verkrijgen of voor fout opsporing, de referenties op te halen uit de uitvoer van het implementatie script of het wacht woord opnieuw in te stellen
* IoT Hub → IoT Edge
    * De identiteiten van de IoT Edge apparaten beheren die toegang kunnen krijgen tot de hub, configureren welke modules worden geïnstalleerd en welke configuratie ze gebruiken, bijvoorbeeld coderings parameters voor de OPC-Uitgever
* IoT Hub → IoT Edge → \<DEVICE> set modules → OpcPublisher (alleen voor zelfstandige OPC-Uitgever bewerking)


## <a name="configuration-options"></a>Configuratie-opties

|Configuratie optie (Steno/volledige naam)    |    Beschrijving   |
|----------------------------------------------|------------------|
PF/publishfile |De bestands naam voor het configureren van de knoop punten die moeten worden gepubliceerd. Als deze optie is opgegeven, wordt de OPC-uitgever in de zelfstandige modus geplaatst.
LF/logboek |De bestands naam van het logboek bestand dat moet worden gebruikt.
ll/loglevel |Het te gebruiken logboek niveau (toegestaan: fataal, fout, waarschuwing, info, fouten opsporen, uitgebreid).
me/messageencoding |De bericht codering voor uitgaande berichten toegestane waarden: JSON, Uadp
mm-messagingmode |De berichten modus voor de toegestane waarden voor uitgaande berichten: PubSub, voor beelden
FM-fullfeaturedmessage |De volledige aanbevolen modus voor berichten (alle velden ingevuld). De standaard waarde is ' True ' voor oudere compatibiliteits gebruik ' onwaar '
AA/autoaccept |De uitgever die alle servers vertrouwt die verbinding is met
BS/BatchSize |Het aantal OPC UA-berichten voor gegevens wijziging dat in de cache moet worden opgeslagen voor batch verwerking.
si/iothubsendinterval |Het interval voor batch verwerking van de trigger in seconden.
MS/iothubmessagesize |De maximale grootte van het bericht (IoT D2C).
om/maxoutgressmessages |De maximale grootte van de buffer voor het uituitgangs bericht van (IoT D2C).
di/diagnosticsinterval |Bevat informatie over de diagnostische gegevens van de uitgever voor het opgegeven interval in seconden (informatie over het logboek niveau is vereist). -1 Hiermee schakelt u het externe diagnostische logboek en de diagnostische uitvoer uit
lt/logflugtimespan |De tijds duur in seconden voor het leegmaken van het logboek bestand.
ih/iothubprotocol |Het protocol dat moet worden gebruikt voor communicatie met de hub. Toegestane waarden: AmqpOverTcp, AmqpOverWebsocket, MqttOverTcp, MqttOverWebsocket, AMQP, Mqtt, TCP, WebSocket, any
hb/heartbeatinterval |De uitgever gebruikt deze standaard waarde in seconden voor de instelling van het heartbeat-interval van knoop punten zonder een heartbeat-interval instelling.
OT/operationtimeout |De time-out van de bewerking van de Publisher OPC UA-client in MS.
OL/opcmaxstringlen |De maximale lengte van een teken reeks OPC kan verzenden/ontvangen.
Oi/opcsamplinginterval |De standaard waarde in milliseconden voor het aanvragen van de servers aan voorbeeld waarden
op/opcpublishinginterval |De standaard waarde in milliseconden voor de publicatie-interval instelling van de abonnementen op de OPC UA-server.
CT/createsessiontimeout |Het interval waarmee de uitgever in seconden keep alive-berichten verzendt naar de OPC-servers op de eind punten waarmee deze is verbonden.
kt/keepalivethresholt |Geef het aantal Keep Alive-pakketten op dat een server kan missen voordat de sessie wordt verbroken.
TM/trustmyself |Het certificaat van de uitgever wordt automatisch in het vertrouwde archief geplaatst.
at/appcertstoretype |Het eigen certificaat archief type van de toepassing (toegestaan: Directory, X509Store).


## <a name="next-steps"></a>Volgende stappen
Nu u hebt geleerd hoe u de standaard waarden van de configuratie wijzigt, kunt u 

> [!div class="nextstepaction"]
> [IIoT-gegevens verzamelen in ADX](tutorial-industrial-iot-azure-data-explorer.md)

> [!div class="nextstepaction"]
> [Visualiseren en analyseren van de gegevens met behulp van Time Series Insights](tutorial-visualize-data-time-series-insights.md)
