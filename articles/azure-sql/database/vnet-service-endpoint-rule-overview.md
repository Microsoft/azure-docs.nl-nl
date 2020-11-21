---
title: VNet-eind punten en-regels voor data bases in Azure SQL Database
description: Een subnet markeren als Virtual Network Service-eind punt. Vervolgens wordt het eind punt als een regel voor het virtuele netwerk naar de toegangs beheer lijst van uw data base. Uw data base accepteert vervolgens communicatie van alle virtuele machines en andere knoop punten in het subnet.
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
ms.openlocfilehash: 97be3bf0ecec20c4bf2e1633f893c9aa0d9ba49d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020279"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-servers-in-azure-sql-database"></a>Virtuele netwerk service-eind punten en-regels gebruiken voor servers in Azure SQL Database
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

*Regels voor virtuele netwerken* zijn één firewall beveiligings functie waarmee wordt bepaald of de server voor uw data bases en elastische pools in [Azure SQL database](sql-database-paas-overview.md) of voor uw data bases in [Azure Synapse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) communicatie accepteert die wordt verzonden vanuit bepaalde subnetten in virtuele netwerken. In dit artikel wordt uitgelegd waarom de regel functie van het virtuele netwerk soms de beste optie is voor het veilig toestaan van communicatie met uw data base in Azure SQL Database en Azure Synapse Analytics (voorheen SQL Data Warehouse).

> [!NOTE]
> Dit artikel is van toepassing op zowel Azure SQL Database als Azure Synapse Analytics. Ter vereenvoudiging verwijst de term “database” naar beide databases in Azure SQL Database en Azure Synapse Analytics. Alle verwijzingen naar “server” verwijzen ook naar de [logische SQL Server](logical-servers.md) die als host fungeert voor Azure SQL Database en Azure Synapse Analytics.

Als u een regel voor een virtueel netwerk wilt maken, moet er eerst een [service-eind punt voor het virtuele netwerk][vm-virtual-network-service-endpoints-overview-649d] zijn voor de regel waarnaar moet worden verwezen.

## <a name="how-to-create-a-virtual-network-rule"></a>Een regel voor een virtueel netwerk maken

