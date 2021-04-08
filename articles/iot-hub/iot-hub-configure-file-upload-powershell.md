---
title: Gebruik de Azure PowerShell voor het configureren van bestand uploaden | Microsoft Docs
description: De Azure PowerShell-cmdlets gebruiken om uw IoT-hub te configureren om het uploaden van bestanden vanaf verbonden apparaten mogelijk te maken. Bevat informatie over het configureren van het Azure Storage-doel account.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: e2b106e64aed08b0586575d4d77602329454a673
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92536008"
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>Uploads van IoT Hub-bestanden configureren met Power shell

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Als u de [functie voor het uploaden van bestanden in IOT hub](iot-hub-devguide-file-upload.md)wilt gebruiken, moet u eerst een Azure Storage-account koppelen aan uw IOT-hub. U kunt een bestaand opslag account gebruiken of een nieuwe maken.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Een actief Azure-account. Als u geen account hebt, kunt u in slechts een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) maken.

* [Azure PowerShell-cmdlets](/powershell/azure/install-Az-ps).

* Een Azure IoT-hub. Als u geen IoT-hub hebt, kunt u de [cmdlet New-AzIoTHub](/powershell/module/az.iothub/new-aziothub) gebruiken om er een te maken of de portal te gebruiken om [een IOT-hub te maken](iot-hub-create-through-portal.md).

* Een Azure Storage-account. Als u geen Azure Storage-account hebt, kunt u de [Azure Storage Power shell-cmdlets](/powershell/module/az.storage/) gebruiken om er een te maken of de portal te gebruiken voor het [maken van een opslag account](../storage/common/storage-account-create.md)

## <a name="sign-in-and-set-your-azure-account"></a>Meld u aan en stel uw Azure-account in

Meld u aan bij uw Azure-account en selecteer uw abonnement.

1. Voer bij de Power shell-prompt de cmdlet **Connect-AzAccount** uit:

    ```powershell
    Connect-AzAccount
    ```

2. Als u meerdere Azure-abonnementen hebt en u zich aanmeldt bij Azure, hebt u toegang tot alle Azure-abonnementen die zijn gekoppeld aan uw referenties. Gebruik de volgende opdracht om de Azure-abonnementen weer te geven die beschikbaar zijn voor gebruik:

    ```powershell
    Get-AzSubscription
    ```

    Gebruik de volgende opdracht om het abonnement te selecteren dat u wilt gebruiken voor het uitvoeren van de opdrachten voor het beheren van uw IoT-hub. U kunt de naam van het abonnement of de id van de uitvoer van de vorige opdracht gebruiken:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

## <a name="retrieve-your-storage-account-details"></a>Details van uw opslag account ophalen

Bij de volgende stappen wordt ervan uitgegaan dat u uw opslag account hebt gemaakt met behulp van het **Resource Manager** -implementatie model en niet het **klassieke** implementatie model.

Als u het uploaden van bestanden vanaf uw apparaten wilt configureren, hebt u de connection string voor een Azure-opslag account nodig. Het opslag account moet zich in hetzelfde abonnement benemen als uw IoT-hub. U hebt ook de naam van een BLOB-container in het opslag account nodig. Gebruik de volgende opdracht om de sleutels van uw opslag account op te halen:

```powershell
Get-AzStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

Noteer de sleutel waarde voor **key1** -opslag account. U hebt deze nodig in de volgende stappen.

U kunt een bestaande BLOB-container voor het uploaden van bestanden gebruiken of een nieuwe maken:

* Als u de bestaande BLOB-containers in uw opslag account wilt weer geven, gebruikt u de volgende opdrachten:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzStorageContainer -Context $ctx
    ```

* Als u een BLOB-container in uw opslag account wilt maken, gebruikt u de volgende opdrachten:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>Uw IoT-hub configureren

U kunt nu uw IoT-hub configureren voor [het uploaden van bestanden naar de IOT-hub](iot-hub-devguide-file-upload.md) met behulp van de gegevens van uw opslag account.

Voor de configuratie zijn de volgende waarden vereist:

* **Opslag container**: een BLOB-container in een Azure-opslag account in uw huidige Azure-abonnement dat u wilt koppelen aan uw IOT-hub. U hebt de benodigde gegevens voor het opslag account opgehaald in de voor gaande sectie. IoT Hub genereert automatisch SAS-Uri's met schrijf machtigingen voor deze BLOB-container zodat apparaten kunnen worden gebruikt bij het uploaden van bestanden.

* **Meldingen ontvangen voor geüploade bestanden**: meldingen voor het uploaden van bestanden in-of uitschakelen.

* **SAS TTL**: deze instelling is de time-to-Live van de SAS-uri's die op het apparaat worden geretourneerd door IOT hub. Standaard ingesteld op één uur.

* **Instellingen voor bestands meldingen standaard-TTL**: de time-to-Live van een melding bij het uploaden van een bestand voordat deze is verlopen. Standaard ingesteld op één dag.

* **Aantal bestands meldingen maximale leverings** duur: het aantal keren dat de IOT hub een melding over het uploaden van een bestand probeert te leveren. Standaard ingesteld op 10.

Gebruik de volgende Power shell-cmdlet om de instellingen voor het uploaden van bestanden te configureren op uw IoT-hub:

```powershell
Set-AzIotHub `
    -ResourceGroupName "{your iot hub resource group}" `
    -Name "{your iot hub name}" `
    -FileUploadNotificationTtl "01:00:00" `
    -FileUploadSasUriTtl "01:00:00" `
    -EnableFileUploadNotifications $true `
    -FileUploadStorageConnectionString "DefaultEndpointsProtocol=https;AccountName={your storage account name};AccountKey={your storage account key};EndpointSuffix=core.windows.net" `
    -FileUploadContainerName "{your blob container name}" `
    -FileUploadNotificationMaxDeliveryCount 10
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