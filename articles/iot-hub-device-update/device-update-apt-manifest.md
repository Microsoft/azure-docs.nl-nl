---
title: Informatie over het bijwerken van het apparaat voor Azure IoT Hub APT-manifest | Microsoft Docs
description: Krijg inzicht in de manier waarop updates voor IoT Hub apt-manifest voor een update op basis van een pakket worden gebruikt.
author: vimeht
ms.author: vimeht
ms.date: 2/17/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 878748fcfc9b096e340b53c06969962af99f603f
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561165"
---
# <a name="device-update-apt-manifest"></a>APT-manifest voor het bijwerken van het apparaat

Het APT-manifest is een JSON-bestand met een beschrijving van de update-informatie die vereist is voor APT Updatehandler. Dit bestand kan worden geïmporteerd in update voor het apparaat voor IoT Hub, net zoals elke andere update.

[Meer informatie](import-update.md) over het importeren van updates in de update van het apparaat.

## <a name="overview"></a>Overzicht

Wanneer een APT-manifest als update aan een update agent voor apparaten wordt geleverd, verwerkt de agent het manifest en worden de benodigde bewerkingen uitgevoerd. Tot deze bewerkingen behoren het downloaden en installeren van de pakketten die zijn opgegeven in het manifest bestand APT en de bijbehorende afhankelijkheden.

De update van het apparaat ondersteunt APT UpdateType en APT updatehandler. Deze ondersteuning maakt het mogelijk dat de Update Agent van het apparaat de geïnstalleerde Debian-pakketten evalueert en de benodigde pakketten bijwerkt. 

## <a name="schema"></a>Schema

Een APT-manifest bestand is een JSON-bestand met een versieel schema.

```json
{
    "name": "<name>",
    "version": "<version>",
    "packages": [
        {
            "name": "<package name>",
            "version": "<version specifier>"
        }
    ]
}
```

Voorbeeld:

```json
{
    "name": "contoso-iot-edge",
    "version": "1.0.0.0",
    "packages": [
        {
            "name" : "thermocontrol",
            "version" : "1.0.1"
        },
        {
            "name" : "tempreport",
            "version" : "2.0.0"
        }
    ]
}
```

### <a name="name"></a>naam

De naam voor dit APT-manifest. Dit kan een wille keurige naam of ID zijn die relevant is voor uw scenario's. Bijvoorbeeld `contoso-iot-edge`.

### <a name="version"></a>versie

Een versie nummer voor dit APT-manifest. Bijvoorbeeld `1.0.0.0`.


### <a name="packages"></a>Pakketten

Een lijst met objecten die eigenschappen bevatten die specifiek zijn voor het pakket.

#### <a name="name"></a>naam

De naam of ID van het pakket. Bijvoorbeeld `iotedge`.

#### <a name="version"></a>versie

De gewenste versie criteria voor het pakket. Bijvoorbeeld `1.0.8-2`.

Momenteel wordt alleen exact versie nummer ondersteund. Het versie nummer is de gewenste versie van het Debian-pakket in de indeling [epoche:] upstream_version [-debian_revision].

**epoche** is een niet-ondertekende int.

**upstream_version** kunnen alfanumerieke tekens bevatten, zoals '. ', ' + ', '-' en ' ~ '. De naam moet beginnen met een cijfer.
> [!NOTE]
> ' 1.0.8 ' is gelijk aan ' 1.0.8-0 '

**`"name":"iotedge" and "version":"1.0.8-2"`** Is bijvoorbeeld gelijk aan het installeren van een pakket met behulp van de opdracht`apt-get install iotedge=1.0.8-2`

> [!NOTE]
> De versie waarde bevat geen gelijkteken

Als versie wordt wegge laten, wordt de meest recente beschik bare versie van het opgegeven pakket geïnstalleerd.

