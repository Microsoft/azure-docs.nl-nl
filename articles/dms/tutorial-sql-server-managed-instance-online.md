---
title: 'Zelfstudie: SQL Server online naar SQL Managed Instance migreren'
titleSuffix: Azure Database Migration Service
description: Leer hoe u een onlinemigratie uitvoert van SQL Server naar een Azure SQL Managed Instance met behulp van Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 08/04/2020
ms.openlocfilehash: 4ee3e9e7d2aa0247011415b43517147fd421902a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101094933"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-managed-instance-online-using-dms"></a>Zelfstudie: SQL Server online migreren naar een Azure SQL Managed Instance met behulp van DMS

U kunt Azure Database Migration Service gebruiken om de databases met minimale downtime te migreren van een SQL Server-exemplaar naar een [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md). In het artikel [Een SQL Server-exemplaar migreren naar Azure SQL Managed Instance](../azure-sql/managed-instance/migrate-to-instance-from-sql-server.md) vindt u aanvullende methoden waarvoor enkele handmatige stappen nodig kunnen zijn.

In deze zelfstudie migreert u de database **Adventureworks2012** met minimale downtime van een on-premises exemplaar van SQL Server naar een SQL Managed Instance. Dit doet u met behulp van Azure Database Migration Service.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> * Maak een exemplaar van de Azure Database Migration Service.
> * Maak een migratieproject en start de onlinemigratie met behulp van Azure Database Migration Service.
> * Houd de migratie in de gaten.
> * Voer de cutover uit voor de migratie als u klaar bent.

> [!IMPORTANT]
> Voor onlinemigraties van SQL Server naar SQL Managed Instance met behulp van Azure Database Migration Service moet u de volledige databaseback-up en volgende logboekback-ups in de SMB-netwerkshare opgeven die door de service kunnen worden gebruikt om uw databases te migreren. Azure Database Migration Service initieert geen back-ups en maakt in plaats daarvan gebruik van bestaande back-ups voor de migratie, die u mogelijk al hebt als onderdeel van het noodherstelplan.
> Zorg ervoor dat u [back-ups maakt met behulp van de optie WITH CHECKSUM](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?preserve-view=true&view=sql-server-2017). Voeg ook geen meerdere back-ups (zoals full en t-log) toe aan één back-upmedium, maar maak elke back-up in een afzonderlijk back-upbestand. U kunt gecomprimeerde back-ups gebruiken om de kans te verkleinen op problemen bij het migreren van grote back-ups.

> [!NOTE]
> Als u Azure Database Migration Service gebruikt om een onlinemigratie uit te voeren, is het vereist dat u een exemplaar maakt op basis van de prijscategorie Premium.

> [!IMPORTANT]
> Voor een optimale migratie-ervaring raadt Microsoft u aan een exemplaar van Azure Database Migration Service te maken in dezelfde Azure-regio als de doeldatabase. Het verplaatsen van gegevens naar regio's of geografieën kan het migratieproces vertragen en fouten veroorzaken.

> [!IMPORTANT]
> Houd de duur van het onlinemigratieproces zo kort mogelijk om het risico van onderbreking te minimaliseren als gevolg van herconfiguratie van exemplaren of gepland onderhoud. In het geval van een dergelijke gebeurtenis wordt het migratieproces namelijk helemaal opnieuw gestart. Bij gepland onderhoud wordt er een respijtperiode van 36 uur gehanteerd voordat het migratieproces opnieuw wordt gestart.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

