---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 37917e0ed663675677f1d0452b5796120ca2694e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001720"
---
### <a name="azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service
Met de **Azure Storage-gekoppelde service** kunt u een Azure Storage-account aan een Azure-data factory koppelen met behulp van de **accountsleutel**, waarmee u de data factory kunt openen met wereldwijde toegang tot de Azure Storage. In de volgende tabel vindt u een beschrijving van de JSON-elementen die specifiek zijn voor Azure Storage-gekoppelde service.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type |Het type eigenschap moet worden ingesteld op: **AzureStorage** |Ja |
| connectionString |Geef de gegevens op die nodig zijn om verbinding te maken met Azure Storage voor de connectionString-eigenschap. |Ja |

Zie voor informatie over het ophalen van de toegangssleutels voor het opslagaccount [Toegangssleutels voor opslagaccounts beheren](../articles/storage/common/storage-account-keys-manage.md).

**Voorbeeld:**  

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="azure-storage-sas-linked-service"></a>Een gekoppelde Azure Storage SAS-service
Een SAS (Shared Access Signature; handtekening voor gedeelde toegang) biedt gedelegeerde toegang tot bronnen in uw opslagaccount. U kunt een client gedurende de opgegeven periode een beperkte set machtigingen verlenen voor objecten in uw opslagaccount, zonder dat u hiervoor de toegangssleutels voor het account hoeft te delen. De SAS is een URI die in de queryparameters alle informatie omvat die nodig is voor geverifieerde toegang tot een opslagresource. Om toegang te krijgen tot opslagresources met de SAS, hoeft de client alleen de SAS door te geven aan de juiste constructor of methode. Zie [Beperkte toegang verlenen tot Azure Storage-resources via SAS (Shared Access Signatures)](../articles/storage/common/storage-sas-overview.md) voor meer informatie over SAS.

> [!IMPORTANT]
> Azure Data Factory biedt nu alleen ondersteuning voor **service-SAS**, maar niet voor account-SAS. Let op dat de SAS-URL die kan worden afgeleid van Azure Portal of Storage Explorer, een account-SAS is. Deze wordt niet ondersteund.

> [!TIP]
> U kunt de volgende PowerShell-opdrachten uitvoeren om een service-SAS te genereren voor uw opslagaccount (vervang de plaatshouders en ken de benodigde machtiging toe): `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Met de gekoppelde Azure Storage SAS-service kunt u een Azure Storage-account koppelen aan een Azure-data factory met behulp van een Shared Access Signature (SAS). Het biedt de data factory met beperkte/tijdgebonden toegang tot alle/specifieke resources (blob/container) in de opslag. In de volgende tabel vindt u een beschrijving van de JSON-elementen die specifiek zijn voor Azure Storage SAS-gekoppelde service. 

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type |Het type eigenschap moet worden ingesteld op: **AzureStorageSas** |Ja |
| sasUri |Geef Shared Access Signature-URI op voor de Azure Storage-resources, zoals blob, container of tabel.  |Ja |

**Voorbeeld:**

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<Specify SAS URI of the Azure Storage resource>"
        }
    }
}
```

Bij het maken van een **SAS URI**, moet u het volgende overwegen:  

* Stel de juiste **machtigingen voor lezen/schrijven** op objecten in op basis van de manier waarop de gekoppelde service (lezen, schrijven, lezen/schrijven) wordt gebruikt in uw data factory.
* Stel de **verlooptijd** op de juiste wijze in. Zorg ervoor dat de toegang tot Azure Storage-objecten niet verloopt binnen de actieve periode van de pijplijn.
* URI moet worden gemaakt op het juiste container-/blob- of tabelniveau dat nodig is. Met een SAS-URI naar een Azure-blob kan de Data Factory-service toegang krijgen tot die specifieke blob. Met een SAS-URI naar een Azure-blob-container kan de Data Factory-Service blobs in die container doorlopen. Als u later toegang wilt bieden tot meer of minder objecten of als u de SAS-URI wilt bijwerken, moet u de gekoppelde service bijwerken met de nieuwe URI.   