Als u alleen een regel voor een virtueel netwerk maakt, kunt u verdergaan met de stappen en uitleg [verderop in dit artikel](#anchor-how-to-by-using-firewall-portal-59j).

## <a name="details-about-virtual-network-rules"></a>Details over regels voor virtuele netwerken

In deze sectie worden verschillende details over regels voor het virtuele netwerk beschreven.

### <a name="only-one-geographic-region"></a>Slechts één geografische regio

Elk Virtual Network Service-eind punt is alleen van toepassing op één Azure-regio. Met het eind punt kunnen andere regio's geen communicatie van het subnet accepteren.

Een regel voor het virtuele netwerk is beperkt tot de regio waarin het onderliggende eind punt van toepassing is.

### <a name="server-level-not-database-level"></a>Server niveau, niet database niveau

Elke regel voor het virtuele netwerk is van toepassing op uw hele server en niet alleen op een bepaalde data base op de server. Met andere woorden, de regel voor het virtuele netwerk geldt op server niveau, niet op database niveau.

- IP-regels kunnen daarentegen op beide niveaus van toepassing zijn.

### <a name="security-administration-roles"></a>Beveiligings beheer rollen

Er is een schei ding van beveiligings rollen in het beheer van Virtual Network Service-eind punten. Actie is vereist voor elk van de volgende rollen:

- **Netwerk beheerder:** &nbsp; Schakel het eind punt in.
- **Database beheerder:** &nbsp; Werk de toegangs beheer lijst (ACL) bij om het opgegeven subnet toe te voegen aan de-server.

*Alternatief voor Azure RBAC:*

De rollen van de netwerk beheerder en de database beheerder hebben meer mogelijkheden dan nodig zijn voor het beheren van regels voor het virtuele netwerk. Er is slechts een subset van de mogelijkheden nodig.

U hebt de mogelijkheid om op [rollen gebaseerd toegangs beheer (Azure RBAC)][rbac-what-is-813s] in azure te gebruiken om één aangepaste rol te maken die alleen de benodigde subset van mogelijkheden heeft. De aangepaste rol kan worden gebruikt in plaats van de netwerk beheerder of de database beheerder. De surface area van uw beveiligings risico is lager als u een gebruiker toevoegt aan een aangepaste rol, en de gebruiker toevoegt aan de andere twee belang rijke beheerders rollen.

> [!NOTE]
> In sommige gevallen bevinden de data base in Azure SQL Database en het VNet-subnet deel uit van verschillende abonnementen. In deze gevallen moet u ervoor zorgen dat u de volgende configuraties hebt:
>
> - Beide abonnementen moeten zich in dezelfde Azure Active Directory Tenant bezitten.
> - De gebruiker beschikt over de vereiste machtigingen voor het initiëren van bewerkingen, zoals het inschakelen van service-eind punten en het toevoegen van een VNet-subnet aan de opgegeven server.
> - Voor beide abonnementen moet de micro soft. SQL-provider zijn geregistreerd.

## <a name="limitations"></a>Beperkingen

Voor Azure SQL Database heeft de functie regels voor virtuele netwerken de volgende beperkingen:

- In de firewall voor uw data base in Azure SQL Database verwijst elke regel van het virtuele netwerk naar een subnet. Al deze subnetten waarnaar wordt verwezen, moeten worden gehost in dezelfde geografische regio die de data base host.

- Elke-server kan Maxi maal 128 ACL-vermeldingen hebben voor elk gegeven virtueel netwerk.

- De regels voor virtuele netwerken zijn alleen van toepassing op Azure Resource Manager virtuele netwerken. en niet op [klassieke implementatie model][arm-deployment-model-568f] netwerken.

- Als u service-eind punten voor virtuele netwerken inschakelt voor Azure SQL Database, worden ook de eind punten ingeschakeld voor de services MySQL en PostgreSQL Azure. Met eind punten op probeert echter verbinding te maken vanaf de eind punten naar uw MySQL-of PostgreSQL-instanties.
  - De onderliggende reden is dat MySQL en PostgreSQL waarschijnlijk geen regel voor het virtuele netwerk zijn geconfigureerd. U moet een regel voor het virtuele netwerk configureren voor Azure Database for MySQL en PostgreSQL en de verbinding slaagt.
  - Als u VNet-firewall regels wilt definiëren op een logische SQL-Server die al is geconfigureerd met persoonlijke eind punten, stelt u **toegang tot openbaar netwerk weigeren** in op **Nee**.

- IP-adresbereiken op de firewall zijn van toepassing op de volgende netwerk items, maar de regels voor het virtuele netwerk doen dit niet:
  - [Virtueel particulier netwerk (VPN) van site-naar-site (S2S)][vpn-gateway-indexmd-608y]
  - On-premises via [ExpressRoute](../../expressroute/index.yml)

### <a name="considerations-when-using-service-endpoints"></a>Overwegingen bij het gebruik van service-eind punten

Wanneer u service-eind punten voor Azure SQL Database gebruikt, raadpleegt u de volgende overwegingen:

- **Uitgaand naar Azure SQL database open bare ip's is vereist**: netwerk beveiligings groepen (nsg's) moeten zijn geopend voor het Azure SQL database van IP-adressen om verbinding te kunnen maken. U kunt dit doen met behulp van NSG- [service Tags](../../virtual-network/network-security-groups-overview.md#service-tags) voor Azure SQL database.

### <a name="expressroute"></a>ExpressRoute

Als u [ExpressRoute](../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) gebruikt vanuit uw on-premises netwerk voor openbare peering of Microsoft-peering, moet u de NAT IP-adressen opgeven die worden gebruikt. Voor openbare peering gebruikt elk ExpressRoute-circuit standaard twee NAT IP-adressen. Deze worden toegepast op Azure-serviceverkeer wanneer het verkeer het Microsoft Azure-backbone-netwerk binnenkomt. Voor Microsoft-peering worden de NAT IP-adressen die worden gebruikt opgegeven door de klant of de serviceprovider. Voor toegang tot uw serviceresources moet u deze openbare IP-adressen toestaan in de instelling voor IP-firewall voor de resource. Wanneer u op zoek bent naar de IP-adressen van uw ExpressRoute-circuit voor openbare peering, opent u [een ondersteuningsticket met ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via de Azure-portal. Meer informatie over [NAT voor openbare peering en Microsoft-peering met ExpressRoute.](../../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)
  
Als u communicatie vanuit uw circuit naar Azure SQL Database wilt toestaan, moet u IP-netwerk regels maken voor de open bare IP-adressen van uw NAT.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Gevolgen van het gebruik van VNet-service-eind punten met Azure Storage

Azure Storage heeft dezelfde functie geïmplementeerd waarmee u de connectiviteit met uw Azure Storage-account kunt beperken. Als u ervoor kiest om deze functie te gebruiken met een Azure Storage-account dat wordt gebruikt door Azure SQL Database, kunt u problemen ondervinden. Hierna volgt een lijst en bespreking van Azure SQL Database-en Azure Synapse Analytics-functies waarop dit van invloed is.

### <a name="azure-synapse-polybase-and-copy-statement"></a>Azure Synapse poly base-en kopieer instructie

Poly base en de instructie COPY worden meestal gebruikt voor het laden van gegevens in azure Synapse Analytics van Azure Storage accounts voor gegevens opname met hoge door voer. Als de Azure Storage account waarvan u gegevens wilt laden, alleen toegang heeft tot een set VNet-subnetten, wordt de verbinding verbroken wanneer u poly base gebruikt en wordt de instructie COPY naar het opslag account onderbroken. Voor het inschakelen van scenario's voor importeren en exporteren met behulp van COPY en poly Base met Azure Synapse Analytics verbinding maken met Azure Storage dat is beveiligd met VNet, volgt u de stappen die hieronder worden beschreven:

#### <a name="prerequisites"></a>Vereisten

- Installeer Azure PowerShell met behulp van deze [gids](/powershell/azure/install-az-ps).
- Als u een v1-of blob-opslagaccount voor algemeen gebruik hebt, moet u eerst een upgrade uitvoeren naar de v2 voor algemeen gebruik met behulp van deze [gids](../../storage/common/storage-account-upgrade.md).
- U moet **vertrouwde Microsoft-services toegang geven tot dit opslagaccount** ingeschakeld onder het instellingenmenu van uw Azure Storage-account **Firewalls en virtuele netwerken**. Als u deze configuratie inschakelt, kan poly base en de instructie COPY verbinding maken met het opslag account met behulp van sterke verificatie waarbij netwerk verkeer op de Azure-backbone blijft. Raadpleeg deze [gids](../../storage/common/storage-network-security.md#exceptions) voor meer informatie.

> [!IMPORTANT]
> De module PowerShell Azure Resource Manager wordt nog steeds ondersteund in Azure SQL Database, maar alle toekomstige ontwikkeling is voor de Az.Sql-module. De AzureRM-module blijft tot ten minste december 2020 bugfixes ontvangen.  De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn vrijwel identiek. Zie [Introductie van de nieuwe Az-module van Azure PowerShell](/powershell/azure/new-azureps-module-az) voor meer informatie over de compatibiliteit van de argumenten.

#### <a name="steps"></a>Stappen

1. Registreer in Power shell **uw server** die als host fungeert voor Azure Synapse met Azure Active Directory (Aad):

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

1. **Een opslagaccount voor algemeen gebruik v2 maken** met deze [gids](../../storage/common/storage-account-create.md).

   > [!NOTE]
   >
   > - Als u een v1-of blob-opslagaccount voor algemeen gebruik hebt, moet u **eerst een upgrade uitvoeren naar de v2** voor algemeen gebruik met behulp van deze [gids](../../storage/common/storage-account-upgrade.md).
   > - Raadpleeg deze [hand leiding](../../storage/blobs/data-lake-storage-known-issues.md)voor bekende problemen met Azure data Lake Storage Gen2.

1. Navigeer onder uw opslagaccount naar **Access Control (IAM)** en selecteer **Roltoewijzing toevoegen**. Wijs de Azure-rol **Storage BLOB data Inzender** toe aan de server die als host fungeert voor uw Azure Synapse Analytics, die u bij Azure Active Directory (Aad) hebt geregistreerd, zoals in stap #1.

   > [!NOTE]
   > Deze stap kan alleen worden uitgevoerd door leden met de bevoegdheid eigenaar van het opslag account. Raadpleeg deze [gids](../../role-based-access-control/built-in-roles.md) voor verschillende ingebouwde Azure-rollen.
  
1. **Poly base-verbinding met het Azure Storage-account:**

   1. Maak een database **[hoofd sleutel](/sql/t-sql/statements/create-master-key-transact-sql)** als u deze nog niet eerder hebt gemaakt:

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. Create Data Base scoped Credential met **Identity = ' managed service Identity '**:

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       >
       > - U hoeft geen geheim op te geven met Azure Storage toegangs sleutel, omdat dit mechanisme gebruikmaakt van [beheerde identiteiten](../../active-directory/managed-identities-azure-resources/overview.md) onder de voor vallen.
       > - De IDENTITEITs naam moet **Managed Service Identity** voor poly base-connectiviteit zijn om te werken met Azure Storage account dat is beveiligd met VNet.

   1. Maak een externe gegevens bron met een `abfss://` schema om verbinding te maken met uw v2-opslag account voor algemeen gebruik met poly Base:

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       >
       > - Als u al externe tabellen hebt gekoppeld aan het algemeen-en Blob Storage-account, moet u deze externe tabellen eerst neerzetten en vervolgens de bijbehorende externe gegevens bron verwijderen. Maak vervolgens een externe gegevens bron met `abfss://` een schema dat verbinding maakt met het v2-opslag account voor algemeen gebruik en maak alle externe tabellen opnieuw met behulp van deze nieuwe externe gegevens bron. U kunt de [wizard scripts genereren en publiceren](/sql/ssms/scripting/generate-and-publish-scripts-wizard) gebruiken om voor het gemak create-scripts te genereren voor alle externe tabellen.
       > - `abfss://`Raadpleeg deze [hand leiding](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md)voor meer informatie over het schema.
       > - Raadpleeg deze [hand leiding](/sql/t-sql/statements/create-external-data-source-transact-sql)voor meer informatie over het maken van een externe gegevens bron.

   1. Query's uitvoeren als normaal met [externe tabellen](/sql/t-sql/statements/create-external-table-transact-sql).

### <a name="azure-sql-database-blob-auditing"></a>Azure SQL Database BLOB-controle

Met Blob-controle worden controle logboeken naar uw eigen opslag account gepusht. Als dit opslag account gebruikmaakt van de functie voor de VNet-service-eind punten, wordt de verbinding van Azure SQL Database naar het opslag account verbroken.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Een VNet-firewall regel toevoegen aan uw server zonder de VNet-service-eind punten in te scha kelen

Lang geleden moesten voordat deze functie werd verbeterd, de VNet-service-eind punten worden ingeschakeld voordat u een live VNet-regel in de firewall zou kunnen implementeren. De eind punten die betrekking hebben op een bepaald VNet-subnet naar een data base in Azure SQL Database. Maar nu vanaf januari 2018, kunt u deze vereiste omzeilen door de **IgnoreMissingVNetServiceEndpoint** -vlag in te stellen.

Als u alleen een firewall regel instelt, wordt de server niet beveiligd. U moet ook VNet-service-eind punten inschakelen om de beveiliging van kracht te laten worden. Wanneer u service-eind punten inschakelt, wordt de downtime van uw VNet-subnet in stand gezet totdat de overgang van uit naar wordt voltooid. Dit geldt met name in de context van grote VNets. U kunt de vlag **IgnoreMissingVNetServiceEndpoint** gebruiken om de downtime te verminderen of te elimineren tijdens de overgang.

U kunt de vlag **IgnoreMissingVNetServiceEndpoint** instellen met behulp van Power shell. Zie [Power shell om een Virtual Network Service-eind punt en-regel voor Azure SQL database te maken][sql-db-vnet-service-endpoint-rule-powershell-md-52d]voor meer informatie.

## <a name="errors-40914-and-40615"></a>Fouten 40914 en 40615

Verbindings fout 40914 is gekoppeld aan *regels voor virtuele netwerken*, zoals opgegeven in het deel venster Firewall in de Azure Portal. Fout 40615 is vergelijkbaar, maar heeft betrekking op *IP-adres regels* op de firewall.

### <a name="error-40914"></a>Fout 40914

*Bericht tekst:* Kan de server *[Server naam]* die door de aanmelding is aangevraagd, niet openen. De client is niet gemachtigd om toegang te krijgen tot de server.

*Fout beschrijving:* De-client bevindt zich in een subnet met virtuele netwerk server-eind punten. Maar de server heeft geen regel voor het virtuele netwerk die het subnet aan het recht verleent om te communiceren met de data base.

*Fout oplossing:* Gebruik in het deel venster Firewall van de Azure Portal het besturings element regels voor virtuele netwerken om [een regel voor het virtuele netwerk](#anchor-how-to-by-using-firewall-portal-59j) voor het subnet toe te voegen.

### <a name="error-40615"></a>Fout 40615

*Bericht tekst:* Kan de server niet openen, {0} die door de aanmelding is aangevraagd. De client met het IP-adres {1} is niet gemachtigd om toegang te krijgen tot de server.

*Fout beschrijving:* De client probeert verbinding te maken vanaf een IP-adres dat niet is gemachtigd om verbinding te maken met de server. De server firewall heeft geen IP-adres regel waarmee een client kan communiceren vanaf het opgegeven IP-adres naar de data base.

*Fout oplossing:* Voer het IP-adres van de client in als een IP-regel. Doe dit met behulp van het deelvenster Firewall in de Azure Portal.

<a name="anchor-how-to-by-using-firewall-portal-59j"></a>

## <a name="portal-can-create-a-virtual-network-rule"></a>De portal kan een regel voor een virtueel netwerk maken

In deze sectie ziet u hoe u de [Azure Portal][http-azure-portal-link-ref-477t] kunt gebruiken om een *regel voor een virtueel netwerk* te maken in uw data base in Azure SQL database. De regel vertelt uw data base om communicatie te accepteren van een bepaald subnet dat is gelabeld als een *Virtual Network Service-eind punt*.

> [!NOTE]
> Als u van plan bent een service-eind punt toe te voegen aan de VNet-firewall regels van uw server, moet u eerst controleren of service-eind punten zijn ingeschakeld voor het subnet.
>
> Als service-eind punten niet zijn ingeschakeld voor het subnet, vraagt de portal u om deze in te scha kelen. Klik op de knop **inschakelen** op dezelfde Blade waarop u de regel toevoegt.

## <a name="powershell-alternative"></a>Power shell-alternatief

Met een script kunnen ook regels voor virtuele netwerken worden gemaakt met behulp van Power shell-cmdlet **New-AzSqlServerVirtualNetworkRule** of [AZ Network vnet Create](/cli/azure/network/vnet#az-network-vnet-create). Zie [Power shell om een Virtual Network Service-eind punt en regel voor Azure SQL database te maken][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>REST API alternatief

Intern, de Power shell-cmdlets voor SQL VNet-acties roepen REST-Api's. U kunt de REST-Api's rechtstreeks aanroepen.

- [Virtual Network regels: bewerkingen][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Vereisten

U moet al een subnet hebben dat is gelabeld met de specifieke naam van het Virtual Network service-eindpunt *type* dat relevant is voor Azure SQL database.

- De relevante naam van het eindpunt type is **micro soft. SQL**.
- Zie [controleren of uw subnet een eind punt is][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]als uw subnet mogelijk niet is gelabeld met de type naam.

<a name="a-portal-steps-for-vnet-rule-200"></a>

## <a name="azure-portal-steps"></a>Azure Portal stappen

1. Meld u aan bij de [Azure-portal][http-azure-portal-link-ref-477t].

2. Zoek en selecteer **SQL-servers** en selecteer vervolgens uw server. Onder **beveiliging** selecteert u **firewalls en virtuele netwerken**.

3. Stel het besturings element **toegang tot Azure-Services toestaan** in op uit.

    > [!IMPORTANT]
    > Als u het besturings element instelt op aan, accepteert uw server communicatie vanaf elk subnet binnen de grens van Azure, d.w.z. afkomstig van een van de IP-adressen die worden herkend als die binnen bereiken die zijn gedefinieerd voor Azure data centers. Het is mogelijk dat het besturings element dat is ingesteld op aan, overmatig toegankelijk is vanuit het beveiligings oogpunt van de weer gave. Met de functie Microsoft Azure Virtual Network Service-eind punt, in combi natie met de regel functie voor virtuele netwerken van SQL Database, kan uw beveiligings surface area worden verminderd.

4. Klik in de sectie **virtuele netwerken** op het besturings element **+ bestaande toevoegen** .

    ![Klik op bestaande toevoegen (subnet-eind punt, als een SQL-regel).][image-portal-firewall-vnet-add-existing-10-png]

5. In het nieuwe deel venster **maken/bijwerken** vult u de besturings elementen in met de namen van uw Azure-resources.

    > [!TIP]
    > U moet het juiste **adres voorvoegsel** voor uw subnet toevoegen. U kunt de waarde vinden in de portal.
    > Ga naar alle **resources** &gt; **alle typen** &gt; **virtuele netwerken**. Met het filter worden uw virtuele netwerken weer gegeven. Klik op uw virtuele netwerk en klik vervolgens op **subnetten**. De kolom **adres bereik** bevat het adres voorvoegsel dat u nodig hebt.

    ![Vul velden in voor nieuwe regel.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Klik op de knop **OK** onder aan het deel venster.

7. Zie de resulterende regel voor het virtuele netwerk in het deel venster Firewall.

    ![Zie de nieuwe regel in het deel venster Firewall.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> De volgende statussen of provincies zijn van toepassing op de regels:
>
> - **Gereed:** Geeft aan dat de bewerking die u hebt gestart, is geslaagd.
> - **Mislukt:** Geeft aan dat de bewerking die u hebt gestart, is mislukt.
> - **Verwijderd:** Is alleen van toepassing op de Verwijder bewerking en geeft aan dat de regel is verwijderd en niet langer van toepassing is.
> - **InProgress:** Geeft aan dat de bewerking wordt uitgevoerd. De oude regel is van toepassing terwijl de bewerking zich in deze status bevindt.

<a name="anchor-how-to-links-60h"></a>

## <a name="related-articles"></a>Verwante artikelen:

- [Service-eindpunten voor een virtueel Azure-netwerk][vm-virtual-network-service-endpoints-overview-649d]
- [Firewall regels op server niveau en database niveau][sql-db-firewall-rules-config-715d]

## <a name="next-steps"></a>Volgende stappen

- [Gebruik Power shell om een service-eind punt voor een virtueel netwerk te maken en vervolgens een regel voor het virtuele netwerk voor Azure SQL Database.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Virtual Network regels: bewerkingen][rest-api-virtual-network-rules-operations-862r] met rest-api's

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
