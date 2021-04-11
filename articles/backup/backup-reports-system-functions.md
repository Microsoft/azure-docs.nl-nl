---
title: Systeem functies in Azure Monitor-logboeken
description: Aangepaste query's schrijven in Azure Monitor logboeken met behulp van systeem functies
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: acb45e6ad0250a1f8d10377fdd509e40051f25b9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564905"
---
# <a name="system-functions-on-azure-monitor-logs"></a>Systeem functies in Azure Monitor-logboeken

Azure Backup biedt een aantal functies, de zogenaamde systeem functies of oplossings functies, die standaard beschikbaar zijn in uw Log Analytics (LA)-werk ruimten.
 
Deze functies werken op gegevens in de [onbewerkte Azure backup tabellen](./backup-azure-reports-data-model.md) in La en retour neren gegevens die u helpen bij het ophalen van gegevens van al uw back-upentiteiten, met behulp van eenvoudige query's. Gebruikers kunnen para meters door geven aan deze functies om de gegevens te filteren die door deze functies worden geretourneerd. 

Het is raadzaam om systeem functies te gebruiken voor het uitvoeren van query's in uw back-upgegevens in LA-werk ruimten voor het maken van aangepaste rapporten, aangezien deze een aantal voor delen bieden, zoals wordt beschreven in de volgende sectie.

## <a name="benefits-of-using-system-functions"></a>Voor delen van het gebruik van systeem functies

* **Eenvoudigere query's**: het gebruik van functies helpt u bij het verminderen van het aantal benodigde koppelingen in uw query's. Standaard retour neren de functies ' afgevlakt ' schema's, waarbij alle informatie wordt opgenomen die betrekking heeft op de entiteit (back-upexemplaar, taak, kluis, enzovoort) die wordt opgevraagd. Als u bijvoorbeeld een lijst met geslaagde back-uptaken wilt ophalen op basis van de naam van het back-upitem en de bijbehorende container, geeft u een eenvoudige aanroep van de functie **_AzureBackup_getJobs ()** alle informatie over elke taak. Aan de andere kant zou het direct uitvoeren van een query op de onbewerkte tabellen vereisen dat u meerdere samen voegingen tussen [AddonAzureBackupJobs](./backup-azure-reports-data-model.md#addonazurebackupjobs) -en [CoreAzureBackup](./backup-azure-reports-data-model.md#coreazurebackup) -tabellen uitvoert.

