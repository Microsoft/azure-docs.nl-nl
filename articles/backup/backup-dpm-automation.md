---
title: Power shell gebruiken voor het maken van back-ups van DPM-workloads
description: Meer informatie over het implementeren en beheren van Azure Backup voor Data Protection Manager (DPM) met behulp van Power shell
ms.topic: conceptual
ms.date: 01/23/2017
ms.openlocfilehash: 176cbffe5152462055c4ffdb2367cf9c0ab97c1f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90968307"
---
# <a name="deploy-and-manage-backup-to-azure-for-data-protection-manager-dpm-servers-using-powershell"></a>Met behulp van PowerShell back-ups implementeren en beheren in Azure voor Data Protection Manager (DPM)-servers

In dit artikel wordt beschreven hoe u Power shell gebruikt om Azure Backup in te stellen op een DPM-server en om back-up en herstel te beheren.

## <a name="setting-up-the-powershell-environment"></a>De Power shell-omgeving instellen

Voordat u Power shell kunt gebruiken voor het beheren van back-ups van Data Protection Manager naar Azure, moet u de juiste omgeving hebben in Power shell. Aan het begin van de Power shell-sessie zorgt u ervoor dat u de volgende opdracht uitvoert om de juiste modules te importeren en kunt u op de juiste wijze verwijzen naar de DPM-cmdlets:

```powershell
& "C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin\DpmCliInitScript.ps1"
```

```Output
Welcome to the DPM Management Shell!

Full list of cmdlets: Get-Command
Only DPM cmdlets: Get-DPMCommand
Get general help: help
Get help for a cmdlet: help <cmdlet-name> or <cmdlet-name> -?
Get definition of a cmdlet: Get-Command <cmdlet-name> -Syntax
Sample DPM scripts: Get-DPMSampleScript
```

## <a name="setup-and-registration"></a>Installatie en registratie

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Down load de nieuwste Azure PowerShell](/powershell/azure/install-az-ps)om te beginnen.

De volgende installatie-en registratie taken kunnen worden geautomatiseerd met Power shell:

* Een Recovery Services-kluis maken
* De Azure Backup-Agent installeren
* Registreren bij de Azure Backup-Service
* Netwerk instellingen
* Versleutelingsinstellingen

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

De volgende stappen leiden u door het maken van een Recovery Services kluis. Een Recovery Services kluis wijkt af van een back-upkluis.

1. Als u Azure Backup voor de eerste keer gebruikt, moet u de cmdlet **REGI ster-AzResourceProvider** gebruiken om de Azure Recovery service-provider te registreren bij uw abonnement.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. De Recovery Services kluis is een ARM-resource, dus moet u deze in een resource groep plaatsen. U kunt een bestaande resource groep gebruiken of een nieuwe maken. Wanneer u een nieuwe resource groep maakt, geeft u de naam en de locatie voor de resource groep op.

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "West US"
    ```

3. Gebruik de cmdlet **New-AzRecoveryServicesVault** om een nieuwe kluis te maken. Zorg ervoor dat u dezelfde locatie opgeeft als voor de kluis die voor de resource groep is gebruikt.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```

