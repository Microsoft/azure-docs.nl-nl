---
title: 'Zelfstudie: Back-up van SAP HANA-database beheren met CLI'
description: In deze zelfstudie leert u hoe u met behulp van Azure CLI back-ups beheert van SAP HANA-databases die op een Azure-VM worden uitgevoerd.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7090701e3642fd9703737060e0876c8bbfc27994
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765175"
---
# <a name="tutorial-manage-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Zelfstudie: SAP HANA-databases op een Azure-VM beheren met Azure CLI

Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdrachtregel of door middel van scripts. In deze documentatie vindt u informatie over het beheren van een back-up van een SAP HANA-database op een Azure-VM, uitsluitend met behulp van Azure CLI. U kunt deze stappen overigens ook uitvoeren in de [Azure-portal](./sap-hana-db-manage.md).

Gebruik [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) voor het uitvoeren van CLI-opdrachten.

Aan het einde van deze zelfstudie kunt u:

> [!div class="checklist"]
>
> * Taken voor back-up en herstel controleren
> * Nieuwe databases beveiligen die worden toegevoegd aan een SAP HANA-exemplaar
> * Het beleid wijzigen
> * Beveiliging stoppen
> * Beveiliging hervatten

Als u [Een back-up maken van een SAP HANA-database in Azure met behulp van CLI](tutorial-sap-hana-backup-cli.md) hebt gebruikt om een back-up te maken van uw SAP HANA-database, gebruikt u de volgende resources:

* een resourcegroep met de naam *saphanaResourceGroup*;
* een kluis met de naam *saphanaVault*
* een beveiligde container met de naam *VMAppContainer;Compute;saphanaResourceGroup;saphanaVM*
* een database/item waarvan een back-up is gemaakt met de naam *saphanadatabase; hxe; hxe*
* resources in de locatie *westus2*

Met Azure CLI kunt u eenvoudig een SAP HANA-database beheren die wordt uitgevoerd op een Azure-VM waarvan een back-up is gemaakt met Azure Backup. In deze zelfstudie worden de verschillende beheerbewerkingen beschreven.

## <a name="monitor-backup-and-restore-jobs"></a>Taken voor back-up en herstel controleren