* **Vloeiendere overgang van de verouderde diagnose gebeurtenis**: met behulp van systeem functies kunt u probleemloos overstappen van de [verouderde diagnostische gebeurtenis](./backup-azure-diagnostic-events.md#legacy-event) (AzureBackupReport in de modus AzureDiagnostics) naar de [resource-specifieke gebeurtenissen](./backup-azure-diagnostic-events.md#diagnostics-events-available-for-azure-backup-users). Alle systeem functies van Azure Backup bieden u de mogelijkheid om een para meter op te geven waarmee u kunt kiezen of de functie alleen gegevens uit de resource-specifieke tabellen moet opvragen, of dat er gegevens moeten worden opgehaald uit de verouderde tabel en de resource-specifieke tabellen (met ontdubbeling van records).
    * Als u met succes hebt gemigreerd naar de resource-specifieke tabellen, kunt u ervoor kiezen om de verouderde tabel uit te sluiten van query's door de functie.
    * Als u momenteel bezig bent met de migratie en een aantal gegevens in de verouderde tabellen hebt die u voor analyse nodig hebt, kunt u ervoor kiezen om de verouderde tabel op te nemen. Wanneer de overgang is voltooid en u geen gegevens meer nodig hebt van de verouderde tabel, kunt u de waarde van de para meter die is door gegeven aan de functie in uw query's gewoon bijwerken om de verouderde tabel uit te sluiten.
    * Als u nog steeds alleen de verouderde tabel gebruikt, werken de functies nog steeds als u ervoor kiest om de verouderde tabel op te genomen via dezelfde para meter. Het wordt echter aanbevolen om op het eerste van de [resource-specifieke tabellen over te scha kelen](./backup-azure-diagnostic-events.md#steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace) .

* **Minder kans op het aantal aangepaste query's**: als Azure backup verbeteringen doorvoert in het schema van de onderliggende La-tabellen voor toekomstige rapportage scenario's, wordt de definitie van de functies ook bijgewerkt om rekening te houden met de schema wijzigingen. Als u bijvoorbeeld systeem functies gebruikt voor het maken van aangepaste query's, worden uw query's niet onderbroken, zelfs niet als er wijzigingen zijn in het onderliggende schema van de tabellen.

> [!NOTE]
> Systeem functies worden beheerd door micro soft en hun definities kunnen niet worden bewerkt door gebruikers. Als u bewerkte functies nodig hebt, kunt u [opgeslagen functies](../azure-monitor/logs/functions.md) maken in La.

## <a name="types-of-system-functions-offered-by-azure-backup"></a>Typen systeem functies die worden aangeboden door Azure Backup

* **Kern functies**: Dit zijn functies waarmee u een query kunt uitvoeren op een van de belangrijkste Azure backup entiteiten, zoals back-instanties, kluizen, beleids regels, taken en facturerings entiteiten. Met de functie **_AzureBackup_getBackupInstances** wordt bijvoorbeeld een lijst geretourneerd met alle back-upinstanties die in uw omgeving aanwezig zijn op de laatste voltooide dag (in UTC). De para meters en het geretourneerde schema voor elk van deze kern functies worden hieronder in dit artikel beschreven.

* **Trend functies**: Dit zijn functies die historische records retour neren voor uw back-up-gerelateerde entiteiten (bijvoorbeeld back-upinstanties, facturerings groepen) en u de mogelijkheid geven om dagelijkse, wekelijkse en maandelijkse trend gegevens op basis van belang rijke metrieken (bijvoorbeeld aantal, verbruikte opslag) die betrekking hebben op deze entiteiten te verkrijgen. De para meters en het geretourneerde schema voor elk van deze trend functies worden hieronder in dit artikel beschreven.

> [!NOTE]
> Momenteel retour neren systeem functies gegevens naar de laatst voltooide dag (in UTC). De gegevens voor de huidige gedeeltelijke dag worden niet geretourneerd. Als u dus records voor de huidige dag wilt ophalen, moet u de tabellen met onbewerkte LA gebruiken.


## <a name="list-of-system-functions"></a>Lijst met systeem functies

### <a name="core-functions"></a>Kern functies

#### <a name="_azurebackup_getvaults"></a>_AzureBackup_GetVaults ()

Deze functie retourneert de lijst met alle Recovery Services kluizen in uw Azure-omgeving die zijn gekoppeld aan de werk ruimte LA.

**Parameters**

| **Parameter naam** | **Beschrijving** | **Vereist?** : | **Voorbeeldwaarde** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Gebruik deze para meter samen met de para meter RangeEnd alleen als u alle kluis gerelateerde records moet ophalen uit de periode van Range Start naar RangeEnd. De waarde van Range Start en RangeEnd zijn standaard NULL, waardoor de functie alleen de meest recente record voor elke kluis ophaalt. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Gebruik deze para meter samen met de Range Start-para meter alleen als u alle kluis gerelateerde records in de periode van Range Start naar RangeEnd moet ophalen. De waarde van Range Start en RangeEnd zijn standaard NULL, waardoor de functie alleen de meest recente record voor elke kluis ophaalt. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Gebruik deze para meter om de uitvoer van de functie te filteren op een bepaalde set abonnementen waarbij back-upgegevens bestaan. Het opgeven van een door komma's gescheiden lijst met abonnements-Id's als een para meter voor deze functie helpt u alleen de kluizen op te halen die zich in de opgegeven abonnementen bevinden. Standaard is de waarde van deze para meter ' * ', waarmee de functie wordt doorzocht op records in alle abonnementen. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Gebruik deze para meter om de uitvoer van de functie te filteren op een bepaalde set regio's waarin de back-upgegevens bestaan. Als u een door komma's gescheiden lijst met regio's opgeeft als para meter voor deze functie, kunt u alleen de kluizen ophalen die zich in de opgegeven regio's bevinden. Standaard is de waarde van deze para meter ' * ', waarmee de functie wordt doorzocht op records in alle regio's. | N | "Oost, westus"|
| VaultList    |Gebruik deze para meter om de uitvoer van de functie voor een bepaalde set kluizen te filteren. Het opgeven van een door komma's gescheiden lijst met kluis namen als een para meter voor deze functie helpt u bij het ophalen van records die alleen betrekking hebben op de opgegeven kluizen. Standaard is de waarde van deze para meter ' * ', waarmee de functie wordt gebruikt voor het zoeken naar records in alle kluizen. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Gebruik deze para meter om de uitvoer van de functie te filteren op records die betrekking hebben op een bepaald type kluis. Momenteel is het enige ondersteunde kluis type ' micro soft. Recovery Services/kluizen '. Dit is de standaard waarde van deze para meter | N | "Micro soft. Recovery Services/kluizen"|
| ExcludeLegacyEvent  | Gebruik deze para meter om te kiezen of u wilt zoeken in gegevens in de verouderde tabel AzureDiagnostics of niet. Als de waarde van deze para meter False is, vraagt de functie gegevens op uit de tabel AzureDiagnostics en de resource-specifieke tabellen. Als de waarde van deze para meter True is, vraagt de functie alleen gegevens op uit de resource-specifieke tabellen. De standaardwaarde is Waar. | N | true |

**Geretourneerde velden**

| **Veld naam** | **Beschrijving** |
| -------------- | --------------- |
| UniqueId | Primaire sleutel voor het identificeren van de unieke ID van de kluis |
| Id | Azure Resource Manager (ARM)-ID van de kluis |
| Name | Naam van de kluis |
| SubscriptionId | ID van het abonnement waarin de kluis zich bevindt |
| Locatie | De locatie waar de kluis zich bevindt |
| VaultStore_StorageReplicationType | Type opslag replicatie dat is gekoppeld aan de kluis |
| Tags | Tags van de kluis |
| TimeGenerated | Tijds tempel van de record |
| Type |  Type kluis, dat is ' micro soft. Recovery Services/kluizen '|

#### <a name="_azurebackup_getpolicies"></a>_AzureBackup_GetPolicies ()

Deze functie retourneert de lijst met back-upbeleiden die worden gebruikt in uw Azure-omgeving, samen met gedetailleerde informatie over elk beleid, zoals het gegevens bron type, het type opslag replicatie, enzovoort.

**Parameters**

| **Parameter naam** | **Beschrijving** | **Vereist?** : | **Voorbeeldwaarde** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Gebruik deze para meter samen met de Range Start-para meter alleen als u alle aan het beleid gerelateerde records moet ophalen uit de periode van Range Start naar RangeEnd. De waarde van Range Start en RangeEnd zijn standaard NULL, waardoor de functie alleen de meest recente record voor elk beleid kan ophalen. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Gebruik deze para meter samen met de Range Start-para meter alleen als u alle aan het beleid gerelateerde records moet ophalen uit de periode van Range Start naar RangeEnd. De waarde van Range Start en RangeEnd zijn standaard NULL, waardoor de functie alleen de meest recente record voor elk beleid kan ophalen. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Gebruik deze para meter om de uitvoer van de functie te filteren op een bepaalde set abonnementen waarbij back-upgegevens bestaan. Als u een door komma's gescheiden lijst met abonnements-Id's opgeeft als para meter voor deze functie, kunt u alleen de beleids regels ophalen die zich in de opgegeven abonnementen bevinden. Standaard is de waarde van deze para meter ' * ', waarmee de functie wordt doorzocht op records in alle abonnementen. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Gebruik deze para meter om de uitvoer van de functie te filteren op een bepaalde set regio's waarin de back-upgegevens bestaan. Als u een door komma's gescheiden lijst met regio's opgeeft als para meter voor deze functie, kunt u alleen de beleids regels ophalen die zich in de opgegeven regio's bevinden. Standaard is de waarde van deze para meter ' * ', waarmee de functie wordt doorzocht op records in alle regio's. | N | "Oost, westus"|
| VaultList    |Gebruik deze para meter om de uitvoer van de functie voor een bepaalde set kluizen te filteren. Het opgeven van een door komma's gescheiden lijst met kluis namen als een para meter voor deze functie helpt u bij het ophalen van records van beleids regels die alleen betrekking hebben op de opgegeven kluizen. Standaard is de waarde van deze para meter ' * ', waarmee de functie wordt gebruikt om te zoeken naar records van beleids regels in alle kluizen. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Gebruik deze para meter om de uitvoer van de functie te filteren op records die betrekking hebben op een bepaald type kluis. Momenteel is het enige ondersteunde kluis type ' micro soft. Recovery Services/kluizen '. Dit is de standaard waarde van deze para meter. | N | "Micro soft. Recovery Services/kluizen"|
| ExcludeLegacyEvent  | Gebruik deze para meter om te kiezen of u wilt zoeken in gegevens in de verouderde tabel AzureDiagnostics of niet. Als de waarde van deze para meter False is, vraagt de functie gegevens op uit de tabel AzureDiagnostics en de resource-specifieke tabellen. Als de waarde van deze para meter True is, vraagt de functie alleen gegevens op uit de resource-specifieke tabellen. De standaardwaarde is Waar. | N | true |
| BackupSolutionList | Gebruik deze para meter om de uitvoer van de functie te filteren op een bepaalde set back-upoplossingen die worden gebruikt in uw Azure-omgeving. Als u bijvoorbeeld ' back-up van virtuele Azure-machine ', SQL in azure VM backup, DPM ' opgeeft als waarde van deze para meter, retourneert de functie alleen records die zijn gerelateerd aan items waarvan een back-up is gemaakt met behulp van back-ups van Azure virtual machine, SQL in azure VM backup of DPM naar Azure backup. De waarde van deze para meter is standaard ' * ', waardoor de functie records retourneert die betrekking hebben op alle back-upoplossingen die worden ondersteund door back-uprapporten (ondersteunde waarden zijn ' back-up van virtuele machine van Azure ', ' SQL in azure VM backup ', ' SAP HANA in azure VM backup ', ' Azure Storage (Azure Files) Backup ', ' Azure Backup Agent ', ' DPM ', ' Azure Backup Server ' of een door komma's gescheiden combi natie van een van deze waarden). | N | "Back-up van virtuele Azure-machine, SQL in azure VM backup, DPM, Azure Backup-Agent" |

**Geretourneerde velden**

| **Veld naam** | **Beschrijving** |
| -------------- | --------------- |
| UniqueId | Primaire sleutel voor het identificeren van de unieke ID van het beleid |
| Id | Azure Resource Manager-ID (ARM) van het beleid |
| Name | De naam van het beleid |
| Back-upoplossing | Back-upoplossing waaraan het beleid is gekoppeld. Bijvoorbeeld Azure VM backup, SQL in azure VM backup, enzovoort. |
| TimeGenerated | Tijds tempel van de record |
| VaultUniqueId | Refererende sleutel die verwijst naar de kluis die aan het beleid is gekoppeld |
| VaultResourceId | Azure Resource Manager-ID (ARM) van de kluis die aan het beleid is gekoppeld |
| VaultName | De naam van de kluis die aan het beleid is gekoppeld |
| VaultTags | Tags van de kluis die aan het beleid is gekoppeld |
| VaultLocation | Locatie van de kluis die aan het beleid is gekoppeld |
| VaultSubscriptionId | Abonnements-ID van de kluis die aan het beleid is gekoppeld |
| VaultStore_StorageReplicationType | Type opslag replicatie van de kluis die aan het beleid is gekoppeld |
| VaultType | Type kluis, dat is ' micro soft. Recovery Services/kluizen ' |
| ExtendedProperties | Aanvullende eigenschappen van het beleid |

#### <a name="_azurebackup_getjobs"></a>_AzureBackup_GetJobs ()

Deze functie retourneert een lijst met alle taken voor back-up en herstel die zijn geactiveerd in een opgegeven tijds bereik, samen met gedetailleerde informatie over elke taak, zoals de taak status, de duur van de taak, de overdracht van gegevens, enzovoort.

**Parameters**

| **Parameter naam** | **Beschrijving** | **Vereist?** : | **Voorbeeldwaarde** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Gebruik deze para meter in combi natie met de para meter RangeEnd voor het ophalen van de lijst met alle taken die zijn gestart in de tijds periode van Range Start naar RangeEnd. | J | "2021-03-03 00:00:00" |
| RangeEnd     | Gebruik deze para meter samen met de Range Start-para meter om de lijst met alle taken die zijn gestart in de periode van Range Start naar RangeEnd op te halen. | J |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Gebruik deze para meter om de uitvoer van de functie te filteren op een bepaalde set abonnementen waarbij back-upgegevens bestaan. Het opgeven van een door komma's gescheiden lijst met abonnements-Id's als een para meter voor deze functie helpt u bij het ophalen van de taken die zijn gekoppeld aan kluizen in de opgegeven abonnementen. Standaard is de waarde van deze para meter ' * ', waarmee de functie wordt doorzocht op records in alle abonnementen. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Gebruik deze para meter om de uitvoer van de functie te filteren op een bepaalde set regio's waarin de back-upgegevens bestaan. Het opgeven van een door komma's gescheiden lijst met regio's als een para meter voor deze functie helpt u bij het ophalen van de taken die zijn gekoppeld aan kluizen in de opgegeven regio's. Standaard is de waarde van deze para meter ' * ', waarmee de functie wordt doorzocht op records in alle regio's. | N | "Oost, westus"|
| VaultList    | Gebruik deze para meter om de uitvoer van de functie voor een bepaalde set kluizen te filteren. Het opgeven van een door komma's gescheiden lijst met kluis namen als een para meter voor deze functie helpt u bij het ophalen van taken die alleen betrekking hebben op de opgegeven kluizen. Standaard is de waarde van deze para meter ' * ', waarmee de functie wordt gebruikt voor het zoeken naar taken in alle kluizen. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Gebruik deze para meter om de uitvoer van de functie te filteren op records die betrekking hebben op een bepaald type kluis. Momenteel is het enige ondersteunde kluis type ' micro soft. Recovery Services/kluizen '. Dit is de standaard waarde van deze para meter. | N | "Micro soft. Recovery Services/kluizen"|
| ExcludeLegacyEvent  | Gebruik deze para meter om te kiezen of u wilt zoeken in gegevens in de verouderde tabel AzureDiagnostics of niet. Als de waarde van deze para meter False is, vraagt de functie gegevens op uit de tabel AzureDiagnostics en de resource-specifieke tabellen. Als de waarde van deze para meter True is, vraagt de functie alleen gegevens op uit de resource-specifieke tabellen. De standaardwaarde is Waar. | N | true |
| BackupSolutionList | Gebruik deze para meter om de uitvoer van de functie te filteren op een bepaalde set back-upoplossingen die worden gebruikt in uw Azure-omgeving. Als u bijvoorbeeld ' back-up van virtuele Azure-machine ', SQL in azure VM backup, DPM ' opgeeft als waarde van deze para meter, retourneert de functie alleen records die zijn gerelateerd aan items waarvan een back-up is gemaakt met behulp van back-ups van Azure virtual machine, SQL in azure VM backup of DPM naar Azure backup. De waarde van deze para meter is standaard ' * ', waardoor de functie records retourneert die betrekking hebben op alle back-upoplossingen die worden ondersteund door back-uprapporten (ondersteunde waarden zijn ' back-up van virtuele machine van Azure ', ' SQL in azure VM backup ', ' SAP HANA in azure VM backup ', ' Azure Storage (Azure Files) Backup ', ' Azure Backup Agent ', ' DPM ', ' Azure Backup Server ' of een door komma's gescheiden combi natie van een van deze waarden). | N | "Back-up van virtuele Azure-machine, SQL in azure VM backup, DPM, Azure Backup-Agent" |
| JobOperationList | Gebruik deze para meter om de uitvoer van de functie voor een specifiek type taak te filteren. Bijvoorbeeld: back-up of herstellen. Standaard is de waarde van deze para meter ' * ', waarmee de functie wordt doorzocht op back-up-en herstel taken. | N | Programma | 
| JobStatusList | Gebruik deze para meter om de uitvoer van de functie te filteren op een specifieke taak status. Bijvoorbeeld voltooid, mislukt, enzovoort. Standaard is de waarde van deze para meter ' * ', waarmee de functie wordt doorzocht op alle taken ongeacht de status. | N | "Mislukt, CompletedWithWarnings" |
| JobFailureCodeList | Gebruik deze para meter om de uitvoer van de functie voor een specifieke fout code te filteren. Standaard is de waarde van deze para meter ' * ', waarmee de functie wordt doorzocht op alle taken ongeacht de fout code. | N | Geleverd
| DatasourceSetName | Gebruik deze para meter om de uitvoer van de functie te filteren op een bepaalde bovenliggende resource. Als u bijvoorbeeld SQL wilt retour neren in azure VM-back-exemplaren die deel uitmaken van de virtuele machine "testvm", geeft u _testvm_ op als de waarde van deze para meter. Standaard is de waarde ' * ', waarmee de functie wordt gebruikt voor het zoeken naar records in alle back-upexemplaarën. | N | testvm |
| BackupInstanceName | Gebruik deze para meter om te zoeken naar taken op een bepaalde back-upinstantie op naam. Standaard is de waarde ' * ', waarmee de functie wordt gebruikt voor het zoeken naar records in alle back-upexemplaarën. | N | testvm |
| ExcludeLog | Gebruik deze para meter om logboek taken uit te sluiten die worden geretourneerd door de functie (helpt bij het uitvoeren van query prestaties). Standaard is de waarde van deze para meter True, waardoor de functie logboek taken uitsluiten. | N | true


