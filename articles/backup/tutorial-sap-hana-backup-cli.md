---
title: 'Zelfstudie: back-ups van de SAP HANA-database in Azure maken met behulp van Azure CLI'
description: In deze zelfstudie ontdekt u hoe u een back-up maakt van SAP HANA-databases die op een virtuele Azure-machine worden uitgevoerd, naar een Azure Backup Recovery Services-kluis met behulp van Azure CLI.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: f146bed9ee607fe1b1b6062f9fe372fbb9b9ba6a
ms.sourcegitcommit: 6e2d37afd50ec5ee148f98f2325943bafb2f4993
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/23/2020
ms.locfileid: "97746742"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Zelfstudie: Back-ups maken van SAP HANA-databases in een virtuele Azure-machine met behulp van Azure CLI

Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdrachtregel of door middel van scripts. In deze documentatie leest u hoe u een back-up maakt van een SAP HANA-database en on-demand back-ups activeert met behulp van Azure CLI. U kunt deze stappen ook uitvoeren met [Azure Portal](./backup-azure-sap-hana-database.md).

In dit document wordt ervan uitgegaan dat er al een SAP HANA-database is geïnstalleerd op een virtuele Azure-machine. (U kunt ook [een virtuele machine maken met behulp van Azure CLI](../virtual-machines/linux/quick-create-cli.md)). Aan het einde van deze zelfstudie kunt u:

> [!div class="checklist"]
>
> * Een Recovery Services-kluis maken
> * Een SAP HANA-exemplaar registreren en database(s) op dit exemplaar detecteren
> * Back-up inschakelen op een SAP HANA-database
> * Een on-demand back-up activeren

