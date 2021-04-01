---
title: Schijven uitsluiten van replicatie met Azure Site Recovery
description: Schijven uitsluiten van replicatie naar Azure met Azure Site Recovery.
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 15989fbfd65f758eb777c5170c217aba8707e0be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008256"
---
# <a name="exclude-disks-from-disaster-recovery"></a>Schijven uitsluiten van herstel na nood gevallen

In dit artikel wordt beschreven hoe u schijven uitsluiten van replicatie tijdens herstel na nood gevallen van on-premises naar Azure met [Azure site Recovery](site-recovery-overview.md). U kunt schijven om een aantal redenen uitsluiten van replicatie:

- De niet-belangrijkste gegevens die op de uitgesloten schijf worden overgevoerd, worden niet gerepliceerd.
- Voor het optimaliseren van verbruikte replicatie bandbreedte of doel bronnen.
- Om opslag-en netwerk bronnen op te slaan door niet de gegevens te repliceren die u niet nodig hebt.
- Azure-Vm's hebben Site Recovery replicatie limieten bereikt.


## <a name="supported-scenarios"></a>Ondersteunde scenario's

U kunt schijven uitsluiten van replicatie zoals in de tabel wordt samenvatten.

**Azure naar Azure** | **VMware naar Azure** | **Hyper-V naar Azure** | **Fysieke server naar Azure**
--- | --- | --- | ---
Ja | Ja | Ja | Ja

## <a name="exclude-limitations"></a>Beperkingen uitsluiten

**Beperking** | **Azure-VM's** | **VMware-VM's** | **Virtuele Hyper-V-machines**
--- | --- | ---
**Schijf typen** | U kunt basis schijven uitsluiten van replicatie.<br/><br/> U kunt geen besturingssysteem schijven of dynamische schijven uitsluiten. Tijdelijke schijven worden standaard uitgesloten. | U kunt basis schijven uitsluiten van replicatie.<br/><br/> U kunt geen besturingssysteem schijven of dynamische schijven uitsluiten. | U kunt basis schijven uitsluiten van replicatie.<br/><br/> Besturingssysteemschijven kunt u niet uitsluiten. We raden u aan geen dynamische schijven uit te sluiten. Site Recovery kan niet identificeren welke VHS Basic of Dynamic is in de gast-VM. Als niet alle afhankelijke dynamische volume schijven worden uitgesloten, wordt de beveiligde dynamische schijf een defecte schijf op een failover-VM. de gegevens op de schijf zijn dan niet toegankelijk.
**Schijf repliceren** | U kunt een schijf die wordt gerepliceerd niet uitsluiten.<br/><br/> Schakel replicatie voor de virtuele machine uit en opnieuw in. |  U kunt een schijf die wordt gerepliceerd niet uitsluiten. |  U kunt een schijf die wordt gerepliceerd niet uitsluiten.
**Mobility service (VMware)** | Niet relevant | U kunt alleen schijven uitsluiten op Vm's waarop de Mobility-service is geïnstalleerd.<br/><br/> Dit betekent dat u de Mobility-service hand matig moet installeren op de virtuele machines waarvoor u schijven wilt uitsluiten. U kunt het mechanisme push installatie niet gebruiken omdat de Mobility-service pas wordt geïnstalleerd nadat replicatie is ingeschakeld. | Niet relevant.
**Toevoegen/verwijderen** | U kunt beheerde schijven toevoegen aan Azure-Vm's met replicatie mogelijkheden met beheerde schijven. U kunt geen schijven verwijderen van Azure-Vm's die zijn ingeschakeld voor replicatie. | U kunt geen schijven toevoegen of verwijderen nadat de replicatie is ingeschakeld. Schakel de replicatie uit en weer in om een schijf toe te voegen. | U kunt geen schijven toevoegen of verwijderen nadat de replicatie is ingeschakeld. Schakel de replicatie uit en weer in.
**Failover** | Als een app een door u uitgesloten schijf nodig heeft, moet u na de failover de schijf hand matig maken zodat de gerepliceerde app kan worden uitgevoerd.<br/><br/> U kunt de schijf ook tijdens een VM-failover maken door Azure Automation te integreren in een herstel plan. | Als u een schijf uitsluit die nodig is voor een app, maakt u deze hand matig in azure na een failover. | Als u een schijf uitsluit die nodig is voor een app, maakt u deze hand matig in azure na een failover.
**On-premises failback-schijven die hand matig zijn gemaakt** | Niet relevant | **Windows-vm's**: schijven die hand matig zijn gemaakt in azure, worden niet meer weer gegeven. Als u bijvoorbeeld een failover hebt uitgevoerd voor drie schijven en twee schijven rechtstreeks op een virtuele Azure-machine maakt, worden alleen de drie schijven waarvoor een failover is voltooid, teruggezet.<br/><br/> **Linux-vm's**: schijven die hand matig zijn gemaakt in azure, worden teruggezet. Als u bijvoorbeeld een failover hebt uitgevoerd voor drie schijven en er twee schijven op een virtuele Azure-machine maakt, wordt er een fout weer gegeven. U kunt geen schijven die handmatig zijn gemaakt, uitsluiten van failback. | Schijven die hand matig zijn gemaakt in azure, worden niet meer weer gegeven. Als u bijvoorbeeld een failover hebt uitgevoerd voor drie schijven en twee schijven rechtstreeks op een virtuele Azure-machine maakt, worden er slechts drie schijven met een failover-fout weer gegeven.
**On-premises failback-uitgesloten schijven** | Niet relevant | Als u terugkeert naar de oorspronkelijke computer, bevat de failback-VM-schijf configuratie geen uitgesloten schijven. Schijven die zijn uitgesloten van VMware naar Azure-replicatie, zijn niet beschikbaar op de failback-VM. | Wanneer failback naar de oorspronkelijke Hyper-V-locatie gaat, blijft de schijf configuratie van de failback-VM hetzelfde als die van de oorspronkelijke bron-VM-schijf. Schijven die zijn uitgesloten van Hyper-V-site naar Azure-replicatie, zijn beschikbaar op de failback-VM.



