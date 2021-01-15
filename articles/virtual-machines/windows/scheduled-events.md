---
title: Scheduled Events voor virtuele Windows-machines in azure
description: Geplande gebeurtenissen met de Azure-Metadata Service voor uw virtuele Windows-machines.
author: EricRadzikowskiMSFT
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/01/2020
ms.author: ericrad
ms.reviwer: mimckitt
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e4b5248ecb47c9456836aa9c4d7ebb2ad122c1dd
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98231868"
---
# <a name="azure-metadata-service-scheduled-events-for-windows-vms"></a>Azure Metadata Service: Scheduled Events voor Windows-Vm's

Scheduled Events is een Azure-Metadata Service die uw toepassings tijd biedt voor het voorbereiden van onderhoud van virtuele machines (VM). Het bevat informatie over aanstaande onderhouds gebeurtenissen (bijvoorbeeld opnieuw opstarten), zodat uw toepassing deze kan voorbereiden en de onderbreking kan beperken. Het is beschikbaar voor alle Azure-Virtual Machines typen, waaronder PaaS en IaaS in Windows en Linux. 

Zie [Scheduled Events voor Linux-vm's](../linux/scheduled-events.md)voor meer informatie over Scheduled Events in Linux.

> [!Note] 
> Scheduled Events is algemeen beschikbaar in alle Azure-regio's. Zie de [Beschik baarheid van versie en regio](#version-and-region-availability) voor de meest recente release-informatie.

## <a name="why-use-scheduled-events"></a>Waarom Scheduled Events gebruiken?

Veel toepassingen kunnen profiteren van de tijd die is voor bereiding op het onderhoud van de virtuele machine. De tijd kan worden gebruikt voor het uitvoeren van toepassingsspecifieke taken die de beschik baarheid, betrouw baarheid en service verbeteren, waaronder: 

- Controle punt en herstellen.
- Verbinding verbreken.
- Primaire replica-failover.
- Verwijderen uit een load balancer groep.
- Gebeurtenis logboek registratie.
- Correct afgesloten.

Met Scheduled Events kan uw toepassing detecteren wanneer er onderhoud plaatsvindt en taken activeren om de impact te beperken.  

Scheduled Events bevat gebeurtenissen in de volgende use-cases:

- Door het [platform geïnitieerd onderhoud](../maintenance-and-updates.md?bc=/azure/virtual-machines/windows/breadcrumb/toc.json&toc=/azure/virtual-machines/windows/toc.json) (bijvoorbeeld VM opnieuw opstarten, Livemigratie of geheugen behoud van updates voor host)
- De virtuele machine wordt uitgevoerd op hardware die is [gedegradeerd als host](https://azure.microsoft.com/blog/find-out-when-your-virtual-machine-hardware-is-degraded-with-scheduled-events) die binnenkort wordt voorspeld
- Door de gebruiker geïnitieerd onderhoud (bijvoorbeeld: een gebruiker start een virtuele machine opnieuw of implementeert deze)
- [Spot-VM](../spot-vms.md) en verwijderingen van exemplaren van de [schaalset](../../virtual-machine-scale-sets/use-spot.md) .

## <a name="the-basics"></a>De basisbeginselen  

  Metadata Service geeft informatie over het uitvoeren van Vm's met behulp van een REST-eind punt dat toegankelijk is vanuit de VM. De informatie is beschikbaar via een nonroutable IP-adres, zodat het niet buiten de virtuele machine wordt weer gegeven.

### <a name="scope"></a>Bereik
Geplande gebeurtenissen worden verzonden naar:

- Zelfstandige Virtual Machines.
- Alle virtuele machines in een Cloud service.
- Alle virtuele machines in een beschikbaarheidsset.
- Alle virtuele machines in een beschikbaarheids zone.
- Alle virtuele machines in een plaatsings groep met schaal sets. 

> [!NOTE]
> Scheduled Events voor alle virtuele machines (Vm's) in een FC-Tenant (Fabric controller) worden aan alle Vm's in een FC-Tenant geleverd. FC-Tenant is gelijk aan een zelfstandige virtuele machine, een volledige Cloud service, een volledige Beschikbaarheidsset en een plaatsings groep voor een VM-Schaalset (VMSS), ongeacht het gebruik van de beschikbaarheids zone. 

Controleer daarom het `Resources` veld in de gebeurtenis om te bepalen welke vm's worden beïnvloed.

### <a name="endpoint-discovery"></a>Eindpunt detectie
Voor VNET-ingeschakelde Vm's is Metadata Service verkrijgbaar via een statisch nonroutable IP-adres `169.254.169.254` . Het volledige eind punt voor de meest recente versie van Scheduled Events is: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01`

Als de virtuele machine niet is gemaakt binnen een Virtual Network, zijn de standaard aanvragen voor Cloud Services en klassieke Vm's vereist om het te gebruiken IP-adres te detecteren. Zie dit voor beeld voor meer informatie over [het detecteren van het eind punt van de host](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="version-and-region-availability"></a>Beschik baarheid van versie en regio
Er is een versie van de Scheduled Events-service. Versies zijn verplicht. de huidige versie is `2019-01-01` .

| Versie | Release type | Regio's | Releaseopmerkingen | 
| - | - | - | - | 
| 2019-08-01 | Algemene Beschik baarheid | Alles | <li> Ondersteuning toegevoegd voor Event source |
| 2019-04-01 | Algemene Beschik baarheid | Alles | <li> Er is ondersteuning toegevoegd voor de beschrijving van de gebeurtenis |
| 2019-01-01 | Algemene Beschik baarheid | Alles | <li> Er is ondersteuning toegevoegd voor de gebeurtenis ' Terminate ' voor de virtuele-machine schaal sets |
| 2017-11-01 | Algemene Beschik baarheid | Alles | <li> Er is ondersteuning toegevoegd voor de gebeurtenis ' preempt ' voor VM-verwijdering van de publicatie<br> | 
| 2017-08-01 | Algemene Beschik baarheid | Alles | <li> Achterliggend onderstrepings teken verwijderd uit resource namen voor IaaS-Vm's<br><li>Er is een vereiste voor de meta gegevens header afgedwongen voor alle aanvragen | 
| 2017-03-01 | Preview | Alles | <li>Eerste release |


> [!NOTE] 
> Eerdere Preview-versies van Scheduled Events {nieuwste} als de API-versie worden ondersteund. Deze indeling wordt niet meer ondersteund en zal in de toekomst worden afgeschaft.

### <a name="enabling-and-disabling-scheduled-events"></a>Scheduled Events in-en uitschakelen
De eerste keer dat u een aanvraag voor gebeurtenissen maakt, wordt Scheduled Events ingeschakeld voor uw service. U moet een vertraagde reactie verwachten in uw eerste aanroep van Maxi maal twee minuten.

Scheduled Events is uitgeschakeld voor uw service als deze niet 24 uur een aanvraag doet.

### <a name="user-initiated-maintenance"></a>Door de gebruiker geïnitieerd onderhoud
Door de gebruiker geïnitieerde VM-onderhoud via de Azure Portal, API, CLI of Power shell resulteert in een geplande gebeurtenis. U kunt vervolgens de logica voor onderhouds voorbereiding testen in uw toepassing en uw toepassing kan voorbereiden op door de gebruiker geïnitieerd onderhoud.

Als u een virtuele machine opnieuw opstart, wordt een gebeurtenis met het type `Reboot` gepland. Als u een virtuele machine opnieuw implementeert, wordt er een gebeurtenis met het type `Redeploy` gepland.

## <a name="use-the-api"></a>De API gebruiken

### <a name="headers"></a>Kopteksten
Wanneer u Metadata Service een query uitvoert, moet u de header opgeven `Metadata:true` om ervoor te zorgen dat de aanvraag niet per ongeluk is omgeleid. De `Metadata:true` header is vereist voor alle aanvragen voor geplande gebeurtenissen. Het toevoegen van de header in de aanvraag resulteert in een antwoord op een ' ongeldige aanvraag ' van Metadata Service.

### <a name="query-for-events"></a>Query's uitvoeren op gebeurtenissen
U kunt een query uitvoeren voor geplande gebeurtenissen door de volgende aanroep uit te voeren:

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Een antwoord bevat een matrix met geplande gebeurtenissen. Een lege matrix houdt in dat er momenteel geen gebeurtenissen worden gepland.
Als er geplande gebeurtenissen zijn, bevat het antwoord een matrix met gebeurtenissen. 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze" | "Preempt" | "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},       
            "Description": {eventDescription},
            "EventSource" : "Platform" | "User",
        }
    ]
}
```

### <a name="event-properties"></a>Gebeurtenis eigenschappen
|Eigenschap  |  Beschrijving |
| - | - |
| Gebeurtenis | De wereld wijde unieke id voor deze gebeurtenis. <br><br> Voorbeeld: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Dit heeft invloed op deze gebeurtenis. <br><br> Waarden: <br><ul><li> `Freeze`: De virtuele machine is gepland om enkele seconden te worden onderbroken. De CPU-en netwerk verbinding wordt mogelijk onderbroken, maar er is geen invloed op het geheugen of geopende bestanden.<li>`Reboot`: De virtuele machine is gepland voor opnieuw opstarten (niet-permanent geheugen gaat verloren). <li>`Redeploy`: De virtuele machine is ingepland om te worden verplaatst naar een ander knoop punt (tijdelijke schijven gaan verloren). <li>`Preempt`: De locatie van de virtuele machine wordt verwijderd (tijdelijke schijven gaan verloren). <li> `Terminate`: De virtuele machine is gepland om te worden verwijderd. |
| ResourceType | Type resource waarop deze gebeurtenis betrekking heeft. <br><br> Waarden: <ul><li>`VirtualMachine`|
| Resources| Lijst met resources die deze gebeurtenis beïnvloedt. De lijst is gegarandeerd dat machines uit Maxi maal één [update domein](../manage-availability.md)worden opgenomen, maar bevat mogelijk niet alle computers in de UD. <br><br> Voorbeeld: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | De status van deze gebeurtenis. <br><br> Waarden: <ul><li>`Scheduled`: Deze gebeurtenis is gepland om te starten na het tijdstip dat is opgegeven in de `NotBefore` eigenschap.<li>`Started`: Deze gebeurtenis is gestart.</ul> Er `Completed` is ooit geen of vergelijk bare status. De gebeurtenis wordt niet meer geretourneerd wanneer de gebeurtenis is voltooid.
| NotBefore| Tijdstip waarna deze gebeurtenis kan worden gestart. <br><br> Voorbeeld: <br><ul><li> Ma, 19 sep 2016 18:29:47 GMT  |
| Beschrijving | Beschrijving van deze gebeurtenis. <br><br> Voorbeeld: <br><ul><li> De hostserver ondergaat onderhoud. |
| Source | Initiator van de gebeurtenis. <br><br> Voorbeeld: <br><ul><li> `Platform`: Deze gebeurtenis wordt gestart door het platform. <li>`User`: Deze gebeurtenis wordt geïnitieerd door de gebruiker. |

### <a name="event-scheduling"></a>Gebeurtenissen plannen
Elke gebeurtenis wordt in de toekomst gepland op basis van het gebeurtenis type. Deze tijd wordt weer gegeven in de eigenschap van een gebeurtenis `NotBefore` . 

|EventType  | Minimale kennisgeving |
| - | - |
| Kering| 15 minuten |
| Opnieuw opstarten | 15 minuten |
| Opnieuw implementeren | 10 minuten |
| Preempt | 30 seconden |
| Terminate | [Configureer bare gebruiker](../../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md#enable-terminate-notifications): 5 tot 15 minuten |

> [!NOTE] 
> In sommige gevallen kan Azure host-uitval voors pellen vanwege gedegradeerde hardware en zal proberen de onderbreking van uw service te verhelpen door een migratie te plannen. De betrokken virtuele machines ontvangen een geplande gebeurtenis met een `NotBefore` van de meeste dagen in de toekomst. De werkelijke tijd is afhankelijk van de voorspelde risico analyse. Azure probeert zo de voorafgaande kennisgeving van 7 dagen te geven, maar de werkelijke tijd varieert en kan kleiner zijn als de voor spelling is dat er een hoge kans op het onmiddellijk uitvallen van de hardware is. Om het risico voor uw service tot een minimum te beperken, raden we u aan uw virtuele machine zo snel mogelijk zelf opnieuw te implementeren.

### <a name="polling-frequency"></a>Pollingfrequentie

U kunt het eind punt zo vaak of zo vaak als gewenst controleren op updates. Hoe langer de tijd is tussen aanvragen, hoe meer tijd u mogelijk kwijtraakt om te reageren op een aanstaande gebeurtenis. De meeste gebeurtenissen hebben een kennisgeving van vijf tot 15 minuten, hoewel in sommige gevallen een voorafgaande kennisgeving van minder dan 30 seconden kan zijn. Om ervoor te zorgen dat u zo veel mogelijk tijd hebt om maat regelen te nemen, raden we u aan om de service één keer per seconde te pollen.

### <a name="start-an-event"></a>Een gebeurtenis starten 

Nadat u een geplande gebeurtenis hebt gemaakt en de logica hebt voltooid om het probleem op te starten, kunt u de openstaande gebeurtenis goed keuren door een `POST` oproep naar metadata service te maken `EventId` . Deze aanroep geeft aan dat Azure de minimale meldings tijd kan verkorten (indien mogelijk). 

Het volgende JSON-voor beeld wordt verwacht in de `POST` aanvraag tekst. De aanvraag moet een lijst met bevatten `StartRequests` . Elk `StartRequest` bevat `EventId` voor de gebeurtenis die u wilt versnellen:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="bash-sample"></a>Bash-voor beeld
```
curl -H Metadata:true -X POST -d '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01
```

> [!NOTE] 
> Door een gebeurtenis te bevestigen, kan de gebeurtenis `Resources` in de gebeurtenis worden voortgezet, niet alleen de VM die de gebeurtenis bevestigt. Daarom kunt u kiezen of u een leider wilt selecteren om de bevestiging te coördineren. Dit kan net zo eenvoudig zijn als de eerste computer in het `Resources` veld.

## <a name="python-sample"></a>Python-voor beeld 

De volgende voorbeeld query's Metadata Service voor geplande gebeurtenissen en keurt elke openstaande gebeurtenis goed:

```python
#!/usr/bin/python

