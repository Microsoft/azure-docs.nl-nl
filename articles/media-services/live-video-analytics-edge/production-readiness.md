---
title: Gereedheid van productie en aanbevolen procedures-Azure
description: Dit artikel bevat richt lijnen voor het configureren en implementeren van de live video-analyse op IoT Edge module in productie omgevingen.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 56982d84b7ffac718072683076657d56a2691d6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97400553"
---
# <a name="production-readiness-and-best-practices"></a>Productiegereedheid en best practices

Dit artikel bevat richt lijnen voor het configureren en implementeren van de live video-analyse op IoT Edge module in productie omgevingen. Raadpleeg de voor [bereiding voor het implementeren van uw IOT EDGE oplossing in productie](../../iot-edge/production-checklist.md) artikel over het voorbereiden van uw IOT EDGE-oplossing. 

> [!NOTE]
> U moet de IT-afdeling van uw organisatie raadplegen over aspecten die betrekking hebben op de beveiliging.

## <a name="running-the-module-as-a-local-user"></a>De module uitvoeren als een lokale gebruiker

Wanneer u de live video Analytics op IoT Edge module implementeert op een apparaat aan de rand, wordt het standaard uitgevoerd met verhoogde bevoegdheden. Als u dit doet, ziet u de volgende stappen als u de logboeken op de module ( `sudo iotedge logs {name-of-module}` ) controleert:

```
!! production readiness: user accounts – Warning
       LOCAL_USER_ID and LOCAL_GROUP_ID environment variables are not set. The program will run as root!
       For optimum security, make sure to set LOCAL_USER_ID and LOCAL_GROUP_ID environment variables to a non-root user and group.
       See https://aka.ms/lva-iot-edge-prod-checklists-user-accounts for more information.
```

In de volgende secties wordt beschreven hoe u de bovenstaande waarschuwing kunt aanpakken.

### <a name="creating-and-using-a-local-user-account"></a>Een lokaal gebruikers account maken en gebruiken

U kunt de live video analyse op IoT Edge module in productie uitvoeren met een account met zo min mogelijk bevoegdheden. De volgende opdrachten laten bijvoorbeeld zien hoe u een lokaal gebruikers account kunt maken op een virtuele Linux-machine:

```
sudo groupadd -g 1010 localuser
sudo adduser --home /home/edgeuser --uid 1010 -gid 1010 edgeuser
```

Vervolgens kunt u in het implementatie manifest de LOCAL_USER_ID en LOCAL_GROUP_ID omgevings variabelen instellen op die niet-hoofd gebruiker en-groep:

```
"lvaEdge": {
"version": "1.0",
…
"env": {
    "LOCAL_USER_ID": 
    {
        "value": "1010"
    },
    "LOCAL_GROUP_ID": {
        "value": "1010"
    }
}
},
…
```

### <a name="granting-permissions-to-device-storage"></a>Machtigingen verlenen voor opslag van apparaten

De live video Analytics on IoT Edge-module vereist dat er bestanden naar het lokale bestands systeem kunnen worden geschreven wanneer:

