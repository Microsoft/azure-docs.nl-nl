---
title: Eindpunten en regels voor virtuele netwerken voor databases in Azure SQL Database
description: Een subnet markeren als een service-eindpunt voor een virtueel netwerk. Voeg vervolgens het eindpunt als een regel voor een virtueel netwerk toe aan de ACL voor uw database. Uw database accepteert vervolgens communicatie van alle virtuele machines en andere knooppunten in het subnet.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, genemi
ms.date: 11/14/2019
ms.openlocfilehash: 67e807e948caf1fec014457814c1b7f105630f9f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784421"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-servers-in-azure-sql-database"></a>Service-eindpunten en regels voor virtuele netwerken gebruiken voor servers in Azure SQL Database

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

 Regels voor virtuele netwerken zijn een firewallbeveiligingsfunctie waarmee wordt bepaald of de server voor uw databases en elastische pools in [Azure SQL Database of](sql-database-paas-overview.md) voor uw toegewezen SQL-pooldatabases (voorheen SQL DW) in [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) communicatie accepteert die vanuit bepaalde subnetten in virtuele netwerken wordt verzonden. In dit artikel wordt uitgelegd waarom regels voor virtuele netwerken soms de beste optie zijn voor het veilig toestaan van communicatie met uw database in SQL Database en Azure Synapse Analytics.

> [!NOTE]
> Dit artikel is van toepassing op SQL Database en Azure Synapse Analytics. Ter vereenvoudiging verwijst de *term database* naar beide databases in SQL Database en Azure Synapse Analytics. Op dezelfde manier verwijzen verwijzingen naar *de server* naar de logische [SQL-server](logical-servers.md) die als host SQL Database en Azure Synapse Analytics.

Als u een regel voor een virtueel netwerk wilt maken, moet er eerst een [service-eindpunt][vm-virtual-network-service-endpoints-overview-649d] voor een virtueel netwerk zijn waarnaar de regel moet verwijzen.

## <a name="create-a-virtual-network-rule"></a>Een regel voor een virtueel netwerk maken

