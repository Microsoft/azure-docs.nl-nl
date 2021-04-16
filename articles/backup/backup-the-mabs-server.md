---
title: Een back-up maken van de MABS-server
description: Meer informatie over het maken van een back Microsoft Azure Backup server (MABS).
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: fbd3d1f2d7cb24c21962dbe5c88acebf73652a04
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519116"
---
# <a name="back-up-the-mabs-server"></a>Een back-up maken van de MABS-server

Om ervoor te zorgen dat gegevens kunnen worden hersteld als Microsoft Azure Backup Server (MABS) uitvalt, hebt u een strategie nodig voor het maken van een back-up van de MABS-server. Als er geen back-up van wordt gemaakt, moet u deze handmatig opnieuw opbouwen na een storing en kunnen herstelpunten op basis van schijven niet worden hersteld. U kunt een back-up maken van MABS-servers door een back-up te maken van de MABS-database.

## <a name="back-up-the-mabs-database"></a>Een back-up maken van de MABS-database

Als onderdeel van uw MABS-back-upstrategie moet u een back-up maken van de MABS-database. De MABS-database heet DPMDB. Deze database bevat de MABS-configuratie samen met gegevens over back-ups van MABS. Als er sprake is van een noodlott, kunt u de meeste functionaliteit van een MABS-server opnieuw opbouwen met behulp van een recente back-up van de database. Ervan uitgaande dat u de database kunt herstellen, zijn back-ups op basis van tape toegankelijk en blijven alle beveiligingsgroepsinstellingen en back-upschema's behouden. Als de schijven van de MABS-opslaggroep niet worden beïnvloed door de storing, kunnen back-ups op schijfbasis ook worden gebruikt na het opnieuw bouwen. U kunt verschillende methoden gebruiken om een back-up van de database te maken.

