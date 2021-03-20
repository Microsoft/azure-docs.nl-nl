---
title: 'Zelfstudie: SAP HANA-database herstellen in Azure met behulp van CLI'
description: In deze zelfstudie leert u hoe u SAP HANA-databases die worden uitgevoerd op een virtuele Azure-machine herstelt vanuit een Azure Backup Recovery Services-kluis met behulp van Azure CLI.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0e524bfe090f0d67b76c13e876f44e83986aeb9e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91334800"
---
# <a name="tutorial-restore-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Zelfstudie: SAP HANA-databases op een virtuele Azure-machine herstellen met behulp van Azure CLI

Azure CLI wordt gebruikt om Azure-resources te maken en te beheren vanaf de opdrachtregel of door middel van scripts. Deze documentatie bevat informatie over het herstellen van een back-up van een SAP HANA-database op een Azure-VM met behulp van Azure CLI. U kunt deze stappen ook uitvoeren met [Azure Portal](./sap-hana-db-restore.md).

Gebruik [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) voor het uitvoeren van CLI-opdrachten.

Aan het einde van deze zelfstudie kunt u:

> [!div class="checklist"]
>
> * Herstelpunten weergeven voor een back-up van een database
> * Een database herstellen

In deze zelfstudie wordt ervan uitgegaan dat u een SAP HANA-database hebt die wordt uitgevoerd op een Azure-VM en waarvan een back-up is gemaakt met behulp van Azure Backup. Als u [Een back-up maken van een SAP HANA-database in Azure met behulp van CLI](tutorial-sap-hana-backup-cli.md) hebt gebruikt om een back-up te maken van uw SAP HANA-database, gebruikt u de volgende resources:

* een resourcegroep met de naam *saphanaResourceGroup*;
* een kluis met de naam *saphanaVault*
* een beveiligde container met de naam *VMAppContainer;Compute;saphanaResourceGroup;saphanaVM*
* een database/item waarvan een back-up is gemaakt met de naam *saphanadatabase; hxe; hxe*
* resources in de locatie *westus2*

## <a name="view-restore-points-for-a-backed-up-database"></a>Herstelpunten weergeven voor een back-up van een database

Als u de lijst met alle herstelpunten voor een database wilt weergeven, gebruikt u de cmdlet [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-show-log-chain) als volgt:

```azurecli-interactive
az backup recoverypoint list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
   --output table
```

De lijst met herstelpunten ziet er als volgt uit:

```output
Name                      Time                               BackupManagementType   Item Name               RecoveryPointType
-------------------       ---------------------------------  ---------------------  ----------------------  ------------------
7660777527047692711       2019-12-10T04:00:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Full
7896624824685666836       2019-12-15T10:33:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Differential
DefaultRangeRecoveryPoint                                    AzureWorkload          SAPHanaDtabase;hxe;hxe  Log
```

Zoals u kunt zien, bevat de bovenstaande lijst drie herstelpunten: één voor elke volledige, differentiële en logboekback-up.

>[!NOTE]
>U kunt ook de begin- en eindpunten van elke niet-verbroken logboekback-upketen weergeven met behulp van de cmdlet [az backup recoverypoint show-log-chain](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-show-log-chain).

## <a name="prerequisites-to-restore-a-database"></a>Vereisten voor het herstellen van een database

Zorg ervoor dat aan de volgende vereisten wordt voldaan voordat u een database herstelt:

* U kunt de database alleen herstellen naar een SAP HANA-exemplaar dat zich in dezelfde regio bevindt
* Het doelexemplaar moet zijn geregistreerd bij dezelfde kluis als de bron
* Azure Backup kan niet twee verschillende SAP HANA-exemplaren op dezelfde VM herkennen. Daarom is het terugzetten van gegevens van het ene naar het andere exemplaar op dezelfde VM niet mogelijk.

## <a name="restore-a-database"></a>Een database herstellen

Azure Backup kan SAP HANA-databases die op virtuele Azure-machines worden uitgevoerd als volgt herstellen:

* Herstel naar een specifieke datum of tijd (op de seconde) met behulp van logboekback-ups. Azure Backup bepaalt automatisch de juiste keten van volledige back-ups, differentiële back-ups en logboekback-ups die nodig zijn voor het herstellen op basis van de geselecteerde tijd.
* Herstel naar een specifieke volledige of differentiële back-up om naar een specifiek herstelpunt te herstellen.

