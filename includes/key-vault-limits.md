---
author: rothja
ms.service: key-vault
ms.topic: include
ms.date: 04/21/2020
ms.author: jroth
ms.openlocfilehash: e4abbeadb0d30911d99fff57c0e99a3e427a6d8d
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95555489"
---
### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Belangrijkste transacties (maximum aantal transacties in 10 seconden per kluis per regio<sup>1</sup>):

|Type sleutel|HSM-sleutel<br>Sleutel maken|HSM-sleutel<br>Alle andere transacties|Softwaresleutel<br>Sleutel maken|Softwaresleutel<br>Alle andere transacties|
|:---|---:|---:|---:|---:|
|RSA 2048-bits|5|1000|10|2.000|
|RSA 3072-bits|5|250|10|500|
|RSA 4096-bits|5|125|10|250|
|ECC P-256|5|1000|10|2.000|
|ECC P-384|5|1000|10|2.000|
|ECC P-521|5|1000|10|2.000|
|ECC SECP256K1|5|1000|10|2.000|

> [!NOTE]
> In de vorige tabel ziet u dat RSA 2048-bits-softwaresleutels met 2000 GET-transacties per 10 seconden zijn toegestaan. Voor RSA 2048-bits HSM-sleutels zijn 1000 GET-transacties per 10 seconden toegestaan.
>
> De drempelwaarden voor beperking worden gewogen en afdwinging wordt bepaald op basis van de som. Wanneer u zoals aangeven in de vorige tabel GET-bewerkingen uitvoert op RSA HSM-sleutels, is het gebruik van 4096-bits sleutels achtmaal duurder in vergelijking met 2048-bits sleutels. En dat komt doordat 1000/125 = 8.
>
> In een gegeven interval van 10 seconden kan een Azure Key Vault-client *slechts één* van de volgende bewerkingen uitvoeren voordat er een `429`HTTP-statuscode voor beperking wordt aangetroffen:
> - 2000 RSA 2048-bits softwaresleutel voor GET-transacties
> - 1000 RSA 2048-bits HSM-sleutel voor GET-transacties
> - 125 RSA 4096-bits HSM-sleutel voor GET-transacties
> - 124 RSA 4096-bits HSM-sleutel voor GET-transacties en 8 RSA 2048-bits HSM-sleutels voor GET-transacties

### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Geheimen, sleutels voor beheerde opslagaccounts en kluistransacties:

| Transactietype | Maximum aantal transacties in 10 seconden per kluis per regio<sup>1</sup> |
| --- | --- |
| Alle transacties |2.000 |

Zie de [Azure Key Vault-beperkingsrichtlijnen](../articles/key-vault/general/overview-throttling.md) voor informatie over het verwerken van beperkingen wanneer deze limieten worden overschreden.

<sup>1</sup> Een limiet voor het hele abonnement voor alle transactietypen is vijf keer per sleutelkluislimiet. Andere HSM-transacties per abonnement zijn bijvoorbeeld beperkt tot 5000 transacties binnen tien seconden per abonnement.

### <a name="azure-private-link-integration"></a>Azure Private Link- integratie

> [!NOTE]
> Het aantal sleutelkluizen met ingeschakelde privé-eindpunten per abonnement is een aanpasbare limiet. De limiet die hieronder wordt weergegeven, is de standaardlimiet. Als u een hogere limiet wilt aanvragen voor uw service, kunt u een e-mail sturen naar akv-privatelink@microsoft.com. We zullen deze aanvragen per geval goedkeuren.

| Resource | Limiet |
| -------- | ----- |
| Privé-eindpunten per sleutelkluis | 64 |
| Sleutelkluizen met privé-eindpunten per abonnement | 400 |