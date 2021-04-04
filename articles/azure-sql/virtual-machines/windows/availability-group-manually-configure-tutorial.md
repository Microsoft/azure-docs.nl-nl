---
title: 'Zelfstudie: SQL Server AlwaysOn-beschikbaarheidsgroep configureren'
description: Deze zelfstudie laat zien hoe u een SQL Server AlwaysOn-beschikbaarheidsgroep maakt in Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2018
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: feab48f32396bcc89621433930c9a9f4689d8286
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97355440"
---
# <a name="tutorial-manually-configure-an-availability-group-sql-server-on-azure-vms"></a>Zelfstudie: Een beschikbaarheidsgroep handmatig configureren (SQL Server op Azure-VM's)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Deze zelfstudie laat zien hoe u een AlwaysOn-beschikbaarheidsgroep voor SQL Server maakt in Azure Virtual Machines (VM's). Met de volledige zelfstudie wordt een beschikbaarheidsgroep met een databasereplica in twee SQL-servers gemaakt.

Hoewel in dit artikel handmatig de omgeving van de beschikbaarheidsgroep wordt geconfigureerd, is het ook mogelijk om dit te doen met behulp van de [Azure Portal](availability-group-azure-portal-configure.md), [PowerShell of de Azure CLI](availability-group-az-commandline-configure.md) of [Azure-quickstart-sjablonen](availability-group-quickstart-template-configure.md). 


**Geschatte tijd**: Het duurt ongeveer dertig minuten om de zelfstudie te voltooien zodra aan de [vereisten](availability-group-manually-configure-prerequisites-tutorial.md) is voldaan.


## <a name="prerequisites"></a>Vereisten

In de zelfstudie wordt ervan uitgegaan dat u enige basiskennis hebt van SQL Server AlwaysOn-beschikbaarheidsgroepen. Zie [Overview of Always On Availability Groups (SQL Server)](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) (Overzicht van AlwaysOn-beschikbaarheidsgroepen (SQL Server)) als u meer informatie nodig hebt.