**Geretourneerde velden**

| **Veld naam** | **Beschrijving** |
| -------------- | --------------- |
| UniqueId | Primaire sleutel voor het identificeren van de unieke ID van de taak |
| OperationCategory | Categorie van de bewerking die wordt uitgevoerd. Bijvoorbeeld back-ups maken, herstellen |
| Bewerking | Details van de bewerking die wordt uitgevoerd. Bijvoorbeeld logboek (voor back-up van Logboeken)|
| Status | De status van de taak. Bijvoorbeeld voltooid, mislukt, CompletedWithWarnings |
| ErrorTitle | Fout code van de taak |
| StartTime | De datum en tijd waarop de taak is gestart |
| DurationInSecs | Duur van de taak in seconden |
| DataTransferredInMBs | Gegevens die worden overgedragen door de taak in MBs |
| RestoreJobRPDateTime | De datum en tijd waarop het herstel punt dat wordt hersteld, is gemaakt |
| RestoreJobRPLocation | De locatie waar het herstel punt dat wordt hersteld is opgeslagen |
| BackupInstanceUniqueId | Refererende sleutel die verwijst naar het back-upexemplaar dat is gekoppeld aan de taak |
| BackupInstanceId | Azure Resource Manager (ARM) ID van het back-upexemplaar dat aan de taak is gekoppeld |
| BackupInstanceFriendlyName | De naam van het back-upexemplaar dat aan de taak is gekoppeld |
| DatasourceResourceId | Azure Resource Manager-ID (ARM) van de onderliggende gegevens bron die aan de taak is gekoppeld. Bijvoorbeeld Azure Resource Manager ARM-ID van de VM |
| DatasourceFriendlyName | Beschrijvende naam van de onderliggende gegevens bron die is gekoppeld aan de taak |
| Data Source Type | Het type gegevens bron dat is gekoppeld aan de taak. Bijvoorbeeld ' micro soft. Compute/informatie ' |
| BackupSolution | De back-upoplossing waaraan de taak is gekoppeld. Bijvoorbeeld Azure VM backup, SQL in azure VM backup, enzovoort. |
| DatasourceSetResourceId | Azure Resource Manager (ARM) ID van de bovenliggende bron van de gegevens bron (indien van toepassing). Voor een SQL in de gegevens bron van een Azure-VM bevat dit veld bijvoorbeeld de Azure Resource Manager ARM-ID van de virtuele machine waarin de SQL Database zich bevindt. |
| DatasourceSetType | Type van de bovenliggende resource van de gegevens bron (indien van toepassing). Voor een SAP HANA in de gegevens bron van een Azure-VM is dit veld bijvoorbeeld micro soft. Compute/informatie omdat de bovenliggende resource een Azure VM is |
| VaultResourceId | Azure Resource Manager-ID (ARM) van de kluis die aan de taak is gekoppeld |
| VaultUniqueId | Refererende sleutel die verwijst naar de kluis die aan de taak is gekoppeld |
| VaultName | De naam van de kluis die aan de taak is gekoppeld |
| VaultTags | Tags van de kluis die aan de taak is gekoppeld |
| VaultSubscriptionId | De abonnements-ID van de kluis die aan de taak is gekoppeld |
| VaultLocation | Locatie van de kluis die aan de taak is gekoppeld |
| VaultStore_StorageReplicationType | Type opslag replicatie van de kluis die aan de taak is gekoppeld |
| VaultType | Type kluis, dat is ' micro soft. Recovery Services/kluizen ' |
| TimeGenerated | Tijds tempel van de record |

