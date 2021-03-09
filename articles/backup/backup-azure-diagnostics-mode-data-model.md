---
title: Gegevens model van Azure Monitor logboeken
description: In dit artikel vindt u informatie over de Azure Monitor Log Analytics gegevens model gegevens voor Azure Backup gegevens.
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 004c5a6c0c2c4dcfcf13134bd5a5143ba647048f
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102500985"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Log Analytics gegevens model voor Azure Backup gegevens

Gebruik het Log Analytics gegevens model om aangepaste waarschuwingen van Log Analytics te maken.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
>
> * Dit gegevens model is een verwijzing naar de Azure Diagnostics modus voor het verzenden van diagnostische gebeurtenissen naar Log Analytics (LA). Raadpleeg het volgende artikel voor meer informatie over het gegevens model voor de nieuwe resource-specifieke modus: [gegevens model voor het Azure backup van diagnostische gebeurtenissen](./backup-azure-reports-data-model.md)
> * Voor het maken van aangepaste rapportage weergaven is het raadzaam om [systeem functies te gebruiken in azure monitor logboeken](backup-reports-system-functions.md) in plaats van het werken met de onbewerkte tabellen die hieronder worden weer gegeven.

## <a name="using-azure-backup-data-model"></a>Azure Backup gegevens model gebruiken

U kunt de volgende velden gebruiken als onderdeel van het gegevens model voor het maken van visuele elementen, aangepaste query's en dash boards op basis van uw vereisten.

### <a name="alert"></a>Waarschuwing

Deze tabel bevat details over velden die betrekking hebben op waarschuwingen.

| Veld | Gegevenstype | Beschrijving |
| --- | --- | --- |
| AlertUniqueId_s |Tekst |De unieke id van de gegenereerde waarschuwing |
| AlertType_s |Tekst |Type waarschuwing, bijvoorbeeld back-up |
| AlertStatus_s |Tekst |De status van de waarschuwing, bijvoorbeeld actief |
| AlertOccurrenceDateTime_s |Datum/tijd |Datum en tijd waarop de waarschuwing is gemaakt |
| AlertSeverity_s |Tekst |Ernst van de waarschuwing, bijvoorbeeld kritiek |
|AlertTimeToResolveInMinutes_s    | Aantal        |De tijd die nodig is om een waarschuwing op te lossen. Leeg voor actieve waarschuwingen.         |
|AlertConsolidationStatus_s   |Tekst         |Vaststellen of de waarschuwing een geconsolideerde waarschuwing is         |
|CountOfAlertsConsolidated_s     |Aantal         |Aantal waarschuwingen dat is geconsolideerd als het een geconsolideerde waarschuwing is          |
|AlertRaisedOn_s     |Tekst         |Het type entiteit waarop de waarschuwing is opgetreden         |
|AlertCode_s     |Tekst         |Code voor een unieke identificatie van een waarschuwings type         |
|RecommendedAction_s   |Tekst         |Aanbevolen actie om de waarschuwing op te lossen         |
| EventName_s |Tekst |De naam van de gebeurtenis. Altijd AzureBackupCentralReport |
| BackupItemUniqueId_s |Tekst |De unieke id van het back-upitem dat aan de waarschuwing is gekoppeld |
| SchemaVersion_s |Tekst |Huidige versie van het schema, bijvoorbeeld **v2** |
| State_s |Tekst |Huidige status van het waarschuwings object, bijvoorbeeld actief, verwijderd |
| BackupManagementType_s |Tekst |Provider type voor het uitvoeren van back-ups, bijvoorbeeld IaaSVM, FileFolder waartoe deze waarschuwing behoort |
| OperationName |Tekst |De naam van de huidige bewerking, bijvoorbeeld waarschuwing |
| Categorie |Tekst |Categorie diagnostische gegevens die naar Azure Monitor logboeken worden gepusht. Altijd AzureBackupReport |
| Resource |Tekst |Dit is de resource waarvoor gegevens worden verzameld, de naam van Recovery Services kluis wordt weer gegeven |
| ProtectedContainerUniqueId_s |Tekst |De unieke id van de beveiligde server die is gekoppeld aan de waarschuwing (was ProtectedServerUniqueId_s in v1)|
| VaultUniqueId_s |Tekst |De unieke id van de beveiligde kluis die is gekoppeld aan de waarschuwing |
| SourceSystem |Tekst |Bron systeem van de huidige gegevens-Azure |
| ResourceId |Tekst |De unieke id voor de resource waarover gegevens worden verzameld. Bijvoorbeeld een Recovery Services kluis Resource-ID |
| SubscriptionId |Tekst |De abonnements-id van de resource (bijvoorbeeld Recovery Services kluis) waarvoor gegevens worden verzameld |
| ResourceGroup |Tekst |De resource groep van de resource (bijvoorbeeld Recovery Services kluis) waarvoor gegevens worden verzameld |
| ResourceProvider |Tekst |Resource provider waarvoor gegevens worden verzameld. Bijvoorbeeld micro soft. Recovery Services |
| ResourceType |Tekst |Het resource type waarvoor gegevens worden verzameld. Bijvoorbeeld kluizen |

