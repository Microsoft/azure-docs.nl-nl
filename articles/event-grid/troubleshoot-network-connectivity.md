---
title: Problemen met de netwerk verbinding oplossen-Azure Event Grid | Microsoft Docs
description: In dit artikel vindt u informatie over het oplossen van problemen met netwerk verbindingen met Azure Event Grid.
author: batrived
ms.topic: article
ms.date: 06/21/2020
ms.author: batrived
ms.openlocfilehash: fa119784715b8c88ef3c9f2700b2cac1cc467234
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96339742"
---
# <a name="troubleshoot-connectivity-issues---azure-event-grid"></a>Verbindings problemen oplossen-Azure Event Grid

Er zijn verschillende redenen waarom client toepassingen geen verbinding kunnen maken met een Event Grid onderwerp/domein. De verbindings problemen die u ondervindt, kunnen permanent of tijdelijk zijn. Als het probleem voortdurend optreedt (permanent), wilt u mogelijk de firewall instellingen van uw organisatie controleren, de IP-Firewall instellingen, service tags, persoonlijke eind punten en meer. Voor tijdelijke problemen kan het uitvoeren van opdrachten voor het controleren van verwijderde pakketten en het verkrijgen van netwerk traceringen helpen bij het oplossen van de problemen.

In dit artikel vindt u tips voor het oplossen van verbindings problemen met Azure Event Grid.

## <a name="troubleshoot-permanent-connectivity-issues"></a>Problemen met permanente connectiviteit oplossen

Als de toepassing helemaal geen verbinding kan maken met het event grid, volgt u de stappen in deze sectie om het probleem op te lossen.

### <a name="check-if-theres-a-service-outage"></a>Controleren of er sprake is van een service storing