In dit artikel wordt een onlinemigratie beschreven van SQL Server naar een SQL Managed Instance. Zie [SQL Server offline migreren naar een SQL Managed Instance met behulp van DMS](tutorial-sql-server-to-managed-instance.md) voor informatie over een offlinemigratie.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Maak een Microsoft Azure Virtual Network voor Azure Database Migration Service met behulp van het Azure Resource Manager-implementatiemodel. Dit geeft site-naar-site-verbinding met uw on-premises bronservers met behulp van [ExpressRoute](../expressroute/expressroute-introduction.md) of [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). [Lees hier meer over netwerktopologieën voor migraties van SQL Managed Instance met behulp van Azure Database Migration Service](./resource-network-topologies.md). Voor meer informatie over het maken van een virtueel netwerk raadpleegt u de [Documentatie over virtuele netwerken](../virtual-network/index.yml) en dan met name de quickstart-artikelen met stapsgewijze informatie.

    > [!NOTE]
    > Als u bij de installatie van een virtueel netwerk gebruikmaakt van ExpressRoute met netwerkpeering voor Microsoft, voegt u de volgende service-[eindpunten](../virtual-network/virtual-network-service-endpoints-overview.md) toe aan het subnet waarin de service wordt ingericht:
    >
    > * Eindpunt van de doeldatabase (bijvoorbeeld SQL-eindpunt, Cosmos DB-eindpunt, enzovoort)
    > * Opslageindpunt
    > * Service Bus-eindpunt
    >
    > Deze configuratie is noodzakelijk omdat Azure Database Migration Service geen internetverbinding biedt.
    >
    >Als u geen site-naar-site-verbinding hebt tussen het on-premises netwerk en Azure of als er beperkte bandbreedte beschikbaar is voor de site-naar-site-verbinding, kunt u overwegen Azure Database Migration Service te gebruiken in de hybride modus (preview). De hybride modus maakt gebruik van een on-premises migratiewerkrol en een exemplaar van Azure Database Migration Service dat in de cloud wordt uitgevoerd. Als u een exemplaar van Azure Database Migration Service in de hybride modus wilt maken, raadpleegt u het artikel [Create an instance of the Azure Database Migration Service by using the Azure portal](./quickstart-create-data-migration-service-portal.md) (Een exemplaar van Azure Database Migration Service maken in de hybride modus met behulp van de Azure-portal).

    > [!IMPORTANT]
    > Met betrekking tot het opslagaccount die wordt gebruikt als onderdeel van de migratie, moet u op een van de volgende manieren te werk gaan:
    > * Het volledige netwerk toegang geven tot het opslagaccount.
    > * Schakel in het MI-subnet [subnetdelegering](../virtual-network/manage-subnet-delegation.md) in werk de firewallregels van het opslagaccount bij om dit subnet toe te staan.

