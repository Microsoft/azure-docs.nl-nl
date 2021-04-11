---
title: 'Zelfstudie: SQL Server naar SQL Managed Instance migreren'
titleSuffix: Azure Database Migration Service
description: Leer hoe u migreert van SQL Server naar een Azure SQL Managed Instance met behulp van Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019,fasttrack-edit
ms.topic: tutorial
ms.date: 01/08/2020
ms.openlocfilehash: 2dfcb4ade9da76d5ec4883e9f4b5b3cb3e0c8682
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076833"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-managed-instance-offline-using-dms"></a>Zelfstudie: SQL Server migreren naar een offline Azure SQL Managed Instance met behulp van DMS

U kunt Azure Database Migration Service gebruiken om de databases te migreren van een SQL Server-exemplaar naar een [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md). In het artikel [Een SQL Server-exemplaar migreren naar SQL Managed Instance](../azure-sql/managed-instance/migrate-to-instance-from-sql-server.md) vindt u aanvullende methoden, waarvoor enkele handmatige stappen nodig kunnen zijn.

In deze zelfstudie migreert u de database **Adventureworks2012** van een on-premises exemplaar van SQL Server naar een SQL Managed Instance. Dit doet u met behulp van Azure Database Migration Service.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> - Maak een exemplaar van de Azure Database Migration Service.
> - Maak een migratieproject met behulp van Azure Database Migration Service.
> - De migratie uitvoeren.
> - Houd de migratie in de gaten.
> - Een migratierapport downloaden.

> [!IMPORTANT]
> Voor offline migraties van SQL Server naar SQL Managed Instance kan Azure Database Migration Service de back-upbestanden voor u maken. U kunt ook de laatste volledige back-up van de database in de SMB-netwerkshare opgeven die door de service wordt gebruikt om uw databases te migreren. Voeg geen meerdere back-ups toe aan één back-upmedium, maar maak elke back-up in een afzonderlijk back-upbestand. U kunt ook gecomprimeerde back-ups gebruiken om de kans te verkleinen op problemen bij het migreren van grote back-ups.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

In dit artikel wordt een offline migratie vanuit SQL Server naar een SQL Managed Instance beschreven. Zie [SQL Server online migreren naar een SQL Managed Instance met behulp van DMS](tutorial-sql-server-managed-instance-online.md) voor informatie over een online migratie.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

- Maak een Microsoft Azure Virtual Network voor Azure Database Migration Service met behulp van het Azure Resource Manager-implementatiemodel. Dit geeft site-naar-site-verbinding met uw on-premises bronservers met behulp van [ExpressRoute](../expressroute/expressroute-introduction.md) of [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). [Lees hier meer over netwerktopologieën voor migraties van SQL Managed Instance met behulp van Azure Database Migration Service](./resource-network-topologies.md). Voor meer informatie over het maken van een virtueel netwerk raadpleegt u de [Documentatie over virtuele netwerken](../virtual-network/index.yml) en dan met name de quickstart-artikelen met stapsgewijze informatie.

    > [!NOTE]
    > Als u bij de installatie van een virtueel netwerk gebruikmaakt van ExpressRoute met netwerkpeering voor Microsoft, voegt u de volgende service-[eindpunten](../virtual-network/virtual-network-service-endpoints-overview.md) toe aan het subnet waarin de service wordt ingericht:
    > - Eindpunt van de doeldatabase (bijvoorbeeld SQL-eindpunt, Cosmos DB-eindpunt, enzovoort)
    > - Opslageindpunt
    > - Service Bus-eindpunt
    >
    > Deze configuratie is noodzakelijk omdat Azure Database Migration Service geen internetverbinding biedt.

