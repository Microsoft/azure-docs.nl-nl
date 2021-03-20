---
title: Virtuele netwerk eindpunten en regels voor data bases in Azure SQL Database
description: Een subnet markeren als een service-eind punt voor een virtueel netwerk. Voeg vervolgens het eind punt als een virtuele netwerk regel toe aan de ACL voor uw data base. Uw data base accepteert vervolgens communicatie van alle virtuele machines en andere knoop punten in het subnet.
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
ms.openlocfilehash: 0dcffe6731c177d1d45c569361fcb200f23af86c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99095355"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-servers-in-azure-sql-database"></a>Service-eindpunten en regels voor virtuele netwerken gebruiken voor servers in Azure SQL Database

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

De *regels voor virtuele netwerken* zijn een firewall beveiligings functie waarmee wordt bepaald of de server voor uw data bases en elastische pools in [Azure SQL database](sql-database-paas-overview.md) of voor uw toegewezen SQL-groep (voorheen SQL DW)-data bases in [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) communicatie accepteert die wordt verzonden vanuit bepaalde subnetten in virtuele netwerken. In dit artikel wordt uitgelegd waarom de regels voor virtuele netwerken soms de beste manier zijn om communicatie met uw data base in SQL Database en Azure Synapse Analytics veilig toe te staan.

> [!NOTE]
> Dit artikel is van toepassing op zowel SQL Database als Azure Synapse Analytics. Ter vereenvoudiging verwijst de term *Data Base* naar beide data bases in SQL database en Azure Synapse Analytics. Alle verwijzingen naar de *Server* verwijzen ook naar de [logische SQL-Server](logical-servers.md) die als host fungeert voor SQL database en Azure Synapse Analytics.

Als u een regel voor een virtueel netwerk wilt maken, moet er eerst een [service-eind punt voor het virtuele netwerk][vm-virtual-network-service-endpoints-overview-649d] zijn voor de regel waarnaar moet worden verwezen.

## <a name="create-a-virtual-network-rule"></a>Een regel voor een virtueel netwerk maken

