---
title: Vereisten voor Blob-opslag voor Microsoft Azure Stack Edge | Microsoft Docs
description: Meer informatie over de ondersteunde versies voor Api's, Sdk's en client bibliotheken voor Azure Stack Edge-Blob-opslag
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: alkohli
ms.openlocfilehash: 887aaf32592c74a5884916bc7ad63d79544fb554
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575852"
---
# <a name="azure-stack-edge-blob-storage-requirements"></a>Vereisten voor Blob-opslag voor Azure Stack Edge

In dit artikel vindt u een overzicht van de versies van de Azure-Api's, Azure-client bibliotheken en hulpprogram ma's die worden ondersteund met de Blob-opslag Azure Stack Edge. Azure Stack Edge-Blob-opslag biedt functionaliteit voor het beheer van blobs met Azure-consistente semantiek. In dit artikel wordt ook een overzicht gegeven van de bekende verschillen in Blob Storage van Azure Stack Edge van de Azure Storage services.

We raden u aan de informatie zorgvuldig te bekijken voordat u verbinding maakt met de Blob-opslag van de Azure Stack Edge en vervolgens naar wens terug te gaan.

## <a name="storage-differences"></a>Verschillen in opslag

|     Functie                                             |     Azure Storage                                     |     Blob-opslag Azure Stack Edge |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure File Storage                                   |    Door de cloud gebaseerde SMB-bestands shares worden ondersteund              |    Niet ondersteund      |
|    Type opslagaccount                                 |    Algemene en Azure Blob Storage-accounts    |    Alleen voor algemeen gebruik v1|
|    Blobnaam                                            |    1.024 tekens (2.048 bytes)                     |    880 tekens (1.760 bytes)|
|    Maximale grootte van blok-BLOB                              |    4,75 TB (100 MB X 50.000 blokken)                   |    4,75 TB (100 MB x 50.000 blokken) voor Azure Stack Edge|
|    Maximum grootte voor pagina-blobs                               |    8 TB                                               |    1 TB                   |
|    Pagina formaat pagina-BLOB                                  |    512 bytes                                          |    4 kB                   |

## <a name="supported-api-versions"></a>Ondersteunde API-versies

De volgende versies van Azure Storage service-Api's worden ondersteund met Azure Stack Edge-Blobopslag.

### <a name="azure-stack-edge-2113772170-onwards"></a>Azure Stack Edge-2.1.1377.2170

[!INCLUDE [data-box-rest-supported-api-versions](../../includes/data-box-rest-supported-api-versions.md)]

## <a name="supported-azure-client-libraries"></a>Ondersteunde Azure-client bibliotheken

Voor Azure Stack Edge-Blobopslag zijn er specifieke client bibliotheken en specifieke vereisten voor eindpunt achtervoegsels. De Blob Storage-eind punten van de Azure Stack Edge hebben geen volledige pariteit met de nieuwste versie van de Azure-Blob Storage REST API. Zie de [Ondersteunde API-versies voor Azure stack Edge](#supported-api-versions). U moet rekening houden met de versie die compatibel is met de REST API voor de opslag-client bibliotheken.

### <a name="azure-stack-edge-2113772170-onwards"></a>Azure Stack Edge-2.1.1377.2170

De volgende versies van de Azure-client bibliotheek worden ondersteund voor Azure Stack Edge-Blob-opslag.

[!INCLUDE [data-box-rest-supported-azure-client-libraries](../../includes/data-box-rest-supported-azure-client-libraries.md)]

### <a name="install-the-php-client-via-composer---current"></a>De PHP-client installeren via Composer-actueel

De PHP-client installeren via Composer:

1. Maak een bestand met de naam composer.jsin de hoofdmap van het project met de volgende code (voor beeld gebruikt Azure Storage Blob-service).

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. Down load `composer.phar` de hoofdmap van het project.

3. Uitvoeren: PHP Composer. Phar-installatie.


## <a name="endpoint-declaration"></a>Eindpunt declaratie

In de Azure Stack Edge Blob Storage SDK, het eind punt achtervoegsel- `<device serial number>.microsoftdatabox.com` -identificeert het domein Azure stack Edge. Ga voor meer informatie over het eind punt van de BLOB-service naar [gegevens overdragen via opslag accounts met Azure stack Edge Pro GPU](azure-stack-edge-j-series-deploy-add-storage-accounts.md).


## <a name="examples"></a>Voorbeelden

### <a name="net"></a>.NET

Voor Azure Stack Edge-Blob-opslag is het eind punt achtervoegsel opgegeven in het `app.config` bestand:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

Voor Azure Stack Edge-Blob-opslag is het eind punt achtervoegsel opgegeven in de installatie van connection string:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

Voor de Blob-opslag voor Azure Stack Edge is het eind punt achtervoegsel opgegeven in het declaratie-exemplaar:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

Voor Azure Stack Edge-Blob-opslag is het eind punt achtervoegsel opgegeven in de installatie van de connection string:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

Voor Azure Stack Edge-Blob-opslag is het eind punt achtervoegsel opgegeven in de installatie van de connection string:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Voor de Blob-opslag voor Azure Stack Edge is het eind punt achtervoegsel opgegeven in het declaratie-exemplaar:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

Voor Azure Stack Edge-Blob-opslag is het eind punt achtervoegsel opgegeven in de installatie van de connection string:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Volgende stappen

* [De implementatie van Azure Stack Edge Pro met GPU voorbereiden](azure-stack-edge-gpu-deploy-prep.md)