### <a name="backupitem"></a>BackupItem

Deze tabel bevat details over velden die betrekking hebben op Back-upitems.

| Veld | Gegevenstype | Beschrijving |
| --- | --- | --- |
| EventName_s |Tekst |De naam van de gebeurtenis. Altijd AzureBackupCentralReport |  
| BackupItemUniqueId_s |Tekst |De unieke id van het back-upitem |
| BackupItemId_s |Tekst |Id van het back-upitem (dit veld is alleen voor v1-schema) |
| BackupItemName_s |Tekst |Naam van back-upitem |
| BackupItemFriendlyName_s |Tekst |Beschrijvende naam van het back-upitem |
| BackupItemType_s |Tekst |Type back-upitem, bijvoorbeeld VM, FileFolder |
| BackupItemProtectionState_s |Tekst |De beveiligings status van het back-upitem |
| BackupItemAppVersion_s |Tekst |Toepassings versie van het back-upitem |
| ProtectionState_s |Tekst |Huidige beveiligings status van het back-upitem, bijvoorbeeld beveiligd, ProtectionStopped |
| ProtectionGroupName_s |Tekst | Naam van de beveiligings groep waarop het back-upitem is beveiligd, voor SC DPM en MABS, indien van toepassing|
| SecondaryBackupProtectionState_s |Tekst |Of secundaire beveiliging is ingeschakeld voor het back-upitem|
| SchemaVersion_s |Tekst |De versie van het schema, bijvoorbeeld **v2** |
| State_s |Tekst |Status van het object voor het back-upitem, bijvoorbeeld actief, verwijderd |
| BackupManagementType_s |Tekst |Provider type voor het uitvoeren van back-ups, bijvoorbeeld IaaSVM, FileFolder waaraan dit back-upitem behoort |
| OperationName |Tekst |De naam van de bewerking, bijvoorbeeld BackupItem |
| Categorie |Tekst |Categorie diagnostische gegevens die naar Azure Monitor logboeken worden gepusht. Altijd AzureBackupReport |
| Resource |Tekst |Resource waarvoor gegevens worden verzameld, bijvoorbeeld Recovery Services kluis naam |
| SourceSystem |Tekst |Bron systeem van de huidige gegevens-Azure |
| ResourceId |Tekst |De resource-ID voor de gegevens die worden verzameld, bijvoorbeeld Recovery Services kluis Resource-ID |
| SubscriptionId |Tekst |De abonnements-id van de resource (voor ex. Recovery Services kluis) voor gegevens die worden verzameld |
| ResourceGroup |Tekst |Resource groep van de resource (voor bijvoorbeeld Recovery Services kluis) voor gegevens die worden verzameld |
| ResourceProvider |Tekst |Resource provider voor gegevens die worden verzameld, bijvoorbeeld micro soft. Recovery Services |
| ResourceType |Tekst |Het type resource voor de gegevens die worden verzameld, bijvoorbeeld kluizen |

### <a name="backupitemassociation"></a>BackupItemAssociation

Deze tabel bevat details over koppelingen van back-upitems met verschillende entiteiten.

| Veld | Gegevenstype | Beschrijving |
| --- | --- | --- |
| EventName_s |Tekst |Dit veld vertegenwoordigt de naam van deze gebeurtenis. Het is altijd AzureBackupCentralReport |  
| BackupItemUniqueId_s |Tekst |De unieke ID van het back-upitem |
| SchemaVersion_s |Tekst |Dit veld geeft de huidige versie van het schema aan. Het is **v2** |
| State_s |Tekst |Huidige status van het object koppeling van het back-upitem, bijvoorbeeld actief, verwijderd |
| BackupManagementType_s |Tekst |Provider type voor het uitvoeren van een back-uptaak van de server, bijvoorbeeld IaaSVM, FileFolder |
| BackupItemSourceSize_s |Tekst | Front-end grootte van het back-upitem |
| BackupManagementServerUniqueId_s |Tekst | Veld voor het identificeren van de back-upbeheerserver waarop het back-upitem is beveiligd, indien van toepassing |
| Categorie |Tekst |Dit veld vertegenwoordigt de categorie diagnostische gegevens die naar Log Analytics zijn gepusht. Het is AzureBackupReport |
| OperationName |Tekst |Dit veld vertegenwoordigt de naam van de huidige bewerking-BackupItemAssociation |
| Resource |Tekst |Dit is de resource waarvoor gegevens worden verzameld, de naam van Recovery Services kluis wordt weer gegeven |
| ProtectedContainerUniqueId_s |Tekst |De unieke id van de beveiligde server die is gekoppeld aan het back-upitem (was ProtectedServerUniqueId_s in v1) |
| VaultUniqueId_s |Tekst |De unieke id van de kluis die het back-upitem bevat |
| SourceSystem |Tekst |Bron systeem van de huidige gegevens-Azure |
| ResourceId |Tekst |De resource-id voor de gegevens die worden verzameld. Bijvoorbeeld Recovery Services kluis Resource-ID |
| SubscriptionId |Tekst |De abonnements-id van de resource (voor ex. Recovery Services kluis) waarvoor gegevens worden verzameld |
| ResourceGroup |Tekst |Resource groep van de resource (voor bijvoorbeeld Recovery Services kluis) waarvoor gegevens worden verzameld |
| ResourceProvider |Tekst |Resource provider voor gegevens die worden verzameld, bijvoorbeeld micro soft. Recovery Services |
| ResourceType |Tekst |Het type resource voor de gegevens die worden verzameld, bijvoorbeeld kluizen |