#### <a name="_azurebackup_getbackupinstances"></a>_AzureBackup_GetBackupInstances ()

Deze functie retourneert de lijst met back-upinstanties die zijn gekoppeld aan uw Recovery Services-kluizen, samen met gedetailleerde informatie over elk back-upexemplaar, zoals het verbruik van Cloud opslag, het bijbehorende beleid, enzovoort.

**Parameters**

| **Parameter naam** | **Beschrijving** | **Vereist?** : | **Voorbeeldwaarde** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Gebruik deze para meter in combi natie met de para meter RangeEnd alleen als u alle records met een back-up-exemplaar moet ophalen uit de periode van Range Start naar RangeEnd. De waarde van Range Start en RangeEnd zijn standaard NULL, waardoor de functie alleen de meest recente record voor elk back-upexemplaar ophaalt. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Gebruik deze para meter samen met de Range Start-para meter alleen als u alle records met een back-upexemplaar moet ophalen uit de periode van Range Start naar RangeEnd. De waarde van Range Start en RangeEnd zijn standaard NULL, waardoor de functie alleen de meest recente record voor elk back-upexemplaar ophaalt. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Gebruik deze para meter om de uitvoer van de functie te filteren op een bepaalde set abonnementen waarbij back-upgegevens bestaan. Het opgeven van een door komma's gescheiden lijst met abonnements-Id's als een para meter voor deze functie helpt u bij het ophalen van de back-upinstanties die zich in de opgegeven abonnementen bevinden. Standaard is de waarde van deze para meter ' * ', waarmee de functie wordt doorzocht op records in alle abonnementen. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Gebruik deze para meter om de uitvoer van de functie te filteren op een bepaalde set regio's waarin de back-upgegevens bestaan. Het opgeven van een door komma's gescheiden lijst met regio's als een para meter voor deze functie helpt u bij het ophalen van de back-upinstanties die zich in de opgegeven regio's bevinden. Standaard is de waarde van deze para meter ' * ', waarmee de functie wordt doorzocht op records in alle regio's. | N | "Oost, westus"|
| VaultList    |Gebruik deze para meter om de uitvoer van de functie voor een bepaalde set kluizen te filteren. Het opgeven van een door komma's gescheiden lijst met kluis namen als een para meter voor deze functie helpt u bij het ophalen van records van back-upinstanties die alleen betrekking hebben op de opgegeven kluizen. Standaard is de waarde van deze para meter ' * ', waarmee de functie wordt doorzocht op records van back-upinstanties in alle kluizen. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Gebruik deze para meter om de uitvoer van de functie te filteren op records die betrekking hebben op een bepaald type kluis. Momenteel is het enige ondersteunde kluis type ' micro soft. Recovery Services/kluizen '. Dit is de standaard waarde van deze para meter. | N | "Micro soft. Recovery Services/kluizen"|
| ExcludeLegacyEvent  | Gebruik deze para meter om te kiezen of u wilt zoeken in gegevens in de verouderde tabel AzureDiagnostics of niet. Als de waarde van deze para meter False is, vraagt de functie gegevens op uit de tabel AzureDiagnostics en de resource-specifieke tabellen. Als de waarde van deze para meter True is, vraagt de functie alleen gegevens op uit de resource-specifieke tabellen. De standaardwaarde is Waar. | N | true |
| BackupSolutionList | Gebruik deze para meter om de uitvoer van de functie te filteren op een bepaalde set back-upoplossingen die worden gebruikt in uw Azure-omgeving. Als u bijvoorbeeld ' back-up van virtuele Azure-machine ', SQL in azure VM backup, DPM ' opgeeft als waarde van deze para meter, retourneert de functie alleen records die zijn gerelateerd aan items waarvan een back-up is gemaakt met behulp van back-ups van Azure virtual machine, SQL in azure VM backup of DPM naar Azure backup. De waarde van deze para meter is standaard ' * ', waardoor de functie records retourneert die betrekking hebben op alle back-upoplossingen die worden ondersteund door back-uprapporten (ondersteunde waarden zijn ' back-up van virtuele machine van Azure ', ' SQL in azure VM backup ', ' SAP HANA in azure VM backup ', ' Azure Storage (Azure Files) Backup ', ' Azure Backup Agent ', ' DPM ', ' Azure Backup Server ' of een door komma's gescheiden combi natie van een van deze waarden). | N | "Back-up van virtuele Azure-machine, SQL in azure VM backup, DPM, Azure Backup-Agent" |
| ProtectionInfoList | Gebruik deze para meter om te kiezen of u alleen de back-upinstanties wilt toevoegen die actief zijn, of dat u ook deze instanties wilt toevoegen waarvoor de beveiliging is gestopt en instanties waarvoor de eerste back-up in behandeling is. Ondersteunde waarden zijn ' Protected ', ' ProtectionStopped ', ' InitialBackupPending ' of een door komma's gescheiden combi natie van een van deze waarden. Standaard is de waarde ' * ', waarmee de functie wordt doorzocht op alle back-upexemplaarsen, ongeacht de beveiligings Details. | N | Dergelijke |
| DatasourceSetName | Gebruik deze para meter om de uitvoer van de functie te filteren op een bepaalde bovenliggende resource. Als u bijvoorbeeld SQL wilt retour neren in azure VM-back-exemplaren die deel uitmaken van de virtuele machine "testvm", geeft u _testvm_ op als de waarde van deze para meter. Standaard is de waarde ' * ', waarmee de functie wordt gebruikt voor het zoeken naar records in alle back-upexemplaarën. | N | testvm |
| BackupInstanceName | Gebruik deze para meter om op naam te zoeken naar een bepaald back-upexemplaar. Standaard is de waarde ' * ', waarmee de functie wordt doorzocht op alle back-upinstanties. | N | testvm |
| DisplayAllFields | Gebruik deze para meter om te kiezen of u alleen een subset wilt ophalen van de velden die door de functie worden geretourneerd. Als de waarde van deze para meter False is, elimineert de functie gegevens over de opslag en het bewaar punt uit de uitvoer van de functie. Dit is handig als u deze functie gebruikt als een tussenliggende stap in een grotere query en de prestaties van de query wilt optimaliseren door kolommen te verwijderen die u niet nodig hebt voor analyse. Standaard is de waarde van deze para meter True, waardoor de functie alle velden retourneert die betrekking hebben op het back-upexemplaar. | N | true |