Voordat u met de zelfstudie begint, moet u [de vereisten voor het maken van AlwaysOn-beschikbaarheidsgroepen in Azure Virtual Machines voltooien](availability-group-manually-configure-prerequisites-tutorial.md). Als deze vereisten al zijn voltooid, kunt u doorgaan naar [Cluster maken](#CreateCluster).

De volgende tabel bevat de vereisten die u moet voltooien voordat u met deze zelfstudie begint:

| Vereiste |Beschrijving |
|----- |----- |----- |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Twee SQL Server-exemplaren** | - In een Azure-beschikbaarheidsset <br/> - In één domein <br/> - Met de functie failoverclustering geïnstalleerd |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Windows Server** | Bestandsshare voor cluster-witness |  
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **SQL Server-serviceaccount** | Domeinaccount |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **SQL Server Agent-serviceaccount** | Domeinaccount |  
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Firewallpoorten geopend** | - SQL Server: **1433** voor standaardexemplaar <br/> - Eindpunt van databasespiegeling: **5022** of een beschikbare poort <br/> - Statustest voor IP-adres van load balancer voor beschikbaarheidsgroepen: **59999** of een beschikbare poort <br/> - Statustest voor IP-adres van load balancer voor clusterkernen: **58888** of een beschikbare poort |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Failoverclusterfunctie toevoegen** | Voor beide SQL Server-exemplaren is deze functie vereist |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Account van installatiedomein** | - Lokale beheerder op elke SQL-server <br/> - Lid van de vaste SQL Server-serverrol 'sysadmin' voor elk exemplaar van SQL Server  |

>[!NOTE]
> Een groot aantal stappen in deze zelfstudie kunnen nu worden geautomatiseerd met de [Azure Portal](availability-group-azure-portal-configure.md), [PowerShell en de Azure CLI](./availability-group-az-commandline-configure.md) en [Azure-quickstart-sjablonen](availability-group-quickstart-template-configure.md).


<!--**Procedure**: *This is the first "step". Make titles H2's and short and clear – H2's appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>

## <a name="create-the-cluster"></a>Het cluster maken

Nadat de vereisten zijn voltooid, gaat u in de eerste stap een Windows Server-failovercluster maken dat twee SQL-servers en een witness-server bevat.

1. Gebruik Remote Desktop Protocol (RDP) om verbinding te maken met de eerste SQL Server. Gebruik een domeinaccount dat een beheerder is voor de SQL-servers en de witness-server.

   >[!TIP]
   >Als u het [document met vereisten](availability-group-manually-configure-prerequisites-tutorial.md) hebt gevolgd, hebt u een account gemaakt met de naam **CORP\Install**. Gebruik dit account.

2. Selecteer in het dashboard **Serverbeheer** de optie **Hulpmiddelen** en vervolgens **Failoverclusterbeheer**.
3. Klik in het linkerdeel venster met de rechtermuisknop op **Failoverclusterbeheer** en selecteer **Cluster maken**.

   ![Cluster maken](./media/availability-group-manually-configure-tutorial/40-createcluster.png)

4. Maak in de wizard Cluster maken een cluster met één knooppunt door de pagina's met de instellingen in de volgende tabel te volgen:

   | Pagina | Instellingen |
   | --- | --- |
   | Voordat u begint |Standaardinstellingen gebruiken |
   | Servers selecteren |Typ de naam van de eerste SQL-server in **Servernaam invoeren** en selecteer **Toevoegen**. |
   | Validatiewaarschuwing |Selecteer **Nee, ik heb geen ondersteuning van Microsoft voor dit cluster nodig en wil daarom de validatietests niet uitvoeren. Selecteer Volgende om door te gaan met het maken van het cluster**. |
   | Toegangspunt voor beheer van het cluster |Typ in **Clusternaam** een clusternaam, bijvoorbeeld **SQLAGCluster1**.|
   | Bevestiging |Gebruik standaardinstellingen tenzij u opslagruimten gebruikt. Zie de opmerking na deze tabel. |

### <a name="set-the-windows-server-failover-cluster-ip-address"></a>Het IP-adres van het Windows Server-failovercluster instellen

  > [!NOTE]
  > In Windows Server 2019 wordt door het cluster een **naam voor de gedistribueerde server** in plaats van een **naam voor het clusternetwerk** gemaakt. Als u Windows Server 2019 gebruikt, slaat u de stappen over die verwijzen naar de naam van de clusterkern in deze zelfstudie. U kunt een naam voor een clusternetwerk maken met behulp van [PowerShell](failover-cluster-instance-storage-spaces-direct-manually-configure.md#create-failover-cluster). Bekijk de blog [Failover Cluster: Cluster Network Object](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97) (Failovercluster: clusternetwerkobject) voor meer informatie. 

1. Schuif in **Failoverclusterbeheer** omlaag naar **Clusterkernbronnen** en vouw de clustergegevens uit. Als het goed is, ziet u dat de bronnen **Naam** en **IP-adres** beide de status **Mislukt** hebben. De IP-adresbron kan niet online worden gebracht omdat het cluster hetzelfde IP-adres wordt toegewezen als de computer zelf. Daarom is het een dubbel adres.

2. Klik met de rechtermuisknop op de mislukte bron van het **IP-adres** en selecteer vervolgens **Eigenschappen**.

   ![Clustereigenschappen](./media/availability-group-manually-configure-tutorial/42_IPProperties.png)

3. Selecteer **Vast IP-adres** en geef een beschikbaar adres op uit hetzelfde subnet als dat voor uw virtuele machines.

4. Klik in de sectie **Clusterkernbronnen** met de rechtermuisknop op clusternaam en selecteer **Online brengen**. Wacht totdat beide bronnen online zijn. Wanneer de bron van de clusternaam online is, wordt de server voor de domeincontroller (DC) bijgewerkt met een nieuw Active Directory (AD)-computeraccount. Gebruik dit AD-account om de geclusterde service van de beschikbaarheidsgroep later uit te voeren.

### <a name="add-the-other-sql-server-to-cluster"></a><a name="addNode"></a>De andere SQL-server aan het cluster toevoegen

Voeg de andere SQL-server aan het cluster toe.

1. Klik in de browserstructuur met de rechtermuisknop op het cluster en selecteer **Knooppunt toevoegen**.

    ![Knooppunt aan het cluster toevoegen](./media/availability-group-manually-configure-tutorial/44-addnode.png)

1. Selecteer **Volgende** in de **wizard Knooppunt toevoegen**. Voeg op de pagina **Servers selecteren** de tweede SQL-server toe. Typ de servernaam in **Servernaam invoeren** en selecteer **Toevoegen**. Klik op **Volgende** als u klaar bent.

1. Selecteer op de pagina **Validatiewaarschuwing** de optie **Nee** (in een productiescenario moet u de validatietests uitvoeren). Selecteer vervolgens **Volgende**.

8. Als u opslagruimten gebruikt, schakelt u op de pagina **Bevestiging** het selectievakje **Voeg alle in aanmerking komende opslag aan het cluster toe** uit.

   ![Bevestiging van knooppunt toevoegen](./media/availability-group-manually-configure-tutorial/46-addnodeconfirmation.png)

   >[!WARNING]
   >Als u opslagruimten gebruikt en het selectievakje **Voeg alle in aanmerking komende opslag aan het cluster toe** niet uitschakelt, worden de virtuele schijven tijdens het clusterproces ontkoppelt. Als gevolg hiervan worden ze niet weergegeven in schijfbeheer of de verkenner totdat de opslagruimten met behulp van PowerShell uit het cluster worden verwijderd en opnieuw worden gekoppeld. Er worden meerdere schijven in opslaggroepen gegroepeerd. Zie [Opslagruimten](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11)) voor meer informatie.
   >

1. Selecteer **Next**.

1. Selecteer **Finish**.

   Failoverclusterbeheer geeft aan dat het cluster een nieuw knooppunt heeft. Het knooppunt wordt in de container **Knooppunten** vermeld.

10. Meld u af bij de sessie voor Extern bureaublad.

### <a name="add-a-cluster-quorum-file-share"></a>Een bestandsshare voor een clusterquorum toevoegen

In dit voorbeeld gebruikt het Windows-cluster een bestandsshare om een clusterquorum te maken. In deze zelfstudie wordt gebruikgemaakt van Quorum van het type Knooppunt- en bestandssharemeerderheid. Raadpleeg [Understanding Quorum Configurations in a Failover Cluster](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731739(v=ws.11)) (Inzicht in quorumconfiguraties in een failovercluster) voor meer informatie.

1. Maak in een sessie voor Extern bureaublad verbinding met de lidserver van de bestandssharewitness.

1. Selecteer in **Serverbeheer** de optie **Hulpmiddelen**. Open **Computerbeheer**.

1. Selecteer **Gedeelde mappen**.

1. Klik met de rechtermuisknop op **Shares** en selecteer **Nieuwe share...** .

   ![Klik met de rechtermuisknop op Shares en selecteer Nieuwe share](./media/availability-group-manually-configure-tutorial/48-newshare.png)

   Gebruik **Wizard Gedeelde map maken** om een share te maken.

1. Selecteer in **Mappad** de optie **Bladeren** en zoek of maak een pad voor de gedeelde map. Selecteer **Next**.

1. Verifieer in **Naam, beschrijving en instellingen** de naam en het pad van de share. Selecteer **Next**.

1. Stel in **Machtigingen voor gedeelde map** **Machtigingen aanpassen** in. Selecteer **Aangepast...** .

1. Selecteer in **Machtigingen aanpassen** de optie **Toevoegen...** .

1. Zorg ervoor dat het account dat is gebruikt voor het maken van het cluster, volledig beheer heeft.

   ![Zorg ervoor dat het account dat is gebruikt voor het maken van het cluster, volledig beheer heeft](./media/availability-group-manually-configure-tutorial/50-filesharepermissions.png)

1. Selecteer **OK**.

1. Selecteer in **Machtigingen voor gedeelde map** de optie **Voltooien**. Selecteer opnieuw **Voltooien**.  

1. Meld u af bij de server

### <a name="configure-the-cluster-quorum"></a>Het clusterquorum configureren

Stel vervolgens het clusterquorum in.

1. Maak in een sessie voor Extern bureaublad verbinding met het eerste clusterknooppunt.

1. Klik in **Failoverclusterbeheer** met de rechtermuisknop op het cluster, wijs **Meer acties** aan en selecteer **Instellingen van het clusterquorum configureren...** .

   ![Selecteer Instellingen van het clusterquorum configureren](./media/availability-group-manually-configure-tutorial/52-configurequorum.png)

1. Selecteer in **Wizard Clusterquorum configureren** de optie **Volgende**.

1. Kies in **Optie voor quorumconfiguratie selecteren** de optie **De quorumwitness selecteren** en vervolgens **Volgende**.

1. Selecteer in **De quorumwitness selecteren** de optie **Bestandssharewitness configureren**.

   >[!TIP]
   >Windows Server 2016 ondersteunt een cloudwitness. Als u dit type witness kiest, hebt u geen bestandssharewitness nodig. Zie [Deploy a cloud witness for a Failover Cluster](/windows-server/failover-clustering/deploy-cloud-witness) (Een cloudwitness voor een failovercluster implementeren) voor meer informatie. In deze zelfstudie wordt een bestandssharewitness gebruikt die wordt ondersteund door eerdere besturingssystemen.
   >

1. Typ in **Bestandssharewitness configureren** het pad voor de share die u hebt gemaakt. Selecteer **Next**.

1. Controleer de instellingen in **Bevestiging**. Selecteer **Next**.

1. Selecteer **Finish**.

De clusterkernbronnen worden geconfigureerd met een bestandssharewitness.

## <a name="enable-availability-groups"></a>Beschikbaarheidsgroepen inschakelen

Schakel vervolgens de functie **AlwaysOn-beschikbaarheidsgroepen** in. Voer deze stappen uit op beide SQL-servers.

1. Start in het scherm **Start** **SQL Server Configuration Manager**.
2. Selecteer in de browserstructuur **SQL Server-services**, klik met de rechtermuisknop op de service **SQL Server (MSSQLSERVER)** en selecteer **Eigenschappen**.
3. Selecteer het tabblad **Hoge beschikbaarheid met AlwaysOn** en selecteer vervolgens **AlwaysOn-beschikbaarheidsgroepen inschakelen**. Dit gaat als volgt:

    ![AlwaysOn-beschikbaarheidsgroepen inschakelen](./media/availability-group-manually-configure-tutorial/54-enableAlwaysOn.png)

4. Selecteer **Toepassen**. Selecteer **OK** in het pop-updialoogvenster.

5. Start de SQL Server-service opnieuw.

Herhaal deze stappen op de andere SQL-server.

<!-----------------
## <a name="endpoint-firewall"></a>Open firewall for the database mirroring endpoint

Each instance of SQL Server that participates in an availability group requires a database mirroring endpoint. This endpoint is a TCP port for the instance of SQL Server that is used to synchronize the database replicas in the availability groups on that instance.

On both SQL Servers, open the firewall for the TCP port for the database mirroring endpoint.

1. On the first SQL Server **Start** screen, launch **Windows Firewall with Advanced Security**.
2. In the left pane, select **Inbound Rules**. On the right pane, select **New Rule**.
3. For **Rule Type**, choose **Port**.
1. For the port, specify TCP and choose an unused TCP port number. For example, type *5022* and select **Next**.

   >[!NOTE]
   >For this example, we're using TCP port 5022. You can use any available port.

5. In the **Action** page, keep **Allow the connection** selected and select **Next**.
6. In the **Profile** page, accept the default settings and select **Next**.
7. In the **Name** page, specify a rule name, such as **Default Instance Mirroring Endpoint** in the **Name** text box, then select **Finish**.

Repeat these steps on the second SQL Server.
-------------------------->

## <a name="create-a-database-on-the-first-sql-server"></a>Een database maken op de eerste SQL-server

1. Start het RDP-bestand op de eerste SQL-server met een domeinaccount dat lid is van de vaste serverrol sysadmin.
1. Open SQL Server Management Studio en maak verbinding met de eerste SQL-server.
7. Klik in **Objectverkenner** met de rechtermuisknop op **Databases** en selecteer **Nieuwe database**.
8. Typ in **Databasenaam** **MyDB1** en selecteer **OK**.

### <a name="create-a-backup-share"></a><a name="backupshare"></a> Een back-upshare maken

1. Selecteer op de eerste SQL-server in **Serverbeheer** de optie **Hulpmiddelen**. Open **Computerbeheer**.

1. Selecteer **Gedeelde mappen**.

1. Klik met de rechtermuisknop op **Shares** en selecteer **Nieuwe share...** .

   ![Selecteer Nieuwe share](./media/availability-group-manually-configure-tutorial/48-newshare.png)

   Gebruik **Wizard Gedeelde map maken** om een share te maken.

1. Selecteer in **Mappad** de optie **Bladeren** en zoek of maak een pad voor de gedeelde map van de databaseback-up. Selecteer **Next**.

1. Verifieer in **Naam, beschrijving en instellingen** de naam en het pad van de share. Selecteer **Next**.

1. Stel in **Machtigingen voor gedeelde map** **Machtigingen aanpassen** in. Selecteer **Aangepast...** .

1. Selecteer in **Machtigingen aanpassen** de optie **Toevoegen...** .

1. Zorg ervoor dat de serviceaccounts van SQL Server en SQL Server Agent voor beide servers volledig beheer hebben.

   ![Zorg ervoor dat de serviceaccounts van SQL Server en SQL Server Agent voor beide servers volledig beheer hebben.](./media/availability-group-manually-configure-tutorial/68-backupsharepermission.png)

1. Selecteer **OK**.

1. Selecteer in **Machtigingen voor gedeelde map** de optie **Voltooien**. Selecteer opnieuw **Voltooien**.  

### <a name="take-a-full-backup-of-the-database"></a>Een volledige back-up van de database uitvoeren

U dient een back-up te maken van de nieuwe database om de logboekketen te initialiseren. Als u geen back-up van de nieuwe database maakt, kan deze niet worden opgenomen in een beschikbaarheidsgroep.

1. Klik in **Objectverkenner** met de rechtermuisknop op de database, wijs **Taken...** aan en selecteer **Een back-up maken**.

1. Selecteer **OK** om een volledige back-up te maken naar de standaardlocatie voor back-ups.

## <a name="create-the-availability-group"></a>De beschikbaarheidsgroep maken

U bent nu klaar om een beschikbaarheidsgroep te configureren met de volgende stappen:

* Maak een database op de eerste SQL-server.
* Maak een volledige back-up en een logboekback-up van de database.
* Zet de volledige en logboekback-ups terug naar de tweede SQL-server met de optie **NORECOVERY**.
* Maak de beschikbaarheidsgroep (**BG1**) met synchrone doorvoer, automatische failover en leesbare secundaire replica's.

### <a name="create-the-availability-group"></a>Maak de beschikbaarheidsgroep:

1. Ga in een sessie voor Extern bureaublad naar de eerste SQL-server. Klik in **Objectverkenner** in SSMS met de rechtermuisknop op **Hoge beschikbaarheid met AlwaysOn** en selecteer **Wizard Nieuwe beschikbaarheidsgroep**.

    ![Wizard Nieuwe beschikbaarheidsgroep starten](./media/availability-group-manually-configure-tutorial/56-newagwiz.png)

2. Selecteer op de pagina **Inleiding** de optie **Volgende**. Typ op de pagina **Naam beschikbaarheidsgroep opgeven** de naam voor de beschikbaarheidsgroep in **Naam van beschikbaarheidsgroep**. Bijvoorbeeld **BG1**. Selecteer **Next**.

    ![Wizard Nieuwe beschikbaarheidsgroep, Naam van beschikbaarheidsgroep opgeven](./media/availability-group-manually-configure-tutorial/58-newagname.png)

3. Selecteer op de pagina **Database selecteren** uw database en selecteer vervolgens **Volgende**.

   >[!NOTE]
   >De database voldoet aan de vereisten voor een beschikbaarheidsgroep, omdat u ten minste één volledige back-up hebt gemaakt op de beoogde primaire replica.
   >

   ![Wizard Nieuwe beschikbaarheidsgroep, Databases selecteren](./media/availability-group-manually-configure-tutorial/60-newagselectdatabase.png)

4. Selecteer op de pagina **Replica's opgeven** de optie **Replica toevoegen**.

   ![Wizard Nieuwe beschikbaarheidsgroep, Replica's opgeven](./media/availability-group-manually-configure-tutorial/62-newagaddreplica.png)

5. Het dialoogvenster **Verbinding maken met server** verschijnt. Typ de naam van de tweede server in **Servernaam**. Selecteer **Verbinden**.

   Terug op de pagina **Replica's opgeven** ziet u nu de tweede server vermeld staan in **Beschikbaarheidsreplica's**. Configureer de replica's als volgt.

   ![Wizard Nieuwe beschikbaarheidsgroep, Replica's opgeven (volledig)](./media/availability-group-manually-configure-tutorial/64-newagreplica.png)

6. Selecteer **Eindpunten** om het eindpunt van de databasespiegeling voor deze beschikbaarheidsgroep te zien. Gebruik dezelfde poort die u hebt gebruikt bij het instellen van de [firewallregel voor eindpunten van databasespiegeling](availability-group-manually-configure-prerequisites-tutorial.md#endpoint-firewall).

    ![Wizard Nieuwe beschikbaarheidsgroep, Initiële gegevenssynchronisatie selecteren](./media/availability-group-manually-configure-tutorial/66-endpoint.png)

8. Selecteer op de pagina **Initiële gegevenssynchronisatie selecteren** de optie **Volledig** en geef een gedeelde netwerklocatie op. Gebruik voor de locatie de [back-upshare die u hebt gemaakt](#backupshare). In het voorbeeld was het **\\\\<Eerste SQL-server\>\Back-up\\** . Selecteer **Next**.

   >[!NOTE]
   >Bij volledige synchronisatie wordt een volledige back-up van de database gemaakt op het eerste exemplaar van SQL Server en wordt deze teruggezet naar het tweede exemplaar. Voor grote databases wordt volledige synchronisatie niet aanbevolen, omdat het lange tijd kan duren. U kunt deze tijd verkorten door handmatig een back-up van de database te maken en deze te herstellen met `NO RECOVERY`. Als de database al met `NO RECOVERY` op de tweede SQL Server is hersteld voordat u de beschikbaarheidsgroep configureert, kiest u **Alleen samenvoegen**. Als u de back-up wilt maken nadat u de beschikbaarheidsgroep hebt geconfigureerd, kiest u **Initiële gegevenssynchronisatie overslaan**.
   >

   ![Kies Eerste gegevenssynchronisatie overslaan](./media/availability-group-manually-configure-tutorial/70-datasynchronization.png)

9. Selecteer op de pagina **Validatie** de optie **Volgende**. De pagina moet er ongeveer uitzien als op deze afbeelding:

    ![Wizard Nieuwe beschikbaarheidsgroep, Validatie](./media/availability-group-manually-configure-tutorial/72-validation.png)

    >[!NOTE]
    >Er is een waarschuwing voor de configuratie van de listener omdat u geen listener voor een beschikbaarheidsgroep hebt geconfigureerd. U kunt deze waarschuwing negeren omdat op virtuele machines van Azure de listener wordt gemaakt nadat de Azure-load balancer is gemaakt.

10. Selecteer op de pagina **Overzicht** de optie **Voltooien** en wacht totdat de wizard de nieuwe beschikbaarheidsgroep configureert. Op de pagina **Voortgang** kunt u **Meer informatie** selecteren om de details van de voortgang te bekijken. Nadat de wizard is voltooid, kijkt u op de pagina **Resultaten** om te controleren of de beschikbaarheidsgroep is gemaakt.

     ![Wizard Nieuwe beschikbaarheidsgroep, Resultaten](./media/availability-group-manually-configure-tutorial/74-results.png)

11. Selecteer **Sluiten** om de wizard af te sluiten.

### <a name="check-the-availability-group"></a>De beschikbaarheidsgroep controleren

1. In **Objectverkenner** vouwt u achtereenvolgens **Hoge beschikbaarheid met AlwaysOn** en **Beschikbaarheidsgroepen** uit. Nu ziet u de nieuwe beschikbaarheidsgroep in deze container. Klik met de rechtermuisknop op de beschikbaarheidsgroep en selecteer **Dashboard weergeven**.

   ![Dashboard beschikbaarheidsgroep weergeven](./media/availability-group-manually-configure-tutorial/76-showdashboard.png)

   Uw **AlwaysOn-dashboard** moet er ongeveer uitzien als in de volgende schermafbeelding:

   ![Dashboard beschikbaarheidsgroep](./media/availability-group-manually-configure-tutorial/78-agdashboard.png)

   U kunt de replica's, de failovermodus van elke replica en de synchronisatiestatus zien.

2. Selecteer uw cluster in **Failoverclusterbeheer**. Selecteer **Rollen**. De naam van de beschikbaarheidsgroep die u hebt gebruikt, is een rol op het cluster. Deze beschikbaarheidsgroep heeft geen IP-adres voor clientverbindingen omdat u geen listener hebt geconfigureerd. U gaat de listener configureren nadat u een Azure-load balancer hebt gemaakt.

   ![Beschikbaarheidsgroep in Failoverclusterbeheer](./media/availability-group-manually-configure-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Voer geen failover uit voor de beschikbaarheidsgroep vanuit Failoverclusterbeheer. Alle failoverbewerkingen moeten vanuit het **AlwaysOn-dashboard** in SSMS worden uitgevoerd. Zie [Restrictions on Using The Failover Cluster Manager with Availability Groups](/sql/database-engine/availability-groups/windows/failover-clustering-and-always-on-availability-groups-sql-server) (Beperkingen voor het gebruik van Failoverclusterbeheer met beschikbaarheidsgroepen) voor meer informatie.
    >

Op dit moment hebt u een beschikbaarheidsgroep met replica's op twee exemplaren van SQL Server. U kunt de beschikbaarheidsgroep tussen exemplaren verplaatsen. U kunt nog geen verbinding maken met de beschikbaarheidsgroep omdat u geen listener hebt. In virtuele machines van Azure is een load balancer vereist voor de listener. In de volgende stap gaat u de load balancer in Azure maken.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Een Azure-load balancer maken

[!INCLUDE [sql-ag-use-dnn-listener](../../includes/sql-ag-use-dnn-listener.md)]

In virtuele machines van Azure is voor een SQL Server-beschikbaarheidsgroep een load balancer vereist. De load balancer bevat de IP-adressen voor de listeners voor de beschikbaarheidsgroep en het Windows Server-failovercluster. In deze sectie wordt een overzicht gegeven van de manier waarop de load balancer in Azure Portal wordt gemaakt.

Een load balancer in Azure kan een Standard Load Balancer of een Basic Load Balancer zijn. De Standard Load Balancer heeft meer functies dan de Basic Load Balancer. Voor een beschikbaarheidsgroep is de Standard Load Balancer vereist als u een beschikbaarheidszone gebruikt (in plaats van een beschikbaarheidsset). Zie [Vergelijking tussen Load Balancer-SKU's](../../../load-balancer/skus.md) voor meer informatie over het verschil tussen de Load Balancer-SKU's.

1. Ga in Azure Portal naar de resourcegroep met uw SQL-servers en selecteer **+Toevoegen**.
1. Zoek naar **Load Balancer**. Kies de load balancer die door Microsoft is gepubliceerd.

   ![Kies de load balancer die door Microsoft is gepubliceerd](./media/availability-group-manually-configure-tutorial/82-azureloadbalancer.png)

1. Selecteer **Maken**.
1. Configureer de volgende parameters voor de load balancer.

   | Instelling | Veld |
   | --- | --- |
   | **Naam** |Gebruik een tekstnaam voor de load balancer, bijvoorbeeld **sqlLB**. |
   | **Type** |Intern |
   | **Virtueel netwerk** |Gebruik de naam van het virtuele Azure-netwerk. |
   | **Subnet** |Gebruik de naam van het subnet waarin de virtuele machine zich bevindt.  |
   | **IP-adrestoewijzing** |Statisch |
   | **IP-adres** |Gebruik een beschikbaar adres van het subnet. Gebruik dit adres voor de listener voor uw beschikbaarheidsgroep. Dit adres kan verschillen van het IP-adres van uw cluster.  |
   | **Abonnement** |Gebruik hetzelfde abonnement als dat voor de virtuele machine. |
   | **Locatie** |Gebruik dezelfde locatie als die voor de virtuele machine. |

   De blade in Azure Portal moet er als volgt uitzien:

   ![Load balancer maken](./media/availability-group-manually-configure-tutorial/84-createloadbalancer.png)

1. Selecteer **Maken** om de load balancer te maken.

Als u de load balancer wilt configureren, moet u een back-endpool maken en een statustest uitvoeren, en de taakverdelingsregels instellen. Doe dit in Azure Portal.

### <a name="add-a-backend-pool-for-the-availability-group-listener"></a>Een back-endpool toevoegen voor de listener voor de beschikbaarheidsgroep

1. Ga in Azure Portal naar uw beschikbaarheidsgroep. Mogelijk moet u de weergave vernieuwen om de zojuist gemaakte load balancer weer te geven.

   ![Load Balancer in resourcegroep zoeken](./media/availability-group-manually-configure-tutorial/86-findloadbalancer.png)

1. Selecteer de load balancer, selecteer **Back-endpools** en selecteer **+Toevoegen**.

1. Voer een naam voor de back-endpool in.

1. Koppel de back-endpool met de beschikbaarheidsset die de virtuele machines bevat.

1. Controleer onder **IP-configuraties van doelnetwerk** de optie **VIRTUELE MACHINE** en kies beide virtuele machines die als host moeten fungeren voor de replica's van de beschikbaarheidsgroep. Neem de bestandssharewitness-server niet op.

   >[!NOTE]
   >Als beide virtuele machines niet zijn opgegeven, kunnen er alleen verbindingen worden gemaakt met de primaire replica.

1. Selecteer **OK** om de back-endpool te maken.

### <a name="set-the-probe"></a>De statustest instellen

1. Selecteer de load balancer, kies **Statustesten** en selecteer vervolgens **+Toevoegen**.

1. Stel de statustest voor de listener als volgt in:

   | Instelling | Beschrijving | Voorbeeld
   | --- | --- |---
   | **Naam** | Tekst | SQLAlwaysOnEndPointProbe |
   | **Protocol** | Kies TCP | TCP |
   | **Poort** | Een niet-gebruikte poort | 59999 |
   | **Interval**  | De hoeveelheid tijd tussen de testpogingen in seconden |5 |
   | **Drempelwaarde voor beschadigd** | Het aantal opeenvolgende mislukte tests dat zich moet voordoen opdat de status van een virtuele machine als onjuist kan worden beschouwd  | 2 |

1. Selecteer **OK** om de statustest in te stellen.

### <a name="set-the-load-balancing-rules"></a>Taakverdelingsregels instellen

1. Selecteer de load balancer, kies **Taakverdelingsregels** en selecteer **+Toevoegen**.

1. Stel de taakverdelingsregels voor de listener als volgt in.

   | Instelling | Beschrijving | Voorbeeld
   | --- | --- |---
   | **Naam** | Tekst | SQLAlwaysOnEndPointListener |
   | **Front-end-IP-adres** | Kies een adres |Gebruik het adres dat u hebt gemaakt bij het maken van de load balancer. |
   | **Protocol** | Kies TCP |TCP |
   | **Poort** | Gebruik de poort voor de listener voor de beschikbaarheidsgroep | 1433 |
   | **Poort back-end** | Dit veld wordt niet gebruikt wanneer er een zwevend IP-adres voor Direct Server Return is ingesteld | 1433 |
   | **Test** |De naam die u voor de test hebt opgegeven | SQLAlwaysOnEndPointProbe |
   | **Sessiepersistentie** | Vervolgkeuzelijst | **Geen** |
   | **Time-out voor inactiviteit** | Het aantal minuten om een TCP-verbinding open te houden | 4 |
   | **Zwevend IP (Direct Server Return)** | |Ingeschakeld |

   > [!WARNING]
   > Direct Server Return wordt tijdens het maken ingesteld. De naam kan niet worden gewijzigd.
   >

1. Selecteer **OK** om de taakverdelingsregels voor de listener in te stellen.

### <a name="add-the-cluster-core-ip-address-for-the-windows-server-failover-cluster-wsfc"></a>Het IP-adres van de clusterkern toevoegen voor het Windows Server-failovercluster (WSFC)

Het IP-adres van de WSFC moet ook op de load balancer aanwezig zijn.

1. Ga in Azure Portal naar dezelfde Azure-load balancer. Selecteer **Front-end-IP-configuratie** en selecteer **+Toevoegen**. Gebruik het IP-adres dat u voor de WSFC in de clusterkernbronnen hebt geconfigureerd. Stel het IP-adres in als statisch.

1. Selecteer op de load balancer de optie **Statustesten** en selecteer vervolgens **+Toevoegen**.

1. Stel de statustest voor het IP-adres van de WSFC-clusterkern als volgt in:

   | Instelling | Beschrijving | Voorbeeld
   | --- | --- |---
   | **Naam** | Tekst | WSFCEndPointProbe |
   | **Protocol** | Kies TCP | TCP |
   | **Poort** | Een niet-gebruikte poort | 58888 |
   | **Interval**  | De hoeveelheid tijd tussen de testpogingen in seconden |5 |
   | **Drempelwaarde voor beschadigd** | Het aantal opeenvolgende mislukte tests dat zich moet voordoen opdat de status van een virtuele machine als onjuist kan worden beschouwd  | 2 |

1. Selecteer **OK** om de statustest in te stellen.

1. Stel de taakverdelingsregels in. Selecteer **Taakverdelingsregels** en vervolgens **+Toevoegen**.

1. Stel het de taakverdelingsregels voor het IP-adres van de clusterkern als volgt in.

   | Instelling | Beschrijving | Voorbeeld
   | --- | --- |---
   | **Naam** | Tekst | WSFCEndPoint |
   | **Front-end-IP-adres** | Kies een adres |Gebruik het adres dat u hebt gemaakt bij het configureren van het IP-adres van de WSFC. Dit wijkt af van het IP-adres van de listener |
   | **Protocol** | Kies TCP |TCP |
   | **Poort** | Gebruik de poort voor het IP-adres van het cluster. Dit is een beschikbare poort die niet wordt gebruikt voor de listenertestpoort. | 58888 |
   | **Poort back-end** | Dit veld wordt niet gebruikt wanneer er een zwevend IP-adres voor Direct Server Return is ingesteld | 58888 |
   | **Test** |De naam die u voor de test hebt opgegeven | WSFCEndPointProbe |
   | **Sessiepersistentie** | Vervolgkeuzelijst | **Geen** |
   | **Time-out voor inactiviteit** | Het aantal minuten om een TCP-verbinding open te houden | 4 |
   | **Zwevend IP (Direct Server Return)** | |Ingeschakeld |

   > [!WARNING]
   > Direct Server Return wordt tijdens het maken ingesteld. De naam kan niet worden gewijzigd.
   >

1. Selecteer **OK** om de taakverdelingsregels in te stellen.

## <a name="configure-the-listener"></a><a name="configure-listener"></a> Listener configureren

De volgende stap bestaat uit het configureren van een listener voor een beschikbaarheidsgroep op het failovercluster.

> [!NOTE]
> In deze zelfstudie kunt u zien hoe u één listener maakt met één IP-adres voor een interne load balancer. Zie [Een listener voor een beschikbaarheidsgroep en een load balancer maken | Azure](availability-group-listener-powershell-configure.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) als u een of meer listeners wilt maken met behulp van een of meer IP-adressen.
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Listener-poort instellen

Stel in SQL Server Management Studio de listener-poort in.

1. Start SQL Server Management Studio en maak verbinding met de primaire replica.

1. Ga naar **Hoge beschikbaarheid met AlwaysOn** > **Beschikbaarheidsgroepen** > **Listeners voor beschikbaarheidsgroep**.

1. U ziet nu de naam van de listener die u hebt gemaakt in Failoverclusterbeheer. Klik met de rechtermuisknop op de naam en selecteer **Eigenschappen**.

1. Geef in het vak **Poort** het poortnummer op voor de listener voor de beschikbaarheidsgroep. 1433 is de standaardwaarde. Selecteer **OK**.

U hebt nu een SQL Server-beschikbaarheidsgroep in virtuele Azure-machines die worden uitgevoerd in de modus Resourcebeheerder.

## <a name="test-connection-to-listener"></a>Verbinding met listener testen

Test als volgt de verbinding:

1. Gebruik RDP om verbinding te maken met een SQL-server die zich in hetzelfde virtuele netwerk bevindt, maar die geen eigenaar is van de replica. U kunt de andere SQL-server in het cluster gebruiken.

1. Gebruik het hulpprogramma **sqlcmd** om de verbinding te testen. Met het volgende script wordt bijvoorbeeld met Windows-verificatie een **sqlcmd**-verbinding met de primaire replica tot stand gebracht via de listener:

   ```cmd
   sqlcmd -S <listenerName> -E
   ```

   Als de listener een andere poort dan de standaardpoort (1433) gebruikt, geeft u de poort op in de verbindingsreeks. Met de volgende `sqlcmd`-opdracht wordt bijvoorbeeld verbinding met een listener op poort 1435 gemaakt:

   ```cmd
   sqlcmd -S <listenerName>,1435 -E
   ```

De SQLCMD-verbinding maakt automatisch verbinding met het exemplaar van SQL Server dat als host voor de primaire replica fungeert.

> [!TIP]
> Zorg ervoor dat de poort die u opgeeft, geopend is op de firewall van beide SQL-servers. Voor beide servers is een regel voor binnenkomende verbindingen vereist voor de TCP-poort die u gebruikt. Zie [Add or Edit Firewall Rule](/previous-versions/orphan-topics/ws.11/cc753558(v=ws.11)) (Firewallregel toevoegen of bewerken) voor meer informatie.
>

## <a name="next-steps"></a>Volgende stappen

- [Een IP-adres aan een load balancer toevoegen voor een tweede beschikbaarheidsgroep](availability-group-listener-powershell-configure.md#Add-IP).