4. Geef het type opslag redundantie op dat moet worden gebruikt. U kunt [lokaal redundante opslag (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage), [geografisch redundante opslag (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage)of [zone-redundante opslag (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage)gebruiken. In het volgende voor beeld ziet u de optie **BackupStorageRedundancy** voor *testVault*  ingesteld op **georedundant**.

   > [!TIP]
   > Voor veel Azure Backup-cmdlets is het object Recovery Services-kluis als invoer vereist. Daarom is het handiger het object Backup Recovery Services-kluis in een variabele op te slaan.
   >
   >

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>De kluizen in een abonnement weer geven

Gebruik **Get-AzRecoveryServicesVault** om de lijst met alle kluizen in het huidige abonnement weer te geven. U kunt deze opdracht gebruiken om te controleren of een nieuwe kluis is gemaakt, of om te zien welke kluizen beschikbaar zijn in het abonnement.

Voer de opdracht Get-AzRecoveryServicesVault uit en alle kluizen in het abonnement worden weer gegeven.

```powershell
Get-AzRecoveryServicesVault
```

```Output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="installing-the-azure-backup-agent-on-a-dpm-server"></a>De Azure Backup-Agent op een DPM-server installeren

Voordat u de Azure Backup-Agent installeert, moet u het installatie programma downloaden en op de Windows-Server presen teren. U kunt de meest recente versie van het installatie programma downloaden van het [micro soft Download centrum](https://aka.ms/azurebackup_agent) of van de pagina dash board van de Recovery Services kluis. Sla het installatie programma op een gemakkelijk toegankelijke locatie op, zoals `C:\Downloads\*` .

Als u de agent wilt installeren, voert u de volgende opdracht uit in een Power shell-console met verhoogde bevoegdheid **op de DPM-server**:

```powershell
MARSAgentInstaller.exe /q
```

Hiermee installeert u de agent met alle standaard opties. De installatie duurt enkele minuten op de achtergrond. Als u de optie */nu* niet opgeeft, wordt het venster **Windows Update** geopend aan het einde van de installatie om te controleren of er updates zijn.

De agent wordt weer gegeven in de lijst met geïnstalleerde Program ma's. Ga naar **configuratie scherm**  >  **Program** ma's  >  **en onderdelen** om de lijst met geïnstalleerde Program ma's weer te geven.

![Agent geïnstalleerd](./media/backup-dpm-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Installatieopties

Als u alle beschik bare opties wilt weer geven via de opdracht regel, gebruikt u de volgende opdracht:

```powershell
MARSAgentInstaller.exe /?
```

De beschik bare opties zijn onder andere:

| Optie | Details | Standaard |
| --- | --- | --- |
| /q |Stille installatie |- |
| /p: "locatie" |Het pad naar de installatiemap voor de Azure Backup-Agent. |C:\Program Files\Microsoft Azure Recovery Services-agent |
| /s: "locatie" |Het pad naar de cachemap voor de Azure Backup-Agent. |C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Opt-in Microsoft Update |- |
| /nu |Niet controleren op updates nadat de installatie is voltooid |- |
| /d |Hiermee wordt Microsoft Azure Recovery Services agent verwijderd |- |
| /ph |Hostadres van proxy |- |
| /po |Poort nummer van de proxy-host |- |
| /pu |Gebruikers naam proxy host |- |
| /pw |Proxy wachtwoord |- |

## <a name="registering-dpm-to-a-recovery-services-vault"></a>DPM registreren bij een Recovery Services kluis

Nadat u de Recovery Services kluis hebt gemaakt, downloadt u de nieuwste agent en de kluis referenties en slaat u deze op op een handige locatie zoals C:\Downloads.

```powershell
$credspath = "C:\downloads"
$credsfilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
$credsfilename
```

```Output
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

Voer op de DPM-server de cmdlet [Start-OBRegistration](/powershell/module/msonlinebackup/start-obregistration) uit om de computer bij de kluis te registreren.

```powershell
$cred = $credspath + $credsfilename
Start-OBRegistration-VaultCredentials $cred -Confirm:$false
```

```Output
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

### <a name="initial-configuration-settings"></a>Initiële configuratie-instellingen

Zodra de DPM-server is geregistreerd bij de Recovery Services kluis, begint de standaard instellingen voor abonnementen. Deze abonnements instellingen omvatten netwerken, versleuteling en het Faserings gebied. Als u de abonnements instellingen wilt wijzigen, moet u eerst een ingang ophalen voor de bestaande (standaard) instellingen met behulp van de cmdlet [Get-DPMCloudSubscriptionSetting](/powershell/module/dataprotectionmanager/get-dpmcloudsubscriptionsetting) :

```powershell
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Alle wijzigingen worden aangebracht in dit lokale Power shell-object ```$setting```  , waarna het volledige object wordt doorgevoerd in DPM en Azure backup worden opgeslagen met behulp van de cmdlet [set-DPMCloudSubscriptionSetting](/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting) . U moet de vlag gebruiken ```–Commit``` om ervoor te zorgen dat de wijzigingen behouden blijven. De instellingen worden niet toegepast en worden gebruikt door Azure Backup tenzij ze worden vastgelegd.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="networking"></a>Netwerken

Als de verbinding van de DPM-computer met de Azure Backup-service op Internet via een proxy server wordt uitgevoerd, moeten de proxyserver instellingen worden ingesteld voor geslaagde back-ups. Dit wordt gedaan met behulp van de ```-ProxyServer``` ```-ProxyPort``` -en- ```-ProxyUsername``` en- ```ProxyPassword``` para meters met de cmdlet [set-DPMCloudSubscriptionSetting](/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting) . In dit voor beeld is er geen proxy server, dus we verwijderen expliciet informatie over de proxy.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

Bandbreedte gebruik kan ook worden beheerd met opties van ```-WorkHourBandwidth``` en ```-NonWorkHourBandwidth``` voor een bepaalde set dagen van de week. In dit voor beeld stellen we geen beperking in.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

## <a name="configuring-the-staging-area"></a>Het faserings gebied configureren

De Azure Backup-agent die op de DPM-server wordt uitgevoerd, heeft tijdelijke opslag nodig voor gegevens die vanuit de cloud worden hersteld (lokaal faserings gebied). Configureer het faserings gebied met de cmdlet [set-DPMCloudSubscriptionSetting](/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting) en de ```-StagingAreaPath``` para meter.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

In het bovenstaande voor beeld wordt het faserings gebied ingesteld op *C:\StagingArea* in het Power shell-object ```$setting``` . Zorg ervoor dat de opgegeven map al bestaat, anders mislukt de laatste door voering van de abonnements instellingen.

### <a name="encryption-settings"></a>Versleutelingsinstellingen

De back-upgegevens die naar Azure Backup worden verzonden, worden versleuteld om de vertrouwelijkheid van de gegevens te beveiligen. De wachtwoordzin voor versleuteling is het wacht woord voor het ontsleutelen van de gegevens op het moment van herstel. Het is belang rijk om deze informatie veilig en veilig te stellen zodra deze is ingesteld.

In het onderstaande voor beeld wordt met de eerste opdracht de teken reeks geconverteerd ```passphrase123456789``` naar een beveiligde teken reeks en wordt de beveiligde teken reeks toegewezen aan de variabele met de naam ```$Passphrase``` . met de tweede opdracht wordt de beveiligde teken reeks ingesteld ```$Passphrase``` als het wacht woord voor het versleutelen van back-ups.

```powershell
$Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [!IMPORTANT]
> Behoud de wachtwoordzingegevens veilig en veilig zodra deze is ingesteld. U kunt geen gegevens herstellen vanuit Azure zonder deze wachtwoordzin.
>
>

Op dit moment moet u alle vereiste wijzigingen voor het object hebben aangebracht ```$setting``` . Vergeet niet de wijzigingen door te voeren.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>Gegevens beschermen tot Azure Backup

In deze sectie voegt u een productie server toe aan DPM en beschermt u de gegevens vervolgens naar de lokale DPM-opslag en vervolgens op Azure Backup. In de voor beelden wordt gedemonstreerd hoe u een back-up van bestanden en mappen maakt. De logica kan eenvoudig worden uitgebreid om een back-up te maken van een gegevens bron die door DPM wordt ondersteund. Alle DPM-back-ups zijn onderhevig aan een beveiligings groep (PG) met vier onderdelen:

1. **Groeps leden** is een lijst van alle Beveilig bare objecten (ook wel *gegevens bronnen* genoemd in DPM) die u wilt beveiligen in dezelfde beveiligings groep. U kunt bijvoorbeeld productie-Vm's in één beveiligings groep beveiligen en SQL Server data bases in een andere beveiligings groep, aangezien deze mogelijk verschillende back-upvereisten hebben. Voordat u een back-up van een gegevens bron op een productie server kunt maken, moet u ervoor zorgen dat de DPM-agent op de server is geïnstalleerd en wordt beheerd door DPM. Volg de stappen voor [het installeren van de DPM-agent](/system-center/dpm/deploy-dpm-protection-agent) en het koppelen hiervan aan de juiste DPM-server.
2. Met de **methode voor gegevens beveiliging** worden de doel locaties voor back-ups opgegeven: tape, schijf en Cloud. In ons voor beeld beveiligen we gegevens op de lokale schijf en in de Cloud.
3. Een **back-upschema** dat opgeeft wanneer er back-ups moeten worden gemaakt en hoe vaak de gegevens moeten worden gesynchroniseerd tussen de DPM-server en de productie server.
4. Een **Bewaar schema** dat aangeeft hoe lang de herstel punten in azure moeten worden bewaard.

### <a name="creating-a-protection-group"></a>Een beveiligings groep maken

Maak eerst een nieuwe beveiligings groep met behulp van de cmdlet [New-DPMProtectionGroup](/powershell/module/dataprotectionmanager/new-dpmprotectiongroup) .

```powershell
$PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

Met de bovenstaande cmdlet wordt een beveiligings groep gemaakt met de naam *ProtectGroup01*. Een bestaande beveiligings groep kan ook later worden gewijzigd om een back-up toe te voegen aan de Azure-Cloud. Als u echter wijzigingen wilt aanbrengen in de beveiligings groep: nieuw of bestaand, moeten we een ingang voor een *gewijzigd* object ophalen met behulp van de cmdlet [Get-DPMModifiableProtectionGroup](/powershell/module/dataprotectionmanager/get-dpmmodifiableprotectiongroup) .

```powershell
$MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>Groeps leden toevoegen aan de beveiligings groep

Elke DPM-agent kent de lijst met gegevens bronnen op de server waarop deze is geïnstalleerd. Als u een gegevens bron wilt toevoegen aan de beveiligings groep, moet de DPM-agent eerst een lijst met de gegevens bronnen naar de DPM-server verzenden. Een of meer gegevens bronnen worden vervolgens geselecteerd en toegevoegd aan de beveiligings groep. De Power shell-stappen die nodig zijn om dit te krijgen zijn:

1. Haal een lijst op met alle servers die door DPM worden beheerd via de DPM-agent.
2. Kies een specifieke server.
3. Een lijst met alle gegevens bronnen op de server ophalen.
4. Een of meer gegevens bronnen kiezen en toevoegen aan de beveiligings groep

De lijst met servers waarop de DPM-agent is geïnstalleerd en die wordt beheerd door de DPM-server is verkregen met de cmdlet [Get-DPMProductionServer](/powershell/module/dataprotectionmanager/get-dpmproductionserver) . In dit voor beeld wordt Power shell met de naam *productionserver01* voor het maken van een back-up gefilterd en geconfigureerd.

```powershell
$server = Get-ProductionServer -DPMServerName "TestingServer" | Where-Object {($_.servername) –contains "productionserver01"}
```

Haal nu de lijst met gegevens bronnen op ```$server``` met behulp van de cmdlet [Get-DPMDatasource](/powershell/module/dataprotectionmanager/get-dpmdatasource) . In dit voor beeld filteren we op het volume `D:\` dat u wilt configureren voor back-up. Deze gegevens bron wordt vervolgens toegevoegd aan de beveiligings groep met behulp van de cmdlet [add-DPMChildDatasource](/powershell/module/dataprotectionmanager/add-dpmchilddatasource) . Gebruik het *aanpas* bare beveiligings groeps object ```$MPG``` om de toevoegingen te maken.

```powershell
$DS = Get-Datasource -ProductionServer $server -Inquire | Where-Object { $_.Name -contains "D:\" }

Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

Herhaal deze stap zo vaak als nodig is, totdat u alle geselecteerde gegevens bronnen aan de beveiligings groep hebt toegevoegd. U kunt ook beginnen met slechts één gegevens bron en de werk stroom voor het maken van de beveiligings groep volt ooien, en op een later tijdstip meer gegevens bronnen toevoegen aan de beveiligings groep.

### <a name="selecting-the-data-protection-method"></a>De methode voor gegevens beveiliging selecteren

Zodra de gegevens bronnen zijn toegevoegd aan de beveiligings groep, is de volgende stap het opgeven van de beveiligings methode met behulp van de cmdlet [set-DPMProtectionType](/powershell/module/dataprotectionmanager/set-dpmprotectiontype) . In dit voor beeld is de beveiligings groep ingesteld voor een lokale schijf en een back-up van de Cloud. U moet ook de gegevens bron opgeven die u wilt beveiligen met de Cloud met de vlag [add-DPMChildDatasource](/powershell/module/dataprotectionmanager/add-dpmchilddatasource) met-online.

```powershell
Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>De Bewaar termijn instellen

Stel de Bewaar periode voor de back-uppunten in met behulp van de cmdlet [set-DPMPolicyObjective](/powershell/module/dataprotectionmanager/set-dpmpolicyobjective) . Hoewel het afwijkend lijkt om de Bewaar periode in te stellen voordat het back-upschema is gedefinieerd, stelt het gebruik van de ```Set-DPMPolicyObjective``` cmdlet automatisch een standaard back-upschema in dat vervolgens kan worden gewijzigd. Het is altijd mogelijk om eerst het back-upschema en het Bewaar beleid in te stellen.

In het onderstaande voor beeld stelt de cmdlet de Bewaar parameters in voor schijf back-ups. Hierdoor blijven back-ups 10 dagen behouden en worden gegevens elke 6 uur gesynchroniseerd tussen de productie server en de DPM-server. ```SynchronizationFrequencyMinutes```Er wordt niet gedefinieerd hoe vaak een back-uppunt wordt gemaakt, maar hoe vaak gegevens worden gekopieerd naar de DPM-server.  Met deze instelling voor komt u dat back-ups te groot worden.

```powershell
Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

Voor back-ups naar Azure (DPM verwijst ernaar als online back-ups) de Bewaar termijn kan worden geconfigureerd voor [lange termijn retentie met behulp van een groot vader-vader-zoon-Scheme (GFS)](backup-azure-backup-cloud-as-tape.md). Dat wil zeggen dat u een gecombineerd Bewaar beleid kunt definiëren waarbij dagelijks, wekelijks, maandelijks en jaarlijks Bewaar beleid wordt betrokken. In dit voor beeld maken we een matrix die het complexe Bewaar schema vertegenwoordigt dat we willen en vervolgens de Bewaar termijn configureren met de cmdlet [set-DPMPolicyObjective](/powershell/module/dataprotectionmanager/set-dpmpolicyobjective) .

```powershell
$RRlist = @()
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>Het back-upschema instellen

DPM stelt automatisch een standaard back-upschema in als u de beveiligings doelstelling opgeeft met behulp van de ```Set-DPMPolicyObjective``` cmdlet. Als u de standaard schema's wilt wijzigen, gebruikt u de cmdlet [Get-DPMPolicySchedule](/powershell/module/dataprotectionmanager/get-dpmpolicyschedule) , gevolgd door de cmdlet [set-DPMPolicySchedule](/powershell/module/dataprotectionmanager/set-dpmpolicyschedule) .

```powershell
$onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

In het bovenstaande voor beeld ```$onlineSch``` is een matrix met vier elementen die het bestaande schema voor online beveiliging voor de beveiligings groep in het gfs-schema bevat:

1. ```$onlineSch[0]``` bevat het dagelijkse schema
2. ```$onlineSch[1]``` bevat de wekelijkse planning
3. ```$onlineSch[2]``` bevat de maandelijkse planning
4. ```$onlineSch[3]``` bevat het jaarlijkse schema

Als u de wekelijkse planning wilt wijzigen, moet u dus verwijzen naar ```$onlineSch[1]``` .

### <a name="initial-backup"></a>Eerste back-up

Als er voor de eerste keer een back-up van een gegevens bron wordt gemaakt, moet DPM een initiële replica maken waarmee een volledige kopie van de gegevens bron wordt gemaakt die op het DPM-replica volume moet worden beveiligd. Deze activiteit kan worden gepland voor een specifiek tijdstip of kan hand matig worden geactiveerd met de cmdlet [set-DPMReplicaCreationMethod](/powershell/module/dataprotectionmanager/set-dpmreplicacreationmethod) met de para meter ```-NOW``` .

```powershell
Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```

### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>De grootte van het herstel punt volume van de DPM-replica & wijzigen

U kunt ook de grootte van het DPM-replica volume en het schaduw kopie volume wijzigen met de cmdlet [set-DPMDatasourceDiskAllocation](/powershell/module/dataprotectionmanager/set-dpmdatasourcediskallocation) , zoals in het volgende voor beeld: Get-DatasourceDiskAllocation-data source $DS Set-DatasourceDiskAllocation-data source $DS-ProtectionGroup $MPG-Manual-ReplicaArea (2 GB)-ShadowCopyArea (2GB)

### <a name="committing-the-changes-to-the-protection-group"></a>De wijzigingen door voeren in de beveiligings groep

Ten slotte moeten de wijzigingen worden doorgevoerd voordat DPM de back-up kan maken op basis van de nieuwe configuratie van de beveiligings groep. Dit kan worden bereikt met de cmdlet [set-DPMProtectionGroup](/powershell/module/dataprotectionmanager/set-dpmprotectiongroup) .

```powershell
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

## <a name="view-the-backup-points"></a>De back-uppunten weer geven

U kunt de cmdlet [Get-DPMRecoveryPoint](/powershell/module/dataprotectionmanager/get-dpmrecoverypoint) gebruiken om een lijst met alle herstel punten voor een gegevens bron op te halen. In dit voor beeld zullen we het volgende doen:

* alle PGs op de DPM-server ophalen en opslaan in een matrix ```$PG```
* Haal de gegevens bronnen op die overeenkomen met de ```$PG[0]```
* alle herstel punten voor een gegevens bron ophalen.

```powershell
$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>Gegevens herstellen die zijn beveiligd in azure

Het herstellen van gegevens is een combi natie van een ```RecoverableItem``` object en een ```RecoveryOption``` object. In de vorige sectie hebben we een lijst met de back-uppunten voor een gegevens bron ontvangen.

In het onderstaande voor beeld laten we zien hoe u een Hyper-V virtuele machine van Azure Backup herstelt door back-uppunten te combi neren met het doel voor herstel. Dit voor beeld bevat het volgende:

* Een herstel optie maken met de cmdlet  [New-DPMRecoveryOption](/powershell/module/dataprotectionmanager/new-dpmrecoveryoption) .
* De matrix met back-uppunten ophalen met behulp van de ```Get-DPMRecoveryPoint``` cmdlet.
* Een back-uppunt kiezen om te herstellen van.

```powershell
$RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation "C:\VMRecovery"

$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

De opdrachten kunnen gemakkelijk worden uitgebreid voor elk gegevens bron type.

## <a name="next-steps"></a>Volgende stappen

* Zie [Introduction to DPM backup](backup-azure-dpm-introduction.md) (Engelstalig) voor meer informatie over dpm naar Azure backup