import json
import socket
import urllib2

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01"
this_host = socket.gethostname()


def get_scheduled_events():
    req = urllib2.Request(metadata_url)
    req.add_header('Metadata', 'true')
    resp = urllib2.urlopen(req)
    data = json.loads(resp.read())
    return data


def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid = evt['EventId']
        status = evt['EventStatus']
        resources = evt['Resources']
        eventtype = evt['EventType']
        resourcetype = evt['ResourceType']
        notbefore = evt['NotBefore'].replace(" ", "_")
    description = evt['Description']
    eventSource = evt['EventSource']
        if this_host in resources:
            print("+ Scheduled Event. This host " + this_host +
                " is scheduled for " + eventtype + 
        " by " + eventSource + 
        " with description " + description +
        " not before " + notbefore)
            # Add logic for handling events here


def main():
    data = get_scheduled_events()
    handle_scheduled_events(data)


if __name__ == '__main__':
    main()
```

## <a name="next-steps"></a>Volgende stappen 
- Bekijk [Scheduled Events op Azure vrijdag](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) om een demo te bekijken. 
- Bekijk de Scheduled Events code voorbeelden in de [meta gegevens van het Azure-exemplaar Scheduled Events de GitHub-opslag plaats](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm).
- Lees meer over de Api's die beschikbaar zijn in de [instance metadata service](instance-metadata-service.md).
- Meer informatie over [gepland onderhoud voor virtuele Windows-machines in azure](../maintenance-and-updates.md?bc=/azure/virtual-machines/windows/breadcrumb/toc.json&toc=/azure/virtual-machines/windows/toc.json).
