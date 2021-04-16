---
title: Lokale IoT Edge van een apparaat gebruiken vanuit een module - Azure IoT Edge | Microsoft Docs
description: Gebruik omgevingsvariabelen en maak opties om moduletoegang in te IoT Edge lokale opslag van het apparaat.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/14/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 78752d4da42fe07461ae0e82b10343dc7219ad91
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482055"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>Modules toegang geven tot de lokale opslag van een apparaat

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Naast het opslaan van gegevens met behulp van Azure Storage-services of in de containeropslag van uw apparaat, kunt u ook opslagruimte op het hostapparaat IoT Edge zelf voor verbeterde betrouwbaarheid, met name wanneer u offline werkt.

## <a name="link-module-storage-to-device-storage"></a>Moduleopslag koppelen aan apparaatopslag

Als u een koppeling vanuit moduleopslag naar de opslag op het hostsysteem wilt inschakelen, maakt u een omgevingsvariabele voor uw module die naar een opslagmap in de container wijst. Gebruik vervolgens de opties voor maken om die opslagmap te binden aan een map op de hostmachine.

Als u bijvoorbeeld de IoT Edge-hub wilt inschakelen om berichten op te slaan in de lokale opslag van uw apparaat en deze later op te halen, kunt u de omgevingsvariabelen en de opties voor maken configureren in de Azure Portal in de sectie **Runtime-instellingen.**

1. Voeg voor IoT Edge hub en IoT Edge-agent een omgevingsvariabele met de naam **storageFolder** toe die naar een map in de module wijst.
1. Voeg voor IoT Edge hub en IoT Edge-agent bindingen toe om een lokale map op de hostmachine te verbinden met een map in de module. Bijvoorbeeld:

   ![Maakopties en omgevingsvariabelen voor lokale opslag toevoegen](./media/how-to-access-host-storage-from-module/offline-storage.png)

U kunt de lokale opslag ook rechtstreeks in het implementatiemanifest configureren. Bijvoorbeeld:

```json
"systemModules": {
    "edgeAgent": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"]
                }
            }
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        }
    },
    "edgeHub": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"],
                    "PortBindings":{"5671/tcp":[{"HostPort":"5671"}],"8883/tcp":[{"HostPort":"8883"}],"443/tcp":[{"HostPort":"443"}]}}}
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        },
        "status": "running",
        "restartPolicy": "always"
    }
}
```

Vervang en door het opslagpad van uw host en `<HostStoragePath>` `<ModuleStoragePath>` module; beide waarden moeten een absoluut pad zijn.

In een Linux-systeem betekent bijvoorbeeld dat de `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` map **/etc/iotedge/storage** op uw hostsysteem is toewijzing aan de map **/iotedge/storage/** in de container. In een Windows-systeem, zoals een ander voorbeeld, betekent dat de map C: temp op uw hostsysteem is toe te staan aan de `"Binds":["C:\\temp:C:\\contemp"]` map **C: \\ contemp** in de container. **\\**

Bovendien moet u op Linux-apparaten ervoor zorgen dat het gebruikersprofiel voor uw module over de vereiste lees-, schrijf- en uitvoermachtigingen beschikt voor de hostsysteemmap. Terugkerend naar het eerdere voorbeeld van het inschakelen van IoT Edge hub voor het opslaan van berichten in de lokale opslag van uw apparaat, moet u machtigingen verlenen aan het gebruikersprofiel, UID 1000. (De IoT Edge-agent werkt als hoofdmap, dus er zijn geen extra machtigingen nodig.) Er zijn verschillende manieren om mapmachtigingen op Linux-systemen te beheren, waaronder het wijzigen van de directory-eigenaar en het wijzigen van de `chown` `chmod` machtigingen, zoals:

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

Meer informatie over opties voor het maken vindt u [in docker docs.](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)

## <a name="encrypted-data-in-module-storage"></a>Versleutelde gegevens in moduleopslag

Wanneer modules de workload-API IoT Edge van de daemon aanroepen om gegevens te versleutelen, wordt de versleutelingssleutel afgeleid met behulp van de module-id en de generatie-id van de module. Er wordt een generatie-id gebruikt om geheimen te beveiligen als een module uit de implementatie wordt verwijderd. Vervolgens wordt later een andere module met dezelfde module-id geïmplementeerd op hetzelfde apparaat. U kunt de generatie-id van een module weergeven met behulp van de Azure [CLI-opdracht az iot hub module-identity show.](/cli/azure/iot/hub/module-identity)

Als u bestanden tussen modules tussen generaties wilt delen, mogen ze geen geheimen bevatten, anders kunnen ze niet worden ontsleuteld.

## <a name="next-steps"></a>Volgende stappen

Zie Gegevens aan de rand opslaan met Azure Blob Storage op IoT Edge voor een extra voorbeeld van toegang tot [hostopslag vanuit een module.](how-to-store-data-blob.md)