### <a name="backupmanagementserver"></a>BackupManagementServer

Deze tabel bevat details over koppelingen van back-upitems met verschillende entiteiten.

| Veld | Gegevenstype | Beschrijving |
| --- | --- | --- |
|BackupManagementServerName_s     |Tekst         |Naam van de server voor back-upbeheer        |
|AzureBackupAgentVersion_s     |Tekst         |Versie van de Azure Backup-Agent op de server voor back-upbeheer          |
|BackupManagementServerVersion_s     |Tekst         |Versie van de server voor back-upbeheer|
|BackupManagementServerOSVersion_s    |Tekst            |Versie van het besturings systeem van de server voor back-upbeheer|
|BackupManagementServerType_s     |Tekst         |Type van de server voor back-upbeheer, zoals MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Tekst         |Veld om de back-upbeheerserver op unieke wijze te identificeren       |
| SourceSystem |Tekst |Bron systeem van de huidige gegevens-Azure |
| ResourceId |Tekst |De resource-id voor de gegevens die worden verzameld. Bijvoorbeeld Recovery Services kluis Resource-ID |
| SubscriptionId |Tekst |De abonnements-id van de resource (voor ex. Recovery Services kluis) waarvoor gegevens worden verzameld |
| ResourceGroup |Tekst |Resource groep van de resource (voor bijvoorbeeld Recovery Services kluis) waarvoor gegevens worden verzameld |
| ResourceProvider |Tekst |Resource provider voor gegevens die worden verzameld, bijvoorbeeld micro soft. Recovery Services |
| ResourceType |Tekst |Het type resource voor de gegevens die worden verzameld, bijvoorbeeld kluizen |

### <a name="job"></a>Taak

Deze tabel bevat details over projectgerelateerde velden.

| Veld | Gegevenstype | Beschrijving |
| --- | --- | --- |
| EventName_s |Tekst |De naam van de gebeurtenis. Altijd AzureBackupCentralReport |
| BackupItemUniqueId_s |Tekst |De unieke id van het back-upitem |
| SchemaVersion_s |Tekst |De versie van het schema, bijvoorbeeld **v2** |
| State_s |Tekst |Huidige status van het taak object, bijvoorbeeld actief, verwijderd |
| BackupManagementType_s |Tekst |Provider type voor het uitvoeren van een back-uptaak van de server, bijvoorbeeld IaaSVM, FileFolder |
| OperationName |Tekst |Dit veld bevat de naam van de huidige bewerking-taak |
| Categorie |Tekst |Dit veld vertegenwoordigt de categorie diagnostische gegevens die naar Azure Monitor logboeken worden gepusht. Het is AzureBackupReport |
| Resource |Tekst |Dit is de resource waarvoor gegevens worden verzameld, de naam van Recovery Services kluis wordt weer gegeven |
| ProtectedServerUniqueId_s |Tekst |De unieke id van de beveiligde server die aan de taak is gekoppeld |
| ProtectedContainerUniqueId_s |Tekst | Unieke ID voor het identificeren van de beveiligde container waarop de taak wordt uitgevoerd |
| VaultUniqueId_s |Tekst |De unieke id van de beveiligde kluis |
| JobOperation_s |Tekst |Bewerking waarvoor de taak wordt uitgevoerd bijvoorbeeld back-ups maken, herstellen, back-up configureren |
| JobStatus_s |Tekst |De status van de voltooide taak, bijvoorbeeld voltooid, is mislukt |
| JobFailureCode_s |Tekst |De teken reeks voor de fout code omdat de taak fout is opgetreden |
| JobStartDateTime_s |Datum/tijd |Datum en tijd waarop de taak is gestart |
| BackupStorageDestination_s |Tekst |Doel van back-upopslag, bijvoorbeeld Cloud, schijf  |
| AdHocOrScheduledJob_s |Tekst | Veld om op te geven of de taak ad-hoc of gepland is |
| JobDurationInSecs_s | Aantal |Totale taak duur in seconden |
| DataTransferredInMB_s | Aantal |Gegevens die worden overgebracht in MB voor deze taak|
| JobUniqueId_g |Tekst |Unieke ID voor het identificeren van de taak |
| RecoveryJobDestination_s |Tekst | Doel van een herstel taak, waarbij de gegevens worden hersteld |
| RecoveryJobRPDateTime_s |DateTime | De datum, het tijdstip waarop het herstel punt dat wordt hersteld, is gemaakt |
| RecoveryJobRPLocation_s |Tekst | De locatie waar het herstel punt dat wordt hersteld is opgeslagen|
| SourceSystem |Tekst |Bron systeem van de huidige gegevens-Azure |
| ResourceId |Tekst |De resource-id voor de gegevens die worden verzameld. Bijvoorbeeld Recovery Services kluis Resource-ID|
| SubscriptionId |Tekst |De abonnements-id van de resource (bijvoorbeeld Recovery Services kluis) waarvoor gegevens worden verzameld |
| ResourceGroup |Tekst |De resource groep van de resource (bijvoorbeeld Recovery Services kluis) waarvoor gegevens worden verzameld |
| ResourceProvider |Tekst |Resource provider waarvoor gegevens worden verzameld. Bijvoorbeeld micro soft. Recovery Services |
| ResourceType |Tekst |Het resource type waarvoor gegevens worden verzameld. Bijvoorbeeld kluizen |

