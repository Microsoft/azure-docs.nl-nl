---
title: De Azure-SSIS-integratieruntime inrichten
description: Informatie over het inrichten van de Azure-SSIS-integratieruntime in Azure Data Factory, zodat u SSIS-pakketten in Azure kunt implementeren en uitvoeren.
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 02/22/2021
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 7c439d71806d2deba508ce35131f21ebfbd7a3ec
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740406"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>De Azure-SSIS-integratieruntime inrichten in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Deze zelfstudie bevat de stappen voor het gebruik van de Azure-portal om een Azure SQL Server Integration Services-integratieruntime (SSIS) (IR) in te richten in Azure Data Factory (ADF). Een Azure-SSIS IR ondersteunt:

- Pakketten die zijn geïmplementeerd in SSIS Catalog (SSISDB) die worden gehost door een server of beheerd exemplaar van Azure SQL Database (projectimplementatiemodel)
- Pakketten die zijn geïmplementeerd in het bestandssysteem, Azure Files of SQL Server-database (MSDB) die worden gehost door Azure SQL Managed Instance (pakketimplementatiemodel)

Nadat een Azure-SSIS IR is ingericht, kunt u vertrouwde hulpprogramma's gebruiken om uw pakketten in Azure te implementeren en uit te voeren. Deze hulpprogramma's zijn al ingeschakeld voor Azure en bevatten SQL Server Data Tools (SSDT), SQL Server Management Studio (SSMS) en opdrachtregelprogramma's zoals [dtutil](/sql/integration-services/dtutil-utility) en [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md).

Zie [Overzicht van integratieruntime in Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime) voor algemene informatie over een Azure-SSIS IR.

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Een data factory maken.
> * Een Azure-SSIS-integratieruntime inrichten.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-abonnement**. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

- **Azure SQL Database-server (optioneel)** . Als u nog geen databaseserver hebt, maakt u die in Azure Portal voordat u begint. Met Data Factory wordt vervolgens een SSISDB-exemplaar op deze databaseserver gemaakt. 

  Het wordt aangeraden om de databaseserver in dezelfde Azure-regio te maken als de Integration Runtime. Met deze configuratie kan de Integration Runtime uitvoeringslogboeken wegschrijven naar SSISDB zonder dat hierbij Azure-regio's worden overschreden.

  Houd de volgende zaken in gedachten:

  - Op basis van de geselecteerde databaseserver kan het SSISDB-exemplaar namens u worden gemaakt als een enkele database, als onderdeel van een elastische pool of in een beheerd exemplaar. Deze is toegankelijk in een openbaar netwerk of kan worden toegevoegd aan een virtueel netwerk. Zie [SQL Database en een beheerd exemplaar van SQL vergelijken](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance) voor hulp bij het kiezen van het type databaseserver om SSISDB te hosten. 
  
    Als u een Azure SQL Database-server met IP-firewall regels/service-eindpunten voor virtuele netwerken of een beheerd exemplaar met een privé-eindpunt gebruikt om SSISDB te hosten, of als u toegang tot on-premises gegevens nodig hebt zonder een zelf-hostende IR te configureren, moet u uw Azure-SSIS IR toevoegen aan een virtueel netwerk. Zie [Een Azure-SSIS IR maken in een virtueel netwerk](./create-azure-ssis-integration-runtime.md) voor meer informatie.

  - Controleer of de instelling **Toegang tot Azure-services toestaan** is ingeschakeld voor de databaseserver. Deze instelling is niet van toepassing wanneer u Azure SQL Database met IP-firewall regels/service-eindpunten voor virtuele netwerken of een beheerd exemplaar met een privé-eindpunt gebruikt om SSISDB te hosten. Zie [Secure Azure SQL Database](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules) (Azure SQL Database beveiligen) voor meer informatie. Zie [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) om deze instelling in te schakelen met behulp van PowerShell.

  - Voeg het IP-adres van de clientcomputer (of een reeks IP-adressen dat het IP-adres van de clientcomputer bevat) toe aan de lijst met client-IP-adressen in de instellingen van de firewall voor de databaseserver. Zie [Overzicht van firewallregels op Azure SQL Database-serverniveau en -databaseniveau](../azure-sql/database/firewall-configure.md) voor meer informatie.

  - U kunt verbinding maken met de databaseserver via SQL-verificatie met de beheerdersreferenties van de server of Azure Active Directory-verificatie met de beheerde identiteit voor uw data factory. Voor het laatste moet u de beheerde identiteit voor uw data factory toevoegen aan een Azure Active Directory-groep met toegangsmachtigingen tot de databaseserver. Zie [Een Azure-SSIS IR met Azure Active Directory-verificatie maken](./create-azure-ssis-integration-runtime.md) voor meer informatie.

  - Controleer of de databaseserver al een SSISDB-exemplaar heeft. Het inrichten van een Azure-SSIS IR biedt geen ondersteuning voor het gebruik van een bestaand SSIS-exemplaar.

