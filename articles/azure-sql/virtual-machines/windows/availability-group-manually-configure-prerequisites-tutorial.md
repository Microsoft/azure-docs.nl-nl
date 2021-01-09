---
title: 'Zelf studie: vereisten voor een beschikbaarheids groep'
description: In deze zelf studie ziet u hoe u de vereisten configureert voor het maken van een SQL Server AlwaysOn-beschikbaarheids groep op Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/29/2018
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 33233e8a6aa54e65094e0cc6130e804241d7201c
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98044285"
---
# <a name="tutorial-prerequisites-for-creating-availability-groups-on-sql-server-on-azure-virtual-machines"></a>Zelf studie: vereisten voor het maken van beschikbaarheids groepen op SQL Server op Azure Virtual Machines

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In deze zelf studie ziet u hoe u de vereisten voor het maken van een SQL Server AlwaysOn [-beschikbaarheids groep op Azure virtual machines (vm's)](availability-group-manually-configure-tutorial.md)kunt volt ooien. Wanneer u de vereisten hebt voltooid, hebt u een domein controller, twee SQL Server Vm's en een Witness-server in één resource groep.

Hoewel in dit artikel handmatig de omgeving van de beschikbaarheidsgroep wordt geconfigureerd, is het ook mogelijk om dit te doen met behulp van de [Azure Portal](availability-group-azure-portal-configure.md), [PowerShell of de Azure CLI](availability-group-az-commandline-configure.md) of [Azure-quickstart-sjablonen](availability-group-quickstart-template-configure.md). 

**Geschatte tijd**: het kan enkele uren duren om de vereisten te volt ooien. Veel van deze tijd wordt besteed aan het maken van virtuele machines.

In het volgende diagram ziet u wat u in de zelf studie maakt.

![Beschikbaarheidsgroep](./media/availability-group-manually-configure-prerequisites-tutorial-/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>Documentatie voor de beschikbaarheids groep bekijken

In deze zelf studie wordt ervan uitgegaan dat u een basis memorandum hebt van SQL Server AlwaysOn-beschikbaarheids groepen. Zie overzicht van AlwaysOn [Availability groups (SQL Server)](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)als u niet bekend bent met deze technologie.


## <a name="create-an-azure-account"></a>Een Azure-account maken

U hebt een Azure-account nodig. U kunt [een gratis Azure-account openen of de](https://signup.azure.com/signup?offer=ms-azr-0044p&appId=102&ref=azureplat-generic) [voor delen van Visual Studio-abonnee activeren](/visualstudio/subscriptions/subscriber-benefits).

## <a name="create-a-resource-group"></a>Een resourcegroep maken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer **+** deze optie om een nieuw object te maken in de portal.

   ![Nieuw object](./media/availability-group-manually-configure-prerequisites-tutorial-/01-portalplus.png)

3. Geef de **resource groep** op in het zoek venster voor **Marketplace** .

   ![Resourcegroep](./media/availability-group-manually-configure-prerequisites-tutorial-/01-resourcegroupsymbol.png)

4. Selecteer een **resource groep**.
5. Selecteer **Maken**.
6. Typ onder **naam van resource groep** een naam voor de resource groep. Typ bijvoorbeeld **SQL-ha-RG**.
7. Als u meerdere Azure-abonnementen hebt, controleert u of het abonnement het Azure-abonnement is waarin u de beschikbaarheids groep wilt maken.
8. Selecteer een locatie. De locatie is de Azure-regio waar u de beschikbaarheids groep wilt maken. In dit artikel worden alle resources op één Azure-locatie gebouwd.
9. Controleer of het selectie vakje **vastmaken aan dash board** is ingeschakeld. Deze optionele instelling plaatst een snelkoppeling voor de resource groep op het Azure Portal dash board.

   ![De snelkoppeling naar de resource groep voor de Azure Portal](./media/availability-group-manually-configure-prerequisites-tutorial-/01-resourcegroup.png)

10. Selecteer **maken** om de resource groep te maken.

De resource groep wordt door Azure gemaakt en er wordt een snelkoppeling naar de resource groep in de portal gespeld.

## <a name="create-the-network-and-subnets"></a>Het netwerk en de subnetten maken

De volgende stap is het maken van de netwerken en subnetten in de Azure-resource groep.

De oplossing maakt gebruik van één virtueel netwerk met twee subnetten. Het [overzicht van virtuele](../../../virtual-network/virtual-networks-overview.md) netwerken biedt meer informatie over netwerken in Azure.

Het virtuele netwerk maken in de Azure Portal:

1. Selecteer **+ toevoegen** in de resource groep. 

   ![Nieuw item](./media/availability-group-manually-configure-prerequisites-tutorial-/02-newiteminrg.png)
2. Zoek naar het **virtuele netwerk**.

     ![Virtueel netwerk zoeken](./media/availability-group-manually-configure-prerequisites-tutorial-/04-findvirtualnetwork.png)
3. Selecteer **virtueel netwerk**.
4. Selecteer in het **virtuele netwerk** het **Resource Manager** -implementatie model en selecteer vervolgens **maken**.

    De volgende tabel bevat de instellingen voor het virtuele netwerk:

   | **Veld** | Waarde |
   | --- | --- |
   | **Naam** |autoHAVNET |
   | **Adresruimte** |10.33.0.0/24 |
   | **Subnetnaam** |Beheerder |
   | **Subnetadresbereik** |10.33.0.0/29 |
   | **Abonnement** |Geef het abonnement op dat u wilt gebruiken. Het **abonnement** is leeg als u slechts één abonnement hebt. |
   | **Resourcegroep** |Kies **bestaande gebruiken** en kies de naam van de resource groep. |
   | **Locatie** |De Azure-locatie opgeven. |

   De adres ruimte en het adres bereik van het subnet kunnen afwijken van de tabel. Afhankelijk van uw abonnement stelt de portal een beschik bare adres ruimte en een bijbehorend adres bereik voor het subnet voor. Als er onvoldoende adres ruimte beschikbaar is, gebruikt u een ander abonnement.

   In het voor beeld wordt de naam **beheerder** van het subnet gebruikt. Dit subnet is voor de domein controllers.

5. Selecteer **Maken**.

   ![Het virtuele netwerk configureren](./media/availability-group-manually-configure-prerequisites-tutorial-/06-configurevirtualnetwork.png)

Azure keert u terug naar het portal-dash board en waarschuwt u wanneer het nieuwe netwerk is gemaakt.

### <a name="create-a-second-subnet"></a>Een tweede subnet maken

Het nieuwe virtuele netwerk heeft één subnet met de naam **admin**. De domein controllers gebruiken dit subnet. De SQL Server-Vm's gebruiken een tweede subnet met de naam **SQL**. Dit subnet configureren:

1. Selecteer op het dash board de resource groep die u hebt gemaakt, **SQL-ha-RG**. Zoek het netwerk in de resource groep onder **resources**.

    Als **SQL-ha-RG** niet zichtbaar is, zoekt u deze door **resource groepen** te selecteren en te filteren op basis van de naam van de resource groep.

2. Selecteer **autoHAVNET** in de lijst met resources. 
3. Selecteer **subnetten** onder **instellingen** in het virtuele netwerk van **autoHAVNET** .

    Noteer het subnet dat u al hebt gemaakt.

   ![Noteer het subnet dat u al hebt gemaakt](./media/availability-group-manually-configure-prerequisites-tutorial-/07-addsubnet.png)

5. Selecteer **+ subnet** om een tweede subnet te maken.
6. Configureer het subnet in **subnet toevoegen** door **sqlsubnet** onder **naam** te typen. Azure geeft automatisch een geldig **adres bereik** op. Controleer of dit adres bereik ten minste 10 adressen bevat. In een productie omgeving hebt u mogelijk meer adressen nodig.
7. Selecteer **OK**.

    ![Subnet configureren](./media/availability-group-manually-configure-prerequisites-tutorial-/08-configuresubnet.png)

De volgende tabel geeft een overzicht van de netwerk configuratie-instellingen:

| **Veld** | Waarde |
| --- | --- |
| **Naam** |**autoHAVNET** |
| **Adresruimte** |Deze waarde is afhankelijk van de beschik bare adres ruimten in uw abonnement. Een typische waarde is 10.0.0.0/16. |
| **Subnetnaam** |**beheerder** |
| **Subnetadresbereik** |Deze waarde is afhankelijk van de beschik bare adresbereiken in uw abonnement. Een typische waarde is 10.0.0.0/24. |
| **Subnetnaam** |**sqlsubnet** |
| **Subnetadresbereik** |Deze waarde is afhankelijk van de beschik bare adresbereiken in uw abonnement. Een typische waarde is 10.0.1.0/24. |
| **Abonnement** |Geef het abonnement op dat u wilt gebruiken. |
| **Resourcegroep** |**SQL-HA-RG** |
| **Locatie** |Geef dezelfde locatie op die u hebt gekozen voor de resource groep. |

## <a name="create-availability-sets"></a>Beschikbaarheidssets maken

Voordat u virtuele machines maakt, moet u beschikbaarheids sets maken. Beschikbaarheids sets verminderen de uitval tijd voor geplande of niet-geplande onderhouds gebeurtenissen. Een beschikbaarheidsset van Azure is een logische groep resources die Azure plaatst op fysieke fout domeinen en update domeinen. Een fout domein zorgt ervoor dat de leden van de beschikbaarheidsset afzonderlijke stroom-en netwerk bronnen hebben. Een update domein zorgt ervoor dat de leden van de beschikbaarheidsset niet tegelijkertijd voor onderhoud worden ingesteld. Zie voor meer informatie [De beschikbaarheid van virtuele machines beheren](../../../virtual-machines/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

U hebt twee beschikbaarheids sets nodig. Een voor de domein controllers. De tweede is voor de SQL Server Vm's.

Als u een beschikbaarheidsset wilt maken, gaat u naar de resource groep en selecteert u **toevoegen**. Filter de resultaten door de **beschikbaarheidsset** in te voeren. Selecteer **beschikbaarheidsset** in de resultaten en selecteer vervolgens **maken**.

Configureer twee beschikbaarheids sets volgens de para meters in de volgende tabel:

| **Veld** | Beschikbaarheidsset van domein controller | Beschikbaarheidsset SQL Server |
| --- | --- | --- |
| **Naam** |adavailabilityset |sqlavailabilityset |
| **Resourcegroep** |SQL-HA-RG |SQL-HA-RG |
| **Foutdomeinen** |3 |3 |
| **Updatedomeinen** |5 |3 |

Nadat u de beschikbaarheids sets hebt gemaakt, keert u terug naar de resource groep in de Azure Portal.

## <a name="create-domain-controllers"></a>Domein controllers maken

Nadat u de netwerk-, subnetten-en beschikbaarheids sets hebt gemaakt, kunt u de virtuele machines voor de domein controllers maken.

### <a name="create-virtual-machines-for-the-domain-controllers"></a>Virtuele machines maken voor de domein controllers

Als u de domein controllers wilt maken en configureren, gaat u terug naar de resource groep **SQL-ha-RG** .

1. Selecteer **Toevoegen**. 
2. Typ **Windows Server 2016 Data Center**.
3. Selecteer **Windows Server 2016 Data Center**. Controleer in **Windows Server 2016 Data Center** of het implementatie model **Resource Manager** is en selecteer vervolgens **maken**. 

Herhaal de voor gaande stappen om twee virtuele machines te maken. Noem de twee virtuele machines:

* AD-primair-DC
* AD-secundair-DC

  > [!NOTE]
  > De virtuele machine **met AD-secondaire domein controller** is optioneel en biedt een hoge Beschik baarheid voor Active Directory Domain Services.
  >

De volgende tabel bevat de instellingen voor deze twee computers:

| **Veld** | Waarde |
| --- | --- |
| **Naam** |Eerste domein controller: *ad-Primary-DC*.</br>Tweede domein controller *ad-secundair-DC*. |
| **VM-schijf type** |SSD |
| **Gebruikersnaam** |Domein Administrator |
| **Wachtwoord** |Contoso! 0000 |
| **Abonnement** |*Uw abonnement* |
| **Resourcegroep** |SQL-HA-RG |
| **Locatie** |*Uw locatie* |
| **Grootte** |DS1_V2 |
| **Storage** | **Beheerde schijven gebruiken**  -  **Ja** |
| **Virtueel netwerk** |autoHAVNET |
| **Subnet** |beheerder |
| **Openbaar IP-adres** |*Dezelfde naam als de virtuele machine* |
| **Netwerkbeveiligingsgroep** |*Dezelfde naam als de virtuele machine* |
| **Beschikbaarheidsset** |adavailabilityset </br>**Fout domeinen**: 2 </br>**Update domeinen**: 2|
| **Diagnostics** |Ingeschakeld |
| **Opslagaccount voor diagnose** |*Automatisch gemaakt* |

   >[!IMPORTANT]
   >U kunt een virtuele machine alleen in een beschikbaarheidsset plaatsen wanneer u deze maakt. U kunt de beschikbaarheidsset niet wijzigen nadat een virtuele machine is gemaakt. Zie [de beschik baarheid van virtuele machines beheren](../../../virtual-machines/manage-availability.md).

De virtuele machines worden gemaakt door Azure.

Nadat de virtuele machines zijn gemaakt, configureert u de domein controller.

### <a name="configure-the-domain-controller"></a>De domein controller configureren

In de volgende stappen configureert u de **ad-Primary-DC-** computer als een domein controller voor Corp.contoso.com.

1. Open in de Portal de resource groep **SQL-ha-RG** en selecteer de **ad-Primary-DC-** computer. Selecteer in **ad-primaire domein controller** **verbinding maken** om een RDP-bestand te openen voor toegang tot extern bureau blad.

    ![Verbinding maken met een virtuele machine](./media/availability-group-manually-configure-prerequisites-tutorial-/20-connectrdp.png)

2. Meld u aan met uw geconfigureerde beheerders account (**\DomainAdmin**) en wacht woord (**Contoso! 0000**).
3. Het **Serverbeheer** dash board moet standaard worden weer gegeven.
4. Selecteer de koppeling **functies en onderdelen toevoegen** op het dash board.

    ![Serverbeheer-functies toevoegen](./media/availability-group-manually-configure-prerequisites-tutorial-/22-addfeatures.png)

5. Selecteer **volgende** totdat u toegang hebt tot de sectie **Server functies** .
6. Selecteer de **Active Directory Domain Services** **-en DNS-server** functies. Wanneer u hierom wordt gevraagd, voegt u extra functies toe die vereist zijn voor deze rollen.

   > [!NOTE]
   > Windows waarschuwt u dat er geen statisch IP-adres is. Als u de configuratie wilt testen, selecteert u **door gaan**. Stel voor productie scenario's het IP-adres in op statisch in het Azure Portal, of [gebruik Power shell om het statische IP-adres van de computer van de domein controller](/previous-versions/azure/virtual-network/virtual-networks-reserved-private-ip)in te stellen.
   >

    ![Dialoog venster Rollen toevoegen](./media/availability-group-manually-configure-prerequisites-tutorial-/23-addroles.png)

7. Selecteer **volgende** totdat u het **bevestigings** gedeelte hebt bereikt. Schakel het selectie vakje **de doel server automatisch opnieuw opstarten als dit vereist** is in.
8. Selecteer **Installeren**.
9. Nadat de installatie van de onderdelen is voltooid, gaat u terug naar het dash board van **Serverbeheer** .
10. Selecteer de optie nieuw **AD DS** in het linkerdeel venster.
11. Selecteer de koppeling **meer** op de gele waarschuwings balk.

    ![AD DS dialoog venster op de DNS-Server-VM](./media/availability-group-manually-configure-prerequisites-tutorial-/24-addsmore.png)
    
12. Selecteer in de kolom **actie** van het dialoog venster **alle Server taak Details** de optie **deze server promo veren naar een domein controller**.
13. Gebruik de volgende waarden in de **Wizard Active Directory Domain Services configuratie**:

    | **Pagina** | Instelling |
    | --- | --- |
    | **Implementatieconfiguratie** |**Een nieuw forest toevoegen**<br/> **Naam van hoofd domein** = Corp.contoso.com |
    | **Domeincontrolleropties** |**Wacht woord voor DSRM** = contoso! 0000<br/>**Bevestig het wacht woord** = contoso! 0000 |

14. Selecteer **volgende** om de andere pagina's in de wizard te door lopen. Controleer op de pagina **controle van vereisten** of het volgende bericht wordt weer **gegeven: alle controles van vereisten zijn geslaagd**. U kunt alle toepasselijke waarschuwings berichten bekijken, maar het is mogelijk om door te gaan met de installatie.
15. Selecteer **Installeren**. De virtuele machine van **ad-Primary-DC** wordt automatisch opnieuw opgestart.

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>Noteer het IP-adres van de primaire domein controller

Gebruik de primaire domein controller voor DNS. Noteer het IP-adres van de primaire domein controller.

Een manier om het IP-adres van de primaire domein controller op te halen, is via de Azure Portal.

1. Open de resource groep op het Azure Portal.

2. Selecteer de primaire domein controller.

3. Selecteer **netwerk interfaces** op de primaire domein controller.

![Netwerkinterfaces](./media/availability-group-manually-configure-prerequisites-tutorial-/25-primarydcip.png)

Noteer het privé-IP-adres voor deze server.

### <a name="configure-the-virtual-network-dns"></a>De DNS van het virtuele netwerk configureren

Nadat u de eerste domein controller hebt gemaakt en DNS op de eerste server inschakelt, configureert u het virtuele netwerk voor het gebruik van deze server voor DNS.

1. Selecteer in het Azure Portal het virtuele netwerk.

2. Selecteer bij **instellingen** de optie **DNS-server**.

3. Selecteer **aangepast** en typ het privé-IP-adres van de primaire domein controller.

4. Selecteer **Opslaan**.

### <a name="configure-the-second-domain-controller"></a>De tweede domein controller configureren

Nadat de primaire domein controller opnieuw is opgestart, kunt u de tweede domein controller configureren. Deze optionele stap is voor hoge Beschik baarheid. Volg deze stappen om de tweede domein controller te configureren:

1. Open in de Portal de resource groep **SQL-ha-RG** en selecteer de **ad-Secondary-DC-** computer. Op **ad-secundair-DC** selecteert u **verbinding maken** om een RDP-bestand te openen voor toegang tot extern bureau blad.
2. Meld u aan bij de virtuele machine met behulp van uw geconfigureerde beheerders account (**BUILTIN\DomainAdmin**) en wacht woord (**Contoso! 0000**).
3. Wijzig het adres van de voor Keurs-DNS-server in het adres van de domein controller.
4. Selecteer de netwerk interface in **netwerk centrum**.

   ![Netwerkinterface](./media/availability-group-manually-configure-prerequisites-tutorial-/26-networkinterface.png)

5. Selecteer **Eigenschappen**.
6. Selecteer **Internet Protocol versie 4 (TCP/IPv4)** en selecteer vervolgens **Eigenschappen**.
7. Selecteer **de volgende DNS-server adressen gebruiken** en geef het adres van de primaire domein controller op in de **voor Keurs-DNS-server**.
8. Selecteer **OK** en **sluiten** om de wijzigingen door te voeren. U kunt nu lid worden van de virtuele machine aan **Corp.contoso.com**.

   >[!IMPORTANT]
   >Als u de verbinding met uw externe bureau blad hebt verbroken nadat u de DNS-instelling hebt gewijzigd, gaat u naar de Azure Portal en start u de virtuele machine opnieuw op.

9. Open **Serverbeheer dash board** van het externe bureau blad naar de secundaire domein controller.
10. Selecteer de koppeling **functies en onderdelen toevoegen** op het dash board.

    ![Serverbeheer-functies toevoegen](./media/availability-group-manually-configure-prerequisites-tutorial-/22-addfeatures.png)
11. Selecteer **volgende** totdat u toegang hebt tot de sectie **Server functies** .
12. Selecteer de **Active Directory Domain Services** **-en DNS-server** functies. Wanneer u hierom wordt gevraagd, voegt u extra functies toe die vereist zijn voor deze rollen.
13. Nadat de installatie van de onderdelen is voltooid, gaat u terug naar het dash board van **Serverbeheer** .
14. Selecteer de optie nieuw **AD DS** in het linkerdeel venster.
15. Selecteer de koppeling **meer** op de gele waarschuwings balk.
16. Selecteer in de kolom **actie** van het dialoog venster **alle Server taak Details** de optie **deze server promo veren naar een domein controller**.
17. Onder **implementatie configuratie** selecteert **u een domein controller toevoegen aan een bestaand domein**.

    ![Implementatie configuratie](./media/availability-group-manually-configure-prerequisites-tutorial-/28-deploymentconfig.png)

18. Klik op **Selecteren**.
19. Verbinding maken met behulp van het beheerders account (**Corp. CONTOSO. COM\domainadmin**) en het wacht woord (**Contoso! 0000**).
20. Kies in **een domein in het forest selecteren** uw domein en selecteer vervolgens **OK**.
21. Gebruik in de **Opties voor domein controller** de standaard waarden en stel een DSRM-wacht woord in.

    >[!NOTE]
    >Op de pagina **DNS-opties** wordt mogelijk gewaarschuwd dat er geen delegering voor deze DNS-server kan worden gemaakt. U kunt deze waarschuwing negeren in niet-productie omgevingen.
    >

22. Selecteer **volgende** totdat het dialoog venster de controle van **vereisten** bereikt. Selecteer vervolgens **Installeren**.

Nadat de configuratie wijzigingen door de server zijn voltooid, start u de server opnieuw op.

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>Voeg het privé-IP-adres toe aan de tweede domein controller aan de VPN DNS-server

Wijzig in de Azure Portal onder virtueel netwerk de DNS-server zodanig dat het IP-adres van de secundaire domein controller bevat. Met deze instelling wordt de DNS-service redundantie toegestaan.

### <a name="configure-the-domain-accounts"></a><a name="DomainAccounts"></a> Domein accounts configureren

In de volgende stappen configureert u de Active Directory accounts. De volgende tabel bevat de accounts:

| |Installatie account<br/> |sqlserver-0 <br/>SQL Server-en SQL Agent-service account |sqlserver-1<br/>SQL Server-en SQL Agent-service account
| --- | --- | --- | ---
|**Voor naam** |Installeren |SQLSvc1 | SQLSvc2
|**SamAccountName van gebruiker** |Installeren |SQLSvc1 | SQLSvc2

Gebruik de volgende stappen om elk account te maken.

1. Meld u aan bij de **ad-Primary-DC-** computer.
2. Selecteer in **Serverbeheer** **extra** en selecteer vervolgens **Active Directory-beheercentrum**.   
3. Selecteer **Corp (lokaal)** in het linkerdeel venster.
4. Selecteer in het deel venster rechter **taken** de optie **Nieuw** en selecteer vervolgens **gebruiker**.

   ![Active Directory-beheercentrum](./media/availability-group-manually-configure-prerequisites-tutorial-/29-addcnewuser.png)

   >[!TIP]
   >Stel een complex wacht woord in voor elk account.<br/> Voor niet-productie omgevingen stelt u het gebruikers account in op nooit verlopen.
   >

5. Selecteer **OK** om de gebruiker te maken.
6. Herhaal de voor gaande stappen voor elk van de drie accounts.

### <a name="grant-the-required-permissions-to-the-installation-account"></a>De vereiste machtigingen verlenen aan het installatie account

1. Selecteer in de **Active Directory-beheercentrum** **Corp (lokaal)** in het linkerdeel venster. Selecteer vervolgens in het deel venster rechterdeel **taken** de optie **Eigenschappen**.

    ![Eigenschappen van CORP-gebruikers](./media/availability-group-manually-configure-prerequisites-tutorial-/31-addcproperties.png)

2. Selecteer **uitbrei dingen** en selecteer vervolgens de knop **Geavanceerd** op het tabblad **beveiliging** .
3. Selecteer in het dialoog venster **Geavanceerde beveiligings instellingen voor Corp** de optie **toevoegen**.
4. Klik op **een principal selecteren**, zoek naar **CORP\Install** en selecteer **OK**.
5. Schakel het selectie vakje **alle eigenschappen lezen** in.

6. Schakel het selectie vakje **computer objecten maken** in.

     ![Corp-gebruikers machtigingen](./media/availability-group-manually-configure-prerequisites-tutorial-/33-addpermissions.png)

7. Selecteer **OK** en vervolgens weer **OK**. Sluit het venster Eigenschappen van **Corp** .

Nu u klaar bent met het configureren van Active Directory en de gebruikers objecten, maakt u twee SQL Server Vm's en een Witness-Server-VM. Voeg vervolgens alle drie toe aan het domein.

## <a name="create-sql-server-vms"></a>SQL Server Vm's maken

Maak drie extra virtuele machines. De oplossing vereist twee virtuele machines met SQL Server exemplaren. Een derde virtuele machine werkt als Witness. In Windows Server 2016 kunt u een [cloudwitness](/windows-server/failover-clustering/deploy-cloud-witness)gebruiken. Voor consistentie met eerdere besturings systemen maakt dit artikel echter gebruik van een virtuele machine voor een Witness.  

Voordat u verdergaat, moet u rekening houden met de volgende ontwerp beslissingen.

* **Opslag-Azure-Managed Disks**

   Gebruik Azure Managed Disks voor de opslag van de virtuele machine. Micro soft adviseert Managed Disks voor SQL Server virtuele machines. Managed Disks verwerken de opslag achter de schermen. Bovendien distribueert Azure de opslagresources zodat voldoende redundantie wordt geboden wanneer virtuele machines met Managed Disks zich in dezelfde beschikbaarheidsset bevinden. Zie [Azure Managed Disks overview](../../../virtual-machines/managed-disks-overview.md) (Overzicht van Azure Managed Disks) voor meer informatie. Zie [Managed disks voor virtuele machines in een beschikbaarheidsset gebruiken](../../../virtual-machines/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set)voor meer informatie over beheerde schijven in een beschikbaarheidsset.

* **Netwerk-privé-IP-adressen in productie**

   Voor de virtuele machines gebruikt deze zelf studie open bare IP-adressen. Met een openbaar IP-adres is externe verbinding rechtstreeks met de virtuele machine via internet mogelijk en worden configuratie stappen eenvoudiger. In productie omgevingen raadt micro soft alleen privé-IP-adressen aan om de kwets baarheid van de VM-resource van het SQL Server exemplaar te verminderen.

* **Netwerk-aanbevolen één NIC per server** 

Gebruik één NIC per server (cluster knooppunt) en één subnet. Azure-netwerken hebben fysieke redundantie, waardoor extra Nic's en subnetten onnodig worden op een gast cluster van de virtuele machine van Azure. In het cluster validatie rapport wordt u gewaarschuwd dat de knoop punten alleen bereikbaar zijn op één netwerk. U kunt deze waarschuwing negeren op virtuele Azure-machine gast-failoverclusters.

### <a name="create-and-configure-the-sql-server-vms"></a>De SQL Server Vm's maken en configureren

Maak vervolgens drie Vm's: twee SQL Server Vm's en één virtuele machine voor een extra cluster knooppunt. Als u elke virtuele machine wilt maken, gaat u terug naar de resource groep **SQL-ha-RG** en selecteert u vervolgens **toevoegen**. Zoek het juiste galerij-item, selecteer **virtuele machine** en selecteer vervolgens **uit galerie**. Gebruik de informatie in de volgende tabel om u te helpen bij het maken van de virtuele machines:


| Pagina | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Het juiste galerie-item selecteren |**Windows Server 2016 Datacenter** |**SQL Server 2016 SP1 Enter prise op Windows Server 2016** |**SQL Server 2016 SP1 Enter prise op Windows Server 2016** |
| **Basis beginselen** van de configuratie van virtuele machines |**Name** = cluster-FSW<br/>**Gebruikers naam** = domein Administrator<br/>**Wacht woord** = contoso! 0000<br/>**Abonnement** = uw abonnement<br/>**Resource groep** = SQL-ha-RG<br/>**Locatie** = uw Azure-locatie |**Name** = sqlserver-0<br/>**Gebruikers naam** = domein Administrator<br/>**Wacht woord** = contoso! 0000<br/>**Abonnement** = uw abonnement<br/>**Resource groep** = SQL-ha-RG<br/>**Locatie** = uw Azure-locatie |**Name** = sqlserver-1<br/>**Gebruikers naam** = domein Administrator<br/>**Wacht woord** = contoso! 0000<br/>**Abonnement** = uw abonnement<br/>**Resource groep** = SQL-ha-RG<br/>**Locatie** = uw Azure-locatie |
| Virtuele-machine configuratie **grootte** |**Grootte** = ds1 \_ v2 (1 VCPU, 3,5 GB) |**Grootte** = DS2 \_ v2 (2 VCPU'S, 7 GB)</br>De omvang moet ondersteuning bieden voor SSD Storage (Premium-schijf ondersteuning). )) |**Grootte** = DS2 \_ v2 (2 VCPU'S, 7 GB) |
| Configuratie- **instellingen** voor virtuele machines |**Opslag**: gebruik beheerde schijven.<br/>**Virtueel netwerk** = autoHAVNET<br/>**Subnet** = sqlsubnet (10.1.1.0/24)<br/>Het **open bare IP-adres** wordt automatisch gegenereerd.<br/>**Netwerk beveiligings groep** = geen<br/>**Diagnostische gegevens over bewaking** = ingeschakeld<br/>**Opslag account voor diagnostische gegevens** = een automatisch gegenereerd opslag account gebruiken<br/>**Beschikbaarheidsset** = sqlAvailabilitySet<br/> |**Opslag**: gebruik beheerde schijven.<br/>**Virtueel netwerk** = autoHAVNET<br/>**Subnet** = sqlsubnet (10.1.1.0/24)<br/>Het **open bare IP-adres** wordt automatisch gegenereerd.<br/>**Netwerk beveiligings groep** = geen<br/>**Diagnostische gegevens over bewaking** = ingeschakeld<br/>**Opslag account voor diagnostische gegevens** = een automatisch gegenereerd opslag account gebruiken<br/>**Beschikbaarheidsset** = sqlAvailabilitySet<br/> |**Opslag**: gebruik beheerde schijven.<br/>**Virtueel netwerk** = autoHAVNET<br/>**Subnet** = sqlsubnet (10.1.1.0/24)<br/>Het **open bare IP-adres** wordt automatisch gegenereerd.<br/>**Netwerk beveiligings groep** = geen<br/>**Diagnostische gegevens over bewaking** = ingeschakeld<br/>**Opslag account voor diagnostische gegevens** = een automatisch gegenereerd opslag account gebruiken<br/>**Beschikbaarheidsset** = sqlAvailabilitySet<br/> |
| Instellingen voor configuratie van virtuele machine **SQL Server** |Niet van toepassing |**SQL-connectiviteit** = privé (binnen Virtual Network)<br/>**Poort** = 1433<br/>**SQL-verificatie** = uitschakelen<br/>**Opslag configuratie** = algemeen<br/>**Automatische patching** = zondag om 2:00<br/>**Automatische back-up** = uitgeschakeld</br>**Azure Key Vault-integratie** = uitgeschakeld |**SQL-connectiviteit** = privé (binnen Virtual Network)<br/>**Poort** = 1433<br/>**SQL-verificatie** = uitschakelen<br/>**Opslag configuratie** = algemeen<br/>**Automatische patching** = zondag om 2:00<br/>**Automatische back-up** = uitgeschakeld</br>**Azure Key Vault-integratie** = uitgeschakeld |

<br/>

> [!NOTE]
> De computer grootten die hier worden voorgesteld, zijn bedoeld voor het testen van beschikbaarheids groepen in azure Virtual Machines. Voor de beste prestaties van werk belastingen in de productie raadpleegt u de aanbevelingen voor SQL Server computer grootten en configuratie in [Best practices voor de prestaties van SQL Server in Azure virtual machines](performance-guidelines-best-practices.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>

Nadat de drie Vm's volledig zijn ingericht, moet u deze toevoegen aan het **Corp.contoso.com** -domein en CORP\Install beheerders rechten verlenen aan de machines.

### <a name="join-the-servers-to-the-domain"></a><a name="joinDomain"></a>De servers toevoegen aan het domein

U kunt nu lid worden van de virtuele machines aan **Corp.contoso.com**. Voer de volgende stappen uit voor de SQL Server Vm's en de bestands share Witness-server:

1. Maak extern verbinding met de virtuele machine met **BUILTIN\DomainAdmin**.
2. Selecteer in **Serverbeheer** de optie **Lokale server**.
3. Selecteer de koppeling voor de **werk groep** .
4. Selecteer **wijzigen** in de sectie **computer naam** .
5. Schakel het selectie vakje **domein** in en typ **Corp.contoso.com** in het tekstvak. Selecteer **OK**.
6. Geef in het pop-upvenster **Windows-beveiliging** de referenties op voor het standaard domein beheerders account (**CORP\DomainAdmin**) en het wacht woord (**Contoso! 0000**).
7. Wanneer het bericht Welkom bij het domein corp.contoso.com wordt weer gegeven, selecteert u **OK**.
8. Selecteer **sluiten** en selecteer **nu opnieuw opstarten** in het pop-updialoogvenster.

## <a name="add-accounts"></a>Accounts toevoegen

Voeg het installatie account toe als beheerder op elke virtuele machine, Ken machtigingen toe aan het installatie account en lokale accounts in SQL Server en werk het SQL Server-service account bij. 

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>De Corp\Install-gebruiker toevoegen als beheerder op elke VM van het cluster

Nadat elke virtuele machine opnieuw is opgestart als lid van het domein, voegt u **CORP\Install** toe als lid van de lokale groep Administrators.

1. Wacht totdat de virtuele machine opnieuw is opgestart en start het RDP-bestand opnieuw vanaf de primaire domein controller om u aan te melden bij **sqlserver-0** met behulp van het **CORP\DomainAdmin** -account.

   >[!TIP]
   >Zorg ervoor dat u zich aanmeldt met het domein beheerders account. In de vorige stappen gebruikt u het ingebouwde Administrator-account. Nu de server zich in het domein bevindt, gebruikt u het domein account. Geef in uw RDP-sessie de *domein* \\ *gebruikers naam* op.
   >

2. Selecteer in **Serverbeheer** **extra** en selecteer vervolgens **computer beheer**.
3. Vouw in het venster **computer beheer** **lokale gebruikers en groepen** uit en selecteer vervolgens **groepen**.
4. Dubbel klik op de groep **Administrators** .
5. Selecteer in het dialoog venster **Eigenschappen van beheerder** de knop **toevoegen** .
6. Voer de gebruikers **CORP\Install** in en selecteer **OK**.
7. Selecteer **OK** om het dialoog venster met **beheerders eigenschappen** te sluiten.
8. Herhaal de vorige stappen in **sqlserver-1** en **cluster-FSW**.


### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>Maak een aanmelding op elke SQL Server virtuele machine voor het installatie account

Gebruik het installatie account (CORP\install) om de beschikbaarheids groep te configureren. Dit account moet lid zijn van de vaste serverrol **sysadmin** op elke SQL Server VM. Met de volgende stappen maakt u een aanmelding voor het installatie account:

1. Maak verbinding met de server via de Remote Desktop Protocol (RDP) met behulp van het *\<MachineName\> \DomainAdmin* -account.

1. Open SQL Server Management Studio en maak verbinding met het lokale exemplaar van SQL Server.

1. Selecteer in **objectverkenner** **beveiliging**.

1. Klik met de rechter muisknop op **aanmeldingen**. Selecteer **nieuwe aanmelding**.

1. In **Aanmelding - Nieuw** selecteert u **Zoeken**.

1. Selecteer **locaties**.

1. Voer de netwerk referenties voor de domein beheerder in.

1. Gebruik het installatie account (CORP\install).

1. Stel de aanmelding in op een lid van de vaste serverrol **sysadmin** .

1. Selecteer **OK**.

Herhaal de voor gaande stappen op de andere SQL Server VM.

### <a name="configure-system-account-permissions"></a>Machtigingen voor systeem accounts configureren

Als u een account voor het systeem account wilt maken en de juiste machtigingen wilt verlenen, voert u de volgende stappen uit op elk SQL Server-exemplaar:

1. Maak een account voor `[NT AUTHORITY\SYSTEM]` elke SQL Server-instantie. Met het volgende script maakt u dit account:

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. Ken de volgende machtigingen toe aan `[NT AUTHORITY\SYSTEM]` elk SQL Server-exemplaar:

   - `ALTER ANY AVAILABILITY GROUP`
   - `CONNECT SQL`
   - `VIEW SERVER STATE`

   Met het volgende script worden deze machtigingen verleend:

   ```sql
   GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM]
   GO 
   ```

### <a name="set-the-sql-server-service-accounts"></a><a name="setServiceAccount"></a>De SQL Server-service accounts instellen

Stel op elke SQL Server-VM de SQL Server-service account in. Gebruik de accounts die u hebt gemaakt tijdens het configureren van de domein accounts.

1. Open **SQL Server Configuration Manager**.
2. Klik met de rechter muisknop op de SQL Server-service en selecteer **Eigenschappen**.
3. Stel het account en het wacht woord in.
4. Herhaal deze stappen op de andere SQL Server VM.  

Voor SQL Server-beschikbaarheids groepen moet elke SQL Server VM als een domein account worden uitgevoerd.

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>Functies voor failover clustering toevoegen aan virtuele SQL Server Vm's

Als u functies voor Failover Clustering wilt toevoegen, voert u de volgende stappen uit op beide SQL Server Vm's:

1. Maak verbinding met de virtuele machine van SQL Server via de Remote Desktop Protocol (RDP) met behulp van het *CORP\install* -account. Open **Serverbeheer dash board**.
2. Selecteer de koppeling **functies en onderdelen toevoegen** op het dash board.

    ![Serverbeheer-functies toevoegen](./media/availability-group-manually-configure-prerequisites-tutorial-/22-addfeatures.png)

3. Selecteer **volgende** totdat u toegang hebt tot het gedeelte **Server functies** .
4. Selecteer in **functies** **failover clustering**.
5. Voeg aanvullende vereiste onderdelen toe.
6. Selecteer **installeren** om de functies toe te voegen.

Herhaal de stappen op de andere SQL Server VM.

  >[!NOTE]
  > Deze stap, samen met de daad werkelijke deelname van de SQL Server Vm's aan het failovercluster, kan nu worden geautomatiseerd met [Azure SQL VM cli](./availability-group-az-commandline-configure.md) en [Azure Quick](availability-group-quickstart-template-configure.md)start-sjablonen.
  >

### <a name="tuning-failover-cluster-network-thresholds"></a>Drempel waarden voor failover cluster-netwerken afstemmen

Wanneer u Windows-Failoverclusterknooppunten uitvoert in azure Vm's met SQL Server-beschikbaarheids groepen, wijzigt u de cluster instelling in een meer beperkte bewakings status.  Hierdoor is het cluster veel stabieler en betrouwbaarder.  Zie voor meer informatie [IaaS met de drempel waarden voor het SQL Server-failover cluster netwerk](/windows-server/troubleshoot/iaas-sql-failover-cluster).


## <a name="configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall"></a> De firewall op elke SQL Server VM configureren

Voor de oplossing moeten de volgende TCP-poorten in de firewall zijn geopend:

- **SQL Server VM**: poort 1433 voor een standaard exemplaar van SQL Server.
- **Azure Load Balancer-test:** Elke beschik bare poort. Voor beelden regel matig gebruik van 59999.
- **Eind punt voor database spiegeling:** Elke beschik bare poort. Voor beelden regel matig gebruik van 5022.

De firewall poorten moeten op beide SQL Server Vm's zijn geopend.

De methode voor het openen van de poorten is afhankelijk van de firewall-oplossing die u gebruikt. In de volgende sectie wordt uitgelegd hoe u de poorten in Windows Firewall kunt openen. Open de vereiste poorten op elk van uw SQL Server Vm's.

### <a name="open-a-tcp-port-in-the-firewall"></a>Een TCP-poort openen in de firewall

1. Op de eerste SQL Server **Start** scherm, start u **Windows Firewall met geavanceerde beveiliging**.
2. Selecteer in het linkerdeel venster **regels voor binnenkomende verbindingen**. Selecteer **nieuwe regel** in het rechterdeel venster.
3. Kies **poort** bij **regel type**.
4. Geef voor de poort **TCP** op en typ de juiste poort nummers. Zie het volgende voorbeeld:

   ![SQL-firewall](./media/availability-group-manually-configure-prerequisites-tutorial-/35-tcpports.png)

5. Selecteer **Next**.
6. Laat op de pagina **actie** **de optie verbinding toestaan** ingeschakeld en selecteer **volgende**.
7. Accepteer de standaard instellingen op de **profiel** pagina en selecteer **volgende**.
8. Geef op de pagina **naam** een regel naam op (zoals **Azure lb probe**) in het tekstvak **naam** en selecteer vervolgens **volt ooien**.

Herhaal deze stappen op de tweede SQL Server VM.


## <a name="next-steps"></a>Volgende stappen

* [Een SQL Server AlwaysOn-beschikbaarheids groep maken op Azure Virtual Machines](availability-group-manually-configure-tutorial.md)