**Geretourneerde velden**

| **Veld naam** | **Beschrijving** |
| -------------- | --------------- |
| UniqueId | Primaire sleutel voor het identificeren van de unieke ID van het back-upexemplaar |
| Id | Azure Resource Manager (ARM) ID van het back-upexemplaar |
| FriendlyName | Beschrijvende naam van het back-upexemplaar |
| ProtectionInfo | Informatie over de beveiligings instellingen van het back-upexemplaar. Bijvoorbeeld beveiliging geconfigureerd, beveiliging gestopt, eerste back-up in behandeling |
| LatestRecoveryPoint | Datum en tijd van het laatste herstel punt dat is gekoppeld aan het back-upexemplaar |
| OldestRecoveryPoint | De datum en tijd van het oudste herstel punt dat is gekoppeld aan het back-upexemplaar |
| SourceSizeInMBs | Front-end grootte van het back-upexemplaar in MBs |
| VaultStore_StorageConsumptionInMBs | Totale Cloud opslag die wordt gebruikt door het back-upexemplaar in de standaard-laag van de kluis |
| DataSourceFriendlyName | Beschrijvende naam van de gegevens bron die overeenkomt met het back-upexemplaar |
| BackupSolution | De back-upoplossing waaraan het back-upexemplaar is gekoppeld. Bijvoorbeeld Azure VM backup, SQL in azure VM backup, enzovoort. |
| Data Source Type | Type van de gegevens bron die overeenkomt met het back-upexemplaar. Bijvoorbeeld ' micro soft. Compute/informatie ' |
| DatasourceResourceId | Azure Resource Manager-ID (ARM) van de onderliggende gegevens bron die overeenkomt met het back-upexemplaar. Bijvoorbeeld Azure Resource Manager ARM-ID van de VM |
| DatasourceSetFriendlyName | Beschrijvende naam van de bovenliggende resource van de gegevens bron (indien van toepassing). Voor een SQL in de gegevens bron van een Azure-VM bevat dit veld bijvoorbeeld de naam van de virtuele machine waarin de SQL Database bestaat |
| DatasourceSetResourceId | Azure Resource Manager (ARM) ID van de bovenliggende bron van de gegevens bron (indien van toepassing). Voor een SQL in de gegevens bron van een Azure-VM bevat dit veld bijvoorbeeld de Azure Resource Manager ARM-ID van de virtuele machine waarin de SQL Database zich bevindt. |
| DatasourceSetType | Type van de bovenliggende resource van de gegevens bron (indien van toepassing). Voor een SAP HANA in de gegevens bron van een Azure-VM is dit veld bijvoorbeeld micro soft. Compute/informatie omdat de bovenliggende resource een Azure VM is |
| PolicyName | De naam van het beleid dat is gekoppeld aan het back-upexemplaar |
| PolicyUniqueId | Refererende sleutel die verwijst naar het beleid dat is gekoppeld aan het back-upexemplaar  |
| PolicyId | Azure Resource Manager (ARM) ID van het beleid dat is gekoppeld aan het back-upexemplaar |
| VaultResourceId | Azure Resource Manager (ARM) ID van de kluis die is gekoppeld aan het back-upexemplaar |
| VaultUniqueId | Refererende sleutel die verwijst naar de kluis die is gekoppeld aan het back-upexemplaar |
| VaultName | De naam van de kluis die is gekoppeld aan het back-upexemplaar |
| VaultTags | Tags van de kluis die is gekoppeld aan het back-upexemplaar |
| VaultSubscriptionId | Abonnements-ID van de kluis die is gekoppeld aan het back-upexemplaar |
| VaultLocation | Locatie van de kluis die is gekoppeld aan het back-upexemplaar |
| VaultStore_StorageReplicationType | Type opslag replicatie van de kluis die is gekoppeld aan het back-upexemplaar |
| VaultType | Type kluis, dat is ' micro soft. Recovery Services/kluizen ' |
| TimeGenerated | Tijds tempel van de record |

#### <a name="_azurebackup_getbillinggroups"></a>_AzureBackup_GetBillingGroups ()

Deze functie retourneert een lijst met alle back-gerelateerde facturerings entiteiten (facturerings groepen), samen met informatie over de belangrijkste facturerings onderdelen, zoals de frontend-grootte en de totale Cloud opslag.

**Parameters**

| **Parameter naam** | **Beschrijving** | **Vereist?** : | **Voorbeeldwaarde** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Gebruik deze para meter samen met de para meter RangeEnd alleen als u alle gerelateerde records van de facturerings groep in de periode van Range Start naar RangeEnd moet ophalen. De waarde van Range Start en RangeEnd zijn standaard NULL, waardoor de functie alleen de meest recente record voor elke facturerings groep ophaalt. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Gebruik deze para meter samen met de Range Start-para meter alleen als u alle gerelateerde records van de facturerings groep in de periode van Range Start naar RangeEnd moet ophalen. De waarde van Range Start en RangeEnd zijn standaard NULL, waardoor de functie alleen de meest recente record voor elke facturerings groep ophaalt. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Gebruik deze para meter om de uitvoer van de functie te filteren op een bepaalde set abonnementen waarbij back-upgegevens bestaan. Het opgeven van een door komma's gescheiden lijst met abonnements-Id's als een para meter voor deze functie helpt u bij het ophalen van de facturerings groepen die zich in de opgegeven abonnementen bevinden. Standaard is de waarde van deze para meter ' * ', waarmee de functie wordt doorzocht op records in alle abonnementen. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Gebruik deze para meter om de uitvoer van de functie te filteren op een bepaalde set regio's waarin de back-upgegevens bestaan. Het opgeven van een door komma's gescheiden lijst met regio's als een para meter voor deze functie helpt u bij het ophalen van de facturerings groepen die zich in de opgegeven regio's bevinden. Standaard is de waarde van deze para meter ' * ', waarmee de functie wordt doorzocht op records in alle regio's. | N | "Oost, westus"|
| VaultList    |Gebruik deze para meter om de uitvoer van de functie voor een bepaalde set kluizen te filteren. Het opgeven van een door komma's gescheiden lijst met kluis namen als een para meter voor deze functie helpt u bij het ophalen van records van back-upinstanties die alleen betrekking hebben op de opgegeven kluizen. Standaard is de waarde van deze para meter ' * ', waarmee de functie wordt doorzocht op records van facturerings groepen in alle kluizen. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Gebruik deze para meter om de uitvoer van de functie te filteren op records die betrekking hebben op een bepaald type kluis. Momenteel is het enige ondersteunde kluis type ' micro soft. Recovery Services/kluizen '. Dit is de standaard waarde van deze para meter. | N | "Micro soft. Recovery Services/kluizen"|
| ExcludeLegacyEvent  | Gebruik deze para meter om te kiezen of u wilt zoeken in gegevens in de verouderde tabel AzureDiagnostics of niet. Als de waarde van deze para meter False is, vraagt de functie gegevens op uit de tabel AzureDiagnostics en de resource-specifieke tabellen. Als de waarde van deze para meter True is, vraagt de functie alleen gegevens op uit de resource-specifieke tabellen. De standaardwaarde is Waar. | N | true |
| BackupSolutionList | Gebruik deze para meter om de uitvoer van de functie te filteren op een bepaalde set back-upoplossingen die worden gebruikt in uw Azure-omgeving. Als u bijvoorbeeld ' back-up van virtuele Azure-machine ', SQL in azure VM backup, DPM ' opgeeft als waarde van deze para meter, retourneert de functie alleen records die zijn gerelateerd aan items waarvan een back-up is gemaakt met behulp van back-ups van Azure virtual machine, SQL in azure VM backup of DPM naar Azure backup. De waarde van deze para meter is standaard ' * ', waardoor de functie records retourneert die betrekking hebben op alle back-upoplossingen die worden ondersteund door back-uprapporten (ondersteunde waarden zijn ' back-up van virtuele machine van Azure ', ' SQL in azure VM backup ', ' SAP HANA in azure VM backup ', ' Azure Storage (Azure Files) Backup ', ' Azure Backup Agent ', ' DPM ', ' Azure Backup Server ' of een door komma's gescheiden combi natie van een van deze waarden). | N | "Back-up van virtuele Azure-machine, SQL in azure VM backup, DPM, Azure Backup-Agent" |
| BillingGroupName | Gebruik deze para meter om op naam te zoeken naar een bepaalde facturerings groep. Standaard is de waarde ' * ', waarmee de functie wordt gebruikt om te zoeken naar alle facturerings groepen. | N | testvm |

