---
title: Serverloze SQL-pool
description: Meer informatie over serverloze SQL-pool in Azure Synapse Analytics.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: e1a0f4f22411e506cab3b54e955a4cdc16986fe2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667494"
---
# <a name="serverless-sql-pool-in-azure-synapse-analytics"></a>Serverloze SQL-pool in Azure Synapse Analytics 

Elke Azure Synapse Analytics-werkruimte wordt geleverd met serverloze SQL-pool-eindpunten die u kunt gebruiken om een query uit te voeren op gegevens in de lake.

Serverloze SQL-pool is een queryservice voor de gegevens in uw data lake. De service biedt op de volgende manieren toegang tot uw gegevens:
 
- Een vertrouwde T-SQL-syntaxis om gegevens op te vragen, zonder dat u gegevens naar een gespecialiseerd archief hoeft te kopiëren of te laden. 
- Geïntegreerde connectiviteit via de T-SQL-interface waarmee u beschikt over een breed scala aan business intelligence en ad-hoc hulpmiddelen voor query's, met inbegrip van de populairste stuurprogramma's. 

Serverloze SQL-pool is een systeem voor gedistribueerde gegevensverwerking, gebouwd voor gegevens en berekeningsfuncties op grote schaal. Met serverloze SQL-pool kunt u big data binnen enkele seconden tot minuten analyseren, afhankelijk van de werkbelasting. Dankzij de ingebouwde fouttolerantie voor query-uitvoering, biedt het systeem een hoge betrouwbaarheid en succespercentages, zelfs voor langlopende query's op grote gegevenssets.

Serverloze SQL-pool is serverloos, waardoor het niet nodig is om een infrastructuur in te stellen of clusters te onderhouden. Er wordt binnen elke Azure Synapse-werkruimte een standaardeindpunt voor deze service verstrekt, zodat u query's kunt gaan uitvoeren zodra de werkruimte is gemaakt. 

Er worden geen kosten in rekening gebracht voor gereserveerde resources, alleen voor de gegevens die worden verwerkt door query's die u uitvoert. Dit model is dus met recht een model voor betalen naar gebruik.  