Als u een database wilt herstellen, gebruikt u de cmdlet [az restore restore-azurewl](/cli/azure/backup/restore#az-backup-restore-restore-azurewl), die een herstelconfiguratieobject als een van de invoerwaarden vereist. Dit object kan worden gegenereerd met behulp van de cmdlet [az backup recoveryconfig show](/cli/azure/backup/recoveryconfig#az-backup-recoveryconfig-show). Het herstelconfiguratieobject bevat alle details voor het uitvoeren van een herstelbewerking. Een van deze is de herstelmodus: **OriginalWorkloadRestore** of **AlternateWorkloadRestore**.

>[!NOTE]
> **OriginalWorkloadRestore**: de gegevens herstellen naar hetzelfde exemplaar van SAP HANA als de oorspronkelijke bron. Met deze optie wordt de oorspronkelijke database overschreven. <br>
> **AlternateWorkloadRestore**: de database herstellen naar een alternatieve locatie en de oorspronkelijke brondatabase behouden.

## <a name="restore-to-alternate-location"></a>Herstellen naar alternatieve locatie

Als u een database naar een andere locatie wilt herstellen, gebruikt u **AlternateWorkloadRestore** als herstelmodus. Vervolgens moet u het herstelpunt kiezen. Dit kan een eerder punt in de tijd zijn of een van de vorige herstelpunten.

In deze zelfstudie gaat u naar een eerder herstelpunt herstellen. [Bekijk de lijst met herstelpunten](#view-restore-points-for-a-backed-up-database) voor de database en kies het punt waarnaar u wilt herstellen. In deze zelfstudie wordt het herstelpunt met de naam *7660777527047692711* gebruikt.

Met de bovenstaande herstelpuntnaam en de herstelmodus kunt u het herstelconfiguratieobject maken met behulp van de cmdlet [az backup recoveryconfig show](/cli/azure/backup/recoveryconfig#az-backup-recoveryconfig-show). Laten we eens kijken wat de betekenis is van de overige parameters in deze cmdlet:

* **--target-item-name**: dit is de naam die wordt gebruikt door de herstelde database. In dit geval hebben we de naam *restored_database* gebruikt.
* **--target-server-name**: dit is de naam van een SAP HANA-server die is geregistreerd bij een Recovery Services-kluis en zich in dezelfde regio bevindt als de database die moet worden hersteld. Voor deze zelfstudie herstelt u de database op dezelfde SAP HANA-server die we hebben beveiligd, met de naam *hxehost*.
* **--target-server-type**: voor het terugzetten van SAP HANA-databases, moet **HanaInstance** worden gebruikt.

```azurecli-interactive

az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode AlternateWorkloadRestore \
    --rp-name 7660777527047692711 \
    --target-item-name restored_database \
    --target-server-name hxehost \
    --target-server-type HANAInstance \
    --workload-type SAPHANA \
    --output json
```

De reactie op de bovenstaande query is een herstelconfiguratieobject dat er ongeveer als volgt uitziet:

```output
{"restore_mode": "AlternateLocation", "container_uri": " VMAppContainer;Compute;saphanaResourceGroup;saphanaVM ", "item_uri": "SAPHanaDatabase;hxe;hxe", "recovery_point_id": "7660777527047692711", "item_type": "SAPHana", "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm", "database_name": null, "container_id": null, "alternate_directory_paths": null}
```

Nu voert u de cmdlet [az restore restore-azurewl](/cli/azure/backup/restore#az-backup-restore-restore-azurewl) uit om de database te herstellen. Om deze opdracht te gebruiken, gaan we de bovenstaande JSON-uitvoer invoeren die is opgeslagen in een bestand met de naam *recoveryconfig.json*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

De uitvoer ziet er als volgt uit:

```output
Name                                  Resource
------------------------------------  -------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

Het antwoord is de taaknaam. Deze taaknaam kan worden gebruikt voor het zoeken van de taakstatus met behulp van de cmdlet [az backup job show](/cli/azure/backup/job#az-backup-job-show).

## <a name="restore-and-overwrite"></a>Herstellen en overschrijven

Om te herstellen naar de oorspronkelijke locatie, gebruiken we **OrignialWorkloadRestore** als herstelmodus. Vervolgens moet u het herstelpunt kiezen. Dit kan een eerder punt in de tijd zijn of een van de vorige herstelpunten.

Voor deze zelfstudie kiezen we het vorige tijdstip van '28-11-2019-09:53:00' om naar te herstellen. U kunt dit herstelpunt opgeven in de volgende indelingen: dd-mm-jjjj, dd-mm-jjjj-uu: mm:ss. Als u een geldig tijdstip wilt kiezen om naar te herstellen, gebruikt u de cmdlet [az backup recoverypoint show-log-chain](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-show-log-chain), waarin de intervallen van niet-verbroken logboekketenback-ups worden weergegeven.

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode OriginalWorkloadRestore \
    --log-point-in-time 28-11-2019-09:53:00 \
    --output json
```

De reactie op de bovenstaande query is een herstelconfiguratieobject dat er als volgt uitziet:

```output
{"restore_mode": "OriginalLocation", "container_uri": " VMAppContainer;Compute;saphanaResourceGroup;saphanaVM ", "item_uri": "SAPHanaDatabase;hxe;hxe", "recovery_point_id": "DefaultRangeRecoveryPoint", "log_point_in_time": "28-11-2019-09:53:00", "item_type": "SAPHana", "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm", "database_name": null, "container_id": null, "alternate_directory_paths": null}"
```

Nu voert u de cmdlet [az restore restore-azurewl](/cli/azure/backup/restore#az-backup-restore-restore-azurewl) uit om de database te herstellen. Om deze opdracht te gebruiken, gaan we de bovenstaande JSON-uitvoer invoeren die is opgeslagen in een bestand met de naam *recoveryconfig.json*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

De uitvoer ziet er als volgt uit:

```output
Name                                  Resource
------------------------------------  --------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

Het antwoord is de taaknaam. Deze taaknaam kan worden gebruikt voor het zoeken van de taakstatus met de cmdlet [az backup job show](/cli/azure/backup/job#az-backup-job-show).

## <a name="restore-as-files"></a>Herstellen als bestanden

Als u de back-upgegevens wilt herstellen als bestanden in plaats van een database, gebruiken we **RestoreAsFiles** als de herstelmodus. Kies vervolgens het herstelpunt. Dit kan een eerder tijdstip of een van de vorige herstelpunten zijn. Zodra de bestanden zijn gedumpt naar een opgegeven pad, kunt u deze bestanden naar elke SAP HANA-machine overbrengen waarop u deze als een database wilt herstellen. Omdat u deze bestanden naar een willekeurige machine kunt verplaatsen, kunt u de gegevens nu in abonnementen en regio's herstellen.

Voor deze zelfstudie kiezen we het vorige tijdstip `28-11-2019-09:53:00` om naar te herstellen en `/home/saphana/restoreasfiles` als locatie voor het dumpen van back-upbestanden op dezelfde SAP HANA-server. U kunt dit herstelpunt opgeven in de volgende indelingen: **dd-mm-jjjj** of **dd-mm-jjjj-uu: mm: ss**. Als u een geldig tijdstip wilt kiezen om naar te herstellen, gebruikt u de cmdlet [az backup recoverypoint show-log-chain](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-show-log-chain), waarin de intervallen van niet-verbroken logboekketenback-ups worden weergegeven.

Met behulp van de bovenstaande herstelpuntnaam en de herstelmodus kunt u het herstelconfiguratieobject maken met behulp van de cmdlet [az backup recoveryconfig show](/cli/azure/backup/recoveryconfig#az-backup-recoveryconfig-show). Laten we eens kijken wat de betekenis is van de overige parameters in deze cmdlet:

* **--target-container-name**: dit is de naam van een SAP HANA-server die is geregistreerd bij een Recovery Services-kluis en zich in dezelfde regio bevindt als de database die moet worden hersteld. Voor deze zelfstudie herstelt u de database als bestanden op dezelfde SAP HANA-server die we hebben beveiligd, met de naam *hxehost*.
* **--rp-name**: voor een herstel naar een bepaald tijdstip, wordt de naam van het herstelpunt **DefaultRangeRecoveryPoint**

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode RestoreAsFiles \
    --log-point-in-time 28-11-2019-09:53:00 \
    --rp-name DefaultRangeRecoveryPoint \
    --target-container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --filepath /home/saphana/restoreasfiles \
    --output json
```

De reactie op de bovenstaande query is een herstelconfiguratieobject dat er als volgt uitziet:

```output
{
  "alternate_directory_paths": null,
  "container_id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.RecoveryServices/vaults/SAPHANAVault/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SAPHANA;hanamachine",
  "container_uri": "VMAppContainer;compute;saphana;hanamachine",
  "database_name": null,
  "filepath": "/home/",
  "item_type": "SAPHana",
  "item_uri": "SAPHanaDatabase;hxe;hxe",
  "log_point_in_time": "04-07-2020-09:53:00",
  "recovery_mode": "FileRecovery",
  "recovery_point_id": "DefaultRangeRecoveryPoint",
  "restore_mode": "AlternateLocation",
  "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/hanamachine"
}
```

Nu voert u de cmdlet [az restore restore-azurewl](/cli/azure/backup/restore#az-backup-restore-restore-azurewl) uit om de database als bestanden te herstellen. Om deze opdracht te gebruiken, voeren we de bovenstaande JSON-uitvoer in die is opgeslagen in een bestand met de naam *recoveryconfig.json*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output json
```

De uitvoer ziet er als volgt uit:

```output
{
  "eTag": null,
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/SAPHANARESOURCEGROUP/providers/Microsoft.RecoveryServices/vaults/SAPHANAVault/backupJobs/608e737e-c001-47ca-8c37-57d909c8a704",
  "location": null,
  "name": "608e737e-c001-47ca-8c37-57d909c8a704",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "7ddd3c3a-c0eb-11ea-a5f8-54ee75ec272a",
    "backupManagementType": "AzureWorkload",
    "duration": "0:00:01.781847",
    "endTime": null,
    "entityFriendlyName": "HXE [hxehost]",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {
        "Job Type": "Restore as files"
      },
      "tasksList": [
        {
          "status": "InProgress",
          "taskId": "Transfer data from vault"
        }
      ]
    },
    "jobType": "AzureWorkloadJob",
    "operation": "Restore",
    "startTime": "2020-07-08T07:20:29.336434+00:00",
    "status": "InProgress",
    "workloadType": "SAPHanaDatabase"
  },
  "resourceGroup": "saphanaResourceGroup",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

Het antwoord is de taaknaam. Deze taaknaam kan worden gebruikt voor het zoeken van de taakstatus met de cmdlet [az backup job show](/cli/azure/backup/job#az-backup-job-show).

De bestanden die worden gedumpt in de doelcontainer zijn:

* Back-upbestanden van database
* Catalogusbestanden
* JSON-metagegevensbestanden (voor elk back-upbestand dat betrokken is)

Normaal gesproken maakt een netwerksharepad of pad van een gekoppelde Azure-bestandsshare indien opgegeven als doelpad toegang tot deze bestanden door andere computers in hetzelfde netwerk of met dezelfde Azure-bestandsshare die eraan is gekoppeld eenvoudiger.

>[!NOTE]
>Als u de back-upbestanden van de database wilt herstellen op een Azure-bestandsshare die is gekoppeld aan de geregistreerde doel-VM, moet u ervoor zorgen dat het hoofdaccount lees- en schrijfmachtigingen heeft voor de Azure-bestandsshare.

Op basis van het type herstelpunt dat is gekozen (**tijdstip** of **volledig en differentieel**), worden een of meer mappen weergegeven die in het doelpad zijn gemaakt. Een van de mappen met de naam `Data_<date and time of restore>` bevat de volledige en differentiële back-ups en de andere map met de naam `Log` bevat de logboekback-ups.

Verplaats deze teruggezette bestanden naar de SAP HANA-server waar u ze wilt herstellen als een database. Volg daarna deze stappen om de database te herstellen:

1. Stel machtigingen in voor de map waarin de back-upbestanden zijn opgeslagen met behulp van de volgende opdracht:

    ```bash
    chown -R <SID>adm:sapsys <directory>
    ```

1. Voer de volgende set opdrachten uit als `<SID>adm`

    ```bash
    su - <sid>adm
    ```

1. Genereer het catalogusbestand voor herstel. Extraheer de **BackupId** uit het JSON-metagegevensbestand voor de volledige back-up, die later wordt gebruikt in de herstelbewerking. Zorg ervoor dat de volledige en logboekback-ups zich in verschillende mappen bevinden en verwijder de catalogusbestanden en JSON-bestanden met metagegevens in deze mappen.

    ```bash
    hdbbackupdiag --generate --dataDir <DataFileDir> --logDirs <LogFilesDir> -d <PathToPlaceCatalogFile>
    ```

    In de bovenstaande opdracht:

    * `<DataFileDir>`: de map die de volledige back-ups bevat
    * `<LogFilesDir>`: de map die de logboekback-ups bevat
    * `<PathToPlaceCatalogFile>`: de map waarin het gegenereerde catalogusbestand moet worden geplaatst

1. Herstel met behulp van het zojuist gegenereerde catalogusbestand via HANA Studio of voer de HDBSQL-herstelquery uit met deze zojuist gegenereerde catalogus. HDBSQL-query's worden hieronder weergegeven:

    * Ga als volgt te werk om te herstellen naar een specifiek tijdstip:

        Als u een nieuwe herstelde database maakt, voert u de HDBSQL-opdracht uit om een nieuwe database te maken `<DatabaseName>` en vervolgens stopt u de database voor herstel. Als u echter alleen een bestaande database herstelt, voert u de HDBSQL-opdracht uit om de database te stoppen.

        Voer vervolgens de volgende opdracht uit om de database te herstellen:

        ```hdbsql
        RECOVER DATABASE FOR <DatabaseName> UNTIL TIMESTAMP '<TimeStamp>' CLEAR LOG USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING LOG PATH (' <LogFileDir>') USING DATA PATH ('<DataFileDir>') USING BACKUP_ID <BackupIdFromJsonFile> CHECK ACCESS USING FILE
        ```

        * `<DatabaseName>`: de naam van de nieuwe database of bestaande database die u wilt herstellen
        * `<Timestamp>`: exacte tijdstempel van het herstel naar een bepaald tijdstip
        * `<DatabaseName@HostName>`: de naam van de database waarvan de back-up wordt gebruikt voor herstel en de **host**/SAP HANA-servernaam waarop deze database zich bevindt. Met de optie `USING SOURCE <DatabaseName@HostName>` geeft u op dat de gegevensback-up (gebruikt voor herstel) van een database met een andere SID of een andere naam dan de SAP HANA-doelmachine is. Dit hoeft dus niet te worden opgegeven voor herstelbewerkingen die worden uitgevoerd op dezelfde HANA-server als die waarop de back-up is gemaakt.
        * `<PathToGeneratedCatalogInStep3>`: het pad naar het catalogusbestand dat in **stap 3** is gegenereerd
        * `<DataFileDir>`: de map die de volledige back-ups bevat
        * `<LogFilesDir>`: de map die de logboekback-ups bevat
        * `<BackupIdFromJsonFile>`: de **BackupId** die in **stap 3** is geëxtraheerd

    * Ga als volgt te werk om te herstellen naar een bepaalde volledige of differentiële back-up:

        Als u een nieuwe herstelde database maakt, voert u de HDBSQL-opdracht uit om een nieuwe database te maken `<DatabaseName>` en vervolgens stopt u de database voor herstel. Als u echter alleen een bestaande database herstelt, voert u de HDBSQL-opdracht uit om de database te stoppen:

        ```hdbsql
        RECOVER DATA FOR <DatabaseName> USING BACKUP_ID <BackupIdFromJsonFile> USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING DATA PATH ('<DataFileDir>')  CLEAR LOG
        ```

        * `<DatabaseName>`: de naam van de nieuwe database of bestaande database die u wilt herstellen
        * `<Timestamp>`: het exacte tijdstempel van het herstel naar een bepaald tijdstip
        * `<DatabaseName@HostName>`: de naam van de database waarvan de back-up wordt gebruikt voor herstel en de **host** /SAP HANA-servernaam waarop deze database zich bevindt. Met de optie `USING SOURCE <DatabaseName@HostName>` geeft u op dat de gegevensback-up (gebruikt voor herstel) van een database met een andere SID of een andere naam dan de SAP HANA-doelmachine is. Dit hoeft dus niet te worden opgegeven voor herstelbewerkingen die worden uitgevoerd op dezelfde HANA-server als die waarop de back-up is gemaakt.
        * `<PathToGeneratedCatalogInStep3>`: het pad naar het catalogusbestand dat in **stap 3** is gegenereerd
        * `<DataFileDir>`: de map die de volledige back-ups bevat
        * `<LogFilesDir>`: de map die de logboekback-ups bevat
        * `<BackupIdFromJsonFile>`: de **BackupId** die in **stap 3** is geëxtraheerd

## <a name="next-steps"></a>Volgende stappen

* Voor informatie over het beheren van SAP HANA-databases waarvan een back-up wordt gemaakt met behulp van Azure CLI, gaat u verder met de zelfstudie [Een SAP HANA-database beheren in Azure VM met behulp van CLI](tutorial-sap-hana-backup-cli.md)

* Als u wilt weten hoe u met de Azure-portal een SAP HANA-database herstelt die op een virtuele Azure-machine wordt uitgevoerd, raadpleegt u [SAP HANA-databases op virtuele Azure-machines herstellen](./sap-hana-db-restore.md)