**Geretourneerde velden**

| **Veld naam** | **Beschrijving** |
| -------------- | --------------- |
| UniqueId | Primaire sleutel voor het identificeren van de unieke ID van de facturerings groep |
| FriendlyName | Beschrijvende naam van de facturerings groep |
| Name | Naam van de facturerings groep |
| Type | Type facturerings groep. Bijvoorbeeld ProtectedContainer of BackupItem |
| SourceSizeInMBs | Front-end grootte van de facturerings groep in MBs |
| VaultStore_StorageConsumptionInMBs | Totale Cloud opslag die door de facturerings groep in de kluis-Standard-laag wordt gebruikt |
| BackupSolution | De back-upoplossing waaraan de facturerings groep is gekoppeld. Bijvoorbeeld Azure VM backup, SQL in azure VM backup, enzovoort. |
| VaultResourceId | Azure Resource Manager-ID (ARM) van de kluis die aan de facturerings groep is gekoppeld |
| VaultUniqueId | Refererende sleutel die verwijst naar de kluis die aan de facturerings groep is gekoppeld |
| VaultName | De naam van de kluis die aan de facturerings groep is gekoppeld |
| VaultTags | Tags van de kluis die aan de facturerings groep is gekoppeld |
| VaultSubscriptionId | Abonnements-ID van de kluis die aan de facturerings groep is gekoppeld |
| VaultLocation | Locatie van de kluis die aan de facturerings groep is gekoppeld |
| VaultStore_StorageReplicationType | Type opslag replicatie van de kluis die aan de facturerings groep is gekoppeld |
| VaultType | Type kluis, dat is ' micro soft. Recovery Services/kluizen ' |
| TimeGenerated | Tijds tempel van de record |
| ExtendedProperties | Aanvullende eigenschappen van de facturerings groep |

### <a name="trend-functions"></a>Trend functies

#### <a name="_azurebackup_getbackupinstancestrends"></a>_AzureBackup_GetBackupInstancesTrends ()

Deze functie retourneert historische records voor elk back-upexemplaar, zodat u dagelijkse, wekelijkse en maandelijkse trends kunt bekijken die betrekking hebben op het aantal back-instances en het opslag verbruik, op meerdere niveaus van granulariteit.

**Parameters**

| **Parameter naam** | **Beschrijving** | **Vereist?** : | **Voorbeeldwaarde** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Gebruik deze para meter in combi natie met de para meter RangeEnd om alle gerelateerde records van het back-upexemplaar op te halen in de periode van Range Start naar RangeEnd. | J | "2021-03-03 00:00:00" |
| RangeEnd     | Gebruik deze para meter samen met de Range Start-para meter om alle gerelateerde records van het back-upexemplaar op te halen in de periode van Range Start naar RangeEnd. | J |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Gebruik deze para meter om de uitvoer van de functie te filteren op een bepaalde set abonnementen waarbij back-upgegevens bestaan. Het opgeven van een door komma's gescheiden lijst met abonnements-Id's als een para meter voor deze functie helpt u bij het ophalen van de back-upinstanties die zich in de opgegeven abonnementen bevinden. Standaard is de waarde van deze para meter ' * ', waarmee de functie wordt doorzocht op records in alle abonnementen. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Gebruik deze para meter om de uitvoer van de functie te filteren op een bepaalde set regio's waarin de back-upgegevens bestaan. Het opgeven van een door komma's gescheiden lijst met regio's als een para meter voor deze functie helpt u bij het ophalen van de back-upinstanties die zich in de opgegeven regio's bevinden. Standaard is de waarde van deze para meter ' * ', waarmee de functie wordt doorzocht op records in alle regio's. | N | "Oost, westus"|
| VaultList    |Gebruik deze para meter om de uitvoer van de functie voor een bepaalde set kluizen te filteren. Het opgeven van een door komma's gescheiden lijst met kluis namen als een para meter voor deze functie helpt u bij het ophalen van records van back-upinstanties die alleen betrekking hebben op de opgegeven kluizen. Standaard is de waarde van deze para meter ' * ', waarmee de functie wordt doorzocht op records van back-upinstanties in alle kluizen. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Gebruik deze para meter om de uitvoer van de functie te filteren op records die betrekking hebben op een bepaald type kluis. Momenteel is het enige ondersteunde kluis type ' micro soft. Recovery Services/kluizen '. Dit is de standaard waarde van deze para meter. | N | "Micro soft. Recovery Services/kluizen"|
| ExcludeLegacyEvent  | Gebruik deze para meter om te kiezen of u wilt zoeken in gegevens in de verouderde tabel AzureDiagnostics of niet. Als de waarde van deze para meter False is, vraagt de functie gegevens op uit de tabel AzureDiagnostics en de resource-specifieke tabellen. Als de waarde van deze para meter True is, vraagt de functie alleen gegevens op uit de resource-specifieke tabellen. De standaardwaarde is Waar. | N | true |
| BackupSolutionList | Gebruik deze para meter om de uitvoer van de functie te filteren op een bepaalde set back-upoplossingen die worden gebruikt in uw Azure-omgeving. Als u bijvoorbeeld ' back-up van virtuele Azure-machine ', SQL in azure VM backup, DPM ' opgeeft als waarde van deze para meter, retourneert de functie alleen records die zijn gerelateerd aan items waarvan een back-up is gemaakt met behulp van back-ups van Azure virtual machine, SQL in azure VM backup of DPM naar Azure backup. De waarde van deze para meter is standaard ' * ', waardoor de functie records retourneert die betrekking hebben op alle back-upoplossingen die worden ondersteund door back-uprapporten (ondersteunde waarden zijn ' back-up van virtuele machine van Azure ', ' SQL in azure VM backup ', ' SAP HANA in azure VM backup ', ' Azure Storage (Azure Files) Backup ', ' Azure Backup Agent ', ' DPM ', ' Azure Backup Server ' of een door komma's gescheiden combi natie van een van deze waarden). | N | "Back-up van virtuele Azure-machine, SQL in azure VM backup, DPM, Azure Backup-Agent" |
| ProtectionInfoList | Gebruik deze para meter om te kiezen of u alleen de back-upinstanties wilt toevoegen die actief zijn, of dat u ook deze instanties wilt toevoegen waarvoor de beveiliging is gestopt en instanties waarvoor de eerste back-up in behandeling is. Ondersteunde waarden zijn ' Protected ', ' ProtectionStopped ', ' InitialBackupPending ' of een door komma's gescheiden combi natie van een van deze waarden. Standaard is de waarde ' * ', waarmee de functie wordt doorzocht op alle back-upexemplaarsen, ongeacht de beveiligings Details. | N | Dergelijke |
| DatasourceSetName | Gebruik deze para meter om de uitvoer van de functie te filteren op een bepaalde bovenliggende resource. Als u bijvoorbeeld SQL wilt retour neren in azure VM-back-exemplaren die deel uitmaken van de virtuele machine "testvm", geeft u _testvm_ op als de waarde van deze para meter. Standaard is de waarde ' * ', waarmee de functie wordt gebruikt voor het zoeken naar records in alle back-upexemplaarën. | N | testvm |
| BackupInstanceName | Gebruik deze para meter om op naam te zoeken naar een bepaald back-upexemplaar. Standaard is de waarde ' * ', waarmee de functie wordt doorzocht op alle back-upinstanties. | N | testvm |
| DisplayAllFields | Gebruik deze para meter om te kiezen of u alleen een subset wilt ophalen van de velden die door de functie worden geretourneerd. Als de waarde van deze para meter False is, elimineert de functie gegevens over de opslag en het bewaar punt uit de uitvoer van de functie. Dit is handig als u deze functie gebruikt als een tussenliggende stap in een grotere query en de prestaties van de query wilt optimaliseren door kolommen te verwijderen die u niet nodig hebt voor analyse. Standaard is de waarde van deze para meter True, waardoor de functie alle velden retourneert die betrekking hebben op het back-upexemplaar. | N | true |
| AggregationType | Gebruik deze para meter om de tijd granulatie op te geven waarmee gegevens moeten worden opgehaald. Als de waarde van deze para meter "dagelijks" is, retourneert de functie een record per back-upexemplaar per dag, zodat u de dagelijkse trends van het opslag verbruik en het aantal back-upinstanties kunt analyseren. Als de waarde van deze para meter ' Wekelijks ' is, retourneert de functie een record per back-upexemplaar per week, zodat u wekelijkse trends kunt analyseren. U kunt ook ' maandelijks ' opgeven om maandelijkse trends te analyseren. De standaard waarde is "dagelijks". Als u gegevens in grotere Peri Oden bekijkt, is het raadzaam om ' Wekelijks ' of ' maandelijks ' te gebruiken voor betere query prestaties en een gemakkelijke analyse van trend. | N | Laten |

