---
title: Power shell gebruiken om een back-up te maken van Windows Server naar Azure
description: In dit artikel leert u hoe u Power shell gebruikt om Azure Backup in te stellen op Windows Server of een Windows-client, en back-up en herstel te beheren.
ms.topic: conceptual
ms.date: 12/2/2019
ms.openlocfilehash: 582d8123f16b2d5a543d862b8eb3e45895087e4a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90987100"
---
# <a name="deploy-and-manage-backup-to-azure-for-windows-serverwindows-client-using-powershell"></a>Met behulp van PowerShell back-ups implementeren en beheren in Azure voor een Windows-server/Windows-client

In dit artikel wordt beschreven hoe u Power shell gebruikt om Azure Backup in te stellen op Windows Server of een Windows-client, en back-up en herstel te beheren.

## <a name="install-azure-powershell"></a>Azure PowerShell installeren

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Installeer de meest recente versie van Power shell](/powershell/azure/install-az-ps)om aan de slag te gaan.

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

De volgende stappen leiden u door het maken van een Recovery Services kluis. Een Recovery Services kluis wijkt af van een back-upkluis.

1. Als u Azure Backup voor de eerste keer gebruikt, moet u de cmdlet **REGI ster-AzResourceProvider** gebruiken om de Azure Recovery service-provider te registreren bij uw abonnement.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. De Recovery Services kluis is een Azure Resource Manager resource, dus u moet deze in een resource groep plaatsen. U kunt een bestaande resource groep gebruiken of een nieuwe maken. Wanneer u een nieuwe resource groep maakt, geeft u de naam en de locatie voor de resource groep op.  

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "WestUS"
    ```

3. Gebruik de cmdlet **New-AzRecoveryServicesVault** om de nieuwe kluis te maken. Zorg ervoor dat u dezelfde locatie opgeeft als voor de kluis die voor de resource groep is gebruikt.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "WestUS"
    ```

