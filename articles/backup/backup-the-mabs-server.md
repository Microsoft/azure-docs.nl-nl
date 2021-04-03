---
title: Een back-up maken van de MABS-server
description: Meer informatie over het maken van een back-up van de Microsoft Azure Backup-Server (MABS).
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: 81a6ee005e15b1d7ab7b11a938b8ab14143818f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92172116"
---
# <a name="back-up-the-mabs-server"></a>Een back-up maken van de MABS-server

Om ervoor te zorgen dat gegevens kunnen worden hersteld als Microsoft Azure Backup Server (MABS) mislukt, hebt u een strategie nodig voor het maken van een back-up van de MABS-server. Als er geen back-up van wordt gemaakt, moet u deze hand matig opnieuw bouwen na een storing en kunnen herstel punten op basis van schijven niet worden hersteld. U kunt back-ups maken van MABS-servers door een back-up te maken van de MABS-data base.

## <a name="back-up-the-mabs-database"></a>Back-up maken van de MABS-data base

Als onderdeel van uw MABS-back-upstrategie moet u een back-up maken van de MABS-data base. De MABS-data base heet DPMDB. Deze data base bevat de MABS-configuratie in combi natie met gegevens over back-ups van MABS. Als er een nood geval is, kunt u de meeste functionaliteit van een MABS-server opnieuw samen stellen met behulp van een recente back-up van de data base. Als u de Data Base kunt herstellen, zijn op tapes gebaseerde back-ups toegankelijk en blijven alle beveiligings groeps instellingen en back-upschemas behouden. Als de onderbreking geen invloed heeft op de schijven van de MABS-opslag groep, kunnen back-ups op basis van de schijf ook worden gebruikt na het opnieuw opbouwen. U kunt verschillende methoden gebruiken om een back-up van de database te maken.

