---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: eeea7b1fed0c2a1f805e21b4dec9ec3cad7fc976
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95996803"
---
## <a name="configure-your-application-to-access-azure-storage"></a>Uw toepassing configureren voor toegang tot Azure Storage
Er zijn twee manieren om uw toepassing te verifiëren voor toegang tot opslag Services:

* Gedeelde sleutel: alleen gedeelde sleutel gebruiken voor test doeleinden
* Shared Access Signature (SAS): SAS gebruiken voor productie toepassingen

### <a name="shared-key"></a>Gedeelde sleutel
Verificatie op basis van gedeelde sleutels houdt in dat uw toepassing de account naam en de account sleutel gebruikt voor toegang tot opslag Services. Voor het snel weer geven van de manier waarop u deze bibliotheek gebruikt, wordt gebruikgemaakt van gedeelde sleutel verificatie in deze aan de slag.

> [!WARNING] 
> **Gebruik verificatie op basis van gedeelde sleutels alleen voor test doeleinden.** Uw account naam en account sleutel, die volledige lees-/schrijftoegang tot het gekoppelde opslag account geven, worden gedistribueerd naar elke persoon die uw app downloadt. Dit is **geen** goede gewoonte omdat u risico loopt dat uw sleutel door niet-vertrouwde clients is aangetast.
> 
> 

Wanneer u verificatie met gedeelde sleutels gebruikt, maakt u een [Connection String](../articles/storage/common/storage-configure-connection-string.md). De connection string bestaat uit:  

* De **DefaultEndpointsProtocol** : u kunt http of https kiezen. Het gebruik van HTTPS wordt echter nadrukkelijk aanbevolen.
* De **account naam** : de naam van uw opslag account
* De **account sleutel** : Ga in [Azure Portal](https://portal.azure.com)naar uw opslag account en klik op het pictogram **sleutels** om deze informatie te vinden.
* Beschrijving **EndpointSuffix** : dit wordt gebruikt voor opslag Services in regio's met verschillende eindpunt achtervoegsels, zoals Azure China of Azure governance.

Hier volgt een voor beeld van connection string verificatie met behulp van gedeelde sleutel:

`"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"`

### <a name="shared-access-signatures-sas"></a>Shared Access Signatures (SAS)
Voor een mobiele toepassing is de aanbevolen methode voor het verifiëren van een aanvraag door een client aan de Azure Storage-service met behulp van een Shared Access Signature (SAS). Met SAS kunt u een client toegang verlenen tot een resource voor een bepaalde periode, met een opgegeven set machtigingen.
Als eigenaar van het opslag account moet u een SAS genereren voor uw mobiele clients. Als u de SAS wilt genereren, wilt u waarschijnlijk een afzonderlijke service schrijven die de SA'S genereert die naar uw clients moeten worden gedistribueerd. Voor test doeleinden kunt u de [Microsoft Azure Storage Explorer](https://storageexplorer.com) of [Azure Portal](https://portal.azure.com) gebruiken om een SAS te genereren. Wanneer u de SAS maakt, kunt u het tijds interval opgeven waarover de SA'S geldig zijn en de machtigingen die de SAS verleent aan de client.

In het volgende voor beeld ziet u hoe u de Microsoft Azure Storage Explorer kunt gebruiken om een SAS te genereren.

1. Als u dat nog niet hebt [gedaan, installeert u de Microsoft Azure Storage Explorer](https://storageexplorer.com)
2. Maak verbinding met uw abonnement.
3. Klik op uw opslag account en klik op het tabblad acties linksonder. Klik op Shared Access Signature ophalen om een ' connection string ' te genereren voor uw SA'S.
4. Hier volgt een voor beeld van een SAS-connection string die lees-en schrijf machtigingen verleent op service-, container-en object niveau voor de BLOB-service van het opslag account.
   
   `"SharedAccessSignature=sv=2015-04-05&ss=b&srt=sco&sp=rw&se=2016-07-21T18%3A00%3A00Z&sig=3ABdLOJZosCp0o491T%2BqZGKIhafF1nlM3MzESDDD3Gg%3D;BlobEndpoint=https://youraccount.blob.core.windows.net"`

Zoals u kunt zien, is het niet mogelijk om uw account sleutel in uw toepassing zichtbaar te maken wanneer u een SAS gebruikt. U vindt meer informatie over SA'S en aanbevolen procedures voor het gebruik van SAS door [gedeelde toegangs handtekeningen](../articles/storage/common/storage-sas-overview.md)uit te checken: inzicht in het SAS-model.