**Geretourneerde velden**

| **Veld naam** | **Beschrijving** |
| -------------- | --------------- |
| UniqueId | Primaire sleutel voor het identificeren van de unieke ID van het back-upexemplaar |
| Id | Azure Resource Manager (ARM) ID van het back-upexemplaar |
| FriendlyName | Beschrijvende naam van het back-upexemplaar |
| ProtectionInfo | Informatie over de beveiligings instellingen van het back-upexemplaar. Bijvoorbeeld beveiliging geconfigureerd, beveiliging gestopt, eerste back-up in behandeling |
| LatestRecoveryPoint | Datum en tijd van het laatste herstel punt dat is gekoppeld aan het back-upexemplaar |
| OldestRecoveryPoint | De datum en tijd van het oudste herstel punt dat is gekoppeld aan het back-upexemplaar |
| SourceSizeInMBs | Front-end grootte van het back-upexemplaar in MBs |
| VaultStore_StorageConsumptionInMBs | Totale Cloud opslag die wordt gebruikt door het back-upexemplaar in de standaard-laag van de kluis |
| DataSourceFriendlyName | Beschrijvende naam van de gegevens bron die overeenkomt met het back-upexemplaar |
| BackupSolution | De back-upoplossing waaraan het back-upexemplaar is gekoppeld. Bijvoorbeeld Azure VM backup, SQL in azure VM backup, enzovoort. |
| Data Source Type | Type van de gegevens bron die overeenkomt met het back-upexemplaar. Bijvoorbeeld ' micro soft. Compute/informatie ' |
| DatasourceResourceId | Azure Resource Manager-ID (ARM) van de onderliggende gegevens bron die overeenkomt met het back-upexemplaar. Bijvoorbeeld Azure Resource Manager ARM-ID van de VM |
| DatasourceSetFriendlyName | Beschrijvende naam van de bovenliggende resource van de gegevens bron (indien van toepassing). Voor een SQL in de gegevens bron van een Azure-VM bevat dit veld bijvoorbeeld de naam van de virtuele machine waarin de SQL Database bestaat |
| DatasourceSetResourceId | Azure Resource Manager (ARM) ID van de bovenliggende bron van de gegevens bron (indien van toepassing). Voor een SQL in de gegevens bron van een Azure-VM bevat dit veld bijvoorbeeld de Azure Resource Manager ARM-ID van de virtuele machine waarin de SQL Database zich bevindt. |
| DatasourceSetType | Type van de bovenliggende resource van de gegevens bron (indien van toepassing). Voor een SAP HANA in de gegevens bron van een Azure-VM is dit veld bijvoorbeeld micro soft. Compute/informatie omdat de bovenliggende resource een Azure VM is |
| PolicyName | De naam van het beleid dat is gekoppeld aan het back-upexemplaar |
| PolicyUniqueId | Refererende sleutel die verwijst naar het beleid dat is gekoppeld aan het back-upexemplaar  |
| PolicyId | Azure Resource Manager (ARM) ID van het beleid dat is gekoppeld aan het back-upexemplaar |
| VaultResourceId | Azure Resource Manager (ARM) ID van de kluis die is gekoppeld aan het back-upexemplaar |
| VaultUniqueId | Refererende sleutel die verwijst naar de kluis die is gekoppeld aan het back-upexemplaar |
| VaultName | De naam van de kluis die is gekoppeld aan het back-upexemplaar |
| VaultTags | Tags van de kluis die is gekoppeld aan het back-upexemplaar |
| VaultSubscriptionId | Abonnements-ID van de kluis die is gekoppeld aan het back-upexemplaar |
| VaultLocation | Locatie van de kluis die is gekoppeld aan het back-upexemplaar |
| VaultStore_StorageReplicationType | Type opslag replicatie van de kluis die is gekoppeld aan het back-upexemplaar |
| VaultType | Type kluis, dat is ' micro soft. Recovery Services/kluizen ' |
| TimeGenerated | Tijds tempel van de record |

#### <a name="_azurebackup_getbillinggroupstrends"></a>_AzureBackup_GetBillingGroupsTrends ()

Deze functie retourneert historische records voor elke facturerings entiteit, zodat u dagelijkse, wekelijkse en maandelijkse trends kunt bekijken die betrekking hebben op de grootte van het front-end-en opslag verbruik, op meerdere niveaus van granulariteit.

**Parameters**

