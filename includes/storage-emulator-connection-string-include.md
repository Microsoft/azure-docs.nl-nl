---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/17/2020
ms.author: tamram
ms.openlocfilehash: 37fba0101365e425110c2943264c8c0e8c511329
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97582690"
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

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Verbinding maken met het Emulator-account met behulp van een snelkoppeling
De eenvoudigste manier om verbinding te maken met de emulator vanuit uw toepassing is door een connection string te configureren in het configuratie bestand van uw toepassing dat verwijst naar de snelkoppeling `UseDevelopmentStorage=true` . Hier volgt een voor beeld van een connection string naar de emulator in een *app.config* -bestand: 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

De is gelijk aan het volledig opgeven van de account naam, de account sleutel en de eind punten voor elk van de emulator services die u wilt gebruiken in de connection string. Dit is nodig zodat de connection string verwijst naar de emulator-eind punten die verschillen van die voor een productie-opslag account. Zo ziet de waarde van uw connection string er als volgt uit:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```