> [!NOTE]
> Zie [Beschikbaarheid van Data Factory en SSIS IR per regio](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all) voor een lijst met Azure-regio's waarin Data Factory en Azure-SSIS IR momenteel beschikbaar zijn. 

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

Als u uw data factory via de Azure-portal wilt maken, volgt u de stapsgewijze instructies in [Een data factory maken via de gebruikersinterface](./quickstart-create-data-factory-portal.md#create-a-data-factory). Selecteer **Vastmaken aan dashboard** zodat u snel toegang hebt nadat de data factory is gemaakt. 

Nadat uw data factory is gemaakt, opent u de overzichtspagina in de Azure-portal. Selecteer de tegel **Ontwerpen en controleren** om de pagina **Aan de slag** op een afzonderlijk tabblad te openen. Daar kunt u doorgaan met het maken van uw Azure-SSIS IR.

## <a name="create-an-azure-ssis-integration-runtime"></a>Een Azure SSIS Integration Runtime maken

### <a name="from-the-data-factory-overview"></a>Vanuit het overzicht van Data Factory

1. Selecteer op de pagina **Aan de slag** de tegel **SSIS-integratieruntime configureren**. 

   ![De tegel SSIS-integratieruntime configureren](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Zie de sectie [Een Azure-SSIS-integratieruntime inrichten](#provision-an-azure-ssis-integration-runtime) voor de resterende stappen voor het instellen van een Azure-SSIS-IR. 

### <a name="from-the-authoring-ui"></a>Vanuit de gebruikersinterface Ontwerpen

1. Ga in de gebruikersinterface van Azure Data Factory naar het tabblad **Bewerken** en selecteer **Verbindingen**. Ga vervolgens naar het tabblad **Integratieruntimes** om de bestaande integratieruntimes in uw data factory weer te geven. 

   ![Selecties voor het weergeven van bestaande IR’s](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Selecteer **Nieuwe** om een Azure-SSIS IR te maken en open het deelvenster **Installatie van integratieruntime**. 

   ![Integratieruntime via menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. Selecteer in het venster **Installatie van integratieruntime** de tegel **Bestaande SSIS-pakketten verplaatsen voor uitvoering in Azure** en selecteer vervolgens **Volgende**.

   ![Geef het type integratieruntime op](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Zie de sectie [Een Azure-SSIS-integratieruntime inrichten](#provision-an-azure-ssis-integration-runtime) voor de resterende stappen voor het instellen van een Azure-SSIS-IR. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Een Azure-SSIS-integratieruntime inrichten

Het deelvenster **Installatie van integratieruntime** heeft drie pagina's waar u de algemene instellingen, implementatie-instellingen en geavanceerde instellingen kunt configureren.

### <a name="general-settings-page"></a>Pagina Algemene instellingen

Voer op de pagina **Algemene instellingen** van het deelvenster **Installatie van integratieruntime** de volgende stappen uit: 

   ![Algemene instellingen](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. Voer bij **Naam** de naam van de integratieruntime in. 

   1. Voer bij **beschrijving** de beschrijving van de integratieruntime in. 

   1. Selecteer bij **Locatie** de locatie voor de integratieruntime. Alleen ondersteunde locaties worden weergegeven. We raden u aan dezelfde locatie van uw databaseserver te selecteren voor het hosten van SSISDB. 

   1. Selecteer bij **Knooppuntgrootte** de grootte van knooppunt in het integratieruntimecluster. Alleen ondersteunde knooppuntgrootten worden weergegeven. Selecteer een grote knooppuntgrootte (omhoog schalen) als u veel reken-/geheugenintensieve pakketten wilt uitvoeren. 

   1. Selecteer bij **Aantal knooppunten** het aantal knooppunten in het integratieruntimecluster. Alleen ondersteunde knooppuntaantallen worden weergegeven. Selecteer een groot cluster met veel knooppunten (uitschalen), als u veel pakketten parallel wilt uitvoeren. 

   1. Selecteer bij **Editie/licentie** de SQL Server-editie voor uw integratieruntime: Standard of Enterprise. Selecteer Enterprise als u geavanceerde functies in de integratieruntime wilt gebruiken. 

   1. Selecteer bij **Geld besparen** de optie Azure Hybrid Benefit voor uw integratieruntime: **Ja** of **Nee**. Selecteer **Ja** als u uw eigen SQL Server-licentie met Software Assurance wilt gebruiken om te profiteren van de kostenbesparingen met hybride gebruik. 

   1. Selecteer **Next**. 

### <a name="deployment-settings-page"></a>Pagina Implementatie-instellingen

Op de pagina **Implementatie-instellingen** van het deelvenster **Integratieruntime-instellingen** hebt u de mogelijkheid om SSISDB- en/of Azure-SSIS IR-pakketarchieven te maken.

#### <a name="creating-ssisdb"></a>SSISDB maken

Schakel het selectievakje **SSIS-catalogus maken (SSISDB) die wordt gehost door Azure SQL Database server/beheerd exemplaar om uw projecten/pakketten/omgevingen/uitvoeringslogboeken op te slaan** op de pagina **Implementatie-instellingen** van het deelvenster **Integratieruntime-instellingen** in. Als u uw pakketten wilt implementeren in het bestandssysteem, Azure Files of SQL Server-database (MSDB) die worden gehost door Azure SQL Managed Instance (pakketimplementatiemodel), hoeft u geen SSISDB te maken en hoeft u ook het selectievakje niet in te schakelen.

Schakel dit selectievakje in, ongeacht uw implementatiemodel, als u SQL Server Agent gehost door Azure SQL Managed Instance wilt gebruiken om uw pakketuitvoeringen te plannen, aangezien dit wordt ingeschakeld door SSISDB. Zie [SSIS-pakketuitvoeringen plannen via de agent voor Azure SQL Managed Instance](./how-to-invoke-ssis-package-managed-instance-agent.md) voor meer informatie.
   
Als u het selectievakje inschakelt, moet u de volgende stappen uitvoeren om uw eigen databaseserver in te stellen als host voor de SSISDB die we namens u maken en beheren.

   ![Implementatie-instellingen voor SSISDB](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings.png)
   
   1. Selecteer bij **Abonnement** het Azure-abonnement dat uw databaseserver heeft voor het hosten van SSISDB. 

   1. Selecteer bij **Locatie** de locatie van uw databaseserver voor het hosten van SSISDB. We raden u aan dezelfde locatie van uw integratieruntime te selecteren.

   1. Selecteer bij het **Eindpunt voor de Catalog-databaseserver** het eindpunt van uw databaseserver voor het hosten van SSISDB. 
   
      Op basis van de geselecteerde databaseserver kan het SSISDB-exemplaar namens u worden gemaakt als een enkele database, als onderdeel van een elastische pool of in een beheerd exemplaar. Deze is toegankelijk in een openbaar netwerk of kan worden toegevoegd aan een virtueel netwerk. Zie [SQL Database en een beheerd exemplaar van SQL vergelijken](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance) voor hulp bij het kiezen van het type databaseserver om SSISDB te hosten.   

      Als u een Azure SQL Database-server met IP-firewall regels/service-eindpunten voor virtuele netwerken of een beheerd exemplaar met een privé-eindpunt selecteert om SSISDB te hosten, of als u toegang tot on-premises gegevens nodig hebt zonder een zelf-hostende IR te configureren, moet u uw Azure-SSIS IR toevoegen aan een virtueel netwerk. Zie [Een Azure-SSIS IR maken in een virtueel netwerk](./create-azure-ssis-integration-runtime.md) voor meer informatie.

   1. Schakel het selectievakje **Azure Active Directory-verificatie gebruiken met de beheerde identiteit voor uw ADF** om de verificatiemethode voor uw databaseserver voor het hosten van SSISDB te kiezen. U kiest SQL-verificatie of Azure Active Directory-verificatie met de beheerde identiteit voor uw data factory.

      Als u het selectievakje selecteert, moet u de beheerde identiteit voor uw data factory toevoegen aan een Azure Active Directory-groep met toegangsmachtigingen tot de databaseserver. Zie [Een Azure-SSIS IR met Azure Active Directory-verificatie maken](./create-azure-ssis-integration-runtime.md) voor meer informatie.
   
   1. Voer bij **Gebruikersnaam van beheerder** de gebruikersnaam voor SQL-verificatie voor uw databaseserver voor het hosten van SSISDB in. 

   1. Voer bij **Beheerderswachtwoord** het wachtwoord voor SQL-verificatie voor uw databaseserver voor het hosten van SSISDB in. 

   1. Schakel het selectie vakje **dubbele stand-by Azure-SSIS Integration runtime koppelen met SSISDB-failover** in om een dubbele stand-by-standby Azure SSIS IR-paar te configureren die wordt gesynchroniseerd met een Azure SQL database/Managed instance failover groep voor bedrijfs continuïteit en herstel na nood gevallen (BCDR).
   
      Als u het selectie vakje inschakelt, voert u een naam in om uw paar primaire en secundaire Azure-SSIS-IRs te identificeren in het tekstvak **naam van dubbele stand-by-koppeling** . U moet dezelfde paar naam invoeren bij het maken van uw primaire en secundaire Azure-SSIS-IRs.

      Zie [uw Azure-SSIS IR configureren voor BCDR](./configure-bcdr-azure-ssis-integration-runtime.md)voor meer informatie.

   1. Selecteer bij **Serverlaag catalogusdatabase** de servicelaag voor uw databaseserver voor het hosten van SSISDB. Selecteer de laag Basic, Standard of Premium of selecteer de naam van een elastische pool.

Selecteer **Verbinding testen** wanneer dit van toepassing is en selecteer **Volgende**.

#### <a name="creating-azure-ssis-ir-package-stores"></a>Azure-SSIS IR-pakketarchieven maken

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

### <a name="advanced-settings-page"></a>De pagina Geavanceerde instellingen

Voer op de pagina **Geavanceerde instellingen** van het deelvenster **Installatie van integratieruntime** de volgende stappen uit. 

   ![Geavanceerde instellingen](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. Selecteer bij het **Maximale aantal parallelle uitvoeringen per knooppunt** het maximum aantal pakketten dat gelijktijdig per knooppunt kan worden uitgevoerd in het integratieruntimecluster. Alleen ondersteunde pakketaantallen worden weergegeven. Selecteer een klein aantal als u meer dan één kern wilt gebruiken om een enkel groot pakket uit te voeren dat reken- of geheugenintensief is. Selecteer een groot aantal als u een of meer kleine pakketten in één kern wilt uitvoeren. 

   1. Schakel het selectievakje **Uw Azure-SSIS Integration Runtime aanpassen met aanvullende systeemconfiguraties/onderdeelinstallaties** in om te kiezen of u aangepaste installaties van standaard/express wilt toevoegen aan uw Azure-SSIS IR. Zie [Aangepaste installatie voor een Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md) voor meer informatie.
   
   1. Selecteer het selectievakje **Selecteer een VNet voor uw Azure-SSIS Integration Runtime om lid te worden, ADF toe te staan bepaalde netwerkresources te maken en eventueel uw eigen statische openbare IP-adressen mee te nemen** om te kiezen of u uw Azure-SSIS IR wilt aanmelden bij een virtueel netwerk.

      Selecteer deze optie als u een Azure SQL Database-server met IP-firewall regels/service-eindpunten voor virtuele netwerken of een beheerd exemplaar met een privé-eindpunt gebruikt om SSISDB te hosten, of als u toegang tot on-premises gegevens nodig hebt zonder een zelf-hostende IR te configureren. Zie [Een Azure-SSIS IR maken in een virtueel netwerk](./create-azure-ssis-integration-runtime.md) voor meer informatie. 
   
   1. Schakel het selectievakje **Zelf-hostende Integration Runtime instellen als proxy voor uw Azure-SSIS Integration Runtime** in om te kiezen of u een zelf-hostende IR wilt configureren als proxy voor uw Azure-SSIS IR. Zie [Een zelf-hostende IR instellen als proxy](./self-hosted-integration-runtime-proxy-ssis.md) voor meer informatie.   

   1. Selecteer **Doorgaan**. 

Controleer op de pagina **Overzicht** van het deelvenster **Integration Runtime instellen** alle inrichtingsinstellingen, plaats een bladwijzer bij de aanbevolen documentatielinks en selecteer **Voltooien** om de integratieruntime te maken. 

   > [!NOTE]
   > Dit proces duurt ongeveer 5 minuten, tenzij u een aangepaste installatietijd hebt ingesteld.
   >
   > Als u SSISDB gebruikt, maakt de Data Factory-service verbinding met uw databaseserver om SSISDB voor te bereiden. 
   > 
   > Wanneer u een Azure-SSIS IR inricht, worden ook het Azure Feature Pack voor SSIS en de Access Redistributable geïnstalleerd. Deze onderdelen bieden connectiviteit met Excel- en Access-bestanden en met verschillende Azure-gegevensbronnen, naast de gegevensbronnen die worden ondersteund door de ingebouwde onderdelen. Zie [Ingebouwde/vooraf geïnstalleerde onderdelen op Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md) voor meer informatie over ingebouwde en vooraf geïnstalleerde onderdelen. Zie [Aangepaste installatie voor Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md) voor meer informatie over aanvullende onderdelen die u kunt installeren.

### <a name="connections-pane"></a>Deelvenster Verbindingen

Ga in het deelvenster **Verbindingen** van de hub **Beheren** naar de pagina **Integratieruntimes** en selecteer **Vernieuwen**. 

   ![Deelvenster Verbindingen](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   U kunt uw Azure-SSIS IR bewerken/opnieuw configureren door de naam ervan te selecteren. U kunt ook de relevante knoppen voor het bewaken/starten/stoppen/verwijderen van uw Azure-SSIS IR selecteren, automatisch een ADF-pijplijn genereren met de activiteit voor het uitvoeren van SSIS-pakketten om deze op uw Azure-SSIS IR uit te voeren en de JSON-code/payload van uw Azure-SSIS IR weergeven.  U kunt uw Azure-SSIS IR alleen bewerken/verwijderen wanneer deze is gestopt.

## <a name="deploy-ssis-packages"></a>SSIS-pakketten implementeren

Als u SSISDB gebruikt, kunt u uw pakketten ernaar implementeren en deze uitvoeren op uw Azure-SSIS IR met behulp van de Azure SSDT- of SSMS-hulpprogramma's. Deze hulpprogramma's maken verbinding met uw databaseserver via het servereindpunt: 

- Voor een Azure SQL Database-server is de indeling van het servereindpunt `<server name>.database.windows.net`.
- Voor een beheerd exemplaar met een privé-eindpunt is de indeling van het servereindpunt `<server name>.<dns prefix>.database.windows.net`.
- Voor een beheerd exemplaar met een openbaar eindpunt is de indeling van het servereindpunt `<server name>.public.<dns prefix>.database.windows.net,3342`. 

Als u geen gebruik maakt van SSISDB, kunt u uw pakketten implementeren in het bestandssysteem, Azure Files of MSDB gehost door uw Azure SQL Managed Instance en deze uitvoeren op uw Azure-SSIS IR met behulp van de opdrachtregelprogramma's [dtutil](/sql/integration-services/dtutil-utility) en [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md). 

Zie [SSIS-projecten/pakketten implementeren](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages) voor meer informatie.

In beide gevallen kunt u uw geïmplementeerde pakketten ook uitvoeren op Azure-SSIS IR met behulp van de activiteit voor het uitvoeren van SSIS-pakketten in Data Factory-pijplijnen. Zie [Het uitvoeren van SSIS-pakketten aanroepen als een Data Factory-activiteit van de eerste klasse](./how-to-invoke-ssis-package-ssis-activity.md)voor meer informatie.

Zie ook de volgende SSIS-documentatie: 

- [SSIS-pakketten implementeren, uitvoeren en bewaken in Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Verbinding maken met SSISDB in Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Pakketuitvoering plannen in Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Connect to on-premises data sources with Windows authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) (Verbinding maken met on-premises gegevensbronnen met Windows-verificatie) 

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over uw Azure-SSIS-integratieruntime, gaat u verder met het volgende artikel: 

> [!div class="nextstepaction"]
> [Een Azure-SSIS IR aanpassen](./how-to-configure-azure-ssis-ir-custom-setup.md)