Controleer op de site van de Azure- [service status](https://azure.microsoft.com/status/)de Azure Event grid service-onderbreking.

### <a name="check-if-the-ports-required-to-communicate-with-event-grid-arent-blocked-by-organizations-firewall"></a>Controleren of de poorten die nodig zijn om te communiceren met Event Grid niet worden geblokkeerd door de firewall van de organisatie

Controleer of de poorten die worden gebruikt bij het communiceren met Azure Event Grid niet worden geblokkeerd op de firewall van uw organisatie. Zie de volgende tabel voor de uitgaande poorten die u moet openen om te communiceren met Azure Event Grid.

| Protocol | Poorten |
| -------- | ----- |
| HTTPS    | 443   |

Hier volgt een voor beeld van een opdracht waarmee wordt gecontroleerd of de 443-poort is geblokkeerd.

```powershell
.\psping.exe -n 25 -i 1 -q {sampletopicname}.{region}-{suffix}.eventgrid.azure.net:443 -nobanner
```

Op Linux:

```shell
telnet {sampletopicname}.{region}-{suffix}.eventgrid.azure.net 443
```

### <a name="verify-that-ip-addresses-are-allowed-in-your-corporate-firewall"></a>Controleren of IP-adressen zijn toegestaan in uw bedrijfs firewall

Wanneer u met Azure werkt, moet u soms specifieke IP-adresbereiken of Url's in uw bedrijfs firewall of-proxy toestaan om toegang te krijgen tot alle Azure-Services die u gebruikt of probeert te gebruiken. Controleer of het verkeer is toegestaan op IP-adressen die worden gebruikt door Event Grid. Voor IP-adressen die worden gebruikt door Azure Event Grid: Zie [Azure IP-adresbereiken en service Tags-Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519) en [service tag-AzureEventGrid](network-security.md#service-tags).

In het [Azure IP-bereik en de service Tags-open bare Cloud](https://www.microsoft.com/download/details.aspx?id=56519) document worden ook IP-adressen **per regio** vermeld. U kunt adresbereiken voor de **regio van het onderwerp** en de **gekoppelde regio** in uw bedrijfs firewall of proxy toestaan. Zie [bedrijfs continuïteit en herstel na nood gevallen (BCDR): Azure-gekoppelde regio's](../best-practices-availability-paired-regions.md)voor een gepaarde regio voor een regio. 

> [!NOTE]
> Er kunnen nieuwe IP-adressen worden toegevoegd aan de AzureEventGrid-servicetag, maar dit is niet gebruikelijk. Daarom is het een goed idee om de service Tags per week te controleren.

### <a name="verify-that-azureeventgrid-service-tag-is-allowed-in-your-network-security-groups"></a>Controleer of het AzureEventGrid-service label is toegestaan in uw netwerk beveiligings groepen

Als uw toepassing binnen een subnet wordt uitgevoerd en als er een netwerk beveiligings groep is gekoppeld, moet u controleren of Internet-uitgaand is toegestaan of dat het AzureEventGrid-service label is toegestaan. Zie [service Tags](../virtual-network/service-tags-overview.md)

### <a name="check-the-ip-firewall-settings-for-your-topicdomain"></a>Controleer de IP-Firewall instellingen voor uw onderwerp/domein

Controleer of het open bare IP-adres van de computer waarop de toepassing wordt uitgevoerd, niet wordt geblokkeerd door de EventGrid-onderwerp/domein-IP-firewall.

Event Grid onderwerpen/-domeinen zijn standaard toegankelijk vanuit Internet zolang de aanvraag een geldige verificatie en autorisatie heeft. Met IP-firewall kunt u dit nog verder beperken tot een aantal IPv4-adressen of IPv4-adresbereiken in CIDR-notatie [(klasseloze Inter-Domain route ring)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

De IP-firewall regels worden toegepast op het Event Grid onderwerp/domein niveau. Daarom gelden de regels voor alle verbindingen van clients die gebruikmaken van elk ondersteund protocol. Een verbindings poging van een IP-adres dat niet overeenkomt met een toegestane IP-regel op het Event Grid onderwerp/het domein, wordt geweigerd als verboden. In het antwoord wordt de IP-regel niet vermeld.

Zie [IP-firewall regels configureren voor een Azure Event grid onderwerp/domein](configure-firewall.md)voor meer informatie.

#### <a name="find-the-ip-addresses-blocked-by-ip-firewall"></a>De IP-adressen zoeken die door IP-firewall worden geblokkeerd

Diagnostische logboeken inschakelen voor Event Grid onderwerp/domein [Diagnostische logboeken inschakelen](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-a-custom-topic). U ziet het IP-adres voor de verbinding die wordt geweigerd.

```json
{
  "time": "2019-11-01T00:17:13.4389048Z",
  "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME",
  "category": "PublishFailures",
  "operationName": "Post",
  "message": "inputEventsCount=null, requestUri=https://SAMPLE-TOPIC-NAME.region-suffix.eventgrid.azure.net/api/events, publisherInfo=PublisherInfo(category=User, inputSchema=EventGridEvent, armResourceId=/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME), httpStatusCode=Forbidden, errorType=ClientIPRejected, errorMessage=Publishing to SAMPLE-TOPIC-NAME.{region}-{suffix}.EVENTGRID.AZURE.NET by client {clientIp} is rejected due to IpAddress filtering rules."
}
```

### <a name="check-if-the-eventgrid-topicdomain-can-be-accessed-using-only-a-private-endpoint"></a>Controleren of de EventGrid-onderwerp/het-domein toegankelijk is met alleen een persoonlijk eind punt

Als het onderwerp of het domein van de Event Grid zo is geconfigureerd dat het alleen toegankelijk is via een particulier eind punt, controleert u of de client toepassing toegang heeft tot het onderwerp/domein via het persoonlijke eind punt. Als u dit wilt bevestigen, controleert u of de client toepassing wordt uitgevoerd in een subnet en dat er een persoonlijk eind punt is voor Event Grid onderwerp/domein in dat subnet.

Met de [service voor persoonlijke koppelingen van Azure](../private-link/private-link-overview.md) kunt u toegang krijgen tot Azure Event grid via een **persoonlijk eind punt** in uw virtuele netwerk. Een persoonlijk eind punt is een netwerk interface waarmee u privé en veilig kunt verbinden met een service die wordt aangestuurd door een persoonlijke Azure-koppeling. Het privé-eindpunt maakt gebruik van een privé-IP-adres van uw VNet, waardoor de service feitelijk in uw VNet wordt geplaatst. Al het verkeer naar de service kan worden gerouteerd via het privé-eindpunt, zodat er geen gateways, NAT-apparaten, ExpressRoute of VPN-verbindingen of openbare IP-adressen nodig zijn. Verkeer tussen uw virtuele netwerk en de services wordt via het backbonenetwerk van Microsoft geleid, waarmee de risico's van het openbare internet worden vermeden. U kunt verbinding maken met een exemplaar van een Azure-resource, zodat u het hoogste granulariteit krijgt in toegangsbeheer.

Zie [Private-eind punten configureren](configure-private-endpoints.md)voor meer informatie.

## <a name="troubleshoot-transient-connectivity-issues"></a>Problemen met de tijdelijke verbinding oplossen

Als u problemen ondervindt met de connectiviteit, raadpleegt u de volgende secties voor probleemoplossings tips.

### <a name="run-the-command-to-check-dropped-packets"></a>Voer de opdracht uit om verwijderde pakketten te controleren

Als er onregelmatige verbindings problemen zijn, voert u de volgende opdracht uit om te controleren of er verloren pakketten zijn. Met deze opdracht wordt geprobeerd 25 verschillende TCP-verbindingen elke 1 seconde te maken met de service. Vervolgens kunt u controleren of het aantal geslaagde/mislukte en ook TCP-verbindings latentie wordt weer geven. U kunt het `psping` hulp programma [hier](/sysinternals/downloads/psping)downloaden.

```shell
.\psping.exe -n 25 -i 1 -q {sampletopicname}.{region}-{suffix}.eventgrid.azure.net:443 -nobanner
```

U kunt gelijkwaardige opdrachten gebruiken als u andere hulp middelen gebruikt, zoals `tcpping` [tcpping.exe](https://www.elifulkerson.com/projects/tcping.php).

Verkrijg een netwerk tracering als de vorige stappen niet helpen en analyseren met behulp van hulpprogram ma's zoals [wireshark](https://www.wireshark.org/). Neem zo nodig contact op met [Microsoft ondersteuning](https://support.microsoft.com/) .

### <a name="service-upgradesrestarts"></a>Service-upgrades/opnieuw opstarten

Problemen met de tijdelijke verbinding kunnen zich voordoen als de back-end-service is bijgewerkt en opnieuw wordt opgestart. Wanneer deze zich voordoen, ziet u mogelijk de volgende symptomen:

- Er is mogelijk een neerzetten in inkomende berichten/aanvragen.
- Het logboek bestand kan fout berichten bevatten.
- De verbinding van de toepassingen kan enkele seconden worden verbroken met de service.
- Aanvragen kunnen tijdelijk worden beperkt.

Door deze tijdelijke fouten op te lossen en vervolgens opnieuw te proberen, zorgt u ervoor dat uw code bestand is tegen deze tijdelijke problemen.

## <a name="next-steps"></a>Volgende stappen

Als u meer hulp nodig hebt, kunt u uw probleem in het [stack overflow forum](https://stackoverflow.com/questions/tagged/azure-eventgrid) plaatsen of een [ondersteunings ticket](https://azure.microsoft.com/support/options/)openen.