### <a name="policy"></a>Beleid

Deze tabel bevat details over velden die betrekking hebben op het beleid.

| Veld | Gegevenstype | Versies van toepassing | Beschrijving |
| --- | --- | --- | --- |
| EventName_s |Tekst ||Dit veld vertegenwoordigt de naam van deze gebeurtenis. Het is altijd AzureBackupCentralReport |
| SchemaVersion_s |Tekst ||Dit veld geeft de huidige versie van het schema aan. Het is **v2** |
| State_s |Tekst ||Huidige status van het beleids object, bijvoorbeeld actief, verwijderd |
| BackupManagementType_s |Tekst ||Provider type voor het uitvoeren van een back-uptaak van de server, bijvoorbeeld IaaSVM, FileFolder |
| OperationName |Tekst ||Dit veld bevat de naam van de huidige bewerking-beleid |
| Categorie |Tekst ||Dit veld vertegenwoordigt de categorie diagnostische gegevens die naar Azure Monitor logboeken worden gepusht. Het is AzureBackupReport |
| Resource |Tekst ||Dit is de resource waarvoor gegevens worden verzameld, de naam van Recovery Services kluis wordt weer gegeven |
| PolicyUniqueId_g |Tekst ||Unieke ID voor het identificeren van het beleid |
| PolicyName_s |Tekst ||De naam van het beleid dat is gedefinieerd |
| BackupFrequency_s |Tekst ||De frequentie waarmee back-ups worden uitgevoerd, bijvoorbeeld dagelijks, wekelijks |
| BackupTimes_s |Tekst ||De datum en tijd waarop back-ups zijn gepland |
| BackupDaysOfTheWeek_s |Tekst ||Dagen van de week waarop back-ups zijn gepland |
| RetentionDuration_s |Geheel getal ||Bewaar periode voor geconfigureerde back-ups |
| DailyRetentionDuration_s |Geheel getal ||Totale retentie duur in dagen voor geconfigureerde back-ups |
| DailyRetentionTimes_s |Tekst ||De datum en tijd waarop een dagelijkse Bewaar periode is geconfigureerd |
| WeeklyRetentionDuration_s |Decimaal getal ||Totale wekelijkse Bewaar periode in weken voor geconfigureerde back-ups |
| WeeklyRetentionTimes_s |Tekst ||De datum en tijd waarop een wekelijkse Bewaar periode is geconfigureerd |
| WeeklyRetentionDaysOfTheWeek_s |Tekst ||Dagen van de week geselecteerd voor een wekelijkse Bewaar periode |
| MonthlyRetentionDuration_s |Decimaal getal ||Totale Bewaar periode in maanden voor geconfigureerde back-ups |
| MonthlyRetentionTimes_s |Tekst ||De datum en tijd waarop de maandelijkse retentie is geconfigureerd |
| MonthlyRetentionFormat_s |Tekst ||Type configuratie voor de maandelijkse Bewaar periode, bijvoorbeeld dagelijks voor dag, wekelijks voor op basis van de week |
| MonthlyRetentionDaysOfTheWeek_s |Tekst ||Dagen van de week geselecteerd voor een maandelijkse Bewaar periode |
| MonthlyRetentionWeeksOfTheMonth_s |Tekst ||Weken van de maand waarin de maandelijkse retentie is geconfigureerd, bijvoorbeeld eerst, laatste |
| YearlyRetentionDuration_s |Decimaal getal ||Totale Bewaar duur in jaren voor geconfigureerde back-ups |
| YearlyRetentionTimes_s |Tekst ||De datum en tijd waarop de jaarlijkse Bewaar periode is geconfigureerd |
| YearlyRetentionMonthsOfTheYear_s |Tekst ||Maanden van het jaar dat is geselecteerd voor een jaarlijkse Bewaar periode |
| YearlyRetentionFormat_s |Tekst ||Type configuratie voor jaarlijks bewaren, bijvoorbeeld dagelijks voor dag, wekelijks voor op basis van een week | |
| YearlyRetentionDaysOfTheMonth_s |Tekst ||De datums van de maand die zijn geselecteerd voor een jaarlijkse Bewaar periode |
| SynchronisationFrequencyPerDay_s |Geheel getal |v2|Aantal keren per dag dat een back-up van een bestand wordt gesynchroniseerd voor SC DPM en MABS |
| DiffBackupFormat_s |Tekst |v2|Indeling voor differentiële back-ups voor SQL in azure VM-back-up |
| DiffBackupTime_s |Tijd |v2|Tijd voor differentiële back-ups voor SQL in azure VM-back-up|
| DiffBackupRetentionDuration_s |Decimaal getal |v2|Bewaar periode voor differentiële back-ups voor SQL in azure VM-back-up|
| LogBackupFrequency_s |Decimaal getal |v2|Frequentie voor logboek back-ups voor SQL|
| LogBackupRetentionDuration_s |Decimaal getal |v2|Bewaar periode voor back-ups van Logboeken voor SQL in azure VM-back-up|
| DiffBackupDaysofTheWeek_s |Tekst |v2|Dagen van de week voor differentiële back-ups voor SQL in azure VM-back-up|
| SourceSystem |Tekst ||Bron systeem van de huidige gegevens-Azure |
| ResourceId |Tekst ||De resource-id voor de gegevens die worden verzameld. Bijvoorbeeld Recovery Services kluis Resource-ID |
| SubscriptionId |Tekst ||De abonnements-id van de resource (bijvoorbeeld Recovery Services kluis) waarvoor gegevens worden verzameld |
| ResourceGroup |Tekst ||De resource groep van de resource (bijvoorbeeld Recovery Services kluis) waarvoor gegevens worden verzameld |
| ResourceProvider |Tekst ||Resource provider waarvoor gegevens worden verzameld. Bijvoorbeeld micro soft. Recovery Services |
| ResourceType |Tekst ||Het resource type waarvoor gegevens worden verzameld. Bijvoorbeeld kluizen |

