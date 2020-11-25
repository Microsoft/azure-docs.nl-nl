---
title: Verbindings problemen oplossen-Azure Event Hubs | Microsoft Docs
description: Dit artikel bevat informatie over het oplossen van verbindings problemen met Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 8eddc0e8c598e4553b30759d179fecb6ae880829
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "96012677"
---
# <a name="troubleshoot-connectivity-issues---azure-event-hubs"></a>Verbindings problemen oplossen-Azure Event Hubs
Er zijn verschillende redenen waarom client toepassingen geen verbinding kunnen maken met een Event Hub. De verbindings problemen die u ondervindt, kunnen permanent of tijdelijk zijn. Als het probleem voortdurend optreedt (permanent), wilt u mogelijk de connection string controleren, de firewall instellingen van uw organisatie, de instellingen van de IP-firewall, de instellingen voor netwerk beveiliging (Service-eind punten, persoonlijke eind punten, enzovoort). Voor tijdelijke problemen voert u een upgrade uit naar de nieuwste versie van de SDK, voert u opdrachten uit om verwijderde pakketten te controleren en netwerk traceringen te verkrijgen, kan u helpen bij het oplossen van de problemen. 

In dit artikel vindt u tips voor het oplossen van verbindings problemen met Azure Event Hubs. 

## <a name="troubleshoot-permanent-connectivity-issues"></a>Problemen met permanente connectiviteit oplossen
Als de toepassing helemaal geen verbinding kan maken met de Event Hub, volgt u de stappen in deze sectie om het probleem op te lossen. 

