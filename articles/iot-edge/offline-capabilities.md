---
title: Apparaten offline gebruiken - Azure IoT Edge | Microsoft Docs
description: Begrijpen hoe IoT Edge apparaten en modules gedurende langere tijd zonder internetverbinding kunnen werken en hoe IoT Edge gewone IoT-apparaten ook offline kunnen laten werken.
author: kgremban
ms.author: kgremban
ms.date: 11/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c9412e2adeb9b43b4c61437fb41e68bc96b86afd
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481834"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Uitgebreide offlinemogelijkheden voor IoT Edge, modules en onderliggende apparaten

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge biedt ondersteuning voor uitgebreide offlinebewerkingen op uw IoT Edge-apparaten en maakt offlinebewerkingen op niet-IoT Edge onderliggende apparaten mogelijk. Zolang een IoT Edge één kans heeft gehad om verbinding te maken met IoT Hub, kunnen dat apparaat en alle onderliggende apparaten blijven werken met onregelmatige of geen internetverbinding.

## <a name="how-it-works"></a>Uitleg

Wanneer een IoT Edge in de offlinemodus wordt gezet, krijgt IoT Edge hub drie rollen. Ten eerste worden alle berichten op de upstream op de opslag op het apparaat op het apparaat op een later moment op het apparaat op te nemen. Ten tweede fungeert de hub namens IoT Hub om modules en onderliggende apparaten te verifiëren, zodat ze kunnen blijven werken. Ten derde zorgt deze ervoor dat er communicatie mogelijk is tussen onderliggende apparaten die normaal gesproken via IoT Hub zou lopen.

In het volgende voorbeeld ziet u hoe een IoT Edge werkt in de offlinemodus:

1. **Apparaten configureren**

   IoT Edge apparaten zijn automatisch offlinemogelijkheden ingeschakeld. Als u deze mogelijkheid wilt uitbreiden naar andere IoT-apparaten, moet u een bovenliggende/onderliggende relatie tussen de apparaten in de IoT Hub. Vervolgens configureert u de onderliggende apparaten om hun toegewezen bovenliggende apparaat te vertrouwen en de communicatie tussen apparaten en de cloud als gateway via het bovenliggende apparaat te sturen.

2. **Synchroniseren met IoT Hub**

   Ten minste één keer na de installatie van IoT Edge runtime moet het IoT Edge online zijn om te synchroniseren met IoT Hub. Bij deze synchronisatie krijgt IoT Edge apparaat details over alle onderliggende apparaten die aan het apparaat zijn toegewezen. Het IoT Edge werkt ook de lokale cache veilig bij om offlinebewerkingen mogelijk te maken en haalt instellingen op voor lokale opslag van telemetrieberichten.

3. **Offline gaan**

   Hoewel de verbinding met IoT Hub is verbroken, IoT Edge het apparaat, de geïmplementeerde modules en alle IoT-apparaten voor onbepaalde tijd werken. Modules en onderliggende apparaten kunnen worden gestart en opnieuw worden gestart door te authenticeren met de IoT Edge hub terwijl ze offline zijn. Telemetrie die upstream is gebonden aan IoT Hub wordt lokaal opgeslagen. Communicatie tussen modules of tussen onderliggende IoT-apparaten wordt onderhouden via directe methoden of berichten.

4. **Opnieuw verbinding maken en opnieuw synchroniseren met IoT Hub**

   Zodra de verbinding met IoT Hub hersteld, wordt IoT Edge apparaat opnieuw gesynchroniseerd. Lokaal opgeslagen berichten worden direct aan de IoT Hub bezorgd, maar zijn afhankelijk van de snelheid van de verbinding, IoT Hub latentie en gerelateerde factoren. Ze worden geleverd in dezelfde volgorde waarin ze zijn opgeslagen.

   Eventuele verschillen tussen de gewenste en gerapporteerde eigenschappen van de modules en apparaten worden afgestemd. De IoT Edge alle wijzigingen in de set toegewezen onderliggende IoT-apparaten bijgewerkt.

## <a name="restrictions-and-limits"></a>Beperkingen en limieten