|Methode databaseback-up|Voordelen|Nadelen|
|--------------------------|--------------|-----------------|
|[Back-up naar Azure](#back-up-to-azure)|<li>Eenvoudig geconfigureerd en gecontroleerd in MABS.<li>Meerdere locaties van de back-updatabasebestanden.<li>Cloudopslag biedt een robuuste oplossing voor noodherstel.<li>Zeer veilige opslag voor de database.<li>Ondersteunt 120 online herstelpunten.|<li>Azure-account en aanvullende MABS-configuratie zijn vereist. Brengt enige kosten met zich mee voor Azure-opslag.<li> Vereist een ondersteunde versie van het systeem op basis van Windows Server met de Azure-agent om toegang te krijgen tot MABS-back-ups die zijn opgeslagen in de Azure backup-kluis. Dit kan geen andere MABS-server zijn.<li>Geen optie als de database lokaal wordt gehost en u secundaire beveiliging wilt inschakelen. <li>Er komt extra voorbereidings- en hersteltijd bij kijken.|
|[Maak een back-up van de data base door een back-up te maken van de opslag groep MABS](#back-up-the-database-by-backing-up-the-mabs-storage-pool)|<li>Eenvoudig te configureren en te controleren.<li>De back-up wordt bewaard op de schijven van de MABS en is eenvoudig lokaal toegankelijk.<li>Geplande back-ups van MABS ondersteunen 512 snelle volledige back-ups. Als u elk uur een back-up maakt, hebt u 21 dagen volledige beveiliging.|<li>Geen goede optie voor noodherstel. Het is online en het herstel werkt mogelijk niet zoals verwacht als de MABS-server of de opslag groep mislukt.<li>Geen optie als de database lokaal wordt gehost en u secundaire beveiliging wilt inschakelen. <li>Enkele voor bereiding en speciale stappen zijn vereist om toegang te krijgen tot de herstel punten als de MABS-service of-console niet actief is of werkt.|
|[Back-up maken met systeemeigen SQL Server-back-up op een lokale schijf](#back-up-with-native-sql-server-backup-to-a-local-disk)|<li>In SQL Server ingebouwd.<li>De back-up wordt op een lokale schijf opgeslagen die eenvoudig toegankelijk is.<li>U kunt instellen dat deze zo vaak als u wilt wordt uitgevoerd.<li>Volledig onafhankelijk van MABS.<li>U kunt een back-upbestandsopruiming planning.|<li>Geen een goede optie voor noodherstel, tenzij de back-ups naar een externe locatie worden gekopieerd.<li>Vereist lokale opslag voor back-ups, die de Bewaar periode en frequentie kunnen beperken.|
|[Back-up maken met systeem eigen SQL-back-up en MABS-beveiliging naar een share die wordt beveiligd door MABS](#back-up-to-a-share-protected-by-mabs)|<li>Eenvoudig worden bewaakt in MABS.<li>Meerdere locaties van de back-updatabasebestanden.<li>Eenvoudig toegankelijk vanaf alle Windows-computers in het netwerk.<li>Mogelijk de snelste herstelmethode.|<li>Ondersteunt alleen 64 herstelpunten.<li>Geen goede optie voor noodherstel van site. Storing in de MABS-server of de MABS-opslag groep kan de herstel inspanningen belemmeren.<li>Geen optie als de MABS-data base lokaal wordt gehost en u secundaire beveiliging wilt inschakelen. <li>Extra voorbereiding is nodig om deze te configureren en testen.<li>Een aantal extra voor bereiding-en herstel tijd is nodig als de MABS-server zelf niet actief is, maar de schijven van de MABS-opslag groep prima zijn.|

- Als u een back-up maakt met behulp van een MABS-beveiligings groep, kunt u het beste een unieke beveiligings groep voor de Data Base gebruiken.

    > [!NOTE]
    > Voor herstel doeleinden moet de MABS-installatie die u wilt herstellen met de MABS-data base, overeenkomen met de versie van de MABS-data base zelf.  Als de data base die u wilt herstellen bijvoorbeeld afkomstig is van een MABS V3 met de installatie van de update pakket 1, moet op de MABS-server dezelfde versie met update pakket 1 worden uitgevoerd. Dit betekent dat u mogelijk MABS moet verwijderen en opnieuw moet installeren met een compatibele versie voordat u de data base herstelt.  Als u de databaseversie wilt controleren, moet u deze mogelijk handmatig koppelen aan een tijdelijke databasenaam en vervolgens een SQL-query voor de database uitvoeren om op basis van de primaire en secundaire versies te controleren welk updatepakket het meest recent is geïnstalleerd.

- Volg deze stappen om de versie van de MABS-data base te controleren:

    1. Als u de query wilt uitvoeren, opent u SQL Management Studio en maakt u vervolgens verbinding met het SQL-exemplaar waarop de MABS-data base wordt uitgevoerd.

    2. Selecteer de MABS-data base en start vervolgens een nieuwe query.

    3. Plak de volgende SQL-query in het querydeelvenster en voer de query uit:

        `Select distinct MajorVersionNumber,MinorVersionNumber ,BuildNumber, FileName FROM dbo.tbl\_AM\_AgentPatch order byMajorVersionNumber,MinorVersionNumber,BuildNumber`

    Als er niets wordt geretourneerd in de query resultaten of als de MABS-server is bijgewerkt van vorige versies, maar er sinds die tijd geen nieuw update pakket is geïnstalleerd, is er geen vermelding voor de primaire, secundaire voor een basis installatie van MABS. Raadpleeg de [lijst met buildnummers voor MABS](https://social.technet.microsoft.com/wiki/contents/articles/36381.microsoft-azure-backup-server-list-of-build-numbers.aspx)om de MABS-versies te controleren die aan de update pakketten zijn gekoppeld.

### <a name="back-up-to-azure"></a>Back-up naar Azure

1. Voordat u begint, moet u een script uitvoeren om het MABS van het replica volume op te halen, zodat u weet welk herstel punt de MABS-back-up bevat. Doe dit na de initiële replicatie met Azure Backup. Vervang in het script door `dplsqlservername%` de naam van het SQL Server exemplaar dat als host fungeert voor de MABS-data base.

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

    Zorg ervoor dat u de wachtwoord code hebt die is opgegeven toen de Azure Recovery Services-agent werd geïnstalleerd en dat de MABS-server is geregistreerd in de Azure Backup kluis. U hebt deze wachtwoordcode nodig om de back-up te herstellen.

2. Maak een Azure Backup-kluis en download het Azure Backup Agent-installatiebestand en de kluisreferenties. Voer het installatie bestand uit om de agent op de MABS-server te installeren en gebruik de kluis referenties om de MABS-server in de kluis te registreren. [Meer informatie](backup-azure-microsoft-azure-backup.md).

3. Nadat de kluis is geconfigureerd, stelt u een MABS-beveiligings groep in die de MABS-Data Base bevat. Selecteer deze optie als u een back-up wilt maken naar schijf en naar Azure.

#### <a name="recover-the-mabs-database-from-azure"></a>De MABS-data base herstellen vanuit Azure

U kunt de data base herstellen vanuit Azure met behulp van een MABS-server die is geregistreerd in de Azure Backup kluis, als volgt:

1. Selecteer in de MABS-console de optie **herstel**  >  **externe MABS toevoegen**.

2. Geef de kluis referenties op (down load van de Azure Backup kluis). Houd er rekening mee dat de referenties slechts twee dagen geldig zijn.

3. In **externe MABS voor herstel** selecteren selecteert u de MABS-server waarvoor u de Data Base wilt herstellen, typt u de wachtwoordzin voor versleuteling en selecteert u **OK.**

4. Selecteer het herstelpunt dat u wilt gebruiken in de lijst met beschikbare punten. Selecteer **externe MABS wissen** om terug te keren naar de weer gave lokale MABS.

## <a name="back-up-the-mabs-database-to-mabs-storage-pool"></a>Maak een back-up van de MABS-Data Base naar de MABS-opslag groep

> [!NOTE]  
> Deze optie is van toepassing op MABS met Modern Backup Storage.

1. Selecteer in de MABS-console **beveiliging beveiligings**  >  **groep maken**.
2. Op de pagina **Type beveiligingsgroep selecteren** selecteert u **Servers**.
3. Selecteer op de pagina **groeps leden selecteren** de optie **DPM-database**. Vouw de MABS-server uit en selecteer DPMDB.
4. Selecteer op de pagina **methode voor gegevens beveiliging selecteren** de optie Ik kies voor **kortetermijnbeveiliging met schijf**. Geef de opties voor kortetermijnbeveiliging op.
5. Voer na de initiële replicatie van de MABS-data base het volgende SQL-script uit:

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

### <a name="recover-mabs-database"></a>MABS-data base herstellen

Als u uw MABS opnieuw wilt samen stellen met dezelfde data base, moet u eerst de MABS-gegevens bank herstellen en deze synchroniseren met de nieuw geïnstalleerde MABS.

#### <a name="use-the-following-steps"></a>Gebruik de volgende stappen

1. Open een opdracht prompt voor beheer en voer uit `psexec.exe -s powershell.exe` om een Power shell-venster in de systeem context te starten.
2. Bepaal de locatie van waaruit u de Data Base wilt herstellen:

#### <a name="to-copy-the-database-from-the-last-backup"></a>De data base van de laatste back-up kopiëren

1. Navigeren naar het pad naar de replica-VHD `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PhysicalReplicaId\>`
2. Koppel de **disk0. vhdx** in het bestand met behulp van de `mount-vhd disk0.vhdx` opdracht.
3. Zodra de VHD met de replica is gekoppeld, gebruikt `mountvol.exe` u om een stationsletter toe te wijzen aan het replica volume met behulp van de fysieke replica-id van de SQL-script uitvoer. Bijvoorbeeld: `mountvol X: \?\Volume{}\`

#### <a name="to-copy-the-database-from-a-previous-recovery-point"></a>De data base van een vorig herstel punt kopiëren

1. Ga naar de container Directory DPMDB  `\<MABSServer FQDN\>\<PhysicalReplicaId\>` . U ziet meerdere mappen met enkele unieke GUID-id's onder de bijbehorende herstel punten die zijn gemaakt voor de MABS DB. Andere directory's vertegenwoordigen een PIT/herstel punt.
2. Ga naar een wille keurig pad naar een virtuele harde schijf, bijvoorbeeld `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PITId\>` en koppel de **disk0. vhdx** aanwezig met behulp van de `mount-vhd disk0.vhdx` opdracht.
3. Zodra de VHD met de replica is gekoppeld, gebruikt `mountvol.exe` u om een stationsletter toe te wijzen aan het replica volume met behulp van de fysieke replica-id van de SQL-script uitvoer. Bijvoorbeeld: `mountvol X: \?\Volume{}\`

   Alle termen die worden weer gegeven met hoek accolades in de bovenstaande stappen, zijn houders. Vervang deze door de juiste waarden als volgt:
   - **ReFSVolume** -toegangspad van de SQL-script uitvoer
   - **MABSSERVER FQDN** -volledig gekwalificeerde naam van de MABS-server
   - **PhysicalReplicaId** -id van de fysieke replica van het SQL-script uit
   - **PITId** -GUID-id, behalve de fysieke replica-id in de container Directory.
4. Open een andere opdracht prompt voor beheer en voer uit `psexec.exe -s cmd.exe` om een opdracht prompt in de systeem context te starten.
5. Wijzig de map in het station X: en navigeer naar de locatie van de MABS-database bestanden.
6. Kopieer deze naar een locatie waarvandaan u gemakkelijk kunt herstellen. Sluit het PsExec CMD-venster nadat u het hebt gekopieerd.
7. Ga naar het Power shell-venster PsExec dat u in stap 1 hebt geopend, navigeer naar het VHDX-pad en ontkoppel de VHDX met behulp van de opdracht `dismount-vhd disk0.vhdx` .
8. Nadat de MABS-server opnieuw is geïnstalleerd, kunt u de herstelde DPMDB gebruiken om aan de MABS-server te koppelen door de opdracht uit te voeren `DPMSYNC-RESTOREDB` .
9. `DPMSYNC-SYNC`Eenmaal uitvoeren `DPMSYNC-RESTOREDB` is voltooid.

### <a name="back-up-the-database-by-backing-up-the-mabs-storage-pool"></a>Maak een back-up van de data base door een back-up te maken van de opslag groep MABS

> [!NOTE]
> Deze optie is van toepassing op MABS met verouderde opslag.

Voordat u begint, moet u een script uitvoeren om het MABS van het replica volume op te halen, zodat u weet welk herstel punt de MABS-back-up bevat. Doe dit na de initiële replicatie met Azure Backup. Vervang in het script door `dplsqlservername%` de naam van het SQL Server exemplaar dat als host fungeert voor de MABS-data base.

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

1. Selecteer in de MABS-console **beveiliging beveiligings**  >  **groep maken**.

2. Selecteer op de pagina **type beveiligings groep selecteren** de optie  **servers**.

3. Selecteer op de pagina **groeps leden selecteren** de MABS-data base. Vouw het item MABS server uit en selecteer **DPMDB**.

4. Selecteer op de pagina  **methode voor gegevens beveiliging selecteren** de optie Ik kies voor **kortetermijnbeveiliging met schijf**. Geef de opties voor kortetermijnbeveiliging op. We raden een Bewaar termijn van twee weken aan voor MABS-data bases.

#### <a name="recover-the-database"></a>De database herstellen

Als de MABS-server nog steeds operationeel is en de opslag groep intact is (zoals problemen met de MABS-service of-console), kopieert u de data base van het replica volume of een schaduw kopie als volgt:

1. Selecteer het tijdstip vanaf waar u de database wilt herstellen.

    - Als u de data base van de laatste back-up wilt kopiëren die rechtstreeks vanuit het MABS-replica volume is gemaakt, gebruikt u **mountvol.exe** om een stationsletter toe te wijzen aan het replica volume met behulp van de GUID van de SQL-script uitvoer. Bijvoorbeeld: `C:\Mountvol X: \\?\Volume{d7a4fd76\-a0a8\-11e2\-8fd3\-001c23cb7375}\`

    - Als u de data base vanaf een vorig herstel punt (schaduw kopie) wilt kopiëren, moet u alle schaduw kopieën voor de replica weer geven met behulp van de volume-GUID van de SQL-script uitvoer. Met deze opdracht worden de schaduw kopieën voor dat volume weer gegeven: `C:\>Vssadmin list shadows /for\=\\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` . Noteer de aanmaak tijd en de schaduw kopie-ID vanaf welke u wilt herstellen.

2. Vervolgens gebruikt u **diskshadow.exe** om de schaduw kopie aan een ongebruikte stationsletter X: te koppelen met behulp van de schaduw kopie-id, zodat u de database bestanden kunt kopiëren.

3. Open een opdracht prompt voor beheer en voer uit `psexec.exe -s cmd.exe` om een opdracht prompt te starten in de systeem context, zodat u gemachtigd bent om naar het replica volume (X:) te navigeren en kopieer de bestanden.

4. CD-rom naar X: en navigeer naar de locatie van de MABS-database bestanden. Kopieer deze naar een locatie waarvandaan u gemakkelijk kunt herstellen. Nadat de kopie is voltooid, is het PsExec CMD-venster beschikbaar en wordt **diskshadow.exe** uitgevoerd en wordt het X:-volume zichtbaar.

5. U kunt de database bestanden nu herstellen met behulp van SQL Management Studio of door **DPMSYNC \- RESTOREDB** uit te voeren.

## <a name="back-up-with-native-sql-server-backup-to-a-local-disk"></a>Back-up maken met systeemeigen SQL Server-back-up naar een lokale schijf

U kunt een back-up maken van de MABS-Data Base op een lokale schijf met een eigen SQL Server back-up, onafhankelijk van MABS.

- Bekijk een [overzicht](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) van de back-up van SQL Server.

- [Meer informatie](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service) over back-ups maken van SQL Server naar de cloud.

## <a name="back-up-to-a-share-protected-by-mabs"></a>Back-up maken naar een share die wordt beveiligd door MABS

Deze back-upoptie gebruikt systeem eigen SQL om een back-up te maken van de MABS-Data Base naar een share, de share met MABS te beschermen en Windows VSS vorige versies te gebruiken om het herstel te vergemakkelijken.

### <a name="before-you-start"></a>Voordat u begint

1. Op de SQL Server maakt u een map op een station met voldoende beschik bare ruimte voor het opslaan van één kopie van een back-up. Bijvoorbeeld: `C:\MABSBACKUP`.

1. Deel de map. U kunt map bijvoorbeeld delen `C:\MABSBACKUP` als *DPMBACKUP*.

1. Kopieer en plak de OSQL-opdracht hieronder in Klad blok en sla deze op in een bestand met de naam `C:\MABSACKUP\bkupdb.cmd` . Zorg ervoor dat er geen. txt-extensie is. Wijzig de SQL_Instance_name en DPMDB_NAME zodat deze overeenkomen met de naam van het exemplaar en de DPMDB die door uw MABS-server wordt gebruikt.

    ```SQL
    OSQL -E -S localhost\SQL_INSTANCE_NAME -Q "BACKUP DATABASE DPMDB_NAME TO DISK='C:\DPMBACKUP\dpmdb.bak' WITH FORMAT"
    ```

1. Open in Klad blok het **ScriptingConfig.xml** -bestand dat zich bevindt in de `C:\Program Files\Microsoft System Center\DPM\DPM\Scripting` map op de MABS-server.

1. Wijzig **ScriptingConfig.xml** en wijzig **DataSource =** in de STATIONSLETTER die de map DPMDBBACKUP/share bevat. Wijzig de vermelding tag prebackupscript in het volledige pad en de naam van **bkupdb. cmd** dat is opgeslagen in stap 3.

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

1. Sla de wijzigingen in **ScriptingConfig.xml** op.

1. Beveilig de map C:\MABSBACKUP of de `\sqlservername\MABSBACKUP` share met behulp van MABS en wacht totdat de eerste replica is gemaakt. Er moet een **dpmdb. bak** aanwezig zijn in de map C:\MABSBACKUP als gevolg van het script dat voorafgaat aan de back-up, dat op zijn beurt is gekopieerd naar de replica MABS.

1. Als u herstel door selfservice Services niet inschakelt, moet u extra stappen uitvoeren om de map MABSBACKUP op de replica te delen:

    1. Ga in de MABS-console > **beveiliging** naar de gegevens bron MABSBACKUP en selecteer deze. Selecteer in de sectie Details de optie **klikken om details weer te geven** op de koppeling naar het pad naar de replica en het pad naar Klad blok te kopiëren. Verwijder het bronpad en behoud het doelpad. Het pad moet er ongeveer als volgt uitzien: `C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP` .

    2. Maak een share met dat pad met de share naam **MABSSERVERNAME-DPMDB**. U kunt de opdracht Net Share hieronder uit een beheeropdrachtprompt gebruiken.

        ```cmd
        Net Share MABSSERVERNAME-dpmdb="C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

### <a name="configure-the-backup"></a>De back-up configureren

U kunt een back-up maken van de MABS-Data Base op dezelfde manier als andere SQL Server-data bases met SQL Server systeem eigen back-up.

- Bekijk een [overzicht](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) van de back-up van SQL Server.

- [Meer informatie](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service) over back-ups maken van SQL Server naar de cloud.

### <a name="recover-the-mabs-database"></a>De MABS-data base herstellen

1. Maak `\\MABSServer\MABSSERVERNAME-dpmdb` vanaf elke Windows-computer verbinding met de share via Explorer.

2. Klik met de rechter muisknop op het bestand **dpmdb. bak** om de eigenschappen weer te geven. Op het tabblad **Vorige versies** staan alle back-ups die u kunt selecteren en kopiëren. De laatste back-up bevindt zich ook nog in de map C:\MABSBACKUP, die ook eenvoudig toegankelijk is.

3. Als u een aan een SAN gekoppelde MABS-opslag groep moet verplaatsen naar een andere server om te kunnen lezen van het replica volume of als u Windows opnieuw wilt installeren om lokale bijgevoegde schijven te lezen, moet u het pad of de volume-GUID van het MABS-replica volume eerst weten, zodat u weet welk volume de back-up van de Data Base bevat. U kunt het onderstaande SQL-script gebruiken om informatie op te halen op elk gewenst moment na de initiële beveiliging, maar voordat u moet herstellen. Vervang de `%dpmsqlservername%` door de naam van de SQL Server die als host fungeert voor de data base.

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

4. Als u wilt herstellen nadat u de MABS hebt verplaatst, of als u een MABS-server opnieuw bouwt:

    1. U hebt de volume-GUID, dus als u wilt dat het volume op een andere Windows-Server moet worden gekoppeld of nadat een MABS opnieuw is gebouwd, gebruikt u **mountvol.exe** om een stationsletter toe te wijzen met behulp van de volume-GUID van de SQL-script uitvoer: `C:\Mountvol X: \\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` .

    2. Deel de map MABSBACKUP op het replica volume opnieuw met behulp van de stationsletter en het gedeelte van het pad naar de map die de mapstructuur vertegenwoordigt.

        ```cmd
        net share SERVERNAME-DPMDB="X:\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

    3. Maak `\\SERVERNAME\MABSSERVERNAME-dpmdb` vanaf elke Windows-computer verbinding met de share via Explorer.

    4. Klik met de rechter muisknop op het bestand **dpmdb. bak** om de eigenschappen weer te geven. Op het tabblad **Vorige versies** staan alle back-ups die u kunt selecteren en kopiëren.

## <a name="using-dpmsync"></a>DPMSync gebruiken

**DpmSync** is een opdracht regel programma waarmee u de MABS-data base met de status van de schijven in de opslag groep en de geïnstalleerde beveiligingsagents kunt synchroniseren. DpmSync herstelt de MABS-data base, synchroniseert de MABS-data base met de replica's in de opslag groep, herstelt de rapport database en wijst de ontbrekende replica's opnieuw toe.

### <a name="parameters"></a>Parameters

| Parameter      | Beschrijving    |
|----------------|-----------------------------|
| **-RestoreDb**                       | Hiermee wordt een MABS-data base teruggezet vanaf een opgegeven locatie.|
| **-Synchroniseren**                            | Hiermee synchroniseert u herstelde data bases. U moet **DpmSync: Sync** uitvoeren na het herstellen van de data bases. Na het uitvoeren van **DpmSync – Sync**, kunnen sommige replica's nog steeds als ontbrekend worden gemarkeerd. |
| **-** *Locatie* van DbLoc                | Hiermee wordt de locatie van de back-up van de MABS-data base aangeduid.|
| **-INSTANCENAME** <br/>*server\instance*     | Exemplaar waarnaar DPMDB moet worden hersteld.|
| **-Reallocatereplica uit**         | Alle ontbrekende replica volumes worden opnieuw toegewezen zonder synchronisatie. |
| **-DataCopied**                      | Geeft aan dat u het laden van gegevens in de zojuist toegewezen replica volumes hebt voltooid. Dit is alleen van toepassing op client computers. |

**Voor beeld 1:** Als u de MABS-data base van een lokaal back-upmedium op de MABS-server wilt herstellen, voert u de volgende opdracht uit:

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak
```

Nadat u de MABS-Data Base hebt hersteld, voert u de volgende opdracht uit om de data bases te synchroniseren:

```cmd
DpmSync -Sync
```

Nadat u de MABS-Data Base hebt hersteld en gesynchroniseerd en voordat u de replica herstelt, voert u de volgende opdracht uit om de schijf ruimte voor de replica opnieuw toe te wijzen:

```cmd
DpmSync -ReallocateReplica
```

**Voor beeld 2:** Als u de MABS-Data Base wilt herstellen vanaf een externe data base, voert u de volgende opdracht uit op de externe computer:

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak –InstanceName contoso\ms$dpm
```

Nadat u de MABS-Data Base hebt hersteld, voert u de volgende opdracht uit op de MABS-server om de data bases te synchroniseren:

```cmd
DpmSync -Sync
```

Nadat u de MABS-Data Base hebt hersteld en gesynchroniseerd en voordat u de replica herstelt, voert u de volgende opdracht uit op de MABS-server om schijf ruimte voor de replica opnieuw toe te wijzen:

```cmd
DpmSync -ReallocateReplica
```

## <a name="next-steps"></a>Volgende stappen

- [Ondersteunings matrix voor MABS](backup-support-matrix-mabs-dpm.md)
- [VEELGESTELDE VRAGEN MABS](backup-azure-dpm-azure-server-faq.md)