### <a name="policyassociation"></a>PolicyAssociation

Deze tabel bevat details over beleids koppelingen met verschillende entiteiten.

| Veld | Gegevenstype | Versies van toepassing | Beschrijving |
| --- | --- | --- | --- |
| EventName_s |Tekst ||Dit veld vertegenwoordigt de naam van deze gebeurtenis. Het is altijd AzureBackupCentralReport |
| SchemaVersion_s |Tekst ||Dit veld geeft de huidige versie van het schema aan. Het is **v2** |
| State_s |Tekst ||Huidige status van het beleids object, bijvoorbeeld actief, verwijderd |
| BackupManagementType_s |Tekst ||Provider type voor het uitvoeren van een back-uptaak van de server, bijvoorbeeld IaaSVM, FileFolder |
| OperationName |Tekst ||Dit veld vertegenwoordigt de naam van de huidige bewerking-PolicyAssociation |
| Categorie |Tekst ||Dit veld vertegenwoordigt de categorie diagnostische gegevens die naar Azure Monitor logboeken worden gepusht. Het is AzureBackupReport |
| Resource |Tekst ||Dit is de resource waarvoor gegevens worden verzameld, de naam van Recovery Services kluis wordt weer gegeven |
| PolicyUniqueId_g |Tekst ||Unieke ID voor het identificeren van het beleid |
| VaultUniqueId_s |Tekst ||De unieke ID van de kluis waartoe dit beleid behoort |
| BackupManagementServerUniqueId_s |Tekst |v2 |Veld voor het identificeren van de back-upbeheerserver waarop het back-upitem is beveiligd, indien van toepassing        |
| SourceSystem |Tekst ||Bron systeem van de huidige gegevens-Azure |
| ResourceId |Tekst ||De resource-id voor de gegevens die worden verzameld. Bijvoorbeeld Recovery Services kluis Resource-ID |
| SubscriptionId |Tekst ||De abonnements-id van de resource (bijvoorbeeld Recovery Services kluis) waarvoor gegevens worden verzameld |
| ResourceGroup |Tekst ||De resource groep van de resource (bijvoorbeeld Recovery Services kluis) waarvoor gegevens worden verzameld |
| ResourceProvider |Tekst ||Resource provider waarvoor gegevens worden verzameld. Bijvoorbeeld micro soft. Recovery Services |
| ResourceType |Tekst ||Het resource type waarvoor gegevens worden verzameld. Bijvoorbeeld kluizen |

### <a name="protected-container"></a>Beveiligde container

Deze tabel bevat basis velden over beveiligde containers. (Was ProtectedServer in v1)