## <a name="typical-scenarios"></a>Typische scenario's

Voor beelden van gegevens verloop die fantastische kandidaten zijn voor uitsluiting, zijn schrijf bewerkingen naar een wissel bestand (pagefile.sys) en schrijven naar het TempDB-bestand van Microsoft SQL Server. Afhankelijk van de werk belasting en het opslag subsysteem kunnen de paginerings-en tempdb-bestanden een aanzienlijke hoeveelheid verloop registreren. Het repliceren van dit type gegevens naar Azure is resource-intensief.

- Als u de replicatie voor een virtuele machine wilt optimaliseren, met één virtueel schijf die zowel het besturings systeem als het wissel bestand bevat, kunt u het volgende doen:
    1. Splits de virtuele schijf in twee virtuele schijven. De ene virtuele schijf bevat het besturingssysteem en de andere het wisselbestand.
    2. Sluit de wisselbestandschijf uit van replicatie.

- Als u de replicatie wilt optimaliseren voor een schijf die zowel het Microsoft SQL Server TempDB-bestand als het systeem database bestand bevat, kunt u het volgende doen:
    1. Zet het systeemdatabasebestand en het tempdb-bestand op twee verschillende schijven.
    2. Sluit de tempdb-schijf uit van replicatie.

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>Voorbeeld 1: de tempdb-schijf in SQL Server uitsluiten

Laten we eens kijken hoe u schijf uitsluiting, failover en failover kunt afhandelen voor een bron SQL Server Windows VM-* * * SalesDB * * *, waarvoor we TempDB willen uitsluiten. 

### <a name="exclude-disks-from-replication"></a>Schijven uitsluiten van replicatie

We hebben deze schijven op de bron-SalesDB van de Windows-VM.

**Schijf naam** | **Gast besturingssysteem schijf** | **Stationsletter** | **Gegevens type schijf**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Besturingssysteem schijf.
DB-Disk1| Disk1 | D:\ | SQL-systeem database en gebruiker Database1.
DB-Disk2 (de schijf is uitgesloten van beveiliging) | Disk2 | E:\ | Tijdelijke bestanden.
DB-Disk3 (de schijf is uitgesloten van beveiliging) | Disk3 | F:\ | SQL-data base Tempdb.<br/><br/> Mappad-F:\MSSQL\Data\. Noteer het mappad voordat u een failover uitvoert.
DB-Disk4 | Disk4 |G:\ | Gebruikersdatabase2

1. De replicatie voor de SalesDB-VM wordt ingeschakeld.
2. Disk2 en Disk3 worden uitgesloten van replicatie, omdat gegevens verloop op die schijven tijdelijk is. 


