---
title: Toegang krijgen tot bestanden in opslag in een serverloze SQL-pool
description: Beschrijft het doorzoeken van opslagbestanden met behulp van een serverloze SQL-pool in Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/19/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 679df881e928825496a03b2fef7c9374d61593f7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103199991"
---
# <a name="access-external-storage-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Toegang tot externe opslag met behulp van een serverloze SQL-pool in Azure Synapse Analytics

In dit artikel wordt beschreven hoe gebruikers gegevens kunnen lezen van de bestanden die zijn opgeslagen op Azure Storage in een serverloze SQL-groep. Gebruikers hebben de volgende opties om toegang te krijgen tot de opslag:

- De functie [OPENROWSET](develop-openrowset.md) die ad-hocquery's voor de bestanden in Azure Storage mogelijk maakt.
- [Externe tabel](develop-tables-external-tables.md) dat een vooraf gedefinieerde gegevensstructuur is die boven op de set externe bestanden is gebouwd.

Een gebruiker kan [verschillende verificatiemethoden gebruiken](develop-storage-files-storage-access-control.md) zoals Azure AD-passthrough-verificatie (standaard voor Azure AD-principals) en SAS-verificatie (standaard voor SQL-principals).

## <a name="query-files-using-openrowset"></a>Query's uitvoeren op bestanden met OPENROWSET

Met OPENROWSET kunnen gebruikers externe bestanden in Azure Storage opvragen als ze toegang hebben tot de opslag. Een gebruiker die is verbonden met de serverloze SQL-pool, moet de volgende query gebruiken om de inhoud van de bestanden in Azure Storage te kunnen lezen:

```sql
SELECT * FROM
 OPENROWSET(BULK 'https://<storage_account>.dfs.core.windows.net/<container>/<path>/*.parquet', format= 'parquet') as rows
```

Een gebruiker kan toegang krijgen tot de opslag met de volgende toegangsregels:

- Azure AD-gebruiker: gebruikt via `OPENROWSET` de Azure AD-identiteit van aanroeper om toegang te krijgen tot Azure Storage of om toegang te krijgen tot opslag met anonieme toegang.
- SQL-gebruiker: `OPENROWSET` gebruikt anonieme toegang voor toegang tot de opslaglocatie, of kan worden geïmiteerd met behulp van een SAS-token of een beheerde identiteit van een werkruimte.

### <a name="impersonation"></a>[Imitatie](#tab/impersonation)

SQL-principals kunnen ook OPENROWSET gebruiken om rechtstreeks bestanden op te vragen die zijn beveiligd met SAS-tokens of beheerde identiteit van de werkruimte. Als een SQL-gebruiker deze functie uitvoert, moet een hoofdgebruiker met de machtiging `ALTER ANY CREDENTIAL` referenties binnen serverbereik maken die overeenkomen met de URL in de functie (met behulp van de opslagnaam en container) en de machtiging REFERENCES heeft verleend voor de aanroeper van de OPENROWSET-functie:

```sql
EXECUTE AS somepoweruser

CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sas token';

GRANT REFERENCES ON CREDENTIAL::[https://<storage_account>.dfs.core.windows.net/<container>] TO sqluser
```

Als er geen CREDENTIAL op serverniveau is die overeenkomt met de URL, of als de SQL-gebruiker geen referentiemachtiging voor deze referentie heeft, wordt de fout geretourneerd. SQL-principals kunnen niet imiteren met een Azure AD-identiteit.

### <a name="direct-access"></a>[Directe toegang](#tab/direct-access)

Er hoeven geen extra instellingen te worden geconfigureerd om Azure AD-gebruikers hun identiteiten te laten gebruiken voor toegang tot de bestanden.
Elke gebruiker heeft toegang tot Azure-opslag waarvoor anonieme toegang is toegestaan (er is geen aanvullende installatie nodig).

---

> [!NOTE]
> Deze versie van OPENROWSET is ontworpen om snel en eenvoudig gegevens te verkennen met behulp van standaardauthenticatie. Als u imitatie of beheerde identiteit wilt gebruiken, gebruikt u OPENROWSET met DATASOURCE zoals in de volgende sectie wordt beschreven.

## <a name="query-data-sources-using-openrowset"></a>Query's uitvoeren op gegevensbronnen met OPENROWSET

Met OPENROWSET kunnen gebruikers een query uitvoeren op bestanden die in een externe gegevensbron zijn geplaatst:

```sql
SELECT * FROM
 OPENROWSET(BULK 'file/path/*.parquet',
 DATASOURCE = MyAzureInvoices,
 FORMAT= 'parquet') as rows
```