Als u alleen een regel voor het virtuele netwerk wilt maken, gaat u verder met de stappen en uitleg [verderop in dit artikel](#anchor-how-to-by-using-firewall-portal-59j).

## <a name="details-about-virtual-network-rules"></a>Details over regels voor virtuele netwerken

In deze sectie worden verschillende details over regels voor het virtuele netwerk beschreven.

### <a name="only-one-geographic-region"></a>Slechts één geografische regio

Elk service-eind punt van een virtueel netwerk is alleen van toepassing op één Azure-regio. Het eind punt schakelt geen andere regio's in om communicatie van het subnet te accepteren.

Een regel voor het virtuele netwerk is beperkt tot de regio waarin het onderliggende eind punt van toepassing is.

### <a name="server-level-not-database-level"></a>Server niveau, niet database niveau

Elke regel voor het virtuele netwerk is van toepassing op uw hele server en niet alleen op een bepaalde data base op de server. Met andere woorden, de regels voor virtuele netwerken gelden op server niveau, niet op database niveau.

IP-regels kunnen daarentegen op beide niveaus van toepassing zijn.

### <a name="security-administration-roles"></a>Beveiligings beheer rollen

Er is een schei ding van beveiligings rollen in het beheer van service-eind punten van virtuele netwerken. Actie is vereist voor elk van de volgende rollen:

- **Netwerk beheerder (rol [netwerk bijdrager](../../role-based-access-control/built-in-roles.md#network-contributor) ):** &nbsp; Schakel het eind punt in.
- **Database beheerder ([SQL Server rol Inzender](../../role-based-access-control/built-in-roles.md#sql-server-contributor) ):** &nbsp; Werk de toegangs beheer lijst (ACL) bij om het opgegeven subnet toe te voegen aan de-server.

#### <a name="azure-rbac-alternative"></a>Alternatief voor Azure RBAC

De rollen van de netwerk beheerder en de database beheerder hebben meer mogelijkheden dan nodig zijn voor het beheren van regels voor het virtuele netwerk. Er is slechts een subset van de mogelijkheden nodig.

U hebt de mogelijkheid om op [rollen gebaseerd toegangs beheer (RBAC)][rbac-what-is-813s] in azure te gebruiken om één aangepaste rol te maken die alleen de benodigde subset van mogelijkheden heeft. De aangepaste rol kan worden gebruikt in plaats van de netwerk beheerder of de database beheerder. De surface area van uw beveiligings risico is lager als u een gebruiker toevoegt aan een aangepaste rol en de gebruiker toevoegt aan de andere twee belang rijke beheerders rollen.

> [!NOTE]
> In sommige gevallen bevinden de data base in SQL Database en het subnet van het virtuele netwerk zich in verschillende abonnementen. In deze gevallen moet u ervoor zorgen dat u de volgende configuraties hebt:
>
> - Beide abonnementen moeten zich in dezelfde Azure Active Directory-Tenant (Azure AD) bezitten.
> - De gebruiker beschikt over de vereiste machtigingen voor het initiëren van bewerkingen, zoals het inschakelen van service-eind punten en het toevoegen van een subnet van een virtueel netwerk aan de opgegeven server.
> - Voor beide abonnementen moet de micro soft. SQL-provider zijn geregistreerd.

## <a name="limitations"></a>Beperkingen

Voor SQL Database heeft de functie regels voor virtuele netwerken de volgende beperkingen:

- In de firewall voor uw data base in SQL Database verwijst elke regel van het virtuele netwerk naar een subnet. Al deze subnetten waarnaar wordt verwezen, moeten worden gehost in dezelfde geografische regio die de data base host.
- Elke-server kan Maxi maal 128 ACL-vermeldingen hebben voor elk virtueel netwerk.
- De regels voor virtuele netwerken zijn alleen van toepassing op Azure Resource Manager virtuele netwerken en niet op [klassieke implementatie model][arm-deployment-model-568f] netwerken.
- Als u service-eind punten voor virtuele netwerken inschakelt voor SQL Database, worden ook de eind punten voor Azure Database for MySQL en Azure Database for PostgreSQL ingeschakeld. Als eind **punten zijn ingesteld op aan,** wordt geprobeerd verbinding te maken vanaf de eind punten van uw Azure Database for MySQL of Azure database for PostgreSQL instanties mislukken.
  - De onderliggende reden is dat Azure Database for MySQL en Azure Database for PostgreSQL waarschijnlijk geen regel voor het virtuele netwerk zijn geconfigureerd. U moet een regel voor het virtuele netwerk configureren voor Azure Database for MySQL en Azure Database for PostgreSQL, waarna de verbinding wordt voltooid.
  - Als u firewall regels voor virtuele netwerken wilt definiëren op een logische SQL-Server die al is geconfigureerd met persoonlijke eind punten, stelt u **toegang tot open bare netwerk weigeren** in op **Nee**.
- IP-adresbereiken op de firewall zijn van toepassing op de volgende netwerk items, maar de regels voor het virtuele netwerk zijn niet:
  - [Virtueel particulier netwerk (VPN) van site-naar-site (S2S)][vpn-gateway-indexmd-608y]
  - On-premises via [Azure ExpressRoute](../../expressroute/index.yml)

### <a name="considerations-when-you-use-service-endpoints"></a>Overwegingen bij het gebruik van service-eind punten

Wanneer u service-eind punten voor SQL Database gebruikt, raadpleegt u de volgende overwegingen:

- **Uitgaand naar Azure SQL Database open bare Ip's is vereist.** Netwerk beveiligings groepen (Nsg's) moeten worden geopend om IP-adressen te SQL Database om connectiviteit mogelijk te maken. U kunt dit doen met behulp van NSG- [service Tags](../../virtual-network/network-security-groups-overview.md#service-tags) voor SQL database.

### <a name="expressroute"></a>ExpressRoute

Als u [ExpressRoute](../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) gebruikt vanuit uw locatie, voor open bare peering of micro soft-peering moet u de NAT IP-adressen identificeren die worden gebruikt. Voor openbare peering gebruikt elk ExpressRoute-circuit standaard twee NAT IP-adressen. Deze worden toegepast op Azure-serviceverkeer wanneer het verkeer het Microsoft Azure-backbone-netwerk binnenkomt. Voor micro soft-peering worden de NAT IP-adressen die worden gebruikt door de klant of de service provider verschaft. Voor toegang tot uw serviceresources moet u deze openbare IP-adressen toestaan in de instelling voor IP-firewall voor de resource. Wanneer u op zoek bent naar de IP-adressen van uw ExpressRoute-circuit voor openbare peering, opent u [een ondersteuningsticket met ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via de Azure-portal. Zie [NAT-vereisten voor open bare Azure-peering](../../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)voor meer informatie over NAT voor ExpressRoute open bare en micro soft-peering.

Als u communicatie vanuit uw circuit naar SQL Database wilt toestaan, moet u IP-netwerk regels maken voor de open bare IP-adressen van uw NAT.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-virtual-network-service-endpoints-with-azure-storage"></a>Gevolgen van het gebruik van service-eind punten voor virtuele netwerken met Azure Storage

Azure Storage heeft dezelfde functie geïmplementeerd waarmee u de connectiviteit met uw Azure Storage-account kunt beperken. Als u ervoor kiest om deze functie te gebruiken met een Azure Storage account dat SQL Database gebruikt, kunt u problemen ondervinden. Hierna volgt een lijst en bespreking van SQL Database-en Azure Synapse Analytics-functies die worden beïnvloed door dit.

### <a name="azure-synapse-analytics-polybase-and-copy-statement"></a>Azure Synapse Analytics poly base-en COPY-instructie

Poly base en de instructie COPY worden meestal gebruikt voor het laden van gegevens in azure Synapse Analytics van Azure Storage accounts voor gegevens opname met hoge door voer. Als het Azure Storage account waarvan u gegevens laadt, alleen toegang heeft tot een aantal virtuele netwerk subnetten, wordt de verbinding verbroken wanneer u poly base gebruikt en wordt de instructie COPY naar het opslag account onderbroken. Volg de stappen in deze sectie om scenario's voor importeren en exporteren in te scha kelen met behulp van kopiëren en poly Base met Azure Synapse Analytics verbinding maken met Azure Storage dat is beveiligd met een virtueel netwerk.

#### <a name="prerequisites"></a>Vereisten

- Installeer Azure PowerShell met behulp van [deze hand leiding](/powershell/azure/install-az-ps).
- Als u een algemeen v1-of Azure Blob Storage-account hebt, moet u eerst een upgrade uitvoeren naar de v2 voor algemeen gebruik door de stappen in [een upgrade naar een v2-opslag account voor algemeen gebruik te](../../storage/common/storage-account-upgrade.md)volgen.
- U moet **vertrouwde micro soft-Services toegang geven tot dit opslag account** ingeschakeld onder het menu Azure Storage account **firewalls en instellingen voor virtuele netwerken** . Als u deze configuratie inschakelt, kan poly base en de instructie COPY verbinding maken met het opslag account met behulp van sterke verificatie waarbij netwerk verkeer op de Azure-backbone blijft. Zie [deze hand leiding](../../storage/common/storage-network-security.md#exceptions)voor meer informatie.

> [!IMPORTANT]
> De Power shell-Azure Resource Manager module wordt nog steeds ondersteund door SQL Database, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. De AzureRM-module blijft tot ten minste december 2020 bugfixes ontvangen. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn vrijwel identiek. Zie [Introductie van de nieuwe Az-module van Azure PowerShell](/powershell/azure/new-azureps-module-az) voor meer informatie over de compatibiliteit van de argumenten.

#### <a name="steps"></a>Stappen

1. Als u een zelfstandige toegewezen SQL-groep hebt, registreert u uw SQL Server met Azure AD met behulp van Power shell:

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

   Deze stap is niet vereist voor toegewezen SQL-groepen in een Azure Synapse Analytics-werk ruimte.

1. Als u een Azure Synapse Analytics-werk ruimte hebt, registreert u de door het systeem beheerde identiteit van uw werk ruimte:

   1. Ga naar de Azure Synapse Analytics-werk ruimte in de Azure Portal.
   2. Ga naar het deel venster **Managed Identities** .
   3. Zorg ervoor dat de optie **pijp lijnen toestaan** is ingeschakeld.
   
1. Maak een **v2-opslag account voor algemeen gebruik** door de stappen in [een opslag account maken](../../storage/common/storage-account-create.md)te volgen.

   > [!NOTE]
   >
   > - Als u een algemeen v1-of Blob Storage-account hebt, moet u *eerst een upgrade uitvoeren naar v2* door de stappen in [een upgrade naar een v2-opslag account voor algemeen gebruik te](../../storage/common/storage-account-upgrade.md)volgen.
   > - Zie [bekende problemen met Azure data Lake Storage Gen2](../../storage/blobs/data-lake-storage-known-issues.md)voor bekende problemen met Azure data Lake Storage Gen2.

1. Ga onder uw opslag account naar **Access Control (IAM)** en selecteer **functie toewijzing toevoegen**. Wijs de Azure-rol **Storage BLOB data Inzender** toe aan de server of werk ruimte die als host fungeert voor uw toegewezen SQL-groep, die u hebt geregistreerd bij Azure AD.

   > [!NOTE]
   > Deze stap kan alleen worden uitgevoerd door leden met de bevoegdheid eigenaar van het opslag account. Zie [ingebouwde rollen van Azure](../../role-based-access-control/built-in-roles.md)voor verschillende ingebouwde rollen van Azure.
  
1. Poly base-verbinding met het Azure Storage-account inschakelen:

   1. Maak een database [hoofd sleutel](/sql/t-sql/statements/create-master-key-transact-sql) als u deze nog niet eerder hebt gemaakt.

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. Maak een database bereik referentie met **identiteit = ' managed service Identity '**.

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       >
       > - U hoeft geen geheim op te geven met een Azure Storage toegangs sleutel, omdat dit mechanisme gebruikmaakt van [beheerde identiteiten](../../active-directory/managed-identities-azure-resources/overview.md) onder de voor vallen.
       > - Voor poly base-connectiviteit moet de IDENTITEITs naam **Managed Service Identity** zijn om te werken met een Azure Storage account dat is beveiligd met een virtueel netwerk.

   1. Maak een externe gegevens bron met het `abfss://` schema voor het maken van verbinding met uw voor algemeen gebruik v2-opslag account met poly base.

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       >
       > - Als u al externe tabellen hebt gekoppeld aan een algemeen v1-of Blob Storage-account, moet u deze externe tabellen eerst neerzetten. Verwijder vervolgens de bijbehorende externe gegevens bron. Maak vervolgens een externe gegevens bron met het `abfss://` schema dat verbinding maakt met een v2-opslag account voor algemeen gebruik, zoals eerder is weer gegeven. Maak vervolgens alle externe tabellen opnieuw met behulp van deze nieuwe externe gegevens bron. U kunt de [wizard scripts genereren en publiceren](/sql/ssms/scripting/generate-and-publish-scripts-wizard) gebruiken om voor het gemak create-scripts te genereren voor alle externe tabellen.
       > - `abfss://`Zie [de Azure data Lake Storage Gen2 URI gebruiken](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md)voor meer informatie over het schema.
       > - Zie [deze hand leiding](/sql/t-sql/statements/create-external-data-source-transact-sql)voor meer informatie over het maken van een externe gegevens bron.

   1. Query's uitvoeren als normaal door gebruik te maken van [externe tabellen](/sql/t-sql/statements/create-external-table-transact-sql).

### <a name="sql-database-blob-auditing"></a>SQL Database BLOB-controle

Met Blob-controle worden controle logboeken naar uw eigen opslag account gepusht. Als dit opslag account gebruikmaakt van de functie voor de service-eind punten van het virtuele netwerk, wordt de connectiviteit van SQL Database naar het opslag account verbroken.

## <a name="add-a-virtual-network-firewall-rule-to-your-server"></a>Een firewall regel voor een virtueel netwerk toevoegen aan uw server

Lang geleden moesten voordat deze functie werd uitgebreid, de service-eind punten van het virtuele netwerk worden ingeschakeld voordat u een regel voor een virtueel netwerk in de firewall zou kunnen implementeren. De eind punten die betrekking hebben op een bepaald subnet van een virtueel netwerk en een data base in SQL Database. Vanaf januari 2018 kunt u deze vereiste omzeilen door de **IgnoreMissingVNetServiceEndpoint** -vlag in te stellen. U kunt nu een firewall regel voor een virtueel netwerk aan uw server toevoegen zonder de service-eind punten van het virtuele netwerk in te scha kelen.

Als u alleen een firewall regel instelt, wordt de server niet beveiligd. U moet ook service-eind punten van het virtuele netwerk inschakelen om de beveiliging van kracht te laten worden. Wanneer u service-eind punten inschakelt, wordt in het subnet van het virtuele netwerk downtime uitgevoerd totdat de overgang van uitgeschakeld naar op is voltooid. Deze periode van uitval tijd is vooral waar in de context van grote virtuele netwerken. U kunt de vlag **IgnoreMissingVNetServiceEndpoint** gebruiken om de downtime te verminderen of te elimineren tijdens de overgang.

U kunt de vlag **IgnoreMissingVNetServiceEndpoint** instellen met behulp van Power shell. Zie [Power shell voor het maken van een service-eind punt voor een virtueel netwerk en een regel voor SQL database][sql-db-vnet-service-endpoint-rule-powershell-md-52d]voor meer informatie.

## <a name="errors-40914-and-40615"></a>Fouten 40914 en 40615

Verbindings fout 40914 is gekoppeld aan *regels voor virtuele netwerken*, zoals opgegeven in het deel venster **Firewall** in de Azure Portal. Fout 40615 is vergelijkbaar, maar heeft betrekking op *IP-adres regels* op de firewall.

### <a name="error-40914"></a>Fout 40914

**Bericht tekst:** Kan de server *[Server naam]* die door de aanmelding is aangevraagd, niet openen. De client is niet gemachtigd om toegang te krijgen tot de server.

**Fout beschrijving:** De-client bevindt zich in een subnet met virtuele netwerk server-eind punten. Maar de server heeft geen regel voor het virtuele netwerk die het subnet aan het recht verleent om te communiceren met de data base.

**Fout oplossing:** Gebruik in het deel venster **firewall** van de Azure Portal het besturings element regels voor virtuele netwerken om [een regel voor het virtuele netwerk](#anchor-how-to-by-using-firewall-portal-59j) voor het subnet toe te voegen.

### <a name="error-40615"></a>Fout 40615

**Bericht tekst:** Kan de server niet openen, {0} die door de aanmelding is aangevraagd. De client met het IP-adres {1} is niet gemachtigd om toegang te krijgen tot de server.

**Fout beschrijving:** De client probeert verbinding te maken vanaf een IP-adres dat niet is gemachtigd om verbinding te maken met de server. De server firewall heeft geen IP-adres regel waarmee een client kan communiceren vanaf het opgegeven IP-adres naar de data base.

**Fout oplossing:** Voer het IP-adres van de client in als een IP-regel. Gebruik het deel venster **firewall** in de Azure Portal om deze stap uit te voeren.

<a name="anchor-how-to-by-using-firewall-portal-59j"></a>

## <a name="use-the-portal-to-create-a-virtual-network-rule"></a>De portal gebruiken om een regel voor een virtueel netwerk te maken

In deze sectie ziet u hoe u de [Azure Portal][http-azure-portal-link-ref-477t] kunt gebruiken om een *regel voor een virtueel netwerk* te maken in uw data base in SQL database. De regel vertelt uw data base om communicatie van een bepaald subnet te accepteren die als een service- *eind punt van een virtueel netwerk* is gemarkeerd.

> [!NOTE]
> Als u van plan bent een service-eind punt toe te voegen aan de firewall regels voor virtuele netwerken van uw server, moet u eerst controleren of service-eind punten zijn ingeschakeld voor het subnet.
>
> Als service-eind punten niet zijn ingeschakeld voor het subnet, vraagt de portal u om deze in te scha kelen. Selecteer de knop **inschakelen** in hetzelfde deel venster waarop u de regel toevoegt.

## <a name="powershell-alternative"></a>Power shell-alternatief

Met een script kunnen ook regels voor virtuele netwerken worden gemaakt met behulp van de Power shell-cmdlet **New-AzSqlServerVirtualNetworkRule** of [AZ Network vnet Create](/cli/azure/network/vnet#az-network-vnet-create). Als u geïnteresseerd bent, raadpleegt u [Power shell om een service-eind punt voor een virtueel netwerk en een regel voor SQL database te maken][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>REST API alternatief

Intern roepen de Power shell-cmdlets voor virtuele SQL-netwerk acties REST-Api's aan. U kunt de REST-Api's rechtstreeks aanroepen.

- [Regels voor virtueel netwerk: bewerkingen][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Vereisten

U moet al een subnet hebben dat is gelabeld met de specifieke naam van het eindpunt *type* van het virtuele netwerk dat relevant is voor SQL database.

- De relevante naam van het eindpunt type is **micro soft. SQL**.
- Zie [controleren of uw subnet een eind punt is][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]als uw subnet mogelijk niet is gelabeld met de type naam.

<a name="a-portal-steps-for-vnet-rule-200"></a>

## <a name="azure-portal-steps"></a>Azure Portal stappen

1. Meld u aan bij [Azure Portal][http-azure-portal-link-ref-477t].

1. Zoek en selecteer **SQL-servers** en selecteer vervolgens uw server. Onder **beveiliging** selecteert u **firewalls en virtuele netwerken**.

1. Stel **toegang tot Azure-Services toestaan** in op **uit**.

    > [!IMPORTANT]
    > Als u het besturings element instelt **op** aan, accepteert uw server communicatie vanaf elk subnet binnen de Azure-grens. Dit is communicatie die afkomstig is van een van de IP-adressen die worden herkend als die binnen bereiken die zijn gedefinieerd voor Azure-data centers. Het is mogelijk dat het besturings element dat is ingesteld op **aan** , overmatig toegankelijk is vanuit het beveiligings oogpunt van de weer gave. Met de functie voor het Microsoft Azure Virtual Network Service-eind punt in combi natie met de functie regels voor virtuele netwerken van SQL Database samen kan de beveiligings surface area worden verminderd.

1. Selecteer **+ bestaande toevoegen** in de sectie **virtuele netwerken** .

    ![Scherm opname van het selecteren en toevoegen van bestaande (subnet-eind punt, als een SQL-regel).][image-portal-firewall-vnet-add-existing-10-png]

1. In het nieuwe deel venster **maken/bijwerken** vult u de vakken in met de namen van uw Azure-resources.

    > [!TIP]
    > U moet het juiste adres voorvoegsel voor uw subnet toevoegen. U kunt de waarde **voor het adres voorvoegsel** vinden in de portal. Ga naar **alle resources** &gt; **alle typen** &gt; **virtuele netwerken**. Met het filter worden uw virtuele netwerken weer gegeven. Selecteer het virtuele netwerk en selecteer vervolgens **subnetten**. De kolom **adres bereik** bevat het adres voorvoegsel dat u nodig hebt.

    ![Scherm opname waarin de vakken voor de nieuwe regel worden ingevuld.][image-portal-firewall-create-update-vnet-rule-20-png]

1. Selecteer de knop **OK** onder aan het deel venster.

1. Zie de resulterende regel voor het virtuele netwerk in het deel venster **firewall** .

    ![Scherm afbeelding met de nieuwe regel in het deel venster Firewall.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> De volgende statussen of provincies zijn van toepassing op de regels:
>
> - **Gereed**: geeft aan dat de bewerking die u hebt gestart, is geslaagd.
> - **Mislukt**: geeft aan dat de bewerking die u hebt gestart, is mislukt.
> - **Verwijderd**: alleen van toepassing op de Verwijder bewerking en geeft aan dat de regel is verwijderd en niet langer van toepassing is.
> - **InProgress**: geeft aan dat de bewerking wordt uitgevoerd. De oude regel is van toepassing terwijl de bewerking zich in deze status bevindt.

<a name="anchor-how-to-links-60h"></a>

## <a name="related-articles"></a>Verwante artikelen:

- [Service-eindpunten voor een virtueel Azure-netwerk][vm-virtual-network-service-endpoints-overview-649d]
- [Firewall regels op server niveau en database niveau][sql-db-firewall-rules-config-715d]

## <a name="next-steps"></a>Volgende stappen

- [Power shell gebruiken om een service-eind punt voor een virtueel netwerk te maken en vervolgens een regel voor het virtuele netwerk voor SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Regels voor virtueel netwerk: bewerkingen][rest-api-virtual-network-rules-operations-862r] met rest-api's

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