| **Parameter naam** | **Beschrijving** | **Vereist?** : | **Voorbeeldwaarde** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Gebruik deze para meter in combi natie met de para meter RangeEnd om alle gerelateerde records van een facturerings groep op te halen uit de periode van Range Start naar RangeEnd. | J | "2021-03-03 00:00:00" |
| RangeEnd     | Gebruik deze para meter samen met de Range Start-para meter om alle gerelateerde records van de facturerings groep op te halen uit de periode van Range Start naar RangeEnd. | J |"2021-03-10 00:00:00"|
| VaultSubscriptionList  | Gebruik deze para meter om de uitvoer van de functie te filteren op een bepaalde set abonnementen waarbij back-upgegevens bestaan. Het opgeven van een door komma's gescheiden lijst met abonnements-Id's als een para meter voor deze functie helpt u bij het ophalen van de facturerings groepen die zich in de opgegeven abonnementen bevinden. Standaard is de waarde van deze para meter ' * ', waarmee de functie wordt doorzocht op records in alle abonnementen. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Gebruik deze para meter om de uitvoer van de functie te filteren op een bepaalde set regio's waarin de back-upgegevens bestaan. Het opgeven van een door komma's gescheiden lijst met regio's als een para meter voor deze functie helpt u bij het ophalen van de facturerings groepen die zich in de opgegeven regio's bevinden. Standaard is de waarde van deze para meter ' * ', waarmee de functie wordt doorzocht op records in alle regio's. | N | "Oost, westus"|
| VaultList    |Gebruik deze para meter om de uitvoer van de functie voor een bepaalde set kluizen te filteren. Het opgeven van een door komma's gescheiden lijst met kluis namen als een para meter voor deze functie helpt u bij het ophalen van records van back-upinstanties die alleen betrekking hebben op de opgegeven kluizen. Standaard is de waarde van deze para meter ' * ', waarmee de functie wordt doorzocht op records van facturerings groepen in alle kluizen. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Gebruik deze para meter om de uitvoer van de functie te filteren op records die betrekking hebben op een bepaald type kluis. Momenteel is het enige ondersteunde kluis type ' micro soft. Recovery Services/kluizen '. Dit is de standaard waarde van deze para meter. | N | "Micro soft. Recovery Services/kluizen"|
| ExcludeLegacyEvent  | Gebruik deze para meter om te kiezen of u wilt zoeken in gegevens in de verouderde tabel AzureDiagnostics of niet. Als de waarde van deze para meter False is, vraagt de functie gegevens op uit de tabel AzureDiagnostics en de resource-specifieke tabellen. Als de waarde van deze para meter True is, vraagt de functie alleen gegevens op uit de resource-specifieke tabellen. De standaardwaarde is Waar. | N | true |
| BackupSolutionList | Gebruik deze para meter om de uitvoer van de functie te filteren op een bepaalde set back-upoplossingen die worden gebruikt in uw Azure-omgeving. Als u bijvoorbeeld ' back-up van virtuele Azure-machine ', SQL in azure VM backup, DPM ' opgeeft als waarde van deze para meter, retourneert de functie alleen records die zijn gerelateerd aan items waarvan een back-up is gemaakt met behulp van back-ups van Azure virtual machine, SQL in azure VM backup of DPM naar Azure backup. De waarde van deze para meter is standaard ' * ', waardoor de functie records retourneert die betrekking hebben op alle back-upoplossingen die worden ondersteund door back-uprapporten (ondersteunde waarden zijn ' back-up van virtuele machine van Azure ', ' SQL in azure VM backup ', ' SAP HANA in azure VM backup ', ' Azure Storage (Azure Files) Backup ', ' Azure Backup Agent ', ' DPM ', ' Azure Backup Server ' of een door komma's gescheiden combi natie van een van deze waarden). | N | "Back-up van virtuele Azure-machine, SQL in azure VM backup, DPM, Azure Backup-Agent" |
| BillingGroupName | Gebruik deze para meter om op naam te zoeken naar een bepaalde facturerings groep. Standaard is de waarde ' * ', waarmee de functie wordt gebruikt om te zoeken naar alle facturerings groepen. | N | testvm |
| AggregationType | Gebruik deze para meter om de tijd granulatie op te geven waarmee gegevens moeten worden opgehaald. Als de waarde van deze para meter "dagelijks" is, retourneert de functie een record per facturerings groep per dag, zodat u de dagelijkse trends van het opslag verbruik en de frontend-grootte kunt analyseren. Als de waarde van deze para meter ' Wekelijks ' is, retourneert de functie een record per back-upexemplaar per week, zodat u wekelijkse trends kunt analyseren. U kunt ook ' maandelijks ' opgeven om maandelijkse trends te analyseren. De standaard waarde is "dagelijks". Als u gegevens in grotere Peri Oden bekijkt, is het raadzaam om ' Wekelijks ' of ' maandelijks ' te gebruiken voor betere query prestaties en een gemakkelijke analyse van trend. | N | Laten |

**Geretourneerde velden**

| **Veld naam** | **Beschrijving** |
| -------------- | --------------- |
| UniqueId | Primaire sleutel voor het identificeren van de unieke ID van de facturerings groep |
| FriendlyName | Beschrijvende naam van de facturerings groep |
| Name | Naam van de facturerings groep |
| Type | Type facturerings groep. Bijvoorbeeld ProtectedContainer of BackupItem |
| SourceSizeInMBs | Front-end grootte van de facturerings groep in MBs |
| VaultStore_StorageConsumptionInMBs | Totale Cloud opslag die door de facturerings groep in de kluis-Standard-laag wordt gebruikt |
| BackupSolution | De back-upoplossing waaraan de facturerings groep is gekoppeld. Bijvoorbeeld Azure VM backup, SQL in azure VM backup, enzovoort. |
| VaultResourceId | Azure Resource Manager-ID (ARM) van de kluis die aan de facturerings groep is gekoppeld |
| VaultUniqueId | Refererende sleutel die verwijst naar de kluis die aan de facturerings groep is gekoppeld |
| VaultName | De naam van de kluis die aan de facturerings groep is gekoppeld |
| VaultTags | Tags van de kluis die aan de facturerings groep is gekoppeld |
| VaultSubscriptionId | Abonnements-ID van de kluis die aan de facturerings groep is gekoppeld |
| VaultLocation | Locatie van de kluis die aan de facturerings groep is gekoppeld |
| VaultStore_StorageReplicationType | Type opslag replicatie van de kluis die aan de facturerings groep is gekoppeld |
| VaultType | Type kluis, dat is ' micro soft. Recovery Services/kluizen ' |
| TimeGenerated | Tijds tempel van de record |
| ExtendedProperties | Aanvullende eigenschappen van de facturerings groep |

## <a name="sample-queries"></a>Voorbeeld Query's

Hieronder vindt u enkele voor beelden van query's waarmee u aan de slag kunt gaan met het gebruik van systeem functies.

- Alle mislukte back-uptaken van Azure VM binnen een bepaald tijds bereik

    ````Kusto
    _AzureBackup_GetJobs("2021-03-05", "2021-03-06") //call function with RangeStart and RangeEnd parameters set, and other parameters with default value
    | where BackupSolution=="Azure Virtual Machine Backup" and Status=="Failed"
    | project BackupInstanceFriendlyName, BackupInstanceId, OperationCategory, Status,  JobStartDateTime=StartTime, JobDuration=DurationInSecs/3600, ErrorTitle, DataTransferred=DataTransferredInMBs
    ````

- Alle back-uptaken van SQL-logboek in een bepaald tijds bereik

    ````Kusto
    _AzureBackup_GetJobs("2021-03-05", "2021-03-06","*","*","*","*",true,"*","*","*","*","*","*",false) //call function with RangeStart and RangeEnd parameters set, ExcludeLog parameter as false, and other parameters with default value
    | where BackupSolution=="SQL in Azure VM Backup" and Operation=="Log"
    | project BackupInstanceFriendlyName, BackupInstanceId, OperationCategory, Status,  JobStartDateTime=StartTime, JobDuration=DurationInSecs/3600, ErrorTitle, DataTransferred=DataTransferredInMBs
    ````

- Wekelijkse trend van verbruikte back-upopslag voor VM ' testvm '

    ````Kusto
    _AzureBackup_GetBackupInstancesTrends("2021-01-01", "2021-03-06","*","*","*","*",false,"*","*","*","*",true, "Weekly") //call function with RangeStart and RangeEnd parameters set, AggregationType parameter as Weekly, and other parameters with default value
    | where BackupSolution == "Azure Virtual Machine Backup"
    | where FriendlyName == "testvm"
    | project TimeGenerated, VaultStore_StorageConsumptionInMBs
    | render timechart 
    ````

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over back-uprapporten](./configure-reports.md)