| Veld | Gegevenstype | Beschrijving |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Tekst | Veld om een beveiligde container op unieke wijze te identificeren |
| ProtectedContainerOSType_s |Tekst |Type besturings systeem van de beveiligde container |
| ProtectedContainerOSVersion_s |Tekst |Versie van het besturings systeem van de beveiligde container |
| AgentVersion_s |Tekst |Het versie nummer van de back-up van de agent of de beveiligings agent (in het geval van SC DPM en MABS) |
| BackupManagementType_s |Tekst |Provider type voor het uitvoeren van back-ups. Bijvoorbeeld, IaaSVM, FileFolder |
| EntityState_s |Tekst |Huidige status van het beveiligde server object. Bijvoorbeeld actief, verwijderd |
| ProtectedContainerFriendlyName_s |Tekst |Beschrijvende naam van beveiligde server |
| ProtectedContainerName_s |Tekst |De naam van de beveiligde container |
| ProtectedContainerWorkloadType_s |Tekst |Type van de beveiligde container waarvan een back-up is gemaakt. Bijvoorbeeld IaaSVMContainer |
| ProtectedContainerLocation_s |Tekst |Of de beveiligde container zich lokaal of in azure bevindt |
| ProtectedContainerType_s |Tekst |Of de beveiligde container een server of een container is |
| ProtectedContainerProtectionState_s '  |Tekst |Beveiligings status van de beveiligde container |

### <a name="storage"></a>Storage

Deze tabel bevat details over velden die betrekking hebben op opslag.

| Veld | Gegevenstype | Beschrijving |
| --- | --- | --- |
| CloudStorageInBytes_s |Decimaal getal |Back-upopslag voor de cloud die wordt gebruikt voor back-ups, berekend op basis van de meest recente waarde (dit veld is alleen voor v1-schema)|
| ProtectedInstances_s |Decimaal getal |Aantal beveiligde instanties dat wordt gebruikt voor het berekenen van de front-end opslag in de facturering, berekend op basis van de nieuwste waarde |
| EventName_s |Tekst |Dit veld vertegenwoordigt de naam van deze gebeurtenis. Het is altijd AzureBackupCentralReport |
| SchemaVersion_s |Tekst |Dit veld geeft de huidige versie van het schema aan. Het is **v2** |
| State_s |Tekst |Huidige status van het opslag object, bijvoorbeeld actief, verwijderd |
| BackupManagementType_s |Tekst |Provider type voor het uitvoeren van een back-uptaak van de server, bijvoorbeeld IaaSVM, FileFolder |
| OperationName |Tekst |Dit veld bevat de naam van de huidige bewerking-opslag |
| Categorie |Tekst |Dit veld vertegenwoordigt de categorie diagnostische gegevens die naar Azure Monitor logboeken worden gepusht. Het is AzureBackupReport |
| Resource |Tekst |Dit is de resource waarvoor gegevens worden verzameld, de naam van Recovery Services kluis wordt weer gegeven |
| ProtectedServerUniqueId_s |Tekst |De unieke ID van de beveiligde server waarvoor opslag wordt berekend |
| VaultUniqueId_s |Tekst |De unieke ID van de kluis voor opslag wordt berekend |
| SourceSystem |Tekst |Bron systeem van de huidige gegevens-Azure |
| ResourceId |Tekst |De resource-id voor de gegevens die worden verzameld. Bijvoorbeeld Recovery Services kluis Resource-ID |
| SubscriptionId |Tekst |De abonnements-id van de resource (bijvoorbeeld Recovery Services kluis) waarvoor gegevens worden verzameld |
| ResourceGroup |Tekst |De resource groep van de resource (bijvoorbeeld Recovery Services kluis) waarvoor gegevens worden verzameld |
| ResourceProvider |Tekst |Resource provider waarvoor gegevens worden verzameld. Bijvoorbeeld micro soft. Recovery Services |
| ResourceType |Tekst |Het resource type waarvoor gegevens worden verzameld. Bijvoorbeeld kluizen |
| StorageUniqueId_s |Tekst |Unieke ID die wordt gebruikt voor het identificeren van de opslag entiteit |
| StorageType_s |Tekst |Type opslag, bijvoorbeeld Cloud, volume, schijf |
| StorageName_s |Tekst |De naam van de opslag entiteit, bijvoorbeeld E:\ |
| StorageTotalSizeInGBs_s |Tekst |Totale grootte van de opslag, in GB, die wordt verbruikt door de opslag entiteit|

### <a name="storageassociation"></a>StorageAssociation

Deze tabel bevat basis velden die betrekking hebben op opslag en die opslag aan andere entiteiten koppelen.