De uitgebreide offlinemogelijkheden die in dit artikel worden beschreven, zijn beschikbaar in [IoT Edge versie 1.0.7 of hoger.](https://github.com/Azure/azure-iotedge/releases) Eerdere versies hebben een subset van offlinefuncties. Bestaande IoT Edge-apparaten die geen uitgebreide offlinemogelijkheden hebben, kunnen niet worden bijgewerkt door de runtime-versie te wijzigen, maar moeten opnieuw worden geconfigureerd met een nieuwe IoT Edge-apparaat-id om deze functies te verkrijgen.

Alleen niet-IoT Edge kunnen worden toegevoegd als onderliggende apparaten.

IoT Edge apparaten en hun toegewezen onderliggende apparaten kunnen voor onbepaalde tijd offline werken na de initiële, een time-synchronisatie. De opslag van berichten is echter afhankelijk van de TTL-instelling (Time to Live) en de beschikbare schijfruimte voor het opslaan van de berichten.

## <a name="set-up-parent-and-child-devices"></a>Bovenliggende en onderliggende apparaten instellen

Als u IoT Edge uitgebreide offlinemogelijkheden wilt uitbreiden naar onderliggende IoT-apparaten, moet u twee stappen voltooien. Declareer eerst de bovenliggende en onderliggende relaties in de Azure Portal. Maak vervolgens een vertrouwensrelatie tussen het bovenliggende apparaat en eventuele onderliggende apparaten en configureer vervolgens de communicatie tussen apparaten en de cloud om via het bovenliggende apparaat als gateway te gaan.

### <a name="assign-child-devices"></a>Onderliggende apparaten toewijzen

Onderliggende apparaten kunnen elk niet-IoT Edge zijn dat is geregistreerd op dezelfde IoT Hub. Bovenliggende apparaten kunnen meerdere onderliggende apparaten hebben, maar een onderliggend apparaat heeft slechts één bovenliggend apparaat. Er zijn drie opties om onderliggende apparaten in te stellen op een edge-apparaat: via de Azure Portal, met behulp van de Azure CLI of met behulp van de IoT Hub service-SDK.

In de volgende secties vindt u voorbeelden van hoe u de bovenliggende/onderliggende relatie kunt declare IoT Hub bestaande IoT-apparaten. Als u nieuwe apparaat-id's voor uw onderliggende apparaten maakt, zie [Een downstreamapparaat](how-to-authenticate-downstream-device.md) verifiëren om Azure IoT Hub voor meer informatie.

#### <a name="option-1-iot-hub-portal"></a>Optie 1: IoT Hub Portal

U kunt de relatie tussen bovenliggende en onderliggende apparaten declaren bij het maken van een nieuw apparaat. Voor bestaande apparaten kunt u de relatie ook declareer op de pagina met apparaatdetails van het bovenliggende IoT Edge of het onderliggende IoT-apparaat.

   ![Onderliggende apparaten beheren op de pagina IoT Edge apparaatdetails](./media/offline-capabilities/manage-child-devices.png)

#### <a name="option-2-use-the-az-command-line-tool"></a>Optie 2: het `az` opdrachtregelprogramma gebruiken

Met behulp van de [Azure-opdrachtregelinterface](/cli/azure/) met de [IoT-extensie](https://github.com/azure/azure-iot-cli-extension) (v0.7.0 of hoger) kunt u bovenliggende onderliggende relaties met de subopdracht [apparaat-id](/cli/azure/iot/hub/device-identity/) beheren. In het onderstaande voorbeeld wordt een query gebruikt om alle niet-IoT Edge in de hub toe te wijzen als onderliggende apparaten van een IoT Edge apparaat.

```azurecli
# Set IoT Edge parent device
egde_device="edge-device1"

# Get All IoT Devices
device_list=$(az iot hub query \
        --hub-name replace-with-hub-name \
        --subscription replace-with-sub-name \
        --resource-group replace-with-rg-name \
        -q "SELECT * FROM devices WHERE capabilities.iotEdge = false" \
        --query 'join(`, `, [].deviceId)' -o tsv)

# Add all IoT devices to IoT Edge (as child)
az iot hub device-identity add-children \
  --device-id $egde_device \
  --child-list $device_list \
  --hub-name replace-with-hub-name \
  --resource-group replace-with-rg-name \
  --subscription replace-with-sub-name
```

U kunt de [query wijzigen om](../iot-hub/iot-hub-devguide-query-language.md) een andere subset van apparaten te selecteren. De opdracht kan enkele seconden duren als u een grote set apparaten opgeeft.

#### <a name="option-3-use-iot-hub-service-sdk"></a>Optie 3: SDK IoT Hub Service gebruiken

Ten slotte kunt u bovenliggende onderliggende relaties programmatisch beheren met C#, Java of Node.js IoT Hub Service SDK. Hier is een [voorbeeld van het toewijzen van een onderliggend apparaat met](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/e2e/test/iothub/service/RegistryManagerE2ETests.cs) behulp van de C# SDK.

### <a name="set-up-the-parent-device-as-a-gateway"></a>Het bovenliggende apparaat instellen als gateway

U kunt een bovenliggende/onderliggende relatie zien als een transparante gateway, waarbij het onderliggende apparaat een eigen identiteit heeft in IoT Hub maar via de cloud communiceert via het bovenliggende apparaat. Voor veilige communicatie moet het onderliggende apparaat kunnen controleren of het bovenliggende apparaat afkomstig is van een vertrouwde bron. Anders kunnen derden schadelijke apparaten instellen om ouders te imiteren en communicatie te onderscheppen.

Eén manier om deze vertrouwensrelatie te maken, wordt uitgebreid beschreven in de volgende artikelen:

* [Een IoT Edge-apparaat configureren zodat deze werkt als een transparante gateway](how-to-create-transparent-gateway.md)
* [Een downstream (onderliggend) apparaat verbinden met een Azure IoT Edge gateway](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>DNS-servers opgeven

Om de robuustheid te verbeteren, wordt u ten zeerste aangeraden de DNS-serveradressen op te geven die in uw omgeving worden gebruikt. Als u uw DNS-server wilt instellen voor IoT Edge, zie de oplossing voor de [Edge Agent-module](troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device) rapporteert voortdurend 'leeg configuratiebestand' en geen modules starten op het apparaat in het artikel over probleemoplossing.

## <a name="optional-offline-settings"></a>Optionele offline-instellingen

Als uw apparaten offline gaan, slaat IoT Edge bovenliggende apparaat alle apparaat-naar-cloud-berichten op totdat de verbinding opnieuw tot stand is brengen. De IoT Edge hub-module beheert de opslag en doorsturen van offlineberichten. Voor apparaten die gedurende langere tijd offline kunnen gaan, optimaliseert u de prestaties door twee IoT Edge hub-instellingen te configureren.

Verhoog eerst de time to live-instelling, zodat de IoT Edge hub berichten lang genoeg houdt zodat uw apparaat opnieuw verbinding kan maken. Voeg vervolgens extra schijfruimte toe voor berichtopslag.

### <a name="time-to-live"></a>Time To Live

De time to live-instelling is de hoeveelheid tijd (in seconden) die een bericht kan wachten om te worden afgeleverd voordat het verloopt. De standaardwaarde is 7200 seconden (twee uur). De maximumwaarde wordt alleen beperkt door de maximumwaarde van een variabele met gehele getallen, die ongeveer 2 miljard is.

Deze instelling is een gewenste eigenschap van de IoT Edge hub, die is opgeslagen in de module dubbel. U kunt deze configureren in de Azure Portal of rechtstreeks in het implementatiemanifest.

```json
"$edgeHub": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
        }
    }
}
```

### <a name="host-storage-for-system-modules"></a>Hostopslag voor systeemmodules

Berichten en module statusinformatie worden standaard opgeslagen in IoT Edge lokale containerbestandssysteem van de hub. Voor verbeterde betrouwbaarheid, met name wanneer u offline werkt, kunt u ook opslagruimte op het hostapparaat IoT Edge gebruiken. Zie Modules toegang geven tot de lokale opslag [van een apparaat voor meer informatie](how-to-access-host-storage-from-module.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het instellen van een transparante gateway voor verbindingen tussen bovenliggende en onderliggende apparaten:

* [Een IoT Edge-apparaat configureren zodat deze werkt als een transparante gateway](how-to-create-transparent-gateway.md)
* [Een downstream-apparaat verifiëren voor Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Een downstreamapparaat verbinden met een Azure IoT Edge-gateway](how-to-connect-downstream-device.md)
