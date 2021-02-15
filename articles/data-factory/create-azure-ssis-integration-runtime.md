---
title: Een Azure SSIS Integration runtime in Azure Data Factory maken
description: Meer informatie over het maken van een Azure SSIS Integration runtime in Azure Data Factory zodat u SSIS-pakketten kunt implementeren en uitvoeren in Azure.
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/13/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 4e3137b08c558c8e9dfadda07f0b8bb66433ee83
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389413"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Een Azure SSIS Integration runtime in Azure Data Factory maken

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel worden de stappen beschreven voor het inrichten van een Azure-SQL Server Integration Services (SSIS) Integration runtime (IR) in Azure Data Factory (ADF). Een Azure-SSIS IR ondersteunt:

- Pakketten die zijn geïmplementeerd in SSIS Catalog (SSISDB) die worden gehost door een server of beheerd exemplaar van Azure SQL Database (projectimplementatiemodel)
- Pakketten die zijn geïmplementeerd in het bestandssysteem, Azure Files of SQL Server-database (MSDB) die worden gehost door Azure SQL Managed Instance (pakketimplementatiemodel)

Nadat een Azure-SSIS IR is ingericht, kunt u vertrouwde hulpprogramma's gebruiken om uw pakketten in Azure te implementeren en uit te voeren. Deze hulpprogramma's zijn al ingeschakeld voor Azure en bevatten SQL Server Data Tools (SSDT), SQL Server Management Studio (SSMS) en opdrachtregelprogramma's zoals [dtutil](/sql/integration-services/dtutil-utility) en [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md).

In de zelf studie voor het [inrichten van Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md) ziet u hoe u een Azure-SSIS IR maakt via de Azure portal of de Data Factory-app. In de zelf studie ziet u ook hoe u optioneel een Azure SQL Database Server of een beheerd exemplaar kunt gebruiken om SSISDB te hosten. In dit artikel wordt de zelf studie uitgebreid en wordt beschreven hoe u deze optionele taken kunt uitvoeren:

- Gebruik een Azure SQL Database Server met IP-firewall regels/virtuele netwerk service-eind punten of een beheerd exemplaar met een persoonlijk eind punt om SSISDB te hosten. Als vereiste moet u de machtigingen en instellingen van het virtuele netwerk voor uw Azure-SSIS IR configureren om lid te worden van een virtueel netwerk.

- Gebruik Azure Active Directory-verificatie (Azure AD) met de beheerde identiteit voor uw data factory om verbinding te maken met een Azure SQL Database Server of een beheerd exemplaar. Als vereiste moet u de beheerde identiteit voor uw data factory toevoegen als een database gebruiker die een SSISDB-exemplaar kan maken.

- Voeg uw Azure-SSIS IR toe aan een virtueel netwerk of configureer een zelf-hostende IR als proxy voor uw Azure-SSIS IR om toegang te krijgen tot gegevens on-premises.