### <a name="handle-disks-during-failover"></a>Schijven afhandelen tijdens failover

Omdat de schijven niet worden gerepliceerd, worden de virtuele machines die na een failover zijn gemaakt, niet weer gegeven als u een failover naar Azure hebt. De virtuele machine van Azure bevat de schijven die in deze tabel worden samenvatten.

**Gast besturingssysteem schijf** | **Stationsletter** | **Gegevens type schijf**
--- | --- | ---
Disk0 | C:\ | Besturingssysteem schijf.
Disk1 | E:\ | Tijdelijke opslag<br/><br/>Azure voegt deze schijf toe. Omdat Disk2 en Disk3 zijn uitgesloten van replicatie, is E: de eerste stationsletter in de lijst beschik bare. Azure wijst E: toe aan het tijdelijke opslagvolume. Andere stationsletters voor gerepliceerde schijven blijven hetzelfde.
Disk2 | D:\ | SQL-systeemdatabase en gebruikersdatabase 1
Disk3 | G:\ | Gebruikersdatabase2

In ons voor beeld is de SQL TempDB-schijf, omdat Disk3 is uitgesloten van replicatie en niet beschikbaar op de virtuele Azure-machine, de SQL-service is gestopt en heeft het F:\MSSQL\Data-pad nodig. U kunt dit pad op een aantal manieren maken: 

- Voeg na een failover een nieuwe schijf toe en wijs een TempDB-mappad toe.
- Gebruik een bestaande tijdelijke opslagschijf voor het tempdb-mappad.

#### <a name="add-a-new-disk-after-failover"></a>Een nieuwe schijf toevoegen na een failover

1. Noteer de paden van SQL-tempdb.mdf en tempdb.ldf voordat de failover wordt uitgevoerd.
2. Voeg vanuit het Azure Portal een nieuwe schijf toe aan de failover Azure-VM. De schijf moet even groot (of groter) zijn als de bron-SQL TempDB-schijf (Disk3).
3. Meld u aan bij de Azure VM.
4. Initialiseer en formatteer de nieuw toegevoegde schijf via de schijfbeheerconsole (diskmgmt.msc). 
5. Wijs dezelfde stationsletter toe die is gebruikt door de SQL-TempDB-schijf (F:)
6. Maak een tempdb-map op de F:-schijf (F:\MSSQL\Data).
7. Start de SQL-service via de serviceconsole.

#### <a name="use-an-existing-temporary-storage-disk"></a>Een bestaande tijdelijke opslag schijf gebruiken 

1. Open een opdrachtprompt.
2. Voer SQL Server in de herstelmodus uit via de opdrachtprompt.

    ```console
    Net start MSSQLSERVER /f / T3608
    ```

3. Voer de volgende sqlcmd uit om het tempdb-pad in een nieuw pad te wijzigen.

    ```sql
    sqlcmd -A -S SalesDB        **Use your SQL DBname**
    USE master;     
    GO      
    ALTER DATABASE tempdb       
    MODIFY FILE (NAME = tempdev, FILENAME = 'E:\MSSQL\tempdata\tempdb.mdf');
    GO      
    ALTER DATABASE tempdb       
    MODIFY FILE (NAME = templog, FILENAME = 'E:\MSSQL\tempdata\templog.ldf');       
    GO
    ```

4. Stop de Microsoft SQL Server-service.

    ```console
    Net stop MSSQLSERVER
    ```

5. Start de Microsoft SQL Server-service.

    ```console
    Net start MSSQLSERVER
    ```

### <a name="vmware-vms-disks-during-failback-to-original-location"></a>VMware-Vm's: schijven tijdens de failback naar de oorspronkelijke locatie

Nu gaan we kijken hoe u schijven op virtuele VMware-machines afhandelt wanneer u terugkeert naar de oorspronkelijke on-premises locatie.

- **Schijven die zijn gemaakt in azure**: aangezien in het voor beeld een Windows-VM wordt gebruikt, worden schijven die u hand matig in azure maakt, niet gerepliceerd naar uw site wanneer u een failback of een virtuele machine opnieuw beveiligt.
- **Tijdelijke opslag schijf in azure**: de tijdelijke opslag schijf wordt niet gerepliceerd naar on-premises hosts.
- **Uitgesloten schijven**: schijven die zijn uitgesloten van VMware naar Azure-replicatie, zijn na de failback niet beschikbaar op de on-premises VM.

