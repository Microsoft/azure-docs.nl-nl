---
title: Pakketten beheren met Azure-SSIS Integration Runtime-pakket archief
description: Meer informatie over het beheren van pakketten met Azure-SSIS Integration Runtime-pakket opslag.
ms.service: data-factory
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 09/29/2020
ms.openlocfilehash: 66fdd20e66360be7cdaa6d918e18ca09c413da07
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393833"
---
# <a name="manage-packages-with-azure-ssis-integration-runtime-package-store"></a>Pakketten beheren met Azure-SSIS Integration Runtime-pakket archief

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Als u & uw SSIS-workloads (on-premises SQL Server Integration Services) naar de Cloud wilt verplaatsen, kunt u Azure-SSIS Integration Runtime (IR) inrichten in Azure Data Factory (ADF). Zie [een Azure-SSIS IR inrichten](./tutorial-deploy-ssis-packages-azure.md)voor meer informatie. Een Azure-SSIS IR ondersteunt:

- Pakketten die zijn geïmplementeerd in SSIS Catalog (SSISDB) die worden gehost door een server of beheerd exemplaar van Azure SQL Database (projectimplementatiemodel)
- Pakketten die zijn geïmplementeerd in het bestandssysteem, Azure Files of SQL Server-database (MSDB) die worden gehost door Azure SQL Managed Instance (pakketimplementatiemodel)

Wanneer u pakket implementatie model gebruikt, kunt u kiezen of u uw Azure-SSIS IR wilt inrichten met pakket archieven. Ze bieden een pakket beheer-laag boven op bestands systeem, Azure Files of MSDB die wordt gehost door Azure SQL Managed instance. Met Azure-SSIS IR-pakket archief kunt u pakketten importeren/exporteren/verwijderen en uitvoeren en de uitvoering van pakketten via SQL Server Management Studio (SSMS) controleren en stoppen, vergelijkbaar met de [verouderde SSIS-pakket opslag](/sql/integration-services/service/package-management-ssis-service). 

## <a name="connect-to-azure-ssis-ir"></a>Verbinding maken met Azure-SSIS IR

Zodra uw Azure-SSIS IR is ingericht, kunt u er verbinding mee maken om door de pakket winkels op SSMS te bladeren.

![Verbinding maken met Azure-SSIS IR](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect.png)

Selecteer in het venster **objectverkenner** van SSMS **Azure-SSIS Integration runtime** in de vervolg keuzelijst **verbinding maken** . Meld u vervolgens aan bij Azure en selecteer het relevante abonnement, ADF en Azure-SSIS IR dat u hebt ingericht met pakket archieven. Uw Azure-SSIS IR wordt weer gegeven met **actieve pakketten** en de knoop punten **opgeslagen pakketten** onder. Vouw het knoop punt **opgeslagen pakketten** uit om uw pakket winkels onder te bekijken. Breid uw pakket archieven uit om de onderliggende mappen en pakketten weer te geven. Mogelijk wordt u gevraagd om de toegangs referenties voor uw pakket archieven in te voeren, als SSMS niet automatisch verbinding kan maken. Als u bijvoorbeeld een pakket archief boven op MSDB uitbreidt, wordt u mogelijk gevraagd om eerst verbinding te maken met uw Azure SQL-beheerde exemplaar.

![Verbinding maken met Azure SQL Managed instance](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect2.png)

## <a name="manage-folders-and-packages"></a>Mappen en pakketten beheren