### <a name="check-if-there-is-a-service-outage"></a>Controleren of er sprake is van een service storing
Controleer de onderbreking van de Azure Event Hubs-service op de site van de [Azure-service status](https://azure.microsoft.com/status/).

### <a name="verify-the-connection-string"></a>Controleer de connection string 
Controleer of de connection string die u gebruikt juist is. Zie [Connection String ophalen](event-hubs-get-connection-string.md) om de Connection String te krijgen met behulp van de Azure Portal, CLI of Power shell. 

Controleer voor Kafka-clients of producer.config-of consumer.config-bestanden juist zijn geconfigureerd. Zie [berichten verzenden en ontvangen met Kafka in Event hubs](event-hubs-quickstart-kafka-enabled-event-hubs.md#send-and-receive-messages-with-kafka-in-event-hubs)voor meer informatie.

[!INCLUDE [event-hubs-connectivity](../../includes/event-hubs-connectivity.md)]

### <a name="verify-that-azureeventgrid-service-tag-is-allowed-in-your-network-security-groups"></a>Controleer of het AzureEventGrid-service label is toegestaan in uw netwerk beveiligings groepen
Als uw toepassing wordt uitgevoerd in een subnet en er een netwerk beveiligings groep is gekoppeld, moet u controleren of het Internet uitgaand is toegestaan of dat het AzureEventGrid-service label is toegestaan. Zie [service tags voor het virtuele netwerk](../virtual-network/service-tags-overview.md) en zoeken naar `EventHub` .

### <a name="check-if-the-application-needs-to-be-running-in-a-specific-subnet-of-a-vnet"></a>Controleren of de toepassing moet worden uitgevoerd in een specifiek subnet van een vnet
Controleer of de toepassing wordt uitgevoerd in een subnet van een virtueel netwerk dat toegang heeft tot de naam ruimte. Als dat niet het geval is, voert u de toepassing uit in het subnet dat toegang tot de naam ruimte heeft of voegt u het IP-adres toe van de computer waarop de toepassing wordt uitgevoerd voor de [IP-firewall](event-hubs-ip-filtering.md). 

Wanneer u een service-eind punt voor een virtueel netwerk maakt voor een Event Hub naam ruimte, accepteert de naam ruimte alleen verkeer van het subnet dat is gekoppeld aan het service-eind punt. Er is een uitzonde ring op dit gedrag. U kunt specifieke IP-adressen toevoegen aan de IP-firewall om toegang tot het open bare eind punt van Event hub mogelijk te maken. Zie [netwerk service-eind punten](event-hubs-service-endpoints.md)voor meer informatie.

### <a name="check-the-ip-firewall-settings-for-your-namespace"></a>Controleer de IP-Firewall instellingen voor uw naam ruimte
Controleer of het open bare IP-adres van de computer waarop de toepassing wordt uitgevoerd, niet wordt geblokkeerd door de IP-firewall.  

Event Hubs naam ruimten zijn standaard toegankelijk vanuit Internet zolang de aanvraag een geldige verificatie en autorisatie heeft. Met IP-firewall kunt u dit nog verder beperken tot een aantal IPv4-adressen of IPv4-adresbereiken in CIDR-notatie [(klasseloze Inter-Domain route ring)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

De IP-firewall regels worden toegepast op het niveau van de Event Hubs naam ruimte. Daarom gelden de regels voor alle verbindingen van clients die gebruikmaken van elk ondersteund protocol. Een verbindings poging van een IP-adres dat niet overeenkomt met een toegestane IP-regel op de Event Hubs naam ruimte, wordt geweigerd als niet-geautoriseerd. De IP-regel wordt niet vermeld in het antwoord. IP-filter regels worden in volg orde toegepast en de eerste regel die overeenkomt met het IP-adres, bepaalt de accepteren of afwijzen.

Zie [IP-firewall regels voor een Azure Event hubs-naam ruimte configureren](event-hubs-ip-filtering.md)voor meer informatie. Zie [problemen met het netwerk oplossen](#troubleshoot-network-related-issues)om te controleren of er problemen zijn met de IP-filtering, het virtuele netwerk of de certificaat keten.

### <a name="check-if-the-namespace-can-be-accessed-using-only-a-private-endpoint"></a>Controleren of de naam ruimte kan worden geopend met alleen een persoonlijk eind punt
Als de naam ruimte van de Event Hubs zodanig is geconfigureerd dat deze alleen toegankelijk is via een particulier eind punt, controleert u of de client toepassing toegang heeft tot de naam ruimte via het persoonlijke eind punt. 

Met [Azure Private Link service](../private-link/private-link-overview.md) kunt u toegang krijgen tot Azure Event hubs via een **persoonlijk eind punt** in uw virtuele netwerk. Een persoonlijk eind punt is een netwerk interface waarmee u privé en veilig kunt verbinden met een service die wordt aangestuurd door een persoonlijke Azure-koppeling. Het persoonlijke eind punt maakt gebruik van een privé-IP-adres van uw virtuele netwerk, waardoor de service in het virtuele netwerk effectief wordt. Al het verkeer naar de service kan worden gerouteerd via het privé-eindpunt, zodat er geen gateways, NAT-apparaten, ExpressRoute of VPN-verbindingen of openbare IP-adressen nodig zijn. Verkeer tussen uw virtuele netwerk en de services wordt via het backbonenetwerk van Microsoft geleid, waarmee de risico's van het openbare internet worden vermeden. U kunt verbinding maken met een exemplaar van een Azure-resource, zodat u het hoogste granulariteit krijgt in toegangsbeheer.

Zie [Private-eind punten configureren](private-link-service.md)voor meer informatie. Zie de sectie controleren **of de verbinding met het persoonlijke eind punt werkt** om te bevestigen dat een persoonlijk eind punt wordt gebruikt. 

### <a name="troubleshoot-network-related-issues"></a>Problemen met het netwerk oplossen
Voer de volgende stappen uit om problemen met het netwerk op te lossen met Event Hubs: 

Blader naar of [wget](https://www.gnu.org/software/wget/) `https://<yournamespacename>.servicebus.windows.net/` . Het helpt te controleren of er problemen zijn met de IP-filtering of het virtuele netwerk of de certificaat keten (het meest gebruikelijk wanneer u Java SDK gebruikt).

Een voor beeld van een **geslaagd bericht**:

```xml
<feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
```

Een voor beeld van een fout **bericht**:

```json
<Error>
    <Code>400</Code>
    <Detail>
        Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
    </Detail>
</Error>
```

## <a name="troubleshoot-transient-connectivity-issues"></a>Problemen met de tijdelijke verbinding oplossen
Als u problemen ondervindt met de verbinding, gaat u naar de volgende secties voor tips voor probleem oplossing. 

### <a name="use-the-latest-version-of-the-client-sdk"></a>De nieuwste versie van de client-SDK gebruiken
Enkele van de problemen met de tijdelijke verbinding zijn mogelijk opgelost in de nieuwere versies van de SDK dan wat u gebruikt. Zorg ervoor dat u de nieuwste versie van de client-Sdk's in uw toepassingen gebruikt. Sdk's worden continu verbeterd met nieuwe/bijgewerkte functies en probleem oplossingen, zodat u altijd met het nieuwste pakket kunt testen. Controleer de opmerkingen bij de release voor problemen die zijn opgelost en de functies zijn toegevoegd/bijgewerkt. 

Zie het artikel over de [Azure Event hubs-client-sdk's](sdks.md) voor meer informatie over client-sdk's. 

### <a name="run-the-command-to-check-dropped-packets"></a>Voer de opdracht uit om verwijderde pakketten te controleren
Als er onregelmatige verbindings problemen zijn, voert u de volgende opdracht uit om te controleren of er verloren pakketten zijn. Met deze opdracht wordt geprobeerd 25 verschillende TCP-verbindingen elke 1 seconde te maken met de service. Vervolgens kunt u controleren of het aantal geslaagde/mislukte en ook TCP-verbindings latentie wordt weer geven. U kunt het `psping` hulp programma [hier](/sysinternals/downloads/psping)downloaden.

```shell
.\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
```
U kunt gelijkwaardige opdrachten gebruiken als u andere hulp middelen gebruikt, zoals `tnc` , `ping` , enzovoort. 

Verkrijg een netwerk tracering als de vorige stappen niet helpen en analyseren met behulp van hulpprogram ma's zoals [wireshark](https://www.wireshark.org/). Neem zo nodig contact op met [Microsoft ondersteuning](https://support.microsoft.com/) . 

### <a name="service-upgradesrestarts"></a>Service-upgrades/opnieuw opstarten
Problemen met de tijdelijke verbinding kunnen zich voordoen als de back-end-service is bijgewerkt en opnieuw wordt opgestart. Wanneer deze zich voordoen, ziet u mogelijk de volgende symptomen: 

- Er is mogelijk een neerzetten in inkomende berichten/aanvragen.
- Het logboek bestand kan fout berichten bevatten.
- De verbinding van de toepassingen kan enkele seconden worden verbroken met de service.
- Aanvragen kunnen tijdelijk worden beperkt.

Als de toepassings code gebruikmaakt van SDK, is het beleid voor opnieuw proberen al ingebouwd en actief. De toepassing zal opnieuw verbinding maken zonder belang rijke gevolgen voor de toepassing/werk stroom. Door deze tijdelijke fouten op te lossen en vervolgens opnieuw te proberen, zorgt u ervoor dat uw code bestand is tegen deze tijdelijke problemen.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

* [Verificatie- en autorisatieproblemen oplossen](troubleshoot-authentication-authorization.md)