In dit artikel wordt beschreven hoe u een Azure-SSIS IR inricht met behulp van de Azure Portal, Azure PowerShell en een Azure Resource Manager sjabloon.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-abonnement**. Als u nog geen abonnement hebt, kunt u een [gratis proef](https://azure.microsoft.com/pricing/free-trial/) account maken.

- **Azure SQL database server of SQL Managed instance (optioneel)**. Als u nog geen database server of een beheerd exemplaar hebt, maakt u er een in de Azure Portal voordat u aan de slag gaat. Met Data Factory wordt vervolgens een SSISDB-exemplaar op deze databaseserver gemaakt. 

  U wordt aangeraden om de database server of het beheerde exemplaar te maken in dezelfde Azure-regio als de Integration runtime. Met deze configuratie kan de Integration Runtime uitvoeringslogboeken wegschrijven naar SSISDB zonder dat hierbij Azure-regio's worden overschreden.

  Houd de volgende zaken in gedachten:

  - Het SSISDB-exemplaar kan namens u worden gemaakt als één data base, als onderdeel van een elastische pool of in een beheerd exemplaar. Deze is toegankelijk in een openbaar netwerk of kan worden toegevoegd aan een virtueel netwerk. Zie de sectie [SQL database en SQL Managed instance vergelijken](#comparison-of-sql-database-and-sql-managed-instance) in dit artikel voor hulp bij het kiezen tussen SQL database en SQL Managed instance voor het hosten van SSISDB. 
  
    Als u een Azure SQL Database Server met IP-firewall regels/virtuele netwerk service-eind punten of een door SQL beheerd exemplaar met een persoonlijk eind punt gebruikt om SSISDB te hosten, of als u toegang tot on-premises gegevens nodig hebt zonder een zelf-hostende IR te configureren, moet u uw Azure-SSIS IR toevoegen aan een virtueel netwerk. Zie [een Azure-SSIS IR toevoegen aan een virtueel netwerk](./join-azure-ssis-integration-runtime-virtual-network.md)voor meer informatie.

  - Controleer of de instelling **Toegang tot Azure-services toestaan** is ingeschakeld voor de databaseserver. Deze instelling is niet van toepassing wanneer u een Azure SQL Database Server met IP-firewall regels/virtuele netwerk service-eind punten of een door SQL beheerd exemplaar met een persoonlijk eind punt gebruikt om SSISDB te hosten. Zie [Secure Azure SQL Database](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules) (Azure SQL Database beveiligen) voor meer informatie. Zie [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) om deze instelling in te schakelen met behulp van PowerShell.

  - Voeg het IP-adres van de clientcomputer (of een reeks IP-adressen dat het IP-adres van de clientcomputer bevat) toe aan de lijst met client-IP-adressen in de instellingen van de firewall voor de databaseserver. Zie [Overzicht van firewallregels op Azure SQL Database-serverniveau en -databaseniveau](../azure-sql/database/firewall-configure.md) voor meer informatie.

  - U kunt verbinding maken met de databaseserver via SQL-verificatie met de beheerdersreferenties van de server of Azure Active Directory-verificatie met de beheerde identiteit voor uw data factory. Voor het laatste moet u de beheerde identiteit voor uw data factory toevoegen aan een Azure Active Directory-groep met toegangsmachtigingen tot de databaseserver. Zie [Azure AD-verificatie inschakelen voor een Azure-SSIS IR](./enable-aad-authentication-azure-ssis-ir.md)voor meer informatie.

  - Controleer of de databaseserver al een SSISDB-exemplaar heeft. Het inrichten van een Azure-SSIS IR biedt geen ondersteuning voor het gebruik van een bestaand SSIS-exemplaar.

- **Azure Resource Manager virtueel netwerk (optioneel)**. U moet een Azure Resource Manager virtueel netwerk hebben als ten minste één van de volgende voor waarden waar is:

  - U host SSISDB op een Azure SQL Database Server met IP-firewall regels/virtuele netwerk service-eind punten of een beheerd exemplaar met een persoonlijk eind punt.

  - U wilt verbinding maken met on-premises gegevens archieven vanaf SSIS-pakketten die worden uitgevoerd op uw Azure-SSIS IR zonder een zelf-hostende IR te configureren.

- **Azure PowerShell (optioneel)**. Volg de instructies in het [Azure PowerShell installeren en configureren](/powershell/azure/install-az-ps)als u een Power shell-script wilt uitvoeren om uw Azure-SSIS IR in te richten.

### <a name="regional-support"></a>Regionale ondersteuning

Zie [Data Factory en SSIS IR-Beschik baarheid per regio](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)voor een lijst met Azure-regio's waarin Data Factory en een Azure-SSIS IR beschikbaar zijn.

### <a name="comparison-of-sql-database-and-sql-managed-instance"></a>Vergelijking van SQL Database en SQL Managed instance

In de volgende tabel worden bepaalde functies van een Azure SQL Database Server en SQL Managed instance vergeleken met de Azure-SSIR IR:

| Functie | SQL Database| SQL-beheerd exemplaar |
|---------|--------------|------------------|
| **Planning** | De SQL Server Agent is niet beschikbaar.<br/><br/>Zie [een pakket uitvoering plannen in een Data Factory-pijp lijn](/sql/integration-services/lift-shift/ssis-azure-schedule-packages#activity).| De agent Managed instance is beschikbaar. |
| **Verificatie** | U kunt een SSISDB-exemplaar maken met een Inge sloten database gebruiker die een Azure AD-groep vertegenwoordigt met de beheerde identiteit van uw data factory als lid van de **db_owner** rol.<br/><br/>Zie [Azure AD-verificatie inschakelen voor het maken van een SSISDB in Azure SQL database server](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | U kunt een SSISDB-exemplaar maken met een Inge sloten database gebruiker die de beheerde identiteit van uw data factory vertegenwoordigt. <br/><br/>Zie [Azure AD-verificatie inschakelen voor het maken van een SSISDB in Azure SQL Managed instance](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-sql-managed-instance). |
| **Servicelaag** | Wanneer u een Azure-SSIS IR met uw Azure SQL Database-Server maakt, kunt u de servicelaag voor SSISDB selecteren. Er zijn meerdere service lagen. | Wanneer u een Azure-SSIS IR maakt met uw beheerde exemplaar, kunt u de servicelaag voor SSISDB niet selecteren. Alle data bases in uw beheerde exemplaar delen dezelfde resource die aan dat exemplaar is toegewezen. |
| **Virtueel netwerk** | Uw Azure-SSIS IR kan worden toegevoegd aan een Azure Resource Manager virtueel netwerk als u een Azure SQL Database Server met IP-firewall regels/virtuele netwerk service-eind punten gebruikt. | Uw Azure-SSIS IR kan worden toegevoegd aan een Azure Resource Manager virtueel netwerk als u een beheerd exemplaar met een persoonlijk eind punt gebruikt. Het virtuele netwerk is vereist wanneer u geen openbaar eind punt inschakelt voor uw beheerde exemplaar.<br/><br/>Als u uw Azure-SSIS IR lid maakt van hetzelfde virtuele netwerk als uw beheerde exemplaar, moet u ervoor zorgen dat uw Azure-SSIS IR zich in een ander subnet bevindt dan uw beheerde exemplaar. Als u uw Azure-SSIS IR lid maakt van een ander virtueel netwerk dan uw beheerde exemplaar, raden we u aan een peering voor het virtuele netwerk of een netwerk verbinding te maken. Zie [uw toepassing verbinden met een door Azure SQL database beheerd exemplaar](../azure-sql/managed-instance/connect-application-instance.md). |
| **Gedistribueerde transacties** | Deze functie wordt ondersteund via elastische trans acties. MSDTC-trans acties (micro soft gedistribueerde transactie) worden niet ondersteund. Als uw SSIS-pakketten gebruikmaken van MSDTC voor het coördineren van gedistribueerde trans acties, kunt u overwegen om te migreren naar elastische trans acties voor Azure SQL Database. Zie [gedistribueerde trans acties in Cloud databases](../azure-sql/database/elastic-transactions-overview.md)voor meer informatie. | Wordt niet ondersteund. |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>De Azure Portal gebruiken om een Integration runtime te maken

In deze sectie gebruikt u de Azure Portal, met name de Data Factory gebruikers interface (UI) of de app, om een Azure-SSIS IR te maken.

### <a name="create-a-data-factory"></a>Een gegevensfactory maken

Als u uw data factory via de Azure-portal wilt maken, volgt u de stapsgewijze instructies in [Een data factory maken via de gebruikersinterface](./quickstart-create-data-factory-portal.md#create-a-data-factory). Selecteer **Vastmaken aan dashboard** zodat u snel toegang hebt nadat de data factory is gemaakt. 

Nadat uw data factory is gemaakt, opent u de overzichtspagina in de Azure-portal. Selecteer de tegel **auteur & monitor** om de pagina aan de **slag** te openen op een afzonderlijk tabblad. Daar kunt u door gaan met het maken van uw Azure-SSIS IR.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Een Azure-SSIS-integratieruntime inrichten

Selecteer op de pagina **aan de slag** de optie **SSIS Integration runtime** -tegel configureren om het deel venster **installatie van Integration runtime** te openen.

   ![De tegel SSIS-integratieruntime configureren](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

   Het deelvenster **Installatie van integratieruntime** heeft drie pagina's waar u de algemene instellingen, implementatie-instellingen en geavanceerde instellingen kunt configureren.

#### <a name="general-settings-page"></a>Pagina Algemene instellingen

Voer op de pagina **Algemene instellingen** van het deelvenster **Installatie van integratieruntime** de volgende stappen uit:

   ![Algemene instellingen](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. Voer bij **Naam** de naam van de integratieruntime in.

   2. Voer bij **beschrijving** de beschrijving van de integratieruntime in.

   3. Selecteer bij **Locatie** de locatie voor de integratieruntime. Alleen ondersteunde locaties worden weergegeven. We raden u aan dezelfde locatie van uw databaseserver te selecteren voor het hosten van SSISDB.

   4. Selecteer voor **knooppunt grootte** de grootte van het knoop punt in het cluster Integration runtime. Alleen ondersteunde knooppuntgrootten worden weergegeven. Selecteer een grote knooppuntgrootte (omhoog schalen) als u veel reken-/geheugenintensieve pakketten wilt uitvoeren.
   > [!NOTE]
   > Als u [reken isolatie](../azure-government/azure-secure-isolation-guidance.md#compute-isolation)nodig hebt, moet u de grootte van het **Standard_E64i_v3** knooppunt selecteren. Deze knooppunt grootte vertegenwoordigt geïsoleerde virtuele machines die de gehele fysieke host verbruiken en bieden het benodigde isolatie niveau voor bepaalde werk belastingen, zoals het Amerikaanse ministerie van impact 5 (IL5)-workloads van de defensie.
   
   5. Selecteer bij **Aantal knooppunten** het aantal knooppunten in het integratieruntimecluster. Alleen ondersteunde knooppuntaantallen worden weergegeven. Selecteer een groot cluster met veel knooppunten (uitschalen), als u veel pakketten parallel wilt uitvoeren.

   6. Selecteer bij **Editie/licentie** de SQL Server-editie voor uw integratieruntime: Standard of Enterprise. Selecteer Enterprise als u geavanceerde functies in de integratieruntime wilt gebruiken.

   7. Selecteer bij **Geld besparen** de optie Azure Hybrid Benefit voor uw integratieruntime: **Ja** of **Nee**. Selecteer **Ja** als u uw eigen SQL Server-licentie met Software Assurance wilt gebruiken om te profiteren van de kostenbesparingen met hybride gebruik.

   8. Selecteer **Next**.

#### <a name="deployment-settings-page"></a>Pagina Implementatie-instellingen

Op de pagina **Implementatie-instellingen** van het deelvenster **Integratieruntime-instellingen** hebt u de mogelijkheid om SSISDB- en/of Azure-SSIS IR-pakketarchieven te maken.

##### <a name="creating-ssisdb"></a>SSISDB maken

Schakel het selectievakje **SSIS-catalogus maken (SSISDB) die wordt gehost door Azure SQL Database server/beheerd exemplaar om uw projecten/pakketten/omgevingen/uitvoeringslogboeken op te slaan** op de pagina **Implementatie-instellingen** van het deelvenster **Integratieruntime-instellingen** in. Als u uw pakketten wilt implementeren in het bestandssysteem, Azure Files of SQL Server-database (MSDB) die worden gehost door Azure SQL Managed Instance (pakketimplementatiemodel), hoeft u geen SSISDB te maken en hoeft u ook het selectievakje niet in te schakelen.

Schakel dit selectievakje in, ongeacht uw implementatiemodel, als u SQL Server Agent gehost door Azure SQL Managed Instance wilt gebruiken om uw pakketuitvoeringen te plannen, aangezien dit wordt ingeschakeld door SSISDB. Zie [SSIS-pakketuitvoeringen plannen via de agent voor Azure SQL Managed Instance](./how-to-invoke-ssis-package-managed-instance-agent.md) voor meer informatie.
   
Als u het selectievakje inschakelt, moet u de volgende stappen uitvoeren om uw eigen databaseserver in te stellen als host voor de SSISDB die we namens u maken en beheren.

   ![Implementatie-instellingen voor SSISDB](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings.png)
   
   1. Selecteer bij **Abonnement** het Azure-abonnement dat uw databaseserver heeft voor het hosten van SSISDB. 

   1. Selecteer bij **Locatie** de locatie van uw databaseserver voor het hosten van SSISDB. We raden u aan dezelfde locatie van uw integratieruntime te selecteren.

   1. Selecteer bij het **Eindpunt voor de Catalog-databaseserver** het eindpunt van uw databaseserver voor het hosten van SSISDB. 
   
      Op basis van de geselecteerde databaseserver kan het SSISDB-exemplaar namens u worden gemaakt als een enkele database, als onderdeel van een elastische pool of in een beheerd exemplaar. Deze is toegankelijk in een openbaar netwerk of kan worden toegevoegd aan een virtueel netwerk. Zie [SQL Database en een beheerd exemplaar van SQL vergelijken](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance) voor hulp bij het kiezen van het type databaseserver om SSISDB te hosten.   

      Als u een Azure SQL Database-server met IP-firewall regels/service-eindpunten voor virtuele netwerken of een beheerd exemplaar met een privé-eindpunt selecteert om SSISDB te hosten, of als u toegang tot on-premises gegevens nodig hebt zonder een zelf-hostende IR te configureren, moet u uw Azure-SSIS IR toevoegen aan een virtueel netwerk. Zie [Een Azure-SSIS IR maken in een virtueel netwerk]() voor meer informatie.

   1. Schakel het selectievakje **Azure Active Directory-verificatie gebruiken met de beheerde identiteit voor uw ADF** om de verificatiemethode voor uw databaseserver voor het hosten van SSISDB te kiezen. U kiest SQL-verificatie of Azure Active Directory-verificatie met de beheerde identiteit voor uw data factory.

      Als u het selectievakje selecteert, moet u de beheerde identiteit voor uw data factory toevoegen aan een Azure Active Directory-groep met toegangsmachtigingen tot de databaseserver. Zie [Een Azure-SSIS IR met Azure Active Directory-verificatie maken]() voor meer informatie.
   
   1. Voer bij **Gebruikersnaam van beheerder** de gebruikersnaam voor SQL-verificatie voor uw databaseserver voor het hosten van SSISDB in. 

   1. Voer bij **Beheerderswachtwoord** het wachtwoord voor SQL-verificatie voor uw databaseserver voor het hosten van SSISDB in. 

   1. Selecteer bij **Serverlaag catalogusdatabase** de servicelaag voor uw databaseserver voor het hosten van SSISDB. Selecteer de laag Basic, Standard of Premium of selecteer de naam van een elastische pool.

Selecteer **Verbinding testen** wanneer dit van toepassing is en selecteer **Volgende**.

> [!NOTE]
   > Als u Azure SQL Database Server gebruikt om SSISDB te hosten, worden uw gegevens standaard opgeslagen in geografisch redundante opslag voor back-ups. Als u niet wilt dat uw gegevens worden gerepliceerd in andere regio's, volgt u de instructies voor het [configureren van de redundantie van back-upopslag met behulp van Power shell](../azure-sql/database/automated-backups-overview.md?tabs=single-database#configure-backup-storage-redundancy-by-using-powershell).
   
##### <a name="creating-azure-ssis-ir-package-stores"></a>Azure-SSIS IR-pakketarchieven maken

Schakel op de pagina **Implementatie-instellingen** van het deelvenster **Integratieruntime-instellingen** het selectievakje **Pakketarchieven maken om uw pakketten te beheren die zijn geïmplementeerd in het bestandssysteem/Azure Files/SQL Server-database (MSDB) en worden gehost door Azure SQL Managed Instance** in als u uw pakketten wilt beheren die zijn geïmplementeerd in MSDB, het bestandssysteem of Azure Files (pakketimplementatiemodel) met pakketarchieven van Azure-SSIS IR.
   
Met pakketarchieven van Azure-SSIS IR kunt u pakketten importeren/exporteren/verwijderen/uitvoeren en de uitvoering van pakketten controleren of stoppen via SSMS, net zoals met de [verouderde SSIS-pakketarchieven](/sql/integration-services/service/package-management-ssis-service). Zie [SSIS-pakketten beheren met pakketarchieven van Azure-SSIS IR](./azure-ssis-integration-runtime-package-store.md) voor meer informatie.
   
Als u dit selectievakje inschakelt, kunt u meerdere pakketarchieven toevoegen aan uw Azure-SSIS IR door **Nieuwe** te selecteren. Daarnaast kan één pakketarchief worden gedeeld door meerdere Azure-SSIS IR’s.

![Implementatie-instellingen voor MSDB/bestandssysteem/Azure Files](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings2.png)

Voltooi op de pagina **Pakketarchief toevoegen** de volgende stappen.
   
   1. Voer de naam van uw pakketarchief in bij **Naam van pakketarchief**. 

   1. Selecteer voor **Gekoppelde service voor pakketarchief** de bestaande gekoppelde service waarin de toegangsgegevens zijn opgeslagen voor het bestandssysteem/Azure Files/Azure SQL Managed Instance waarin uw pakketten zijn geïmplementeerd of maak een nieuwe door **Nieuwe** te selecteren. Voer in het deelvenster **Nieuwe gekoppelde service** de volgende stappen uit.
   
      > [!NOTE]
      > U kunt een aan **Azure File Storage** of **File System** gekoppelde service gebruiken om toegang te krijgen tot Azure Files. Als u een aan **Azure File Storage** gekoppelde service gebruikt, ondersteunt het Azure-SSIS IR-pakket alleen **Basic** (niet **Accountsleutel** en **SAS URI**) als verificatiemethode. Als u **Basic-** -verificatie wilt gebruiken voor een aan **Azure File Storage** gekoppelde service, kunt u `?feature.upgradeAzureFileStorage=false` toevoegen aan de URL van de ADF-Portal in uw browser. U kunt in plaats daarvan ook een aan **File System** gekoppelde service gebruiken om toegang te krijgen tot Azure Files. 

      ![Implementatie-instellingen voor gekoppelde services](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings-linked-service.png)

      1. Voer bij **Naam** de naam van de gekoppelde service in. 
         
      1. Voer bij **Beschrijving** de beschrijving van de gekoppelde service in. 
         
      1. Voor **Type** selecteert u **Azure File Storage**, **Azure SQL Managed Instance** of **Bestandssysteem**.

      1. U kunt **Verbinding maken via integratieruntime** negeren omdat we altijd uw Azure-SSIS IR gebruiken om de toegangsgegevens voor pakketarchieven op te halen.

      1. Als u **Azure File Storage** selecteert, voert u de volgende stappen uit. 

         1. Selecteer bij **Accountselectiemethode** de optie **Van Azure-abonnement** of **Handmatig invoeren**.
         
         1. Als u **Van Azure-abonnement** hebt gekozen, selecteert u het relevante **Azure-abonnement**, **Naam van opslagaccount** en **Bestandsshare**.
            
         1. Als u **Handmatig invoeren** hebt gekozen, voert u `\\<storage account name>.file.core.windows.net\<file share name>` in bij **Host**, `Azure\<storage account name>` bij **Gebruikersnaam** en `<storage account key>` bij **Wachtwoord**, of u selecteert de **Azure Key Vault** waar het wachtwoord is opgeslagen als geheim.

      1. Als u **Azure SQL Managed Instance** hebt gekozen, voert u de volgende stappen uit. 

         1. Selecteer **Verbindingsreeks** om deze handmatig in te voeren of uw **Azure Key Vault** waar het wachtwoord is opgeslagen als een geheim.
         
         1. Als u **Verbindingsreeks** hebt gekozen, voert u de volgende stappen uit. 

            1. Bij **Fully Qualified Domain Name** voert u `<server name>.<dns prefix>.database.windows.net` of `<server name>.public.<dns prefix>.database.windows.net,3342` in als het privé-eindpunt of openbare eindpunt van uw Azure SQL Managed Instance. Als u het privé-eindpunt opgeeft, is **Verbinding testen** niet van toepassing, omdat de ADF-gebruikersinterface deze niet kan bereiken.

            1. Voer bij **Name database** de invoer `msdb` in.
               
            1. Selecteer **SQL-verificatie**, **Beheerde identiteit** of **Service-principal** bij **Verificatietype**.

            1. Als u **SQL-verificatie** hebt gekozen, voert u de relevante **Gebruikersnaam** en het **Wachtwoord** in of selecteer u de **Azure Key Vault** waar het wachtwoord is opgeslagen als geheim.

            1. Als u **Beheerde identiteit** hebt gekozen, verleent u uw beheerde ADF-identiteit toegang tot uw Azure SQL Managed Instance.

            1. Als u **Service-principal** hebt gekozen, voert u de relevante **Service-principal-id** en de **Service-principal-sleutel** in of selecteert u uw **Azure Key Vault** waar het wachtwoord is opgeslagen als geheim.

      1. Als u **Bestandssysteem** hebt gekozen, voert u het UNC-pad in van de map waarin uw pakketten zijn geïmplementeerd voor **Host**, evenals de relevante **Gebruikersnaam** en het **Wachtwoord** of selecteert u de **Azure Key Vault** waar het wachtwoord is opgeslagen als geheim.

      1. Selecteer **Verbinding testen** wanneer dit van toepassing is en selecteer **Maken**.

   1. De toegevoegde pakketarchieven worden weergegeven op de pagina **Implementatie-instellingen**. Als u deze wilt verwijderen, schakelt u de selectie vakjes in en selecteert u **Verwijderen**.

Selecteer **Verbinding testen** wanneer dit van toepassing is en selecteer **Volgende**.

#### <a name="advanced-settings-page"></a>De pagina Geavanceerde instellingen

Voer op de pagina **Geavanceerde instellingen** van het deelvenster **Installatie van integratieruntime** de volgende stappen uit.

   ![Geavanceerde instellingen](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. Selecteer bij het **Maximale aantal parallelle uitvoeringen per knooppunt** het maximum aantal pakketten dat gelijktijdig per knooppunt kan worden uitgevoerd in het integratieruntimecluster. Alleen ondersteunde pakketaantallen worden weergegeven. Selecteer een klein aantal als u meer dan één kern wilt gebruiken om een enkel groot pakket uit te voeren dat reken- of geheugenintensief is. Selecteer een groot aantal als u een of meer kleine pakketten in één kern wilt uitvoeren.

   1. Schakel het selectievakje **Uw Azure-SSIS Integration Runtime aanpassen met aanvullende systeemconfiguraties/onderdeelinstallaties** in om te kiezen of u aangepaste installaties van standaard/express wilt toevoegen aan uw Azure-SSIS IR. Zie [Aangepaste installatie voor een Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md) voor meer informatie.

      Als u het selectie vakje inschakelt, voert u de volgende stappen uit.

      ![Geavanceerde instellingen met aangepaste installatie](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)
   
      1. Voor de SAS-URI van de **aangepaste installatie container** voert u de SAS-URI in van uw container waarin u scripts en gekoppelde bestanden opslaat voor aangepaste standaard instellingen.

      1. Voor **snelle aangepaste installatie** selecteert u **Nieuw** om het deel venster **snelle aangepaste configuratie toevoegen** te openen en selecteert u vervolgens de gewenste typen onder het vervolg keuzemenu **expliciet aangepaste installatie type** , bijvoorbeeld **opdracht cmdkey**, een **omgevings variabele toevoegen**, **een gelicentieerd onderdeel installeren**, enzovoort.

         Als u het **onderdeel type licentie installeren** selecteert, kunt u in het vervolg keuzemenu **onderdeel naam** alle geïntegreerde onderdelen van onze ISV-partners selecteren en, indien nodig, de product licentie sleutel invoeren/het productlicentie bestand dat u hebt aangeschaft, uploaden naar het vak licentie **sleutel** / **licentie bestand** .
  
         Uw toegevoegde snelle aangepaste Setup wordt weer gegeven op de pagina **Geavanceerde instellingen** . Als u deze wilt verwijderen, kunt u de bijbehorende selectie vakjes selecteren en vervolgens **verwijderen** selecteren.

   1. Selecteer **een VNet selecteren voor uw Azure-SSIS Integration runtime om lid te worden van, ADF toestaan om bepaalde netwerk resources te maken en Schakel eventueel uw eigen statische open bare IP-adressen** in om te kiezen of u wilt samen voegen met een virtueel netwerk. 

      Selecteer deze optie als u een Azure SQL Database Server met IP-firewall regels/virtuele netwerk service-eind punten of een beheerd exemplaar met een persoonlijk eind punt gebruikt om SSISDB te hosten of als u toegang nodig hebt tot on-premises gegevens (dat wil zeggen: u hebt on-premises gegevens bronnen of-bestemmingen in uw SSIS-pakketten) zonder een zelf-hostende IR te configureren. Zie voor meer informatie [lid worden van Azure-SSIS IR aan een virtueel netwerk](./join-azure-ssis-integration-runtime-virtual-network.md). 

      Als u het selectie vakje inschakelt, voert u de volgende stappen uit.

      ![Geavanceerde instellingen met een virtueel netwerk](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

      1. Selecteer bij **abonnement** het Azure-abonnement met uw virtuele netwerk.

      1. Voor **locatie** is dezelfde locatie van de Integration runtime geselecteerd.

      1. Selecteer bij **type** het type van uw virtuele netwerk: klassiek of Azure Resource Manager. We raden u aan om een Azure Resource Manager virtueel netwerk te selecteren, omdat klassieke virtuele netwerken binnenkort worden afgeschaft.

      1. Selecteer voor **VNet-naam** de naam van het virtuele netwerk. Dit moet hetzelfde zijn als die voor uw Azure SQL Database-Server met virtuele netwerk service-eind punten of een beheerd exemplaar met een persoonlijk eind punt om SSISDB te hosten. Of het moet hetzelfde zijn als het account dat is verbonden met uw on-premises netwerk. Anders kan het een virtueel netwerk zijn om uw eigen statische open bare IP-adressen te maken voor Azure-SSIS IR.

      1. Selecteer bij **subnet naam** de naam van het subnet voor het virtuele netwerk. Dit moet hetzelfde zijn als die voor uw Azure SQL Database-Server met virtuele netwerk service-eind punten om SSISDB te hosten. Of het moet een ander subnet zijn dat wordt gebruikt voor uw beheerde exemplaar met een persoonlijk eind punt om SSISDB te hosten. Anders kan het een wille keurige subnet zijn om uw eigen statische open bare IP-adressen voor Azure-SSIS IR te brengen.

      1. Schakel het selectie vakje **statische open bare IP-adressen voor uw Azure-SSIS Integration runtime** in om te kiezen of u uw eigen statische open bare IP-adressen wilt meenemen voor Azure-SSIS IR, zodat u ze op de firewall voor uw gegevens bronnen kunt toestaan.

         Als u het selectie vakje inschakelt, voert u de volgende stappen uit.

         1. Voor het **eerste statische open bare IP-adres** selecteert u het eerste statische open bare IP-adres dat voldoet aan de vereisten voor uw Azure-SSIS IR. Als u er geen hebt, klikt u op **nieuwe koppeling maken** om statische open bare IP-adressen te maken op Azure Portal en klikt u vervolgens op de knop Vernieuwen hier, zodat u ze kunt selecteren.
      
         1. Voor het **tweede statische open bare IP-adres** selecteert u het tweede statische open bare IP-adres dat voldoet aan de vereisten voor uw Azure-SSIS IR. Als u er geen hebt, klikt u op **nieuwe koppeling maken** om statische open bare IP-adressen te maken op Azure Portal en klikt u vervolgens op de knop Vernieuwen hier, zodat u ze kunt selecteren.

   1. Schakel het selectievakje **Zelf-hostende Integration Runtime instellen als proxy voor uw Azure-SSIS Integration Runtime** in om te kiezen of u een zelf-hostende IR wilt configureren als proxy voor uw Azure-SSIS IR. Zie [Een zelf-hostende IR instellen als proxy](./self-hosted-integration-runtime-proxy-ssis.md) voor meer informatie. 

      Als u het selectie vakje inschakelt, voert u de volgende stappen uit.

      ![Geavanceerde instellingen met een zelf-hostende IR](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

      1. Voor **zelf-hostende Integration runtime** selecteert u uw bestaande zelf-hostende IR als proxy voor Azure-SSIS IR.

      1. Voor **faserings opslag gekoppelde** service selecteert u uw bestaande Azure Blob Storage-gekoppelde service of maakt u een nieuwe voor fase ring.

      1. Geef voor het **tijdelijke pad** een BLOB-container op in het geselecteerde Azure Blob Storage-account of laat het leeg om een standaard waarde te gebruiken voor fase ring.

   1. Selecteer **VNet-validatie**  >  **door gaan**. 

Controleer in de sectie **samen vatting** alle inrichtings instellingen, blad wijzer de aanbevolen documentatie koppelingen en selecteer **volt ooien** om het maken van de Integration runtime te starten.

   > [!NOTE]
   > Dit proces duurt ongeveer 5 minuten, tenzij u een aangepaste installatietijd hebt ingesteld. Maar het kan 20-30 minuten duren voordat de Azure-SSIS IR lid wordt van een virtueel netwerk.
   >
   > Als u SSISDB gebruikt, maakt de Data Factory-service verbinding met uw databaseserver om SSISDB voor te bereiden. Ook worden machtigingen en instellingen voor uw virtuele netwerk geconfigureerd, indien opgegeven, en worden uw Azure-SSIS IR aan het virtuele netwerk toegevoegd.
   > 
   > Wanneer u een Azure-SSIS IR inricht, worden ook het Azure Feature Pack voor SSIS en de Access Redistributable geïnstalleerd. Deze onderdelen bieden connectiviteit met Excel- en Access-bestanden en met verschillende Azure-gegevensbronnen, naast de gegevensbronnen die worden ondersteund door de ingebouwde onderdelen. Zie [Ingebouwde/vooraf geïnstalleerde onderdelen op Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md) voor meer informatie over ingebouwde en vooraf geïnstalleerde onderdelen. Zie [Aangepaste installatie voor Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md) voor meer informatie over aanvullende onderdelen die u kunt installeren.

#### <a name="connections-pane"></a>Deelvenster Verbindingen

Ga in het deelvenster **Verbindingen** van de hub **Beheren** naar de pagina **Integratieruntimes** en selecteer **Vernieuwen**. 

   ![Deelvenster Verbindingen](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   U kunt uw Azure-SSIS IR bewerken/opnieuw configureren door de naam ervan te selecteren. U kunt ook de relevante knoppen voor het bewaken/starten/stoppen/verwijderen van uw Azure-SSIS IR selecteren, automatisch een ADF-pijplijn genereren met de activiteit voor het uitvoeren van SSIS-pakketten om deze op uw Azure-SSIS IR uit te voeren en de JSON-code/payload van uw Azure-SSIS IR weergeven.  U kunt uw Azure-SSIS IR alleen bewerken/verwijderen wanneer deze is gestopt.

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Azure SSIS-integratieruntimes in de portal

1. Ga in de gebruikersinterface van Azure Data Factory naar het tabblad **Bewerken** en selecteer **Verbindingen**. Ga vervolgens naar het tabblad **Integratieruntimes** om de bestaande integratieruntimes in uw data factory weer te geven.

   ![Bestaande IR’s weergeven](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Selecteer **Nieuw** om een nieuw Azure-SSIS IR te maken en open het deel venster **Setup van Integration runtime** .

   ![Integratieruntime via menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. Selecteer in het venster **Installatie van integratieruntime** de tegel **Bestaande SSIS-pakketten verplaatsen voor uitvoering in Azure** en selecteer vervolgens **Volgende**.

   ![Geef het type integratieruntime op](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Zie de sectie [een Azure SSIS Integration runtime inrichten](#provision-an-azure-ssis-integration-runtime) voor de resterende stappen voor het instellen van een Azure-SSIS IR.

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>Azure PowerShell gebruiken om een Integration runtime te maken

In deze sectie gebruikt u Azure PowerShell om een Azure-SSIS IR te maken.

### <a name="create-variables"></a>Variabelen maken

Kopieer en plak het volgende script. Geef waarden op voor de variabelen. 

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 
```

### <a name="sign-in-and-select-a-subscription"></a>Meld u aan en selecteer een abonnement

Voeg het volgende script toe om u aan te melden en uw Azure-abonnement te selecteren.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>De verbinding met de database server valideren

Voeg het volgende script toe om uw Azure SQL Database Server of een beheerd exemplaar te valideren.

```powershell
# Validate only if you use SSISDB and you don't use virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}
```

### <a name="configure-the-virtual-network"></a>Het virtuele netwerk configureren

Voeg het volgende script toe om de machtigingen en instellingen van het virtuele netwerk voor uw Azure-SSIS Integration runtime automatisch te configureren om lid te worden.

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een [Azure-resourcegroep](../azure-resource-manager/management/overview.md) met de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en groepsgewijs worden beheerd.

Als uw resourcegroep al bestaat, hoeft u deze code niet naar het script te kopiëren. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Een gegevensfactory maken

Voer de volgende opdracht uit om een data factory te maken.

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Een Integration Runtime maken

Voer de volgende opdrachten uit om een Azure SSIS Integration runtime te maken waarmee SSIS-pakketten worden uitgevoerd in Azure.

Als u SSISDB niet gebruikt, kunt u de `CatalogServerEndpoint` `CatalogPricingTier` para meters, en weglaten `CatalogAdminCredential` .

Als u geen Azure SQL Database Server met IP-firewall regels/virtuele netwerk service-eind punten of een beheerd exemplaar met een persoonlijk eind punt gebruikt om SSISDB te hosten, of toegang tot on-premises gegevens nodig hebt, kunt u de `VNetId` `Subnet` para meters en weglaten. U kunt ze ook weglaten als u een zelf-hostende IR als proxy voor uw Azure-SSIS IR wilt gebruiken om toegang te krijgen tot gegevens on-premises. Als dat niet het geval is, kunt u ze niet weglaten en moet u geldige waarden van de configuratie van uw virtuele netwerk door geven. Zie [een Azure-SSIS IR toevoegen aan een virtueel netwerk](./join-azure-ssis-integration-runtime-virtual-network.md)voor meer informatie.

Als u een beheerd exemplaar gebruikt om SSISDB te hosten, kunt u de `CatalogPricingTier` para meter weglaten of een lege waarde hiervoor door geven. Als dat niet het geval is, kunt u deze niet weglaten en moet u een geldige waarde door geven uit de lijst met ondersteunde prijs categorieën voor Azure SQL Database. Zie [SQL database resource limieten](../azure-sql/database/resource-limits-logical-server.md)voor meer informatie.

Als u Azure AD-verificatie gebruikt met de beheerde identiteit voor uw data factory om verbinding te maken met de database server, kunt u de `CatalogAdminCredential` para meter weglaten. Maar u moet de beheerde identiteit voor uw data factory toevoegen aan een Azure AD-groep met toegangs machtigingen voor de database server. Zie [Azure AD-verificatie inschakelen voor een Azure-SSIS IR](./enable-aad-authentication-azure-ssis-ir.md)voor meer informatie. Als dat niet het geval is, kunt u deze niet weglaten en moet u een geldig object door geven uit de gebruikers naam en het wacht woord van uw server beheerder voor SQL-verificatie

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
    -VnetId $VnetId `
    -Subnet $SubnetName
       
# Add the CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "InstallAzurePowerShell")
    {
        $moduleVersion = "YourAzModuleVersion"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Standard")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Extended")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-integration-runtime"></a>De Integration runtime starten

Voer de volgende opdrachten uit om de Azure-SSIS Integration runtime te starten.

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

> [!NOTE]
> Dit proces duurt ongeveer 5 minuten, tenzij u een aangepaste installatietijd hebt ingesteld. Maar het kan 20-30 minuten duren voordat de Azure-SSIS IR lid wordt van een virtueel netwerk.
>
> Als u SSISDB gebruikt, maakt de Data Factory-service verbinding met uw databaseserver om SSISDB voor te bereiden. Ook worden machtigingen en instellingen voor uw virtuele netwerk geconfigureerd, indien opgegeven, en worden uw Azure-SSIS IR aan het virtuele netwerk toegevoegd.
> 
> Wanneer u een Azure-SSIS IR inricht, worden ook het Azure Feature Pack voor SSIS en de Access Redistributable geïnstalleerd. Deze onderdelen bieden connectiviteit met Excel- en Access-bestanden en met verschillende Azure-gegevensbronnen, naast de gegevensbronnen die worden ondersteund door de ingebouwde onderdelen. Zie [Ingebouwde/vooraf geïnstalleerde onderdelen op Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md) voor meer informatie over ingebouwde en vooraf geïnstalleerde onderdelen. Zie [Aangepaste installatie voor Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md) voor meer informatie over aanvullende onderdelen die u kunt installeren.

### <a name="full-script"></a>Volledige script

Hier is het volledige script waarmee een Azure SSIS Integration runtime wordt gemaakt.

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from the Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to four parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

### Sign in and select a subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to the database server
# Validate only if you use SSISDB and don't use a virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}

### Configure a virtual network
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
    -VnetId $VnetId `
    -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "InstallAzurePowerShell")
    {
        $moduleVersion = "YourAzModuleVersion"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Standard")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Extended")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}

### Start the integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>Een Azure Resource Manager sjabloon gebruiken om een Integration runtime te maken

In deze sectie gebruikt u een Azure Resource Manager sjabloon om de Azure SSIS Integration runtime te maken. Hier volgt een voor beeld van een scenario:

1. Maak een JSON-bestand met de volgende Azure Resource Manager sjabloon. Vervang waarden in de punt haken (tijdelijke aanduidingen) door uw eigen waarden.

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2018-06-01",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D8_v3",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```

2. Als u de Azure Resource Manager sjabloon wilt implementeren, voert u de opdracht uit, `New-AzResourceGroupDeployment` zoals in het volgende voor beeld wordt weer gegeven. In het voor beeld `ADFTutorialResourceGroup` is de naam van de resource groep. `ADFTutorialARM.json` is het bestand dat de JSON-definitie bevat voor uw data factory en de Azure-SSIS IR.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Met deze opdracht worden uw data factory gemaakt en Azure-SSIS IR, maar wordt de IR niet gestart.

3. Voer de volgende opdracht uit om de Azure-SSIS IR te starten `Start-AzDataFactoryV2IntegrationRuntime` :

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
        -DataFactoryName "<Data Factory Name>" `
        -Name "<Azure SSIS IR Name>" `
        -Force
    ```

> [!NOTE]
> Dit proces duurt ongeveer 5 minuten, tenzij u een aangepaste installatietijd hebt ingesteld. Maar het kan 20-30 minuten duren voordat de Azure-SSIS IR lid wordt van een virtueel netwerk.
>
> Als u SSISDB gebruikt, maakt de Data Factory-service verbinding met uw databaseserver om SSISDB voor te bereiden. Ook worden machtigingen en instellingen voor uw virtuele netwerk geconfigureerd, indien opgegeven, en worden uw Azure-SSIS IR aan het virtuele netwerk toegevoegd.
> 
> Wanneer u een Azure-SSIS IR inricht, worden ook het Azure Feature Pack voor SSIS en de Access Redistributable geïnstalleerd. Deze onderdelen bieden connectiviteit met Excel- en Access-bestanden en met verschillende Azure-gegevensbronnen, naast de gegevensbronnen die worden ondersteund door de ingebouwde onderdelen. Zie [Ingebouwde/vooraf geïnstalleerde onderdelen op Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md) voor meer informatie over ingebouwde en vooraf geïnstalleerde onderdelen. Zie [Aangepaste installatie voor Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md) voor meer informatie over aanvullende onderdelen die u kunt installeren.

## <a name="deploy-ssis-packages"></a>SSIS-pakketten implementeren

Als u SSISDB gebruikt, kunt u uw pakketten ernaar implementeren en deze uitvoeren op uw Azure-SSIS IR met behulp van de Azure SSDT- of SSMS-hulpprogramma's. Deze hulpprogramma's maken verbinding met uw databaseserver via het servereindpunt: 

- Voor een Azure SQL Database-server is de indeling van het servereindpunt `<server name>.database.windows.net`.
- Voor een beheerd exemplaar met een privé-eindpunt is de indeling van het servereindpunt `<server name>.<dns prefix>.database.windows.net`.
- Voor een beheerd exemplaar met een openbaar eindpunt is de indeling van het servereindpunt `<server name>.public.<dns prefix>.database.windows.net,3342`. 

Als u geen gebruik maakt van SSISDB, kunt u uw pakketten implementeren in het bestandssysteem, Azure Files of MSDB gehost door uw Azure SQL Managed Instance en deze uitvoeren op uw Azure-SSIS IR met behulp van de opdrachtregelprogramma's [dtutil](/sql/integration-services/dtutil-utility) en [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md). 

Zie [SSIS-projecten/pakketten implementeren](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages) voor meer informatie.

In beide gevallen kunt u uw geïmplementeerde pakketten ook uitvoeren op Azure-SSIS IR met behulp van de activiteit voor het uitvoeren van SSIS-pakketten in Data Factory-pijplijnen. Zie [Het uitvoeren van SSIS-pakketten aanroepen als een Data Factory-activiteit van de eerste klasse](./how-to-invoke-ssis-package-ssis-activity.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie andere Azure-SSIS IR onderwerpen in deze documentatie:

- [Azure-SSIS Integration runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). In dit artikel vindt u informatie over Integration Runtimes in het algemeen, met inbegrip van Azure-SSIS IR.
- [Een Azure-SSIS IR controleren](monitor-integration-runtime.md#azure-ssis-integration-runtime). In dit artikel leest u hoe u informatie over uw Azure-SSIS IR ophaalt en begrijpt.
- [Een Azure-SSIS IR beheren](manage-azure-ssis-integration-runtime.md). In dit artikel leest u hoe u uw Azure-SSIS IR kunt stoppen, starten of verwijderen. U ziet ook hoe u uw Azure-SSIS IR kunt uitschalen door meer knoop punten toe te voegen.
- [SSIS-pakketten implementeren, uitvoeren en bewaken in Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Verbinding maken met SSISDB in Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Connect to on-premises data sources with Windows authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) (Verbinding maken met on-premises gegevensbronnen met Windows-verificatie) 
- [Pakketuitvoering plannen in Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)