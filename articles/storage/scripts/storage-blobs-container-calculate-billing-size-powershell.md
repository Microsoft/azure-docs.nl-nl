---
title: Azure PowerShell-voorbeeldscript - de totale grootte van een blobcontainer berekenen voor facturering | Microsoft Docs
description: De totale grootte van een container in Azure Blob-opslag berekenen voor factureringsdoeleinden.
services: storage
author: fhryo-msft
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 12/29/2020
ms.author: fryu
ms.openlocfilehash: dfc338844e310102447e2498ee9cce8f28a79b9f
ms.sourcegitcommit: 31d242b611a2887e0af1fc501a7d808c933a6bf6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/29/2020
ms.locfileid: "97809561"
---
# <a name="calculate-the-total-billing-size-of-a-blob-container"></a>De totale grootte van een blobcontainer berekenen voor facturering

Met dit script wordt de grootte van een container in Azure Blob-opslag berekend voor het schatten van factureringskosten. Met het script wordt de totale grootte van de blobs in de container opgeteld.

> [!IMPORTANT]
> Met het voorbeeldscript uit dit artikel wordt de factureringsgrootte voor blobmomentopnamen mogelijk niet nauwkeurig berekend.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Met het PowerShell-script wordt de grootte van een container berekend voor factureringsdoeleinden. Als u de containergrootte voor andere doeleinden wilt berekenen, raadpleegt u [Calculate the total size of a Blob storage container](../scripts/storage-blobs-container-calculate-size-powershell.md) (De totale grootte van een Blob Storage-container berekenen) voor een eenvoudiger script dat een schatting biedt.

## <a name="determine-the-size-of-the-blob-container"></a>De grootte van de blobcontainer bepalen

De totale grootte van de blobcontainer omvat de grootte van de container zelf en de grootte van alle blobs onder de container.

In de volgende secties wordt beschreven hoe de opslagcapaciteit voor blobcontainers en blobs wordt berekend.  In de volgende sectie betekent Len(X) het aantal tekens in de tekenreeks.

### <a name="blob-containers"></a>Blobcontainers

De volgende berekening laat zien hoe u de hoeveelheid opslag kunt schatten die per blobcontainer wordt verbruikt:

```
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
```

Hieronder ziet u de uitsplitsing:

* 48 bytes overhead voor elke container, inclusief Tijdstip laatst gewijzigd, Machtigingen, Openbare instellingen en enkele systeemmetagegevens.

* De containernaam wordt opgeslagen in Unicode. Vermenigvuldig dus het aantal tekens met twee.

* Voor elke blok met blobcontainermetagegevens dat is opgeslagen, wordt de lengte van de naam (ASCII) opgeslagen, plus de lengte van de tekenreekswaarde.

* De 512 bytes per Ondertekende id is inclusief ondertekende id-naam, begintijd, verlooptijd en machtigingen.

### <a name="blobs"></a>Blobs

De volgende berekeningen laten zien hoe u de hoeveelheid opslag kunt schatten die per blob wordt verbruikt.

* Blok-blob (basis-blob of momentopname):

   ```
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
   SizeInBytes(data in unique committed data blocks stored) +
   SizeInBytes(data in uncommitted data blocks)
   ```

* Pagina-blob (basis-blob of momentopname):

   ```
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   number of nonconsecutive page ranges with data * 12 bytes +
   SizeInBytes(data in unique pages stored)
   ```

Hieronder ziet u de uitsplitsing:

* 124 bytes overhead voor blob, inclusief:
    - Tijdstip laatst gewijzigd
    - Grootte
    - Cache-Control
    - Content-Type
    - Content-Language
    - Content-Encoding
    - Content-MD5
    - Machtigingen
    - Informatie over momentopname
    - Lease
    - Enkele systeemmetagegevens

* De blobnaam wordt opgeslagen in Unicode. Vermenigvuldig dus het aantal tekens met twee.

* Voor elke blok met metagegevens dat is opgeslagen, wordt de lengte van de naam (opgeslagen als ASCII) toegevoegd, plus de lengte van de tekenreekswaarde.

* Voor de blok-blobs:
  * 8 bytes voor de blok-lijst.
  * Aantal blokken maal de grootte van de blok-id in bytes.
  * De grootte van de gegevens in alle toegezegde en niet-toegezegde blokken.

    >[!NOTE]
    >Wanneer momentopnamen worden gebruikt, is deze grootte alleen inclusief de unieke gegevens voor deze basis-blob of momentopname. Als de niet-toegezegde blokken na een week niet zijn gebruikt, worden ze definitief verwijderd. Hierna tellen ze niet meer mee in de facturering.

* Voor pagina-blobs:
  * Het aantal niet-aaneengesloten pagina’s met gegevens maal 12 bytes. Dit is het aantal unieke paginabereiken dat u ziet wanneer u de API **GetPageRanges** aanroept.

  * De grootte van de gegevens in bytes van alle opgeslagen pagina’s.

    >[!NOTE]
    >Wanneer momentopnamen worden gebruikt, is deze grootte alleen inclusief de unieke pagina’s voor de basis-blob of momentopname die wordt geteld.

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>Volgende stappen

- Zie [Calculate the total size of a Blob storage container](../scripts/storage-blobs-container-calculate-size-powershell.md) (De totale grootte van een Blob Storage-container berekenen) voor een eenvoudiger script dat een schatting biedt.

- Zie [Windows Azure Storage-facturering begrijpen](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/) voor meer informatie over Azure Storage-facturering.

- Raadpleeg de [documentatie van Azure PowerShell](/powershell/azure/) voor meer informatie over de Azure PowerShell-module.

- U vindt aanvullende Storage PowerShell-voorbeeldscripts in [PowerShell-voorbeelden voor Azure Storage](../blobs/storage-samples-blobs-powershell.md).