De gebruiker die deze query uitvoert, moet toegang tot de bestanden kunnen hebben. De gebruikers moeten worden geïmiteerd met een [SAS-token](develop-storage-files-storage-access-control.md?tabs=shared-access-signature) of een [beheerde identiteit van een werkruimte](develop-storage-files-storage-access-control.md?tabs=managed-identity) als ze niet rechtstreeks toegang hebben tot de bestanden met hun [Azure AD-identiteit](develop-storage-files-storage-access-control.md?tabs=user-identity) of [anonieme toegang](develop-storage-files-storage-access-control.md?tabs=public-access).

### <a name="impersonation"></a>[Imitatie](#tab/impersonation)

Met `DATABASE SCOPED CREDENTIAL` geeft u op hoe gebruikers toegang kunnen krijgen tot bestanden op de gegevensbron waarnaar wordt verwezen (momenteel SAS en beheerde identiteit). Een hoofdgebruiker met de machtiging `CONTROL DATABASE` moet `DATABASE SCOPED CREDENTIAL` maken, dat wordt gebruikt om toegang te krijgen tot opslag, en `EXTERNAL DATA SOURCE`, waarmee de URL van de gegevensbron en te gebruiken referenties worden opgegeven:

```sql
EXECUTE AS somepoweruser;

-- Create MASTER KEY if it doesn't exists in database
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'some very strong password';

CREATE DATABASE SCOPED CREDENTIAL AccessAzureInvoices
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&amp;sp=rwac&amp;se=2017-02-01T00:55:34Z&amp;st=201********' ;

CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>/' ,
 CREDENTIAL = AccessAzureInvoices) ;
```

Een aanroeper moet een van de volgende machtigingen hebben voor het uitvoeren van de OPENROWSET-functie:

- Een van de machtigingen voor het uitvoeren van OPENROWSET:
  - Met `ADMINISTER BULK OPERATIONS` kan een gebruiker via aanmelding de OPENROWSET-functie uitvoeren.
  - Met `ADMINISTER DATABASE BULK OPERATIONS` kan een gebruiker binnen een databasebereik de OPENROWSET-functie uitvoeren.
- `REFERENCES DATABASE SCOPED CREDENTIAL` voor de referentie waarnaar wordt verwezen in `EXTERNAL DATA SOURCE`.

### <a name="direct-access"></a>[Directe toegang](#tab/direct-access)

Een gebruiker kan EXTERNAL DATA SOURCE maken zonder CREDENTIAL die verwijst naar een openbaar toegankelijke opslag OF Azure AD-passthrough-verificatie gebruiken:

```sql
CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>') ;
```
---
## <a name="external-table"></a>EXTERNAL TABLE

Een gebruiker met de machtigingen voor het lezen van de tabel kan toegang krijgen tot externe bestanden met behulp van een EXTERNAL TABLE die wordt gemaakt boven op de set Azure Storage-mappen en -bestanden.