| Veld | Gegevenstype | Beschrijving |
| --- | --- |  --- |
| StorageUniqueId_s |Tekst |Unieke ID die wordt gebruikt voor het identificeren van de opslag entiteit |
| SchemaVersion_s |Tekst |Dit veld geeft de huidige versie van het schema aan. Het is **v2** |
| BackupItemUniqueId_s |Tekst |Unieke ID die wordt gebruikt om het back-upitem te identificeren dat is gerelateerd aan de opslag entiteit |
| BackupManagementServerUniqueId_s |Tekst |Unieke ID die wordt gebruikt om de back-upbeheerserver te identificeren die betrekking heeft op de opslag entiteit|
| VaultUniqueId_s |Tekst |Unieke ID die wordt gebruikt om de kluis te identificeren die is gerelateerd aan de opslag entiteit|
| StorageConsumedInMBs_s |Aantal|Grootte van de opslag die wordt gebruikt door het bijbehorende back-upitem in de bijbehorende opslag |
| StorageAllocatedInMBs_s |Aantal |Grootte van de opslag die wordt toegewezen door het bijbehorende back-upitem in de bijbehorende opslag van het type schijf|

### <a name="vault"></a>Kluis

Deze tabel bevat details over aan de kluis gerelateerde velden.

| Veld | Gegevenstype | Beschrijving |
| --- | --- | --- |
| EventName_s |Tekst |Dit veld vertegenwoordigt de naam van deze gebeurtenis. Het is altijd AzureBackupCentralReport |
| SchemaVersion_s |Tekst |Dit veld geeft de huidige versie van het schema aan. Het is **v2** |
| State_s |Tekst |Huidige status van het kluis object, bijvoorbeeld actief, verwijderd |
| OperationName |Tekst |Dit veld bevat de naam van de huidige bewerking-kluis |
| Categorie |Tekst |Dit veld vertegenwoordigt de categorie diagnostische gegevens die naar Azure Monitor logboeken worden gepusht. Het is AzureBackupReport |
| Resource |Tekst |Dit is de resource waarvoor gegevens worden verzameld, de naam van Recovery Services kluis wordt weer gegeven |
| VaultUniqueId_s |Tekst |De unieke ID van de kluis |
| VaultName_s |Tekst |Naam van de kluis |
| AzureDataCenter_s |Tekst |Data Center waar de kluis zich bevindt |
| StorageReplicationType_s |Tekst |Type opslag replicatie voor de kluis, bijvoorbeeld georedundant |
| SourceSystem |Tekst |Bron systeem van de huidige gegevens-Azure |
| ResourceId |Tekst |De resource-id voor de gegevens die worden verzameld. Bijvoorbeeld Recovery Services kluis Resource-ID |
| SubscriptionId |Tekst |De abonnements-id van de resource (bijvoorbeeld Recovery Services kluis) waarvoor gegevens worden verzameld |
| ResourceGroup |Tekst |De resource groep van de resource (bijvoorbeeld Recovery Services kluis) waarvoor gegevens worden verzameld |
| ResourceProvider |Tekst |Resource provider waarvoor gegevens worden verzameld. Bijvoorbeeld micro soft. Recovery Services |
| ResourceType |Tekst |Het resource type waarvoor gegevens worden verzameld. Bijvoorbeeld kluizen |

### <a name="backup-management-server"></a>Beheer server voor back-up

Deze tabel bevat basis velden over back-upbeheerser vers.

|Veld  |Gegevenstype  | Beschrijving  |
|---------|---------|----------|
|BackupManagementServerName_s     |Tekst         |Naam van de server voor back-upbeheer        |
|AzureBackupAgentVersion_s     |Tekst         |Versie van de Azure Backup-Agent op de server voor back-upbeheer          |
|BackupManagementServerVersion_s     |Tekst         |Versie van de server voor back-upbeheer|
|BackupManagementServerOSVersion_s     |Tekst            |Versie van het besturings systeem van de server voor back-upbeheer|
|BackupManagementServerType_s     |Tekst         |Type van de server voor back-upbeheer, zoals MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Tekst         |Veld om de back-upbeheerserver op unieke wijze te identificeren       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

In deze tabel worden de werk belasting (s) aangegeven waaraan een volume is gekoppeld.

| Veld | Gegevenstype | Beschrijving |
| --- | --- | --- |
| StorageUniqueId_s |Tekst |Unieke ID die wordt gebruikt voor het identificeren van de opslag entiteit |
| BackupItemType_s |Tekst |De werk belastingen waarvoor dit volume de voorkeurs opslag is|

### <a name="protectedinstance"></a>ProtectedInstance

Deze tabel bevat basis velden die betrekking hebben op een beveiligd exemplaar.

| Veld | Gegevenstype |Versies van toepassing | Beschrijving |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Tekst |v2|Unieke ID die wordt gebruikt voor het identificeren van het back-upitem voor Vm's waarvan een back-up is gemaakt met DPM, MABS|
| ProtectedContainerUniqueId_s |Tekst |v2|Unieke ID die wordt gebruikt om de beveiligde container te identificeren voor alles behalve Vm's waarvoor een back-up is gemaakt met behulp van DPM, MABS|
| ProtectedInstanceCount_s |Tekst |v2|Aantal beveiligde instanties voor het bijbehorende back-upitem of de beveiligde container op die datum-tijd|