Nadat u verbinding hebt gemaakt met uw Azure-SSIS IR op SSMS, kunt u met de rechter muisknop op een pakket opslaan, mappen of pakketten klikken om een menu te popupen en een **nieuwe map** te selecteren, pakket **importeren**, **pakket exporteren**, **verwijderen** of **vernieuwen**.

   ![Mappen en pakketten beheren](media/azure-ssis-integration-runtime-package-store/ssms-package-store-manage.png)

   *  Selecteer **nieuwe map** om een nieuwe map voor geïmporteerde pakketten te maken.

   *  Selecteer **pakket importeren** om pakketten te importeren uit **bestands systeem**, **SQL Server** (msdb) of het verouderde **SSIS-pakket** in uw pakket opslag.

      ![Pakket importeren](media/azure-ssis-integration-runtime-package-store/ssms-package-store-import.png)

      Afhankelijk van de locatie van het **pakket** waaruit u wilt importeren, selecteert u het relevante type **Server** / **verificatie**, voert u de toegangs referenties in als dat nodig is, selecteert u het **pad naar het pakket** en voert u de nieuwe **pakket naam** in. Bij het importeren van pakketten kan het beveiligings niveau ervan niet worden gewijzigd. Gebruik SQL Server Data Tools (SSDT) of het opdracht regel programma om het te wijzigen `dtutil` .

      > [!NOTE]
      > Het importeren van SSIS-pakketten in Azure-SSIS IR pakket archieven kan slechts één voor één doen en kopieert deze eenvoudigweg naar het onderliggende MSDB/File System/Azure Files met behoud van hun SQL Server/SSIS-versie. 
      >
      > Omdat Azure-SSIS IR op dit moment is gebaseerd op **SQL Server 2017**, zal het uitvoeren van pakketten met lagere versies worden bijgewerkt in SSIS 2017-pakketten tijdens runtime. Het uitvoeren van pakketten met hogere versies wordt niet ondersteund.
      >
      > Omdat oudere SSIS-pakket archieven zijn gebonden aan een specifieke SQL Server-versie en alleen toegankelijk zijn voor SSMS voor die versie, moeten lagere versies van pakketten in verouderde SSIS-pakket archieven eerst worden geëxporteerd naar het bestands systeem met behulp van de opgegeven SSMS-versie voordat ze kunnen worden geïmporteerd in Azure-SSIS IR pakket archieven met SSMS 2019 of hoger.
      >
      > U kunt ook een [dtutil](/sql/integration-services/dtutil-utility) -opdracht regel programma gebruiken om meerdere SSIS-pakketten te importeren in azure-SSIS IR-pakket archieven terwijl u het beveiligings niveau inschakelt, Zie [meerdere pakketten implementeren met dtutil](#deploying-multiple-packages-with-dtutil).

   *  Selecteer **pakket exporteren** om pakketten uit uw pakket archief te exporteren naar **bestands systeem**, **SQL Server** (msdb) of de verouderde **SSIS-pakket opslag**.

      ![Pakket exporteren](media/azure-ssis-integration-runtime-package-store/ssms-package-store-export.png)

      Afhankelijk van de **pakket locatie** waarnaar u wilt exporteren, selecteert u het relevante type **Server** / **verificatie**, voert u indien nodig de toegangs referenties in en selecteert u het **pad naar het pakket**. Wanneer u pakketten exporteert, voert u de wacht woorden in om ze eerst te ontsleutelen en vervolgens kunt u het beveiligings niveau wijzigen, bijvoorbeeld om te voor komen dat gevoelige gegevens worden opgeslagen of om deze te versleutelen of alle gegevens met de gebruikers sleutel of het wacht woord.

      > [!NOTE]
      > Het exporteren van SSIS-pakketten uit Azure-SSIS IR-pakket archieven kan slechts één voor één worden uitgevoerd. Als u dit niet doet, worden ze gewoon gekopieerd zonder dat ze de SQL Server/SSIS-versie behouden, anders worden ze bijgewerkt naar SSIS 2019 of hoger-versie pakketten.
      >
      > Omdat Azure-SSIS IR op dit moment is gebaseerd op **SQL Server 2017**, zal het uitvoeren van pakketten met lagere versies worden bijgewerkt in SSIS 2017-pakketten tijdens runtime. Het uitvoeren van pakketten met hogere versies wordt niet ondersteund.
      >
      > Als u meerdere SSIS-pakketten uit Azure-SSIS IR pakket archieven wilt exporteren terwijl u het beveiligings niveau inschakelt, kunt u het [dtutil](/sql/integration-services/dtutil-utility) -opdracht regel programma gebruiken om [meerdere pakketten te implementeren met dtutil](#deploying-multiple-packages-with-dtutil).

   *  Selecteer **verwijderen** om bestaande mappen/pakketten uit uw pakket archief te verwijderen.

   *  Selecteer **vernieuwen** om nieuw toegevoegde mappen/pakketten weer te geven in uw pakket opslag.

## <a name="execute-packages"></a>Pakketten uitvoeren

Nadat u verbinding hebt gemaakt met uw Azure-SSIS IR op SSMS, kunt u met de rechter muisknop op een opgeslagen pakket klikken om een menu te popupen en **pakket uitvoeren** te selecteren.  Hiermee opent u het dialoog venster **Execute package Utility** , waarin u de pakket uitvoeringen op Azure-SSIS IR kunt configureren als uitvoering van SSIS-pakket activiteiten in ADF-pijp lijnen.

![Execute Package Utility pagina's 1 & 2](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute.png)

![Execute Package Utility pagina's 3 & 4](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute2.png)

De pagina's **Algemeen**, **configuraties**, **uitvoerings opties** en **logboek registratie** van **Execute package Utility** komen overeen met het tabblad  **instellingen** van de activiteit SSIS-pakket uitvoeren. Op deze pagina's kunt u het versleutelings wachtwoord voor uw pakket invoeren en toegang krijgen tot gegevens voor uw pakket configuratie bestand. U kunt ook de referenties en eigenschappen van uw pakket uitvoer opgeven, evenals de toegangs gegevens voor uw Logboekmap.  De pagina **waarden instellen** van **Execute package Utility** dialoog venster komt overeen met het tabblad **Eigenschappen onderdrukkingen** van de activiteit uitvoeren SSIS-pakket, waar u de bestaande pakket eigenschappen kunt opgeven die u wilt overschrijven. Zie [SSIS-pakketten uitvoeren als uitvoering van SSIS-pakket activiteiten in ADF-pijp lijnen](./how-to-invoke-ssis-package-ssis-activity.md)voor meer informatie.

Wanneer u de knop **uitvoeren** selecteert, wordt er automatisch een nieuwe ADF-pijp lijn met activiteit voor het uitvoeren van SSIS-pakketten gegenereerd en geactiveerd. Als er al een ADF-pijp lijn met dezelfde instellingen bestaat, wordt deze opnieuw uitgevoerd en wordt er geen nieuwe pijp lijn gegenereerd. De activiteiten ADF pijp lijn en execute SSIS-pakket krijgen `Pipeline_SSMS_YourPackageName_HashString` respectievelijk de naam en `Activity_SSMS_YourPackageName` .

![Knop Execute Package Utility](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute3.png)

![SSIS-pakket activiteit uitvoeren](media/azure-ssis-integration-runtime-package-store/ssis-activity-package-store.png)

## <a name="monitor-and-stop-running-packages"></a>Het uitvoeren van pakketten controleren en stoppen

Nadat u verbinding hebt gemaakt met uw Azure-SSIS IR op SSMS, kunt u het knoop punt **actieve pakketten** uitvouwen om uw pakketten die momenteel worden uitgevoerd, weer te geven.  Klik met de rechter muisknop op een van de opdrachten om een menu te popupen en selecteer **stoppen** of **vernieuwen**.

   ![Het uitvoeren van pakketten controleren en stoppen](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor.png)

   *  Selecteer **stoppen** om de momenteel UITGEVOERDe ADF-pijp lijn te annuleren die het pakket uitvoert als EXECUTe SSIS-pakket activiteit.

   *  Selecteer **vernieuwen** om nieuwe pakketten uit uw pakket archieven weer te geven.

## <a name="monitor-azure-ssis-ir-and-edit-package-stores"></a>Azure-SSIS IR bewaken en pakket archieven bewerken

Nadat u verbinding hebt gemaakt met uw Azure-SSIS IR op SSMS, kunt u er met de rechter muisknop op klikken om een menu weer te pop-up en **Ga naar Azure Data Factory Portal** of **vernieuwen**.

   ![Naar de ADF-Portal](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor2.png)

   *  Selecteer **Ga naar Azure Data Factory Portal** om de pagina **Integration Runtimes** van ADF monitoring hub te openen, waar u uw Azure-SSIS IR kunt bewaken. Op de tegel **pakket opslag** ziet u het aantal pakket archieven dat aan uw Azure-SSIS IR is gekoppeld.  Als u dat nummer selecteert, wordt er een venster weer gegeven waarin u gekoppelde ADF-Services kunt bewerken waarmee de toegangs gegevens voor uw pakket archieven worden opgeslagen.

      ![Pakket archieven bewerken](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor3.png)

   *  Selecteer **vernieuwen** om onlangs toegevoegde mappen/pakketten in uw pakket op te slaan en uit te voeren in uw pakket archieven.

## <a name="deploying-multiple-packages-with-dtutil"></a>Meerdere pakketten implementeren met dtutil

& als u de on-premises SSIS-werk belastingen wilt opheffen op SSIS in ADF en het verouderde pakket implementatie model wilt behouden, moet u uw pakketten implementeren vanuit bestands systeem, MSDB die worden gehost door SQL Server of verouderde SSIS-pakket archieven in Azure Files, MSDB gehost door Azure SQL Managed instance of Azure-SSIS IR package Stores. Op hetzelfde moment moet u ook het beveiligings niveau van versleuteling per gebruikers sleutel overschakelen naar niet-versleuteld of versleutelen op basis van het wacht woord als u dit nog niet hebt gedaan.

U kunt [dtutil](/sql/integration-services/dtutil-utility) -opdracht regel programma gebruiken dat wordt meegeleverd met SQL Server/SSIS-installatie om meerdere pakketten in batches te implementeren. Het is gebonden aan een specifieke SSIS-versie, dus als u deze gebruikt om pakketten van lagere versies te implementeren zonder het beveiligings niveau te scha kelen, worden deze eenvoudigweg gekopieerd tijdens het behoud van de SSIS-versie. Als u het gebruikt om ze te implementeren en tegelijkertijd het beveiligings niveau te wijzigen, worden ze bijgewerkt naar de SSIS-versie.

 Omdat Azure-SSIS IR op dit moment is gebaseerd op **SQL Server 2017**, zal het uitvoeren van pakketten met lagere versies worden bijgewerkt in SSIS 2017-pakketten tijdens runtime. Het uitvoeren van pakketten met hogere versies wordt niet ondersteund.

Als gevolg hiervan moet het implementeren van pakketten die moeten worden uitgevoerd op Azure-SSIS IR in het pakket implementatie model, gebruikmaken van dtutil 2017 die wordt geleverd met de installatie van SQL Server/SSIS 2017. U kunt de gratis [SQL Server/SSIS 2017 Developer Edition](https://go.microsoft.com/fwlink/?linkid=853016) voor dit doel downloaden en installeren. Nadat de installatie is uitgevoerd, kunt u dtutil 2017 vinden op deze map: `YourLocalDrive:\Program Files\Microsoft SQL Server\140\DTS\Binn` .

### <a name="deploying-multiple-packages-from-file-system-on-premises-into-azure-files-with-dtutil"></a>Meerdere pakketten van het bestands systeem op locatie implementeren in Azure Files met dtutil

 Als u meerdere pakketten van het bestands systeem wilt implementeren in Azure Files en tegelijkertijd het beveiligings niveau wilt wijzigen, kunt u de volgende opdrachten uitvoeren vanaf een opdracht prompt. Vervang alle teken reeksen die specifiek zijn voor uw aanvraag.
  
```dos
REM Persist the access credentials for Azure Files on your local machine
cmdkey /ADD:YourStorageAccountName.file.core.windows.net /USER:azure\YourStorageAccountName /PASS:YourStorageAccountKey

REM Connect Azure Files to a drive on your local machine
net use Z: \\YourStorageAccountName.file.core.windows.net\YourFileShare /PERSISTENT:Yes

REM Go to a local folder where you store your packages
cd YourLocalDrive:\...\YourPackageFolder

REM Run dtutil in a loop to deploy your packages from the local folder into Azure Files while switching their protection level
for %f in (*.dtsx) do dtutil.exe /FILE %f /ENCRYPT FILE;Z:\%f;2;YourEncryptionPassword
```

Als u de bovenstaande opdrachten in een batch-bestand wilt uitvoeren, vervangt u door `%f` `%%f` .

Als u meerdere pakketten van verouderde SSIS-pakket archieven op het bestands systeem in Azure Files wilt implementeren en tegelijkertijd het beveiligings niveau wilt wijzigen, kunt u dezelfde opdrachten gebruiken, maar vervangen door `YourLocalDrive:\...\YourPackageFolder` een lokale map die wordt gebruikt door verouderde SSIS-pakket winkels: `YourLocalDrive:\Program Files\Microsoft SQL Server\YourSQLServerDefaultCompatibilityLevel\DTS\Packages\YourPackageFolder` . Als uw verouderde SSIS-pakket archief bijvoorbeeld is gebonden aan SQL Server 2016, gaat u naar `YourLocalDrive:\Program Files\Microsoft SQL Server\130\DTS\Packages\YourPackageFolder` .  U kunt de waarde voor `YourSQLServerDefaultCompatibilityLevel` uit een [lijst met SQL Server standaard compatibiliteits niveaus](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#arguments)vinden.

Als u Azure-SSIS IR-pakket winkels boven op Azure Files hebt geconfigureerd, worden uw geïmplementeerde pakketten weer gegeven wanneer u verbinding maakt met uw Azure-SSIS IR op SSMS 2019 of hoger.

### <a name="deploying-multiple-packages-from-msdb-on-premises-into-msdb-in-azure-with-dtutil"></a>Implementeren van meerdere pakketten van MSDB on-premises in de MSDB in azure met dtutil

 Als u meerdere pakketten wilt implementeren van MSDB die worden gehost door SQL Server of verouderde SSIS-pakket archieven op MSDB in de MSDB die wordt gehost door Azure SQL Managed instance en het beveiligings niveau tegelijk wilt wijzigen, kunt u verbinding maken met uw SQL Server op SSMS, met de rechter muisknop op `Databases->System Databases->msdb` het knoop punt in de **OBJECTVERKENNER** van SSMS klikken om een **Nieuw query** venster te openen. Vervang alle teken reeksen die specifiek zijn voor uw case:  
  
```sql
BEGIN
  SELECT 'dtutil /SQL '+f.foldername+'\'+NAME+' /ENCRYPT SQL;'+f.foldername+'\'+NAME+';2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourSQLAuthUsername /DestPassword YourSQLAuthPassword'
  FROM msdb.dbo.sysssispackages p
  inner join msdb.dbo.sysssispackagefolders f
  ON p.folderid = f.folderid
END
```

Als u het privé/open bare eind punt van uw Azure SQL Managed instance wilt gebruiken, vervangt u door `YourSQLManagedInstanceEndpoint` `YourSQLMIName.YourDNSPrefix.database.windows.net` / `YourSQLMIName.public.YourDNSPrefix.database.windows.net,3342` respectievelijk.

Met het script worden dtutil-opdracht regels gegenereerd voor alle pakketten in MSDB die u kunt selecteren, kopiëren & plakken en uitvoeren vanaf een opdracht prompt.

![Dtutil-opdracht regels genereren](media/azure-ssis-integration-runtime-package-store/sql-server-msdb-to-sql-mi-msdb.png)

```dos
dtutil /SQL YourFolder\YourPackage1 /ENCRYPT SQL;YourFolder\YourPackage1;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
dtutil /SQL YourFolder\YourPackage2 /ENCRYPT SQL;YourFolder\YourPackage2;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
dtutil /SQL YourFolder\YourPackage3 /ENCRYPT SQL;YourFolder\YourPackage3;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
```

Als u Azure-SSIS IR-pakket winkels boven op MSDB hebt geconfigureerd, worden uw geïmplementeerde pakketten weer gegeven wanneer u verbinding maakt met uw Azure-SSIS IR op SSMS 2019 of hoger.

### <a name="deploying-multiple-packages-from-msdb-on-premises-into-azure-files-with-dtutil"></a>Implementeren van meerdere pakketten van MSDB on-premises in Azure Files met dtutil

 Als u meerdere pakketten wilt implementeren van MSDB die worden gehost door SQL Server of verouderde SSIS-pakket winkels op MSDB in Azure Files en tegelijkertijd het beveiligings niveau kunnen wijzigen, kunt u verbinding maken met uw SQL Server op SSMS, met de rechter muisknop op `Databases->System Databases->msdb` het knoop punt op het **OBJECTVERKENNER** van SSMS om een **Nieuw query** venster te openen en het volgende T-SQL-script uit te voeren. Vervang alle teken reeksen die specifiek zijn voor uw case:  
  
```sql
BEGIN
  SELECT 'dtutil /SQL '+f.foldername+'\'+NAME+' /ENCRYPT FILE;Z:\'+f.foldername+'\'+NAME+'.dtsx;2;YourEncryptionPassword' 
  FROM msdb.dbo.sysssispackages p
  inner join msdb.dbo.sysssispackagefolders f
  ON p.folderid = f.folderid
END
```

Met het script worden dtutil-opdracht regels gegenereerd voor alle pakketten in MSDB die u kunt selecteren, kopiëren & plakken en uitvoeren vanaf een opdracht prompt.

```dos
REM Persist the access credentials for Azure Files on your local machine
cmdkey /ADD:YourStorageAccountName.file.core.windows.net /USER:azure\YourStorageAccountName /PASS:YourStorageAccountKey

REM Connect Azure Files to a drive on your local machine
net use Z: \\YourStorageAccountName.file.core.windows.net\YourFileShare /PERSISTENT:Yes

REM Multiselect, copy & paste, and run the T-SQL-generated dtutil command lines to deploy your packages from MSDB on premises into Azure Files while switching their protection level
dtutil /SQL YourFolder\YourPackage1 /ENCRYPT FILE;Z:\YourFolder\YourPackage1.dtsx;2;YourEncryptionPassword
dtutil /SQL YourFolder\YourPackage2 /ENCRYPT FILE;Z:\YourFolder\YourPackage2.dtsx;2;YourEncryptionPassword
dtutil /SQL YourFolder\YourPackage3 /ENCRYPT FILE;Z:\YourFolder\YourPackage3.dtsx;2;YourEncryptionPassword
```

Als u Azure-SSIS IR-pakket winkels boven op Azure Files hebt geconfigureerd, worden uw geïmplementeerde pakketten weer gegeven wanneer u verbinding maakt met uw Azure-SSIS IR op SSMS 2019 of hoger.

## <a name="next-steps"></a>Volgende stappen

U kunt de automatisch gegenereerde ADF-pijp lijnen opnieuw uitvoeren/bewerken met het uitvoeren van SSIS-pakket activiteiten of nieuwe maken in de ADF-Portal. Zie [SSIS-pakketten uitvoeren als uitvoering van SSIS-pakket activiteiten in ADF-pijp lijnen](./how-to-invoke-ssis-package-ssis-activity.md)voor meer informatie.