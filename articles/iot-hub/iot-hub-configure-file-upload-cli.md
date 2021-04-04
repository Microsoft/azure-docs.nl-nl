---
title: Het uploaden van bestanden naar IoT Hub configureren met behulp van Azure CLI | Microsoft Docs
description: Het configureren van uploads van bestanden naar Azure IoT Hub met behulp van de platformoverschrijdende Azure CLI.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: 4dbda13ffe04e0a4214b24ccaca2b8103a39b9f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92536059"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Uploads van IoT Hub-bestanden configureren met behulp van Azure CLI

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Als u [bestanden van een apparaat wilt uploaden](iot-hub-devguide-file-upload.md), moet u eerst een Azure Storage-account koppelen aan uw IOT-hub. U kunt een bestaand opslag account gebruiken of een nieuwe maken.

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Een actief Azure-account. Als u geen account hebt, kunt u in slechts een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) maken.

* [Azure CLI](/cli/azure/install-azure-cli).

* Een Azure IoT-hub. Als u geen IoT-hub hebt, kunt u de [ `az iot hub create` opdracht](/cli/azure/iot/hub#az-iot-hub-create) gebruiken om een IOT-hub te maken of [met behulp van de portal](iot-hub-create-through-portal.md).

* Een Azure Storage-account. Als u geen Azure Storage account hebt, kunt u de Azure CLI gebruiken om er een te maken. Zie [Een opslagaccount maken](../storage/common/storage-account-create.md) voor meer informatie.

## <a name="sign-in-and-set-your-azure-account"></a>Meld u aan en stel uw Azure-account in

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

    Gebruik de volgende opdracht om het abonnement te selecteren dat u wilt gebruiken voor het uitvoeren van de opdrachten voor het maken van uw IoT-hub. U kunt de naam van het abonnement of de id van de uitvoer van de vorige opdracht gebruiken:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>Details van uw opslag account ophalen

Bij de volgende stappen wordt ervan uitgegaan dat u uw opslag account hebt gemaakt met behulp van het **Resource Manager** -implementatie model en niet het **klassieke** implementatie model.

Als u het uploaden van bestanden vanaf uw apparaten wilt configureren, hebt u de connection string voor een Azure-opslag account nodig. Het opslag account moet zich in hetzelfde abonnement benemen als uw IoT-hub. U hebt ook de naam van een BLOB-container in het opslag account nodig. Gebruik de volgende opdracht om de sleutels van uw opslag account op te halen:

```azurecli
az storage account show-connection-string --name {your storage account name} \
  --resource-group {your storage account resource group}
```

Noteer de waarde **Connections Tring** . U hebt deze nodig in de volgende stappen.

U kunt een bestaande BLOB-container voor het uploaden van bestanden gebruiken of een nieuwe maken:

* Als u de bestaande BLOB-containers in uw opslag account wilt weer geven, gebruikt u de volgende opdracht:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Als u een BLOB-container in uw opslag account wilt maken, gebruikt u de volgende opdracht:

    ```azurecli
    az storage container create --name {container name} \
      --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Bestand uploaden

U kunt nu uw IoT-hub configureren, zodat u [bestanden kunt uploaden naar de IOT-hub](iot-hub-devguide-file-upload.md) met behulp van de gegevens van uw opslag account.

Voor de configuratie zijn de volgende waarden vereist:

* **Opslag container**: een BLOB-container in een Azure-opslag account in uw huidige Azure-abonnement dat u wilt koppelen aan uw IOT-hub. U hebt de benodigde gegevens voor het opslag account opgehaald in de voor gaande sectie. IoT Hub genereert automatisch SAS-Uri's met schrijf machtigingen voor deze BLOB-container zodat apparaten kunnen worden gebruikt bij het uploaden van bestanden.

* **Meldingen ontvangen voor geüploade bestanden**: meldingen voor het uploaden van bestanden in-of uitschakelen.

* **SAS TTL**: deze instelling is de time-to-Live van de SAS-uri's die op het apparaat worden geretourneerd door IOT hub. Standaard ingesteld op één uur.

* **Instellingen voor bestands meldingen standaard-TTL**: de time-to-Live van een melding bij het uploaden van een bestand voordat deze is verlopen. Standaard ingesteld op één dag.

* **Aantal bestands meldingen maximale leverings** duur: het aantal keren dat de IOT hub een melding over het uploaden van een bestand probeert te leveren. Standaard ingesteld op 10.

Gebruik de volgende Azure CLI-opdrachten voor het configureren van de instellingen voor het uploaden van bestanden op uw IoT-hub:

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

U kunt de configuratie voor het uploaden van bestanden op uw IoT-hub bekijken met de volgende opdracht:

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>Volgende stappen

Zie [bestanden uploaden van een apparaat](iot-hub-devguide-file-upload.md)voor meer informatie over de mogelijkheden voor het uploaden van bestanden van IOT hub.

Volg deze koppelingen voor meer informatie over het beheren van Azure IoT Hub:

* [IoT-apparaten bulksgewijs beheren](iot-hub-bulk-identity-mgmt.md)
* [Uw IoT-hub bewaken](monitor-iot-hub.md)

Zie voor meer informatie over de mogelijkheden van IoT Hub:

* [Ontwikkelaars handleiding IoT Hub](iot-hub-devguide.md)
* [AI implementeren op Edge-apparaten met Azure IoT Edge](../iot-edge/quickstart-linux.md)
* [Beveilig uw IoT-oplossing vanaf de grond](../iot-fundamentals/iot-security-ground-up.md)