|Methode databaseback-up|Voordelen|Nadelen|
|--------------------------|--------------|-----------------|
|[Back-up naar Azure](#back-up-to-azure)|<li>Eenvoudig geconfigureerd en bewaakt in MABS.<li>Meerdere locaties van de back-updatabasebestanden.<li>Cloudopslag biedt een robuuste oplossing voor noodherstel.<li>Zeer veilige opslag voor de database.<li>Ondersteunt 120 online herstelpunten.|<li>Hiervoor zijn een Azure-account en aanvullende MABS-configuratie vereist. Brengt enige kosten met zich mee voor Azure-opslag.<li> Vereist een ondersteunde versie van het Windows Server-systeem met de Azure-agent om toegang te krijgen tot MABS-back-ups die zijn opgeslagen in de Azure Backup-kluis. Dit kan geen andere MABS-server zijn.<li>Geen optie als de database lokaal wordt gehost en u secundaire beveiliging wilt inschakelen. <li>Er komt extra voorbereidings- en hersteltijd bij kijken.|
|[Een back-up maken van de database door een back-up te maken van de MABS-opslaggroep](#back-up-the-database-by-backing-up-the-mabs-storage-pool)|<li>Eenvoudig te configureren en te controleren.<li>De back-up wordt bewaard op de MABS-opslaggroepschijven en is eenvoudig lokaal toegankelijk.<li>Geplande MABS-back-ups ondersteunen 512 express volledige back-ups. Als u elk uur een back-up hebt, hebt u 21 dagen volledige beveiliging.|<li>Geen goede optie voor noodherstel. Het is online en herstel werkt mogelijk niet zoals verwacht als de MABS-server of opslaggroepschijf uitvalt.<li>Geen optie als de database lokaal wordt gehost en u secundaire beveiliging wilt inschakelen. <li>Enige voorbereiding en speciale stappen zijn vereist om toegang te krijgen tot de herstelpunten als de MABS-service of -console niet wordt uitgevoerd of werkt.|
|[Back-up maken met systeemeigen SQL Server-back-up op een lokale schijf](#back-up-with-native-sql-server-backup-to-a-local-disk)|<li>In SQL Server ingebouwd.<li>De back-up wordt bewaard op een lokale schijf die eenvoudig toegankelijk is.<li>U kunt instellen dat deze zo vaak als u wilt wordt uitgevoerd.<li>Volledig onafhankelijk van MABS.<li>U kunt een back-upbestandsopruiming planning.|<li>Geen een goede optie voor noodherstel, tenzij de back-ups naar een externe locatie worden gekopieerd.<li>Vereist lokale opslag voor back-ups, waardoor de retentie en frequentie kunnen worden beperkt.|
|[Back-up maken met systeemeigen SQL-back-up en MABS-beveiliging naar een share die wordt beveiligd door MABS](#back-up-to-a-share-protected-by-mabs)|<li>Eenvoudig bewaakt in MABS.<li>Meerdere locaties van de back-updatabasebestanden.<li>Eenvoudig toegankelijk vanaf alle Windows-computers in het netwerk.<li>Mogelijk de snelste herstelmethode.|<li>Ondersteunt alleen 64 herstelpunten.<li>Geen goede optie voor noodherstel van site. Een fout in de MABS-server of MABS-opslaggroepschijf kan de herstelinspanningen belemmeren.<li>Geen optie als de MABS-database lokaal wordt gehost en u secundaire beveiliging wilt inschakelen. <li>Extra voorbereiding is nodig om deze te configureren en testen.<li>Er is extra voorbereidings- en hersteltijd nodig als de MABS-server zelf niet beschikbaar is, maar de SCHIJVEN van de MABS-opslaggroep goed zijn.|

- Als u een back-up maakt met behulp van een MABS-beveiligingsgroep, raden we u aan een unieke beveiligingsgroep voor de database te gebruiken.

    > [!NOTE]
    > Voor hersteldoeleinden moet de MABS-installatie die u wilt herstellen met de MABS-database overeenkomen met de versie van de MABS-database zelf.  Als de database die u wilt herstellen bijvoorbeeld afkomstig is van een MABS V3 met de installatie van update rollup 1, moet op de MABS-server dezelfde versie met Update rollup 1 worden uitgevoerd. Dit betekent dat u MABS mogelijk moet verwijderen en opnieuw moet installeren met een compatibele versie voordat u de database herstelt.  Als u de databaseversie wilt controleren, moet u deze mogelijk handmatig koppelen aan een tijdelijke databasenaam en vervolgens een SQL-query voor de database uitvoeren om op basis van de primaire en secundaire versies te controleren welk updatepakket het meest recent is geïnstalleerd.

- Volg deze stappen om de MABS-databaseversie te controleren:

    1. Als u de query wilt uitvoeren, opent u SQL Management Studio en maakt u vervolgens verbinding met het SQL-exemplaar waarin de MABS-database wordt uitgevoerd.

    2. Selecteer de MABS-database en start een nieuwe query.

    3. Plak de volgende SQL-query in het querydeelvenster en voer de query uit:

        `Select distinct MajorVersionNumber,MinorVersionNumber ,BuildNumber, FileName FROM dbo.tbl\_AM\_AgentPatch order byMajorVersionNumber,MinorVersionNumber,BuildNumber`

    Als er niets wordt geretourneerd in de queryresultaten, of als de MABS-server is bijgewerkt vanuit eerdere versies, maar sindsdien geen nieuw update-rollup is geïnstalleerd, is er geen vermelding voor de hoofdserver, secundair voor een basisinstallatie van MABS. Zie Lijst met buildnummers voor [MABS](https://social.technet.microsoft.com/wiki/contents/articles/36381.microsoft-azure-backup-server-list-of-build-numbers.aspx)om de MABS-versies te controleren die zijn gekoppeld aan update-rollups.

### <a name="back-up-to-azure"></a>Back-up naar Azure

1. Voordat u begint, moet u een script uitvoeren om het pad van het MABS-replicavolume op te halen, zodat u weet welk herstelpunt de MABS-back-up bevat. Doe dit na de initiële replicatie met Azure Backup. Vervang in het script door de naam van het SQL Server die als host voor de `dplsqlservername%` MABS-database wordt gebruikt.

    ```SQL
    Select ag.NetbiosName as ServerName,ds.DataSourceName,vol.MountPointPath
    from tbl_IM_DataSource as ds
    join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
    join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
    join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
    and vol.Usage =1
    and lr.Validity in (1,2)
    where ds.datasourcename like '%dpmdb%'
    and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB
    ```

    Zorg ervoor dat u de wachtwoordcode hebt die is opgegeven tijdens de installatie van de Azure Recovery Services-agent en dat de MABS-server is geregistreerd in de Azure Backup kluis. U hebt deze wachtwoordcode nodig om de back-up te herstellen.

2. Maak een Azure Backup-kluis en download het Azure Backup Agent-installatiebestand en de kluisreferenties. Voer het installatiebestand uit om de agent op de MABS-server te installeren en gebruik de kluisreferenties om de MABS-server in de kluis te registreren. [Meer informatie](backup-azure-microsoft-azure-backup.md).

3. Nadat de kluis is geconfigureerd, stelt u een MABS-beveiligingsgroep in die de MABS-database bevat. Selecteer om er een back-up van te maken op schijf en naar Azure.

#### <a name="recover-the-mabs-database-from-azure"></a>De MABS-database herstellen vanuit Azure

U kunt de database als volgt herstellen vanuit Azure met behulp van een MABS-server die is geregistreerd in de Azure Backup kluis:

1. Selecteer in de MABS-console **De optie Externe**  >  **MABS-herstel toevoegen.**

2. Geef de kluisreferenties op (download vanuit de Azure Backup kluis). Houd er rekening mee dat de referenties slechts twee dagen geldig zijn.

3. Selecteer **in Externe MABS** voor herstel selecteren de MABS-server waarvoor u de database wilt herstellen, typ de wachtwoordzin voor versleuteling en selecteer **OK.**

4. Selecteer het herstelpunt dat u wilt gebruiken in de lijst met beschikbare punten. Selecteer **Externe MABS clear om terug** te keren naar de lokale MABS-weergave.

## <a name="back-up-the-mabs-database-to-mabs-storage-pool"></a>Een back-up maken van de MABS-database naar de MABS-opslaggroep

> [!NOTE]  
> Deze optie is van toepassing op MABS met Modern Backup Storage.

1. Selecteer in de MABS-console **Beveiliging**  >  **Beveiligingsgroep maken.**
2. Op de pagina **Type beveiligingsgroep selecteren** selecteert u **Servers**.
3. Selecteer op **de pagina Groepsleden** selecteren de **optie DPM-database.** Vouw de MABS-server uit en selecteer DPMDB.
4. Selecteer op **de pagina Methode voor gegevensbeveiliging** selecteren de optie Ik wil **kortetermijnbeveiliging met schijf**. Geef de opties voor kortetermijnbeveiliging op.
5. Voer na de initiële replicatie van de MABS-database het volgende SQL-script uit:

```SQL
select AG.NetbiosName, DS.DatasourceName, V.AccessPath, LR.PhysicalReplicaId from tbl_IM_DataSource DS
join tbl_PRM_LogicalReplica as LR
on DS.DataSourceId = LR.DataSourceId
join tbl_AM_Server as AG
on DS.ServerId=AG.ServerId
join tbl_PRM_ReplicaVolume RV
on RV.ReplicaId = LR.PhysicalReplicaId
join tbl_STM_Volume V
on RV.StorageId = V.StorageId
where datasourcename like N'%dpmdb%' and ds.ProtectedGroupId is not null
and LR.Validity in (1,2)
and AG.ServerName like N'%<dpmsqlservername>%' -- <dpmsqlservername> is a placeholder, put netbios name of server hosting DPMDB
```

### <a name="recover-mabs-database"></a>MABS-database herstellen

Als u uw MABS wilt reconstrueren met dezelfde database, moet u eerst de MABS-database herstellen en deze synchroniseren met de zojuist geïnstalleerde MABS.

#### <a name="use-the-following-steps"></a>Gebruik de volgende stappen

1. Open een beheeropdrachtprompt en voer uit `psexec.exe -s powershell.exe` om een PowerShell-venster in de systeemcontext te starten.
2. Bepaal de locatie van waar u de database wilt herstellen:

#### <a name="to-copy-the-database-from-the-last-backup"></a>De database kopiëren vanaf de laatste back-up

1. Navigeer naar het replica-VHD-pad `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PhysicalReplicaId\>`
2. De **disk0.vhdx aanwezig** in deze met behulp van de `mount-vhd disk0.vhdx` opdracht.
3. Zodra de replica-VHD is bevestigd, gebruikt u om een stationletter toe te wijzen aan het replicavolume met behulp van de fysieke `mountvol.exe` replica-id uit de SQL-scriptuitvoer. Bijvoorbeeld: `mountvol X: \?\Volume{}\`

#### <a name="to-copy-the-database-from-a-previous-recovery-point"></a>De database van een eerder herstelpunt kopiëren

1. Navigeer naar de DPMDB-containermap  `\<MABSServer FQDN\>\<PhysicalReplicaId\>` . U ziet meerdere directories met enkele unieke GUID-id's onder de bijbehorende herstelpunten die voor de MABS DB zijn genomen. Andere directories vertegenwoordigen een PIT/herstelpunt.
2. Navigeer naar een pit vhd-pad, bijvoorbeeld en `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PITId\>` de **disk0.vhdx** aanwezig in het met behulp van de `mount-vhd disk0.vhdx` opdracht.
3. Zodra de replica-VHD is bevestigd, gebruikt u om een stationletter toe te wijzen aan het replicavolume, met behulp van de fysieke replica-id uit de uitvoer van `mountvol.exe` het SQL-script. Bijvoorbeeld: `mountvol X: \?\Volume{}\`

   Alle termen met angular braces in de bovenstaande stappen zijn plaatshouders. Vervang ze als volgt door de juiste waarden:
   - **ReFSVolume** : toegangspad vanuit de uitvoer van het SQL-script
   - **MABSServer FQDN** - Volledig gekwalificeerde naam van de MABS-server
   - **PhysicalReplicaId:** fysieke replica-id van het SQL-script uit
   - **PITId:** andere GUID-id dan de id van de fysieke replica in de containermap.
4. Open een andere beheeropdrachtprompt en voer uit `psexec.exe -s cmd.exe` om een opdrachtprompt in de systeemcontext te starten.
5. Wijzig de map in het station X: en navigeer naar de locatie van de MABS-databasebestanden.
6. Kopieer deze naar een locatie waarvandaan u gemakkelijk kunt herstellen. Sluit het venster psexec cmd nadat u hebt gekopieerd.
7. Ga naar het psexec PowerShell-venster dat in stap 1 is geopend, navigeer naar het VHDX-pad en ontkoppel de VHDX met behulp van de opdracht `dismount-vhd disk0.vhdx` .
8. Nadat u de MABS-server opnieuw hebt geïnstalleerd, kunt u de herstelde DPMDB gebruiken om verbinding te maken met de MABS-server door de opdracht uit te `DPMSYNC-RESTOREDB` voeren.
9. Voer `DPMSYNC-SYNC` uit zodra is `DPMSYNC-RESTOREDB` voltooid.

### <a name="back-up-the-database-by-backing-up-the-mabs-storage-pool"></a>Een back-up maken van de database door een back-up te maken van de MABS-opslaggroep

> [!NOTE]
> Deze optie is van toepassing op MABS met verouderde opslag.

Voordat u begint, moet u een script uitvoeren om het pad van het MABS-replicavolume op te halen, zodat u weet welk herstelpunt de MABS-back-up bevat. Doe dit na de initiële replicatie met Azure Backup. Vervang in het script door de naam van het SQL Server die als host voor de `dplsqlservername%` MABS-database wordt gebruikt.

```SQL
Select ag.NetbiosName as ServerName,ds.DataSourceName,vol.MountPointPath
from tbl_IM_DataSource as ds
join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
and vol.Usage =1
and lr.Validity in (1,2)
where ds.datasourcename like '%dpmdb%'
and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB
```

1. Selecteer in de MABS-console **Beveiliging**  >  **Beveiligingsgroep maken.**

2. Selecteer op **de pagina Type beveiligingsgroep** selecteren de optie **Servers.**

3. Selecteer op **de pagina Groepsleden** selecteren de MABS-database. Vouw het MABS-serveritem uit en selecteer **DPMDB.**

4. Selecteer op  **de pagina Methode voor gegevensbeveiliging** selecteren de optie Ik wil **kortetermijnbeveiliging met schijf**. Geef de opties voor kortetermijnbeveiliging op. We raden een bewaartermijn van twee weken aan voor MABS-databases.

#### <a name="recover-the-database"></a>De database herstellen

Als de MABS-server nog steeds operationeel is en de opslaggroep intact is (zoals problemen met de MABS-service of -console), kopieert u de database als volgt van het replicavolume of een schaduwkopie:

1. Selecteer het tijdstip vanaf waar u de database wilt herstellen.

    - Als u de database wilt kopiëren vanaf de laatste back-up die rechtstreeks van het MABS-replicavolume is gemaakt, gebruikt **umountvol.exe** om een stationletter toe te wijzen aan het replicavolume met behulp van de GUID uit de SQL-scriptuitvoer. Bijvoorbeeld: `C:\Mountvol X: \\?\Volume{d7a4fd76\-a0a8\-11e2\-8fd3\-001c23cb7375}\`

    - Als u de database van een eerder herstelpunt (schaduwkopie) wilt kopiëren, moet u alle schaduwkopieen voor de replica met behulp van de volume-GUID van de SQL-scriptuitvoer in een lijst zetten. Met deze opdracht geeft u een lijst van schaduwkopieen voor dat volume: `C:\>Vssadmin list shadows /for\=\\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` . Noteer de aanmaaktijd en de schaduwkopie-id waar u vanaf wilt herstellen.

2. Gebruik vervolgens **diskshadow.exe** schaduwkopie aan een ongebruikte stationletter X: met behulp van de schaduwkopie-id, zodat u de databasebestanden kunt kopiëren.

3. Open een beheeropdrachtprompt en voer uit om een opdrachtprompt in de systeemcontext te starten, zodat u bent machtigingen hebt om naar het replicavolume te navigeren `psexec.exe -s cmd.exe` (X:) en kopieer de bestanden.

4. Cd naar het station X: en navigeer naar de locatie van de MABS-databasebestanden. Kopieer deze naar een locatie waarvandaan u gemakkelijk kunt herstellen. Nadat het kopiëren is voltooid, bestaat het cmd-venster psexec endiskshadow.exehet X:-volume uit en unexpose. 

5. U kunt nu de databasebestanden herstellen met behulp van SQL Management Studio of door **DPMSYNC \- RESTOREDB uit te uitvoeren.**

## <a name="back-up-with-native-sql-server-backup-to-a-local-disk"></a>Back-up maken met systeemeigen SQL Server-back-up naar een lokale schijf

U kunt een back-up van de MABS-database maken op een lokale schijf met systeemeigen SQL Server back-up, onafhankelijk van MABS.

- Bekijk een [overzicht](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) van de back-up van SQL Server.

- [Meer informatie](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service) over back-ups maken van SQL Server naar de cloud.

## <a name="back-up-to-a-share-protected-by-mabs"></a>Een back-up maken van een share die wordt beveiligd door MABS

Deze back-upoptie maakt gebruik van systeemeigen SQL om een back-up te maken van de MABS-database naar een share, bebeveiligen de share met MABS en maakt gebruik van eerdere versies van Windows VSS om het herstellen te vergemakkelijken.

### <a name="before-you-start"></a>Voordat u begint

1. Maak op SQL Server schijf een map met voldoende vrije ruimte voor één kopie van een back-up. Bijvoorbeeld: `C:\MABSBACKUP`.

1. Deel de map. Deel bijvoorbeeld de map `C:\MABSBACKUP` als *DPMBACKUP*.

1. Kopieer en plak de onderstaande OSQL-opdracht in Kladblok en sla deze op in een bestand met de naam `C:\MABSACKUP\bkupdb.cmd` . Zorg ervoor dat er geen .txt-extensie is. Wijzig de SQL_Instance_name en DPMDB_NAME overeen met het exemplaar en de DPMDB-naam die door uw MABS-server worden gebruikt.

    ```SQL
    OSQL -E -S localhost\SQL_INSTANCE_NAME -Q "BACKUP DATABASE DPMDB_NAME TO DISK='C:\DPMBACKUP\dpmdb.bak' WITH FORMAT"
    ```

1. Open in Kladblok het **ScriptingConfig.xml** bestand onder de `C:\Program Files\Microsoft System Center\DPM\DPM\Scripting` map op de MABS-server.

1. Wijzig **ScriptingConfig.xml** en wijzig **DataSourceName=** in de stationletter die de map/share DPMDBBACKUP bevat. Wijzig de vermelding PreBackupScript in het volledige pad en de naam van **bkupdb.cmd** die is opgeslagen in stap 3.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ScriptConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="https://schemas.microsoft.com/2003/dls/ScriptingConfig.xsd">
    <DatasourceScriptConfig DataSourceName="C:">
    <PreBackupScript>C:\MABSDBBACKUP\bkupdb.cmd</PreBackupScript>
    <TimeOut>120</TimeOut>
    </DatasourceScriptConfig>
    </ScriptConfiguration>
    ```

1. Sla de wijzigingen op in **ScriptingConfig.xml**.

1. Bebeveiligen van de map C:\MABSBACKUP of de share met behulp van MABS en `\sqlservername\MABSBACKUP` wacht tot de eerste replica is gemaakt. Er moet een **dpmdb.bak** in de map C:\MABSBACKUP staan als gevolg van het uitvoeren van het script vóór de back-up, dat op zijn beurt is gekopieerd naar de MABS-replica.

1. Als u selfserviceherstel niet inschakelen, hebt u een aantal extra stappen nodig om de MAP MABSBACKUP op de replica te delen:

    1. Zoek in de MABS-console > **Beveiliging** de gegevensbron MABSBACKUP en selecteer deze. Selecteer in de sectie details Klik **om details weer te geven op** de koppeling naar het replicapad en kopieer het pad naar Kladblok. Verwijder het bronpad en behoud het doelpad. Het pad moet er ongeveer als volgt uitzien: `C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP` .

    2. Maak een share naar dat pad met behulp van de sharenaam **MABSSERVERNAME-DPMDB.** U kunt de opdracht Net Share hieronder uit een beheeropdrachtprompt gebruiken.

        ```cmd
        Net Share MABSSERVERNAME-dpmdb="C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

### <a name="configure-the-backup"></a>De back-up configureren

U kunt een back-up maken van de MABS-database net als elke andere SQL Server database met behulp van SQL Server systeemeigen back-up.

- Bekijk een [overzicht](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) van de back-up van SQL Server.

- [Meer informatie](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service) over back-ups maken van SQL Server naar de cloud.

### <a name="recover-the-mabs-database"></a>De MABS-database herstellen

1. Maak vanaf elke `\\MABSServer\MABSSERVERNAME-dpmdb` Windows-computer verbinding met de share via Verkenner.

2. Klik met de rechtermuisknop op **het bestand dpmdb.bak** om eigenschappen weer te geven. Op het tabblad **Vorige versies** staan alle back-ups die u kunt selecteren en kopiëren. De laatste back-up bevindt zich ook nog in de map C:\MABSBACKUP die ook eenvoudig toegankelijk is.

3. Als u een via SAN gekoppelde MABS-opslaggroepschijf naar een andere server moet verplaatsen om het replicavolume te kunnen lezen of Windows opnieuw moet installeren om lokaal gekoppelde schijven te lezen, moet u van tevoren het koppelpunt of de volume-GUID van het MABS Replica-volume kennen, zodat u weet welk volume de back-up van de database bevat. U kunt het onderstaande SQL-script gebruiken om informatie op te halen op elk gewenst moment na de initiële beveiliging, maar voordat u moet herstellen. Vervang door `%dpmsqlservername%` de naam van de SQL Server als host voor de database.

    ```sql
    Select ag.NetbiosName as
    ServerName,ds.DataSourceName,vol.MountPointPath,vol.GuidName
    from tbl_IM_DataSource as ds
    join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
    join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
    join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
    and vol.Usage =1
    and lr.Validity in (1,2)
    where ds.datasourcename like '%C:\%' -- volume drive letter for DPMBACKUP
    and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB

    ```

4. Als u moet herstellen na het verplaatsen van de MABS-opslaggroepschijven of het opnieuw opbouwen van een MABS-server:

    1. U hebt de volume-GUID, dus als dat volume moet worden bevestigd op een andere Windows-server of nadat een MABS-server opnieuw is opgebouwd, gebruikt **umountvol.exe** om er een stationletter aan toe te wijzen met behulp van de volume-GUID van de SQL-scriptuitvoer: `C:\Mountvol X: \\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` .

    2. Deel de map MABSBACKUP opnieuw op het replicavolume met behulp van de letter van het station en het gedeelte van het replicapad dat de mapstructuur vertegenwoordigt.

        ```cmd
        net share SERVERNAME-DPMDB="X:\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

    3. Maak vanaf elke `\\SERVERNAME\MABSSERVERNAME-dpmdb` Windows-computer verbinding met de share via Verkenner.

    4. Klik met de rechtermuisknop op **het bestand dpmdb.bak** om de eigenschappen weer te geven. Op het tabblad **Vorige versies** staan alle back-ups die u kunt selecteren en kopiëren.

## <a name="using-dpmsync"></a>DPMSync gebruiken

**DpmSync** is een opdrachtregelprogramma waarmee u de MABS-database kunt synchroniseren met de status van de schijven in de opslaggroep en met de geïnstalleerde beveiligingsagents. Met DpmSync wordt de MABS-database hersteld, de MABS-database gesynchroniseerd met de replica's in de opslaggroep, de rapportdatabase hersteld en ontbrekende replica's opnieuw toegewezen.

### <a name="parameters"></a>Parameters

| Parameter      | Beschrijving    |
|----------------|-----------------------------|
| **-RestoreDb**                       | Hiermee herstelt u een MABS-database vanaf een opgegeven locatie.|
| **-Sync**                            | Hiermee worden herstelde databases gesynchroniseerd. U moet **DpmSync –Sync uitvoeren nadat** u de databases hebt hersteld. Nadat u **DpmSync –Sync hebt uitgevoerd,** zijn sommige replica's mogelijk nog steeds gemarkeerd als ontbrekend. |
| **-DbLoc-locatie**                 | Identificeert de locatie van de back-up van de MABS-database.|
| **-InstanceName** <br/>*server\exemplaar*     | Exemplaar waarop DPMDB moet worden hersteld.|
| **-ReallocateReplica**         | Alle ontbrekende replicavolumes worden opnieuw toegewezen zonder synchronisatie. |
| **-DataCopied**                      | Geeft aan dat u klaar bent met het laden van gegevens in de zojuist toegewezen replicavolumes. Dit is alleen van toepassing op clientcomputers. |

**Voorbeeld 1:** Voer de volgende opdracht uit om de MABS-database te herstellen vanaf lokale back-upmedia op de MABS-server:

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak
```

Nadat u de MABS-database hebt hersteld, moet u de volgende opdracht uitvoeren om de databases te synchroniseren:

```cmd
DpmSync -Sync
```

Nadat u de MABS-database hebt hersteld en gesynchroniseerd en voordat u de replica herstelt, moet u de volgende opdracht uitvoeren om de schijfruimte voor de replica opnieuw toe te staan:

```cmd
DpmSync -ReallocateReplica
```

**Voorbeeld 2:** Als u de MABS-database wilt herstellen vanuit een externe database, moet u de volgende opdracht uitvoeren op de externe computer:

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak –InstanceName contoso\ms$dpm
```

Nadat u de MABS-database hebt hersteld, moet u de volgende opdracht uitvoeren op de MABS-server om de databases te synchroniseren:

```cmd
DpmSync -Sync
```

Nadat u de MABS-database hebt hersteld en gesynchroniseerd en voordat u de replica herstelt, moet u de volgende opdracht uitvoeren op de MABS-server om de schijfruimte voor de replica opnieuw toe te staan:

```cmd
DpmSync -ReallocateReplica
```

## <a name="next-steps"></a>Volgende stappen

- [MABS-ondersteuningsmatrix](backup-support-matrix-mabs-dpm.md)
- [Veelgestelde vragen over MABS](backup-azure-dpm-azure-server-faq.yml)