* Het gebruik van een module dubbele eigenschap [`applicationDataDirectory`](module-twin-configuration-schema.md#module-twin-properties) , waarbij u een map in het lokale bestands systeem moet opgeven voor het opslaan van configuratie gegevens.
* Als u een media grafiek wilt gebruiken om video op te nemen in de Cloud, vereist de module het gebruik van een map op het edge-apparaat als een cache (Zie [doorlopend video-opname](continuous-video-recording-concept.md) artikel voor meer informatie).
* [Vastleggen in een lokaal bestand](event-based-video-recording-concept.md#video-recording-based-on-events-from-other-sources), waarbij u een bestandspad voor de opgenomen video moet opgeven.

Als u van plan bent om gebruik te maken van een van de bovenstaande gebruikers accounts, moet u ervoor zorgen dat het bovenstaande account toegang heeft tot de relevante map. Denk bijvoorbeeld aan applicationDataDirectory. U kunt een map maken op het edge-apparaat en de opslag ruimte van het apparaat koppelen aan module opslag. 

```
sudo mkdir /var/local/mediaservices
sudo chown -R edgeuser /var/local/mediaservices
```

U kunt vervolgens in de opties voor het maken van de rand in het implementatie manifest een bindings instelling toevoegen aan een map in de module (zoals '/var/lib/azuremediaservices/'), waarbij u de map (' var/Local/Media Services/') toewijst. En u gebruikt de laatste map als de waarde voor de applicationDataDirectory.

```
"lvaEdge": {
    "version": "1.0",
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
        "image": "mcr.microsoft.com/media/live-video-analytics:1.0",
        "createOptions": "{\"HostConfig\":{\"LogConfig\":{\"Type\":\"\",\"Config\":{\"max-size\":\"10m\",\"max-file\":\"10\"}},\"Binds\":[\"/var/local/mediaservices/:/var/lib/azuremediaservices/\"]}}"
    },
    "env": {
        "LOCAL_USER_ID": 
        {
            "value": "1010"
        },
        "LOCAL_GROUP_ID": {
            "value": "1010"
        }
    }
    },
    …
    
    "lvaEdge": {
    "properties.desired": {
    "applicationDataDirectory": "/var/lib/azuremediaservices",
    …
    }
}
```

Als u de voor beeld-media grafieken voor de Snelstartgids en zelf studies bekijkt, zoals [continue video-opname](continuous-video-recording-tutorial.md), ziet u dat de media cache directory (localMediaCachePath) gebruikmaakt van een submap onder applicationDataDirectory. Dit is de aanbevolen benadering, omdat de cache tijdelijke gegevens bevat.

### <a name="naming-video-assets-or-files"></a>Video-assets of -bestanden een naam geven

Met media grafieken kunt u activa maken in de Cloud-of MP4-bestanden aan de rand. Media-assets kunnen worden gegenereerd door [continue video-opnamen](continuous-video-recording-tutorial.md) of op [gebeurtenissen gebaseerde video-opname](event-based-video-recording-tutorial.md). Hoewel deze assets en bestanden als gewenst kunnen worden genoemd, is de aanbevolen naamgevings structuur voor het doorlopend video-opname op basis van media " &lt; AnyText &gt; -$ {System. GraphTopologyName}-$ {System. GraphInstanceName}".   

Vervangings patroon wordt gedefinieerd door het $-teken gevolgd door accolades: **$ {variabelenaam}**.  

Zo kunt u de assetNamePattern op de Asset Sink als volgt instellen:

```
"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}
```

Voor op gebeurtenissen gebaseerde video-opnames gegenereerde assets is het aanbevolen naamgevings patroon " &lt; AnyText &gt; -$ {System. datetime}". De systeem variabele zorgt ervoor dat activa niet overschreven worden als gebeurtenissen op hetzelfde moment plaatsvinden. Zo kunt u de assetNamePattern op de Asset Sink als volgt instellen:

```
"assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}"
```

Als u meerdere exemplaren van dezelfde grafiek uitvoert, kunt u de naam van de grafiek topologie en de naam van het exemplaar gebruiken om onderscheid te maken. Zo kunt u de assetNamePattern op de Asset Sink als volgt instellen:

```
"assetNamePattern": "sampleAssetFromEVR-${System.GraphTopologyName}-${System.GraphInstanceName}-${System.DateTime}"
```

Voor op gebeurtenissen gebaseerde video-opnames gegenereerde MP4 video clips aan de rand moet het aanbevolen naamgevings patroon DateTime bevatten en voor meerdere exemplaren van dezelfde grafiek wordt aanbevolen de systeem variabelen GraphTopologyName en GraphInstanceName te gebruiken. U kunt bijvoorbeeld filePathPattern op bestands Sink als volgt instellen: 

```
"fileNamePattern": "/var/media/sampleFilesFromEVR-${fileSinkOutputName}-${System.DateTime}"
```

of 

```
"fileNamePattern": "/var/media/sampleFilesFromEVR-${fileSinkOutputName}--${System.GraphTopologyName}-${System.GraphInstanceName} ${System.DateTime}"
```
>[!NOTE]
> In het bovenstaande voor beeld is de variabele **fileSinkOutputName** de naam van een voorbeeld variabele die u in de grafiek topologie definieert. Dit is **geen** systeem variabele. 

#### <a name="system-variables"></a>Systeem variabelen
Sommige door het systeem gedefinieerde variabelen die u kunt gebruiken zijn:

|Systeem variabele|Beschrijving|Voorbeeld|
|-----------|-----------|-----------|
|System. DateTime|De UTC-datum tijd in de ISO8601-bestands indeling (Basic-weer gave-YYYYMMDDThhmmss).|20200222T173200Z|
|System. PreciseDateTime|UTC-datum en-tijd in ISO8601 bestands indeling met milliseconden (Basic-weer gave YYYYMMDDThhmmss. SSS).|20200222T 173200.123 Z|
|System. GraphTopologyName|Door de gebruiker ingevoerde naam van de uitgevoerde grafiek topologie.|IngestAndRecord|
|System. GraphInstanceName|Door de gebruiker ingevoerde naam van het exemplaar van de uitvoering van Graph.|camera001|

>[!TIP]
> System. PreciseDateTime kan niet worden gebruikt bij het benoemen van assets vanwege het '. ' in de naam
### <a name="keeping-your-vm-clean"></a>Uw VM opschonen

De Linux-VM die u als een edge-apparaat gebruikt, kan niet meer reageren als deze niet periodiek wordt beheerd. Het is essentieel om de caches leeg te laten, overbodige pakketten te elimineren en ongebruikte containers van de virtuele machine te verwijderen. Hier volgt een aantal aanbevolen opdrachten die u kunt gebruiken op uw Edge-VM.

1. `sudo apt-get clean`

    Met de apt-opdracht ophalen verwijdert u de lokale opslag plaats van opgehaalde pakket bestanden die in/var/cache. blijven De mappen die worden opgeschoond, zijn/var/cache/apt/archives/en/var/cache/apt/archives/partial/. De enige bestanden die het/var/cache/apt/archives heeft, zijn het vergrendelings bestand en de gedeeltelijke submap. De apt-get-opdracht ophalen wordt doorgaans gebruikt om schijf ruimte zo nodig te wissen, doorgaans als onderdeel van het regel matig geplande onderhoud. Zie voor meer informatie [opschonen met apt-get](https://www.networkworld.com/article/3453032/cleaning-up-with-apt-get.html).
1. `sudo apt-get autoclean`

    De apt-optie voor het volledig opschonen, zoals apt-ophalen, wist de lokale opslag plaats van opgehaalde pakket bestanden, maar Hiermee worden alleen bestanden verwijderd die niet meer kunnen worden gedownload en niet nuttig zijn. Zo kunt u ervoor zorgen dat uw cache te groot groeit.
1. `sudo apt-get autoremove1`

    Met de optie voor automatisch verwijderen worden de pakketten verwijderd die automatisch zijn geïnstalleerd, omdat een ander pakket dat vereist, maar met de andere pakketten die worden verwijderd, zijn ze niet meer nodig
1. `sudo docker image ls` – Bevat een lijst met docker-installatie kopieën op uw rand systeem
1. `sudo docker system prune`

    Docker gebruikt een conservatieve benadering voor het opschonen van ongebruikte objecten (ook wel garbagecollection genoemd), zoals installatie kopieën, containers, volumes en netwerken: deze objecten worden doorgaans niet verwijderd, tenzij u hiervoor expliciet een docker vraagt. Dit kan ervoor zorgen dat docker extra schijf ruimte gebruikt. Voor elk type object biedt docker een opdracht voor het weghalen. Daarnaast kunt u docker-systeem weghalen gebruiken om meerdere typen objecten tegelijk op te schonen. Raadpleeg [ongebruikte docker-objecten](https://docs.docker.com/config/pruning/)weghalen voor meer informatie.
1. `sudo docker rmi REPOSITORY:TAG`

    Als er updates worden uitgevoerd voor de module Edge, kan uw docker nog steeds oudere versies van de Edge-module bevatten. In dat geval is het raadzaam om de opdracht docker RMI te gebruiken voor het verwijderen van specifieke installatie kopieën die worden geïdentificeerd door de code van de afbeeldings versie.

## <a name="next-steps"></a>Volgende stappen

[Quickstart: Over Live Video Analytics in IoT Edge](get-started-detect-motion-emit-events-quickstart.md)
