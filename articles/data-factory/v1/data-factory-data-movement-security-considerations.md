---
title: Beveiligings overwegingen voor het verplaatsen van gegevens in Azure Data Factory
description: Meer informatie over het beveiligen van gegevens verplaatsing in Azure Data Factory.
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 33b1ad381b3f7865768f9e39295a2985f8aa5234
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100375099"
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Azure Data Factory-beveiligings overwegingen voor gegevens verplaatsing

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [beveiligings overwegingen voor gegevens verplaatsing voor Data Factory](../data-movement-security-considerations.md).

## <a name="introduction"></a>Introductie
In dit artikel wordt een basis beveiligings infrastructuur beschreven die services voor gegevens verplaatsing in Azure Data Factory gebruiken om uw gegevens te beveiligen. Azure Data Factory beheer bronnen zijn gebaseerd op de Azure-beveiligings infrastructuur en gebruiken alle mogelijke beveiligings maatregelen die door Azure worden geboden.

In een Data Factory-oplossing maakt u een of meer gegevens[pijplijnen](data-factory-create-pipelines.md). Een pijplijn is een logische groep activiteiten die samen een taak uitvoeren. Deze pijp lijnen bevinden zich in de regio waar de data factory is gemaakt. 

Hoewel Data Factory alleen beschikbaar is in de regio's **VS-West**, VS- **Oost** en **Europa-Noord** , is de service voor gegevens verplaatsing [wereld wijd beschikbaar in meerdere regio's](data-factory-data-movement-activities.md#global). Data Factory-service zorgt ervoor dat de gegevens geen geografisch gebied/regio verlaten, tenzij u de service expliciet een alternatieve regio laat gebruiken als de service voor gegevens verplaatsing nog niet is geïmplementeerd in die regio. 

Azure Data Factory zichzelf geen gegevens opslaat, met uitzonde ring van de gekoppelde service referenties voor gegevens archieven in de Cloud, die zijn versleuteld met behulp van certificaten. Hiermee kunt u gegevensgestuurde werk stromen maken om de verplaatsing van gegevens te organiseren tussen [ondersteunde gegevens archieven](data-factory-data-movement-activities.md#supported-data-stores-and-formats) en gegevens verwerking met behulp van [Compute Services](data-factory-compute-linked-services.md) in andere regio's of in een on-premises omgeving. U kunt hiermee ook [werk stromen bewaken en beheren](data-factory-monitor-manage-pipelines.md) met zowel programmatische als gebruikersinterface mechanismen.

Gegevens verplaatsing met Azure Data Factory is **gecertificeerd** voor:
-    [HIPAA/HITECH](/compliance/regulatory/offering-hipaa-hitech)  
-    [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-    [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018) 
-    [CSA STER](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)
     
Ga naar het [vertrouwens centrum van micro soft](https://microsoft.com/en-us/trustcenter/default.aspx)als u geïnteresseerd bent in de naleving van Azure en hoe Azure een eigen infra structuur beveiligt. 

In dit artikel worden beveiligings overwegingen in de volgende twee scenario's voor het verplaatsen van gegevens besproken: 

- **Cloud scenario**: in dit scenario zijn uw bron en doel openbaar toegankelijk via internet. Dit zijn onder andere beheerde services voor Cloud opslag, zoals Azure Storage, Azure Synapse Analytics, Azure SQL Database, Azure Data Lake Store, Amazon S3, Amazon Redshift, SaaS-services zoals Sales Force, en webprotocols zoals FTP en OData. [Hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats)vindt u een volledige lijst met ondersteunde gegevens bronnen.
- **Hybride scenario**: in dit scenario bevindt uw bron of doel zich achter een firewall of in een on-premises bedrijfs netwerk, of het gegevens archief bevindt zich in een particulier netwerk/virtueel netwerk (meestal de bron) en is niet openbaar toegankelijk. Database servers die worden gehost op virtuele machines vallen ook onder dit scenario.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Cloud scenario's
### <a name="securing-data-store-credentials"></a>Referenties voor gegevens opslag beveiligen
Azure Data Factory beschermt uw referenties voor uw gegevens Archief door ze te **versleutelen** met behulp van **certificaten die door micro soft worden beheerd**. Deze certificaten worden elke **twee jaar** geroteerd (inclusief verlenging van het certificaat en de migratie van referenties). Deze versleutelde referenties worden veilig opgeslagen in een **Azure Storage beheerd door Azure Data Factory beheer Services**. Raadpleeg [Azure Storage Security Overview](../../storage/blobs/security-recommendations.md)(Engelstalig) voor meer informatie over Azure Storage beveiliging.

### <a name="data-encryption-in-transit"></a>Gegevens versleuteling tijdens overdracht
Als de gegevens opslag in de Cloud HTTPS of TLS ondersteunt, worden alle gegevens overdrachten tussen services voor gegevens verplaatsing in Data Factory en een gegevens archief in de Cloud via Secure Channel HTTPS of TLS.

> [!NOTE]
> Alle verbindingen met **Azure SQL database** en **Azure Synapse Analytics** vereisen altijd VERSLEUTELING (SSL/TLS) wanneer gegevens onderweg naar en van de Data Base worden verzonden. Bij het ontwerpen van een pijp lijn met behulp van een JSON-editor, voegt u de **versleutelings** eigenschap toe en stelt u deze in op **true** in de **Connection String**. Wanneer u de [wizard kopiëren](data-factory-azure-copy-wizard.md)gebruikt, wordt deze eigenschap standaard ingesteld door de wizard. Voor **Azure Storage** kunt u **https** gebruiken in de Connection String.

### <a name="data-encryption-at-rest"></a>Versleuteling van inactieve gegevens
Sommige gegevens archieven ondersteunen de versleuteling van gegevens in rust. U wordt aangeraden om het mechanisme voor gegevens versleuteling in te scha kelen voor deze gegevens archieven. 

#### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics
Transparent Data Encryption (TDE) in azure Synapse Analytics helpt bij het beschermen tegen de dreiging van schadelijke activiteiten door realtime-versleuteling en ontsleuteling van uw gegevens in rust uit te voeren. Dit gedrag is transparant voor de client. Zie [een Data Base beveiligen in azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md)voor meer informatie.

#### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database biedt ook ondersteuning voor transparent Data Encryption (TDE), dat helpt bij het beveiligen van de dreiging van schadelijke activiteiten door realtime-versleuteling en ontsleuteling van de gegevens uit te voeren zonder dat de toepassing hoeft te worden gewijzigd. Dit gedrag is transparant voor de client. Zie [transparent Data Encryption met Azure SQL database](/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database)voor meer informatie. 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake Store biedt ook versleuteling voor gegevens die zijn opgeslagen in het account. Als deze functie Data Lake is ingeschakeld, worden gegevens automatisch versleuteld voordat ze worden opgehaald en ontsleuteld voordat ze worden opgeheven, zodat deze transparant worden voor de client die toegang heeft tot de gegevens. Zie [beveiliging in azure data Lake Store](../../data-lake-store/data-lake-store-security-overview.md)voor meer informatie. 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob Storage en Azure Table Storage
Azure Blob Storage en Azure-tabel opslag ondersteunt Storage Service Encryption (SSE), waarmee uw gegevens automatisch worden versleuteld voordat ze worden opgeslagen en ontsleuteld voordat ze worden opgehaald. Zie [Azure Storage Service Encryption voor data-at-rest](../../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 ondersteunt zowel client-als server versleuteling van gegevens in rust. Zie [gegevens beveiligen met versleuteling](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html)voor meer informatie. Op dit moment biedt Data Factory geen ondersteuning voor Amazon S3 binnen een virtuele privécloud (VPC).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift ondersteunt cluster versleuteling voor Data-at-rest. Zie [Amazon Redshift data base Encryption](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html)(Engelstalig) voor meer informatie. Data Factory biedt momenteel geen ondersteuning voor Amazon Redshift binnen een VPC. 

#### <a name="salesforce"></a>SalesForce
Sales Force ondersteunt afschermings platform versleuteling waarmee alle bestanden, bijlagen en aangepaste velden kunnen worden versleuteld. Zie [Wat is de web server OAuth-verificatie stroom](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm)? voor meer informatie.  

## <a name="hybrid-scenarios-using-data-management-gateway"></a>Hybride Scenario's (met behulp van Data Management Gateway)
Voor hybride scenario's moet Data Management Gateway worden geïnstalleerd in een on-premises netwerk of in een virtueel netwerk (Azure) of in een virtuele privécloud (Amazon). De gateway moet toegang hebben tot de lokale gegevens opslag. Zie [Data Management Gateway](data-factory-data-management-gateway.md)voor meer informatie over de gateway. 

![Data Management Gateway kanalen](media/data-factory-data-movement-security-considerations/data-management-gateway-channels.png)

Het **opdracht kanaal** staat communicatie toe tussen services voor gegevens verplaatsing in Data Factory en Data Management Gateway. De communicatie bevat informatie met betrekking tot de activiteit. Het gegevens kanaal wordt gebruikt voor het overbrengen van gegevens tussen on-premises gegevens archieven en gegevens archieven in de Cloud.    

### <a name="on-premises-data-store-credentials"></a>Referenties voor het on-premises gegevens archief
De referenties voor uw on-premises gegevens archieven worden lokaal opgeslagen (niet in de Cloud). Ze kunnen op drie verschillende manieren worden ingesteld. 

- Gebruik **onbewerkte tekst** (minder veilig) via HTTPS via de wizard Azure Portal/kopiëren. De referenties worden door gegeven als tekst zonder opmaak naar de on-premises gateway.
- **Java script Cryptography-bibliotheek van de wizard kopiëren** gebruiken.
- Met behulp **van een klik-eenmaal gebaseerde credentials Manager-app**. De Click-toepassing wordt uitgevoerd op de on-premises computer die toegang heeft tot de gateway en waarmee referenties voor het gegevens archief worden ingesteld. Deze optie en de volgende zijn de veiligste opties. De referentie beheer-app gebruikt standaard de poort 8050 op de computer met de gateway voor beveiligde communicatie.  
- Gebruik de Power shell-cmdlet [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/New-azDataFactoryEncryptValue) om referenties te versleutelen. De cmdlet maakt gebruik van het certificaat dat door de gateway is geconfigureerd voor het versleutelen van de referenties. U kunt de versleutelde referenties die door deze cmdlet worden geretourneerd, gebruiken en deze toevoegen aan het **EncryptedCredential** -element van de **Connections Tring** in het JSON-bestand dat u gebruikt met de cmdlet [New-AzDataFactoryLinkedService](/powershell/module/az.datafactory/new-azdatafactorylinkedservice) of in het json-code fragment in de Data Factory editor van de portal. Deze optie en de toepassing waarop u kunt klikken, zijn de veiligste opties. 

#### <a name="javascript-cryptography-library-based-encryption"></a>Versleuteling op basis van Java script Cryptography-bibliotheek
U kunt referenties voor gegevens opslag versleutelen met behulp van de [Java script Cryptography-bibliotheek](https://www.microsoft.com/download/details.aspx?id=52439) via de [wizard kopiëren](data-factory-copy-wizard.md). Wanneer u deze optie selecteert, haalt de wizard kopiëren de open bare sleutel van de gateway op en gebruikt deze om de referenties van het gegevens archief te versleutelen. De referenties worden ontsleuteld door de gateway computer en beveiligd door Windows [DPAPI](/previous-versions/ms995355(v=msdn.10)).

**Ondersteunde browsers:** IE8, IE9, IE10, IE11, micro soft Edge en de nieuwste Firefox, Chrome, Opera, Safari-browsers. 

#### <a name="click-once-credentials-manager-app"></a>Eenmaal klikken referenties beheer-app
U kunt de wizard voor op één keer gebaseerde referentie beheer-app starten vanuit Azure Portal/kopiëren tijdens het ontwerpen van pijp lijnen. Deze toepassing zorgt ervoor dat de referenties niet in tekst zonder opmaak worden overgedragen via de kabel. Standaard gebruikt het poort **8050** op de computer met de gateway voor beveiligde communicatie. Deze poort kan, indien nodig, worden gewijzigd.  
  
![HTTPS-poort voor de gateway](media/data-factory-data-movement-security-considerations/https-port-for-gateway.png)

Data Management Gateway gebruikt momenteel één **certificaat**. Dit certificaat wordt gemaakt tijdens de installatie van de gateway (dit is van toepassing op Data Management Gateway gemaakt na november 2016 en versie 2.4. xxxx. x of hoger). U kunt dit certificaat vervangen door uw eigen SSL/TLS-certificaat. Dit certificaat wordt gebruikt door de toepassing voor referentie beheer voor klikken om veilig verbinding te maken met de gateway computer voor het instellen van referenties voor gegevens archieven. De referenties van het gegevens archief worden veilig on-premises opgeslagen door gebruik te maken van de Windows [DPAPI](/previous-versions/ms995355(v=msdn.10)) op de computer met de gateway. 

> [!NOTE]
> Oudere gateways die zijn geïnstalleerd vóór november 2016 of versie 2.3. xxxx. x blijven referenties gebruiken die zijn versleuteld en opgeslagen in de Cloud. Zelfs als u de gateway naar de nieuwste versie bijwerkt, worden de referenties niet gemigreerd naar een on-premises computer    
  
| Gateway versie (tijdens het maken) | Opgeslagen referenties | Referentie versleuteling/beveiliging | 
| --------------------------------- | ------------------ | --------- |  
| < = 2.3. xxxx. x | In de Cloud | Versleuteld met behulp van certificaten (anders dan het certificaat dat wordt gebruikt door de referentie beheer-app) | 
| > = 2.4. xxxx. x | On-premises | Beveiligd via DPAPI | 
  

### <a name="encryption-in-transit"></a>Versleuteling 'in transit'
Alle gegevens overdrachten zijn via Secure Channel **https** en **TLS via TCP** om te voor komen dat man-in-the-middle-aanvallen optreden tijdens de communicatie met Azure-Services.
 
U kunt ook [IPSec VPN](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) of [Express route](../../expressroute/expressroute-introduction.md) gebruiken om het communicatie kanaal te beveiligen tussen uw on-premises netwerk en Azure.

Virtueel netwerk is een logische weer gave van uw netwerk in de Cloud. U kunt een on-premises netwerk verbinden met uw virtuele Azure-netwerk (VNet) door IPSec VPN in te stellen (site-naar-site) of Express route (persoonlijke peering)        

De volgende tabel bevat een overzicht van de aanbevelingen voor de netwerk-en gateway configuratie op basis van verschillende combi Naties van bron-en doel locaties voor het verplaatsen van hybride gegevens.

| Bron | Doel | Netwerkconfiguratie | Gateway instellen |
| ------ | ----------- | --------------------- | ------------- | 
| On-premises | Virtuele machines en Cloud Services die zijn geïmplementeerd in virtuele netwerken | IPSec VPN (punt-naar-site of site-naar-site) | De gateway kan on-premises of op een virtuele Azure-machine (VM) in VNet worden geïnstalleerd | 
| On-premises | Virtuele machines en Cloud Services die zijn geïmplementeerd in virtuele netwerken | ExpressRoute (persoonlijke peering) | De gateway kan on-premises of op een virtuele Azure-machine worden geïnstalleerd in VNet | 
| On-premises | Services op basis van Azure met een openbaar eind punt | ExpressRoute (open bare peering) | De gateway moet on-premises zijn geïnstalleerd | 

De volgende installatie kopieën tonen het gebruik van Data Management Gateway voor het verplaatsen van gegevens tussen een on-premises data base en Azure-Services met behulp van Express route en IPSec VPN (met Virtual Network):

**Express route:**
 
![Express route met gateway gebruiken](media/data-factory-data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN:**

![IPSec VPN met gateway](media/data-factory-data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-filtering-ip-address-of-gateway"></a>Firewall configuraties en filteren van het IP-adres van de gateway

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Firewallvereisten voor on-premises/particulier netwerk    
In een onderneming wordt een **bedrijfs firewall** uitgevoerd op de centrale router van de organisatie. En **Windows Firewall** wordt uitgevoerd als een daemon op de lokale computer waarop de gateway is geïnstalleerd. 

De volgende tabel bevat de **uitgaande poort** -en domein vereisten voor de **bedrijfs firewall**.

| Domeinnamen | Uitgaande poorten | Beschrijving |
| ------------ | -------------- | ----------- | 
| `*.servicebus.windows.net` | 443, 80 | Vereist door de gateway om verbinding te maken met Services voor gegevens verplaatsing in Data Factory |
| `*.core.windows.net` | 443 | Wordt gebruikt door de gateway om verbinding te maken met Azure Storage account wanneer u de functie voor [gefaseerd kopiëren](data-factory-copy-activity-performance.md#staged-copy) gebruikt. | 
| `*.frontend.clouddatahub.net` | 443 | Vereist door de gateway om verbinding te maken met de Azure Data Factory-service. | 
| `*.database.windows.net` | 1433    | (Optioneel) vereist wanneer uw bestemming Azure SQL Database/Azure Synapse Analytics is. Gebruik de functie voor gefaseerd kopiëren om gegevens te kopiëren naar Azure SQL Database/Azure Synapse Analytics zonder de poort 1433 te openen. | 
| `*.azuredatalakestore.net` | 443 | (Optioneel) vereist wanneer uw bestemming Azure Data Lake archief is | 

> [!NOTE] 
> Mogelijk moet u poorten/filter domeinen op het niveau van de bedrijfs firewall beheren zoals vereist door de respectieve gegevens bronnen. In deze tabel worden alleen Azure SQL Database, Azure Synapse Analytics, Azure Data Lake Store als voor beeld gebruikt.   

De volgende tabel bevat de **Binnenkomende poort** vereisten voor de **Windows Firewall**.

| Poorten voor inkomend verkeer | Description | 
| ------------- | ----------- | 
| 8050 (TCP) | Vereist door de Credential Manager-toepassing om veilig referenties in te stellen voor on-premises gegevens archieven op de gateway. | 

![Gateway poort vereisten](media/data-factory-data-movement-security-considerations/gateway-port-requirements.png)

#### <a name="ip-configurationsfiltering-in-data-store"></a>IP-configuraties/filteren in gegevens archief
Sommige gegevens archieven in de Cloud moeten ook het goed keuren van het IP-adres van de computer die toegang tot deze bestanden heeft. Zorg ervoor dat het IP-adres van de gateway computer op de juiste wijze wordt goedgekeurd/geconfigureerd in de firewall.

Voor de volgende gegevens archieven in de Cloud moet het IP-adres van de gateway computer worden goedgekeurd. Voor sommige van deze gegevens archieven is het niet mogelijk om het IP-adres goed te keuren. 

- [Azure SQL Database](../../azure-sql/database/firewall-configure.md) 
- [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md)
- [Azure Data Lake Store](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../../cosmos-db/how-to-configure-firewall.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**Vraag:** Kan de gateway worden gedeeld tussen verschillende gegevens fabrieken?
**Antwoord:** Deze functie wordt nog niet ondersteund. We zijn hier druk mee bezig.

**Vraag:** Wat zijn de poort vereisten voor het werken met de gateway?
**Antwoord:** Gateway maakt HTTP-verbindingen voor het openen van Internet. De **uitgaande poorten 443 en 80** moeten voor de gateway worden geopend om deze verbinding te kunnen maken. Open de **Binnenkomende poort 8050** alleen op computer niveau (niet op het niveau van de bedrijfs firewall) voor de toepassing referentie beheer. Als Azure SQL Database of Azure Synapse Analytics als bron/doel wordt gebruikt, moet u ook **1433** -poort openen. Zie de sectie [firewall configuraties en IP-adressen filteren](#firewall-configurations-and-filtering-ip-address-of gateway) voor meer informatie. 

**Vraag:** Wat zijn de certificaat vereisten voor de gateway?
**Antwoord:** Voor de huidige gateway is een certificaat vereist dat door de Credential Manager-toepassing wordt gebruikt voor het veilig instellen van referenties voor het gegevens archief. Dit certificaat is een zelfondertekend certificaat dat is gemaakt en geconfigureerd door de gateway-installatie. U kunt in plaats daarvan uw eigen TLS/SSL-certificaat gebruiken. Zie voor meer informatie klikt u op de sectie voor [het toepassings beheer van referenties](#click-once-credentials-manager-app) . 

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over de prestaties van de Kopieer activiteit [Kopieer activiteit prestaties en afstemmings handleiding](data-factory-copy-activity-performance.md).