Een gebruiker met [-machtigingen voor het maken van een externe tabel](/sql/t-sql/statements/create-external-table-transact-sql?preserve-view=true&view=sql-server-ver15#permissions) (bijvoorbeeld CREATE TABLE en ALTER ANY CREDENTIAL of REFERENCES DATABASE SCOPED CREDENTIAL) kunnen het volgende script gebruiken om een tabel boven op Azure Storage-gegevensbron te maken:

```sql
CREATE EXTERNAL TABLE [dbo].[DimProductexternal]
( ProductKey int, ProductLabel nvarchar, ProductName nvarchar )
WITH
(
LOCATION='/DimProduct/year=*/month=*' ,
DATA_SOURCE = AzureDataLakeStore ,
FILE_FORMAT = TextFileFormat
) ;
```

De gebruiker die gegevens uit deze tabel leest, moet toegang tot de bestanden kunnen hebben. De gebruikers moeten worden geïmiteerd met een [SAS-token](develop-storage-files-storage-access-control.md?tabs=shared-access-signature) of een [beheerde identiteit van een werkruimte](develop-storage-files-storage-access-control.md?tabs=managed-identity) als ze niet rechtstreeks toegang hebben tot de bestanden met hun [Azure AD-identiteit](develop-storage-files-storage-access-control.md?tabs=user-identity) of [anonieme toegang](develop-storage-files-storage-access-control.md?tabs=public-access).

### <a name="impersonation"></a>[Imitatie](#tab/impersonation)

Met DATABASE SCOPED CREDENTIAL geeft u op hoe gebruikers toegang kunnen krijgen tot bestanden op de gegevensbron waarnaar wordt verwezen. Een gebruiker met de machtiging CONTROL DATABASE moet DATABASE SCOPED CREDENTIAL maken dat wordt gebruikt om toegang te krijgen tot opslag en EXTERNAL DATA SOURCE waarmee de URL van de gegevensbron en te gebruiken referenties worden opgegeven:

```sql
EXECUTE AS somepoweruser;

-- Create MASTER KEY if it doesn't exists in database
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'some very strong password';

CREATE DATABASE SCOPED CREDENTIAL cred
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&sp=rwac&se=2017-02-01T00:55:34Z&st=201********' ;

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>' ,
 CREDENTIAL = cred
 ) ;
```

### <a name="direct-access"></a>[Directe toegang](#tab/direct-access)

Een gebruiker kan EXTERNAL DATA SOURCE maken zonder CREDENTIAL die verwijst naar een openbaar toegankelijke opslag OF Azure AD-passthrough-verificatie gebruiken:

```sql
CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>') ;
```
---

### <a name="read-external-files-with-external-table"></a>Externe bestanden met EXTERNAL TABLE lezen

Met EXTERNAL TABLE kunt u gegevens lezen van de bestanden waarnaar wordt verwezen via de gegevensbron met behulp van de SQL SELECT-standaardinstructie:

```sql
SELECT *
FROM dbo.DimProductsExternal
```

Een aanroeper moet de volgende machtigingen hebben om gegevens te lezen:
- `SELECT`-machtiging IN de externe tabel
- `REFERENCES DATABASE SCOPED CREDENTIAL`-machtiging als `DATA SOURCE` over `CREDENTIAL` beschikt

## <a name="permissions"></a>Machtigingen

De volgende tabel geeft een lijst van de vereiste machtigingen voor de hierboven genoemde bewerkingen.

| Query’s uitvoeren | Vereiste machtigingen|
| --- | --- |
| OPENROWSET(BULK) zonder gegevensbron | `ADMINISTER BULK OPERATIONS`, `ADMINISTER DATABASE BULK OPERATIONS`of SQL-aanmelding moet over REFERENCES CREDENTIAL::\<URL> beschikken voor met SAS beveiligde opslag |
| OPENROWSET(BULK) met gegevensbron zonder referenties | `ADMINISTER BULK OPERATIONS` of `ADMINISTER DATABASE BULK OPERATIONS`, |
| OPENROWSET(BULK) met gegevensbron en referenties | `REFERENCES DATABASE SCOPED CREDENTIAL` en één van `ADMINISTER BULK OPERATIONS` of `ADMINISTER DATABASE BULK OPERATIONS` |
| CREATE EXTERNAL DATA SOURCE | `ALTER ANY EXTERNAL DATA SOURCE` en `REFERENCES DATABASE SCOPED CREDENTIAL` |
| CREATE EXTERNAL TABLE | `CREATE TABLE`, `ALTER ANY SCHEMA`, `ALTER ANY EXTERNAL FILE FORMAT` en `ALTER ANY EXTERNAL DATA SOURCE` |
| SELECT FROM EXTERNAL TABLE | `SELECT TABLE` en `REFERENCES DATABASE SCOPED CREDENTIAL` |
| CETAS | Voor het maken van een tabel - `CREATE TABLE`, `ALTER ANY SCHEMA`, `ALTER ANY DATA SOURCE` en `ALTER ANY EXTERNAL FILE FORMAT`. Gegevens lezen: `ADMINISTER BULK OPERATIONS` of `REFERENCES CREDENTIAL` of `SELECT TABLE` per tabel/weergave/functie in query + lezen/schrijven-machtiging voor opslag |

## <a name="next-steps"></a>Volgende stappen

U kunt nu doorgaan met de volgende Help-artikelen:

- [Query uitvoeren op gegevens in opslag](query-data-storage.md)

- [Query uitvoeren op CSV-bestand](query-single-csv-file.md)

- [Query's uitvoeren op Parquet-bestanden](query-parquet-files.md)

- [Query's uitvoeren op JSON-bestanden](query-json-files.md)

- [Query uitvoeren op mappen en meerdere bestanden](query-folders-multiple-csv-files.md)

- [Partitionerings- en metagegevensfuncties gebruiken](query-specific-files.md)

- [Query uitvoeren op geneste typen](query-parquet-nested-types.md)

- [Weergaven maken en gebruiken](create-use-views.md)
