---
title: Upload van bestanden naar IoT Hub configureren met behulp van Azure CLI | Microsoft Docs
description: Bestandsuploads configureren naar Azure IoT Hub met behulp van de platformoverschrijdende Azure CLI.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: ea6ec30ad5f3b1cdbc906cc94cb211295b84e802
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761723"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Uploads IoT Hub bestanden configureren met behulp van Azure CLI

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Als [u bestanden vanaf een apparaat wilt uploaden,](iot-hub-devguide-file-upload.md)moet u eerst een Azure Storage account koppelen aan uw IoT-hub. U kunt een bestaand opslagaccount gebruiken of een nieuw opslagaccount maken.

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Een actief Azure-account. Als u geen account hebt, kunt u binnen een paar minuten een gratis [account](https://azure.microsoft.com/pricing/free-trial/) maken.

* [Azure CLI](/cli/azure/install-azure-cli).

* Een Azure IoT-hub. Als u geen IoT-hub hebt, kunt [ `az iot hub create` ](/cli/azure/iot/hub#az_iot_hub_create) u de opdracht gebruiken om er een te maken of een [IoT-hub maken met behulp van de portal](iot-hub-create-through-portal.md).

* Een Azure Storage-account. Als u geen account voor Azure Storage hebt, kunt u de Azure CLI gebruiken om er een te maken. Zie [Een opslagaccount maken](../storage/common/storage-account-create.md) voor meer informatie.

## <a name="sign-in-and-set-your-azure-account"></a>Aanmelden en uw Azure-account instellen

Meld u aan bij uw Azure-account en selecteer uw abonnement.

1. Voer bij de opdrachtprompt deze [aanmeldingsopdracht](/cli/azure/get-started-with-azure-cli) uit:

    ```azurecli
    az login
    ```

    Volg de instructies om te verifiëren met de code en meld u aan bij uw Azure-account via een webbrowser.

2. Als u meerdere Azure-abonnementen hebt en u zich aanmeldt bij Azure, hebt u toegang tot alle Azure accounts die zijn gekoppeld aan uw referenties. Gebruik de volgende [opdracht om de Azure-accounts weer te geven](/cli/azure/account) die u kunt gebruiken:

    ```azurecli
    az account list
    ```

    Gebruik de volgende opdracht om het abonnement te selecteren dat u wilt gebruiken om de opdrachten uit te voeren om uw IoT-hub te maken. U kunt de naam van het abonnement of de id van de uitvoer van de vorige opdracht gebruiken:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>Gegevens van uw opslagaccount ophalen

In de volgende stappen wordt ervan  uitgenomen dat u uw opslagaccount hebt gemaakt met het Resource Manager implementatiemodel en niet met het **klassieke** implementatiemodel.

Als u uploads van bestanden vanaf uw apparaten wilt configureren, moet connection string voor een Azure-opslagaccount. Het opslagaccount moet zich in hetzelfde abonnement als uw IoT-hub. U hebt ook de naam van een blobcontainer in het opslagaccount nodig. Gebruik de volgende opdracht om uw opslagaccountsleutels op te halen:

```azurecli
az storage account show-connection-string --name {your storage account name} \
  --resource-group {your storage account resource group}
```

Noteer de **waarde van connectionString.** U hebt deze nodig in de volgende stappen.

U kunt een bestaande blobcontainer gebruiken voor het uploaden van bestanden of een nieuwe maken:

* Gebruik de volgende opdracht om de bestaande blobcontainers in uw opslagaccount weer te geven:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Gebruik de volgende opdracht om een blobcontainer te maken in uw opslagaccount:

    ```azurecli
    az storage container create --name {container name} \
      --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Bestand uploaden

U kunt nu uw IoT-hub configureren zodat u bestanden kunt uploaden naar de [IoT-hub](iot-hub-devguide-file-upload.md) met behulp van de gegevens van uw opslagaccount.

Voor de configuratie zijn de volgende waarden vereist:

* **Opslagcontainer:** een blobcontainer in een Azure-opslagaccount in uw huidige Azure-abonnement om te koppelen aan uw IoT-hub. U hebt de benodigde opslagaccountgegevens in de vorige sectie opgehaald. IoT Hub genereert automatisch SAS-URI's met schrijfmachtigingen voor deze blobcontainer die apparaten kunnen gebruiken wanneer ze bestanden uploaden.

* **Meldingen ontvangen voor geüploade bestanden:** uploadmeldingen voor bestanden in- of uitschakelen.

* **SAS TTL:** deze instelling is de time-to-live van de SAS-URI's die op het apparaat worden geretourneerd door IoT Hub. Standaard ingesteld op één uur.

* **Standaard-TTL voor** bestandsmeldingsinstellingen: de time-to-live-melding voor het uploaden van bestanden voordat deze is verlopen. Standaard ingesteld op één dag.

* **Maximale leveringsaantal bestandsmeldingen:** het aantal keren dat de IoT Hub een melding voor het uploaden van bestanden probeert te leveren. Standaard ingesteld op 10.

Gebruik de volgende Azure CLI-opdrachten om de instellingen voor het uploaden van bestanden op uw IoT-hub te configureren:

<!--Robinsh this is out of date, add cloud powershell -->

Gebruik in een bash-shell:

```azurecli
az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} \
  --set properties.enableFileUploadNotifications=true

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

U kunt de configuratie voor het uploaden van bestanden op uw IoT-hub controleren met behulp van de volgende opdracht:

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>Volgende stappen

Zie Upload files from a device (Bestanden uploaden vanaf een apparaat) IoT Hub over de [uploadmogelijkheden van bestanden.](iot-hub-devguide-file-upload.md)

Volg deze koppelingen voor meer informatie over het beheren van Azure IoT Hub:

* [IoT-apparaten bulksgewijs beheren](iot-hub-bulk-identity-mgmt.md)
* [Uw IoT-hub bewaken](monitor-iot-hub.md)

Als u de mogelijkheden van uw IoT Hub wilt verkennen, gaat u naar:

* [IoT Hub ontwikkelaarshandleiding](iot-hub-devguide.md)
* [AI implementeren op Edge-apparaten met Azure IoT Edge](../iot-edge/quickstart-linux.md)
* [Uw IoT-oplossing vanaf de basis beveiligen](../iot-fundamentals/iot-security-ground-up.md)