---
title: Azure Backup met Azure Monitor bewaken
description: Bewaak Azure Backup werk belastingen en maak aangepaste waarschuwingen met behulp van Azure Monitor.
ms.topic: conceptual
ms.date: 06/04/2019
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 1800771bfff0afbcec8440383536734246ea8f5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100580730"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Op schaal controleren met behulp van Azure Monitor

Azure Backup biedt [ingebouwde mogelijkheden voor bewaking en waarschuwingen](backup-azure-monitoring-built-in-monitor.md) in een Recovery Services kluis. Deze mogelijkheden zijn beschikbaar zonder enige extra beheerinfrastructuur. Maar deze ingebouwde service is beperkt in de volgende scenario's:

- Als u gegevens van meerdere Recovery Services-kluizen in abonnementen bewaakt
- Als het voorkeurs meldings kanaal *geen* e-mail adres is
- Als gebruikers waarschuwingen voor meer scenario's willen
- Als u informatie wilt weer geven van een on-premises onderdeel, zoals System Center Data Protection Manager in azure, dat niet wordt weer gegeven in [**back-uptaken**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) of [**back-upwaarschuwingen**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) van de portal

## <a name="using-log-analytics-workspace"></a>Log Analytics werkruimte gebruiken

### <a name="create-alerts-by-using-log-analytics"></a>Waarschuwingen maken met behulp van Log Analytics

In Azure Monitor kunt u uw eigen waarschuwingen maken in een Log Analytics-werk ruimte. In de werk ruimte gebruikt u *Azure-actie groepen* om uw voorkeurs meldings mechanisme te selecteren.

> [!IMPORTANT]
> Zie voor meer informatie over de kosten voor het maken van deze query [Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/).

Open de sectie **logs** van de werk ruimte log Analytics en maak een query voor uw eigen Logboeken. Wanneer u **nieuwe waarschuwings regel** selecteert, wordt de pagina Azure monitor waarschuwings-maken geopend, zoals wordt weer gegeven in de volgende afbeelding.

![Een waarschuwing maken in een Log Analytics-werk ruimte](media/backup-azure-monitoring-laworkspace/custom-alert.png)

Hier is de resource al gemarkeerd als de Log Analytics-werk ruimte en de integratie van de actie groep.

