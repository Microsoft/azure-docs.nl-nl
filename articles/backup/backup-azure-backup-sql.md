---
title: Back-ups maken van SQL Server naar Azure als een DPM-workload
description: Een inleiding tot het maken van back-ups van SQL Server-data bases met behulp van de Azure Backup-Service
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 592a51051a0d02a6c1d491db0fe559e2e62babb2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96327046"
---
# <a name="back-up-sql-server-to-azure-as-a-dpm-workload"></a>Back-ups maken van SQL Server naar Azure als een DPM-workload

Dit artikel leidt u door de configuratie stappen voor het maken van back-ups van SQL Server-data bases met behulp van Azure Backup.

Als u back-ups wilt maken van SQL Server-data bases naar Azure, hebt u een Azure-account nodig. Als u er nog geen hebt, kunt u in slechts een paar minuten een gratis account maken. Zie [uw gratis Azure-account maken](https://azure.microsoft.com/pricing/free-trial/)voor meer informatie.

Een back-up maken van een SQL Server Data Base naar Azure en deze herstellen vanuit Azure:

1. Maak een back-upbeleid om SQL Server-data bases in azure te beveiligen.
1. Maak back-ups op aanvraag in Azure.
1. De data base herstellen vanuit Azure.

>[!NOTE]
>DPM 2019 UR2 ondersteunt SQL Server failover cluster instances (FCI) met behulp van gedeelde cluster volumes (CSV).<br><br>
>De beveiliging van [SQL Server failover-cluster exemplaar met opslagruimten direct op Azure](../azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure.md)  en [SQL Server failovercluster met gedeelde schijven van Azure](../azure-sql/virtual-machines/windows/failover-cluster-instance-azure-shared-disks-manually-configure.md) wordt ondersteund met deze functie. De DPM-server moet worden geïmplementeerd in de virtuele machine van Azure om het SQL FCI-exemplaar te beveiligen dat is geïmplementeerd op virtuele machines van Azure. 

## <a name="prerequisites-and-limitations"></a>Vereisten en beperkingen

* Als u een database met bestanden op een externe bestandsshare hebt, mislukt de beveiliging met fout-id 104. DPM biedt geen ondersteuning voor de beveiliging van SQL Server gegevens op een externe bestands share.
* DPM kan geen data bases beveiligen die zijn opgeslagen op externe SMB-shares.
* Zorg ervoor dat de replica's van de [beschikbaarheids groep zijn geconfigureerd als alleen-lezen](/sql/database-engine/availability-groups/windows/configure-read-only-access-on-an-availability-replica-sql-server).
* U moet de systeem account **NTAuthority\System** expliciet toevoegen aan de groep Sysadmin op SQL Server.
* Wanneer u een herstel bewerking op een andere locatie uitvoert voor een gedeeltelijk Inge sloten data base, moet u ervoor zorgen dat de functie [Inge sloten data bases](/sql/relational-databases/databases/migrate-to-a-partially-contained-database#enable) is ingeschakeld voor het SQL-doel exemplaar.
* Wanneer u een herstel bewerking op een andere locatie uitvoert voor een bestands stroom database, moet u ervoor zorgen dat de functie [Bestands stroom database](/sql/relational-databases/blob/enable-and-configure-filestream) is ingeschakeld voor het SQL-doel exemplaar.
* Beveiliging voor SQL Server AlwaysOn:
  * DPM detecteert beschikbaarheidsgroepen bij het uitvoeren van een query bij het maken van beveiligingsgroepen.
  * DPM detecteert een failover en zet de beveiliging van de database voort.
  * DPM ondersteunt configuraties van clusters op meerdere locaties voor een exemplaar van SQL Server.
* Wanneer u databases beveiligt die het kenmerk AlwaysOn gebruiken, heeft DPM de volgende beperkingen:
  * DPM voldoet aan het back-upbeleid voor beschikbaarheids groepen die zijn ingesteld in SQL Server op basis van de voor keuren voor back-ups, als volgt:
    * Voorkeur voor secundaire: back-ups moeten op een secundaire replica plaatsvinden, behalve wanneer de primaire replica de enige replica online is. Als er meerdere secundaire replica's beschikbaar zijn, wordt het knoop punt met de hoogste back-upprioriteit geselecteerd voor back-up. Als alleen de primaire replica beschikbaar is, moet de back-up op de primaire replica worden uitgevoerd.
    * Alleen secundaire: back-up mag niet op de primaire replica worden uitgevoerd. Als de primaire replica de enige online replica is, mag de back-up niet plaatsvinden.
    * Primaire: back-ups moeten altijd op de primaire replica plaatsvinden.
    * Iedere replica: back-ups kunnen op alle beschikbare replica's in de beschikbaarheidsgroep plaatsvinden. Het knooppunt waarvan een back-up moet worden gemaakt, zal gebaseerd zijn op de back-upprioriteiten voor elk van de knooppunten.
  * Houd rekening met het volgende:
    * U kunt back-ups maken van elke Lees bare replica, dat wil zeggen primair, synchroon secundair, asynchroon secundair.
    * Als een replica wordt uitgesloten van de back-up, bijvoorbeeld als **replica uitsluiten** is ingeschakeld of als niet leesbaar is gemarkeerd, wordt die replica niet geselecteerd voor back-up onder een van de opties.
    * Als er meerdere replica's beschikbaar en leesbaar zijn, wordt het knoop punt met de hoogste back-upprioriteit geselecteerd voor back-up.
    * Als de back-up mislukt op het geselecteerde knoop punt, mislukt de back-upbewerking.
    * Herstel naar de oorspronkelijke locatie wordt niet ondersteund.
* Back-upproblemen SQL Server 2014 of hoger:
  * SQL Server 2014 heeft een nieuwe functie toegevoegd voor het maken van een [Data Base voor on-premises SQL Server in Windows Azure Blob-opslag](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure). DPM kan niet worden gebruikt om deze configuratie te beveiligen.
  * Er zijn enkele bekende problemen met de voor keur voor secundaire back-upvoorkeur voor de SQL AlwaysOn-optie. DPM maakt altijd een back-up van secundair. Als er geen secundair kan worden gevonden, mislukt de back-up.

## <a name="before-you-start"></a>Voordat u begint

Voordat u begint, moet u ervoor zorgen dat u voldoet aan de [vereisten](backup-azure-dpm-introduction.md#prerequisites-and-limitations) voor het gebruik van Azure backup voor het beveiligen van werk belastingen. Hier volgen enkele van de vereiste taken:

* Maak een back-upkluis.
* Kluis referenties downloaden.
* Installeer de Azure Backup-Agent.
* Registreer de server bij de kluis.

## <a name="create-a-backup-policy"></a>Maak een back-upbeleid

Als u SQL Server-data bases in azure wilt beveiligen, moet u eerst een back-upbeleid maken:

1. Selecteer de werk ruimte **beveiliging** op de Data Protection Manager-server (DPM).
1. Selecteer **Nieuw** om een beveiligings groep te maken.

    ![Een beveiligingsgroep maken](./media/backup-azure-backup-sql/protection-group.png)
1. Op de start pagina raadpleegt u de richt lijnen over het maken van een beveiligings groep. Selecteer vervolgens **Volgende**.
1. Selecteer **servers**.

    ![Het type server beveiligings groep selecteren](./media/backup-azure-backup-sql/pg-servers.png)
1. Vouw de SQL Server virtuele machine uit waarvan de data bases waarvan u een back-up wilt maken, zich bevinden. U ziet de gegevens bronnen waarvan een back-up van die server kan worden gemaakt. Vouw **alle SQL-shares** uit en selecteer vervolgens de data bases waarvan u een back-up wilt maken. In dit voor beeld selecteren we Report Server $ MSDPM2012 en Report Server $ MSDPM2012TempDB. Selecteer vervolgens **Volgende**.

    ![Een SQL Server-Data Base selecteren](./media/backup-azure-backup-sql/pg-databases.png)
1. Geef de beveiligings groep de naam en selecteer **Ik wil online beveiliging**.

    ![Een methode voor gegevens beveiliging kiezen-beveiliging op korte termijn schijf of online Azure-beveiliging](./media/backup-azure-backup-sql/pg-name.png)
1. Voeg op de pagina **Short-Term doelstellingen opgeven** de benodigde invoer toe voor het maken van back-uppunten op de schijf.

    In dit voor beeld is de **Bewaar** termijn ingesteld op *5 dagen*. De frequentie van de back- **upsynchronisatie** wordt ingesteld op elke *15 minuten*. **Snelle volledige back-up** is ingesteld op *8:00 pm*.

    ![Doelen voor de korte termijn instellen voor back-upbeveiliging](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > In dit voor beeld wordt een back-uppunt gemaakt om 8:00 uur elke dag. De gegevens die zijn gewijzigd sinds het back-uppunt van de vorige dag 8:00 PM worden overgedragen. Dit proces heet **snelle volledige back-up**. Hoewel de transactie Logboeken om de 15 minuten worden gesynchroniseerd, als we de Data Base op 9:00 uur moeten herstellen, wordt het punt gemaakt door de logboeken opnieuw af te spelen vanaf het laatste snelle volledige back-uppunt, dat 8:00 PM in dit voor beeld is.
   >
   >

1. Selecteer **Next**. DPM toont de totale beschik bare opslag ruimte. Ook wordt het mogelijke schijfruimte gebruik weer gegeven.

    ![Schijf toewijzing instellen](./media/backup-azure-backup-sql/pg-storage.png)

    DPM maakt standaard één volume per gegevens bron (SQL Server-Data Base). Het volume wordt gebruikt voor de eerste back-upkopie. In deze configuratie beperkt LDM (Logical Disk Manager) DPM-beveiliging tot 300 gegevens bronnen (SQL Server-data bases). Als u deze beperking wilt omzeilen, selecteert u **gegevens in de DPM-opslag groep samen zoeken**. Als u deze optie gebruikt, gebruikt DPM één volume voor meerdere gegevens bronnen. Met deze instelling kan DPM Maxi maal 2.000 SQL Server data bases beveiligen.

    Als u **de volumes automatisch verg root**, kunt u met DPM het verhoogde back-upvolume uitbreiden naarmate de productie gegevens groeien. Als u **de volumes automatisch verg Roten** selecteert, wordt de back-upopslag door DPM beperkt tot de gegevens bronnen in de beveiligings groep.

1. Als u een beheerder bent, kunt u ervoor kiezen om de eerste back-up **automatisch over het netwerk** over te dragen en de overdrachts tijd te kiezen. Of kies ervoor om de back-up **hand matig** te verplaatsen. Selecteer vervolgens **Volgende**.

    ![Een methode voor het maken van replica's kiezen](./media/backup-azure-backup-sql/pg-manual.png)

    De eerste back-up vereist de overdracht van de volledige gegevens bron (SQL Server-Data Base). De back-upgegevens worden verplaatst van de productie server (SQL Server computer) naar de DPM-server. Als deze back-up groot is, kan het overdragen van de gegevens via het netwerk leiden tot bandbreedte congestie. Daarom kunnen beheerders ervoor kiezen om Verwissel bare media te gebruiken om de eerste back-up **hand matig** over te dragen. Het is ook mogelijk dat de gegevens **automatisch via het netwerk** worden overgedragen op een opgegeven tijdstip.

    Nadat de eerste back-up is voltooid, worden de back-ups incrementeel door lopen op de eerste back-upkopie. Incrementele back-ups zijn vaak klein en kunnen eenvoudig via het netwerk worden overgedragen.

1. Kies wanneer u een consistentie controle wilt uitvoeren. Selecteer vervolgens **Volgende**.

    ![Kies wanneer u een consistentie controle wilt uitvoeren](./media/backup-azure-backup-sql/pg-consistent.png)

    DPM kan een consistentie controle uitvoeren op de integriteit van het back-uppunt. Het berekent de controlesom van het back-upbestand op de productie server (de SQL Server computer in dit voor beeld) en de gegevens van de back-up van dat bestand in DPM. Als bij de controle een conflict wordt gevonden, wordt ervan uitgegaan dat het back-upbestand in DPM beschadigd is. DPM corrigeert de back-upgegevens door de blokken te verzenden die overeenkomen met de controlesom komen niet overeen. Omdat de consistentie controle een prestatie-intensieve bewerking is, kunnen beheerders ervoor kiezen de consistentie controle te plannen of deze automatisch uit te voeren.

1. Selecteer de gegevens bronnen die u wilt beveiligen in Azure. Selecteer vervolgens **Volgende**.

    ![Gegevens bronnen selecteren die u wilt beveiligen in azure](./media/backup-azure-backup-sql/pg-sqldatabases.png)
1. Als u een beheerder bent, kunt u back-upscheman en bewaar beleid kiezen die passen bij het beleid van uw organisatie.

    ![Schema's en bewaar beleid kiezen](./media/backup-azure-backup-sql/pg-schedule.png)

    In dit voor beeld worden de back-ups dagelijks uitgevoerd om 12:00 uur en 8:00 PM.

    > [!TIP]
    > Voor snelle herstel moet u een aantal herstel punten voor de korte termijn op uw schijf opslaan. Deze herstel punten worden gebruikt voor operationeel herstel. Azure fungeert als een goede externe locatie, waardoor er meer Sla's en gegarandeerde Beschik baarheid zijn.
    >
    > Gebruik DPM om Azure-back-ups te plannen nadat de back-ups van de lokale schijf zijn voltooid. Wanneer u deze procedure volgt, wordt de laatste schijf back-up naar Azure gekopieerd.
    >

1. Kies het schema voor het Bewaar beleid. Zie [Azure Backup gebruiken om uw tape infrastructuur te vervangen](backup-azure-backup-cloud-as-tape.md)voor meer informatie over de werking van het Bewaar beleid.

    ![Een Bewaar beleid kiezen](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    In dit voorbeeld:

    * Back-ups worden dagelijks uitgevoerd om 12:00 uur en 8:00 PM. Ze worden gedurende 180 dagen bewaard.
    * De back-up op zaterdag om 12:00 uur wordt gedurende 104 weken bewaard.
    * De back-up van de laatste zaterdag van de maand om 12:00 uur wordt gedurende 60 maanden bewaard.
    * De back-up van de laatste zaterdag van maart om 12:00 uur wordt gedurende tien jaar bewaard.

    Nadat u een Bewaar beleid hebt gekozen, selecteert u **volgende**.

1. Kies hoe u de eerste back-up naar Azure wilt overdragen.

    * De optie **automatisch via het netwerk** wordt gevolgd door uw back-upschema om de gegevens over te dragen naar Azure.
    * Zie [overzicht van offline back-ups](offline-backup-overview.md)voor meer informatie over **offline back-ups**.

    Nadat u een overdrachts mechanisme hebt gekozen, selecteert u **volgende**.

1. Controleer op de pagina **samen vatting** de details van het beleid. Selecteer vervolgens **groep maken**. U kunt **sluiten** selecteren en de voortgang van de taak bekijken in de werk ruimte **bewaking** .

    ![De voortgang van het maken van de beveiligings groep](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="create-on-demand-backup-copies-of-a-sql-server-database"></a>Back-ups op aanvraag maken van een SQL Server Data Base

Er wordt een herstel punt gemaakt wanneer de eerste back-up wordt uitgevoerd. In plaats van te wachten tot het schema wordt uitgevoerd, kunt u hand matig het maken van een herstel punt activeren:

1. Controleer in de beveiligings groep of de database status **OK** is.

    ![Een beveiligings groep, met de database status](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
1. Klik met de rechter muisknop op de data base en selecteer **herstel punt maken**.

    ![Kiezen voor het maken van een online herstel punt](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
1. Selecteer **online beveiliging** in de vervolg keuzelijst. Selecteer vervolgens **OK** om het maken van een herstel punt in azure te starten.

    ![Beginnen met het maken van een herstel punt in azure](./media/backup-azure-backup-sql/sqlbackup-azure.png)
1. U kunt de voortgang van de taak weer geven in de werk ruimte **bewaking** .

    ![Taak voortgang weer geven in de bewakings console](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Een SQL Server-Data Base herstellen vanuit Azure

Een beveiligde entiteit, zoals een SQL Server Data Base, herstellen vanuit Azure:

1. Open de beheer console van DPM-server. Ga naar de werk ruimte **herstel** om de servers te zien waarvan DPM een back-up maakt. Selecteer de data base (in dit voor beeld Report Server $ MSDPM2012). Selecteer een **herstel tijd** die eindigt op **online**.

    ![Een herstelpunt selecteren](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
1. Klik met de rechter muisknop op de naam van de data base en selecteer **herstellen**.

    ![Een Data Base herstellen vanuit Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
1. DPM toont de details van het herstel punt. Selecteer **Next**. Als u de Data Base wilt overschrijven, selecteert u het herstel type **herstellen naar het oorspronkelijke exemplaar van SQL Server**. Selecteer vervolgens **Volgende**.

    ![Een Data Base op de oorspronkelijke locatie herstellen](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    In dit voor beeld kan DPM de data base herstellen naar een andere SQL Server exemplaar of naar een zelfstandige netwerkmap.
1. Op de pagina **herstel opties opgeven** kunt u de herstel opties selecteren. U kunt bijvoorbeeld **beperking van netwerk bandbreedte gebruik** kiezen om de band breedte te beperken die door het herstel wordt gebruikt. Selecteer vervolgens **Volgende**.
1. Op de pagina **samen vatting** ziet u de huidige herstel configuratie. Selecteer **herstellen**.

    De herstel status toont de data base die wordt hersteld. U kunt **sluiten** selecteren om de wizard te sluiten en de voortgang in de werk ruimte **bewaking** weer te geven.

    ![Het herstel proces starten](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Wanneer het herstel is voltooid, is de herstelde data base consistent met de toepassing.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure backup Veelgestelde vragen](backup-azure-backup-faq.md)voor meer informatie.