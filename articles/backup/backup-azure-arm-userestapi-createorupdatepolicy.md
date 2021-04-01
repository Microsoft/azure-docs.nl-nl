---
title: Back-upbeleid maken met behulp van REST API
description: In dit artikel vindt u informatie over het maken en beheren van back-upbeleid (planning en retentie) met behulp van REST API.
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: 5ffc4115-0ae5-4b85-a18c-8a942f6d4870
ms.openlocfilehash: e4e6f5b5cf28c3830a91a494ea60680eee1546f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89179603"
---
# <a name="create-azure-recovery-services-backup-policies-using-rest-api"></a>Back-upbeleid voor Azure Recovery Services maken met behulp van REST API

De stappen voor het maken van een back-upbeleid voor een Azure Recovery Services-kluis worden beschreven in het [beleid rest API document](/rest/api/backup/protectionpolicies/createorupdate). We gebruiken dit document als referentie voor het maken van een beleid voor Azure VM-back-up.

## <a name="create-or-update-a-policy"></a>Een beleid maken of bijwerken

Als u een Azure Backup beleid wilt maken of bijwerken, gebruikt u de volgende *put* -bewerking

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupPolicies/{policyName}?api-version=2019-05-13
```

De `{policyName}` en `{vaultName}` zijn aanwezig in de URI. Meer informatie vindt u in de hoofd tekst van de aanvraag.

## <a name="create-the-request-body"></a>De aanvraag tekst maken

Als u bijvoorbeeld een beleid voor Azure VM backup wilt maken, volgt u de onderdelen van de hoofd tekst van de aanvraag.

|Name  |Vereist  |Type  |Beschrijving  |
|---------|---------|---------|---------|
|properties     |   Waar      |  ProtectionPolicy:[AzureIaaSVMProtectionPolicy](/rest/api/backup/protectionpolicies/createorupdate#azureiaasvmprotectionpolicy)      | ProtectionPolicyResource-eigenschappen        |
|tags     |         | Object        |  Resourcetags       |

Raadpleeg het [back-upbeleid rest API document](/rest/api/backup/protectionpolicies/createorupdate)voor een volledige lijst met definities in de hoofd tekst van de aanvraag.

### <a name="example-request-body"></a>Voorbeeld aanvraag tekst

De volgende aanvraag tekst definieert een back-upbeleid voor back-ups van virtuele Azure-machines.

Het beleid vertelt:

- Neem elke maandag, woensdag, donderdag om 10:00 AM Pacific Standard Time een wekelijkse back-up.
- Bewaar de back-ups die zijn gemaakt op elke maandag, woensdag, donderdag gedurende één week.
- Bewaar de back-ups die zijn gemaakt op elke eerste woensdag en derde donderdag van een maand voor twee maanden (overschrijft de eerdere Bewaar voorwaarden, indien van toepassing).
- Bewaar de back-ups die zijn gemaakt op de vierde maandag en vierde donderdag in februari en november voor vier jaar (overschrijft de eerdere Bewaar voorwaarden, indien van toepassing).

```json
{
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "timeZone": "Pacific Standard Time",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ]
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    }
  }
}
```

> [!IMPORTANT]
> De tijd notaties voor planning en retentie ondersteunen alleen DateTime. Ze ondersteunen geen tijd notatie.

## <a name="responses"></a>Antwoorden

Het maken/bijwerken van het back-upbeleid is een [asynchrone bewerking](../azure-resource-manager/management/async-operations.md). Dit betekent dat met deze bewerking een andere bewerking wordt gemaakt die afzonderlijk moet worden bijgehouden.

Er worden twee antwoorden geretourneerd: 202 (geaccepteerd) wanneer een andere bewerking wordt gemaakt en vervolgens 200 (OK) wanneer deze bewerking is voltooid.

|Naam  |Type  |Beschrijving  |
|---------|---------|---------|
|200 OK     |    [Beveiligings PolicyResource](/rest/api/backup/protectionpolicies/createorupdate#protectionpolicyresource)     |  OK       |
|202 geaccepteerd     |         |     Geaccepteerd    |

### <a name="example-responses"></a>Voorbeeld reacties

Zodra u de *put* -aanvraag voor het maken of bijwerken van beleid hebt verzonden, is het eerste antwoord 202 (geaccepteerd) met een locatie header of Azure-async-header.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operations/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Volg vervolgens de resulterende bewerking met behulp van de locatie header of de Azure-AsyncOperation koptekst met een eenvoudige *Get* -opdracht.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2019-05-13
```

Zodra de bewerking is voltooid, wordt 200 (OK) geretourneerd met de beleids inhoud in de hoofd tekst van het antwoord.

```json
{
  "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1",
  "name": "testPolicy1",
  "type": "Microsoft.RecoveryServices/vaults/backupPolicies",
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ],
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleWeeklyFrequency": 0
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    },
    "timeZone": "Pacific Standard Time",
    "protectedItemsCount": 0
  }
}
```

Als er al een beleid wordt gebruikt om een item te beveiligen, wordt bij elke update in het beleid de beveiliging voor alle gekoppelde items [gewijzigd](backup-azure-arm-userestapi-backupazurevms.md#changing-the-policy-of-protection) .

## <a name="next-steps"></a>Volgende stappen

[Schakel de beveiliging voor een niet-beveiligde Azure-vm in](backup-azure-arm-userestapi-backupazurevms.md).

Raadpleeg de volgende documenten voor meer informatie over de Azure Backup REST-Api's:

- [REST API Azure Recovery Services provider](/rest/api/recoveryservices/)
- [Aan de slag gaan met Azure REST API](/rest/api/azure/)
