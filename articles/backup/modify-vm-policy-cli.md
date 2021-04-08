---
title: Het bestaande VM-back-upbeleid bijwerken met CLI
description: Meer informatie over het bijwerken van het bestaande VM-back-upbeleid met behulp van Azure CLI.
ms.topic: conceptual
ms.date: 12/31/2020
ms.openlocfilehash: 33083d6585d2b9296cd184ba258b8d2143d685b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98728575"
---
# <a name="update-the-existing-vm-backup-policy-using-cli"></a>Het bestaande VM-back-upbeleid bijwerken met CLI

U kunt Azure CLI gebruiken om een bestaand back-upbeleid voor VM'S bij te werken. In dit artikel wordt uitgelegd hoe u het bestaande beleid naar een JSON-bestand exporteert, het bestand wijzigt en vervolgens Azure CLI gebruikt om het beleid bij te werken met het gewijzigde beleid.

## <a name="modify-an-existing-policy"></a>Bestaand beleid bewerken

Voer de volgende stappen uit om een bestaand back-upbeleid voor VM'S te wijzigen:

1. Voer de opdracht [AZ backup policy show](/cli/azure/backup/policy#az_backup_policy_show) uit om de details op te halen van het beleid dat u wilt bijwerken.

    Voorbeeld:

    ```azurecli
    az backup policy show --name testing123 --resource-group rg1234 --vault-name testvault
    ```

    In het bovenstaande voor beeld ziet u de details van een VM-beleid met de naam *testing123*.

    Uitvoer:

    ```json
    {
    "eTag": null,
    "id": "/Subscriptions/efgsf-123-test-subscription/resourceGroups/rg1234/providers/Microsoft.RecoveryServices/vaults/testvault/backupPolicies/testing123",
    "location": null,
    "name": "testing123",
    "properties": {
        "backupManagementType": "AzureIaasVM",
        "instantRpDetails": {
        "azureBackupRgNamePrefix": null,
        "azureBackupRgNameSuffix": null
        },
        "instantRpRetentionRangeInDays": 2,
        "protectedItemsCount": 0,
        "retentionPolicy": {
        "dailySchedule": {
            "retentionDuration": {
            "count": 180,
            "durationType": "Days"
            },
            "retentionTimes": [
            "2020-08-03T04:30:00+00:00"
            ]
        },
        "monthlySchedule": null,
        "retentionPolicyType": "LongTermRetentionPolicy",
        "weeklySchedule": {
            "daysOfTheWeek": [
            "Sunday"
            ],
            "retentionDuration": {
            "count": 30,
            "durationType": "Weeks"
            },
            "retentionTimes": [
            "2020-08-03T04:30:00+00:00"
            ]
        },
        "yearlySchedule": null
        },
        "schedulePolicy": {
        "schedulePolicyType": "SimpleSchedulePolicy",
        "scheduleRunDays": null,
        "scheduleRunFrequency": "Daily",
        "scheduleRunTimes": [
            "2020-08-03T04:30:00+00:00"
        ],
        "scheduleWeeklyFrequency": 0
        },
        "timeZone": "UTC"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupPolicies"
    }
    ```

1. Sla de bovenstaande uitvoer op in een. JSON-bestand. U kunt deze bijvoorbeeld opslaan als *Policy.jsop*.
1. Werk het JSON-bestand bij op basis van uw vereisten en sla de wijzigingen op.

    Voor beeld: als u de wekelijkse retentie naar 60 dagen wilt bijwerken, werkt u de volgende sectie van het JSON-bestand bij door het aantal te wijzigen in 60.

    ```json
            "retentionDuration": {
          "count": 60,
          "durationType": "Weeks"
        }

    ```

1. Sla de wijzigingen op.
1. Voer de opdracht [AZ backup policy set](/cli/azure/backup/policy#az_backup_policy_set) uit en geef het volledige pad van het bijgewerkte JSON-bestand door als waarde voor de para meter **--Policy** .

    ```azurecli
    az backup policy set --resource-group rg1234 --vault-name testvault --policy C:\temp2\Policy.json --name testing123
    ```

>[!NOTE]
>U kunt ook het voor beeld-JSON-beleid ophalen door de opdracht [AZ backup policy Get-default-for-VM](/cli/azure/backup/policy#az_backup_policy_get_default_for_vm) uit te voeren.

## <a name="next-steps"></a>Volgende stappen

- [Back-ups van Azure-VM'S beheren met de Azure Backup-Service](backup-azure-manage-vms.md)