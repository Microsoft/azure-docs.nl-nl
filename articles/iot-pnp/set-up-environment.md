---
title: De IoT-bronnen instellen die u nodig hebt voor IoT Plug en Play | Microsoft Docs
description: Maak een IoT Hub-en Device Provisioning service-exemplaar voor gebruik met de Snelstartgids en zelf studies van IoT Plug en Play.
author: dominicbetts
ms.author: dobett
ms.date: 08/11/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 6e6c090c02798103a30cc68f2ca28e8c4f7f2f17
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "96004259"
---
# <a name="set-up-your-environment-for-the-iot-plug-and-play-quickstarts-and-tutorials"></a>Uw omgeving instellen voor de Snelstartgids en zelf studies voor IoT Plug en Play

Voordat u een van de IoT Plug en Play-Quick starts en zelf studies kunt volt ooien, moet u een IoT-hub en de Device Provisioning Service (DPS) configureren in uw Azure-abonnement. U hebt ook lokale kopieën nodig van de model bestanden die worden gebruikt door de voorbeeld toepassingen en het hulp programma Azure IoT Explorer.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Om de vereiste van het lokaal installeren van de Azure CLI te vermijden, kunt u de Azure Cloud Shell gebruiken om de cloudservices in te stellen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resources"></a>De resources maken

Een Azure-resource groep maken voor de resources:

```azurecli-interactive
az group create --name my-pnp-resourcegroup --location centralus
```

Maak een IoT-hub. De volgende opdracht maakt gebruik van de naam `my-pnp-hub` als voor beeld voor de naam van de IOT-hub die u wilt maken. Kies een unieke naam voor de IoT-hub die u wilt gebruiken in plaats van `my-pnp-hub` :

```azurecli-interactive
az iot hub create --name my-pnp-hub --resource-group my-pnp-resourcegroup --sku F1 --partition-count 2
```

Een DPS-exemplaar maken. De volgende opdracht maakt gebruik van de naam `my-pnp-dps` als voor beeld voor de naam van het DPS-exemplaar dat moet worden gemaakt. Kies een unieke naam voor het DPS-exemplaar dat u wilt gebruiken in plaats van `my-pnp-dps` :

```azurecli-interactive
az iot dps create --name my-pnp-dps --resource-group my-pnp-resourcegroup
```

Gebruik de volgende opdrachten om het DPS-exemplaar te koppelen aan uw IoT-hub. Vervang `my-pnp-dps` en door `my-pnp-hub` de unieke namen die u eerder hebt gekozen:

```azurecli-interactive
hubConnectionString=$(az iot hub connection-string show -n my-pnp-hub --key primary --query connectionString -o tsv)
az iot dps linked-hub create --dps-name my-pnp-dps --resource-group my-pnp-resourcegroup --location centralus --connection-string $hubConnectionString
```

## <a name="retrieve-the-settings"></a>De instellingen ophalen

In sommige Quick starts en zelf studies wordt gebruikgemaakt van de connection string voor uw IoT-hub. U hebt ook de connection string nodig wanneer u het hulp programma Azure IoT Explorer instelt. Haal de connection string op en maak er een notitie van. Vervang door `my-pnp-hub` de unieke naam die u hebt gekozen voor uw IOT-hub:

```azurecli-interactive
az iot hub connection-string show -n my-pnp-hub --key primary --query connectionString
```

In de meeste Snelstartgids en zelf studies wordt gebruikgemaakt van het *id-bereik* van uw DPS-configuratie. Het ID-bereik ophalen en nu een notitie maken. Vervang door `my-pnp-dps` de unieke naam die u hebt gekozen voor uw DPS-exemplaar:

```azurecli-interactive
az iot dps show --name my-pnp-dps --query properties.idScope
```

Alle Quick starts en zelf studies gebruiken een DPS-apparaatregistratie. Gebruik de volgende opdracht om een `my-pnp-device` *individuele registratie* van een apparaat te maken in uw DPS-exemplaar. Vervang door `my-pnp-dps` de unieke naam die u hebt gekozen voor uw DPS-exemplaar. Noteer de registratie-ID en waarden voor de primaire sleutel die u in de Quick starts en zelf studies moet gebruiken:

```azurecli-interactive
az iot dps enrollment create --attestation-type symmetrickey --dps-name my-pnp-dps --resource-group my-pnp-resourcegroup --enrollment-id my-pnp-device --device-id my-pnp-device --query '{registrationID:registrationId,primaryKey:attestation.symmetricKey.primaryKey}'
```

## <a name="create-environment-variables"></a>Omgevingsvariabelen maken

Maak vijf omgevings variabelen voor het configureren van de voor beelden in de Quick starts en zelf studies voor het gebruik van de Device Provisioning Service (DPS) om verbinding te maken met uw IoT-hub:

* **IOTHUB_DEVICE_SECURITY_TYPE**: de waarde `DPS` .
* **IOTHUB_DEVICE_DPS_ID_SCOPE**: het DPS-id-bereik dat u eerder hebt genoteerd.
* **IOTHUB_DEVICE_DPS_DEVICE_ID**: de waarde `my-pnp-device` .
* **IOTHUB_DEVICE_DPS_DEVICE_KEY**: de primaire sleutel voor inschrijving die u eerder hebt gemaakt.
* **IOTHUB_DEVICE_DPS_ENDPOINT**: de waarde `global.azure-devices-provisioning.net`

De service voorbeelden hebben de volgende omgevings variabelen nodig om de hub en het apparaat te identificeren waarmee verbinding moet worden gemaakt:

* **IOTHUB_CONNECTION_STRING**: de verbindingsreeks voor de IoT-hub die u eerder hebt genoteerd.
* **IOTHUB_DEVICE_ID**: `my-pnp-device`.

Bijvoorbeeld in een Linux bash-shell:

```bash
export IOTHUB_DEVICE_SECURITY_TYPE="DPS"
export IOTHUB_DEVICE_DPS_ID_SCOPE="<Your ID scope>"
export IOTHUB_DEVICE_DPS_DEVICE_ID="my-pnp-device"
export IOTHUB_DEVICE_DPS_DEVICE_KEY="<Your enrolment primary key>"
export IOTHUB_DEVICE_DPS_ENDPOINT="global.azure-devices-provisioning.net"
export IOTHUB_CONNECTION_STRING="<Your IoT hub connection string>"
export IOTHUB_DEVICE_ID="my-pnp-device"
```

U kunt bijvoorbeeld op de Windows-opdracht regel:

```cmd
set IOTHUB_DEVICE_SECURITY_TYPE=DPS
set IOTHUB_DEVICE_DPS_ID_SCOPE=<Your ID scope>
set IOTHUB_DEVICE_DPS_DEVICE_ID=my-pnp-device
set IOTHUB_DEVICE_DPS_DEVICE_KEY=<Your enrolment primary key>
set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
set IOTHUB_CONNECTION_STRING=<Your IoT hub connection string>
set IOTHUB_DEVICE_ID=my-pnp-device
```

## <a name="download-the-model-files"></a>De model bestanden downloaden

De Quick starts en zelf studies gebruiken voorbeeld model bestanden voor de temperatuur regelaar en thermo staat-apparaten. De voorbeeld model bestanden downloaden:

1. Maak een map *models* op uw lokale computer.

1. Klik met de rechtermuisknop op [TemperatureController.json](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/TemperatureController.json) en sla het JSON-bestand op in de map *models*.

1. Klik met de rechtermuisknop op [Thermostat.json](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/Thermostat.json) en sla het JSON-bestand op in de map *models*.

## <a name="install-the-azure-iot-explorer"></a>Azure IoT Explorer installeren

In de Quick starts en zelf studies wordt het hulp programma **Azure IOT Explorer** gebruikt. Ga naar [Azure IOT Explorer](https://github.com/Azure/azure-iot-explorer/releases) en vouw de lijst met assets uit voor de meest recente release. Down load en installeer de meest recente versie van de toepassing voor uw besturings systeem.

De eerste keer dat u het hulp programma uitvoert, wordt u gevraagd om de IoT hub-connection string. Gebruik de connection string u eerder een notitie hebt gemaakt.

Configureer het hulp programma voor het gebruik van de model bestanden die u eerder hebt gedownload. Selecteer op de start pagina van het hulp programma **IoT Plug en Play-instellingen** en vervolgens op **> lokale map toevoegen**. Selecteer de map *modellen* die u eerder hebt gemaakt. Selecteer vervolgens **Opslaan** om de instellingen op te slaan.

Zie [Azure IoT Explorer installeren en gebruiken](howto-use-iot-explorer.md) voor meer informatie.

## <a name="remove-the-resources"></a>De resources verwijderen

U kunt de IoT hub en het DPS-exemplaar gebruiken voor alle IoT Plug en Play Quick starts en zelf studies, zodat u de stappen in dit artikel maar één keer hoeft uit te voeren. Wanneer u klaar bent, kunt u deze verwijderen uit uw abonnement met de volgende opdracht:

```azurecli-interactive
az group delete --name my-pnp-resourcegroup
```

## <a name="next-steps"></a>Volgende stappen

Nu u uw omgeving hebt ingesteld, kunt u een van de Quick starts of zelf studies volgen, zoals:

> [!div class="nextstepaction"]
> [Een voor beeld van een IoT-Plug en Play apparaat-toepassing verbinden met IoT Hub](quickstart-connect-device.md)
