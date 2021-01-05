---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/28/2020
ms.author: tamram
ms.openlocfilehash: 85cfe3b062d7d9ef3a7bdcf29ef7d2125f8f3ae4
ms.sourcegitcommit: 31d242b611a2887e0af1fc501a7d808c933a6bf6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/29/2020
ms.locfileid: "97812783"
---
De emulator ondersteunt één vast account en een bekende verificatie sleutel voor gedeelde sleutel verificatie. Dit account en deze sleutel zijn de enige gedeelde sleutel referenties die zijn toegestaan voor gebruik met de emulator. Dit zijn:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> De verificatie sleutel die door de emulator wordt ondersteund, is alleen bedoeld voor het testen van de functionaliteit van de client verificatie code. Dit is geen beveiligings doel. U kunt uw productie-opslag account en-sleutel niet gebruiken met de emulator. Gebruik het ontwikkelings account niet met productie gegevens.
>
> De emulator ondersteunt alleen verbinding via HTTP. HTTPS is echter het aanbevolen protocol voor het openen van bronnen in een Azure-opslag account voor productie.
>

#### <a name="connect-to-the-emulator-account-using-the-shortcut"></a>Verbinding maken met het Emulator-account met behulp van de snelkoppeling

De eenvoudigste manier om verbinding te maken met de emulator vanuit uw toepassing is door een connection string te configureren in het configuratie bestand van uw toepassing dat verwijst naar de snelkoppeling `UseDevelopmentStorage=true` . De snelkoppeling is gelijk aan de volledige connection string voor de emulator, waarmee de account naam, de account sleutel en de emulator-eind punten voor elk van de Azure Storage services worden opgegeven:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

In het volgende code fragment van .NET ziet u hoe u de snelkoppeling kunt gebruiken van een methode die een connection string neemt. De constructor [BlobContainerClient (String, String)](/dotnet/api/azure.storage.blobs.blobcontainerclient.-ctor#Azure_Storage_Blobs_BlobContainerClient__ctor_System_String_System_String_) neemt bijvoorbeeld een Connection String.

```csharp
BlobContainerClient blobContainerClient = new BlobContainerClient("UseDevelopmentStorage=true", "sample-container");
blobContainerClient.CreateIfNotExists();
```

Zorg ervoor dat de emulator wordt uitgevoerd voordat u de code in het fragment aanroept.