Als u Apache Spark voor Azure Synapse gebruikt in uw gegevenspijplijn, voor het voorbereiden, opschonen of verrijken van gegevens, kunt u [query's uitvoeren op externe Spark-tabellen](develop-storage-files-spark-tables.md) die u in het proces hebt gemaakt, rechtstreeks vanuit de serverloze SQL-pool. Gebruik [Private Link](../security/how-to-connect-to-workspace-with-private-links.md) om uw eindpunt van de serverloze SQL-pool over te brengen naar het [VNet van uw beheerde werkruimte](../security/synapse-workspace-managed-vnet.md).  

## <a name="serverless-sql-pool-benefits"></a>Voordelen van een serverloze SQL-pool

Als u gegevens in de data lake wilt verkennen, inzicht in de lake wilt verkrijgen of uw bestaande pijplijn voor gegevenstransformatie wilt optimaliseren, is de serverloze SQL-pool een goede keuze. De service is geschikt voor de volgende scenario's:

- Basisvormen van detectie en verkenning: snelle beredenering van de gegevens in verschillende indelingen (Parquet, CSV, JSON) in uw data lake, zodat u kunt plannen hoe u er inzichten uit kunt halen.
- Logisch datawarehouse: een relationele abstractie verkrijgen van onbewerkte of ongelijksoortige gegevens zonder gegevens te verplaatsen en te transformeren, waardoor een altijd actuele weergave van uw gegevens mogelijk is.
- Gegevenstransformatie: eenvoudige, schaalbare en efficiënte manier om gegevens in het lake te transformeren met behulp van T-SQL, zodat deze als invoer kunnen worden gebruikt voor BI en andere hulpprogramma's of kunnen worden geladen in een relationeel gegevensarchief (Synapse SQL-databases, Azure SQL Database, enzovoort).

Er zijn verschillende functies rollen die voordeel kunnen hebben van een serverloze SQL-pool:

- Data engineers kunnen het lake verkennen, gegevens transformeren en voorbereiden met behulp van deze service en hun pijplijnen voor gegevenstransformatie vereenvoudigen. Ga voor meer informatie naar deze [zelfstudie](tutorial-data-analyst.md).
- Gegevenswetenschappers kunnen snel de inhoud en de structuur van de gegevens in het lake beredeneren, met behulp van functies zoals OPENROWSET en automatische schema-inferentie.
- Gegevensanalisten kunnen [gegevens en externe Spark-tabellen verkennen](develop-storage-files-spark-tables.md) die zijn gemaakt door gegevenswetenschappers of data engineers met behulp van de voor hen vertrouwde T-SQL-taal of met hun favoriete hulpprogramma’s, die kunnen worden verbonden met een serverloze SQL-pool.
- BI-professionals kunnen snel [Power BI-rapporten maken van gegevens in het lake](tutorial-connect-power-bi-desktop.md) en in Spark-tabellen.

## <a name="how-to-start-using-serverless-sql-pool"></a>Beginnen met het gebruik van een serverloze SQL-pool

Er wordt in elke Azure Synapse-werkruimte een eindpunt voor een serverloze SQL-pool ingericht. U hoeft alleen maar een werkruimte te maken om direct query's te kunnen uitvoeren met behulp van uw favoriete tools.

## <a name="client-tools"></a>Clienthulpprogramma's

Met een serverloze SQL-pool kunnen bestaande hulpprogramma's voor ad-hoc SQL-query's en business intelligence gebruikmaken van gegevens in de data lake. Omdat SQL on-demand de vertrouwde T-SQL-syntaxis ondersteunt, kan elk hulpprogramma dat via een TDS-verbinding SQL kan aanbieden, on-demand [verbinding maken met en query's uitvoeren op Synapse SQL](connect-overview.md). U kunt verbinding maken met Azure Data Studio en ad-hoc query's uitvoeren of verbinding maken met Power BI om binnen enkele minuten inzichten te verzamelen.

## <a name="t-sql-support"></a>Ondersteuning voor T-SQL

De serverloze SQL-pool biedt de T-SQL-surface area voor query's, waardoor bepaalde aspecten iets zijn verbeterd/uitgebreid om te voorzien in scenario's met het uitvoeren van query's op semi-gestructureerde en ongestructureerde gegevens. Daarnaast worden sommige aspecten van de T-SQL-taal niet ondersteund vanwege het ontwerp van een serverloze SQL-pool. Zo wordt DML-functionaliteit op dit moment bijvoorbeeld niet ondersteund.

- Werkbelastingen kunnen worden georganiseerd met behulp van bekende concepten:
- Databases - het eindpunt van een serverloze SQL-pool kan meerdere databases bevatten.
- Schema's: binnen een database kunnen een of meer eigendomsgroepen voor objecten bestaan, die schema's worden genoemd.
- Weergaven
- Externe resources: gegevensbronnen, bestandsindelingen en tabellen

Beveiliging kan worden afgedwongen met:

- Aanmeldingen en gebruikers
- Referenties om de toegang tot opslagaccounts te beheren
- Machtigingen op objectniveau toekennen, weigeren en intrekken
- Integratie van Azure Active Directory

Ondersteunde T-SQL:

- Volledig [SELECT](/sql/t-sql/queries/select-transact-sql?view=azure-sqldw-latest&preserve-view=true)-surface area wordt ondersteund, met inbegrip van het merendeel van de SQL-functies
- CETAS - CREATE EXTERNAL TABLE AS SELECT
- Alleen DDL-instructies met betrekking tot weergaven en beveiliging

Een serverloze SQL-pool heeft geen lokale opslag; alleen metagegevensobjecten worden opgeslagen in databases. Daarom wordt T-SQL niet ondersteund voor de volgende concepten:

- Tabellen
- Triggers
- Gerealiseerde weergaven
- DDL-instructies anders dan die voor weergaven en beveiliging
- DML-instructies

### <a name="extensions"></a>Extensies

Voor een goede ervaring bij het in-place uitvoeren van query's op gegevens die zich in de bestanden in de data lake bevinden, breidt de serverloze SQL-pool de bestaande [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?view=azure-sqldw-latest&preserve-view=true)-functie uit door de volgende mogelijkheden toe te voegen:

[Query uitvoeren op meerdere bestanden of mappen](query-data-storage.md#query-multiple-files-or-folders)

[Bestandsindeling PARQUET](query-data-storage.md#query-parquet-files)

[Aanvullende opties voor werken met gescheiden tekst (veldeindteken, rij-eindteken, escape-teken)](query-data-storage.md#query-csv-files)

[Een gekozen subset van kolommen lezen](query-data-storage.md#read-a-chosen-subset-of-columns)

[Schema-deductie](query-data-storage.md#schema-inference)

[Bestandsnaamfunctie](query-data-storage.md#filename-function)

[Bestandspadfunctie](query-data-storage.md#filepath-function)

[Werken met complexe typen en geneste of herhaalde gegevensstructuren](query-data-storage.md#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="security"></a>Beveiliging

De serverloze SQL-pool biedt mechanismen om de toegang tot uw gegevens te beveiligen.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integratie en meervoudige verificatie

Dankzij de serverloze SQL-pool kunt u de identiteit van databasegebruikers en andere Microsoft-services centraal beheren met [Azure Active Directory-integratie](../../azure-sql/database/authentication-aad-configure.md). Deze mogelijkheid vereenvoudigt het beheer van machtigingen en verbetert de beveiliging. Azure Active Directory (Azure AD) ondersteunt [Multi-Factor Authentication](../../azure-sql/database/authentication-mfa-ssms-configure.md) (MFA) voor betere beveiliging van gegevens en toepassingen, en ondersteunt ook een proces voor eenmalige aanmelding.

#### <a name="authentication"></a>Verificatie

Verificatie van de serverloze SQL-pool verwijst naar hoe gebruikers hun identiteit bewijzen wanneer ze verbinding maken met het eindpunt. Er worden twee typen verificatie ondersteund:

- **SQL-verificatie**

  Bij deze verificatiemethode wordt een combinatie van gebruikersnaam en wachtwoord gebruikt.

- **Azure Active Directory-verificatie**:

  Bij deze verificatiemethode worden identiteiten gebruikt die worden beheerd door Azure Active Directory. Voor Azure AD-gebruikers kan meervoudige verificatie worden ingeschakeld. Gebruik [waar mogelijk](/sql/relational-databases/security/choose-an-authentication-mode?view=azure-sqldw-latest&preserve-view=true) Active Directory-verificatie (geïntegreerde beveiliging).

#### <a name="authorization"></a>Autorisatie

Autorisatie verwijst naar wat een gebruiker kan doen binnen een serverloze SQL-pool-database. Dit wordt bepaald door de rollidmaatschappen en objectmachtigingen voor de database van uw gebruikersaccount.

Als SQL-verificatie wordt gebruikt, bestaat de SQL-gebruiker alleen in de serverloze SQL-pool en worden machtigingen uitgebreid naar de objecten in de serverloze SQL-pool. Toegang tot beveiligbare objecten in andere services (zoals Azure Storage) kan niet rechtstreeks worden verleend aan SQL-gebruikers omdat deze alleen bestaan binnen het bereik van de serverloze SQL-pool. De SQL-gebruiker moet een van de [ondersteunde autorisatietypen](develop-storage-files-storage-access-control.md#supported-storage-authorization-types) gebruiken voor toegang tot de bestanden.

Als Azure AD-verificatie wordt gebruikt, kan een gebruiker zich aanmelden bij de serverloze SQL-pool en andere services, zoals Azure Storage, en machtigingen verlenen aan de Azure AD-gebruiker.

### <a name="access-to-storage-accounts"></a>Toegang tot opslagaccounts

Een gebruiker die is aangemeld bij de service serverloze SQL-pool moet gemachtigd zijn om toegang te krijgen tot de bestanden in Azure Storage en er query’s op uit te voeren. Serverloze SQL-pool ondersteunt de volgende autorisatietypen:

- **SAS (Shared Access Signature; handtekening voor gedeelde toegang)** biedt gedelegeerde toegang tot resources in een opslagaccount. Met behulp van een SAS kunt u clients toegang geven tot resources in een opslagaccount zonder dat u de sleutels van het account hoeft te delen. Een SAS geeft u nauwkeurige controle over het type toegang dat u verleent aan clients die een SAS hebben: geldigheidsinterval, verleende machtigingen, acceptabel bereik van IP-adressen en acceptabel protocol (https/http).

- **Gebruikersidentiteit**, ook wel 'passthrough' genoemd, is een type autorisatie waarbij de identiteit van de Azure AD-gebruiker die is aangemeld bij de serverloze SQL-pool wordt gebruikt om toegang tot de gegevens te autoriseren. Voordat de gegevens worden vrijgegeven, moet de Azure Storage-beheerder machtigingen verlenen aan de Azure AD-gebruiker voor toegang tot de gegevens. Dit type autorisatie maakt gebruik van de Azure AD-gebruiker die is aangemeld bij de serverloze SQL-pool en wordt daarom niet ondersteund voor SQL-gebruikerstypen.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over de verbinding met eindpunten en het uitvoeren van query's op bestanden vindt u in de volgende artikelen: 
- [Verbinding maken met uw eindpunt](connect-overview.md)
- [Query's uitvoeren op uw bestanden](develop-storage-files-overview.md)