- Zorg ervoor dat de regels voor de netwerk beveiligings groep van uw virtuele netwerk de uitgaande poort 443 van ServiceTag voor ServiceBus, Storage en AzureMonitor niet blok keren. Zie het artikel [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](../virtual-network/virtual-network-vnet-plan-design-arm.md) voor meer informatie over verkeer filteren van verkeer via de netwerkbeveiligingsgroep voor virtuele netwerken.
- Configureer uw [Windows-firewall voor toegang tot de engine van de brondatabase](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Stel uw Windows-firewall open voor toegang van Azure Database Migration Service tot de brondatabase van SQL Server. Standaard verloopt dit via TCP-poort 1433. Als uw standaardexemplaar op een andere poort luistert, voegt u deze toe aan de firewall.
- Als u meerdere benoemde SQL Server-exemplaren uitvoert met behulp van dynamische poorten, kunt u desgewenst de SQL Browser Service inschakelen en toegang tot de UDP-poort 1434 via uw firewalls toestaan, zodat Azure Database Migration Service verbinding kan maken met een benoemd exemplaar op uw bronserver.
- Als u een firewallapparaat gebruikt vóór de brondatabases, moet u mogelijk firewallregels toevoegen om Azure Database Migration Service toegang te geven tot de brondatabase(s) voor migratie. Hetzelfde geldt voor bestanden via SMB-poort 445.
- Maak een SQL Managed Instance aan de hand van de instructies in het artikel [Een SQL Managed Instance maken in de Azure-portal](../azure-sql/managed-instance/instance-create-quickstart.md).
- Zorg ervoor dat de aanmeldingsreferenties die worden gebruikt voor verbinding met het bronexemplaar van SQL Server en het doelexemplaar van SQL Managed Instance lid zijn van de serverrol sysadmin.

    >[!NOTE]
    >Standaard biedt Azure Database Migration Service alleen ondersteuning voor het migreren van SQL-aanmeldingen. U kunt echter het migreren van Windows-aanmeldingen als volgt inschakelen:
    >
    >- Zorg ervoor dat het beheerde exemplaar van SQL Managed AAD-Lees toegang heeft, dat via de Azure Portal kan worden geconfigureerd door een gebruiker met de rol **globale beheerder** .
    >- Configureer uw Azure Database Migration Service-exemplaar om migraties van Windows-gebruikers en -groepen in te schakelen, wat via de Azure-portal, op de pagina Configuratie, kan worden ingesteld. Nadat u deze instelling hebt ingeschakeld, start u de service opnieuw om de wijzigingen van kracht te laten worden.
    >
    > Nadat de service opnieuw is gestart, worden de aanmeldingen van Windows-gebruikers en-groepen weergegeven in de lijst met aanmeldingen die beschikbaar zijn voor migratie. Voor alle aanmeldingen van Windows-gebruikers en-groepen die u migreert, wordt u gevraagd om de bijbehorende domeinnaam op te geven. Gebruikersaccounts voor services (account met domeinnaam NT AUTHORITY) en virtuele gebruikersaccounts (account met domeinnaam NT SERVICE) worden niet ondersteund.

- Maak een netwerkshare die Azure Database Migration Service kan gebruiken om een back-up te maken van de brondatabase.
- Zorg ervoor dat het serviceaccount van het bronexemplaar van SQL Server schrijfbevoegdheid heeft voor de netwerkshare die u hebt gemaakt en dat het computeraccount voor de bronserver lees-/schrijftoegang heeft tot de share.
- Maak een notitie van een Windows-gebruiker (en wachtwoord) die volledig beheer heeft over de netwerkshare die u eerder hebt gemaakt. Azure Database Migration Service imiteert de gebruikersreferenties voor het uploaden van de back-upbestanden naar een Azure-opslagcontainer voor herstelbewerkingen.
- Maak een blob-container en haal de SAS-URI van container op met behulp van de stappen in het artikel [Azure Blob Storage-resources beheren met Storage Explorer](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container). Vergeet niet om alle machtigingen (Lezen, Schrijven, Verwijderen, Lijst) te selecteren in het beleidsvenster terwijl u de SAS-URI maakt. Azure Database Migration Service heeft zo toegang tot de container van het opslagaccount voor het uploaden van de back-upbestanden die worden gebruikt voor het migreren van databases naar SQL Managed Instance.

    > [!NOTE]
    > Azure Database Migration Service biedt geen ondersteuning voor het gebruik van een SAS-token op accountniveau bij het configureren van de instellingen voor het opslagaccount tijdens de stap [Migratie-instellingen configureren](#configure-migration-settings).
    
## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

1. Meld u aan bij de Azure-portal, selecteer **Alle services** en selecteer vervolgens **Abonnementen**.

    ![Portal-abonnementen weergeven](media/tutorial-sql-server-to-managed-instance/portal-select-subscriptions.png)

2. Selecteer het abonnement waarin u het Azure Database Migration Service-exemplaar wilt maken en selecteer vervolgens **Resourceproviders**.

    ![Resourceproviders weergeven](media/tutorial-sql-server-to-managed-instance/portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer rechts van **Microsoft.DataMigration** de optie **Registreren**.

    ![Resourceprovider registreren](media/tutorial-sql-server-to-managed-instance/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Een Azure Database Migration Service-exemplaar maken

1. Selecteer in de Azure-portal + **Een resource maken**, zoek naar **Azure Database Migration Service** en selecteer vervolgens **Azure Database Migration Service** in de vervolgkeuzelijst.

    ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service****Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/tutorial-sql-server-to-managed-instance/dms-create1.png)

3. Geef in het scherm **Migratieservice maken** een naam op voor de service, het abonnement en een nieuwe of bestaande resourcegroep.

4. Selecteer de locatie waarop u het exemplaar van DMS wilt maken.

5. Selecteer een bestaand virtueel netwerk of maak er een.

    Het virtuele netwerk biedt Azure Database Migration Service toegang tot de bron-SQL Server en de doel-SQL Managed Instance.

    Zie het artikel [Een virtueel netwerk maken met de Azure-portal](../virtual-network/quick-create-portal.md) voor meer informatie over het maken van een virtueel netwerk in de Azure-portal.

    Zie het artikel [Netwerktopologieën voor migraties van Azure SQL Managed Instance met behulp van Azure Database Migration Service](./resource-network-topologies.md) voor aanvullende informatie.

6. Selecteer een prijscategorie.

    Zie voor meer informatie over de kosten en prijscategorieën de [Pagina met prijzen](https://aka.ms/dms-pricing).

    ![DMS-service starten](media/tutorial-sql-server-to-managed-instance/dms-create-service2.png)

7. Selecteer **Maken** om de dienst te maken.

## <a name="create-a-migration-project"></a>Een migratieproject maken

Nadat er een exemplaar van de service is gemaakt, zoekt u het exemplaar in de Azure-portal, opent u het en maakt u vervolgens een nieuw migratieproject.

1. Selecteer in de Azure-portal **Alle diensten**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service**.

    ![Zoek alle exemplaren van Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance/dms-search.png)

2. Zoek in het scherm **Azure Database Migration Service** naar de naam van het exemplaar dat u hebt gemaakt en selecteer vervolgens het exemplaar.

3. Selecteer + **Nieuw migratieproject**.

4. Geef in het scherm **Nieuw migratieproject** een naam op voor het project in het tekstvak **Bronservertype**, selecteer **SQL Server**, selecteer in het tekstvak **Type doelserver** **Azure SQL Managed Instance** en selecteer vervolgens **Offlinegegevensmigratie** voor **Type activiteit kiezen**.

   ![DMS-project maken](media/tutorial-sql-server-to-managed-instance/dms-create-project2.png)

5. Selecteer **Maken** om het project te maken.

## <a name="specify-source-details"></a>Geef brondetails op

1. Geef in het scherm **Brongegevens** de verbindingsgegevens op voor de brondatabase van SQL Server.

2. Als u geen vertrouwd certificaat hebt geïnstalleerd op de server, schakelt u het selectievakje **Servercertificaat vertrouwen** in.

    Wanneer er geen vertrouwd certificaat is geïnstalleerd, genereert SQL Server een zelfondertekend certificaat wanneer het exemplaar wordt gestart. Dit certificaat wordt gebruikt voor het versleutelen van de referenties voor clientverbindingen.

    > [!CAUTION]
    > TLS-verbindingen die worden versleuteld met behulp van een zelfondertekend certificaat bieden geen sterke beveiliging. Ze zijn vatbaar voor man-in-the-middle-aanvallen. U moet niet vertrouwen op TLS met behulp van zelfondertekende certificaten in een productieomgeving of op servers die zijn verbonden met internet.

   ![Brondetails](media/tutorial-sql-server-to-managed-instance/dms-source-details1.png)

3. Selecteer **Opslaan**.

4. Selecteer in het scherm **Brondatabases selecteren** de database **Adventureworks2012** voor migratie.

   ![Brondatabases selecteren](media/tutorial-sql-server-to-managed-instance/dms-source-database1.png)

    > [!IMPORTANT]
    > Als u SSIS (SQL Server Integration Services) gebruikt, biedt DMS momenteel geen ondersteuning voor het migreren van de catalogusdatabase voor uw SSIS-projecten/pakketten (SSISDB) van SQL Server naar SQL Managed Instance. U kunt SSIS echter in Azure Data Factory (ADF) inrichten en uw SSIS-projecten/-pakketten opnieuw implementeren naar de bestemmings-SSISDB die wordt gehost door SQL Managed Instance. Zie het artikel [Pakketten van SQL Server Integration Services migreren naar Azure](./how-to-migrate-ssis-packages.md) voor meer informatie over het migreren van SSIS-pakketten.

5. Selecteer **Opslaan**.

## <a name="specify-target-details"></a>Doeldetails opgeven

1. Geef in het scherm **Details migratiedoel** de verbindingsgegevens op voor het doel, dat bestaat uit de vooraf ingerichte SQL Managed Instance waarnaar u de database **AdventureWorks2012** migreert.

    Als u de SQL Managed Instance nog niet hebt ingericht, selecteert u de [koppeling](../azure-sql/managed-instance/instance-create-quickstart.md) voor instructies voor het inrichten van het exemplaar. U kunt gewoon verdergaan met het maken van het project en wanneer de SQL Managed Instance klaar is, gaat u terug naar dit specifieke project om de migratie uit te voeren.

    ![Doel selecteren](media/tutorial-sql-server-to-managed-instance/dms-target-details2.png)

2. Selecteer **Opslaan**.

## <a name="select-source-databases"></a>Brondatabases selecteren

1. Selecteer in het scherm **Brondatabases selecteren** de brondatabase die u wilt migreren.

    ![Brondatabases selecteren](media/tutorial-sql-server-to-managed-instance/select-source-databases.png)

2. Selecteer **Opslaan**.

## <a name="select-logins"></a>Aanmeldingen selecteren

1. Selecteer in het scherm **Aanmeldingen selecteren** de aanmeldingen die u wilt migreren.

    >[!NOTE]
    >Standaard biedt Azure Database Migration Service alleen ondersteuning voor het migreren van SQL-aanmeldingen. Als u ondersteuning wilt inschakelen voor het migreren van Windows-aanmeldingen, raadpleegt u de sectie **Vereisten** van deze zelfstudie.

    ![Aanmeldingen selecteren](media/tutorial-sql-server-to-managed-instance/select-logins.png)

2. Selecteer **Opslaan**.

## <a name="configure-migration-settings"></a>Migratie-instellingen configureren

1. Geef in het scherm **Migratie-instellingen configureren** de volgende instellingen op:

    | Parameter | Beschrijving |
    |--------|---------|
    |**De back-upoptie voor de bron kiezen** | Kies de optie **Ik lever de recentste back-upbestanden** wanneer u al beschikt over een volledige set back-upbestanden die DMS kan gebruiken voor migratie van de database. Kies de optie **Ik laat Azure Database Migration Service back-upbestanden maken** als u wilt dat DMS de volledige back up van de brondatabase gebruikt voor migratie. |
    |**De netwerksharelocatie waarheen back-ups van databases kunnen worden verplaatst door Azure Database Migration Service** | De lokale SMB-netwerkshare waarop Azure Database Migration Service back-ups van de brondatabase kan opslaan. Het serviceaccount waarmee het SQL Server-bronexemplaar wordt uitgevoerd, moet schrijfbevoegdheid op deze netwerkshare hebben. Geef een FQDN-naam of IP-adressen op van de server in de netwerkshare, bijvoorbeeld '\\\servernaam.domeinnaam.com\back-upmap' of '\\\IP-adres\back-upmap'.|
    |**Gebruikersnaam** | Zorg ervoor dat de Windows-gebruiker volledig beheer heeft over de netwerkshare die u hierboven hebt opgegeven. Azure Database Migration Service imiteert de gebruikersreferenties voor het uploaden van de back-upbestanden naar een Azure-opslagcontainer voor herstelbewerkingen. Als TDE-compatibele databases zijn geselecteerd voor migratie, moet de bovenstaande Windows-gebruiker het ingebouwde administratoraccount zijn en moet [Gebruikersaccountbeheer](/windows/security/identity-protection/user-account-control/user-account-control-overview) zijn uitgeschakeld om Azure Database Migration Service in staat te stellen de certificaatbestanden te uploaden en verwijderen. |
    |**Wachtwoord** | Het wachtwoord voor de gebruiker. |
    |**Instellingen voor een opslagaccount** | De SAS-URI die Azure Database Migration Service toegang biedt tot de container van het opslagaccount waarnaar de service de back-upbestanden uploadt en die wordt gebruikt voor het migreren van databases naar SQL Managed Instance. [Lees hier meer over het verkrijgen van de SAS-URI voor de blob-container](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container). Deze SAS-URI moet bestemd zijn voor de blobcontainer, niet voor het opslagaccount.|
    |**TDE-instellingen** | Als u de brondatabases migreert met TDE (Transparent Data Encryption) ingeschakeld, moet u schrijfrechten hebben voor de doel-SQL Managed Instance.  Selecteer in de vervolgkeuzelijst het abonnement waarin de SQL Managed Instance is ingericht.  Selecteer het **beheerde doelexemplaar voor Azure SQL Database** in de vervolgkeuzelijst. |

    ![Migratie-instellingen configureren](media/tutorial-sql-server-to-managed-instance/dms-configure-migration-settings3.png)

2. Selecteer **Opslaan**.

## <a name="review-the-migration-summary"></a>Migratieoverzicht controleren

1. Geef in het tekstvak **Naam activiteit** van het scherm **Migratieoverzicht** een naam op voor de migratieactiviteit.

2. Vouw de sectie **Validatieoptie** uit om het scherm **Validatieoptie kiezen** weer te geven en geef op of de gemigreerde databases moeten worden gevalideerd op juistheid van de query's. Selecteer vervolgens **Opslaan**.

3. Bekijk en controleer de gegevens van het migratieproject.

    ![Overzicht van migratieproject](media/tutorial-sql-server-to-managed-instance/dms-project-summary2.png)

4. Selecteer **Opslaan**.

## <a name="run-the-migration"></a>De migratie uitvoeren

- Selecteer **Migratie uitvoeren**.

  Het venster van de migratieactiviteit wordt weergegeven en de Status van de activiteit is **In behandeling**.

## <a name="monitor-the-migration"></a>Bewaak de migratie

1. Selecteer in het scherm van de migratieactiviteit **Vernieuwen** om de weergave bij te werken.

   ![Schermopname die het migratieactiviteitsscherm en de knop Vernieuwen laat zien.](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration1.png)

    U kunt de databases en aanmeldingscategorieën verder uitvouwen om de migratiestatus van de respectieve serverobjecten te controleren.

   ![Migratieactiviteit wordt uitgevoerd](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration-extend.png)

2. Nadat de migratie is voltooid, selecteert u **Rapport downloaden** om een rapport op te halen met gegevens die zijn gekoppeld aan het migratieproces.

3. Controleer of de doeldatabase aanwezig is op de doel-SQL Managed Instance.

## <a name="next-steps"></a>Volgende stappen

- Zie [Een back-up herstellen naar SQL Managed Instance met behulp van de opdracht T-SQL RESTORE](../azure-sql/managed-instance/restore-sample-database-quickstart.md) voor een zelfstudie waarin wordt uitgelegd hoe u een database migreert naar SQL Managed Instance met de opdracht T-SQL RESTORE.
- Zie [Wat is SQL Managed Instance?](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) voor meer informatie over SQL Managed Instance.
- Zie [Verbinding maken met toepassingen](../azure-sql/managed-instance/connect-application-instance.md) voor meer informatie over het verbinden van apps met SQL Managed Instance.