Als u voltooide of momenteel actieve taken wilt controleren (back-up of herstel), gebruikt u de cmdlet [az backup job list](/cli/azure/backup/job#az_backup_job_list). Met CLI kunt u ook [een taak onderbreken die momenteel wordt uitgevoerd](/cli/azure/backup/job#az_backup_job_stop) of [wachten tot een taak is voltooid](/cli/azure/backup/job#az_backup_job_wait).

```azurecli-interactive
az backup job list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --output table
```

De uitvoer ziet er ongeveer uit zoals in dit voorbeeld:

```output
Name                                  Operation              Status      Item Name       Start Time UTC
------------------------------------  ---------------        ---------   ----------      -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup        Completed   hxe             2019-12-03T03:09:210831+00:00  
ccdb4dce-8b15-47c5-8c46-b0985352238f  Backup (Full)          Completed   hxe [hxehost]   2019-12-01T10:30:58.867489+00:00
4980af91-1090-49a6-ab96-13bc905a5282  Backup (Differential)  Completed   hxe [hxehost]   2019-12-01T10:36:00.563909+00:00
F7c68818-039f-4a0f-8d73-e0747e68a813  Restore (Log)          Completed   hxe [hxehost]   2019-12-03T05:44:51.081607+00:00
```

## <a name="change-policy"></a>Beleid wijzigen

Als u het onderliggende beleid van een SAP HANA back-upconfiguratie wilt wijzigen, gebruikt u de cmdlet [az backup policy set](/cli/azure/backup/policy#az_backup_policy_set). De parameter name in deze cmdlet verwijst naar het back-upitem waarvan u het beleid wilt wijzigen. Voor deze zelfstudie vervangen we het beleid van onze SAP HANA-database *saphanadatabase;hxe;hxe* door het nieuwe beleid *newsaphanaPolicy*. U kunt een nieuw beleid maken met behulp van de cmdlet [az backup policy create](/cli/azure/backup/policy#az_backup_policy_create).

```azurecli-interactive
az backup item set policy --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name newsaphanaPolicy \
    --name saphanadatabase;hxe;hxe \
```

De uitvoer moet er als volgt uitzien:

```output
Name                                  Resource Group
------------------------------------- --------------
cb110094-9b15-4c55-ad45-6899200eb8dd  SAPHANA
```

## <a name="create-incremental-backup-policy"></a>Incrementele back-upbeleid maken

Als u een incrementeel back-upbeleid wilt maken, voert u de [opdracht az backup policy create uit](/cli/azure/backup/policy#az_backup_policy_create) met de volgende parameters:

* **--backup-management-type** – Azure Workload
* **--workload-type** - SAPHana
* **--name:** de naam van het beleid
* **--policy:** JSON-bestand met de juiste gegevens voor planning en retentie
* **--resource-group** - Resourcegroep van de kluis
* **--vault-name:** naam van de kluis

Voorbeeld:

```azurecli
az backup policy create --resource-group saphanaResourceGroup --vault-name saphanaVault --name sappolicy --backup-management-type AzureWorkload --policy sappolicy.json --workload-type SAPHana
```

Voorbeeld-JSON (sappolicy.jsaan):

```json
  "eTag": null,
  "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/saphanaResourceGroup/providers/Microsoft.RecoveryServices/vaults/saphanaVault/backupPolicies/sappolicy",
  "location": null,
  "name": "sappolicy",
  "properties": {
    "backupManagementType": "AzureWorkload",
    "makePolicyConsistent": null,
    "protectedItemsCount": 0,
    "settings": {
      "isCompression": false,
      "issqlcompression": false,
      "timeZone": "UTC"
    },
    "subProtectionPolicy": [
      {
        "policyType": "Full",
        "retentionPolicy": {
          "dailySchedule": null,
          "monthlySchedule": {
            "retentionDuration": {
              "count": 60,
              "durationType": "Months"
            },
            "retentionScheduleDaily": null,
            "retentionScheduleFormatType": "Weekly",
            "retentionScheduleWeekly": {
              "daysOfTheWeek": [
                "Sunday"
              ],
              "weeksOfTheMonth": [
                "First"
              ]
            },
            "retentionTimes": [
              "2021-01-19T00:30:00+00:00"
            ]
          },
          "retentionPolicyType": "LongTermRetentionPolicy",
          "weeklySchedule": {
            "daysOfTheWeek": [
              "Sunday"
            ],
            "retentionDuration": {
              "count": 104,
              "durationType": "Weeks"
            },
            "retentionTimes": [
              "2021-01-19T00:30:00+00:00"
            ]
          },
          "yearlySchedule": {
            "monthsOfYear": [
              "January"
            ],
            "retentionDuration": {
              "count": 10,
              "durationType": "Years"
            },
            "retentionScheduleDaily": null,
            "retentionScheduleFormatType": "Weekly",
            "retentionScheduleWeekly": {
              "daysOfTheWeek": [
                "Sunday"
              ],
              "weeksOfTheMonth": [
                "First"
              ]
            },
            "retentionTimes": [
              "2021-01-19T00:30:00+00:00"
            ]
          }
        },
        "schedulePolicy": {
          "schedulePolicyType": "SimpleSchedulePolicy",
          "scheduleRunDays": [
            "Sunday"
          ],
          "scheduleRunFrequency": "Weekly",
          "scheduleRunTimes": [
            "2021-01-19T00:30:00+00:00"
          ],
          "scheduleWeeklyFrequency": 0
        }
      },
      {
        "policyType": "Incremental",
        "retentionPolicy": {
          "retentionDuration": {
            "count": 30,
            "durationType": "Days"
          },
          "retentionPolicyType": "SimpleRetentionPolicy"
        },
        "schedulePolicy": {
          "schedulePolicyType": "SimpleSchedulePolicy",
          "scheduleRunDays": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday",
            "Saturday"
          ],
          "scheduleRunFrequency": "Weekly",
          "scheduleRunTimes": [
            "2017-03-07T02:00:00+00:00"
          ],
          "scheduleWeeklyFrequency": 0
        }
      },
      {
        "policyType": "Log",
        "retentionPolicy": {
          "retentionDuration": {
            "count": 15,
            "durationType": "Days"
          },
          "retentionPolicyType": "SimpleRetentionPolicy"
        },
        "schedulePolicy": {
          "scheduleFrequencyInMins": 120,
          "schedulePolicyType": "LogSchedulePolicy"
        }
      }
    ],
    "workLoadType": "SAPHanaDatabase"
  },
  "resourceGroup": "saphanaResourceGroup",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupPolicies"
} 
```

Zodra het beleid is gemaakt, wordt in de uitvoer van de opdracht de beleids-JSON weergegeven die u als parameter hebt doorgegeven tijdens het uitvoeren van de opdracht.

U kunt de volgende sectie van het beleid wijzigen om de gewenste back-upfrequentie en -retentie voor incrementele back-ups op te geven.

Bijvoorbeeld:

```json
{
  "policyType": "Incremental",
  "retentionPolicy": {
    "retentionDuration": {
      "count": 30,
      "durationType": "Days"
    },
    "retentionPolicyType": "SimpleRetentionPolicy"
  },
  "schedulePolicy": {
    "schedulePolicyType": "SimpleSchedulePolicy",
    "scheduleRunDays": [
      "Monday",
      "Tuesday",
      "Wednesday",
      "Thursday",
      "Friday",
      "Saturday"
    ],
    "scheduleRunFrequency": "Weekly",
    "scheduleRunTimes": [
      "2017-03-07T02:00:00+00:00"
    ],
    "scheduleWeeklyFrequency": 0
  }
}
```

Voorbeeld:

Als u alleen op zaterdag incrementele back-ups wilt maken en deze 60 dagen wilt bewaren, moet u de volgende wijzigingen aanbrengen in het beleid:

* Retentie **bijwerkenDuration** aantal tot 60 dagen
* Geef alleen zaterdag op als **ScheduleRunDays**

```json
 {
  "policyType": "Incremental",
  "retentionPolicy": {
    "retentionDuration": {
      "count": 60,
      "durationType": "Days"
    },
    "retentionPolicyType": "SimpleRetentionPolicy"
  },
  "schedulePolicy": {
    "schedulePolicyType": "SimpleSchedulePolicy",
    "scheduleRunDays": [
      "Saturday"
    ],
    "scheduleRunFrequency": "Weekly",
    "scheduleRunTimes": [
      "2017-03-07T02:00:00+00:00"
    ],
    "scheduleWeeklyFrequency": 0
  }
}
```

## <a name="protect-new-databases-added-to-an-sap-hana-instance"></a>Nieuwe databases beveiligen die worden toegevoegd aan een SAP HANA-exemplaar

Wanneer u [een SAP HANA-exemplaar registreert bij een Recovery Services-kluis](tutorial-sap-hana-backup-cli.md#register-and-protect-the-sap-hana-instance), worden alle databases op dit exemplaar automatisch gedetecteerd.

Als er echter later nieuwe databases worden toegevoegd aan het SAP HANA-exemplaar, gebruikt u de cmdlet [az backup protectable-item initialize](/cli/azure/backup/protectable-item#az_backup_protectable_item_initialize). Met deze cmdlet worden de nieuw toegevoegde databases gedetecteerd.

```azurecli-interactive
az backup protectable-item initialize --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --workload-type SAPHANA
```

Gebruik vervolgens de cmdlet [az backup protectable-item list](/cli/azure/backup/protectable-item#az_backup_protectable_item_list) om een lijst weer te geven met alle databases die zijn gedetecteerd op uw SAP HANA-exemplaar. Deze lijst bevat geen databases waarvoor al een back-up is geconfigureerd. Als de database waarvan een back-up moet worden gemaakt, is gedetecteerd, raadpleegt u [Back-up inschakelen voor een SAP HANA-database](tutorial-sap-hana-backup-cli.md#enable-backup-on-sap-hana-database).

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

De nieuwe database waarvan u een back-up wilt maken, staat in deze lijst. Deze ziet er als volgt uit:

```output
Name                            Protectable Item Type    ParentName    ServerName    IsProtected
---------------------------     ----------------------   ------------  -----------   ------------
saphanasystem;hxe               SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb    SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;newhxe      SAPHanaDatabase          HXE           hxehost       NotProtected
```

## <a name="stop-protection-for-an-sap-hana-database"></a>Beveiliging van een SAP HANA-database stoppen

U kunt de beveiliging van een SAP HANA-database op een aantal manieren stoppen:

* Alle toekomstige back-uptaken stoppen en alle herstelpunten verwijderen.
* Alle toekomstige back-uptaken stoppen en de herstelpunten intact laten.

Houd rekening met het volgende als u ervoor kiest de herstelpunten intact te laten:

* Alle herstelpunten blijven voor onbepaalde tijd intact en alle verwijderbewerkingen zullen stoppen bij het stoppen van de beveiliging met behoud van gegevens.
* Er worden kosten in rekening gebracht voor het beveiligde exemplaar en de verbruikte opslag.
* Als u een gegevensbron verwijdert zonder back-ups te stoppen, mislukken nieuwe back-ups.

Laten we de verschillende manieren om de beveiliging te stoppen eens wat nader bestuderen.

### <a name="stop-protection-with-retain-data"></a>Bescherming stoppen met gegevensbehoud

Als u de beveiliging met behoud van gegevens wilt stoppen, gebruikt u de cmdlet [az backup protection disable](/cli/azure/backup/protection#az_backup_protection_disable).

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --output table
```

De uitvoer moet er als volgt uitzien:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

Als u de status van deze bewerking wilt controleren, gebruikt u de cmdlet [az backup job show](/cli/azure/backup/job#az_backup_job_show).

### <a name="stop-protection-without-retain-data"></a>Beveiliging stoppen zonder gegevensbehoud

Als u de beveiliging wilt stoppen zonder gegevens te behouden, gebruikt u de cmdlet [az backup protection disable](/cli/azure/backup/protection#az_backup_protection_disable).

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --delete-backup-data true \
    --output table
```

De uitvoer moet er als volgt uitzien:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

Als u de status van deze bewerking wilt controleren, gebruikt u de cmdlet [az backup job show](/cli/azure/backup/job#az_backup_job_show).

## <a name="resume-protection"></a>Beveiliging hervatten

Wanneer u de beveiliging voor de SAP HANA-database stopt met behoud van gegevens, kunt u de beveiliging later hervatten. Als u de gegevens van de back-up niet bewaart, kunt u de beveiliging niet hervatten.

Gebruik de cmdlet [az backup protection resume](/cli/azure/backup/protection#az_backup_protection_resume) om de beveiliging te hervatten.

```azurecli-interactive
az backup protection resume --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name saphanaPolicy \
    --output table
```

De uitvoer moet er als volgt uitzien:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
b2a7f108-1020-4529-870f-6c4c43e2bb9e  saphanaResourceGroup
```

Als u de status van deze bewerking wilt controleren, gebruikt u de cmdlet [az backup job show](/cli/azure/backup/job#az_backup_job_show).

## <a name="next-steps"></a>Volgende stappen

* Als u wilt weten hoe u met de Azure-portal een back-up maakt van een SAP HANA-database die op een Azure-VM wordt uitgevoerd, raadpleegt u [Een back-up maken van SAP HANA-databases op Azure-VM's](./backup-azure-sap-hana-database.md).

* Als u wilt weten hoe u met de Azure-portal een back-up van een SAP HANA-database beheert die op een Azure-VM wordt uitgevoerd, raadpleegt u [Back-up beheren van SAP HANA-database op Azure-VM's](./sap-hana-db-manage.md).