Voordat u de virtuele VMware-machines terugstuurt naar de oorspronkelijke locatie, zijn de schijf instellingen van de Azure VM als volgt.

**Gast besturingssysteem schijf** | **Stationsletter** | **Gegevens type schijf**
--- | --- | ---
Disk0 | C:\ | Besturingssysteem schijf.
Disk1 | E:\ | Tijdelijke opslag.
Disk2 | D:\ | SQL-systeem database en gebruiker Database1.
Disk3 | G:\ | Database2 van gebruiker.

Na de failback heeft de virtuele VMware-machine op de oorspronkelijke locatie de schijven in de tabel samenvatten.

**Gast besturingssysteem schijf** | **Stationsletter** | **Gegevens type schijf**
--- | --- | ---
Disk0 | C:\ | Besturingssysteem schijf.
Disk1 | D:\ | SQL-systeem database en gebruiker Database1.
Disk2 | G:\ | Database2 van gebruiker.


### <a name="hyper-v-vms-disks-during-failback-to-original-location"></a>Virtuele Hyper-V-machines: schijven tijdens de failback naar de oorspronkelijke locatie

Nu gaan we kijken hoe u schijven op virtuele Hyper-V-machines afhandelt wanneer u terugkeert naar de oorspronkelijke on-premises locatie.

- **Schijven die zijn gemaakt in azure**: schijven die u hand matig in azure maakt, worden niet gerepliceerd naar uw site wanneer u een failback maakt of een VM opnieuw beveiligt.
- **Tijdelijke opslag schijf in azure**: de tijdelijke opslag schijf wordt niet gerepliceerd naar on-premises hosts.
- **Uitgesloten schijven**: na failback is de schijf configuratie van de virtuele machine hetzelfde als de oorspronkelijke VM-schijf configuratie. Schijven die zijn uitgesloten van replicatie van Hyper-V naar Azure, zijn beschikbaar op de failback-VM.

Voordat u de Hyper-V-Vm's naar de oorspronkelijke locatie terugstuurt, zijn de Azure VM-schijf instellingen als volgt.

**Gast besturingssysteem schijf** | **Stationsletter** | **Gegevens type schijf**
--- | --- | ---
Disk0 | C:\ | Besturingssysteem schijf.
Disk1 | E:\ | Tijdelijke opslag.
Disk2 | D:\ | SQL-systeem database en gebruiker Database1.
Disk3 | G:\ | Database2 van gebruiker.

Na de geplande failover (failback) van Azure naar on-premises Hyper-V heeft de virtuele Hyper-V-machine op de oorspronkelijke locatie de schijven in de tabel samenvatten.

**Schijfnaam** | **Gastbesturingssysteem schijf#** | **Stationsletter** | **Gegevens type schijf**
 --- | --- | --- | ---
DB-Disk0-OS | Disk0 |   C:\ | Besturingssysteem schijf.
DB-Disk1 | Disk1 | D:\ | SQL-systeem database en gebruiker Database1.
DB-Disk2 (uitgesloten schijf) | Disk2 | E:\ | Tijdelijke bestanden.
DB-Disk3 (uitgesloten schijf) | Disk3 | F:\ | SQL-TempDB-data base<br/><br/> Mappad (F:\MSSQL\Data \) .
DB-Disk4 | Disk4 | G:\ | Gebruikersdatabase2


## <a name="example-2-exclude-the-paging-file-disk"></a>Voor beeld 2: de wissel bestand schijf uitsluiten

Laten we eens kijken hoe u schijf uitsluiting, failover en failover kunt afhandelen voor een Windows-bron-VM waarvoor we de pagefile.sys bestands schijf op het D-station en een alternatief station willen uitsluiten.


### <a name="paging-file-on-the-d-drive"></a>Wissel bestand op het D-station

We hebben deze schijven op de bron-VM.

**Schijf naam** | **Gast besturingssysteem schijf** | **Stationsletter** | **Gegevens type schijf**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Besturingssysteemschijf
DB-Disk1 (uitsluiten van replicatie) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Gebruikersgegevens 1
DB-Disk3 | Disk3 | F:\ | Gebruikersgegevens 2