Bekijk de [scenario's die we momenteel ondersteunen](./sap-hana-backup-support-matrix.md#scenario-support) voor SAP HANA.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Voor deze zelfstudie is versie 2.0.30 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

Een Recovery Services-kluis is een logische container waarin de back-upgegevens voor elke beveiligde resource worden opgeslagen, zoals virtuele Azure-machines, of workloads die op virtuele Azure-machines worden uitgevoerd, zoals SQL- of HANA-databases. Wanneer de back-uptaak voor een beveiligde resource wordt uitgevoerd, wordt er binnen de Recovery Services-kluis een herstelpunt gemaakt. U kunt vervolgens een van deze herstelpunten gebruiken om gegevens voor dat tijdstip te herstellen.

Maak een Recovery Services-kluis met [az backup vault create](/cli/azure/backup/vault#az-backup-vault-create). Geef de resourcegroep en locatie op van de VM die u wilt beveiligen. Ontdek hoe u een virtuele machine maakt met behulp van Azure CLI met deze [Quickstart over virtuele machines](../virtual-machines/linux/quick-create-cli.md).

Voor deze zelfstudie gebruiken we de volgende onderdelen:

* een resourcegroep met de naam *saphanaResourceGroup*;
* een virtuele machine met de naam *saphanaVM*; en
* resources in de locatie *westus2*.

We maken een kluis met de naam *saphanaVault*.

```azurecli-interactive
az backup vault create --resource-group saphanaResourceGroup \
    --name saphanaVault \
    --location westus2
```

De Recovery Services-kluis is standaard ingesteld voor geografisch redundante opslag. In geval van geografisch redundante opslag worden uw back-upgegevens gerepliceerd naar een secundaire Azure-regio die honderden kilometers van de primaire regio is verwijderd. Als de instelling voor opslagredundantie moet worden aangepast, gebruikt u de cmdlet [az backup vault backup-properties set](/cli/azure/backup/vault/backup-properties#az-backup-vault-backup-properties-set).

```azurecli
az backup vault backup-properties set \
    --name saphanaVault  \
    --resource-group saphanaResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

Om te controleren of uw kluis is gemaakt, gebruikt u de cmdlet [az backup vault list](/cli/azure/backup/vault#az-backup-vault-list). U ziet het volgende antwoord:

```output
Location   Name             ResourceGroup
---------  ---------------  -------------  
westus2    saphanaVault     saphanaResourceGroup
```

## <a name="register-and-protect-the-sap-hana-instance"></a>Het SAP HANA-exemplaar registreren en beveiligen

Als u het SAP HANA-exemplaar (de virtuele machine waarop SAP HANA is geïnstalleerd) door de Azure-services wilt laten detecteren, moet een [pre-registratiescript](https://aka.ms/scriptforpermsonhana) op de SAP HANA-machine worden uitgevoerd. Zorg ervoor dat er aan alle [vereisten](./tutorial-backup-sap-hana-db.md#prerequisites) is voldaan voordat u het script gaat uitvoeren. Raadpleeg de sectie [Wat doet het script vóór registratie](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) voor meer informatie over wat u met het script kunt doen.

Zodra het script wordt uitgevoerd, kan het SAP HANA-exemplaar worden geregistreerd bij de Recovery Services-kluis die we eerder hebben gemaakt. U kunt het exemplaar registreren met behulp van de cmdlet [az backup container register](/cli/azure/backup/container#az-backup-container-register). *VMResourceId* is de resource-id van de virtuele machine die u hebt gemaakt om SAP HANA te installeren.

```azurecli-interactive
az backup container register --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --location westus2 \
    --workload-type SAPHANA \
    --backup-management-type AzureWorkload \
    --resource-id VMResourceId
```

>[!NOTE]
>Als de VM zich niet in dezelfde resourcegroep bevindt als de kluis, verwijst *saphanaResourceGroup* naar de resourcegroep waarin de kluis is gemaakt.

Door het SAP HANA-exemplaar te registreren, worden automatisch alle huidige databases gedetecteerd. Als u echter nieuwe databases wilt detecteren die wellicht in de toekomst worden toegevoegd, raadpleegt u de sectie [Nieuwe databases detecteren die aan het geregistreerde SAP HANA-exemplaar zijn toegevoegd](tutorial-sap-hana-manage-cli.md#protect-new-databases-added-to-an-sap-hana-instance).

Als u wilt controleren of het SAP HANA-exemplaar is geregistreerd bij uw kluis, gebruikt u de cmdlet [az backup container list](/cli/azure/backup/container#az-backup-container-list). U ziet het volgende antwoord:

```output
Name                                                    Friendly Name    Resource Group        Type           Registration Status
------------------------------------------------------  --------------   --------------------  ---------      ----------------------
VMAppContainer;Compute;saphanaResourceGroup;saphanaVM   saphanaVM        saphanaResourceGroup  AzureWorkload  Registered
```

>[!NOTE]
> De kolom 'naam' in de bovenstaande uitvoer verwijst naar de containernaam. Deze containernaam wordt in de volgende secties gebruikt om back-ups in te schakelen en te activeren. In dit geval is dat *VMAppContainer;Compute;saphanaResourceGroup;saphanaVM*.

## <a name="enable-backup-on-sap-hana-database"></a>Back-ups inschakelen in een SAP HANA-database

De cmdlet [az backup protectable-item list](/cli/azure/backup/protectable-item#az-backup-protectable-item-list) vermeldt alle databases die worden gedetecteerd in het SAP HANA-exemplaar dat u in de vorige stap hebt geregistreerd.

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

De database waarvan u een back-up wilt maken, staat in deze lijst. Deze ziet er als volgt uit:

```output
Name                           Protectable Item Type    ParentName    ServerName    IsProtected
-----------------------------  ----------------------   ------------  -----------   ------------
saphanasystem;hxe              SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb   SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;hxe        SAPHanaDatabase          HXE           hxehost       NotProtected
```

Zoals u in de bovenstaande uitvoer ziet, is de SID van het SAP HANA-systeem HXE. In deze zelfstudie gaan we een back-up configureren voor de *saphanadatabase;hxe;hxe*-database die op de *hxehost*-server staat.

Om back-ups in een database stuk voor stuk te beveiligen en te configureren, gebruiken we de cmdlet [az backup protection enable-for-azurewl](/cli/azure/backup/protection#az-backup-protection-enable-for-azurewl). Geef de naam op van het beleid dat u wilt gebruiken. Als u een beleid wilt maken met behulp van CLI, gebruikt u de cmdlet [az backup policy create](/cli/azure/backup/policy#az-backup-policy-create). Voor deze zelfstudie gebruiken we het *saphanaPolicy*-beleid.

```azurecli-interactive
az backup protection enable-for-azurewl --resource-group saphanaResourceGroup \
    --policy-name saphanaPolicy \
    --protectable-item-name "saphanadatabase;hxe;hxe"  \
    --protectable-item-type SAPHANADatabase \
    --server-name hxehost \
    --workload-type SAPHANA \
    --output table
```

U kunt controleren of de bovenstaande back-upconfiguratie volledig is met behulp van de cmdlet [az backup job list](/cli/azure/backup/job#az-backup-job-list). De uitvoer ziet er als volgt uit:

```output
Name                                  Operation         Status     Item Name   Start Time UTC
------------------------------------  ---------------   ---------  ----------  -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup   Completed  hxe         2019-12-03T03:09:210831+00:00  
```

De cmdlet [az backup job list](/cli/azure/backup/job#az-backup-job-list) vermeldt alle back-uptaken (gepland of on-demand) die zijn uitgevoerd of die momenteel worden uitgevoerd op de beveiligde database, naast andere bewerkingen zoals registreren, back-ups configureren en het verwijderen van back-upgegevens.

>[!NOTE]
>Azure Backup past de tijd niet automatisch aan de zomertijd aan wanneer u een back-up van een SAP HANA-database maakt die op een virtuele Azure-machine wordt uitgevoerd.
>
>Pas het beleid waar nodig handmatig aan.

## <a name="trigger-an-on-demand-backup"></a>Een on-demand back-up activeren

Waar u in de bovenstaande sectie informatie kreeg over het configureren van een geplande back-up, gaan we het in deze sectie hebben over het activeren van een on-demand back-up. Hiervoor gebruiken we de cmdlet [az backup protection backup-now](/cli/azure/backup/protection#az-backup-protection-backup-now).

>[!NOTE]
> Het retentiebeleid van een on-demand back-up wordt bepaald door het onderliggende retentiebeleid voor de database.

```azurecli-interactive
az backup protection backup-now --resource-group saphanaResourceGroup \
    --item-name saphanadatabase;hxe;hxe \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --backup-type Full
    --retain-until 01-01-2040
    --output table
```

De uitvoer ziet er als volgt uit:

```output
Name                                  ResourceGroup
------------------------------------  -------------
e0f15dae-7cac-4475-a833-f52c50e5b6c3  saphanaResourceGroup
```

Het antwoord is de taaknaam. Deze taaknaam kan worden gebruikt voor het zoeken van de taakstatus met de cmdlet [az backup job show](/cli/azure/backup/job#az-backup-job-show).

>[!NOTE]
>Naast het plannen van een volledige of gedeeltelijke back-up kunnen ze momenteel ook handmatig worden geactiveerd. Back-ups van logboeken worden automatisch geactiveerd en intern door SAP HANA beheerd.
>
> Incrementele back-ups worden momenteel niet ondersteund door Azure Backup.

## <a name="next-steps"></a>Volgende stappen

* Voor informatie over het herstellen va een SAP HANA-database in een virtuele Azure-machine met behulp van CLI, gaat u verder met de zelfstudie [Een SAP HANA-database in een virtuele Azure-machine herstellen met behulp van CLI](tutorial-sap-hana-restore-cli.md)

* Als u wilt weten hoe u met Azure Portal een back-up maakt van een SAP HANA-database die in een virtuele Azure-machine wordt uitgevoerd, raadpleegt u [Een back-up maken van SAP HANA-databases virtuele Azure-machines](./backup-azure-sap-hana-database.md)