* Zorg ervoor dat de regels voor de netwerk beveiligings groep van uw virtuele netwerk de uitgaande poort 443 van ServiceTag voor ServiceBus, Storage en AzureMonitor niet blok keren. Zie het artikel [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](../virtual-network/virtual-network-vnet-plan-design-arm.md) voor meer informatie over verkeer filteren van verkeer via de netwerkbeveiligingsgroep voor virtuele netwerken.
* Configureer uw [Windows-firewall voor toegang tot de engine van de brondatabase](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Stel uw Windows-firewall open voor toegang van Azure Database Migration Service tot de brondatabase van SQL Server. Standaard verloopt dit via TCP-poort 1433. Als uw standaardexemplaar op een andere poort luistert, voegt u deze toe aan de firewall.
* Als u meerdere benoemde SQL Server-exemplaren uitvoert met behulp van dynamische poorten, kunt u desgewenst de SQL Browser Service inschakelen en toegang tot de UDP-poort 1434 via uw firewalls toestaan, zodat Azure Database Migration Service verbinding kan maken met een benoemd exemplaar op uw bronserver.
* Als u een firewallapparaat gebruikt vóór de brondatabases, moet u mogelijk firewallregels toevoegen om Azure Database Migration Service toegang te geven tot de brondatabase(s) voor migratie. Hetzelfde geldt voor bestanden via SMB-poort 445.
* Maak een SQL Managed Instance aan de hand van de instructies in het artikel [Een SQL Managed Instance maken in de Azure-portal](../azure-sql/managed-instance/instance-create-quickstart.md).
* Zorg ervoor dat de aanmeldingsreferenties die worden gebruikt om het bronexemplaar van SQL Server en het doelexemplaar van SQL Managed Instance te verbinden, lid zijn van de serverrol sysadmin.
* Richt een SMB-netwerkshare in die volledige back-upbestanden bevat van uw database en latere back-logbestanden van transacties die Azure Database Migration Service voor migratie van de database kan gebruiken.
* Zorg ervoor dat het serviceaccount van het bronexemplaar van SQL Server schrijfbevoegdheid heeft voor de netwerkshare die u hebt gemaakt en dat het computeraccount voor de bronserver lees-/schrijftoegang heeft tot de share.
* Maak een notitie van een Windows-gebruiker (en wachtwoord) die volledig beheer heeft over de netwerkshare die u eerder hebt gemaakt. Azure Database Migration Service imiteert de gebruikersreferenties voor het uploaden van de back-upbestanden naar een Azure-opslagcontainer voor herstelbewerkingen.
* Maak een Azure Active Directory-toepassings-id die de toepassings-id-sleutel genereert die Azure Database Migration Service kan gebruiken genereert om verbinding te maken met het beheerde doelexemplaar voor Azure SQL Database en Azure Storage Container. Zie het artikel [Portal gebruiken voor het maken van een Azure Active Directory-toepassing en een -service-principal die toegang hebben tot resources](../active-directory/develop/howto-create-service-principal-portal.md) voor meer informatie.

  > [!NOTE]
  > Voor Azure Database Migration Service is de machtiging Inzender vereist voor het abonnement voor de opgegeven toepassings-ID. U kunt ook aangepaste rollen maken die de specifieke machtigingen verlenen die Azure Database Migration Service vereist. Voor stapsgewijze instructies over het gebruik van aangepaste rollen leest u het artikel [Custom roles for SQL Server to Azure SQL Managed Instance online migrations](./resource-custom-roles-sql-db-managed-instance.md) (Aangepaste rollen voor onlinemigraties van SQL Server naar SQL Managed instance).

* Maak of noteer een Azure Storage-account in de **Standard-prestatielaag** waarmee de DMS-service de back-upbestanden van de database kan uploaden en gebruiken voor het migreren van databases.  Zorg ervoor dat u het Azure Storage-account maakt in de regio waarin het exemplaar van Azure Database Migration Service wordt gemaakt.

  > [!NOTE]
  > Wanneer u een database die is beveiligd met [Transparent Data Encryption](../azure-sql/database/transparent-data-encryption-tde-overview.md) migreert naar een beheerd exemplaar, moet het corresponderende certificaat van het on-premises exemplaar of exemplaar van Azure VM SQL Server worden gemigreerd voordat de database wordt hersteld. Zie [Een TDE-certificaat migreren naar een beheerd exemplaar](../azure-sql/database/transparent-data-encryption-tde-overview.md) voor gedetailleerde stappen.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

1. Meld u aan bij de Azure-portal, selecteer **Alle services** en selecteer vervolgens **Abonnementen**.

    ![Portal-abonnementen weergeven](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)

2. Selecteer het abonnement waarin u het Azure Database Migration Service-exemplaar wilt maken en selecteer vervolgens **Resourceproviders**.

    ![Resourceproviders weergeven](media/tutorial-sql-server-to-managed-instance-online/portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer rechts van **Microsoft.DataMigration** de optie **Registreren**.

    ![Resourceprovider registreren](media/tutorial-sql-server-to-managed-instance-online/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Een Azure Database Migration Service-exemplaar maken

1. Selecteer in de Azure-portal + **Een resource maken**, zoek naar **Azure Database Migration Service** en selecteer vervolgens **Azure Database Migration Service** in de vervolgkeuzelijst.

     ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance-online/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service****Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/tutorial-sql-server-to-managed-instance-online/dms-create1.png)

3. Geef in het scherm **Migratieservice maken** een naam op voor de service, het abonnement en een nieuwe of bestaande resourcegroep.

4. Selecteer de locatie waarop u het exemplaar van DMS wilt maken.

5. Selecteer een bestaand virtueel netwerk of maak er een.

    Het virtuele netwerk biedt Azure Database Migration Service toegang tot de bron-SQL Server en de doel-SQL Managed Instance.

    Zie het artikel [Een virtueel netwerk maken met de Azure-portal](../virtual-network/quick-create-portal.md) voor meer informatie over het maken van een virtueel netwerk in de Azure-portal.

    Zie het artikel [Netwerktopologieën voor migraties van SQL Managed Instance met behulp van Azure Database Migration Service](./resource-network-topologies.md) voor aanvullende informatie.

6. Selecteer een SKU in de prijscategorie Premium.

    > [!NOTE]
    > Onlinemigraties worden alleen ondersteund bij gebruik van deze categorie.

    Zie voor meer informatie over de kosten en prijscategorieën de [Pagina met prijzen](https://aka.ms/dms-pricing).

    ![DMS-service starten](media/tutorial-sql-server-to-managed-instance-online/dms-create-service3.png)

7. Selecteer **Maken** om de dienst te maken.

## <a name="create-a-migration-project"></a>Een migratieproject maken

Nadat er een exemplaar van de service is gemaakt, zoekt u het exemplaar in de Azure-portal, opent u het en maakt u vervolgens een nieuw migratieproject.

1. Selecteer in de Azure-portal **Alle diensten**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service**.

    ![Zoek alle exemplaren van Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. Zoek in het scherm **Azure Database Migration Service** naar de naam van het exemplaar dat u hebt gemaakt en selecteer vervolgens het exemplaar.

3. Selecteer + **Nieuw migratieproject**.

4. Geef in het scherm **Nieuw migratieproject** een naam op voor het project, in het tekstvak **Bronservertype**, selecteer **SQL Server**, in het tekstvak **Type doelserver**, selecteer **Azure SQL Managed Instance** en selecteer vervolgens **Onlinegegevensmigratie** voor **Type activiteit kiezen**.

   ![Azure Database Migration Service-project maken](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. Selecteer **Maken en uitvoeren van de activiteit** om het project te maken.

## <a name="specify-source-details"></a>Geef brondetails op

1. Geef in het scherm **Brongegevens** de verbindingsgegevens op voor de brondatabase van SQL Server.

2. Als u geen vertrouwd certificaat hebt geïnstalleerd op de server, schakelt u het selectievakje **Servercertificaat vertrouwen** in.

    Wanneer er geen vertrouwd certificaat is geïnstalleerd, genereert SQL Server een zelfondertekend certificaat wanneer het exemplaar wordt gestart. Dit certificaat wordt gebruikt voor het versleutelen van de referenties voor clientverbindingen.

    > [!CAUTION]
    > TLS-verbindingen die worden versleuteld met behulp van een zelfondertekend certificaat bieden geen sterke beveiliging. Ze zijn vatbaar voor man-in-the-middle-aanvallen. U moet niet vertrouwen op TLS met behulp van zelfondertekende certificaten in een productieomgeving of op servers die zijn verbonden met internet.

   ![Brondetails](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. Selecteer **Opslaan**.

4. Selecteer in het scherm **Brondatabases selecteren** de database **Adventureworks2012** voor migratie.

   ![Brondatabases selecteren](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > Als u SSIS (SQL Server Integration Services) gebruikt, biedt DMS momenteel geen ondersteuning voor het migreren van de catalogusdatabase voor uw SSIS-projecten/pakketten (SSISDB) van SQL Server naar SQL Managed Instance. U kunt SSIS echter in Azure Data Factory (ADF) inrichten en uw SSIS-projecten/-pakketten opnieuw implementeren naar de bestemmings-SSISDB die wordt gehost door SQL Managed Instance. Zie het artikel [Pakketten van SQL Server Integration Services migreren naar Azure](./how-to-migrate-ssis-packages.md) voor meer informatie over het migreren van SSIS-pakketten.

5. Selecteer **Opslaan**.

## <a name="specify-target-details"></a>Doeldetails opgeven

1. Geef op het scherm **Details migratiedoel** waarden op voor **Toepassings-id** en **Sleutel** die het DMS-exemplaar kan gebruiken om verbinding te maken met het doelexemplaar van SQL Managed Instance en het Azure Storage-account.

    Zie het artikel [Portal gebruiken voor het maken van een Azure Active Directory-toepassing en een -service-principal die toegang hebben tot resources](../active-directory/develop/howto-create-service-principal-portal.md) voor meer informatie.

2. Selecteer het **abonnement** met het doelexemplaar van SQL Managed Instance en selecteer vervolgens het doelexemplaar.

    Als u de SQL Managed Instance nog niet hebt ingericht, selecteert u de [koppeling](../azure-sql/managed-instance/instance-create-quickstart.md) voor instructies voor het inrichten van het exemplaar. Wanneer de SQL Managed Instance klaar is, gaat u terug naar dit specifieke project om de migratie uit te voeren.

3. Geef waarden op voor **SQL-gebruiker** en **Wachtwoord** om verbinding te maken met de SQL Managed Instance.

    ![Doel selecteren](media/tutorial-sql-server-to-managed-instance-online/dms-target-details3.png)

4. Selecteer **Opslaan**.

## <a name="select-source-databases"></a>Brondatabases selecteren

1. Selecteer in het scherm **Brondatabases selecteren** de brondatabase die u wilt migreren.

    ![Brondatabases selecteren](media/tutorial-sql-server-to-managed-instance-online/dms-select-source-databases2.png)

2. Selecteer **Opslaan**.

## <a name="configure-migration-settings"></a>Migratie-instellingen configureren

1. Geef in het scherm **Migratie-instellingen configureren** de volgende instellingen op:

    | | |
    |--------|---------|
    |**SMB-netwerksharelocatie** | De lokale SMB-netwerkshare of Azure-bestandsshare die de volledige back-upbestanden bevat van uw database en latere back-logbestanden van transacties die Azure Database Migration Service kan gebruiken voor migratie. Het serviceaccount waarmee het SQL Server-bronexemplaar wordt uitgevoerd, moet lees-\schrijfbevoegdheid op deze netwerkshare hebben. Geef een FQDN-naam of IP-adressen op van de server in de netwerkshare, bijvoorbeeld '\\\servernaam.domeinnaam.com\back-upmap' of '\\\IP-adres\back-upmap'. Voor betere prestaties kunt u het beste afzonderlijke mappen gebruiken voor elke database die moet worden gemigreerd. U kunt het pad naar de bestandsshare op databaseniveau opgeven met behulp van de optie **Geavanceerde instellingen**. Als u problemen ondervindt met het maken van verbinding met de SMB-share, raadpleegt u [SMB-share](known-issues-azure-sql-db-managed-instance-online.md#smb-file-share-connectivity). |
    |**Gebruikersnaam** | Zorg ervoor dat de Windows-gebruiker volledig beheer heeft over de netwerkshare die u hierboven hebt opgegeven. Azure Database Migration Service imiteert de gebruikersreferenties voor het uploaden van de back-upbestanden naar een Azure-opslagcontainer voor herstelbewerkingen. Als u een Azure-bestandsshare gebruikt, gebruikt u de naam van het opslagaccount voorafgegaan door AZURE\ als de gebruikersnaam. |
    |**Wachtwoord** | Het wachtwoord voor de gebruiker. Als u een Azure-bestandsshare gebruikt, gebruikt u een sleutel voor het opslagaccount als wachtwoord. |
    |**Abonnement van het Azure Storage-account** | Selecteer het abonnement met het Azure Storage-account. |
    |**Azure Storage-account** | Selecteer het Azure Storage-account waarnaar DMS de back-upbestanden kan uploaden vanuit de SMB-netwerkshare en vervolgens kan gebruiken voor migratie van de database.  Het is raadzaam om het Storage-account te selecteren in dezelfde regio als de DMS-service voor optimale prestaties bij het uploaden van de bestanden. |

    ![Migratie-instellingen configureren](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

    > [!NOTE]
    > Als Azure Database Migration Service de fout 'Systeemfout 53' of 'Systeemfout 57' weergeeft, kan dit gebeuren omdat Azure Database Migration Service geen toegang heeft tot de Azure-bestandsshare. Als een van deze fouten optreedt, moet u toegang verlenen tot het opslagaccount vanuit het virtuele netwerk. Dit kan met behulp van [deze instructies](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network).

    > [!IMPORTANT]
    > Als de functionaliteit voor loopback-controle is ingeschakeld en de bron SQL-server en de bestandsshare zich op dezelfde computer bevinden, kan de bron niet via FQDN toegang krijgen tot de bestandsshare. U kunt dit probleem oplossen door loopback-controle uit te schakelen met behulp van de instructies [hier](https://support.microsoft.com/help/926642/error-message-when-you-try-to-access-a-server-locally-by-using-its-fqd).

2. Selecteer **Opslaan**.

## <a name="review-the-migration-summary"></a>Migratieoverzicht controleren

1. Geef in het tekstvak **Naam activiteit** van het scherm **Migratieoverzicht** een naam op voor de migratieactiviteit.

2. Bekijk en controleer de gegevens van het migratieproject.

    ![Overzicht van migratieproject](media/tutorial-sql-server-to-managed-instance-online/dms-project-summary3.png)

## <a name="run-and-monitor-the-migration"></a>De migratie uitvoeren en controleren

1. Selecteer **Migratie uitvoeren**.

2. Selecteer op het scherm met migratieactiviteit **Vernieuwen** om de weergave bij te werken.

   ![Migratieactiviteit wordt uitgevoerd](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration2.png)

    U kunt de databases en aanmeldingscategorieën verder uitvouwen om de migratiestatus van de respectieve serverobjecten te controleren.

   ![Activiteitsstatus van migratie](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration-extend2.png)

## <a name="performing-migration-cutover"></a>Migratie-cutover uitvoeren

Nadat de volledige databaseback-up is hersteld op het doelexemplaar van SQL Managed Instance, is de database beschikbaar voor het uitvoeren van een migratie-cutover.

1. Wanneer u klaar bent om de onlinedatabasemigratie te voltooien, selecteert u **Cutover starten**.

2. Stop al het binnenkomende verkeer naar brondatabases.

3. Neem het bestand [tail-log backup], maak het back-upbestand beschikbaar in de SMB-netwerkshare en wacht totdat deze laatste transactielogboekback-up is hersteld.

    U ziet dat op dat moment **Wijzigingen in behandeling** wordt ingesteld op 0.

4. Selecteer **Bevestigen** en selecteer vervolgens **Toepassen**.

    ![Voltooien van cutover voorbereiden](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

    > [!IMPORTANT]
    > Na de cutover kan het aanzienlijk langer duren voordat SQL Managed Instance met alleen de servicelaag Bedrijfskritiek beschikbaar is dan Algemeen gebruik omdat er drie secundaire replica's moeten worden geseed voor de AlwaysOn-beschikbaarheidsgroep. De duur van deze bewerking is afhankelijk van de omvang van de gegevens. Zie [Duur](../azure-sql/managed-instance/management-operations-overview.md#duration)voor meer informatie.

5. Wanneer de migratie de status **Voltooid** heeft, verbindt u uw toepassingen met het nieuwe doelexemplaar van SQL Managed Instance.

    ![Cutover voltooit](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [Een back-up herstellen naar SQL Managed Instance met behulp van de opdracht T-SQL RESTORE](../azure-sql/managed-instance/restore-sample-database-quickstart.md) voor een zelfstudie waarin wordt uitgelegd hoe u een database migreert naar SQL Managed Instance met de opdracht T-SQL RESTORE.
* Zie [Wat is SQL Managed Instance?](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) voor meer informatie over SQL Managed Instance.
* Zie [Verbinding maken met toepassingen](../azure-sql/managed-instance/connect-application-instance.md) voor meer informatie over het verbinden van apps met SQL Managed Instance.