De instellingen voor het wissel bestand op de bron-VM zijn als volgt:

![Scherm afbeelding van het dialoog venster virtueel geheugen met de regel D: Station [wissel bestand] gemarkeerd met een wissel bestand grootte (MB) van 3000-7000.](./media/exclude-disks-replication/pagefile-d-drive-source-vm.png)

1. De replicatie voor de virtuele machine wordt ingeschakeld.
2. DB-Disk1 worden uitgesloten van replicatie.

#### <a name="disks-after-failover"></a>Schijven na een failover

Na een failover heeft de Azure-VM de schijven in de tabel samenvatten.

**Schijf naam** | **Gastbesturingssysteemschijf#** | **Stationsletter** | **Gegevenstype op de schijf**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Besturingssysteemschijf
DB-Disk1 | Disk1 | D:\ | Tijdelijke opslag/pagefile.sys <br/><br/> Omdat DB-Disk1 (D:) is uitgesloten, D: is de eerste stationsletter in de lijst beschik bare.<br/><br/> Azure wijst D: toe aan het tijdelijke opslagvolume.<br/><br/> Omdat D: beschikbaar is, blijft de instelling van het wissel bestand van de virtuele machine hetzelfde.
DB-Disk2 | Disk2 | E:\ | Gebruikersgegevens 1
DB-Disk3 | Disk3 | F:\ | Gebruikersgegevens 2

Onze instellingen voor het wissel bestand op de virtuele Azure-machine zijn als volgt:

![Instellingen voor het wisselbestand op de virtuele Azure-machine](./media/exclude-disks-replication/pagefile-azure-vm-after-failover.png)

### <a name="paging-file-on-another-drive-not-d"></a>Wissel bestand op een ander station (niet D:)

We kijken naar het voor beeld waarin het wissel bestand zich niet op het D-station bevindt.  

We hebben deze schijven op de bron-VM.

**Schijf naam** | **Gast besturingssysteem schijf** | **Stationsletter** | **Gegevens type schijf**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Besturingssysteemschijf
DB-Disk1 (uitsluiten van replicatie) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | Gebruikersgegevens 1
DB-Disk3 | Disk3 | F:\ | Gebruikersgegevens 2

Onze instellingen voor het wissel bestand op de on-premises VM zijn als volgt:

![Instellingen voor het wisselbestand op de on-premises virtuele machine](./media/exclude-disks-replication/pagefile-g-drive-source-vm.png)

1. De replicatie voor de virtuele machine wordt ingeschakeld.
2. DB-Disk1 worden uitgesloten van replicatie.

#### <a name="disks-after-failover"></a>Schijven na een failover

Na een failover heeft de Azure-VM de schijven in de tabel samenvatten.

**Schijf naam** | **Gastbesturingssysteem schijf#** | **Stationsletter** | **Gegevens type schijf**
--- | --- | --- | ---
DB-Disk0-OS | Disk0  |C:\ | Besturingssysteemschijf
DB-Disk1 | Disk1 | D:\ | Tijdelijke opslag<br/><br/> Aangezien D: de eerste letter in de lijst is, wijst Azure de letter D: toe aan het tijdelijke opslagvolume.<br/><br/> De stationsletter is voor alle gerepliceerde schijven hetzelfde.<br/><br/> Omdat de schijf G: niet beschikbaar is, gebruikt het systeem station C: voor het wissel bestand.
DB-Disk2 | Disk2 | E:\ | Gebruikersgegevens 1
DB-Disk3 | Disk3 | F:\ | Gebruikersgegevens 2

Onze instellingen voor het wissel bestand op de virtuele Azure-machine zijn als volgt:

![Scherm afbeelding van het dialoog venster virtueel geheugen met de C:-station is gemarkeerd met de instelling voor de grootte van het wissel bestand ' systeem beheerd '.](./media/exclude-disks-replication/pagefile-azure-vm-after-failover-2.png)


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over richt lijnen voor de tijdelijke opslag schijf:
    - [Meer informatie over](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) het gebruik van Ssd's in azure vm's om SQL Server tempdb-en Buffergroepuitbreiding-extensies op te slaan
    - [Bekijk ](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md) de aanbevolen procedures voor de prestaties van SQL Server in azure vm's.
- Wanneer uw implementatie actief is, kunt u [hier](failover-failback-overview.md) meer lezen over de verschillende soorten failovers.