4. Geef het type opslag redundantie op dat moet worden gebruikt. U kunt [lokaal redundante opslag (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage), [geografisch redundante opslag (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage) of [zone-redundante opslag (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage)gebruiken. In het volgende voor beeld ziet u de optie **-BackupStorageRedundancy** voor *testVault* ingesteld op **georedundant**.

   > [!TIP]
   > Voor veel Azure Backup-cmdlets is het object Recovery Services-kluis als invoer vereist. Daarom is het handiger het object Backup Recovery Services-kluis in een variabele op te slaan.
   >
   >

    ```powershell
    $Vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties -Vault $Vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>De kluizen in een abonnement weer geven

Gebruik **Get-AzRecoveryServicesVault** om de lijst met alle kluizen in het huidige abonnement weer te geven. U kunt deze opdracht gebruiken om te controleren of een nieuwe kluis is gemaakt, of om te zien welke kluizen beschikbaar zijn in het abonnement.

Voer de opdracht **Get-AzRecoveryServicesVault** uit en alle kluizen in het abonnement worden weer gegeven.

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

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="installing-the-azure-backup-agent"></a>De Azure Backup-Agent installeren

Voordat u de Azure Backup-Agent installeert, moet u het installatie programma downloaden en op de Windows-Server presen teren. U kunt de meest recente versie van het installatie programma downloaden van het [micro soft Download centrum](https://aka.ms/azurebackup_agent) of van de pagina dash board van de Recovery Services kluis. Sla het installatie programma op een gemakkelijk toegankelijke locatie op, zoals `C:\Downloads\*` .

U kunt ook Power shell gebruiken om de Downloader op te halen:

 ```powershell
 $MarsAURL = 'https://aka.ms/Azurebackup_Agent'
 $WC = New-Object System.Net.WebClient
 $WC.DownloadFile($MarsAURL,'C:\downloads\MARSAgentInstaller.EXE')
 C:\Downloads\MARSAgentInstaller.EXE /q
 ```

Als u de agent wilt installeren, voert u de volgende opdracht uit in een Power shell-console met verhoogde bevoegdheden:

```powershell
MARSAgentInstaller.exe /q
```

Hiermee installeert u de agent met alle standaard opties. De installatie duurt enkele minuten op de achtergrond. Als u de optie */nu* niet opgeeft, wordt het venster **Windows Update** geopend aan het einde van de installatie om te controleren of er updates zijn. Zodra de agent is geïnstalleerd, wordt deze weer gegeven in de lijst met geïnstalleerde Program ma's.

Ga naar **configuratie scherm**  >  **Program** ma's  >  **en onderdelen** om de lijst met geïnstalleerde Program ma's weer te geven.

![Agent geïnstalleerd](./media/backup-client-automation/installed-agent-listing.png)

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

## <a name="registering-windows-server-or-windows-client-machine-to-a-recovery-services-vault"></a>Windows Server-of Windows-client computer registreren bij een Recovery Services kluis

Nadat u de Recovery Services kluis hebt gemaakt, downloadt u de nieuwste agent en de kluis referenties en slaat u deze op op een handige locatie zoals C:\Downloads.

```powershell
$CredsPath = "C:\downloads"
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault1 -Path $CredsPath
```

### <a name="registering-using-the-ps-az-module"></a>Registreren met de PS AZ-module

> [!NOTE]
> Er is een fout met het genereren van een kluis certificaat opgelost in AZ 3.5.0 release. Gebruik AZ 3.5.0 release version of hoger om een kluis certificaat te downloaden.

In de nieuwste AZ-module van Power shell, vanwege de beperkingen van het onderliggende platform, is voor het downloaden van de kluis referenties een zelfondertekend certificaat vereist. In het volgende voor beeld ziet u hoe u een zelfondertekend certificaat kunt opgeven en de kluis referenties kunt downloaden.

```powershell
$dt = $(Get-Date).ToString("M-d-yyyy")
$cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -FriendlyName 'test-vaultcredentials' -subject "Windows Azure Tools" -KeyExportPolicy Exportable -NotAfter $(Get-Date).AddHours(48) -NotBefore $(Get-Date).AddHours(-24) -KeyProtection None -KeyUsage None -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2") -Provider "Microsoft Enhanced Cryptographic Provider v1.0"
$certficate = [convert]::ToBase64String($cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pfx))
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault -Path $CredsPath -Certificate $certficate
```

Voer op de Windows Server-of Windows-client computer de cmdlet [Start-OBRegistration](/powershell/module/msonlinebackup/start-obregistration) uit om de computer bij de kluis te registreren.
Deze en andere cmdlets die worden gebruikt voor het maken van een back-up, zijn afkomstig uit de MSONLINE-module, die door de MARS-AgentInstaller is toegevoegd als onderdeel van het installatie proces.

Het installatie programma van de agent werkt de $Env:P SModulePath-variabele niet bij. Dit betekent dat automatisch laden van de module mislukt. U kunt het volgende doen om dit op te lossen:

```powershell
$Env:PSModulePath += ';C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules'
```

U kunt de module ook als volgt hand matig laden in uw script:

```powershell
Import-Module -Name 'C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup'
```

Wanneer u de online back-upcmdlets hebt geladen, registreert u de kluis referenties:

```powershell
Start-OBRegistration -VaultCredentials $CredsFilename.FilePath -Confirm:$false
```

```Output
CertThumbprint      : 7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName : testvault
Region              : WestUS
Machine registration succeeded.
```

> [!IMPORTANT]
> Gebruik geen relatieve paden om het bestand met kluis referenties op te geven. U moet een absoluut pad opgeven als invoer voor de cmdlet.
>
>

## <a name="networking-settings"></a>Netwerk instellingen

Wanneer de verbinding van de Windows-computer met Internet via een proxy server wordt uitgevoerd, kunnen de proxy-instellingen ook aan de agent worden door gegeven. In dit voor beeld is er geen proxy server. Daarom worden er expliciet gegevens over de proxy gewist.

Het bandbreedte gebruik kan ook worden beheerd met de opties van `work hour bandwidth` en `non-work hour bandwidth` voor een bepaalde set dagen van de week.

Het instellen van de proxy-en bandbreedte gegevens wordt uitgevoerd met behulp van de cmdlet [set-OBMachineSetting](/powershell/module/msonlinebackup/set-obmachinesetting) :

```powershell
Set-OBMachineSetting -NoProxy
```

```Output
Server properties updated successfully.
```

```powershell
Set-OBMachineSetting -NoThrottle
```

```Output
Server properties updated successfully.
```

## <a name="encryption-settings"></a>Versleutelingsinstellingen

De back-upgegevens die naar Azure Backup worden verzonden, worden versleuteld om de vertrouwelijkheid van de gegevens te beveiligen. De wachtwoordzin voor versleuteling is het wacht woord voor het ontsleutelen van de gegevens op het moment van herstel.

U moet een beveiligings pincode genereren door **genereren** te selecteren onder **instellingen**  >  **Eigenschappen**  >  **beveiliging pincode** in het gedeelte **Recovery Services kluis** van de Azure Portal.

>[!NOTE]
> De beveiligings pincode kan alleen worden gegenereerd via de Azure Portal.

Gebruik deze optie als de `generatedPIN` in de volgende opdracht:

```powershell
$PassPhrase = ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force
Set-OBMachineSetting -EncryptionPassPhrase $PassPhrase -SecurityPin "<generatedPIN>"
```

```Output
Server properties updated successfully
```

> [!IMPORTANT]
> Behoud de wachtwoordzingegevens veilig en veilig zodra deze is ingesteld. U kunt geen gegevens herstellen vanuit Azure zonder deze wachtwoordzin.
>
>

## <a name="back-up-files-and-folders"></a>Back-ups maken van bestanden en mappen

Alle back-ups van Windows-servers en-clients naar Azure Backup zijn onderworpen aan een beleid. Het beleid bestaat uit drie delen:

1. Een **back-upschema** dat aangeeft wanneer back-ups moeten worden gemaakt en gesynchroniseerd met de service.
2. Een **Bewaar schema** dat aangeeft hoe lang de herstel punten in azure moeten worden bewaard.
3. Een **specificatie voor het opnemen/uitsluiten van bestanden** die bepaalt waarvan een back-up moet worden gemaakt.

In dit document wordt ervan uitgegaan dat er geen back-up is geconfigureerd. We beginnen met het maken van een nieuw back-upbeleid met de cmdlet [New-OBPolicy](/powershell/module/msonlinebackup/new-obpolicy) .

```powershell
$NewPolicy = New-OBPolicy
```

Op dit moment is het beleid leeg en zijn er andere cmdlets nodig om te definiëren welke items worden opgenomen of uitgesloten, wanneer back-ups worden uitgevoerd en waar de back-ups worden opgeslagen.

### <a name="configuring-the-backup-schedule"></a>Het back-upschema configureren

De eerste van de drie delen van een beleid is het back-upschema dat wordt gemaakt met de cmdlet [New-OBSchedule](/powershell/module/msonlinebackup/new-obschedule) . Het back-upschema definieert wanneer back-ups moeten worden gemaakt. Wanneer u een planning maakt, moet u twee invoer parameters opgeven:

* De **dagen van de week** waarop de back-up moet worden uitgevoerd. U kunt de back-uptaak uitvoeren op slechts één dag of op elke dag van de week, of een combi natie hiervan.
* **Tijden van de dag** waarop de back-up moet worden uitgevoerd. U kunt Maxi maal drie verschillende tijdstippen van de dag definiëren wanneer de back-up wordt geactiveerd.

U kunt bijvoorbeeld een back-upbeleid configureren dat op 4 P.M. elke zaterdag en zondag wordt uitgevoerd.

```powershell
$Schedule = New-OBSchedule -DaysOfWeek Saturday, Sunday -TimesOfDay 16:00
```

Het back-upschema moet worden gekoppeld aan een beleid en dit kan worden bereikt met behulp van de cmdlet [set-OBSchedule](/powershell/module/msonlinebackup/set-obschedule) .

```powershell
Set-OBSchedule -Policy $NewPolicy -Schedule $Schedule
```

```Output
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```

### <a name="configuring-a-retention-policy"></a>Een Bewaar beleid configureren

In het Bewaar beleid wordt gedefinieerd hoe lang herstel punten die zijn gemaakt op basis van back-uptaken, behouden blijven. Wanneer u een nieuw Bewaar beleid maakt met behulp van de cmdlet [New-OBRetentionPolicy](/powershell/module/msonlinebackup/new-obretentionpolicy) , kunt u het aantal dagen opgeven dat de back-upherstel punten worden bewaard met Azure backup. In het onderstaande voor beeld wordt een Bewaar beleid van zeven dagen ingesteld.

```powershell
$RetentionPolicy = New-OBRetentionPolicy -RetentionDays 7
```

Het Bewaar beleid moet worden gekoppeld aan het hoofd beleid met de cmdlet [set-OBRetentionPolicy](/powershell/module/msonlinebackup/set-obretentionpolicy):

```powershell
Set-OBRetentionPolicy -Policy $NewPolicy -RetentionPolicy $RetentionPolicy
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          :
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

### <a name="including-and-excluding-files-to-be-backed-up"></a>Bestanden opnemen en uitsluiten waarvan een back-up moet worden gemaakt

Een `OBFileSpec` object definieert de bestanden die moeten worden opgenomen en uitgesloten in een back-up. Dit is een set regels die de beveiligde bestanden en mappen op een computer afwerken. U kunt zoveel bestands insluiting-en uitsluitings regels als vereist hebben en deze koppelen aan een beleid. Wanneer u een nieuw OBFileSpec-object maakt, kunt u het volgende doen:

* De bestanden en mappen opgeven die moeten worden opgenomen
* De bestanden en mappen opgeven die moeten worden uitgesloten
* Geef recursieve back-ups van gegevens op in een map (of) of alleen een back-up moet worden gemaakt van de bestanden op het hoogste niveau in de opgegeven map.

Deze laatste wordt bereikt met behulp van de vlag-NonRecursive in de opdracht New-OBFileSpec.

In het onderstaande voor beeld maakt u een back-up van volume C: en D: en sluit u de binaire bestanden van het besturings systeem in de Windows-map en eventuele tijdelijke mappen uit. Hiervoor maken we twee bestands specificaties met de cmdlet [New-OBFileSpec](/powershell/module/msonlinebackup/new-obfilespec) -One voor insluiting en één voor uitsluiting. Zodra de bestands specificaties zijn gemaakt, worden ze gekoppeld aan het beleid met behulp van de cmdlet [add-OBFileSpec](/powershell/module/msonlinebackup/add-obfilespec) .

```powershell
$Inclusions = New-OBFileSpec -FileSpec @("C:\", "D:\")
$Exclusions = New-OBFileSpec -FileSpec @("C:\windows", "C:\temp") -Exclude
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Inclusions
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

```powershell
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Exclusions
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\windows
                  IsExclude:True
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\temp
                  IsExclude:True
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

### <a name="applying-the-policy"></a>Het beleid Toep assen

Het beleids object is nu voltooid en heeft een bijbehorend back-upschema, retentie beleid en een lijst met uitsluitingen/uitsluiting van bestanden. Dit beleid kan nu worden doorgevoerd voor het gebruik van Azure Backup. Voordat u het zojuist gemaakte beleid toepast, moet u ervoor zorgen dat er geen bestaand back-upbeleid is gekoppeld aan de server met behulp van de cmdlet [Remove-OBPolicy](/powershell/module/msonlinebackup/remove-obpolicy) . Als u het beleid verwijdert, wordt om bevestiging gevraagd. Als u de bevestiging wilt overs Laan, gebruikt u de `-Confirm:$false` vlag met de cmdlet.

```powershell
Get-OBPolicy | Remove-OBPolicy
```

```Output
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

Het door voeren van het beleids object wordt uitgevoerd met de cmdlet [set-OBPolicy](/powershell/module/msonlinebackup/set-obpolicy) . Hiermee wordt ook om bevestiging gevraagd. Als u de bevestiging wilt overs Laan, gebruikt u de `-Confirm:$false` vlag met de cmdlet.

```powershell
Set-OBPolicy -Policy $NewPolicy
```

```Output
Microsoft Azure Backup Do you want to save this backup policy ? [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s)
DsList : {DataSource
         DatasourceId:4508156004108672185
         Name:C:\
         FileSpec:FileSpec
         FileSpec:C:\
         IsExclude:False
         IsRecursive:True,

         FileSpec
         FileSpec:C:\windows
         IsExclude:True
         IsRecursive:True,

         FileSpec
         FileSpec:C:\temp
         IsExclude:True
         IsRecursive:True,

         DataSource
         DatasourceId:4508156005178868542
         Name:D:\
         FileSpec:FileSpec
         FileSpec:D:\
         IsExclude:False
         IsRecursive:True
    }
PolicyName : c2eb6568-8a06-49f4-a20e-3019ae411bac
RetentionPolicy : Retention Days : 7
              WeeklyLTRSchedule :
              Weekly schedule is not set

              MonthlyLTRSchedule :
              Monthly schedule is not set

              YearlyLTRSchedule :
              Yearly schedule is not set
State : Existing PolicyState : Valid
```

U kunt de details van het bestaande back-upbeleid weer geven met behulp van de cmdlet [Get-OBPolicy](/powershell/module/msonlinebackup/get-obpolicy) . U kunt verder inzoomen met behulp van de cmdlet [Get-OBSchedule](/powershell/module/msonlinebackup/get-obschedule) voor het back-upschema en de cmdlet [Get-OBRetentionPolicy](/powershell/module/msonlinebackup/get-obretentionpolicy) voor het Bewaar beleid

```powershell
Get-OBPolicy | Get-OBSchedule
```

```Output
SchedulePolicyName : 71944081-9950-4f7e-841d-32f0a0a1359a
ScheduleRunDays : {Saturday, Sunday}
ScheduleRunTimes : {16:00:00}
State : Existing
```

```powershell
Get-OBPolicy | Get-OBRetentionPolicy
```

```Output
RetentionDays : 7
RetentionPolicyName : ca3574ec-8331-46fd-a605-c01743a5265e
State : Existing
```

```powershell
Get-OBPolicy | Get-OBFileSpec
```

```Output
FileName : *
FilePath : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
FileSpec : D:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\
FileSpec : C:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\windows
FileSpec : C:\windows
IsExclude : True
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\temp
FileSpec : C:\temp
IsExclude : True
IsRecursive : True
```

### <a name="performing-an-on-demand-backup"></a>Een back-up op aanvraag uitvoeren

Zodra een back-upbeleid is ingesteld, worden de back-ups volgens het schema uitgevoerd. Het activeren van een back-up op aanvraag is ook mogelijk met de cmdlet [Start-OBBackup](/powershell/module/msonlinebackup/start-obbackup) :

```powershell
Get-OBPolicy | Start-OBBackup
```

```Output
Initializing
Taking snapshot of volumes...
Preparing storage...
Generating backup metadata information and preparing the metadata VHD...
Data transfer is in progress. It might take longer since it is the first backup and all data needs to be transferred...
Data transfer completed and all backed up data is in the cloud. Verifying data integrity...
Data transfer completed
In progress...
Job completed.
The backup operation completed successfully.
```

## <a name="back-up-windows-server-system-state-in-mars-agent"></a>Back-up maken van de systeem status van Windows Server in MARS-agent

In deze sectie wordt de Power shell-opdracht voor het instellen van de systeem status in de MARS-agent beschreven.

### <a name="schedule"></a>Schema

```powershell
$sched = New-OBSchedule -DaysOfWeek Sunday,Monday,Tuesday,Wednesday,Thursday,Friday,Saturday -TimesOfDay 2:00
```

### <a name="retention"></a>Bewaartermijn

```powershell
$rtn = New-OBRetentionPolicy -RetentionDays 32 -RetentionWeeklyPolicy -RetentionWeeks 13 -WeekDaysOfWeek Sunday -WeekTimesOfDay 2:00  -RetentionMonthlyPolicy -RetentionMonths 13 -MonthDaysOfMonth 1 -MonthTimesOfDay 2:00
```

### <a name="configuring-schedule-and-retention"></a>Planning en bewaar periode configureren

```powershell
New-OBPolicy | Add-OBSystemState |  Set-OBRetentionPolicy -RetentionPolicy $rtn | Set-OBSchedule -Schedule $sched | Set-OBSystemStatePolicy
 ```

### <a name="verifying-the-policy"></a>Het beleid controleren

```powershell
Get-OBSystemStatePolicy
 ```

## <a name="restore-data-from-azure-backup"></a>Gegevens herstellen vanaf Azure Backup

In deze sectie wordt u begeleid bij de stappen voor het automatiseren van herstel van gegevens uit Azure Backup. Hiervoor moet u de volgende stappen uitvoeren:

1. Het bron volume kiezen
2. Kies een back-uppunt om te herstellen
3. Een item opgeven dat moet worden hersteld
4. Het herstel proces activeren

### <a name="picking-the-source-volume"></a>Het bron volume wordt opgehaald

Als u een item van Azure Backup wilt herstellen, moet u eerst de bron van het item identificeren. Omdat we de opdrachten uitvoeren in de context van een Windows-Server of een Windows-client, wordt de computer al geïdentificeerd. De volgende stap bij het identificeren van de bron is het identificeren van het volume dat het bevat. Een lijst met volumes of bronnen waarvan een back-up van deze computer wordt gemaakt, kan worden opgehaald door de cmdlet [Get-OBRecoverableSource](/powershell/module/msonlinebackup/get-obrecoverablesource) uit te voeren. Met deze opdracht wordt een matrix geretourneerd van alle bronnen waarvan een back-up van deze server/client is gemaakt.

```powershell
$Source = Get-OBRecoverableSource
$Source
```

```Output
FriendlyName : C:\
RecoverySourceName : C:\
ServerName : myserver.microsoft.com

FriendlyName : D:\
RecoverySourceName : D:\
ServerName : myserver.microsoft.com
```

### <a name="choosing-a-backup-point-from-which-to-restore"></a>Een back-uppunt kiezen waarvan u wilt herstellen

U haalt een lijst met back-uppunten op door de cmdlet [Get-OBRecoverableItem](/powershell/module/msonlinebackup/get-obrecoverableitem) uit te voeren met de juiste para meters. In ons voor beeld kiezen we het meest recente back-uppunt voor het bron volume *C:* en gebruiken om een specifiek bestand te herstellen.

```powershell
$Rps = Get-OBRecoverableItem $Source[0]
$Rps
```

```Output

IsDir                : False
ItemNameFriendly     : C:\
ItemNameGuid         : \\?\Volume{297cbf7a-0000-0000-0000-401f00000000}\
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : C:\
PointInTime          : 10/17/2019 7:52:13 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime :

IsDir                : False
ItemNameFriendly     : C:\
ItemNameGuid         : \\?\Volume{297cbf7a-0000-0000-0000-401f00000000}\
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : C:\
PointInTime          : 10/16/2019 7:00:19 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime :
```

Het object `$Rps` is een matrix met back-uppunten. Het eerste element is het laatste punt en het n-element is het oudste punt. Om het nieuwste punt te kiezen, zullen we gebruiken `$Rps[0]` .

### <a name="specifying-an-item-to-restore"></a>Een item opgeven dat moet worden hersteld

Als u een specifiek bestand wilt herstellen, geeft u de bestands naam ten opzichte van het basis volume op. Als u bijvoorbeeld C:\Test\Cat.job wilt ophalen, voert u de volgende opdracht uit.

```powershell
$Item = New-OBRecoverableItem $Rps[0] "Test\cat.jpg" $FALSE
$Item
```

```Output
IsDir                : False
ItemNameFriendly     : C:\Test\cat.jpg
ItemNameGuid         :
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : cat.jpg
PointInTime          : 10/17/2019 7:52:13 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime : 21-Jun-14 6:43:02 AM

```

### <a name="triggering-the-restore-process"></a>Het herstel proces activeren

Om het herstel proces te activeren, moeten we eerst de herstel opties opgeven. U kunt dit doen met behulp van de cmdlet [New-OBRecoveryOption](/powershell/module/msonlinebackup/new-obrecoveryoption) . Voor dit voor beeld gaan we ervan uit dat u de bestanden wilt herstellen naar *C:\Temp*. Er wordt ook van uitgegaan dat u bestanden wilt overs laan die al bestaan in de doelmap *C:\Temp*. Als u een dergelijke herstel optie wilt maken, gebruikt u de volgende opdracht:

```powershell
$RecoveryOption = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

Activeer nu het herstel proces met behulp van de opdracht [Start-OBRecovery](/powershell/module/msonlinebackup/start-obrecovery) op de geselecteerde `$Item` van de uitvoer van de `Get-OBRecoverableItem` cmdlet:

```powershell
Start-OBRecovery -RecoverableItem $Item -RecoveryOption $RecoveryOption
```

```Output
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Job completed.
The recovery operation completed successfully.
```

## <a name="uninstalling-the-azure-backup-agent"></a>De Azure Backup-Agent verwijderen

Het verwijderen van de Azure Backup Agent kan worden uitgevoerd met behulp van de volgende opdracht:

```powershell
.\MARSAgentInstaller.exe /d /q
```

Het verwijderen van de binaire bestanden van de agent van de computer heeft enkele gevolgen om te overwegen:

* Hiermee verwijdert u het bestands filter van de computer en wordt het bijhouden van de wijzigingen gestopt.
* Alle beleids gegevens worden verwijderd van de computer, maar de beleids informatie blijft opgeslagen in de service.
* Alle back-upscheman worden verwijderd en er worden geen verdere back-ups gemaakt.

De gegevens die zijn opgeslagen in azure blijven echter behouden en blijven bewaard volgens de instellingen voor het Bewaar beleid. Oudere punten worden automatisch verouderd.

## <a name="remote-management"></a>Extern beheer

Alle beheer rondom de Azure Backup Agent, beleids regels en gegevens bronnen kunnen op afstand worden uitgevoerd via Power shell. De computer die extern wordt beheerd, moet op de juiste manier worden voor bereid.

De WinRM-service is standaard geconfigureerd om hand matig te worden gestart. Het opstart type moet worden ingesteld op *automatisch* en de service moet worden gestart. Als u wilt controleren of de WinRM-service wordt uitgevoerd, moet de waarde van de eigenschap status worden *uitgevoerd*.

```powershell
Get-Service -Name WinRM
```

```Output
Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

Power shell moet worden geconfigureerd voor externe communicatie.

```powershell
Enable-PSRemoting -Force
```

```Output
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.
```

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force
```

De machine kan nu extern worden beheerd vanaf de installatie van de agent. Het volgende script kopieert bijvoorbeeld de agent naar de externe computer en installeert deze.

```powershell
$DLoc = "\\REMOTESERVER01\c$\Windows\Temp"
$Agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
$Args = "/q"
Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $DLoc -Force

$Session = New-PSSession -ComputerName REMOTESERVER01
Invoke-Command -Session $Session -Script { param($D, $A) Start-Process -FilePath $D $A -Wait } -ArgumentList $Agent, $Args
```

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over Azure Backup voor Windows Server/client:

* [Kennismaking met Azure Backup](./backup-overview.md)
* [Back-ups maken van Windows-servers](backup-windows-with-mars-agent.md)
