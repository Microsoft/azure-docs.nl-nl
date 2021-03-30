---
title: 'Scriptvoorbeeld: Voorlopig verwijderen uitschakelen voor bestandsshares'
description: Meer informatie over het gebruik van een script om het voorlopig verwijderen van bestandsshares in een opslagaccount uit te schakelen.
ms.topic: sample
ms.date: 02/02/2020
ms.openlocfilehash: 172a7bd19ffd07ca7900a457c447362343e5f185
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84121281"
---
# <a name="disable-soft-delete-for-file-shares-in-a-storage-account"></a>Voorlopig verwijderen uitschakelen voor bestandsshares in een opslagaccount

In dit document wordt het proces uitgelegd voor het uitschakelen van voorlopig verwijderen voor bestandsshares in een opslagaccount.

Volg deze stappen:

1. Installeer armclient. Ga voor meer informatie over het installeren ervan naar [deze link](https://github.com/projectkudu/ARMClient).

2. Sla de volgende twee aanvraagbodybestanden op in een map op uw computer.

    ```json
    rqbody-enableSoftDelete.json

    {
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled":true,
        "days": 14
        }
    },
    "cors": {
        "corsRules": []
    }
    }

    rqbody-disableSoftDelete.json

    {
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled":false,
        "days": 0
        }
    },
    "cors": {
        "corsRules": []
    }
    }
    ```

3. Houd de ID van uw Azure Resource Manager-opslagaccount bij de hand. Bijvoorbeeld: `/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare/providers/Microsoft.Storage/storageAccounts/inquirytest`

4. Meld u aan met uw aanmeldingsgegevens door **armclient login** uit te voeren.

5. De huidige eigenschappen voor het voorlopig verwijderen van bestandsshares ophalen in het opslagaccount.

    Met de volgende GET-bewerking worden de eigenschappen voor het voorlopig verwijderen van bestandsshares opgehaald uit het *inquirytest*-account:

    ```cmd
    armclient get /subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare /providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default?api-version=2019-04-01
    ```

    ```output
    {
    "id": "/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/Bugbash/providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/de
    fault",
    "name": "default",
    "type": "Microsoft.Storage/storageAccounts/fileServices",
    "properties": {
        "cors": {
        "corsRules": []
        },
        "shareDeleteRetentionPolicy": {
        "enabled": true,
        "days": 14
        }
    }
    }
    ```

6. Schakel de optie voor het voorlopig verwijderen van bestandsshares uit in het opslagaccount.

    Met de volgende PUT-bewerking worden de eigenschappen voor het voorlopig verwijderen van bestandsshares in het *inquirytest*-account uitgeschakeld:

    ```cmd
    armclient put /subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare /providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default?api-version=2019-04-01 .\rqbody-disableSoftDelete.json
    ```

    ```Output
    {
    "id": "/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/Bugbash/providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/de
    fault",
    "name": "default",
    "type": "Microsoft.Storage/storageAccounts/fileServices",
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled": false,
        "days": 0
        }
    }
    }
    ```

7. Als u het voorlopig verwijderen opnieuw wilt inschakelen, gebruikt u het volgende voorbeeld.

    Met de volgende PUT-bewerking worden de eigenschappen voor het voorlopig verwijderen van bestandsshares in het account inquirytest-account ingeschakeld.

    ```cmd
    armclient put /subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare /providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default?api-version=2019-04-01 .\rqbody-EnableSoftDelete.json
    ```

    ```Output
    {
    "id": "/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/Bugbash/providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default",
    "name": "default",
    "type": "Microsoft.Storage/storageAccounts/fileServices",
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled": true,
        "days": 14
        }
    }
    }
    ```