Meer [informatie](https://www.debian.org/doc/debian-policy/ch-controlfields.html#s-f-version) over de versie van Debian-pakketten.

> [!NOTE]
> APT package manager negeert de vereisten voor versie beheer die door een pakket worden gegeven wanneer de afhankelijke pakketten die moeten worden geïnstalleerd, automatisch worden opgelost. Tenzij er expliciete versies van afhankelijke pakketten worden opgegeven, gebruiken ze de meest recente, zelfs als het pakket zelf een strikte vereiste (=) voor een bepaalde versie kan opgeven. Deze automatische oplossing kan leiden tot fouten met betrekking tot een unmet-afhankelijkheid. [Meer informatie](https://unix.stackexchange.com/questions/350192/apt-get-not-properly-resolving-a-dependency-on-a-fixed-version-in-a-debian-ubunt)

Als u een specifieke versie van de Azure IoT Edge Security daemon bijwerkt, moet u de gewenste versie van het `iotedge` pakket en het afhankelijke `libiothsm-std` pakket in uw apt-manifest toevoegen.
[Meer informatie](../iot-edge/how-to-update-iot-edge.md#update-the-security-daemon)

> [!NOTE]
> Een apt-manifest kan worden gebruikt voor het bijwerken van de Update Agent van het apparaat en de bijbehorende afhankelijkheden. Vermeld de naam van de apparaat-Update Agent en de gewenste versie in het apt-manifest, zoals u zou doen voor elk ander pakket. Dit apt-manifest kan vervolgens worden geïmporteerd en geïmplementeerd via de update van het apparaat voor IoT Hub pijp lijn. 

## <a name="removing-packages"></a>Pakketten verwijderen

U kunt ook een apt-manifest gebruiken om geïnstalleerde pakketten van uw apparaat te verwijderen. U kunt één apt-manifest gebruiken om meerdere pakketten te verwijderen, toe te voegen en bij te werken. Als u een pakket wilt verwijderen, voegt u een minteken (-) toe achter de pakket naam. Neem geen versie nummer op voor de pakketten die u wilt verwijderen. Als pakketten worden verwijderd via een apt-manifest, worden de afhankelijkheden en configuraties niet verwijderd.

Voorbeeld:

```json
{
    "name": "contoso-video",
    "version": "2.0.0.1",
    "packages": [
        {
            "name" : "foo-"
        }
    ]
}
```
Met dit apt-manifest wordt het pakket ' foo ' verwijderd van de apparaten waarop het is geïmplementeerd. 

## <a name="recommended-value-for-installed-criteria"></a>Aanbevolen waarde voor geïnstalleerde criteria

De geïnstalleerde criteria voor een APT-manifest `<name>-<version>` zijn `<name>` de naam van het apt-manifest en `<version>` is de versie van het apt-manifest. Bijvoorbeeld `contoso-iot-edge-1.0.0.0`. 

## <a name="guidelines-on-creating-an-apt-manifest"></a>Richt lijnen voor het maken van een APT-manifest

Tijdens het maken van het APT-manifest zijn er enkele richt lijnen die u moet onthouden:

- Zorg er altijd voor dat het APT-manifest een juist gevormd JSON-bestand is
- Elk APT-manifest moet een unieke versie hebben. Probeer een gestandaardiseerde methodologie te maken om de versie van het APT-manifest te verhogen, zodat het zinvol is voor uw scenario's en eenvoudig kan worden gevolgd
- Wanneer het gaat om de gewenste status van elk afzonderlijk pakket, geeft u de exacte naam en versie van het pakket op dat u wilt installeren op uw apparaat. De waarden altijd valideren op basis van de pakket opslagplaats die u wilt gebruiken als de bron voor het pakket
- Zorg ervoor dat de pakketten in het APT-manifest worden weer gegeven in de volg orde waarin ze moeten worden geïnstalleerd/verwijderd
- Valideer altijd de installatie van pakketten op een test apparaat om te controleren of het resultaat gewenst is
- Bij de installatie van een specifieke versie van een pakket (bijvoorbeeld `iotedge 1.0.9-1` ), is het best practice in het manifest apt ook de expliciete versies van de afhankelijke pakketten die moeten worden geïnstalleerd (bijvoorbeeld `libiothsm 1.0.9-1` )
- Hoewel het niet is vereist, moet u er altijd voor zorgen dat uw APT-manifest cumulatief is om te voor komen dat uw apparaat een onbekende status krijgt. Een cumulatieve update zorgt ervoor dat op uw apparaten de gewenste versie van elk pakket wordt gebruikt, zelfs als het apparaat een APT-update-implementatie heeft overgeslagen vanwege een fout in de installatie of offline wordt gehaald

Bijvoorbeeld:

**APT-manifest baseren**

```JSON
{
    "name": "contoso-iot-edge",
    "version": "1.0",
    "packages": [
        {
            "name": "foo",
            "version": "1.0.1"
        }
    ]
}
```

**ONJUISTE UPDATE**

Deze update bevat het balk pakket, maar niet het pakket foo.

```JSON
{
    "name": "contoso-iot-edge",
    "version": "2.0",
    "packages": [
        {
            "name": "bar",
            "version": "3.0.2"
        }
    ]
}
```

**GOEDE UPDATE**

Deze update bevat het Foo-pakket en bevat ook een balk pakket.

```JSON
{
    "name": "contoso-iot-edge",
    "version": "2.0",
    "packages": [
        {
            "name": "foo",
            "version": "1.0.1"
        },
        {
            "name": "bar",
            "version": "3.0.2"
        }
    ]
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Nieuwe update importeren](import-update.md)