Als u alleen een regel voor een virtueel netwerk wilt maken, kunt u verder gaan met de stappen en uitleg [verderop in dit artikel.](#anchor-how-to-by-using-firewall-portal-59j)

## <a name="details-about-virtual-network-rules"></a>Details over regels voor virtuele netwerken

In deze sectie worden verschillende details over regels voor virtuele netwerken beschreven.

### <a name="only-one-geographic-region"></a>Slechts één geografische regio

Elk service-eindpunt voor een virtueel netwerk is van toepassing op slechts één Azure-regio. Met het eindpunt kunnen andere regio's geen communicatie van het subnet accepteren.

Een regel voor een virtueel netwerk is beperkt tot de regio waar het onderliggende eindpunt op van toepassing is.

### <a name="server-level-not-database-level"></a>Serverniveau, niet databaseniveau

Elke regel voor een virtueel netwerk is van toepassing op uw hele server, niet alleen op één bepaalde database op de server. Met andere woorden, regels voor virtuele netwerken zijn van toepassing op serverniveau, niet op databaseniveau.

IP-regels kunnen daarentegen op beide niveau's van toepassing zijn.

### <a name="security-administration-roles"></a>Beveiligingsbeheerrollen

Er is een scheiding van beveiligingsrollen in het beheer van service-eindpunten voor virtuele netwerken. Actie is vereist voor elk van de volgende rollen:

- **Netwerkbeheerder ([rol Netwerkbijdrager):](../../role-based-access-control/built-in-roles.md#network-contributor)** &nbsp; Schakel het eindpunt in.
- **Databasebeheerder ([SQL Server rol Inzender):](../../role-based-access-control/built-in-roles.md#sql-server-contributor)** &nbsp; Werk de toegangsbeheerlijst (ACL) bij om het opgegeven subnet toe te voegen aan de server.

#### <a name="azure-rbac-alternative"></a>Alternatief voor Azure RBAC

De rollen Netwerkbeheerder en Databasebeheerder hebben meer mogelijkheden dan nodig zijn voor het beheren van regels voor virtuele netwerken. Er is slechts een subset van hun mogelijkheden nodig.

U hebt de mogelijkheid om op rollen gebaseerd toegangsbeheer [(RBAC)][rbac-what-is-813s] in Azure te gebruiken om één aangepaste rol te maken die alleen de benodigde subset van mogelijkheden heeft. De aangepaste rol kan worden gebruikt in plaats van de netwerkbeheerder of de databasebeheerder. Het surface area van uw beveiligingsblootstelling is lager als u een gebruiker aan een aangepaste rol toevoegt versus de gebruiker toevoegt aan de andere twee hoofdbeheerdersrollen.

> [!NOTE]
> In sommige gevallen maakt de database in SQL Database subnet van het virtuele netwerk deel uit van verschillende abonnementen. In deze gevallen moet u zorgen voor de volgende configuraties:
>
> - Beide abonnementen moeten zich in dezelfde Azure Active Directory (Azure AD)-tenant.
> - De gebruiker beschikt over de vereiste machtigingen om bewerkingen te starten, zoals het inschakelen van service-eindpunten en het toevoegen van een subnet van een virtueel netwerk aan de opgegeven server.
> - Voor beide abonnementen moet de Microsoft.Sql-provider zijn geregistreerd.

## <a name="limitations"></a>Beperkingen

Voor SQL Database heeft de functie regels voor virtuele netwerken de volgende beperkingen:

- In de firewall voor uw database in SQL Database verwijst elke regel van een virtueel netwerk naar een subnet. Al deze subnetten waarnaar wordt verwezen, moeten worden gehost in dezelfde geografische regio waarin de database wordt gehost.
- Elke server kan maximaal 128 ACL-vermeldingen hebben voor elk virtueel netwerk.
- Regels voor virtuele netwerken zijn alleen van toepassing Azure Resource Manager virtuele netwerken en niet op [klassieke implementatiemodelnetwerken.][arm-deployment-model-568f]
- Als u service-eindpunten voor virtuele netwerken in SQL Database, worden ook de eindpunten voor Azure Database for MySQL en Azure Database for PostgreSQL. Als eindpunten zijn ingesteld op **AAN,** kunnen pogingen om verbinding te maken vanaf de eindpunten met uw Azure Database for MySQL of Azure Database for PostgreSQL mislukken.
  - De onderliggende reden is dat Azure Database for MySQL en Azure Database for PostgreSQL waarschijnlijk geen regel voor een virtueel netwerk hebben geconfigureerd. U moet een regel voor virtuele netwerken configureren voor Azure Database for MySQL en Azure Database for PostgreSQL, en de verbinding slaagt.
  - Als u firewallregels voor virtuele netwerken wilt definiëren op een logische SQL-server die al is geconfigureerd met privé-eindpunten, stelt u Openbare netwerktoegang **weigeren** in op **Nee.**
- Op de firewall zijn IP-adresbereiken wel van toepassing op de volgende netwerkitems, maar regels voor virtuele netwerken niet:
  - [Site-naar-site (S2S) virtueel particulier netwerk (VPN)][vpn-gateway-indexmd-608y]
  - On-premises via [Azure ExpressRoute](../../expressroute/index.yml)

### <a name="considerations-when-you-use-service-endpoints"></a>Overwegingen bij het gebruik van service-eindpunten

Wanneer u service-eindpunten gebruikt voor SQL Database, bekijkt u de volgende overwegingen:

- **Uitgaand naar Azure SQL Database openbare IP's is vereist.** Netwerkbeveiligingsgroepen (NSG's) moeten worden geopend om verbinding SQL Database te kunnen maken. U kunt dit doen met behulp van [NSG-servicetags](../../virtual-network/network-security-groups-overview.md#service-tags) voor SQL Database.

### <a name="expressroute"></a>ExpressRoute

Als u [ExpressRoute gebruikt](../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vanuit uw locatie, voor openbare peering of Microsoft-peering, moet u de NAT IP-adressen identificeren die worden gebruikt. Voor openbare peering gebruikt elk ExpressRoute-circuit standaard twee NAT IP-adressen. Deze worden toegepast op Azure-serviceverkeer wanneer het verkeer het Microsoft Azure-backbone-netwerk binnenkomt. Voor Microsoft-peering worden de NAT IP-adressen die worden gebruikt geleverd door de klant of de serviceprovider. Voor toegang tot uw serviceresources moet u deze openbare IP-adressen toestaan in de instelling voor IP-firewall voor de resource. Wanneer u op zoek bent naar de IP-adressen van uw ExpressRoute-circuit voor openbare peering, opent u [een ondersteuningsticket met ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via de Azure-portal. Zie NAT-vereisten voor openbare Azure-peering voor meer informatie over NAT voor openbare [ExpressRoute-peering en Microsoft-peering.](../../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)

Als u communicatie van uw circuit naar SQL Database wilt toestaan, moet u IP-netwerkregels maken voor de openbare IP-adressen van uw NAT.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-virtual-network-service-endpoints-with-azure-storage"></a>Gevolgen van het gebruik van service-eindpunten voor virtuele netwerken met Azure Storage

Azure Storage heeft dezelfde functie geïmplementeerd waarmee u de connectiviteit met uw Azure Storage-account kunt beperken. Als u ervoor kiest om deze functie te gebruiken met een Azure Storage-account dat SQL Database gebruikt, kunt u problemen hebben. Hierna is een lijst en bespreking van SQL Database en Azure Synapse Analytics functies die hierdoor worden beïnvloed.

### <a name="azure-synapse-analytics-polybase-and-copy-statement"></a>Azure Synapse Analytics PolyBase- en COPY-instructie

PolyBase en de COPY-instructie worden vaak gebruikt voor het laden van gegevens in Azure Synapse Analytics van Azure Storage accounts voor gegevens opname met hoge doorvoer. Als het Azure Storage-account waar u gegevens van laadt, alleen toegang heeft tot een set subnetten van virtuele netwerken, wordt de connectiviteit tijdens het gebruik van PolyBase en de COPY-instructie voor het opslagaccount breekt. Voor het inschakelen van import- en exportscenario's met copy en PolyBase met Azure Synapse Analytics die verbinding maken met Azure Storage die is beveiligd met een virtueel netwerk, volgt u de stappen in deze sectie.

#### <a name="prerequisites"></a>Vereisten

- Installeer Azure PowerShell met behulp [van deze handleiding](/powershell/azure/install-az-ps).
- Als u een v1- of Azure Blob Storage-account voor algemeen gebruik hebt, moet u eerst upgraden naar algemeen v2 door de stappen te volgen in Upgrade to a general-purpose v2 storage account (Upgraden naar een [v2-opslagaccount](../../storage/common/storage-account-upgrade.md)voor algemeen gebruik).
- U moet Vertrouwde **gebruikers toegang Microsoft-services** dit opslagaccount toestaan hebben ingeschakeld onder het instellingenmenu **firewalls** en virtuele netwerken van Azure Storage-account. Als u deze configuratie inschakelen, kunnen PolyBase en de COPY-instructie verbinding maken met het opslagaccount met behulp van sterke verificatie waarbij netwerkverkeer op de Azure-backbone blijft. Zie deze handleiding voor [meer informatie.](../../storage/common/storage-network-security.md#exceptions)

> [!IMPORTANT]
> De PowerShell Azure Resource Manager-module wordt nog steeds ondersteund door SQL Database, maar alle toekomstige ontwikkeling is voor de Az.Sql-module. De AzureRM-module blijft tot ten minste december 2020 bugfixes ontvangen. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn vrijwel identiek. Zie [Introductie van de nieuwe Az-module van Azure PowerShell](/powershell/azure/new-azureps-module-az) voor meer informatie over de compatibiliteit van de argumenten.

#### <a name="steps"></a>Stappen

1. Als u een zelfstandige, toegewezen SQL-pool hebt, registreert u uw SQL-server bij Azure AD met behulp van PowerShell:

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

   Deze stap is niet vereist voor toegewezen SQL-pools binnen een Azure Synapse Analytics werkruimte.

1. Als u een Azure Synapse Analytics hebt, registreert u de door het systeem beheerde identiteit van uw werkruimte:

   1. Ga naar uw Azure Synapse Analytics werkruimte in de Azure Portal.
   2. Ga naar het **deelvenster Beheerde identiteiten.**
   3. Zorg ervoor dat **de optie Pijplijnen** toestaan is ingeschakeld.
   
1. Maak een **v2-opslagaccount voor algemeen gebruik** door de stappen in [Een opslagaccount maken te volgen.](../../storage/common/storage-account-create.md)

   > [!NOTE]
   >
   > - Als u een v1- of Blob Storage-account voor algemeen gebruik hebt, moet u eerst upgraden naar *v2* door de stappen te volgen in [Upgrade to a general-purpose v2 storage account (Upgraden naar een v2-opslagaccount voor algemeen gebruik).](../../storage/common/storage-account-upgrade.md)
   > - Zie Bekende problemen met Azure Data Lake Storage Gen2 voor [bekende problemen met Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-known-issues.md).

1. Ga onder uw opslagaccount naar **Access Control (IAM)** en selecteer **Roltoewijzing toevoegen.** Wijs de **Azure-rol Bijdrager** voor opslagblobgegevens toe aan de server of werkruimte die als host voor uw toegewezen SQL-pool wordt gebruikt, die u hebt geregistreerd bij Azure AD.

   > [!NOTE]
   > Alleen leden met eigenaarsrechten voor het opslagaccount kunnen deze stap uitvoeren. Zie Ingebouwde Azure-rollen voor verschillende [ingebouwde Azure-rollen.](../../role-based-access-control/built-in-roles.md)
  
1. PolyBase-connectiviteit met het Azure Storage inschakelen:

   1. Maak een [database-hoofdsleutel](/sql/t-sql/statements/create-master-key-transact-sql) als u er nog geen hebt gemaakt.

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. Maak een referentie binnen databasebereik met **IDENTITY = 'Managed Service Identity'.**

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       >
       > - U hoeft SECRET niet op te geven met een Azure Storage-toegangssleutel, omdat dit mechanisme gebruikmaakt van [beheerde](../../active-directory/managed-identities-azure-resources/overview.md) identiteit.
       > - De identiteitsnaam moet **Managed Service Identity** zijn voor PolyBase-connectiviteit om te kunnen werken met een Azure Storage-account dat is beveiligd met een virtueel netwerk.

   1. Maak een externe gegevensbron met het schema voor het maken van verbinding met uw `abfss://` opslagaccount voor algemeen gebruik v2 met behulp van PolyBase.

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       >
       > - Als er al externe tabellen zijn gekoppeld aan een account voor algemeen gebruik v1 of Blob Storage, moet u deze externe tabellen eerst verwijderen. Zet vervolgens de bijbehorende externe gegevensbron neer. Maak vervolgens een externe gegevensbron met het schema dat verbinding maakt met een v2-opslagaccount voor algemeen `abfss://` gebruik, zoals eerder is weergegeven. Maak vervolgens alle externe tabellen opnieuw met behulp van deze nieuwe externe gegevensbron. U kunt de wizard [Scripts genereren en publiceren gebruiken om](/sql/ssms/scripting/generate-and-publish-scripts-wizard) eenvoudig create-scripts te genereren voor alle externe tabellen.
       > - Zie Use `abfss://` the Azure Data Lake Storage Gen2 [URI (De URI van de Azure Data Lake Storage Gen2 gebruiken) voor meer informatie over het schema.](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md)
       > - Zie deze handleiding voor meer informatie [over](/sql/t-sql/statements/create-external-data-source-transact-sql)CREATE EXTERNAL DATA SOURCE.

   1. Query's uitvoeren zoals normaal met [behulp van externe tabellen](/sql/t-sql/statements/create-external-table-transact-sql).

### <a name="sql-database-blob-auditing"></a>SQL Database blob controleren

Blob-controle pusht auditlogboeken naar uw eigen opslagaccount. Als dit opslagaccount gebruikmaakt van de functie service-eindpunten voor virtuele netwerken, wordt de verbinding SQL Database het opslagaccount wordt breekt.

## <a name="add-a-virtual-network-firewall-rule-to-your-server"></a>Een firewallregel voor een virtueel netwerk toevoegen aan uw server

Lang geleden moest u, voordat deze functie werd uitgebreid, service-eindpunten voor virtuele netwerken in toen u een regel voor een live virtueel netwerk in de firewall kon implementeren. De eindpunten hebben een bepaald subnet van een virtueel netwerk gerelateerd aan een database in SQL Database. Vanaf januari 2018 kunt u deze vereiste omzeilen door de vlag **IgnoreMissingVNetServiceEndpoint in te** stellen. U kunt nu een firewallregel voor een virtueel netwerk toevoegen aan uw server zonder service-eindpunten voor virtuele netwerken in te zetten.

Het instellen van een firewallregel helpt niet om de server te beveiligen. U moet ook service-eindpunten voor virtuele netwerken in te zetten om de beveiliging van kracht te laten worden. Wanneer u service-eindpunten in schakelen, uw virtuele netwerksubnet uitvaltijd totdat de overgang van uitgeschakeld naar aan is voltooid. Deze periode van downtime geldt met name in de context van grote virtuele netwerken. U kunt de vlag **IgnoreMissingVNetServiceEndpoint** gebruiken om de downtime tijdens de overgang te verminderen of te elimineren.

U kunt de **vlag IgnoreMissingVNetServiceEndpoint instellen** met behulp van PowerShell. Zie PowerShell voor het maken van een service-eindpunt en regel voor een virtueel netwerk voor [SQL Database.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]

## <a name="errors-40914-and-40615"></a>Fouten 40914 en 40615

Verbindingsfout 40914 heeft betrekking op regels voor virtuele *netwerken,* zoals opgegeven in het **deelvenster Firewall** in de Azure Portal. Fout 40615 is vergelijkbaar, behalve dat deze betrekking heeft op *IP-adresregels* in de firewall.

### <a name="error-40914"></a>Fout 40914

**Berichttekst:** "Kan de server *[servernaam] die is aangevraagd* door de aanmelding niet openen. Client heeft geen toegang tot de server.

**Foutbeschrijving:** De client zich in een subnet met server-eindpunten voor virtuele netwerken. De server heeft echter geen regel voor een virtueel netwerk die aan het subnet het recht verleent om te communiceren met de database.

**Foutoplossing:** In het **deelvenster Firewall** van de Azure Portal gebruikt u het besturingselement voor regels voor virtuele netwerken om een [regel voor](#anchor-how-to-by-using-firewall-portal-59j) een virtueel netwerk toe te voegen voor het subnet.

### <a name="error-40615"></a>Fout 40615

**Berichttekst:** 'Kan server niet {0} openen' aangevraagd door de aanmelding. Client met IP-adres {1} ' ' heeft geen toegang tot de server.'

**Foutbeschrijving:** De client probeert verbinding te maken vanaf een IP-adres dat niet is gemachtigd om verbinding te maken met de server. De serverfirewall heeft geen IP-adresregel waarmee een client vanaf het opgegeven IP-adres met de database kan communiceren.

**Foutoplossing:** Voer het IP-adres van de client in als een IP-regel. Gebruik het **deelvenster Firewall** in de Azure Portal om deze stap uit te doen.

<a name="anchor-how-to-by-using-firewall-portal-59j"></a>

## <a name="use-the-portal-to-create-a-virtual-network-rule"></a>De portal gebruiken om een regel voor een virtueel netwerk te maken

In deze sectie ziet u hoe u de Azure Portal [om][http-azure-portal-link-ref-477t] een regel *voor* een virtueel netwerk te maken in uw database in SQL Database. De regel vertelt uw database communicatie te accepteren van een bepaald subnet dat is gelabeld als een *service-eindpunt* voor een virtueel netwerk.

> [!NOTE]
> Als u van plan bent een service-eindpunt toe te voegen aan de firewallregels van het virtuele netwerk van uw server, moet u er eerst voor zorgen dat service-eindpunten zijn ingeschakeld voor het subnet.
>
> Als service-eindpunten niet zijn ingeschakeld voor het subnet, wordt u gevraagd deze in teschakelen. Selecteer de **knop** Inschakelen in hetzelfde deelvenster waaraan u de regel toevoegt.

## <a name="powershell-alternative"></a>PowerShell-alternatief

Een script kan ook regels voor virtuele netwerken maken met behulp van de PowerShell-cmdlet **New-AzSqlServerVirtualNetworkRule** of [az network vnet create.](/cli/azure/network/vnet#az_network_vnet_create) Zie PowerShell als u geïnteresseerd bent om een service-eindpunt en regel voor een virtueel netwerk te maken voor [SQL Database.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]

## <a name="rest-api-alternative"></a>REST API alternatief

Intern roepen de PowerShell-cmdlets voor virtuele SQL-netwerkacties REST API's aan. U kunt de REST API's rechtstreeks aanroepen.

- [Regels voor virtuele netwerken: Bewerkingen][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Vereisten

U moet al een subnet hebben dat is getagd met de naam van het *service-eindpunttype* voor het specifieke virtuele netwerk dat relevant is voor SQL Database.

- De naam van het relevante eindpunttype is **Microsoft.Sql**.
- Als uw subnet mogelijk niet is getagd met de typenaam, zie Controleer of [uw subnet een eindpunt is.][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]

<a name="a-portal-steps-for-vnet-rule-200"></a>

## <a name="azure-portal-steps"></a>Azure Portal stappen

1. Meld u aan bij [Azure Portal][http-azure-portal-link-ref-477t].

1. Zoek en selecteer **SQL-servers** en selecteer vervolgens uw server. Selecteer **onder Beveiliging** de optie **Firewalls en virtuele netwerken.**

1. Stel **Toegang tot Azure-services toestaan in** op **UIT.**

    > [!IMPORTANT]
    > Als u het besturingselement op **AAN** laat staan, accepteert uw server communicatie van elk subnet binnen de Grens van Azure. Dat is communicatie die afkomstig is van een van de IP-adressen die worden herkend als die binnen de bereiks die zijn gedefinieerd voor Azure-datacenters. Het instellen van het besturingselement **op AAN** kan overmatige toegang zijn vanuit het oogpunt van beveiliging. De Microsoft Azure Virtual Network service-eindpuntfunctie in coördinatie met de functie voor regels voor virtuele netwerken van SQL Database samen kan uw beveiligingsrisico'surface area.

1. Selecteer **+ Bestaande toevoegen** in de sectie **Virtuele** netwerken.

    ![Schermopname van het selecteren van + Bestaande toevoegen (subnet-eindpunt, als SQL-regel).][image-portal-firewall-vnet-add-existing-10-png]

1. Vul in **het nieuwe deelvenster Maken/bijwerken** de vakken in met de namen van uw Azure-resources.

    > [!TIP]
    > U moet het juiste adres voorvoegsel voor uw subnet opnemen. U vindt de waarde **van het adres voorvoegsel** in de portal. Ga naar **Alle resources** &gt; **Alle typen** Virtuele &gt; **netwerken.** Het filter geeft uw virtuele netwerken weer. Selecteer uw virtuele netwerk en selecteer vervolgens **Subnetten.** De **kolom ADRESBEREIK** heeft het adres voorvoegsel dat u nodig hebt.

    ![Schermopname van het invullen van vakken voor de nieuwe regel.][image-portal-firewall-create-update-vnet-rule-20-png]

1. Selecteer de **knop OK** onderaan het deelvenster.

1. Zie de resulterende regel voor het virtuele netwerk in het **deelvenster Firewall.**

    ![Schermopname van de nieuwe regel in het deelvenster Firewall.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> De volgende statussen of statussen zijn van toepassing op de regels:
>
> - **Gereed:** geeft aan dat de bewerking die u hebt gestart, is geslaagd.
> - **Mislukt:** geeft aan dat de bewerking die u hebt gestart, is mislukt.
> - **Verwijderd:** alleen van toepassing op de bewerking Verwijderen en geeft aan dat de regel is verwijderd en niet meer van toepassing is.
> - **InProgress:** geeft aan dat de bewerking wordt uitgevoerd. De oude regel is van toepassing terwijl de bewerking deze status heeft.

<a name="anchor-how-to-links-60h"></a>

## <a name="related-articles"></a>Verwante artikelen:

- [Service-eindpunten voor een virtueel Azure-netwerk][vm-virtual-network-service-endpoints-overview-649d]
- [Firewallregels op server- en databaseniveau][sql-db-firewall-rules-config-715d]

## <a name="next-steps"></a>Volgende stappen

- [Gebruik PowerShell om een service-eindpunt voor een virtueel netwerk te maken en vervolgens een regel voor virtuele netwerken voor SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Regels voor virtuele netwerken: Bewerkingen met][rest-api-virtual-network-rules-operations-862r] REST API's

<!-- Link references, to images. -->
[image-portal-firewall-vnet-add-existing-10-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png
[image-portal-firewall-create-update-vnet-rule-20-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png
[image-portal-firewall-vnet-result-rule-30-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../../azure-resource-manager/management/deployment-models.md
[expressroute-indexmd-744v]:../index.yml
[rbac-what-is-813s]:../../role-based-access-control/overview.md
[sql-db-firewall-rules-config-715d]:firewall-configure.md
[sql-db-vnet-service-endpoint-rule-powershell-md-52d]:scripts/vnet-service-endpoint-rule-powershell-create.md
[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]:scripts/vnet-service-endpoint-rule-powershell-create.md#a-verify-subnet-is-endpoint-ps-100
[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[vm-virtual-network-service-endpoints-overview-649d]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[vpn-gateway-indexmd-608y]: ../../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
[rest-api-virtual-network-rules-operations-862r]: /rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON
- Azure CLI
- ARM templates
-->