### <a name="recoverypoint"></a>Recovery Point

Deze tabel bevat algemene velden voor herstel punten.

| Veld | Gegevenstype | Beschrijving |
| --- | --- | --- |
| BackupItemUniqueId_s |Tekst |Unieke ID die wordt gebruikt voor het identificeren van het back-upitem voor Vm's waarvan een back-up is gemaakt met DPM, MABS|
| OldestRecoveryPointTime_s |Tekst |Datum en tijd van het oudste herstel punt voor het back-upitem|
| OldestRecoveryPointLocation_s |Tekst |Locatie van het oudste herstel punt voor het back-upitem|
| LatestRecoveryPointTime_s |Tekst |Datum en tijd van het laatste herstel punt voor het back-upitem|
| LatestRecoveryPointLocation_s |Tekst |Locatie van het meest recente herstel punt voor het back-upitem|

## <a name="sample-kusto-queries"></a>Voor beeld van Kusto-Query's

Hieronder vindt u enkele voor beelden voor het schrijven van query's op Azure Backup gegevens die zich in de Azure Diagnostics tabel bevinden:

- Alle geslaagde back-uptaken

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````

- Alle mislukte back-uptaken

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````

- Alle voltooide Azure VM-back-uptaken

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "VM" and BackupManagementType_s == "IaaSVM"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- Alle voltooide SQL-logboek back-uptaken

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s == "Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "SQLDataBase" and BackupManagementType_s == "AzureWorkload"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- Alle geslaagde Azure Backup Agent taken

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "FileFolder" and BackupManagementType_s == "MAB"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

## <a name="v1-schema-vs-v2-schema"></a>V1-schema VS v2-schema

Eerder werden de diagnostische gegevens voor Azure Backup Agent en Azure VM-back-up verzonden naar Azure Diagnostics tabel in een schema waarnaar wordt verwezen als ***v1-schema** _. Daarna werden nieuwe kolommen toegevoegd ter ondersteuning van andere scenario's en werk belastingen, en worden diagnostische gegevens gepusht in een nieuw schema, aangeduid als _ *_v2 schema_* *.  

Om redenen van achterwaartse compatibiliteit, worden diagnostische gegevens voor Azure Backup-Agent en Azure VM-back-up momenteel verzonden naar Azure Diagnostics tabel in zowel het v1-als het v2-schema (met v1-schema nu op een afschaffing-pad). U kunt bepalen welke records in Log Analytics van v1-schema door records te filteren voor SchemaVersion_s = "v1" in uw logboek query's.

Raadpleeg de derde kolom ' description ' in het hierboven beschreven [gegevens model](#using-azure-backup-data-model) om te bepalen welke kolommen alleen bij v1-schema horen.

### <a name="modifying-your-queries-to-use-the-v2-schema"></a>Uw query's wijzigen om het v2-schema te gebruiken

Omdat het v1-schema zich op een afschaffing pad bevindt, is het raadzaam om alleen het v2-schema te gebruiken in alle aangepaste query's op Azure Backup diagnostische gegevens. Hieronder ziet u een voor beeld van hoe u uw query's bijwerkt om afhankelijkheden van v1-schema te verwijderen:

1. Bepaal of uw query een veld gebruikt dat alleen van toepassing is op het v1-schema. Stel dat u een query hebt om alle back-upitems en de bijbehorende beveiligde servers als volgt weer te geven:

    ````Kusto
    AzureDiagnostics
    | where Category=="AzureBackupReport"
    | where OperationName=="BackupItemAssociation"
    | distinct BackupItemUniqueId_s, ProtectedServerUniqueId_s
    ````

    De bovenstaande query gebruikt het veld ProtectedServerUniqueId_s, dat alleen van toepassing is op het v1-schema. Het v2-schema equivalent van dit veld is ProtectedContainerUniqueId_s (zie tabellen hierboven). Het veld BackupItemUniqueId_s is van toepassing op zelfs het v2-schema en hetzelfde veld kan worden gebruikt in deze query.

2. Werk de query bij om de v2-schema veld namen te gebruiken. Het is een aanbevolen procedure om het filter te gebruiken **waarbij SchemaVersion_s = ' v2 '** in al uw query's, zodat alleen records die overeenkomen met het v2-schema worden geparseerd door de query:

    ````Kusto
    AzureDiagnostics
    | where Category=="AzureBackupReport"
    | where OperationName=="BackupItemAssociation"
    | where SchemaVersion_s=="V2"
    | distinct BackupItemUniqueId_s, ProtectedContainerUniqueId_s
    ````

## <a name="next-steps"></a>Volgende stappen

Wanneer u het gegevens model hebt gecontroleerd, kunt u beginnen met het [maken van aangepaste query's](../azure-monitor/visualize/tutorial-logs-dashboards.md) in azure monitor Logboeken om uw eigen dash board te bouwen.