![De pagina Log Analytics waarschuwing: maken](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Waarschuwings voorwaarde

Het definiëren van het kenmerk van een waarschuwing is de trigger voorwaarde. Selecteer **voor waarde** om de Kusto-query automatisch te laden op de pagina **Logboeken** , zoals wordt weer gegeven in de volgende afbeelding. Hier kunt u de voor waarde aan uw behoeften aanpassen. Zie voor [beelden van Kusto-query's](#sample-kusto-queries)voor meer informatie.

![Een waarschuwings voorwaarde instellen](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

Indien nodig kunt u de Kusto-query bewerken. Kies een drempel waarde, punt en frequentie. De drempel waarde bepaalt wanneer de waarschuwing wordt gegenereerd. De periode is het tijd venster waarin de query wordt uitgevoerd. Als de drempel waarde bijvoorbeeld groter is dan 0, de periode 5 minuten is en de frequentie 5 minuten is, voert de regel de query elke vijf minuten uit, waarna de vorige 5 minuten wordt gecontroleerd. Als het aantal resultaten groter is dan 0, ontvangt u een melding via de geselecteerde actie groep.

> [!NOTE]
> Als u de waarschuwings regel één keer per dag wilt uitvoeren, wijzigt u in alle gebeurtenissen/logboeken die zijn gemaakt op de opgegeven dag de waarde van zowel ' period ' als ' frequency ' in 1440, dat wil zeggen 24 uur.

#### <a name="alert-action-groups"></a>Waarschuwings actie groepen

Een actie groep gebruiken om een meldings kanaal op te geven. Voor een overzicht van de beschik bare meldings mechanismen onder **actie groepen**, selecteert u **nieuwe maken**.

![Beschik bare meldings mechanismen in het venster actie groep toevoegen](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

U kunt aan alle vereisten voor waarschuwingen en bewaking voldoen via alleen Log Analytics, of u kunt Log Analytics gebruiken om ingebouwde meldingen aan te vullen.

Zie [logboek waarschuwingen maken, weer geven en beheren met behulp van Azure monitor](../azure-monitor/alerts/alerts-log.md) en [actie groepen maken en beheren in de Azure Portal](../azure-monitor/alerts/action-groups.md)voor meer informatie.

### <a name="sample-kusto-queries"></a>Voor beeld van Kusto-query's

De standaard grafieken bieden u Kusto query's voor basis scenario's waarop u waarschuwingen kunt bouwen. U kunt ook de query's wijzigen om de gegevens op te halen waarvoor u waarschuwingen wilt ontvangen. Plak de volgende voor beelden van Kusto-query's op de **Logboeken** pagina en maak waarschuwingen op de query's:

- Alle geslaagde back-uptaken

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    ````

- Alle mislukte back-uptaken

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Failed"
    ````

- Alle voltooide Azure VM-back-uptaken

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="VM" and BackupManagementType=="IaaSVM"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Alle voltooide SQL-logboek back-uptaken

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup" and JobOperationSubType=="Log"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="SQLDataBase" and BackupManagementType=="AzureWorkload"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Alle geslaagde Azure Backup Agent taken

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="FileFolder" and BackupManagementType=="MAB"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Verbruikte back-upopslag per back-upitem

    ````Kusto
    CoreAzureBackup
    //Get all Backup Items
    | where OperationName == "BackupItem"
    //Get distinct Backup Items
    | distinct BackupItemUniqueId, BackupItemFriendlyName
    | join kind=leftouter
    (AddonAzureBackupStorage
    | where OperationName == "StorageAssociation"
    //Get latest record for each Backup Item
    | summarize arg_max(TimeGenerated, *) by BackupItemUniqueId
    | project BackupItemUniqueId , StorageConsumedInMBs)
    on BackupItemUniqueId
    | project BackupItemUniqueId , BackupItemFriendlyName , StorageConsumedInMBs
    | sort by StorageConsumedInMBs desc
    ````

### <a name="diagnostic-data-update-frequency"></a>Update frequentie van diagnostische gegevens

De diagnostische gegevens van de kluis worden naar de Log Analytics-werk ruimte gepompt met enige vertraging. Elke gebeurtenis arriveert in de Log Analytics werk ruimte van *20 tot 30 minuten* nadat deze is gepusht vanuit de Recovery Services kluis. Hier vindt u meer informatie over de vertraging:

- In alle oplossingen worden de ingebouwde waarschuwingen van de back-upservice gepusht zodra ze zijn gemaakt. Deze worden doorgaans weer gegeven in de Log Analytics-werk ruimte na 20 tot 30 minuten.
- In alle oplossingen worden back-uptaken op aanvraag en herstel taken gepusht zodra deze zijn *voltooid*.
- Voor alle oplossingen behalve SQL backup worden geplande back-uptaken gepusht zodra deze zijn *voltooid*.
- Voor SQL backup, omdat logboek back-ups om de 15 minuten kunnen worden uitgevoerd, worden de gegevens voor alle voltooide geplande back-uptaken, inclusief logboeken, batches en elke 6 uur gepusht.
- Voor alle oplossingen worden andere gegevens, zoals het back-upitem, het beleid, de herstel punten, de opslag, enzovoort, ten minste *één keer per dag* gepusht.
- Een wijziging in de back-upconfiguratie (zoals het wijzigen van beleid of het bewerken van beleid) activeert een push van alle gerelateerde back-upgegevens.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>De activiteiten logboeken van Recovery Services kluis gebruiken

> [!CAUTION]
> De volgende stappen zijn alleen van toepassing op *back-ups van Azure-vm's.* U kunt deze stappen niet gebruiken voor oplossingen zoals de Azure Backup-Agent, SQL-back-ups in azure of Azure Files.

U kunt ook activiteiten Logboeken gebruiken om een melding te ontvangen voor gebeurtenissen zoals het maken van een back-up. Voer de volgende stappen uit om te beginnen:

1. Meld u aan bij de Azure-portal.
2. Open de relevante Recovery Services kluis.
3. Open in de eigenschappen van de kluis de sectie **activiteiten logboek** .

Het juiste logboek identificeren en een waarschuwing maken:

1. Controleer of u activiteiten logboeken ontvangt voor geslaagde back-ups door de filters toe te passen die in de volgende afbeelding worden weer gegeven. Wijzig indien nodig de **time span** -waarde om records weer te geven.

   ![Filteren om activiteiten logboeken te zoeken voor back-ups van Azure-VM'S](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

2. Selecteer de naam van de bewerking om de relevante gegevens weer te geven.
3. Selecteer **nieuwe waarschuwings regel** om de pagina **regel maken** te openen.
4. Maak een waarschuwing door de stappen te volgen in [waarschuwingen voor activiteiten logboek maken, weer geven en beheren met behulp van Azure monitor](../azure-monitor/alerts/alerts-activity-log.md).

   ![Nieuwe waarschuwingsregel](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

Hier is de resource de Recovery Services kluis zelf. Herhaal dezelfde stappen voor alle kluizen waarin u wilt worden gewaarschuwd via activiteiten Logboeken. De voor waarde heeft geen drempel waarde, punt of frequentie omdat deze waarschuwing is gebaseerd op gebeurtenissen. Zodra het relevante activiteiten logboek wordt gegenereerd, wordt de waarschuwing geactiveerd.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Bewaken met behulp van Log Analytics op schaal

U kunt alle waarschuwingen weer geven die zijn gemaakt op basis van activiteiten logboeken en Log Analytics werk ruimten in Azure Monitor. Open het deel venster **waarschuwingen** aan de linkerkant.

Hoewel u meldingen kunt ontvangen via activiteiten logboeken, raden we u ten zeerste aan gebruik te maken van Log Analytics in plaats van activiteiten logboeken voor bewaking op schaal. Waarom?

- **Beperkte scenario's**: meldingen via activiteiten logboeken zijn alleen van toepassing op back-ups van Azure-vm's. De meldingen moeten worden ingesteld voor elke Recovery Services kluis.
- **Passende definitie**: de geplande back-upactiviteit past niet op de nieuwste definitie van activiteiten Logboeken. In plaats daarvan wordt de [resource-logboeken](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)uitgelijnd. Deze uitlijning veroorzaakt onverwachte effecten wanneer de gegevens die door het activiteiten logboek kanaal worden gewijzigd.
- **Problemen met het activiteiten logboek kanaal**: in Recovery Services kluizen worden activiteiten logboeken die zijn gepompt van Azure Backup een nieuw model volgen. Helaas heeft deze wijziging gevolgen voor het genereren van activiteiten Logboeken in Azure Government, Azure Duitsland en Azure China 21Vianet. Als gebruikers van deze Cloud Services waarschuwingen van activiteiten Logboeken in Azure Monitor maken of configureren, worden de waarschuwingen niet geactiveerd. Als een gebruiker in alle open bare Azure-regio's ook [Recovery Services-activiteiten Logboeken in een log Analytics werkruimte verzamelt](../azure-monitor/essentials/activity-log.md), worden deze logboeken niet weer gegeven.

Gebruik een Log Analytics-werk ruimte voor bewaking en waarschuwingen op schaal voor al uw workloads die worden beveiligd door Azure Backup.

## <a name="next-steps"></a>Volgende stappen

Zie [log Analytics gegevens model](backup-azure-reports-data-model.md)voor het maken van aangepaste query's.
