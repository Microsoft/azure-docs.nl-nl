---
title: Beveiligingsoverwegingen
description: Hierin wordt de basis beveiligings infrastructuur beschreven die services voor gegevens verplaatsing in Azure Data Factory gebruiken om uw gegevens te beveiligen.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/26/2020
ms.openlocfilehash: 08a20349d012652fb15dc1818d0201d93175aed8
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550569"
---
# <a name="security-considerations-for-data-movement-in-azure-data-factory"></a>Beveiligings overwegingen voor het verplaatsen van gegevens in Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
>
> * [Versie 1:](v1/data-factory-data-movement-security-considerations.md)
> * [Huidige versie](data-movement-security-considerations.md)

 [!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt een basis beveiligings infrastructuur beschreven die services voor gegevens verplaatsing in Azure Data Factory gebruiken om uw gegevens te beveiligen. Data Factory beheer bronnen zijn gebaseerd op de Azure-beveiligings infrastructuur en gebruiken alle mogelijke beveiligings maatregelen die door Azure worden geboden.

In een Data Factory-oplossing maakt u een of meer gegevens[pijplijnen](concepts-pipelines-activities.md). Een pijplijn is een logische groep activiteiten die samen een taak uitvoeren. Deze pijp lijnen bevinden zich in de regio waar de data factory is gemaakt.

Hoewel Data Factory in slechts enkele regio's beschikbaar is, is de service voor gegevens verplaatsing [wereld wijd beschikbaar](concepts-integration-runtime.md#integration-runtime-location) om te zorgen voor de naleving van de gegevens, de efficiëntie en de verminderde kosten voor het netwerk verkeer.

Azure Data Factory met inbegrip van Azure Integration Runtime en zelf-hostende Integration Runtime slaat geen tijdelijke gegevens op, worden geen gegevens of Logboeken in de cache opgeslagen, met uitzonde ring van gekoppelde service referenties voor Cloud gegevensopslag, die zijn versleuteld met behulp van certificaten. Met Data Factory kunt u gegevensgestuurde werk stromen maken om de verplaatsing van gegevens te organiseren tussen [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)en gegevens verwerking met behulp van [Compute Services](compute-linked-services.md) in andere regio's of in een on-premises omgeving. U kunt werk stromen ook controleren en beheren met behulp van Sdk's en Azure Monitor.

Data Factory is gecertificeerd voor:

| **[CSA ster certificering](https://www.microsoft.com/trustcenter/compliance/csa-star-certification)** |
| :----------------------------------------------------------- |
| **[ISO 20000-1:2011](https://www.microsoft.com/trustcenter/Compliance/ISO-20000-1)** |
| **[ISO 22301:2012](/compliance/regulatory/offering-iso-22301)** |
| **[ISO 27001:2013](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001)** |
| **[ISO 27017:2015](https://www.microsoft.com/trustcenter/compliance/iso-iec-27017)** |
| **[ISO 27018:2014](https://www.microsoft.com/trustcenter/compliance/iso-iec-27018)** |
| **[ISO 9001:2015](https://www.microsoft.com/trustcenter/compliance/iso-9001)** |
| **[SOC 1, 2, 3](https://www.microsoft.com/trustcenter/compliance/soc)** |
| **[HIPAA BAA](/compliance/regulatory/offering-hipaa-hitech)** |
| **[HITRUST](/compliance/regulatory/offering-hitrust)** |

Ga naar het [vertrouwens centrum van micro soft](https://microsoft.com/trustcenter/default.aspx)als u geïnteresseerd bent in de naleving van Azure en hoe Azure een eigen infra structuur beveiligt. Voor de meest recente lijst met alle Azure compliance-aanbiedingen controleren  https://aka.ms/AzureCompliance .

In dit artikel worden beveiligings overwegingen in de volgende twee scenario's voor het verplaatsen van gegevens besproken:

- **Cloud scenario**: in dit scenario zijn zowel uw bron als uw bestemming openbaar toegankelijk via internet. Dit zijn onder andere beheerde services voor Cloud opslag, zoals Azure Storage, Azure Synapse Analytics, Azure SQL Database, Azure Data Lake Store, Amazon S3, Amazon Redshift, SaaS-services zoals Sales Force, en webprotocols zoals FTP en OData. Een volledige lijst met ondersteunde gegevens bronnen vindt u in  [ondersteunde gegevens archieven en-indelingen](copy-activity-overview.md#supported-data-stores-and-formats).
- **Hybride scenario**: in dit scenario bevindt uw bron of uw bestemming zich achter een firewall of binnen een on-premises bedrijfs netwerk. Of het gegevens archief bevindt zich in een particulier netwerk of virtueel netwerk (meestal de bron) en is niet openbaar toegankelijk. Database servers die worden gehost op virtuele machines vallen ook onder dit scenario.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Cloud scenario's

### <a name="securing-data-store-credentials"></a>Referenties voor gegevens opslag beveiligen

- **Versleutelde referenties opslaan in een door Azure Data Factory beheerde opslag**. Data Factory helpt u bij het beveiligen van uw referenties voor gegevens archieven door ze te versleutelen met certificaten die worden beheerd door micro soft. Deze certificaten worden elke twee jaar geroteerd (inclusief het vernieuwen van certificaten en de migratie van referenties). Zie [Azure Storage Security Overview](../storage/blobs/security-recommendations.md)(Engelstalig) voor meer informatie over Azure Storage beveiliging.
- **Referenties opslaan in azure Key Vault**. U kunt de referentie van het gegevens archief ook opslaan in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Data Factory haalt de referentie op tijdens het uitvoeren van een activiteit. Zie [referentie opslaan in azure Key Vault](store-credentials-in-key-vault.md)voor meer informatie.

### <a name="data-encryption-in-transit"></a>Gegevens versleuteling tijdens overdracht
Als de gegevens opslag in de Cloud HTTPS of TLS ondersteunt, worden alle gegevens overdrachten tussen services voor gegevens verplaatsing in Data Factory en een gegevens archief in de Cloud via Secure Channel HTTPS of TLS.

> [!NOTE]
> Alle verbindingen met Azure SQL Database en Azure Synapse Analytics vereisen versleuteling (SSL/TLS) wanneer gegevens onderweg naar en van de Data Base worden verzonden. Wanneer u een pijp lijn ontwerpt met behulp van JSON, voegt u de eigenschap Encryption toe en stelt u deze in op **True** in de Connection String. Voor Azure Storage kunt u **https** gebruiken in de Connection String.

> [!NOTE]
> Volg een van de onderstaande opties om versleuteling in transit in te scha kelen terwijl u gegevens van Oracle verplaatst:
>
> 1. In Oracle server gaat u naar Oracle Advanced Security (OAS) en configureert u de versleutelings instellingen, die ondersteuning biedt voor Triple-DES Encryption (3DES) en Advanced Encryption Standard (AES). Raadpleeg [hier](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759) voor meer informatie. ADF onderhandelt automatisch over de versleutelings methode voor het gebruik van het account dat u configureert in OAS bij het tot stand brengen van de verbinding met Oracle.
> 2. In ADF kunt u EncryptionMethod = 1 toevoegen in de connection string (in de gekoppelde service). Dit maakt gebruik van SSL/TLS als versleutelings methode. Als u dit wilt gebruiken, moet u niet-SSL-versleutelings instellingen uitschakelen in OAS op de Oracle-server zijde om te voor komen dat er versleuteling wordt veroorzaakt.

> [!NOTE]
> Gebruikte TLS-versie is 1,2.

### <a name="data-encryption-at-rest"></a>Versleuteling van inactieve gegevens

Sommige gegevens archieven ondersteunen de versleuteling van gegevens in rust. Het is raadzaam om het mechanisme voor gegevens versleuteling in te scha kelen voor deze gegevens archieven. 

#### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

Met Transparent Data Encryption (TDE) in azure Synapse Analytics kunt u zich beschermen tegen de dreiging van schadelijke activiteiten door real-time versleuteling en ontsleuteling van uw gegevens op rest uit te voeren. Dit gedrag is transparant voor de client. Zie [een Data Base beveiligen in azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md)voor meer informatie.

#### <a name="azure-sql-database"></a>Azure SQL Database

Azure SQL Database biedt ook ondersteuning voor transparent Data Encryption (TDE), dat bescherming biedt tegen de dreiging van schadelijke activiteiten door real-time versleuteling en ontsleuteling van de gegevens uit te voeren, zonder dat de toepassing hoeft te worden gewijzigd. Dit gedrag is transparant voor de client. Zie [transparent Data Encryption for SQL database en Data Warehouse](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)voor meer informatie.

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store

Azure Data Lake Store biedt ook versleuteling voor gegevens die zijn opgeslagen in het account. Als deze functie Data Lake Store is ingeschakeld, worden gegevens automatisch versleuteld voordat ze worden opgehaald en ontsleuteld voordat ze worden opgezocht, waardoor deze transparant worden voor de client die toegang heeft tot de gegevens. Zie [beveiliging in azure data Lake Store](../data-lake-store/data-lake-store-security-overview.md)voor meer informatie. 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob-opslag en Azure-tabel opslag

Azure Blob-opslag en Azure Table Storage ondersteunen Storage Service Encryption (SSE), waarmee uw gegevens automatisch worden versleuteld voordat ze worden opgeslagen en ontsleuteld voordat ze worden opgehaald. Zie [Azure Storage Service Encryption voor data-at-rest](../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3

Amazon S3 ondersteunt zowel client-als server versleuteling van gegevens in rust. Zie [gegevens beveiligen met versleuteling](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html)voor meer informatie.

#### <a name="amazon-redshift"></a>Amazon Redshift

Amazon Redshift ondersteunt cluster versleuteling voor Data-at-rest. Zie [Amazon Redshift data base Encryption](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html)(Engelstalig) voor meer informatie. 

#### <a name="salesforce"></a>SalesForce

Sales Force ondersteunt afschermings platform versleuteling waarmee alle bestanden, bijlagen en aangepaste velden kunnen worden versleuteld. Zie [Wat is de web server OAuth-verificatie stroom](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm)? voor meer informatie.  

## <a name="hybrid-scenarios"></a>Hybride scenario's

Hybride scenario's vereisen een zelf-hostende Integration runtime voor installatie in een on-premises netwerk, binnen een virtueel netwerk (Azure) of binnen een virtuele privécloud (Amazon). De zelf-hostende Integration runtime moet toegang hebben tot de lokale gegevens archieven. Zie [zelf-hostende Integration runtime maken en configureren](./create-self-hosted-integration-runtime.md)voor meer informatie over zelf-hostende Integration runtime. 

![zelf-hostende Integration runtime-kanalen](media/data-movement-security-considerations/data-management-gateway-channels.png)

Het opdracht kanaal staat communicatie toe tussen services voor gegevens verplaatsing in Data Factory en zelf-hostende Integration runtime. De communicatie bevat informatie met betrekking tot de activiteit. Het gegevens kanaal wordt gebruikt voor het overbrengen van gegevens tussen on-premises gegevens archieven en gegevens archieven in de Cloud.    

### <a name="on-premises-data-store-credentials"></a>Referenties voor het on-premises gegevens archief

De referenties kunnen worden opgeslagen in data factory of worden [verwezen door Data Factory](store-credentials-in-key-vault.md) tijdens de runtime van Azure Key Vault. Als referenties worden opgeslagen in data factory, wordt deze altijd versleuteld opgeslagen op de zelf-hostende Integration runtime. 

   - **Referenties lokaal opslaan**. Als u de cmdlet **set-AzDataFactoryV2LinkedService** rechtstreeks gebruikt met de verbindings reeksen en de inline referenties in de JSON, wordt de gekoppelde service versleuteld en opgeslagen in de zelf-hostende Integration runtime.  In dit geval worden de referenties via de Azure-back-end-service, die zeer veilig is, verzonden naar de zelf-hostende integratie computer waar deze uiteindelijk wordt versleuteld en opgeslagen. De zelf-hostende Integration runtime gebruikt Windows [DPAPI](/previous-versions/ms995355(v=msdn.10)) om de gevoelige gegevens en referentie gegevens te versleutelen.

   - **Referenties opslaan in azure Key Vault**. U kunt de referentie van het gegevens archief ook opslaan in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Data Factory haalt de referentie op tijdens het uitvoeren van een activiteit. Zie [referentie opslaan in azure Key Vault](store-credentials-in-key-vault.md)voor meer informatie.

   - **Sla lokaal referenties op zonder de referenties via de Azure-back-end over te brengen naar de zelf-hostende Integration runtime**. Als u lokaal referenties wilt versleutelen en opslaan op de zelf-hostende Integration runtime zonder de referenties via data factory back-end te hoeven door lopen, volgt u de stappen in [referenties versleutelen voor on-premises gegevens archieven in azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md). Alle connectors ondersteunen deze optie. De zelf-hostende Integration runtime gebruikt Windows [DPAPI](/previous-versions/ms995355(v=msdn.10)) om de gevoelige gegevens en referentie gegevens te versleutelen. 

   - Gebruik de cmdlet **New-AzDataFactoryV2LinkedServiceEncryptedCredential** om de gekoppelde service referenties en gevoelige gegevens in de gekoppelde service te versleutelen. U kunt vervolgens de JSON die wordt geretourneerd (met het element **EncryptedCredential** in de Connection String) gebruiken om een gekoppelde service te maken met behulp van de cmdlet **set-AzDataFactoryV2LinkedService** .  

#### <a name="ports-used-when-encrypting-linked-service-on-self-hosted-integration-runtime"></a>Poorten die worden gebruikt bij het versleutelen van de gekoppelde service op zelf-hostende Integration runtime

Power Shell maakt standaard gebruik van poort 8060 op de computer met zelf-hostende Integration runtime voor beveiligde communicatie. Deze poort kan, indien nodig, worden gewijzigd.  

![HTTPS-poort voor de gateway](media/data-movement-security-considerations/https-port-for-gateway.png)


### <a name="encryption-in-transit"></a>Versleuteling 'in transit'

Alle gegevens overdrachten zijn via Secure Channel HTTPS en TLS via TCP om te voor komen dat man-in-the-middle-aanvallen optreden tijdens de communicatie met Azure-Services.

U kunt ook [IPSec VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md) of [Azure ExpressRoute](../expressroute/expressroute-introduction.md) gebruiken om het communicatie kanaal te beveiligen tussen uw on-premises netwerk en Azure.

Azure Virtual Network is een logische weer gave van uw netwerk in de Cloud. U kunt een on-premises netwerk verbinden met uw virtuele netwerk door IPSec VPN (site-naar-site) of ExpressRoute (privé-peering) in te stellen.

De volgende tabel bevat een overzicht van de aanbevelingen voor het netwerk en zelf-hostende Integration runtime-configuratie op basis van verschillende combi Naties van bron-en doel locaties voor het verplaatsen van hybride gegevens.

| Bron      | Doel                              | Netwerkconfiguratie                    | Installatie van integratieruntime                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| On-premises | Virtuele machines en Cloud Services die zijn geïmplementeerd in virtuele netwerken | IPSec VPN (punt-naar-site of site-naar-site) | De zelf-hostende Integration runtime moet worden geïnstalleerd op een virtuele machine van Azure in het virtuele netwerk.  |
| On-premises | Virtuele machines en Cloud Services die zijn geïmplementeerd in virtuele netwerken | ExpressRoute (persoonlijke peering)           | De zelf-hostende Integration runtime moet worden geïnstalleerd op een virtuele machine van Azure in het virtuele netwerk.  |
| On-premises | Services op basis van Azure met een openbaar eind punt | ExpressRoute (micro soft-peering)            | De zelf-hostende Integration runtime kan on-premises of op een virtuele machine van Azure worden geïnstalleerd. |

De volgende installatie kopieën tonen het gebruik van zelf-hostende Integration runtime voor het verplaatsen van gegevens tussen een on-premises data base en Azure-Services met behulp van ExpressRoute en IPSec VPN (met Azure Virtual Network):

#### <a name="express-route"></a>ExpressRoute

![ExpressRoute gebruiken met gateway](media/data-movement-security-considerations/express-route-for-gateway.png) 

#### <a name="ipsec-vpn"></a>IPSec VPN

![IPSec VPN met gateway](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-allow-list-setting-up-for-ip-addresses"></a>Firewall configuraties en lijst met toegestane instellingen voor IP-adressen

> [!NOTE]
> Mogelijk moet u poorten beheren of lijst met toegestane apps instellen voor domeinen op het niveau van de bedrijfs firewall, zoals vereist is door de respectieve gegevens bronnen. In deze tabel worden alleen Azure SQL Database, Azure Synapse Analytics en Azure Data Lake Store als voor beeld gebruikt.

> [!NOTE]
> Zie [dit artikel](./data-access-strategies.md#data-access-strategies-through-azure-data-factory)voor meer informatie over strategieën voor gegevens toegang via Azure Data Factory.

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Firewall vereisten voor on-premises/particulier netwerk

In een onderneming wordt een bedrijfs firewall uitgevoerd op de centrale router van de organisatie. Windows Firewall wordt uitgevoerd als een daemon op de lokale computer waarop de zelf-hostende Integration runtime is geïnstalleerd. 

De volgende tabel bevat uitgaande poort-en domein vereisten voor zakelijke firewalls:

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]

> [!NOTE]
> Mogelijk moet u poorten beheren of lijst met toegestane apps instellen voor domeinen op het niveau van de bedrijfs firewall, zoals vereist is door de respectieve gegevens bronnen. In deze tabel worden alleen Azure SQL Database, Azure Synapse Analytics en Azure Data Lake Store als voor beeld gebruikt.   

De volgende tabel bevat de binnenkomende poort vereisten voor Windows Firewall:

| Poorten voor inkomend verkeer | Description                              |
| ------------- | ---------------------------------------- |
| 8060 (TCP)    | Vereist door de Power shell-versleutelings-cmdlet, zoals beschreven in [referenties versleutelen voor on-premises gegevens archieven in azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md), en door de toepassing voor referentie beheer om veilig referenties in te stellen voor on-premises gegevens archieven op de zelf-hostende Integration runtime. |

![Gateway poort vereisten](media/data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-and-allow-list-setting-up-in-data-stores"></a>IP-configuraties en lijst met toegestane instellingen in gegevens archieven

Voor sommige gegevens archieven in de Cloud moet u ook het IP-adres van de computer toestaan die toegang heeft tot de Store. Zorg ervoor dat het IP-adres van de zelf-hostende Integration runtime-machine op de juiste wijze is toegestaan of geconfigureerd in de firewall.

Voor de volgende gegevens archieven in de Cloud moet u het IP-adres van de zelf-hostende Integration runtime-computer toestaan. Sommige van deze gegevens archieven hebben standaard mogelijk geen Allow-lijst.

* [Azure SQL Database](../azure-sql/database/firewall-configure.md)
* [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md)
* [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
* [Azure Cosmos DB](../cosmos-db/how-to-configure-firewall.md)
* [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**Kan de zelf-hostende Integration runtime worden gedeeld tussen verschillende gegevens fabrieken?**

Ja. Meer informatie [vindt u hier](https://azure.microsoft.com/blog/sharing-a-self-hosted-integration-runtime-infrastructure-with-multiple-data-factories/).

**Wat zijn de poort vereisten voor de zelf-hostende Integration runtime om te werken?**

De zelf-hostende Integration runtime maakt op HTTP gebaseerde verbindingen voor toegang tot het internet. De uitgaande poorten 443 moeten worden geopend voor de zelf-hostende Integration runtime om deze verbinding te maken. Open de binnenkomende poort 8060 alleen op computer niveau (niet het niveau van de bedrijfs firewall) voor de toepassing referentie beheer. Als Azure SQL Database of Azure Synapse Analytics als de bron of de bestemming wordt gebruikt, moet u ook poort 1433 openen. Zie de sectie [firewall configuraties en het instellen van de lijst met toegestane IP-adressen](#firewall-configurations-and-allow-list-setting-up-for-ip-addresses) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het Azure Data Factory de prestaties van de Kopieer activiteit [Kopieer activiteit prestaties en afstemmings handleiding](copy-activity-performance.md).
