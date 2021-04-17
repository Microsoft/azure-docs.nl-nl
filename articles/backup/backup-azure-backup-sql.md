---
title: Back-SQL Server naar Azure als een DPM-workload
description: Een inleiding tot het maken van back-SQL Server databases met behulp van de Azure Backup service
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 03763c3bee5ee4ca5239c49c99fdbeedc242b24d
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515172"
---
# <a name="back-up-sql-server-to-azure-as-a-dpm-workload"></a>Back-SQL Server naar Azure als een DPM-workload

Dit artikel leidt u door de configuratiestappen voor het maken van back-SQL Server databases met behulp van Azure Backup.

Als u een back-SQL Server databases naar Azure wilt maken, hebt u een Azure-account nodig. Als u nog geen account hebt, kunt u binnen een paar minuten een gratis account maken. Zie Uw gratis [Azure-account maken voor meer informatie.](https://azure.microsoft.com/pricing/free-trial/)

Een back-up maken van SQL Server database naar Azure en deze herstellen vanuit Azure:

1. Maak een back-upbeleid voor het beveiligen SQL Server databases in Azure.
1. Back-ups op aanvraag maken in Azure.
1. Herstel de database vanuit Azure.

>[!NOTE]
>DPM 2019 UR2 ondersteunt SQL Server FCI (Failover Cluster Instances) met behulp van gedeelde clustervolumes (CSV).<br><br>
>Beveiliging van SQL Server failovercluster-exemplaar met Opslagruimten Direct in [Azure](../azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure.md)  en SQL Server failovercluster-exemplaar met [gedeelde Azure-schijven](../azure-sql/virtual-machines/windows/failover-cluster-instance-azure-shared-disks-manually-configure.md) wordt ondersteund met deze functie. De DPM-server moet worden geïmplementeerd in de virtuele Azure-machine om het SQL FCI-exemplaar te beveiligen dat is geïmplementeerd op azure-VM's. 

## <a name="prerequisites-and-limitations"></a>Vereisten en beperkingen

* Als u een database met bestanden op een externe bestandsshare hebt, mislukt de beveiliging met fout-id 104. DPM biedt geen ondersteuning voor beveiliging voor SQL Server gegevens op een externe bestands share.
* DPM kan geen databases beveiligen die zijn opgeslagen op externe SMB-shares.
* Zorg ervoor dat [de replica's van de beschikbaarheidsgroep zijn geconfigureerd als alleen-lezen.](/sql/database-engine/availability-groups/windows/configure-read-only-access-on-an-availability-replica-sql-server)
* U moet het systeemaccount **NTAuthority\System** expliciet toevoegen aan de groep Sysadmin op SQL Server.
* Wanneer u een alternatieve locatieherstel voor een gedeeltelijk ingesloten database wilt uitvoeren, moet u ervoor zorgen dat de functie [Ingesloten databases](/sql/relational-databases/databases/migrate-to-a-partially-contained-database#enable) is ingeschakeld voor het doel-SQL-exemplaar.
* Wanneer u een alternatieve locatieherstel voor een bestandsstroomdatabase wilt uitvoeren, moet u ervoor zorgen dat de databasefunctie bestandsstroom is ingeschakeld voor het SQL-doel-exemplaar. [](/sql/relational-databases/blob/enable-and-configure-filestream)
* Beveiliging voor SQL Server AlwaysOn:
  * DPM detecteert beschikbaarheidsgroepen bij het uitvoeren van een query bij het maken van beveiligingsgroepen.
  * DPM detecteert een failover en zet de beveiliging van de database voort.
  * DPM ondersteunt configuraties van clusters op meerdere locaties voor een exemplaar van SQL Server.
* Wanneer u databases beveiligt die het kenmerk AlwaysOn gebruiken, heeft DPM de volgende beperkingen:
  * DPM respecteert het back-upbeleid voor beschikbaarheidsgroepen die zijn ingesteld in SQL Server op basis van de back-upvoorkeuren, als volgt:
    * Voorkeur voor secundaire: back-ups moeten op een secundaire replica plaatsvinden, behalve wanneer de primaire replica de enige replica online is. Als er meerdere secundaire replica's beschikbaar zijn, wordt het knooppunt met de hoogste back-upprioriteit geselecteerd voor back-up. Als alleen de primaire replica beschikbaar is, moet de back-up plaatsvinden op de primaire replica.
    * Alleen secundaire: back-up mag niet op de primaire replica worden uitgevoerd. Als de primaire replica de enige online replica is, mag de back-up niet plaatsvinden.
    * Primaire: back-ups moeten altijd op de primaire replica plaatsvinden.
    * Iedere replica: back-ups kunnen op alle beschikbare replica's in de beschikbaarheidsgroep plaatsvinden. Het knooppunt waarvan een back-up moet worden gemaakt, zal gebaseerd zijn op de back-upprioriteiten voor elk van de knooppunten.
  * Houd rekening met het volgende:
    * Back-ups kunnen plaatsvinden vanaf elke leesbare replica, dat wil zeggen, primaire, synchrone secundaire, asynchrone secundaire.
    * Als een replica wordt uitgesloten van back-up, bijvoorbeeld Replica uitsluiten **is** ingeschakeld of is gemarkeerd als niet-leesbaar, wordt die replica niet geselecteerd voor back-up onder een van de opties.
    * Als er meerdere replica's beschikbaar en leesbaar zijn, wordt het knooppunt met de hoogste back-upprioriteit geselecteerd voor back-up.
    * Als de back-up op het geselecteerde knooppunt mislukt, mislukt de back-upbewerking.
    * Herstel naar de oorspronkelijke locatie wordt niet ondersteund.
* SQL Server back-upproblemen van 2014 of hoger:
  * SQL Server 2014 heeft een nieuwe functie toegevoegd voor het maken van een database voor [on-premises SQL Server in Windows Azure Blob Storage.](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure) DPM kan niet worden gebruikt om deze configuratie te beveiligen.
  * Er zijn enkele bekende problemen met de back-upvoorkeur 'Voorkeur voor secundaire' voor de optie SQL AlwaysOn. DPM maakt altijd een back-up van de secundaire. Als er geen secundaire kan worden gevonden, mislukt de back-up.

## <a name="before-you-start"></a>Voordat u begint

Voordat u begint, moet u ervoor zorgen dat u aan de vereisten [voor het](backup-azure-dpm-introduction.md#prerequisites-and-limitations) gebruik van Azure Backup voor het beveiligen van workloads hebt voldaan. Hier zijn enkele van de vereiste taken:

* Maak een back-upkluis.
* Download kluisreferenties.
* Installeer de Azure Backup agent.
* Registreer de server bij de kluis.

## <a name="create-a-backup-policy"></a>Maak een back-upbeleid

Als u uw SQL Server in Azure wilt beveiligen, maakt u eerst een back-upbeleid:

1. Selecteer op Data Protection Manager (DPM)-server de **werkruimte** Beveiliging.
1. Selecteer **Nieuw om** een beveiligingsgroep te maken.

    ![Een beveiligingsgroep maken](./media/backup-azure-backup-sql/protection-group.png)
1. Lees op de startpagina de richtlijnen voor het maken van een beveiligingsgroep. Selecteer vervolgens **Volgende**.
1. Selecteer **Servers**.

    ![Selecteer het type beveiligingsgroep Servers](./media/backup-azure-backup-sql/pg-servers.png)
1. Vouw de SQL Server machine uit waarop de databases zich bevinden waarop u een back-up wilt maken. U ziet de gegevensbronnen van wie vanaf die server een back-up kan worden maken. Vouw **Alle SQL-shares** uit en selecteer vervolgens de databases van wie u een back-up wilt maken. In dit voorbeeld selecteren we ReportServer$MSDPM2012 en ReportServer$MSDPM2012TempDB. Selecteer vervolgens **Volgende**.

    ![Een database SQL Server selecteren](./media/backup-azure-backup-sql/pg-databases.png)
1. Noem de beveiligingsgroep en selecteer vervolgens **Ik wil onlinebeveiliging.**

    ![Een methode voor gegevensbeveiliging kiezen: kortetermijnschijfbeveiliging of online Azure-beveiliging](./media/backup-azure-backup-sql/pg-name.png)
1. Neem op **de Short-Term doelen opgeven** de benodigde invoer op om back-uppunten naar de schijf te maken.

    In dit voorbeeld **is Bewaartermijn** ingesteld op *5 dagen.* De frequentie **van de back-upsynchronisatie** wordt elke *15 minuten ingesteld op*. **Express Full Backup** is ingesteld op *20:00 uur.*

    ![Kortetermijndoelen instellen voor back-upbeveiliging](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > In dit voorbeeld wordt elke dag om 20:00 uur een back-uppunt gemaakt. De gegevens die zijn gewijzigd sinds het back-uppunt van de vorige dag om 20:00 uur, worden overgedragen. Dit proces wordt Express **Full Backup genoemd.** Hoewel de transactielogboeken elke 15 minuten worden gesynchroniseerd, wordt het punt gemaakt als de database om 21:00 uur moet worden hersteld. In dit voorbeeld wordt het punt gemaakt door de logboeken opnieuw af te spelen vanaf het laatste volledige back-uppunt. Dit is in dit voorbeeld 20:00 uur.
   >
   >

1. Selecteer **Next**. DPM toont de totale beschikbare opslagruimte. Het toont ook het potentiële schijfruimtegebruik.

    ![Schijftoewijzing instellen](./media/backup-azure-backup-sql/pg-storage.png)

    DPM maakt standaard één volume per gegevensbron (SQL Server database). Het volume wordt gebruikt voor de eerste back-upkopie. In deze configuratie beperkt Logical Disk Manager (LDM) DPM-beveiliging tot 300 gegevensbronnen (SQL Server databases). Als u deze beperking wilt omzeilen, **selecteert u Co-locate data in DPM Storage Pool**. Als u deze optie gebruikt, gebruikt DPM één volume voor meerdere gegevensbronnen. Met deze installatie kan DPM maximaal 2000 databases SQL Server beveiligen.

    Als u Volumes **automatisch laten groeien selecteert,** kan DPM rekening houden met het toegenomen back-upvolume naarmate de productiegegevens toenemen. Als u volumes automatisch laten groeien **niet** selecteert, beperkt DPM de back-upopslag tot de gegevensbronnen in de beveiligingsgroep.

1. Als u een beheerder bent, kunt u ervoor kiezen om deze eerste back-up automatisch **via** het netwerk over te dragen en de overdrachtstijd te kiezen. Of kies ervoor **om de back-up** handmatig over te dragen. Selecteer vervolgens **Volgende**.

    ![Een methode voor het maken van replica's kiezen](./media/backup-azure-backup-sql/pg-manual.png)

    De eerste back-upkopie vereist de overdracht van de volledige gegevensbron (SQL Server database). De back-upgegevens worden verplaatst van de productieserver (SQL Server computer) naar de DPM-server. Als deze back-up groot is, kan het overdragen van de gegevens via het netwerk leiden tot opstoppingen in de bandbreedte. Daarom kunnen beheerders ervoor kiezen om verwisselbare media te gebruiken om de eerste back-up **handmatig over te dragen.** Of ze kunnen de gegevens **op een opgegeven** tijdstip automatisch via het netwerk overdragen.

    Nadat de eerste back-up is gemaakt, worden back-ups incrementeel voortgezet op de eerste back-upkopie. Incrementele back-ups zijn doorgaans klein en kunnen eenvoudig via het netwerk worden overgedragen.

1. Kies wanneer u een consistentiecontrole wilt uitvoeren. Selecteer vervolgens **Volgende**.

    ![Kiezen wanneer u een consistentiecontrole wilt uitvoeren](./media/backup-azure-backup-sql/pg-consistent.png)

    DPM kan een consistentiecontrole uitvoeren op de integriteit van het back-uppunt. Het berekent de controlesum van het back-upbestand op de productieserver (de SQL Server-computer in dit voorbeeld) en de back-upgegevens voor dat bestand in DPM. Als met de controle een conflict wordt gevonden, wordt ervan uitgegaan dat het back-upbestand in DPM beschadigd is. DPM herstelt de back-upgegevens door de blokken te verzenden die overeenkomen met de niet-overeenkomende controlesum. Omdat de consistentiecontrole een prestatie-intensieve bewerking is, kunnen beheerders ervoor kiezen om de consistentiecontrole te plannen of deze automatisch uit te voeren.

1. Selecteer de gegevensbronnen die u wilt beveiligen in Azure. Selecteer vervolgens **Volgende**.

    ![Gegevensbronnen selecteren om te beveiligen in Azure](./media/backup-azure-backup-sql/pg-sqldatabases.png)
1. Als u een beheerder bent, kunt u back-upschema's en bewaarbeleidsregels kiezen die geschikt zijn voor het beleid van uw organisatie.

    ![Planningen en bewaarbeleid kiezen](./media/backup-azure-backup-sql/pg-schedule.png)

    In dit voorbeeld worden dagelijks om 12:00 uur en 20:00 uur back-ups gemaakt.

    > [!TIP]
    > Voor snel herstel moet u enkele herstelpunten voor de korte termijn op uw schijf bewaren. Deze herstelpunten worden gebruikt voor operationeel herstel. Azure fungeert als een goede externe locatie, met hogere SLA's en gegarandeerde beschikbaarheid.
    >
    > Gebruik DPM om Azure Backups te plannen nadat de back-ups van de lokale schijf zijn gemaakt. Wanneer u deze oefening volgt, wordt de meest recente schijfback-up naar Azure gekopieerd.
    >

1. Kies de planning voor het retentiebeleid. Zie Use Azure Backup to replace your tape infrastructure (Uw tapeinfrastructuur vervangen) voor meer informatie over [hoe het retentiebeleid werkt.](backup-azure-backup-cloud-as-tape.md)

    ![Een retentiebeleid kiezen](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    In dit voorbeeld:

    * Er worden dagelijks om 12:00 uur en 20:00 uur back-ups gemaakt. Ze worden 180 dagen bewaard.
    * De back-up op zaterdag om 12:00 uur wordt 104 weken bewaard.
    * De back-up van de laatste zaterdag van de maand om 12:00 uur wordt 60 maanden bewaard.
    * De back-up van de laatste zaterdag van maart om 12:00 uur wordt tien jaar bewaard.

    Nadat u een bewaarbeleid hebt gekozen, selecteert u **Volgende.**

1. Kies hoe u de eerste back-up naar Azure wilt overdragen.

    * De **optie Automatisch via het netwerk volgt** uw back-upschema om de gegevens over te dragen naar Azure.
    * Zie Overview of Offline Backup (Overzicht van offline back-up) voor meer informatie over **offline** [back-ups.](offline-backup-overview.md)

    Nadat u een overdrachtsmechanisme hebt gekozen, selecteert u **Volgende.**

1. Controleer de **beleidsdetails** op de pagina Samenvatting. Selecteer vervolgens **Groep maken.** U kunt Sluiten **selecteren en** de voortgang van de taak bekijken in **de werkruimte** Bewaking.

    ![De voortgang van het maken van de beveiligingsgroep](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="create-on-demand-backup-copies-of-a-sql-server-database"></a>Back-ups op aanvraag maken van een SQL Server database

Er wordt een herstelpunt gemaakt wanneer de eerste back-up plaatsvindt. In plaats van te wachten tot de planning is uitgevoerd, kunt u het maken van een herstelpunt handmatig activeren:

1. Zorg ervoor dat in de beveiligingsgroep de databasestatus **OK is.**

    ![Een beveiligingsgroep met de databasestatus](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
1. Klik met de rechtermuisknop op de database en selecteer **herstelpunt maken.**

    ![Kies ervoor om een online herstelpunt te maken](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
1. Selecteer Onlinebeveiliging in de **vervolgkeuzelijst.** Selecteer vervolgens **OK om** te beginnen met het maken van een herstelpunt in Azure.

    ![Een herstelpunt maken in Azure](./media/backup-azure-backup-sql/sqlbackup-azure.png)
1. U kunt de voortgang van de taak bekijken in de **werkruimte** Bewaking.

    ![De voortgang van de taak weergeven in de bewakingsconsole](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Een database SQL Server azure herstellen

Een beveiligde entiteit, zoals een SQL Server, herstellen vanuit Azure:

1. Open de DPM-serverbeheerconsole. Ga naar de **werkruimte Herstel** om de servers te zien van wie DPM een back-up heeft gemaakt. Selecteer de database (in dit voorbeeld ReportServer$MSDPM2012). Selecteer een **hersteltijd** die eindigt op **Online.**

    ![Een herstelpunt selecteren](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
1. Klik met de rechtermuisknop op de databasenaam en selecteer **Herstellen.**

    ![Een database herstellen vanuit Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
1. DPM toont de details van het herstelpunt. Selecteer **Next**. Als u de database wilt overschrijven, selecteert u het hersteltype Herstellen naar het oorspronkelijke **exemplaar van SQL Server**. Selecteer vervolgens **Volgende**.

    ![Een database herstellen naar de oorspronkelijke locatie](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    In dit voorbeeld kan de database met DPM worden hersteld naar een andere SQL Server of naar een zelfstandige netwerkmap.
1. Op de **pagina Herstelopties** opgeven kunt u de herstelopties selecteren. U kunt bijvoorbeeld Beperking van **netwerkbandbreedtegebruik** kiezen om de bandbreedte te beperken die door herstel wordt gebruikt. Selecteer vervolgens **Volgende**.
1. Op de **pagina** Samenvatting ziet u de huidige herstelconfiguratie. Selecteer **Herstellen.**

    De herstelstatus geeft aan dat de database wordt hersteld. U kunt Sluiten **selecteren om** de wizard te sluiten en de voortgang te bekijken in de **werkruimte** Bewaking.

    ![Het herstelproces starten](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Wanneer het herstel is voltooid, is de herstelde database consistent met de toepassing.

## <a name="next-steps"></a>Volgende stappen

Zie veelgestelde vragen Azure Backup [meer informatie.](backup-azure-backup-faq.yml)